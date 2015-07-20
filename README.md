Laravel AMI
===========

An AMI for Laravel projects based on Homestead provisioning scripts. __Do not use in production__; this AMI was created for use with the [Vagrant AWS](https://github.com/mitchellh/vagrant-aws) provider for running tests in a CI environment, and is likely unsecure.

Usage
-----

Simply specify the AMI `ami-0f743235` in Vagrant AWS' configuration.

Minimal configuration for an existing Laravel project:

```
config.vm.provider :aws do |aws, override|
  settings = YAML::load(File.read(homesteadYamlPath))

  # Dummy Vagrant box provided for `Vagrant AWS`
  override.vm.box_url = "https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box"
  override.vm.box = "dummy"

  aws.access_key_id = "MY_COOL_ACCESS_ID"
  aws.secret_access_key = "MY_COOL_ACCESS_KEY"

  # Set the AMI for `laravel-ami`, and the region to Sydney
  aws.ami = "ami-0f743235"
  aws.region = "ap-southeast-2"
  aws.instance_type = "t1.micro"

  aws.security_groups = ["my-cool-security-group"]
  aws.keypair_name = "my_cool_keypair"

  aws.tags = {
   'Name' => settings["name"]
  }

  override.ssh.username = "ubuntu"
  override.ssh.private_key_path = "path/to/my/cool/key.pem"

  settings["folders"].each do |folder|
    # Don't sync `node_modules` or `vendor`, else we'll make provisioning super slow
    override.vm.synced_folder folder["map"], folder["to"], type: "rsync",
      rsync__exclude: ["node_modules/", "vendor/"]
  end
end
```

Note that AWS is the only supported provider, and as such the box cannot be used with other providers such as VirtualBox.
