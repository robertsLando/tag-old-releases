# tag-old-releases

Did you forget to tag all your npm releases in git? Yeah me too. This utility tags them retroactively.

Obviously, that's a subjective thing. So use this at your own risk.

## Usage

```
npm install -g tag-old-releases
cd src/my-repo
tag-old-releases
```

To print `git tag` commands without actually running them:

```
npm install -g tag-old-releases
cd src/my-repo
tag-old-releases --dry-run
```


## How it works

`tag-old-releases` scans through `git log` of the `master` branch, checking out each commit as it goes backwards through time. The oldest release in which `package.json` has a given `version` is tagged as the release for this version, unless a tag already exists. Tags are named simply `x.y.z` (version number).

The rationale is that your release process looks like this:

* People contribute changes, which get merged over various commits, all of which have the old version number
* You decide what the new version number will be when you are actually ready to release, which makes sense for semantic versioning reasons; in that commit you edit your changelog and package.json
* That stuff gets merged back to `master`
* You `npm publish`

If your process differs — for instance if well-meaning contributors bump the version number early and then other critical commits are made before `npm publish` - then this script will make the wrong choice for those versions. Sorry - best we can do.

## A better idea (going forward)

In future, we suggest a script like this in place of `npm publish`:

```
#!/usr/bin/env node

var exec = require('child_process').execSync;
var version = require(process.cwd() + '/package.json').version;
exec('git tag ' + version + ' && git push --tags && npm publish');
```

This will keep you out of trouble by tagging releases at the time you publish.
