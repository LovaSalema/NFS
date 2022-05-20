<div id="top"></div>

<br />
<div align="center">

  <h3 align="center">NFS</h3>

  <p align="center">
    (Network File Share)
    <br />
  </p>
</div>



<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ul>
    <li>
        <a href="#What-is-NFS">What is NFS</a>
    </li>
    <li>
      <a href="#Installing-and-configuring-NFS-Server">Installing and configuring NFS Server </a>
    </li>
    <li><a href="#Creating-Your-Own-Website">Creating Your Own Website</a></li>
    <li><a href="#Install-the-NFS-Client-on-the-Client-Systems">Install the NFS Client on the Client Systems</a></li>
  </ul>
</details>



<!-- ABOUT THE PROJECT 


[![Product Name Screen Shot][product-screenshot]](https://example.com)-->
## What is NFS
NFS (Network File Share) is a protocol that allows you to share directories and files with other Linux clients in a network. The directory to be shared is usually created on the NFS server and files added to it.

The client systems mount the directory residing on the NFS server, which grants them access to the files created. NFS comes in handy when you need to share common data among client systems especially when they are running out of space.

This guide will comprise 2 main sections: Installing and configuring NFS Server on Ubuntu 18.04/20.04 and Installing the NFS client on the client Linux system.

## Installing and configuring NFS Server 
To install and configure the NFS server, follow the steps outlined below.


#### Step 1: Install NFS Kernel Server in Ubuntu
The first step is to install the `nfs-kernel-server` package on the server. But before we do this, let’s first update the system packages using the following apt command.
```sh
$ sudo apt update
```

Once the update is complete, proceed and install the nfs-kernel-server package as shown below. This will store additional packages such as nfs-common and rpcbind which are equally crucial to the setup of the file share.

```sh
$ sudo apt install nfs-kernel-server
```

#### Step 2: Create an NFS Export Directory

The second step will be creating a directory that will be shared among client systems. This is also referred to as the export directory and it’s in this directory that we shall later create files that will be accessible by client systems.

Run the command below by specifying the NFS mount directory name.

```sh
$ sudo mkdir -p /mnt/nfs_share
```

Since we want all the client machines to access the shared directory, remove any restrictions in the directory permissions.

```sh
$ sudo chown -R nobody:nogroup /mnt/nfs_share/
```
You can also tweak the file permissions to your preference. Here’s we have given the read, write and execute privileges to all the contents inside the directory.

```sh
$ sudo chmod 777 /mnt/nfs_share/
```

#### Step 3: Grant NFS Share Access to Client Systems
Permissions for accessing the NFS server are defined in the /etc/exports file. So open the file using your favorite text editor:

```sh
$ sudo vim /etc/exports
```

You can provide access to a single client, multiple clients, or specify an entire subnet.

In this guide, we have allowed an entire subnet to have access to the NFS share.


`/mnt/nfs_share  192.168.43.0/24(rw,sync,no_subtree_check)`
Explanation about the options used in the above command.

`rw`: Stands for Read/Write.
`sync`: Requires changes to be written to the disk before they are applied.
`No_subtree_check`: Eliminates subtree checking.
![NFS set access](https://www.tecmint.com/wp-content/uploads/2020/03/Set-NFS-Share-Access.png)


To grant access to a single client, use the syntax:

`/mnt/nfs_share  client_IP_1 (re,sync,no_subtree_check)`
For multiple clients, specify each client on a separate file:
```sh
/mnt/nfs_share  client_IP_1 (re,sync,no_subtree_check)
/mnt/nfs_share  client_IP_2 (re,sync,no_subtree_check)
```

#### Step 4: Export the NFS Share Directory
After granting access to the preferred client systems, export the NFS share directory and restart the NFS kernel server for the changes to come into effect.

```sh
$ sudo exportfs -a
$ sudo systemctl restart nfs-kernel-server
```
![export NFS share directory](https://www.tecmint.com/wp-content/uploads/2020/03/Export-NFS-Share-Directory.png)

#### Step 5: Allow NFS Access through the Firewall
For the client to access the NFS share, you need to allow access through the firewall otherwise, accessing and mounting the shared directory will be impossible. To achieve this run the command:

```sh
$ sudo ufw allow from 192.168.43.0/24 to any port nfs
```

Reload or enable the firewall (if it was turned off) and check the status of the firewall. Port 2049, which is the default file share, should be opened.

```sh
$ sudo ufw enable
$ sudo ufw status
```
![Open Nfs port on firewile](https://www.tecmint.com/wp-content/uploads/2020/03/Open-NFS-Port-on-Firewall.png)

## Install the NFS Client on the Client Systems
We’re done installing and configuring the NFS service on the Server, let’s now install NFS on the client system.

#### Step 1: Install the NFS-Common Package
As is the norm, begin by updating the system packages and repositories before anything else.
```sh
$ sudo apt update
```

Next, install nfs-common packages as shown.
```sh
$ sudo apt install nfs-common
```

![install NFS on client system](https://www.tecmint.com/wp-content/uploads/2020/03/Install-NFS-on-Client-System.png)

#### Step 2: Create an NFS Mount Point on Client
Next, you need to create a mount point on which you will mount the nfs share from the NFS server. To do this, run the command:

```sh
$ sudo mkdir -p /mnt/nfs_clientshare
```


#### Step 3: Mount NFS Share on Client System

The last step remaining is mounting the NFS share that is shared by the NFS server. This will enable the client system to access the shared directory.

Let’s check the NFS Server’s IP address using the ifconfig command.

```sh
$ ifconfig
```
![IP address](https://www.tecmint.com/wp-content/uploads/2020/03/Check-Ubuntu-Server-IP-Address.png)

To achieve this run the command:
```sh
$ sudo mount 192.168.43.234:/mnt/nfs_share  /mnt/nfs_clientshare
```

#### Step 4: Testing the NFS Share on Client System

To verify that our NFS setup is working, we are going to create a few files in the NFS share directory located in the server.

```sh
$ cd /mnt/nfs_share/
$ touch file1.txt file2.txt file3.txt
```

Now head back to the NFS client system and check if the files exist.

```sh
$ ls -l /mnt/nfs_clientshare/
```

![test NFS share on client](https://www.tecmint.com/wp-content/uploads/2020/03/Test-NFS-Share-on-Client.png)



Great! The output confirms that we can access the files we just created on the NFS server!

<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[contributors-shield]: https://img.shields.io/github/contributors/othneildrew/Best-README-Template.svg?style=for-the-badge
[contributors-url]: https://github.com/othneildrew/Best-README-Template/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/othneildrew/Best-README-Template.svg?style=for-the-badge
[forks-url]: https://github.com/othneildrew/Best-README-Template/network/members
[stars-shield]: https://img.shields.io/github/stars/othneildrew/Best-README-Template.svg?style=for-the-badge
[stars-url]: https://github.com/othneildrew/Best-README-Template/stargazers
[issues-shield]: https://img.shields.io/github/issues/othneildrew/Best-README-Template.svg?style=for-the-badge
[issues-url]: https://github.com/othneildrew/Best-README-Template/issues
[license-shield]: https://img.shields.io/github/license/othneildrew/Best-README-Template.svg?style=for-the-badge
[license-url]: https://github.com/othneildrew/Best-README-Template/blob/master/LICENSE.txt
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://linkedin.com/in/othneildrew
[product-screenshot]: images/screenshot.png
