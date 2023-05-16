# Github action maven release - Sample project

This repository is a sample project to show you a usage of [the maven github action](https://github.com/im-sandbox-phanirb/publish-java-package-with-maven-on-github-packages) 
in a dummy Java application.


# Release your maven project using GitHub actions

We will see how you can use maven release inside a GitHub actions, to release your Java project.

Disclaimer: I am the author of the GitHub action that I will show you how to leverage today: https://github.com/im-sandbox-phanirb/publish-java-package-with-maven-on-github-packages

## Sample repository

I will use a sample repository to illustrate how this GitHub actions. The repository can be found here: https://github.com/im-sandbox-phanirb/publish-java-package-with-maven-on-github-packages

The readme of this GitHub action covers all the features it offers.


## Prepare your repository

Before you even begin setting up this github action, you would need to set up your pom.xml first to be ready for maven releases. We recommend you to refer to the maven release plugin documentation for more details: https://maven.apache.org/maven-release/maven-release-plugin/

#### Configure the Distribution

```
<distributionManagement>
    <repository>
      <id>github</id>
      <name>GitHub Packages</name>
      <url>https://maven.pkg.github.com/im-sandbox-phanirb/publish-java-package-with-maven-on-github-packages</url>
    </repository>
  </distributionManagement>
  ```
  
  ## Setup a basic CI as a base for our release sample

In our sample repository, we will assume we got a basic CI in place that build our maven application. 

We will start with a basic .github/workflows/publish-java-maven.yml:

```
name: Publish package to GitHub Packages
on: 
  workflow_dispatch:
jobs:
  publish:
    runs-on: ubuntu-latest 
    permissions: 
      contents: read
      packages: write 
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-java@v3
        with:
          java-version: '11'
          distribution: 'adopt'
          
      - name: Build with Maven
        run: mvn -B package --file pom.xml
```

We will setup create release, tagging & uplaoding the artifats to release

```
 - name: Create Release
        id: create_release
        uses: actions/create-release@v1.1.0
        env:
          GITHUB_TOKEN: ${{ secrets.JAVA_TOKEN }}
        with:
          tag_name: v1.4.0
          release_name: Release 1.4.0
          body: |
            Release notes for version 1.4.0.
            draft: false
            prerelease: false
      - name: Upload artifact to release
        uses: actions/upload-artifact@v2
        with:
          name: My JAR File
          path: target/*.jar
          release-url: ${{ steps.create_release.outputs.upload_url }}
        env:
          GITHUB_TOKEN: ${{ secrets.JAVA_TOKEN }}
```
Congratulations! At this point you should have a functional maven release in place, which bump the version automatically.

We will publish to packages
```
 - name: Publish package
        run: mvn --batch-mode deploy
        env:
          GITHUB_TOKEN: ${{ secrets.JAVA_TOKEN }}
 ```
 



















