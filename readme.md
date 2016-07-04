Pactbroker Maven Plugin
==========

Pactbroker Maven Plugin integrates with [PactBroker](https://github.com/bethesque/pact_broker) and allows to
upload pacts created by consumer rsp download pacts that are verified against providers.

It also allows to use a **git-repository** instead of a PactBroker-instance as simplification so no additional infrastructure is needed.

Installation
-----
add on-demand-repository (using https://jitpack.io/)
```
<pluginRepositories>
	<pluginRepository>
		<id>jitpack.io</id>
		<url>https://jitpack.io</url>
	</pluginRepository>
</pluginRepositories>
```
or install locally
```
git clone ...
cd pactbroker-maven-plugin
mvn install
```

##Usage


###Consumer

Configure plugin in your pom.xml using the *upload-pacts* goal:

```xml
<build>
  <plugins>
    <plugin>
      <groupId>com.github.warmuuh</groupId>
      <artifactId>pactbroker-maven-plugin</artifactId>
      <version>0.0.8</version>
      <executions>
        <execution>
          <id>upload-pacts</id>
          <goals><goal>upload-pacts</goal></goals>
          <configuration>
            <brokerUrl>ssh://gitlab/pact-repo.git</brokerUrl>
            <consumerPacts>${project.build.directory}/pacts</consumerPacts>
          </configuration>
        </execution>
      </executions>
    </plugin>
  </plugins>
</build>
```


###Producer

Configure plugin in your pom.xml. This time,
the *download-pacts* goal is used:

```xml
<build>
  <plugins>
    <plugin>
      <groupId>com.github.warmuuh</groupId>
      <artifactId>pactbroker-maven-plugin</artifactId>
      <version>0.0.8</version>
      <executions>
        <execution>
          <id>download-pacts</id>
          <phase>generate-resources</phase>
          <goals><goal>download-pacts</goal></goals>
          <configuration>
            <brokerUrl>ssh://gitlab/pact-repo.git</brokerUrl>
            <providerPacts>${project.build.testOutputDirectory}/pacts-dependents</providerPacts>
            <provider>provider</provider>
          </configuration>
        </execution>
      </executions>
    </plugin>
  </plugins>
</build>
```
###Having consumer and producer in a multi-module project
Configure plugin at the parent pom
```xml
<build>
  <plugins>
      <plugin>
            <groupId>com.github.warmuuh</groupId>
            <artifactId>pactbroker-maven-plugin</artifactId>
            <version>0.0.8</version>
            <executions>
                <execution>
                    <goals>
                        <goal>upload-pacts</goal>
                    </goals>
                    <phase>test</phase>
                </execution>
                <execution>
                    <id>download-pacts</id>
                    <phase>generate-test-resources</phase>
                    <goals>
                        <goal>download-pacts</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
  </plugins>
</build>
```

At **generate-test-resources** phase the relevant provider pacts will be downloaded to the providerPacts directory to be able to verify provider behavior against consumers, meanwhile consumer pats will be generated at the test phase.
```xml
<build>
  <plugins>
    <plugin>
      <groupId>com.github.warmuuh</groupId>
      <artifactId>pactbroker-maven-plugin</artifactId>
      <version>0.0.8</version>
      <configuration>
        <brokerUrl>ssh://gitlab/pact-repo.git</brokerUrl>
        <consumerPacts>${project.build.directory}/pacts</consumerPacts>
        <providerPacts>${project.build.testOutputDirectory}/pacts-dependents</providerPacts>
        <provider>provider</provider>
      </configuration>
    </plugin>
  </plugins>
</build>
```

To provide credentials when using git repository while uploading
or downloading pacts, use the configuration sections as below:
```xml
    <configuration>
      <brokerUrl>https://github.com/pact-repo.git</brokerUrl>
      <pacts>target/pacts-dependents</pacts>
	  <provider>provider</provider>
      <username>user</username>
	  <password>password</password>
    </configuration>
```
