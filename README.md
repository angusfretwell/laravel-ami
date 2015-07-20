Laravel AMI
===========

An AMI for Laravel projects based on Homestead provisioning scripts. __Do not use in production__; this AMI was created for use with the [Vagrant AWS](https://github.com/mitchellh/vagrant-aws) provider for running tests in a CI environment, and is likely insecure.

Usage
-----

Simply specify the AMI `ami-0f743235` in Vagrant AWS' configuration.

Minimal configuration for an existing Laravel project:

```ruby
config.vm.provider :aws do |aws, override|
  settings = YAML::load(File.read(homesteadYamlPath))

  # Dummy Vagrant box provided for Vagrant AWS
  override.vm.box_url = "https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box"
  override.vm.box = "dummy"

  aws.access_key_id = "MY_COOL_ACCESS_ID"
  aws.secret_access_key = "MY_COOL_ACCESS_KEY"

  # Set the AMI, and the region to Sydney
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

License
-------

The MIT License (MIT)

Copyright (c) 2015 Angus Fretwell

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
