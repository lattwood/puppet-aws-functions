
README
======

This is a repository of useful functions for integrating puppet with Amazon Web Services.
These custom parser functions are executed on the puppet master during catalog compilation
The gem and aws credentials need only be installed on the master.

Installation
------------

The files in functions should be placed in your puppet config tree in a module at lib/puppet/parser/functions.
The files in facter should be placed in lib/facter.

### Dependencies

- fog gem
- curl
- facter ec2_meta_data lib

### Credentials

There should be a yaml file at '/etc/puppet/fog_cred' in the format:

    :default:
            :aws_access_key_id:     XXXXXXXXXXXXXXXXX
            :aws_secret_access_key: XXXXXXXXXXXXXXXXXXXXXXXXXX


Functions
---------

### get_ips_by_tag($key, $value)

returns a hash of the internal IP addresses of all instances matching the aws
resource tag key, value, or key => value pair passed in. Useful for load balancer
configs.

### elb_register_instance('ELBNAME')

Registers the instance ID with the elb matching the passed name

### r53_set_record($zone, $name, $value, $type, $ttl)

creates a route53 dns record (all dns names must be passed with a . at the end)

### s3_get_etag($bucket, $key)

returns the Etag (md5) of an s3 object

### s3_get_curl($bucket, $key, $filename, $expires)

returns a curl command and signed url referencing the specified s3 object

### s3 example:

    define s3get ($bucket='puppet-filesource', $cwd='/tmp', $expires=30) {

        $file_checksum = s3getEtag($bucket, $key)

        exec { "s3getcurl[$bucket][$title][$name]":
               cwd => $cwd,
               unless => "echo \"$file_checksum  $name\" | md5sum -c --status",
               command => s3_get_curl($bucket, $title, $name, $expires),
        }
    }


