# ruby-pwsh

> _The PowerShell gem._

This gem enables you to execute PowerShell from within ruby without having to instantiate and tear down a PowerShell process for each command called.
It supports Windows PowerShell as well as PowerShell Core (and, soon, _just_ PowerShell) - if you're running *PowerShell v3+, this gem supports you.

The `Manager` class enables you to execute and interoperate with PowerShell from within ruby, leveraging the strengths of both languages as needed.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'ruby-pwsh'
```

And then execute:

```shell
bundle install
```

Or install it yourself as:

```shell
gem install ruby-pwsh
```

## Usage

Instantiating the manager can be done using some defaults:

```ruby
# Instantiate the manager for Windows PowerShell, using the default path and arguments
# Note that this takes a few seconds to instantiate.
posh = Pwsh::Manager.instance(Pwsh::Manager.powershell_path, Pwsh::Manager.powershell_args)
# If you try to create another manager with the same arguments it will reuse the existing one.
ps = Pwsh::Manager.instance(Pwsh::Manager.powershell_path, Pwsh::Manager.powershell_args)
# Note that this time the return is very fast.
# We can also use the defaults for PowerShell Core, though these only work if PowerShell is
# installed to the default paths - if it is installed anywhere else, you'll need to specify
# the full path to the pwsh executable.
pwsh = Pwsh::Manager.instance(Pwsh::Manager.pwsh_path, Pwsh::Manager.pwsh_args)
```

Execution can be done with relatively little additional work - pass the command string you want executed:

```ruby
# Instantiate the Manager:
posh = Pwsh::Manager.instance(Pwsh::Manager.powershell_path, Pwsh::Manager.powershell_args)
# Pretty print the output of `$PSVersionTable` to validate the version of PowerShell running
# Note that the output is a hash with a few different keys, including stdout.
pp(posh.execute('$PSVersionTable'))
# Lets reduce the noise a little and retrieve just the version number:
# Note: We cast to a string because PSVersion is actually a Version object.
pp(posh.execute('[String]$PSVersionTable.PSVersion'))
# We could store this output to a ruby variable if we wanted, for further use:
ps_version = posh.execute('[String]$PSVersionTable.PSVersion')[:stdout].strip
pp("The PowerShell version of the currently running Manager is #{ps_version}")
```

## Reference

You can find the full reference documentation online, [here](https://rubydoc.info/gems/ruby-pwsh).

<!-- ## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake spec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org). -->

## Releasing the Gem and Puppet Module

Steps to release an update to the gem and module include:

1. From main, checkout a new working branch for the release prep (where xyz is the appropriate version, sans periods):
   ```bash
   git checkout -b maint-release_prep_xyz
   ```

2. Update the version in `lib/pwsh/version.rb` and `metadata.json` to the appropriate version for the new release.

3. Run the changelog update task (make sure to verify the changelog, correctly tagging PRs as needed):
   ```bash
   bundle exec rake changelog
   ```

   4. Commit your changes with a short, sensible commit message, like:
   ```bash
   git add lib/pwsh/version.rb
   git add metadata.json
   git add CHANGELOG.md
   git commit -m '(MAINT) Prep for x.y.z release'
   ```

5. Push your changes and submit a pull request for review _against main:
   ```bash
   git push -u origin maint_release_prep_xyz
   ```

6. Ensure tests pass and the code is merged to `main`.

7. Once the release_prep PR has been merged, checkout main and pull down the latests changes.
   ```bash
   git checkout main
   git pull
   ```

8. Assuming that the release_prep merge commit is at the HEAD of main we can simply create and push a tag as follows (replacing xyz with the appropriate version).
   ```bash
   git tag -a xyx -m "Release xyz"
   git push --follow-tags
   ```

9. Execute the publish workflow. This will:
   - Create a GitHub release
   - Build and publish the Gem
   - Build and publish the Puppet module

10. Finally check that the expected versions are present on rubygems.org and the Forge.

## Known Issues

## Supported Operating Systems

The following platforms are supported:

- Windows
- CentOS
- Debian
- Fedora
- OSX
- RedHat
- Ubuntu
