# Restic Backup
Restic is a snapshot-based, git-like, encrypted backup solution.  You can use Restic to define repos (vaults, essentially) which can contain broad or specific backups of files/folders.  It is an astonishingly effective tool and in combination with resticprofile, a helper utility which lets you define backup configurations as YAML, the system is simple to configure to boot.

My current backups are on an External Drive in a repo labeled "ResticBackup".

## TODO:
_I need to rewrite this article. I'm no longer using Windows as my daily driver, and I've switched from using resticprofile to autorestic._

## Pre-requisites:
- Scoop (Windows only)
- Restic
- Restic Profile

_TODO: See if AutoRestic is a better tool than Restic Profile_

Windows Only: Manually add `resticprofile.exe` to path.
Windows Only: Installation is living at `D:\Program Files\resticprofile`

Backups are defined in `profiles.yaml`


## Restic Documentation

<https://restic.readthedocs.io/en/latest/manual_rest.html>


## ResticProfile

### Update ResticProfile:
```
$ resticprofile self-update
```

### List Available Options
```
$ resticprofile profiles
    Profiles available:
      books:       (backup, snapshots)
      default:     (backup)
      development: (backup, snapshots)
      music:       (backup, snapshots)
      pictures:    (backup, snapshots)
    Groups available:
      full-backup:  music, pictures, development, books
```

### Run a Backup

Run a full backup, using the group:

```
$ resticprofile --name "full-backup" backup
```

Alternatively, run for a single item:

```
$ resticprofile --name "books" backup
```

### Inspecting a Backup

List snapshots:

```
$ restic -r [backup_path] snapshots
    enter password for repository:
    ID       Date                Host  Tags      Directory
    --------------------------------------------------
    eeeeeeee 1969-01-01 01:00:00 REVAN documents /documents
    ffffffff 1969-01-01 01:00:00 REVAN music     /music
```

### Restoring Backup

From a snapshot:

```
$ restic -r [backup_path] restore [backup_id OR latest] --target [path_to_restore_to]
```

Restore specific path:

```
$ restic -r [backup_path] restore [backup_id OR latest] --path "[path_to_restore]" --target [path_to_restore_to]
```

### Removing Snapshots

_TODO: add policy rules to resticprofile profiles.yaml to avoid manually managing snapshot history_

- See Inspecting Backup to get snapshot IDs.
- Forget the snapshot with:

```
$ restic -r [backup_path] forget [snapshot_id]
```
- To clean up unreferenced data:

```
$ restic -r [backup_path] prune
```

- OR use an all-in-one and drop all but latest snapshot:

```
$ restic -r [backup_path] forget --keep-last 1 --prune
```
