# apigee-config-maven-plugin

Maven plugin to create, manage Apigee config like Cache, KVM, Target Server, Resource Files, API Products, Developers, Developer Apps and Mask Config.

Help API teams follow API development best practices with Apigee.
  * Track Apigee Config (KVM, cache, target servers, etc.) in source control
  * Deploy config changes along with the API in a CI pipeline
  * Simplify, automate config management during API development
  * Track config changes in prod environment as releases

Small API projects can use the single file format in edge.json to manage their config. Large, complex projects with several config entities can use the multi-file format to organize config in source control. Checkout samples for examples.

This plugin is available in public maven repo and can be used just by referring to it in pom.xml. This github repo is the plugin source code and unless you make changes to the code you do not have to build this repo to use the plugin. Read this document further for plugin usage instructions.

## Plugin Usage
```
mvn install -Ptest -Dapigee.config.options=create

  # Options

  -P<profile>
    Pick a profile in the parent pom.xml (shared-pom.xml in the example).
    Apigee org and env information comes from the profile.

  -Dapigee.config.options
    none   - No action (default)
    create - Create when not found. Pre-existing config is NOT updated even if it is different.
    update - Update when found; create when not found. Refreshes all config to reflect edge.json.
    delete - Delete all config listed in edge.json.
    sync   - Delete and recreate.

  -Dapigee.config.dir=<dir>
     directory containing multi-file format config files.
     
  -Dapigee.config.exportDir=<dir>
     dir where the dev app keys are exported. This is only used for `exportAppKeys` goal. The file name is always devAppKeys.json

  # Individual goals
  You can also work with an individual config type using the 
  corresponding goal directly. The goals available are,

  apiproducts 
  developerapps
  caches
  developers
  kvms
  targetservers
  resourcefiles
  maskconfigs
  exportAppKeys

  For example, the apps goal is used below to only create apps and ignore all other config types.
  mvn apigee-config:apps -Ptest -Dapigee.config.options=create
  
  To export the dev app keys, use the following:
  mvn apigee-config:exportAppKeys -Ptest -Dapigee.config.exportDir=./target  
```
The default "none" action is a NO-OP and it helps deploy APIs (using [apigee-deploy-maven-plugin](https://github.com/apigee/apigee-deploy-maven-plugin)) without affecting config.

## Sample project
Refer to an example project at [/samples/EdgeConfig](https://github.com/apigee/apigee-config-maven-plugin/tree/master/samples/EdgeConfig)

This project demonstrates the creation and management of Apigee Edge Config and performs the following steps in sequence.
  - Creates Caches
  - Creates Target servers
  - Creates KVM
  - Creates Resource File
  - Creates API products
  - Creates Developers
  - Creates Developer Apps

To use, edit samples/EdgeConfig/shared-pom.xml, and update org and env elements in all profiles to point to your Apigee org, env. You can add more profiles corresponding to each env in your org.

      <apigee.org>myorg</apigee.org>
      <apigee.env>test</apigee.env>

To run the plugin and use edge.json jump to samples project `cd /samples/EdgeConfig` and run 

`mvn install -Ptest -Dusername=<your-apigee-username> -Dpassword=<your-apigee-password> -Dapigee.config.options=create`

To run the plugin and use the multi-file config format jump to samples project `cd /samples/EdgeConfig` and run 

`mvn install -Ptest -Dusername=<your-apigee-username> -Dpassword=<your-apigee-password> -Dapigee.config.options=create -Dapigee.config.dir=resources/edge`

Refer to [samples/APIandConfig/HelloWorld](https://github.com/apigee/apigee-config-maven-plugin/tree/master/samples/APIandConfig/HelloWorld) for config management along with API deployment using [apigee-deploy-maven-plugin](https://github.com/apigee/apigee-deploy-maven-plugin). More info at [samples/README.md](https://github.com/apigee/apigee-config-maven-plugin/blob/master/samples/README.md)

## Multi-file config
Projects with several config entities can utilize the multi-file structure to organize config while keeping individual file sizes within manageable limits. The plugin requires the use of specific file names and directories to organize config. 

The apigee.config.dir option must be used to identify the top most directory containing the following config structure.


      ├── api
      │   ├── forecastweatherapi
      |   |   ├── resourceFiles
      |   |   |   ├── jsc
      |   |   |   |    ├── test.js
      │   │   └── kvms.json
      │   │   └── resourcefiles.json
      │   └── oauth
      │       ├── kvms.json
      │       └── maskconfigs.json
      ├── env
      │   ├── prod
      │   │   └── caches.json
      │   └── test
      │       ├── caches.json
      │       ├── kvms.json
      │       └── targetServers.json
      └── org
          ├── apiProducts.json
          ├── developerApps.json
          ├── developers.json
          ├── kvms.json
          └── maskconfigs.json


## Single file config structure - edge.json
Projects with fewer config entities can use the single file edge.json format to capture all config of an API project. The edge.json file organizes config into 3 scopes corresponding to the scopes of config entities that can be created in Edge. The plugin looks for edge.json in the current directory by default.
   ```
     envConfig
     orgConfig
     apiConfig
   ```
For example, API product is an orgConfig whereas Cache is an envConfig. The envConfig is further organized into individual environments as follows.
   ```
     envConfig.test
     envConfig.dev
     envConfig.pre-prod
   ```
The environment name is intended to be the same as the profile name in pom.xml (parent-pom.xml or shared-pom.xml).

The final level is the config entities. 
   ```
     orgConfig.apiProducts[]
     orgConfig.developers[]
     orgConfig.developerApps[]
     envConfig.test.caches[]
     envConfig.test.targetServers[]
   ```

Each config entity is the payload of the corresponding management API. The example project may not show all the attributes of config entities, but any valid input of a management API would work.

Config entities like "developerApps" are grouped under the developerId (email) they belong to.

## ROADMAP
  - Support management API OAuth and MFA
  - KVM (CPS)
  - Keystore, Truststore support
  - Virtual host (on-prem)
  - Custom roles
  - User role association

Please send feature requests using [issues](https://github.com/apigee/apigee-config-maven-plugin/issues)

## Support
* Post a question in [Apigee community](https://community.apigee.com/index.html)
* Create an [issue](https://github.com/apigee/apigee-config-maven-plugin/issues/new)

