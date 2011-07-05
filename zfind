#!/usr/bin/env python

''' Recursively search for files contained in jar/zip files.

    This isn't for searching _inside_ the files, like zgrep, but
    for matching the file names themselves. For instance:

    $ zfind org.apache.http.client.params.HttpClientParams ~/.m2

    will search Maven's local cache for any jars containing the
    HttpClientParams class. It turns out the dots in the package name
    conveniently match slashes in the java class's name:

    /home/dnorth/.m2/repository/org/apache/httpcomponents/httpclient/4.0.1/httpclient-4.0.1.jar: org/apache/http/client/params/HttpClientParams.class

    Ah, there it is! '''

__AUTHOR__ = 'Dan North <dan@dannorth.net>'

import os, sys
from contextlib import closing
import re
import zipfile

class Usage(Exception):
  def __init__(self, msg):
    self.msg = msg

def scan_file(pattern, path):
  ''' Search a zip file's contents for matching file names '''
  if zipfile.is_zipfile(path):
    with closing(zipfile.ZipFile(path)) as zf:
      for name in zf.namelist():
        if re.search(pattern, name):
          print "%s: %s" % (path, name)

def scan_zip_files(pattern, path):
  ''' Use os.walk to recurse down a directory hierarchy looking for zip files '''
  if os.path.isfile(path):
    scan_file(pattern, path)
  else:
    for here, dirs, files in os.walk(path):
      for f in (os.path.join(here, f) for f in files):
        scan_file(pattern, f)

def main(argv=None):
  if (argv is None):
    argv = sys.argv

  try:
    if len(argv) < 3:
      raise Usage("Usage: %s pattern path..." % argv[0])

    pattern = argv[1]
    scanned, total = 0, 0
    for path in argv[2:]:
      scan_zip_files(pattern, os.path.expanduser(path))

    return 0

  except Usage, err:
    print >>sys.stderr, err.msg
    return 1

if __name__ == '__main__':
  sys.exit(main())

