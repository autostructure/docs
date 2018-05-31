# Securing Windows with Puppet Enterprise

## Introduction

The team at Autostructure has found that Puppet Enterprise is useful in the security space, as it can monitor, enforce, and report on baseline security configurations across your infrastructure.

We have developed a module on the forge that hardens Windows Server 2012 and 2016 to Department of Defense (DOD) Security Technical Implementation Guide (STIG) specifications. This is an important part of NIST 800-53 compliance. Along our journey developing this module, we learned a lot about how to secure Windows with Puppet and are going to share our experience.

## Getting Started

The `secure_windows` module is very easy to get started with. By default, all vulnerabilities are enforced with reasonable default values. To get started, simply instantiate the class in your puppet code. We recommend including this module as part of your base profile for Windows.

```puppet
# Base Profile for Windows
class profile::base_windows {
  # Include Windows Hardening on all Windows Servers
  class { '::secure_windows': }
}
```

The above code is all you need to reach full compliance. Separate hardening rules will be applied for standalone servers, member servers, and domain controllers. You do not need to put any extra logic into your base profile for these server types, our module does the heavy lifting for you.

## No-op Mode

As an example, let's take an existing domain controller dc1.example.com and harden it.

The `secure_windows` module will make a lot of changes on the system, and it can be helpful to review these changes before enforcing them on your production servers. To do this, run puppet in `noop` mode. You can do this in the PE Console on on the command line with `puppet agent -t --noop`.

This will output all the changes that Puppet would have made on the system. This report tells us what is out of compliance on the machine and can be used for auditing your infrastructure. In the case of dc1.example.com we will examine the changes our module will make to security settings and then choose if we would like to enforce them.

After reviewing the report with our sysadmins and security team, we go ahead and enforce the changes to become compliant.

## Enforce!

Run puppet on your machines and within minutes they will be compliant.

```shell
puppet agent -t
```

dc1.example.com is now compliant.


## Under the Hood

Here's an example of a vulnerability we addressed for dc1.example.com.

```puppet
# This class manages V-73287
# The Fax Server role must not be installed.
class secure_windows::stig::v73287 (
  Boolean $enforced = false,
) {
  if $enforced {
    windowsfeature { 'fax':
      ensure => absent,
    }
  }
}
```

Each vulnerability has a parameter `$enforced` which can be turned on or off. By default, Automatic Parameter Lookup returns a value of `true` for all vulnerabilities, but you can use your organization's Hiera to disable them selectively, as we will cover below.

For vulnerability V-73287 we followed the guidelines to remove the Fax Server Role and chose to use the windowsfeature module to accomplish this. This will only run on machines for which `$enforced` is true, which is all vulnerabilities by default.

Other vulnerabilities only apply to domain controllers. Some spit out warnings.


## Selectively Enforcing Vulnerabilities

Keep in mind there are a lot of exceptions and you might want to turn some on or off depending on the system.

Here's how to do that:

```yaml
# hieradata/role/videoserver.yaml
secure_windows::stig::v73101::enforced: false
```

This disables the vulnerability for this kind of server. You can then use your own code.


## Reverse Engineering Existing Security Settings

Use the puppet resource command to reverse engineer.



## What's Next?

Next on our roadmap is to fully support Windows Server 2012 for the DoD STIGs.

Following that, we aim to provide full coverage of the CIS Benchmarks for Windows Server 2012 and 2016.
