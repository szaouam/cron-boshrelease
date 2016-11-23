# BOSH Release for cron

This BOSH release is intended to allow you to add generic crontab entries
to any existing BOSH deployment by colocating the `cron` job alongside your
VMs.

Additionally, it serves as a good starting point, should you want to `bosh-gen extract-job`
into your own BOSH release, to make it easier to tie a cronjob to your specific release.

### Deploying
Modify the sample stubs to suit your deployment environment in `templates`.

The next step is to to generate the manifest file that will be used for deployment. Run the `make_manifest` script  `./templates/make_manifest <openstack|warden|aws>`. 

If are using an Openstack environment, the cron release can be deployed using the bosh v2 manifest (must have a cloud-config in your current bosh).
You have to modify the manifest-openstack.yml file to suit your deployment environment.

 


### Colocation Instructions

To colocate, ensure your job has the `cron` template specified:

```
jobs:
- name: myjob
  templates:
  - name: cron
    release: cron
```

For configuring cron entries, use the following properties:

```
properties:
  cron:
    variables: # a key-value map of any environment variables you want set in the crontab
      VAR1: val1
      VAR2: val2
    entries: # a list of key-value objects representing cron entries
    - command: echo hi!
      minute: '*'
      hour: '*'
      day: '*'
      month: '*'
      wday: '*'
```

When deployed, this release will generate a crontab, stick it in /etc/cron.d/cron-boshrelease-crontab, and reload the running cron process to read the new crontab.

### Extraction

Feel free to use bosh-gen to extract the `cron` job into your BOSH release, and provide
custom crontabs that you want always present. Just make sure you modify the filename
being used to install the crontab, so it won't conflict if a user colocates this
BOSH release on top of yours!
