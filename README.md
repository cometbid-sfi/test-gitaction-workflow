This project is a template project, the first published **Cometbid.org** artifacts to Maven Central as an OSS project to kick-off our Contributions to the Java Open-Source Community.

The steps to accomplish this task are documented below.

# How to publish Project's release artifacts to Maven Central

## Informative POM
The first step is to choose a **groupId** that matches a domain that you own or, alternatively, the domain that is used for sharing your open source project.

We own `cometbid.org` domain name, so that was easy, and our projects will be mostly hosted over at GitHub, the following would all be valid groupIds:

- `org.cometbid`
- `org.cometbid.integration`
- `org.cometbid.ut` (We choose this because this project belongs to our utility package - `ut`)
- `com.github.cometbid-sfi`
- `io.github.cometbid-sfi`
  
The next thing to do is to make sure that your pom.xml file includes all of the required information:

- licenses
- developers
- organization(optional)
- SCM

```
<licenses>
  <license>
    <name>MIT License</name>
      <url>http://www.opensource.org/licenses/mit-license.php</url>
      <distribution>repo</distribution>
   </license>
</licenses>
<developers>
   <developer>
     <name>Adebowale Samuel</name>
     <email>samuel.adebowale@cometbid.org</email>
     <url>https://www.cometbid.org/</url>
     <organization>The Cometbid Software Foundation Inc.</organization>
     <organizationUrl>https://github.com/cometbid-project</organizationUrl>
   </developer>
</developers>
<organization>
   <name>Cometbid.Org</name>
   <url>https://www.cometbid.org/</url>
</organization>
<scm>
  <connection>scm:git:git://github.com/cometbid-project/test-gitaction-workflow.git</connection>
  <developerConnection>scm:git:ssh://github.com/cometbid-project/test-gitaction-workflow.git</developerConnection>
  <url>https://github.com/cometbid-project/test-gitaction-workflow/tree/main</url>
</scm>
```

Optional information section: github repo links

```
<distributionManagement>
  <repository>
    <id>github</id>
    <name>GitHub Apache Maven Packages</name>
    <url>https://maven.pkg.github.com/cometbid-project/test-gitaction-workflow</url>
  </repository>
</distributionManagement>

<issueManagement>
  <system>github</system>
  <url>https://github.com/cometbid-project/test-gitaction-workflow/issues</url>
</issueManagement>
```

## Include the plugins

When publishing artifacts to Maven Central, you have to make sure your **source code** and **javadoc** are uploaded as well. You can achieve this by adding the following section to your `pom.xml`:

```
 <plugin>
   <groupId>org.apache.maven.plugins</groupId>
   <artifactId>maven-source-plugin</artifactId>
   <version>3.3.0</version>
   <executions>
     <execution>
       <id>attach-sources</id>
       <goals>
         <goal>jar</goal>
       </goals>
     </execution>
   </executions>
</plugin>
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-javadoc-plugin</artifactId>
    <version>3.6.3</version>
    <executions>
      <execution>
         <id>attach-javadocs</id>
         <goals>
           <goal>jar</goal>
         </goals>
      </execution>
  </executions>
  <configuration>
     <additionalOptions>
       <additionalOption>-Xdoclint:none</additionalOption>
     </additionalOptions>
  </configuration>
</plugin>
```

You need to sign your artifacts before releasing them to Maven central. 

For example:
```
<plugin>
   <groupId>org.apache.maven.plugins</groupId>
   <artifactId>maven-gpg-plugin</artifactId>
   <version>3.1.0</version>
   <executions>
      <execution>
        <id>sign-artifacts</id>
        <phase>verify</phase>
        <goals>
           <goal>sign</goal>
        </goals>
        <configuration>
           <gpgArguments>
               <arg>--pinentry-mode</arg>
               <arg>loopback</arg>
           </gpgArguments>
        </configuration>
      </execution>
  </executions>
</plugin>
```

**NOTE**: the `pinentry-mode=loopback` is necessary to avoid GPG prompting manual entry of the GPG passphrase especially when running build on a remote agent like Github Actions!

## Create a Sonatype JIRA account
In order to publish your artifact, you must have Sonatype JIRA account. To do so visit this link: https://central.sonatype.com  

After successful registration, the following will be generated:
```
<server>
  <id>central</id>
  <username>sonatypeUser</username>
  <password>verySecretPassword</password>
</server>
```
Copy and keep in a safe place for later use, as those are login credentials needed to programmatically and remotely login & publish to Maven Central.

**NOTE:** If you need to deploy your artifact through local builds, you should those your credentials to your ~/.m2/settings.xml file as well.

In our case, we will be using Github Actions to automate the build and publishing process, hence the credentials were added as environment variables on Github.
The [CI/CD With GitHub Actions](#CI/CD) section details how to do this and more.

_Please notice that we kept the `ossrh` profile in the `pom.xml` as it was an old approach to publishing which has now been deprecated in favor of using all that is packaged under `central` profile_

**There is a somewhat annoying part of the entire process, understandably so but nevertheless tiresome:**   

_After registration with Sonatype, you will be required to supply your `groupId` especially if it's a registered domain name to verify that you actually own it_. 

For further details, please visit the links below:  
https://central.sonatype.org/faq/verify-ownership/#question  
https://central.sonatype.org/faq/publisher-early-access/#answer  
https://central.sonatype.org/faq/how-to-set-txt-record/  
https://central.sonatype.org/faq/a-human/   

<div id="The-Challenge"></div>

## The Challenging Part  

- **After Registering the Namespace and setting TXT Record with Domain registrar as specified in the Docs.**    

![Verification_pending](https://github.com/cometbid-sfi/test-gitaction-workflow/assets/20684020/17edbab7-f4cb-4e3f-b7ce-fc3a972d1bad)  

- **Verifying that TXT Record was set correctly as specified in the Docs.**    

![txt-setting-verified](https://github.com/cometbid-sfi/test-gitaction-workflow/assets/20684020/e73babe0-a81c-42dc-8166-0301bab1bec3)  

- **I had to wait for another 24hours, while waiting sent a mail notification to _Sonatype Cental Support team_:**
central-support@sonatype.com, with screenshots. And eventually, we got verified.  

![Verified_domain](https://github.com/cometbid-sfi/test-gitaction-workflow/assets/20684020/65cda021-58f8-47a9-9171-82df3d29650b)  


## Repository Setup
Finally, you need to include the Sonatype snapshots/staging repositories in your pom.xml as follows:

```
<plugin>
  <groupId>org.sonatype.central</groupId>
  <artifactId>central-publishing-maven-plugin</artifactId>
  <version>0.3.0</version>
  <extensions>true</extensions>
  <configuration>
     <publishingServerId>central</publishingServerId>
     <tokenAuth>true</tokenAuth>
     <autoPublish>true</autoPublish>
     <waitUntil>published</waitUntil>
     <outputfilename>${project.artifactId}-${project.version}.zip</outputfilename>
  </configuration>
</plugin>
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-release-plugin</artifactId>
  <version>3.0.1</version>
  <configuration>
       <autoVersionSubmodules>true</autoVersionSubmodules>
       <useReleaseProfile>false</useReleaseProfile>
       <releaseProfiles>release</releaseProfiles>
       <goals>deploy</goals>
  </configuration>                    
  <executions>
       <execution>
         <id>default</id>
         <goals>
           <goal>perform</goal>
         </goals>  
      </execution>
  </executions>
</plugin>
```

### We highly recommend having a different Maven profile for this, especially as that came in handy when we had to switch from `ossrh` to `central` during our setup process.
So put all the above stated plugins between `<plugins>...</plugins>` as shown below:

```
<profile>
    <id>central</id>
    <build>
       <plugins>
             .
             .
             .
       <plugins>
    <build> 
<profile>
```

You can then activate the profile and invariably the plugins during **maven build** using the below command:

`mvn --batch-mode clean deploy -P central -DskipTests=true`

take note of the flag `-P central`, that's the jackpot😄!

## GPG Setup
You’ll have to create and distribute a new GPG key, so start by downloading and installing GnuPG.
For a detailed tutoral on the setup process for generating and managing GPG Key pair(public/private key) visit this link
[GnuPG Tutorial](https://www.devdungeon.com/content/gpg-tutorial).

Preferably, you can follow [Sonatype Documentation](https://central.sonatype.org/publish/requirements/gpg/#generating-a-key-pair) to generate your keys, manage its expiration, and ultimately distribute the [public key to required keyservers](https://central.sonatype.org/publish/requirements/gpg/#distributing-your-public-key)!

The documentation detailed how to generate a keypair, extract the **public key** and distribute it, and how to extract the **private key** which you will need as Github secret.

Some of the keyservers to which you should send your public keys include the following:

- https://pgp.key-server.io/
- https://keyserver.ubuntu.com/
- https://pgp.mit.edu/
- http://keys.gnupg.net/  

IMPORTANT: make sure to remember your GPG passphrase/keep it somewhere safe!

Please skip the following section if you don’t intend on using GitHub Actions. To build and deploy your artifact to Maven Central repositories and perform the release process, just run:  

`mvn --batch-mode clean deploy -Dgpg.passphrase="myPassphrase" -P central -DskipTests=true`  

`-DskipTests=true` flag will ensure you avoid running **Unit/Integration test** during the publishing phase.

<div id="CI/CD"></div>

## CI/CD With GitHub Actions
To automate the build, deploy and publishing process, we highly recommend using **Github Actions** has it has some workflow actions that makes the process pretty easy.

First of all, you need to extract your **GPG private key** from the **GPG Keypair** generated earlier. 
To identify your GPG key and fetch its ID use this command `gpg --list-keys`; you can then export the ASCII-armored version of your private key by running.

`gpg --output private.pgp --armor --export-secret-key MY_KEY_ID`

**MY_KEY_ID** in our case, it was an _email address_, quite a good idea. You will be prompted for your passphrase, supply it and phiam, you'll have your **GPG private key** in a file named `private.pgp`.

Go to `Settings` on your Github account home page, scroll down to the `Security` section, under `Secrets and variables` click on _Actions_ tab, and add the following key/value pairs:

![GPG_KEYs](https://github.com/cometbid-sfi/test-gitaction-workflow/assets/20684020/ee3aa936-ca3c-4367-b0bb-6fa61c7b6dbe)

For GPG_PRIVATE_KEY, make sure to copy the entire content of `private.pgp` file generated.

**Do remember this?**
```
<server>
  <id>central</id>
  <username>sonatypeUser</username>
  <password>verySecretPassword</password>
</server>
```
**Now is the time to use those credentials. Supply the secrets as shown below:**

![Maven-central](https://github.com/cometbid-sfi/test-gitaction-workflow/assets/20684020/c13050ff-2346-42e9-94c1-8f531af5a3e9)

**The final Github secrets variables should end up looking like this:**

![Final output](https://github.com/cometbid-sfi/test-gitaction-workflow/assets/20684020/b6ec7349-a0f5-4dbc-811f-e7f4c021fb64)


You’re now ready to automate the entire build and deploy process with Github actions. Add the following `.github/workflows/release-to-maven-central.yml` file to your project. 
It will take care of **building, signing, and deploying your artifact** to the Sonatype maven central repository every time you initiate a `new release` as against anytime you push to `main` branch.

```
# This workflow will build and Publish a Java project with Maven, and cache/restore any dependencies to improve the workflow execution time
# For more information see: https://docs.github.com/en/actions/automating-builds-and-tests/building-and-testing-java-with-maven

# This workflow uses actions that are not certified by GitHub.
# They are provided by a third-party and are governed by
# separate terms of service, privacy policy, and support
# documentation.

name: release-to-maven-central
on:
  release:
    types: [created]

jobs:
  publish:
    name: Build and Publish
    runs-on: ubuntu-latest

    steps:
      - name: Checkout source code
        uses: actions/checkout@v4

      - name: Get the release version
        id: get_version
        run: echo "VERSION=${GITHUB_REF/refs\/tags\/v/}" >> $GITHUB_OUTPUT

      - name: Set up Maven Central Repository
        uses: actions/setup-java@v4
        with:
          distribution: "adopt"
          java-version: "17"
          server-id: central
          server-username: MAVEN_USERNAME
          server-password: MAVEN_CENTRAL_TOKEN
          gpg-private-key: ${{ secrets.MAVEN_GPG_PRIVATE_KEY }}
          gpg-passphrase: MAVEN_GPG_PASSPHRASE

      - name: Update package version
        run: mvn versions:set -DnewVersion=${{ steps.get_version.outputs.VERSION }}

      - name: Publish to Apache Maven Central
        run: mvn --batch-mode clean deploy -P central -DskipTests=true
        env:
          MAVEN_USERNAME: ${{ secrets.OSS_SONATYPE_USERNAME }}
          MAVEN_CENTRAL_TOKEN: ${{ secrets.OSS_SONATYPE_PASSWORD }}
          MAVEN_GPG_PASSPHRASE: ${{ secrets.MAVEN_GPG_PASSPHRASE }}
```

## Release Process
To verify that the publishing process went well, simply login to the Sonatype account with your credentials and click the **deployments** tab:

![Deployment-tab](https://github.com/cometbid-sfi/test-gitaction-workflow/assets/20684020/db82d453-6769-4bcd-b2b7-92ed5ec58c58)   


Or 
#### Click the Browse link and search by `Component Namespace(org.cometbid.ut)`:  

![search-and-found](https://github.com/cometbid-sfi/test-gitaction-workflow/assets/20684020/b99e0228-9320-4298-94c6-105a018975b3)  

![overview-page](https://github.com/cometbid-sfi/test-gitaction-workflow/assets/20684020/d5cced9a-e63b-439c-82b0-1abdb31c6b6a)  


## Final Thoughts
The process to deploy and release artifacts to Maven Central looks quite daunting, but worth the chase as many of these steps are one-time only and others are quick and easy to repeat every time you want to release a new library for the world to use!

I wish and hope the process is more easy and simple in the future, atleast for now we can be rest assured we achieve the feat of publishing our projects for the World to use, and I look to the rewarding experience that comes with it.

Any comments, contributions or suggestions can be addressed directly on issues you can open on this Repository as we hope to improve this process from time to time as required.

Thanks! 👍 ㊗️


