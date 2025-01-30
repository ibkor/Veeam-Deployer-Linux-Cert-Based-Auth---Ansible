# Veeam Deployer Ansible Playbook

This playbook automates the installation and configuration of Veeam Plugin on Linux servers.

## Directory Structure 

## Prerequisites

1. Create a `files` directory and place the following files in it:
   - Veeam Plugin RPM package (`veeamdeployment-12.2.0.334-1.x86_64.rpm`)
   - Server certificate (`server-cert.p12`)
   - Client certificate (`client-cert.pem`)
  
These files can be generated on VBR Server by running below PowerShell command (adjust validity if needed):
 'Generate-VBRBackupServerDeployerKit -ExportPath "Path" -ValidityPeriodInHours 48 '


2. Ensure Ansible is installed on your control machine:
   ```bash
   sudo yum install ansible   # For RHEL/CentOS
   # or
   sudo apt install ansible   # For Ubuntu/Debian
   ```

3. Verify your `hosts.ini` file contains the correct server information and credentials

## Running the Playbook

1. Test connectivity to your servers:
   ```bash
   ansible linux-servers -i Inventory/hosts.ini -m ping
   ```

2. Run the playbook:
   ```bash
   ansible-playbook -i Inventory/hosts.ini veeamdeployer.yml
   ```

3. To see more detailed output, add the `-v` flag:
   ```bash
   ansible-playbook -i Inventory/hosts.ini veeamdeployer.yml -v
   ```

## What the Playbook Does

1. Creates a temporary directory (`/tmp/VeeamUpload`) on target servers
2. Copies required files from the Ansible control machine to the targets
3. Installs the Veeam Plugin (with GPG check disabled)
4. Configures server and client certificates
5. Restarts the Veeam deployment service
6. Cleans up temporary files

## Troubleshooting

1. If you see Python interpreter errors:
   - Verify Python 3 is installed on target servers
   - Check the `ansible_python_interpreter` path in hosts.ini

2. If you see SSH connection errors:
   - Verify sshpass is installed on the control machine
   - Check the credentials in hosts.ini
   - Ensure SSH service is running on target servers

3. For detailed debugging, use:
   ```bash
   ansible-playbook -i Inventory/hosts.ini veeamdeployer.yml -vvv
   ``` 

# Create main directory and subdirectories
mkdir -p VeeamDeployerAnsible/files
mkdir -p VeeamDeployerAnsible/Inventory

# Change to project directory
cd VeeamDeployerAnsible 

# Install EPEL repository (if not already installed)
sudo yum install epel-release

# Install Development Tools
sudo yum groupinstall "Development Tools"

# Install sshpass from source
cd /tmp
wget https://sourceforge.net/projects/sshpass/files/sshpass/1.09/sshpass-1.09.tar.gz
tar xvf sshpass-1.09.tar.gz
cd sshpass-1.09
./configure
make
sudo make install

# Return to project directory
cd - 

# Copy your files from Windows server to VeeamDeployerAnsible/files/
# You'll need to do this manually using your preferred method (scp, sftp, etc.) 
