
## How to release

### Setup

You will need:
* PGP code signing keys, published in KEYS
* Permission to stage artifacts in Nexus

Make sure you have permission to deploy Parquet artifacts to Nexus by pushing a snapshot:

```
mvn deploy
```

### Release process

Parquet uses the maven-release-plugin to tag a release and push binary artifacts to staging in Nexus. Once maven completes the release, the offical source tarball is built from the tag.

Before you start the release process:

1. Verify that the release is finished (no planned JIRAs are pending)
2. Build and test the project
3. Verify that the changelog is up-to-date

#### 1. Run the prepare script

```
sh dev/release-prepare.sh <version>
```

This runs maven's release prepare with a consistent tag name. After this step, the release tag will exist in the git repository.

#### 2. Run release:perform to stage binaries

```
mvn release:perform
```

This uploads binary artifacts for the release tag to [Nexus][nexus].

#### 3. In Nexus, close the staging repository

Closing a staging repository makes the binaries available in [staging][staging], but does not publish them.

1. Go to [Nexus][nexus].
2. In the menu on the left, choose "Staging Repositories".
3. Select the Parquet repository.
4. At the top, click "Close".

#### 4. Run the source tarball script

```
sh dev/source-release.sh <version> <rc-number>
```

This script builds the source tarball from the release tag's SHA1, signs it, and uploads the necessary files with SVN.

The source release is pushed to https://dist.apache.org/repos/dist/dev/incubator/parquet/

The last message from the script is the release commit's SHA1 hash and URL for the VOTE e-mail.

#### 5. Send a VOTE e-mail to dev@parquet.incubator.apache.org

Here is a template you can use. Make sure everything applies to your release.

```
Subject: Release Apache Parquet Format (Incubating) <VERSION> RC<NUM>
```
```
Hi everyone,

I propose the following RC to be released as official Apache Parquet <VERSION> release.

The commit id is <SHA1>
* This corresponds to the tag: apache-parquet-format-<VERSION>
* https://github.com/apache/incubator-parquet-format/tree/<SHA1>
* https://git-wip-us.apache.org/repos/asf/incubator/repo?p=incubator-parquet-format.git&a=commit&h=<SHA1>

The release tarball, signature, and checksums are here:
* https://dist.apache.org/repos/dist/dev/incubator/parquet/<PATH>

You can find the KEYS file here:
* https://dist.apache.org/repos/dist/dev/incubator/parquet/KEYS

Binary artifacts are staged in Nexus here:
* https://repository.apache.org/content/groups/staging/org/apache/parquet/parquet-format/

This release includes important changes that I should have summarized here, but I'm lazy.

Please download, verify, and test.

Please vote by <72 HOUR FROM NOW>

[ ] +1 Release this as Apache Parquet Format <VERSION>
[ ] +0
[ ] -1 Do not release this because...

```


[nexus]: https://repository.apache.org/
[staging]: https://repository.apache.org/content/groups/staging/org/apache/parquet/
