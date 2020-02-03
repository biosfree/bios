## Debian 10 CMaNGOS Installation Instructions
*How to install & run C(ontinued)-MaNGOS from source code*
- [x] server core: cmangos/mangos-classic
- [x] server data: cmangos/classic-db
- [x] server host: debian 10 (buster)
- [x] goal: Doing WoW-Emulation Right!

The following variables are used in this document:
- `db-new-password` - You new MariaDB password for new user with root privileges

If you are an absolute beginner to programming and compiling then you may want to go through the [Beginner's Guide](https://github.com/cmangos/issues/wiki/Beginners-Guide-Home) first.

### 1. Links
- CMaNGOS: [site](https://cmangos.net) | [forum](https://forum.cmangos.net) | [git repositories](https://github.com/cmangos) | [discord](https://discord.gg/Dgzerzb)
- Classic-DB: [forum](https://github.com/cmangos/classic-db/issues) | [git repository](https://github.com/cmangos/classic-db)
- Author of this guide: @biosfree

### 2. Software requirements
- Debian 10 [Installed](https://github.com/biosfree/cmangos-help) and [Configured](https://github.com/biosfree/cmangos-help)
- Client World of Warcraft Classic (vanilla version 1.12.x)
- MariaDB (community-developed fork of the MySQL) database management system for server. This guide will also help you [install and configure MariaDB](#install_mariadb).
- Some tools: Git, GCC, CMake, Boost and a few libraries (see below)

#### 2.1 Install help tools and some libraries
```bash
sudo apt update
sudo apt install build-essential automake git cmake default-libmysqlclient-dev libtool libssl-dev zlibc libbz2-dev subversion libboost-all-dev
```
or full list without dependencies:
```bash
sudo apt install grep build-essential gcc g++ automake git autoconf make patch cmake default-libmysqlclient-dev libtool libssl-dev binutils zlibc libc6 libbz2-dev subversion libboost-all-dev
```

#### 2.2 Install and Configure MariaDB <a name="install_mariadb"></a>
1. Installing MariaDB version 10.3 from standart debian buster repo
```bash
sudo apt install mariadb-server
```
   * or MariaDB version 10.4 stable from mariadb repo
   ```bash
   sudo apt-get install software-properties-common dirmngr
   sudo apt-key adv --recv-keys --keyserver keyserver.ubuntu.com 0xF1656F24C74CD1D8
   sudo add-apt-repository 'deb [arch=amd64] http://mariadb.mirror.serveriai.lt/repo/10.4/debian buster main'
   ```
   ```bash
   sudo apt update
   sudo apt upgrade
   sudo apt install mariadb-server
   ```
   :grey_exclamation: Check all available versions and mirrors on the official MariaDB Foundation [page](https://downloads.mariadb.org/mariadb/repositories/#distro=Debian&distro_release=buster--buster)

2. Run this script to increase the security of your MariaDB installation
```bash
sudo mysql_secure_installation
```
   * The script will prompt you to determine which actions to perform
   
     > Enter current password for root (enter for none): `ENTER`
     
     > Switch to unix_socket authentication [Y/n] `N`
     
     :exclamation: This request become available only if you installed the latest version from MariaDB repositories
     
     :exclamation: You already have your root account protected with unix_socket authentication, so you can safely answer 'n'
     
     > Set root password? [Y/n] `N`
     
     :exclamation: In Debian, the root account for MariaDB is tied closely to automated system maintenance, so we should not change the configured authentication methods for that account.
     
     :exclamation: You already have your root account protected with unix_socket authentication, so you can safely answer 'n'
     
     > Remove anonymous users? [Y/n] `Y`
     
     > Disallow root login remotely? [Y/n] `Y`
     
     > Remove test database and access to it? [Y/n] `Y`
     
     > Reload privilege tables now? [Y/n] `Y`

3. Optionally set up an additional administrative account `dbadmin` with password `db-new-password` for password access (e.g. for remote full access to the database through SSH-tunnel)
```bash
sudo mysql
```
   * the command prompt line changes to `MariaDB [(none)]>`
   ```mysql
   GRANT ALL ON *.* TO 'dbadmin'@'localhost' IDENTIFIED BY 'db-new-password' WITH GRANT OPTION;
   FLUSH PRIVILEGES;
   EXIT
   ```
   :exclamation: Don't forget to change the `db-new-password` variable to a really complicated password! :speak_no_evil:

### 3. Get the remote data to your system

1. Create a new user to run your mangos server under
```bash
sudo adduser mangos
```
2. Switch to mangos user in a shell
```bash
su mangos
```
3. Create a source dir
```bash
mkdir /home/mangos/source
cd /home/mangos/source
```
4. Git mangos-classic
```bash
git clone git://github.com/cmangos/mangos-classic.git
```
5. Git classic-DB
```bash
git clone git://github.com/cmangos/classic-db.git
```

### 4. Compiling CMaNGOS

1. Create a build dir
```bash
mkdir /home/mangos/source/build
cd /home/mangos/source/build
```
2. Choose build options
   * options that may interest us:
   ```
   -DCMAKE_INSTALL_PREFIX=\  Path where the server should be installed to
   -DPCH=1                   Use precompiled headers (much faster after updates)
   -DDEBUG=0                 Remove debug mode from compiling
   -DBUILD_EXTRACTORS=ON     Build map/dbc/vmap/mmap extractor
   -DBUILD_PLAYERBOT=ON      Build with playerbots mod enabled
   ```
   * example:
   ```
   Example1: command to just compile mangos-classic and install in /home/mangos/wow-vanilla-run dir:
   cmake ../mangos-classic -DCMAKE_INSTALL_PREFIX=\../../wow-vanilla-run -DPCH=1 -DDEBUG=0
   ```
   ```
   Example2: command to compile same as in Example1 + build map extraction tools:
   cmake ../mangos-classic -DCMAKE_INSTALL_PREFIX=\../../wow-vanilla-run -DBUILD_EXTRACTORS=ON -DPCH=1 -DDEBUG=0
   ```
   ```
   Example3: command to compile same as in Example1 + build map extraction tools + playerbots mod
   cmake ../mangos-classic -DCMAKE_INSTALL_PREFIX=\../../wow-vanilla-run -DBUILD_EXTRACTORS=ON -DPCH=1 -DDEBUG=0 -DBUILD_PLAYERBOT=ON
   ```
3. Configure building utility use Example3 settings
```bash
cmake ../mangos-classic -DCMAKE_INSTALL_PREFIX=\../../wow-vanilla-run -DBUILD_EXTRACTORS=ON -DPCH=1 -DDEBUG=0 -DBUILD_PLAYERBOT=ON
```
:grey_exclamation: *(installing in the `/home/mangos/wow-vanilla-run` dir, with map extraction tools and enabled playerbots mod)*

4. Make building CMaNGOS binary files
```bash
make
```
5. Install CMaNGOS binary files
```bash
make install
```

### 5. Extract data files from the client
