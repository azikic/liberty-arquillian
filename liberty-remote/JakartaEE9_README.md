# Arquillian Liberty Remote with Jakarta EE 9

An Arquillian container adapter (`DeployableContainer` implementation) that can connect and run against a remote (different JVM, different machine) Liberty server andrun tests on it over a remote protocol (effectively in a different JVM).

## Prerequisites

**Prerequisite Version**

This `DeployableContainer` has been tested with the latest beta release of Open Liberty. Requires Jakarta EE 9.

For Java EE 8 projects and below, check out the documentation [here](README.md).

**Prerequisite Configuration**

The following features are required in the `server.xml` of the Liberty server.

```
<!-- Enable features -->
<featureManager>
    <feature>pages-3.0</feature>
    <feature>restConnector-2.0</feature>
</featureManager>
```

or

```
<!-- Enable features -->
<featureManager>
    <feature>restfulWS-3.0</feature>
    <feature>restConnector-2.0</feature>
</featureManager>
```

You will also need to enable security, one example would be:

```
<quickStartSecurity userName="admin" userPassword="admin" />

<keyStore id="defaultKeyStore" password="password" />
```

You need to have those keys trusted by your client as well, otherwise you'll see SSL certificate trust errors, and you need to give permissions for the container adapter to write to the dropins directory:

```
<!-- This section is needed to allow upload of files to the dropins directory,
the remote container adapter relies on this configuration -->
<remoteFileAccess>
    <writeDir>${server.config.dir}/dropins</writeDir>
</remoteFileAccess>
```

If you need a sample `server.xml`, please refer to the [one in our source repository](https://github.com/OpenLiberty/liberty-arquillian/blob/main/liberty-remote/src/test/resources/server.xml).

## Configuration

Default Protocol: Servlet 5.0 or REST 3.0 depending on configuration

To enable Arquillian Liberty Remote in your project, add the following to your `pom.xml`:

```xml
<dependencyManagement>
	<dependencies>
		<dependency>
			<groupId>org.jboss.arquillian</groupId>
			<artifactId>arquillian-bom</artifactId>
			<version>1.7.0.Alpha13</version>
			<scope>import</scope>
			<type>pom</type>
		</dependency>
	</dependencies>
</dependencyManagement>

<dependencies>
	...
	<dependency>
		<groupId>io.openliberty.arquillian</groupId>
		<artifactId>arquillian-liberty-remote-jakarta</artifactId>
		<version>2.1.0</version>
		<scope>test</scope>
	</dependency>
	...
</dependencies>
```

**Container Configuration Options**

| Name | Type | Default | Description |
| ---- | ---- | ------- | ----------- |
| serverName | String | defaultServer | Name of the liberty server instance to connect to. |
| hostName | String | None | Hostname for the target machine where the WebSphere Liberty Profile server is running. |
| username | String | None | The username to use to connect to the target. |
| password | String | None | The password to use to connect to the target. |
| httpPort | Integer | 9080 | HTTP Port of the server. |
| httpsPort | Integer | 9443 | HTTPS Port of the server. |
| serverStartTimeout | Integer | 30 | Time in seconds to wait for the application server to start |
| appDeployTimeout | Integer | 20 | Time in seconds to wait for the application deployment to complete and the application to start |
| appUndeployTimeout | Integer | 2 | Time in seconds to wait for the application undeployment to complete |
| outputToConsole | Boolean | true | When enabled output from the application server process will be emitted to stdout |
| testProtocol | String | servlet | Aquillian protocol to contact the server to run a test (available: servlet or rest) |

## Examples

### Example of Container Configuration (arquillian.xml)

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<arquillian xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns="http://jboss.org/schema/arquillian"
xsi:schemaLocation="http://jboss.org/schema/arquillian http://jboss.org/schema/arquillian/arquillian_1_0.xsd">

	<engine>
		<property name="deploymentExportPath">target/</property>
	</engine>
	
	<container qualifier="liberty-remote">
		<configuration>
			<property name="hostName">localhost</property>
			<property name="serverName">defaultServer</property>
			<property name="username">admin</property>
			<property name="password">admin</property>
			<property name="httpPort">9080</property>
			<property name="httpsPort">9443</property>
		</configuration>
	</container>
</arquillian>
```

### Example of Maven profile setup
```
<profile>
	<id>liberty-remote</id>
	<dependencies>
		<dependency>
			<groupId>io.openliberty.arquillian</groupId>
			<artifactId>arquillian-liberty-remote-jakarta</artifactId>
			<version>2.1.0</version>
		</dependency>
	</dependencies>
</profile>
```