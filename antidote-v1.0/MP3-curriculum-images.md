Stages 1 and 2 are part of the nightly build process.  Stage 1 covers the case of building debian or centos from scratch with minimal drivers.  Any configuration that requires internet access (such as installing packages with apt, yum, pip, etc) are done in stage 2.  Stage 3 is where an image is configured for a particular lesson.  

Stage 1

    The first stage is for custom images built on top of Debian or Centos.  It is in this stage that the actual Debian or Centos image is created.  The kernel for these images will be built from the linux source files of the current stable release of these distros.  The kernel configuration will be minimal.  At the time of this writing, we recommend using the minimal kernel config provided by the Firecracker project as a starting point.  That is [located here](https://github.com/firecracker-microvm/firecracker/blob/master/resources/microvm-kernel-config).

Work Items:  

Document the build process for Debian and Centos.  

  * This will likely entail building the kernel and it's lib modules into a target directory.
  * Subsequently we use something like chroot/fakeroot to bootstrap for the intended distro.
  * Use kernel sources from relevant distros, rather than mainline kernel.
  * Use debootstrap for debian
  * Follow instructions on Centos website to use yum and rpm to bootstrap for centos.
  * Convert directory structure to bootable qcow2 image, installing bootloader at some point
  * Review kernel config file.  Ensure we are loading just virtio-drivers and enabling any relevant networking features

Stage 2

    Stage 2 starts with an image file.  We may have built this file in stage 1, or it may have been provided to us.  Whatever the image, it must be converted to qcow2 if it isn't already.  In this stage, we install any items requiring internet access.  First, however, we need to create a user "antidote" with password "antidotepassword" with passwordless sudo ability.  We need to ensure that SSH is enabled and will accept a password.  Any static files that require "privacy" such as license files should be installed at this time as well.  Ansible is an obvious choice for most of these tasks.  "Pre" and "Post" scripts (in BASH or similar) could be run as well, again called with Ansible.

    At the end of stage 2, the only thing remaining to do before an instance is presented to the user is configure it for a specific lesson.  This happens in stage 3.  Again, be aware that there is no internet access when the instance is launched so anything requiring internet access will need to be pre-installed as part of the image on docker hub.  If an existing image can be used, but requires the installation of an additional package, then open a pull request to add additional packages to the Ansible playbook for that image.  At that time, we'll figure out if it should be it's own image or if we can reuse an existing image.  

Work Items:
  * Determine a set of base packages, if any, for all images.  
  * Determine exact process for adding "antidote" user.  This will likely be different for different images.
  * For instance, "virt-customize" could be used for Centos.  Napalm might be used for a network operating system.  And so on.
  * Determine exact process for converting an image to qcow2 if required.

Stage 3

    In this stage, final local configuration changes are made to an image at the time a lesson is launched.  NetConf configuration via Napalm, Python scripts, and Ansible playbooks are the three options we have here.  Common tasks and items should have templates, scripts, or Ansible playbooks written which can be re-used by lesson creators.  For instance, a Python module for specifying a list of files to copy to the image.  Another example would be boiler-plate JSON or XML config for napalm based configurations.  Another example would be parameterized configuration of network interfaces.

Work Items:

  * Determine what common tasks or items could be modularized for re-use across the three configuration methods.
  * Write re-usable code for these things.

