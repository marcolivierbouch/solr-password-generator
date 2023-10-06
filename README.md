# Solr Security Configuration Password Generator
This bash script simplifies the process of generating secure passwords for your Solr instance's security.json file. Ensure the robustness of your Solr security by effortlessly creating strong passwords with the help of this tool.

With this script you can generate new passwort without using the API.

We all know that generating a password for Solr when deploying Sitecore it is the first step for a better security.

See more information here about Solr Basic Authentication Plugin: https://solr.apache.org/guide/7_1/basic-authentication-plugin.html 

## Requirements
Before diving in, make sure to install pwgen using the following command:

```bash
apt install pwgen
```

## Usage Example
Generate a password tailored for your Solr setup using the script, as illustrated below:

```bash
secret=$(./generate_config.sh SolrRocks)
sed -i "s/%REPLACE%/$secret/" ./security.json
```

This will generate a security.json like in the example here https://solr.apache.org/guide/7_1/basic-authentication-plugin.html but of course the salt will change. 
```json
{
"authentication":{ 
   "blockUnknown": true, 
   "class":"solr.BasicAuthPlugin",
   "credentials":{"solr":"IV0EHq1OnNrj6gvRCwvFwTrZ1+z1oBbnQdiVC3otuq0= Ndd7LKvVBAaZIF0QAVi1ekCfAJXr1GGfLtRUXhgrF8c="} 
},
"authorization":{
   "class":"solr.RuleBasedAuthorizationPlugin",
   "permissions":[{"name":"security-edit",
      "role":"admin"}], 
   "user-role":{"solr":"admin"} 
}}
```


## Automatic password rotation
Assuming you have your [Solr instance set up in Azure app service](https://www.getfishtank.com/blog/sitecore-solr-docker-app-service-on-azure), Azure Key Vault configured, and the Solr Password Generator script ready, here's a simplified workflow on how you could implement password rotation in your CI:

1. Retrieve Secret from Azure Key Vault:
Modify your script to fetch the secret from Azure Key Vault using Azure CLI or another appropriate method. Here's an example snippet:

```bash
# Fetch secret from Azure Key Vault
secret=$(az keyvault secret show --vault-name YourKeyVaultName --name YourSecretName --query value -o tsv)
```
Replace YourKeyVaultName and YourSecretName with your actual Key Vault name and secret name.

2. Update Solr security.json:
After obtaining the secret, use it to generate the configuration and update the Solr security.json file. This step remains the same as in your original script:

```bash
password=$(./generate_config.sh "$secret")
sed -i "s/%REPLACE%/$password/" ./security.json
```

3. Deploy Updated Configuration to Solr:
Upload the updated security.json to your Solr instance. This step could involve copying the file to the appropriate directory or sending an HTTP request to Solr to update its configuration.

```bash
# Copy the updated security.json to Solr configuration directory
az webapp deploy --resource-group YOUR_RG --name APP_SERVICE_NAME --src-path ./security.json --type=static --target-path="{PUT_GOOD_PATH_HERE}/security.json"
```

Integrate these steps into your deployment pipeline or automation script. For instance, if you're using a CI/CD tool like Azure DevOps or Jenkins, include the script in your deployment process.

Ensure that the secrets, such as Azure Key Vault credentials, are securely stored and accessed during the deployment process.

By following these steps, you establish a secure and automated password rotation process for Solr, leveraging Azure Key Vault for secret management.
