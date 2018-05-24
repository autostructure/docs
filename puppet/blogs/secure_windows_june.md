# Securing Windows with Puppet Enterprise

## Introduction

## Quick Start

Super easy to use!

```puppet
include ::secure_windows
```

Include it in your base profile for FULL compliance by default!

## No-op Mode

Run this to audit your systems without making any changes!

```puppet
puppet agent -t --noop
```

Or run it in the console

<SCREENSHOT>

## Enforcing

Within minutes the Member Server or Domain Controller will be secured!

## Selectively Enforcing Vulnerabilities

Keep in mind there are a lot of exceptions and you might want to turn some on or off depending on the system.

Here's how to do that:

```yaml
# hieradata/role/videoserver.yaml
secure_windows::stig::v73101::enorced: false
```

This disables the vulnerability for this kind of server. You can then use your own code.

## Writing Custom Code

Here's an example of a vulnerability we addressed. We used the following useful modules.

```puppet
insert manifest here
```

## Reverse Engineering Existing Security Settings

Use the puppet resource command to reverse engineer.

## What's Next?

Next on our roadmap is to fully support Windows Server 2012 for the DoD STIGs.

Following that, we aim to provide full coverage of the CIS Benchmarks for Windows Server 2012 and 2016.
