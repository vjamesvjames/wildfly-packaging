# 0. prepare orig source

# Download the "source" (the binary "Java EE7 Full & Web Distribution")
wget http://download.jboss.org/wildfly/8.2.0.Final/wildfly-8.2.0.Final.tar.gz

# create a link to this file named wildfly-8.2.0.Final.orig.tar.gz
ln -s wildfly-8.2.0.Final.tar.gz wildfly_8.2.0.Final.orig.tar.gz

# unpack and enter directory
tar -xzf wildfly_8.2.0.Final.orig.tar.gz
cd wildfly-8.2.0.Final

# Lets start with packaging (create debian/ dir and changelog)
mkdir debian
# 1. debian/changelog
dch --create --package wildfly --newversion=8.2.0.Final-1  

# 2. debian/compat
echo "9" > debian/compat

# 3. debian/rules
echo -ne '#!/usr/bin/make -f\n%:\n\tdh $@\n' > debian/rules

# 4. debian/control
(cat  <<ENDE
Source: wildfly
Maintainer: Your Name <your.mail@provider.lan>
Standards-Version: 3.9.5
Build-Depends: debhelper (>=9)

Package: wildfly
Architecture: all
Depends: ${shlibs:Depends}, ${misc:Depends}, openjdk-7-jdk
Description: wildfly application server
 This should be a longerdescription of the wildfly application server package.
ENDE
)  > debian/control

# 5. debian/source/format
mkdir -p debian/source
echo "3.0 (quilt)" > debian/source/format

# 6. Add all dir to install
echo "appclient  opt/jboss/wildfly/
bin  opt/jboss/wildfly/
docs  opt/jboss/wildfly/
domain  opt/jboss/wildfly/
modules  opt/jboss/wildfly/
standalone  opt/jboss/wildfly/
welcome-content  opt/jboss/wildfly/
LICENSE.txt  opt/jboss/wildfly/
README.txt  opt/jboss/wildfly/
copyright.txt  opt/jboss/wildfly/
jboss-modules.jar  opt/jboss/wildfly/" > debian/wildfly.install
 
# Build with dpkg-buildpackage
dpkg-buildpackage -us -uc
