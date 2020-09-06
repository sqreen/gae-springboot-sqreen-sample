# GoogleAppEngine SpringBoot Sqreen Sample

This sample shows how to deploy a Spring Boot application protected by Sqreen to Google App Engine stadndard.
For simplicity and transparency, the example is based on the [sample](https://github.com/GoogleCloudPlatform/java-docs-samples/tree/master/appengine-java11/springboot-helloworld) from GoogleCloudPlatform.
Below you will find a Sqreen Integration Guide with required steps to use a Sqrreen Java Agent in the project deployed to the Google App Engine standard.

## Prerequisites
* JDK11
* Maven
* Google Cloud console

## Run application
* Set the correct Cloud SDK project via `gcloud config set project YOUR_PROJECT` to the ID of your application.
* Put your `SQREEN_TOKEN` and `SQREEN_APP_NAME` to `src/main/appengine/app.yaml`
* Build and deploy application `mvn clean package appengine:deploy`
* View web app `gcloud app browse` (or navigate `https://<your-project-id>.appspot.com`)
* In response HTTP headers you should see string `x-protected-by: Sqreen`

## Sqreen Integration Guide
1. Download java agent `sqreen.jar` into dedicated project folder. In the sample java agent located at `sqreen/sqreen.jar`
2. To use Sqreen, you need add java agent as JVM startup option. Edit `app.yaml` and add/modify entrypoint:
```
entrypoint: 'java -javaagent:sqreen.jar -jar app.jar'
```
3. Sqreen requires `SQREEN_TOKEN` and `SQREEN_APP_NAME` related to your Sqreen account. Get token and app from your dashboard and add them as environemtn variables to `app.yaml`:
```
env_variables:
    SQREEN_APP_NAME: XXXXXXXXX
    SQREEN_TOKEN: env_org_XXXXXXXXX
```
4. [Optional] Some web apps has too long startup time, we recommend, for testing purpose, disable healthcheck, to prevent force restart application by Google App Enging. Add command to `app.yaml`
```
health_check:
    enable_health_check: False
```
5. To make Google App Engine use Sqreen - add external files directory (where the `sqreen.jar` stored), to maven configuration `pom.xml`. If you are using `Gradle` to build your project, you must use similar configuration to include external files (please check official documentation).
```
<plugin>
    <groupId>com.google.cloud.tools</groupId>
    <artifactId>appengine-maven-plugin</artifactId>
    ...
    <configuration>
        ....
        <extraFilesDirectories>
            <extraFilesDirectory>sqreen</extraFilesDirectory>
        </extraFilesDirectories>
    </configuration>
</plugin>
```

6. Build, deploy and test the app with Sqreen protection.