[![Circle CI](https://circleci.com/gh/palantir/dropwizard-version-info.svg?style=shield&circle-token=0f0266bd0245635428b61e832c4f59c408e915e6)](https://circleci.com/gh/palantir/dropwizard-version-info)
[ ![Download](https://api.bintray.com/packages/palantir/releases/dropwizard-version-info/images/download.svg) ](https://bintray.com/palantir/releases/dropwizard-version-info/_latestVersion)

Version Info Bundle
===================

Introduction
------------

`VersionInfoBundle` is a simple Dropwizard bundle which exposes a
version string as a Jersey resource under `/version`.

Using the bundle
----------------

1.  Create a version.properties file that contains a `productVersion`
    key in the <project_dir>/src/main/resources/. It should look something like
    this:

    ```
    productVersion: <version>
    ```

    If the properties file cannot be read (for example, because of an
    incorrect path or an IO error), the bundle will throw a
    `RuntimeException` on construction. If the properties file can be
    read, but the `productVersion` key cannot be found, then the version
    will be "unknown".

2.  Add the `com.palantir.versioninfo:dropwizard-version-info:<VERSION>`
    dependency to your project's build.gradle file. The most recent
    version number can be found in the [Relase Notes]
    (https://github.com/palantir/dropwizard-version-info/releases).
    The dependencies section should look something like this:

    ```
    dependencies {
      // ... unrelated dependencies omitted ...

      compile "com.palantir.versioninfo:dropwizard-version-info:<VERSION>"
    }
    ```

3.  Add the bundle in your Dropwizard application's `initialize` method
    (or create one if it doesn't already exist). It should look
    something like this:

    ```
    @Override
    public void initialize(Bootstrap<MyConfiguration> bootstrap) {
        bootstrap.addBundle(new VersionInfoBundle());
    }
    ```

4.  Run your server, then access `<API_ROOT>/version`. The `<API_ROOT>`
    is defined by the `rootPath` and `applicationContextPath` in your
    Dropwizard server configuration.

5.  If your application uses [Feign](https://github.com/Netflix/feign)
    to build clients, make sure your service interface extends
    VersionInfoService interface. The dependencies section for your
    `-api` project should look like this:

    ```
    dependencies {
      // ... unrelated dependencies omitted ...

      compile "com.palantir.versioninfo:dropwizard-version-info-api:<VERSION>"
    }
    ```
    Your service interface should look like this:

    ```
    public interface ExampleService extends VersionInfoService {
        @GET
        @Path("hello")
        @Produces(MediaType.TEXT_PLAIN)
        String hello();
    }
    ```

Custom resource path
--------------------

To load the version.properties file from somewhere else in the
classpath, pass the path to it as an argument to the bundle's
constructor:

```
@Override
public void initialize(Bootstrap<MyConfiguration> bootstrap) {
    bootstrap.addBundle(new VersionInfoBundle("/properties/info.properties"));
}
```

Generating product version from Git
-----------------------------------

To generate the product version from Git, use the [Git-Version Gradle Plugin](https://github.com/palantir/gradle-git-version).
You can use the `write-version.gradle` script to write the version from git into the `version.properties` file:

1. In your `build.gradle`, include the `write-version.gradle` dependency:

    ```
    apply from: "${rootDir}/write-version.gradle"
    ```

2. Set your compile task to depend on the generation of the version file:

    ```
    compileJava.dependsOn writeVersion
    ```

3. Set your `.gitignore` to ignore changes to the `version.properties` file:

    ```
    *version.properties
    ```
