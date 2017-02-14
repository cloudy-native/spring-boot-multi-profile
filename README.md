# What do we have here then?
It's an example of how to run a [Spring Boot](https://projects.spring.io/spring-boot/) application with multiple profiles.

# How do I work it?

## Make an executable JAR file

```
mvn clean package spring-boot:repackage
```

## Copy the executable JAR file to `dist`
> `dist` is independent of the build so there's no confusion about how this would work when deployed to prod

```
cp target/multi-profile-service-0.0.1-SNAPSHOT.jar dist
```

## Change to the `dist` directory and run it

```
cd dist
sh -x ./multi-profile-service-0.0.1-SNAPSHOT.jar --spring.profiles.active=dev,common
```

# What should I see?

There are 3 configuration files at play here

1. `application.yml`, which contains any base configuration (packaged with the application)
1. `application-dev.yml`, which contains dev-specific configuration (also packaged with the application executable)
1. `application-common.properties`, which is not packaged with the executable and is a regular `.properties` file

You should see that both `application-dev.yml` and `application-common.properties` are read.

In another window, execute
```
curl localhost:8080/env | jq
```
and you should see

```
 ...
 "applicationConfig: [file:./application-common.properties]": {
   "common.some.flag": "true"
 },
 "applicationConfig: [classpath:/application-dev.yml]": {
   "dev.some.flag": true
 },
 "applicationConfig: [classpath:/application.yml]": {
   "spring.profiles.active[0]": "common",
   "management.security.enabled": false
 }
}
```

This shows the property sources for the Spring Boot application and their contents.

> If you don't have the remarkable `jq` application and are on a Mac, you can get it with `brew install jq`
