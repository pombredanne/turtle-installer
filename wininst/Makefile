MAKENSIS="C:/Program Files/NSIS/makensis"

all: py26

py24: MAKE_PYTHONVERSION=2.4
py24: MAKE_PYTHONDIR=Python24
py24: create-setup-from-export

py25: MAKE_PYTHONVERSION=2.5
py25: MAKE_PYTHONDIR=Python25
py25: create-setup-from-export

py26: MAKE_PYTHONVERSION=2.6
py26: MAKE_PYTHONDIR=Python26
py26: create-setup-from-export

py27: MAKE_PYTHONVERSION=2.7
py27: MAKE_PYTHONDIR=Python27
py27: create-setup-from-export

clean:
	- rm -rf ../downloads
	- rm -rf ../eggs
	- rm -rf ../bin
	mkdir ../downloads
	mkdir ../downloads/dist
	mkdir ../eggs
	mkdir ../bin

export-tag:
	rm -rf $(TEMP)/turtle-rural-export
	svn export https://office.nelen-schuurmans.nl/svn/Products/Turtle/turtle-rural/tags/$(TAGGED_VERSION) $(TEMP)/turtle-rural-export
	
create-setup-from-export: export-tag
	cd $(TEMP)/turtle-rural-export/wininst; make clean create-setup MAKE_PYTHONVERSION=$(MAKE_PYTHONVERSION) MAKE_PYTHONDIR=$(MAKE_PYTHONDIR)
	
create-setup: clean
	cp turtle-rural.nsi autogen-turtle-rural.nsi
	# If setuptools is installed as a site package, which is
	# required by zest.releaser, the execution of buildout in this
	# Makefile will not download it. However, the execution of
	# buildout in the installer requires it so we have to make
	# sure it is in the right place.
	#
	# Ideally, the buildout option 'include-site-packages=false'
	# lets buildout ignore site packages. Unfortunately, some
	# recipes ignore this option and a site packages may still
	# end up being used.
	cp setuptools*.egg ../eggs
	# we escape the dollar sign in the following sed regular expressions,
	# see the '$$', otherwise make would try to expand them
	sed -i 's/\(StrCpy $$PYTHONVERSION[ ][ ]*\).*/\1"$(MAKE_PYTHONVERSION)"/' autogen-turtle-rural.nsi
	sed -i 's/\(StrCpy $$PYTHONDIR[ ][ ]*\).*/\1"C:\\$(MAKE_PYTHONDIR)"/' autogen-turtle-rural.nsi
	sed -i 's/\(zc.buildout-[0-9]*[.][0-9]*[.][0-9]*-py\)[0-9]*[.][0-9]*/\1$(MAKE_PYTHONVERSION)/' autogen-turtle-rural.nsi
	# we use a forward slash as a directory separator in the shell commands
	# as the shell used by make does not like the backslash (or double
	# backslash) as one
	cd ..; c:/$(MAKE_PYTHONDIR)/python bootstrap.py
	cd ..; bin/buildout buildout:download-cache=downloads buildout:include-site-packages=false
	$(MAKENSIS) autogen-turtle-rural.nsi
	mv turtle-rural-setup.exe turtle-rural-setup-python$(MAKE_PYTHONVERSION).exe
