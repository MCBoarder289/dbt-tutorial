Welcome to your new dbt project!

### Using the starter project

Try running the following commands:
- dbt run
- dbt test


### Resources:
- Learn more about dbt [in the docs](https://docs.getdbt.com/docs/introduction)
- Check out [Discourse](https://discourse.getdbt.com/) for commonly asked questions and answers
- Join the [chat](https://community.getdbt.com/) on Slack for live discussions and support
- Find [dbt events](https://events.getdbt.com) near you
- Check out [the blog](https://blog.getdbt.com/) for the latest news on dbt's development and best practices

### Source Material:
* https://docs.getdbt.com/guides/manual-install

### Different environments using dbt core:
To configure dbt Core for different deployment environments (like development and production) with BigQuery, you'll use distinct profiles in your profiles.yml file, each referencing a different BigQuery project, dataset, and potentially service account. 
Here's a breakdown of the process:
1. Understanding Environments in dbt:
* `Development (dev)`: Used for local development and testing. 
* `Deployment/Production (prod)`: The environment where end-users interact with the data. 
* `dbt Cloud`: dbt Cloud uses "Deployment" as the environment type for final production workflows. 

2. Setting up your profiles.yml file:

* **Create the file**: If you don't have one, create a profiles.yml file in the ~/.dbt/ directory. 
* **Define multiple targets**: Define separate targets for each environment (e.g., dev, prod). 
* **Specify BigQuery connection details**: For each target, configure the type, method, project, dataset, and other optional settings (like threads, timeout_seconds, location, priority, retries). 
  * type: bigquery: Specifies that you're connecting to BigQuery. 
  * method: service-account: Use this for authentication with a service account JSON key file. 
  * project: YOUR_BIGQUERY_PROJECT_ID: Replace with your BigQuery project ID. 
* dataset: YOUR_BIGQUERY_DATASET: Replace with the BigQuery dataset for this environment. 

Example profiles.yml snippet:
```yaml
my-bigquery-project:
  outputs:
    dev:
      type: bigquery
      method: service-account
      project: YOUR_BIGQUERY_PROJECT_ID
      dataset: dbt_dev
      threads: 1
      timeout_seconds: 300
      location: EU
      priority: interactive
      retries: 1
      keyfile: /path/to/your/dev/service_account_key.json

    prod:
      type: bigquery
      method: service-account
      project: YOUR_BIGQUERY_PROJECT_ID
      dataset: dbt_prod
      threads: 1
      timeout_seconds: 300
      location: EU
      priority: interactive
      retries: 1
      keyfile: /path/to/your/prod/service_account_key.json
```
        
* Use different datasets for each environment: This is crucial to avoid accidental overwrites in production. 
* Keep sensitive data secure: Don't hardcode passwords or other sensitive information in the profiles.yml file. Use environment variables instead. 

3. Authentication with Service Accounts:
Create Service Accounts:
Create separate service accounts in the Google Cloud Console for each environment. 
Assign Roles:
Assign the necessary roles to each service account (e.g., BigQuery Data Editor, BigQuery Job User). 
Download JSON Key Files:
Download the JSON key files for each service account and store them securely. 
Refer to the key files in profiles.yml:
Specify the path to the JSON key files in your profiles.yml configuration.

4. Running dbt with different targets:
Use the -t or --target flag: When running dbt commands, use the -t or --target flag to specify the target environment (e.g., dbt run --target dev or dbt run --target prod).

5. dbt Cloud Considerations:
Create Deployment Environments: In dbt Cloud, create separate deployment environments for development and production.
Set Production Environment: Designate one deployment environment as the production environment. 
