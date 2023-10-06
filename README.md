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
