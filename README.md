# trivy-java-db

This repository is a fork of [trivy-java-db](https://github.com/aquasecurity/trivy-java-db).   that restores the ability to build the Java vulnerability database locally.

Upstream [trivy-java-db](https://github.com/aquasecurity/trivy-java-db).   currently relies on the private repository aquasecurity/maven-index for generating the database. Because that repository is not publicly accessible, it prevents external users from rebuilding the database independently. This is a problem as the upstream database is built via a scheduled GitHub Actions workflow. GitHub automatically disables scheduled workflows after 60 days of repository inactivity. If that occurs, the database will no longer be updated until the workflow is manually re-enabled by an Aqua Security team member. By restoring the ability to build the database locally, this fork allows users to maintain an up-to-date Java vulnerability database even if the upstream workflow is disabled.

> This project is not an criticism of the Trivy or Aqua Security. The work done by the Trivy maintainers has been outstanding. I don't take any credit for any of the work done. This fork exists purely to address an operational limitation that affects independent builds.

## Disclaimer

This project is **not affiliated with or endorsed by Aqua Security**.  This project includes a modified version of [trivy-java-db](https://github.com/aquasecurity/trivy-java-db), originally developed by [Aqua Security](https://www.aquasec.com/) 

**Modifications:**

- Restored ability to build the vulnerability database without relying on the private `aquasecurity/maven-index` repository, allowing for independents builds.

## Update interval
Building the database takes a long time (3+ hours), which would burn through my Free GitHub Actions minutes pretty quickly. Because of that, I’m not going to be able to including a daily updating Docker image build workflow in this repo.

If you want to build the database, just fork the repository, modify GH_OWNER and IMAGE_NAME variables and run the GitHub Action yourself.

Sorry! 

---

## Overview
`trivy-java-db` parses all indexes from [maven repository](https://repo.maven.apache.org/maven2) and stores `ArtifactID`, `GroupID`, `Version` and `sha1` for jar files to SQlite DB.

The DB is used in Trivy to discover information about `jars` without GAV inside them.



## Download the java indexes database
You can download the actual compiled database via [Trivy](https://aquasecurity.github.io/trivy/) or [Oras CLI](https://oras.land/cli/).

Trivy:
```sh
TRIVY_TEMP_DIR=$(mktemp -d)
trivy --cache-dir $TRIVY_TEMP_DIR image --download-java-db-only
tar -cf ./javadb.tar.gz -C $TRIVY_TEMP_DIR/java-db metadata.json trivy-java.db
rm -rf $TRIVY_TEMP_DIR
```

oras >= v0.13.0:
```sh
$ oras pull ghcr.io/aquasecurity/trivy-java-db:1
```

oras < v0.13.0:
```sh
$ oras pull -a ghcr.io/aquasecurity/trivy-java-db:1
```
The database can be used for [Air-Gapped Environment](https://aquasecurity.github.io/trivy/latest/docs/advanced/air-gap/).