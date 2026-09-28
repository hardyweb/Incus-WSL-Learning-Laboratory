# Chapter 12 — Incus Snapshots and Recovery

## What is a Snapshot?

A **snapshot** is a saved state of a container or VM. It captures the entire filesystem and memory at a specific point in time.

### Why Snapshots Matter in Education

> Students can experiment without being afraid of permanently breaking their laboratory.

If you make a mistake, you restore the snapshot and start over.

## Create a Snapshot

[WSL Debian]
```bash
incus snapshot create debian01 clean
```

### What Happened

```
Snapshot debian01 created.
```

You can also add a description:
[WSL Debian]
```bash
incus snapshot create debian01 clean --description "Before installing nginx"
```

## List Snapshots

[WSL Debian]
```bash
incus snapshot list debian01
```

Expected output:
```
+------------+--------+----------------------------+
|    NAME    |   DATE |           DESCRIPTION      |
+------------+--------+----------------------------+
| clean      | ...    | Before installing nginx    |
+------------+--------+----------------------------+
```

## Restore a Snapshot

[WSL Debian]
```bash
incus snapshot restore debian01 clean
```

This restores the container to the state it was in when the snapshot was taken.

### After Restoring

The container returns to the exact state it was in when the snapshot was created.

### What About Changes After the Snapshot?

Any changes made after the snapshot was created will be **lost**.

## Delete a Snapshot

[WSL Debian]
```bash
incus snapshot delete debian01 clean
```

## Recommended Snapshot Workflow

### Scenario 1: Before Major Changes

1. Create snapshot before experimenting
   [WSL Debian]
   ```bash
   incus snapshot create debian01 before-upgrade
   ```

2. Make your changes (install software, edit configs, etc.)

3. If something goes wrong, restore
   [WSL Debian]
   ```bash
   incus snapshot restore debian01 before-upgrade
   ```

4. If successful, you can delete the old snapshots
   [WSL Debian]
   ```bash
   incus snapshot delete debian01 before-upgrade
   ```

### Scenario 2: Milestone Snapshots

Create snapshots at important stages:
```bash
incus snapshot create debian01 install-base
incus snapshot create debian01 install-nginx
incus snapshot create debian01 install-node
incus snapshot create debian01 working-v1
incus snapshot create debian01 working-v2
```

## Exercise: Snapshot Practice

1. [WSL Debian] `incus launch images:debian/13 snapshot-practice`
2. [WSL Debian] `incus exec snapshot-practice -- bash`
3. [Incus snapshot-practice] `echo 'my data' > /tmp/important.txt`
4. [WSL Debian] `incus snapshot create snapshot-practice before-change`
5. [Incus snapshot-practice] `echo 'more data' >> /tmp/important.txt`
6. [WSL Debian] `incus snapshot list snapshot-practice`
7. [WSL Debian] `incus exec snapshot-practice -- cat /tmp/important.txt` (should show the new data)
8. [WSL Debian] `incus snapshot restore snapshot-practice before-change`
9. [Incus snapshot-practice] `cat /tmp/important.txt` (should only show original data)
10. [WSL Debian] `incus snapshot delete snapshot-practice before-change`
11. [WSL Debian] `incus delete --force snapshot-practice`

## Advanced: Snapshots and State

Snapshots save:
- Filesystem state (all files)
- Container state (running/stopped)
- Process state (if running)

You can snapshot a **running** container, and the snapshot will include the running state.

### Restore and Run Again

After restoring a running snapshot, the container will return to the state it was in when the snapshot was created.

## What Snapshots DON'T Protect Against

- **Configuration changes** that are meant to persist after restore
- **Data changes** after the snapshot was taken
- **Accidental deletion** (restore still works, but you need to know the snapshot name)

## Snapshots vs Backups

| Feature | Snapshot | Backup |
|---------|----------|--------|
| What it saves | State of instance at a point in time | Copy of filesystem data |
| Storage location | Same storage pool | Can be external |
| Speed | Instant | Depends on data size |
| Use case | Recover from mistakes | Recover from disaster |
| Automatic | Manual | Can be scheduled |
| Includes memory state | Yes (if running) | No |

### Create a Backup

[WSL Debian]
```bash
incus export debian01 debian-backup.tar.gz
```

> ⚠️ **Note:** Backups can be large. Snapshots are lighter.

## Next Step

Continue to [Chapter 13 — Student Projects](13-student-projects.md) for hands-on projects.

## Tested With

> **Tested with:**
> - Debian 13 containers
> - Incus 6.x
> - Snapshots created and restored successfully in test environments
>
> Verify snapshot support with `incus snapshot list` after your first creation.