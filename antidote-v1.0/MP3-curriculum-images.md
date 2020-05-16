# Antidote Mini-Project 3: Images Pipeline

Part of what makes Antidote such a powerful and effective tool for teaching complex topics is the fact that we can pre-build much if not all of the dependencies into an immutable image in advance, that starts the same way for every learner.

Unfortunately, as the recipient of all the complexity we're trying to hide from the learner, images have become the single biggest hurdle for content contributors. It's not obvious to a would-be lesson author what images are available to use, what they do, and why they would choose to build their own, or even how they would go about doing that.

There are a few reasons for this:


The solution to this is:

- A consistent base that runs a lightweight operating system as a virtual machine. The idea is the lightweightedness of a Docker container with the security and isolation that a hypervisor offers
- Tooling for accomplishing common image build and configuration tasks




Ideas:

- Make images their own contribution. They must be done separately from and in advance of any lesson contributions that would need them.

- They'll need their own tests, as well as a way of describing them.

- That way, you can point some outside software at a curriculum repo and it can tell you everything about the images that are stored there, what their capabilities are, etc.




debian chroot

kernel config options:
VIRTIO_NET = Y/N/M (yes, no, module)


TODO:
- Specify what the learner actually needs to
- When do image files get copied into the image?
- How can lesson files get mounted to the running OS?
- Create a "lib" directory in the configurator image



## Image Build Pipeline

Stages 1 and 2 are part of the nightly build process.  Stage 1 covers the case of building debian or centos from scratch with minimal drivers.  Any configuration that requires internet access (such as installing packages with apt, yum, pip, etc) are done in stage 2.  Stage 3 is where an image is configured for a particular lesson.  

### Stage 1

    The first stage is for custom images built on top of Debian or Centos.  It is in this stage that the actual Debian or Centos image is created.  The kernel for these images will be built from the linux source files of the current stable release of these distros.  The kernel configuration will be minimal.  At the time of this writing, we recommend using the minimal kernel config provided by the Firecracker project as a starting point.  That is [located here](https://github.com/firecracker-microvm/firecracker/blob/master/resources/microvm-kernel-config).


Detail from Derick:
1. Create the chroot (bare-bones)
2. Compile kernel (with /lib) using firecracker kernel config options - those are pretty good so let's just use that. This results in a .deb file when building this on a debian system as the host, and is the easiest way
3. Default directory structure like /etc /home etc - and config files in /etc that are necessary to boot, like fstab. Not borrowable from firecracker, but we can steal these from a default debian building
4. Install a bootloader to the chroot - grub likely
5. Install script that stands up network interfaces on boot in a predictable way since we're not using an init system. If this ends up being too hard, we can use initrd, but let's try without it.
6. Execute  the user's "install.sh" script that installs and configures all the packages they need for their lesson
7. Create qcow2 image
8. Boot the image
9. Run standard tests (i.e. can we ssh to this)
10. Run image-specific tests in test.sh (via SSH)





Work Items:  

Document the build process for Debian and Centos.  

  * This will likely entail building the kernel and it's lib modules into a target directory.
  * Subsequently we use something like chroot/fakeroot to bootstrap for the intended distro.
  * Use kernel sources from relevant distros, rather than mainline kernel.
  * Use debootstrap for debian
  * Follow instructions on Centos website to use yum and rpm to bootstrap for centos.
  * Convert directory structure to bootable qcow2 image, installing bootloader at some point
  * Review kernel config file.  Ensure we are loading just virtio-drivers and enabling any relevant networking features

### Stage 2

(from derick convo)
Consistent device / HW addressing for network interface, etc

    Stage 2 starts with an image file.  We may have built this file in stage 1, or it may have been provided to us.  Whatever the image, it must be converted to qcow2 if it isn't already.  In this stage, we install any items requiring internet access.  First, however, we need to create a user "antidote" with password "antidotepassword" with passwordless sudo ability.  We need to ensure that SSH is enabled and will accept a password.  Any static files that require "privacy" such as license files should be installed at this time as well.  Ansible is an obvious choice for most of these tasks.  "Pre" and "Post" scripts (in BASH or similar) could be run as well, again called with Ansible.

    At the end of stage 2, the only thing remaining to do before an instance is presented to the user is configure it for a specific lesson.  This happens in stage 3.  Again, be aware that there is no internet access when the instance is launched so anything requiring internet access will need to be pre-installed as part of the image on docker hub.  If an existing image can be used, but requires the installation of an additional package, then open a pull request to add additional packages to the Ansible playbook for that image.  At that time, we'll figure out if it should be it's own image or if we can reuse an existing image.  

Work Items:
  * Determine a set of base packages, if any, for all images.  
  * Determine exact process for adding "antidote" user.  This will likely be different for different images.
  * For instance, "virt-customize" could be used for Centos.  Napalm might be used for a network operating system.  And so on.
  * Determine exact process for converting an image to qcow2 if required.



### Stage 3 (not really part of the pipeline but I think Derick is right that we should add tooling here to make things easier)

    In this stage, final local configuration changes are made to an image at the time a lesson is launched.  NetConf configuration via Napalm, Python scripts, and Ansible playbooks are the three options we have here.  Common tasks and items should have templates, scripts, or Ansible playbooks written which can be re-used by lesson creators.  For instance, a Python module for specifying a list of files to copy to the image.  Another example would be boiler-plate JSON or XML config for napalm based configurations.  Another example would be parameterized configuration of network interfaces.


Helper functions in the configuration process for doing things better in Python


Work Items:

  * Determine what common tasks or items could be modularized for re-use across the three configuration methods.
  * Write re-usable code for these things.

