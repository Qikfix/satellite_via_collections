# satellite_via_collections

Hi all

This project will help you to install your Satellite, easily!

The goal of this project is to present how to use collections to install your Satellite in a brand new BaseOS.

# Disclaimer

This project scripts are NOT delivered and/or released by Red Hat. This is an independent project to help customers and Red Hat Support team to analyze some data, help with the troubleshooting, or implement something that is not available in the official way at the moment this one was created.


# Requirements
- The server that will be installed `Red Hat Satellite`, also, the credential to register the machine properly.

---

## The Steps

Let's keep it simple, below, you can see all the necessary steps to deploy your Satellite using the collections

- Register the server

```
subscription-manager register
```

- Enable the Satellite repository. For this example, let's use the `6.16` version
```
subscription-manager repos --enable satellite-6.16-for-rhel-9-x86_64-rpms
```

- Install the packages `ansible-collection-redhat-satellite` and `ansible-collection-redhat-satellite_operations`
```
dnf install -y ansible-collection-redhat-satellite ansible-collection-redhat-satellite_operations
```

- Checking the collections available
```
ansible-galaxy collection list
```

- You can download the playbook to your brand new RHEL server
```
curl -o satellite.yml https://raw.githubusercontent.com/Qikfix/satellite_via_collections/refs/heads/main/satellite.yml
```

- Let's update the credentials inside the satellite.yml
```
    rhsm_username: "your_portal_username_here"
    rhsm_password: "your_portal_password_here"
```

- Don't forget to upload a manifest, with at least one subscription on it to your brand new RHEL server. In our example, the file will be located at `/root/manifest_file.zip`


- Time to have some fun
```
ansible-playbook -i satellite-prd.king.lab, -e "sat_version=6.16" -e "manifest_path=/root/manifest_file.zip" -e "base_os=rhel9" satellite.yml
```

Some side note here, as we can see above, we are passing some external vars, like the `sat_version`, the `base_os` and also the `manifest_path`. You can adequate it properly, based on your needs.


- ERROR??
```
ERROR! couldn't resolve module/action 'community.general.redhat_subscription'. This often indicates a misspelling, missing collection, or incorrect module path.

The error appears to be in '/root/satellite.yml': line 106, column 7, but may
be elsewhere in the file depending on the exact syntax problem.

The offending line appears to be:

    # Register to RHSM
    - name: Register to RHSM
      ^ here
```

The error above was on purpose. On this play, I'm doing the `rhsm registration`, and to achieve that, I'm using the collection `community.general` that is not installed by default. If you are going to change this play and remove this call, definitely, you don't need it. However, if you would like to keep this entry, and let your server get reregistered and move on with all the checks/tests, let's proceed as below.


- Installing the community.general
```
ansible-galaxy collection install community.general
```

- Let's do a double check, once again
```
ansible-galaxy collection list
```

- And now, let's have some fun for good! :-)
```
ansible-playbook -i satellite-prd.king.lab, -e "sat_version=6.16" -e "manifest_path=/root/manifest_file.zip" -e "base_os=rhel9" satellite.yml
```

Ps.: You can see the `-i satellite-prd.king.lab,` this is the server FQDN in question I'm installing, and the comma in the end of the FQDN is there, not by mistake, but on purpose.


Enjoy it and let me know if you are facing issues!