# Secrets folder

- Copy `ansible/secrets/.env.example` to `ansible/secrets/.env` and fill values.
- Copy `ansible/secrets/id_rsa.example` to `ansible/secrets/id_rsa` and replace with your private key; set permissions to `600`.
- `ansible/secrets/*.example` files are committed; actual secret files are ignored by `.gitignore`.

Usage

1. Create the secret files (copy examples):

```bash
cp ansible/secrets/.env.example ansible/secrets/.env
cp ansible/secrets/id_rsa.example ansible/secrets/id_rsa
chmod 600 ansible/secrets/id_rsa
```

2. You may instead pass required variables on the command line using `--extra-vars`.

Example:

```bash
ansible-playbook -i ansible/inventory.ini ansible/playbook.yml \
	-e "SMB_SERVER=files.example.local SMB_SHARE=nextcloud SMB_USERNAME=ncuser SMB_PASSWORD='s3cret' MYSQL_ROOT_PASSWORD='dbroot' POSTGRES_PASSWORD='dbpass'" \
	--private-key ansible/secrets/id_rsa -u root
```

3. Optional: encrypt `ansible/secrets/.env` with `ansible-vault` if you prefer encrypted secrets storage.
