# Installation Instructions CMaNGOS
*How to install & run C(ontinued)-MaNGOS from source code*
- [x] server core: cmangos/mangos-classic
- [x] server data: cmangos/classic-db
- [x] server host: debian 10 (buster)
- [x] goal: Doing WoW-Emulation Right!

The following variables are used in this document:
- `db-new-password` - You new MariaDB password for new user with root privileges

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

#### 2.1 Install help tools and some libraries
```bash
sudo apt update
sudo apt install grep build-essential gcc g++ automake git-core autoconf make patch cmake default-libmysqlclient-dev libtool libssl-dev binutils zlibc libc6 libbz2-dev subversion libboost-all-dev
```
#### 2.2 Install and Configure MariaDB <a name="install_mariadb"></a>
1. Installing MariaDB
- version 10.3 from standart debian buster repo
	```bash
	sudo apt install mariadb-server
	```
- or version 10.4 stable from mariadb repo
	```bash
	sudo apt-get install software-properties-common dirmngr
	sudo apt-key adv --recv-keys --keyserver keyserver.ubuntu.com 0xF1656F24C74CD1D8
	sudo add-apt-repository 'deb [arch=amd64] http://mariadb.mirror.serveriai.lt/repo/10.4/debian buster main'
	```
	```bash
	sudo apt update
	sudo apt install mariadb-server
	```
	:grey_exclamation: Check all available versions and mirrors on the official MariaDB Foundation [page](https://downloads.mariadb.org/mariadb/repositories/#distro=Debian&distro_release=buster--buster)

2. Run this script to increase the security of your MariaDB installation
	```bash
	sudo mysql_secure_installation
	```
	The script will prompt you to determine which actions to perform
	
	> Enter current password for root (enter for none): `ENTER`
	
	> Switch to unix_socket authentication [Y/n] `Y`
	
	:exclamation: The script will ask you about the authentication method only if you installed the latest version from MariaDB repositories
	
	> Set root password? [Y/n] `N`
	
	:exclamation: In Debian, the root account for MariaDB is tied closely to automated system maintenance, so we should not change the configured authentication methods for that account.
	
	> Remove anonymous users? [Y/n] `Y`
	
	> Disallow root login remotely? [Y/n] `Y`
	
	> Remove test database and access to it? [Y/n] `Y`
	
	> Reload privilege tables now? [Y/n] `Y`
	
3. Optionally set up an additional administrative account `dbadmin` with password `db-new-password` for password access (e.g. for remote full access to the database)
	```bash
	sudo mysql
	```
	```mysql
	MariaDB [(none)]> GRANT ALL ON *.* TO 'dbadmin'@'localhost' IDENTIFIED BY 'db-new-password' WITH GRANT OPTION;
	```
	```mysql
	MariaDB [(none)]> FLUSH PRIVILEGES;
	```
	```mysql
	MariaDB [(none)]> exit
	```

## 3. Get the remote data to your system

- Create a new user to run your mangos server under
	```bash
	sudo adduser mangos
	```
	```bash
	su mangos
	```
	```bash
	mkdir /home/mangos/source
	cd /home/mangos/source
	```
- clone mangos-classic
	```bash
	git clone git://github.com/cmangos/mangos-classic.git
	```
- Clone classic-DB
	```bash
	git clone git://github.com/cmangos/classic-db.git
	```
