# Verifying File Hashes
File hashes are unique, reproducible string sequences that identify the integrity another file.  They are usually provided alongside download links so that a user can compare their downloaded file's hash with the source hash.  This safeguards against supply chain attacks and data corruption that can occur during a download.

## Linux

Use the built-in `md5sum` or `sha256sum` commands:

```
$ md5sum /path/to/file

OR

$ sha256sum /path/to/file
```

## Windows

Use the built-in `CertUtil` command:

```
Path:\To\File> CertUtil -hashfile <filename> <hashtype>
```

Example:
```
CertUtil -hashfile pop_os.iso sha256
```
