# Day 08: Install Ansible

## Task Description
Deploy Ansible version 4.7.0 on a jump host using pip3, ensuring the ansible binary is accessible system-wide.

## Solution Commands

### 1. Prepare the environment:
```bash
pip3 --version
```

### 2. Global installation:
```bash
sudo pip3 install ansible==4.7.0 --prefix=/usr/local
```

### 3. Verification:
```bash
ansible --version
pip3 show ansible | grep Version
which ansible
```

### 4. PATH configuration (if needed):
Add `/usr/local/bin` to system PATH via `/etc/profile` if the ansible command isn't found.

## Alternative Approaches

### Virtual environment method:
```bash
python3 -m venv ansible-env
source ansible-env/bin/activate
pip3 install ansible==4.7.0
```

### Debian/Ubuntu dependencies:
```bash
sudo apt-get update
sudo apt-get install -y python3 python3-pip
sudo pip3 install ansible==4.7.0 --prefix=/usr/local
```

## Expected Output

```
ansible [core 2.11.12]
  config file = None
  configured module search path = ['/home/thor/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python3.x/site-packages/ansible
  ansible collection location = /home/thor/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/local/bin/ansible
  python version = 3.x.x
```

## Notes
- The bundled core version should be 2.11.12
- Installing with `--prefix=/usr/local` places executable wrappers into `/usr/local/bin`
- Ensure this path exists in system profiles for universal access
- Virtual environment method provides dependency isolation
- Ansible 4.7.0 is a stable release suitable for production use
