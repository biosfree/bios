# Installation Instructions CMaNGOS
*How to install & run C(ontinued)-MaNGOS from source code*
- [x] server core: cmangos/mangos-classic
- [x] server data: cmangos/classic-db
- [x] server host: debian 10 (buster)
- [x] goal: Doing WoW-Emulation Right!

The following variables are used in this document:
- *%new-mariadb-root-password%* - You new MariaDB root password

If you are an absolute beginner to programming and compiling then you may want to go through the [Beginner's Guide](https://github.com/cmangos/issues/wiki/Beginners-Guide-Home) first.

## 1. Links
- CMaNGOS: [site](https://cmangos.net) | [forum](https://forum.cmangos.net) | [git repositories](https://github.com/cmangos) | [discord](https://discord.gg/Dgzerzb)
- Classic-DB: [forum](https://github.com/cmangos/classic-db/issues) | [git repository](https://github.com/cmangos/classic-db)
- Author of this guide: @biosfree

## 2. Software requirements
- Debian 10 [Installed](https://github.com/biosfree/cmangos-help) and [Configured](https://github.com/biosfree/cmangos-help)
- Client World of Warcraft Classic (vanilla version 1.12.x)
- MariaDB (community-developed fork of the MySQL) database management system for server. This guide will also help you [install and configure MariaDB](#install_mariadb).
- Some tools: Git, GCC, CMake, Boost and a few libraries (see below)

##### 2.1 Install help tools and some libraries
```sh
sudo apt update
sudo apt install grep build-essential gcc g++ automake git-core autoconf make patch cmake default-libmysqlclient-dev libtool libssl-dev binutils zlibc libc6 libbz2-dev subversion libboost-all-dev
```
##### 2.2 Install and Configure MariaDB <a name="install_mariadb"></a>
- Install MariaDB
	```sh
	sudo apt install mariadb-server
	```
- Config MariaDB
	
	Run this script to increase the security of your MariaDB installation
	```sh
	sudo mysql_secure_installation
	```
	- The script will prompt you to determine which actions to perform
		> Enter current password for root (enter for none): *enter*
		
		> Set root password? [Y/n] *yes*
		
		> New password: *%new-mariadb-root-password%*
		
		> Re-enter new password: *%new-mariadb-root-password%*
		
		> Remove anonymous users? [Y/n] *yes*
		
		> Disallow root login remotely? [Y/n] *yes*
		
		> Remove test database and access to it? [Y/n] *yes*
		
		> Reload privilege tables now? [Y/n] *yes*

## 3. Get the remote data to your system

- Create a new user to run your mangos server under
	```sh
	useradd -m -d /home/mangos -c "MaNGOS" -U mangos
	```
- clone mangos-classic
	```sh
	git clone git://github.com/cmangos/mangos-classic.git
	```
- Clone classic-DB
	```sh
	git clone git://github.com/cmangos/classic-db.git
	```
