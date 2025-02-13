# Howto display deployments

Custom Deploy Integration for CircleCI builds

add this Orb to your config.yaml
```
orbs:
  report_deploy: opslevel/report_deploy@1.0.0
```

Add this job configuration
```
  run-report-deploy:
    parameters:
      environment:
        type: string
      integration-url:
        type: string
      service:
        type: string
      username:
        type: string
      description:
        type: string
    docker:
      - image: 'public.ecr.aws/opslevel/cli:v2024.3.15'
    steps:
      - checkout
      - run:
          name: Set Integration URL
          command: |
            echo 'export CIRCLE_PIPELINE_ID_ORIGINAL="${CIRCLE_PIPELINE_ID}"' >> "$BASH_ENV"
            echo 'export CIRCLE_PIPELINE_ID="${CIRCLE_PIPELINE_ID}-<< parameters.environment >>"' >> "$BASH_ENV"
            echo 'export INTEGRATION_URL="<< parameters.integration-url >>"' >> "$BASH_ENV"
            echo 'export DEPLOYER_NAME="<< parameters.username >>"' >> "$BASH_ENV"
            source "$BASH_ENV"
      - report_deploy/report:
          deployer_email: "your-team@redcare-pharmacy.com"
          deployer_name: << parameters.username >>
          description: << parameters.description >>
          environment: << parameters.environment >>
          service: << parameters.service >>
```

Add this job configurations with dependency to the deploy steps and change service to name of your service make sure alias or 

e.g. reference:
```
      - run-report-deploy:
          requires:
            - deploy-to-reference
          context: credentials
          environment: reference
          integration-url: ${OPSLEVEL_REFERENCE_URL}
          service: 'service-name-or-alias-in-opslevel'
          username: ${CIRCLE_USERNAME}
          description: 'Deployed to reference'
          name: 'Report reference deploy to OpsLevel'
```

for prod:

```
      - run-report-deploy:
          requires:
            - deploy-to-production
          context: credentials
          environment: 'production'
          integration-url: ${OPSLEVEL_PRODUCTION_URL}
          service: 'service-name-or-alias-in-opslevel'
          username: ${CIRCLE_USERNAME}
          description: 'Deployed to production'
          name: 'Report production deploy to OpsLevel'
```


