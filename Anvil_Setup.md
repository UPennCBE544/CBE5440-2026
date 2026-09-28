## Anvil Setup
- Setting up ACCESS ID : [Login](https://operations.access-ci.org/identity/new-user)
  Please make sure to input all the necessary information regarding your academic status, current country of residence, and citizenship in the Allocation Profile.

  **[Allocation_Profile](https://urldefense.com/v3/__https://allocations.access-ci.org/profile__;!!IBzWLUs!Vlp9e2XoIjuB1njU36I0YVdpd5gw6pRBcZaGLQA7WkH9ZVyJXT3rO7Mo8BAbKX4F6_wSt8cdetI6xPZmkBW9oO7_$)
- **Anvil User Guide**: [link](https://www.rcac.purdue.edu/knowledge/anvil)

### Logging In
General login information: [https://www.rcac.purdue.edu/knowledge/anvil/access/login](https://www.rcac.purdue.edu/knowledge/anvil/access/login)

Start by generating an [SSH key](https://docs.rcac.purdue.edu/userguides/anvil/getting-started/#ssh) by following the steps provided.

## Installations
### Installing a display server:
#### Mac OSX
Download and install:

* [XQuartz](http://www.xquartz.org/)

#### Windows

Download and install:
* [Xming](http://sourceforge.net/projects/xming/) (Note: disable automatic installation of PuTTY with Xming. The above installer is a newer version)

Launch Xming. You will always need to have this open in order to forward graphical windows from the external clusters.

### Setting Up Bashrc:
After logging in, please run these commands in your home directory
cp /home/x-shan4/.bashrc ./
source .bashrc

### Things you can do on your local environment
You can install these on your local environment if you want to try things out locally without using the cluster.

### Installing Anaconda:
1. Go to this link: https://www.anaconda.com/download/success#downloads
2. Download the installer
3. Open the pkg and install

### Installing ASE:
In terminal: conda install conda-forge::ase

In terminal: conda install tk

Confirm ASE works by typing ase gui

### Installing JupyterNotebook:
In terminal: pip install notebook

In terminal: jupyter notebook

<a name='logging'></a>

