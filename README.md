stages:
  - build
  - test
  - deploy

variables:
  MAVEN_CLI_OPTS: "-s .m2/settings.xml --batch-mode --errors --fail-at-end"

cache:
  paths:
    - .m2/repository

build:
  stage: build
  script:
    - echo "Building the project..."
    - mvn $MAVEN_CLI_OPTS clean package
  artifacts:
    paths:
      - target/*.jar
  only:
    - master

test:
  stage: test
  script:
    - echo "Running unit tests..."
    - mvn $MAVEN_CLI_OPTS test
  only:
    - master

deploy:
  stage: deploy
  script:
    - echo "Deploying to staging environment..."
    - scp target/*.jar user@staging-server:/path/to/deploy
    - ssh user@staging-server 'bash -s' < deploy_script.sh
  only:
    - master
  environment:
    name: staging
    url: http://staging.example.com
