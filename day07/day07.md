# Day 07: Linux SSH Authentication

## Task Description
The xFusionCorp Industries system administration team requires password-less SSH access to all app servers through their respective sudo users to enable automated scripts running on the jump host.

## Solution Commands

### SSH key generation (on jump host):
```bash
ssh-keygen -t rsa
```
Accept default path `/home/thor/.ssh/id_rsa` with an empty passphrase.

### Public key distribution:
```bash
ssh-copy-id tony@stapp01
ssh-copy-id steve@stapp02
ssh-copy-id banner@stapp03
```
Each command requires entering the target user's password once, which appends the public key to their `authorized_keys` file.

### Verification:
```bash
ssh tony@stapp01
ssh steve@stapp02
ssh banner@stapp03
```

## Implementation Details
- The `thor` user on the jump host generates an RSA key pair
- Public keys are distributed to three app servers (stapp01, stapp02, stapp03)
- Each server has a designated sudo user (tony, steve, banner respectively)
- Successful verification occurs when SSH connections establish without password prompts
- The setup enables automated scripts to execute operations across all app servers

## Understanding SSH Keys

### Key pair components:
- **Private key** (`id_rsa`) - Stays on the jump host, never share
- **Public key** (`id_rsa.pub`) - Distributed to remote servers

### How it works:
1. Jump host presents its private key
2. Remote server validates against stored public key
3. Authentication succeeds without password

## Notes
- Always protect your private key with appropriate permissions (600)
- Use empty passphrase for automation, or use ssh-agent for interactive use
- This setup is essential for configuration management tools like Ansible
- Passwordless authentication improves security by eliminating password transmission
