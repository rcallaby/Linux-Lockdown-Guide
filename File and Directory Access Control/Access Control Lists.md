# Access Control Lists

### Step 1: Verify ACL Support
Ensure that your filesystem supports ACLs. Most modern filesystems like ext3, ext4, and XFS support ACLs. You can check if ACL support is enabled with the `tune2fs` command:

```bash
sudo tune2fs -l /dev/sda1 | grep "Default mount options:"
```

You should see `acl` listed in the output. If not, you may need to remount the filesystem with ACL support.

### Step 2: Install ACL Utilities
Ensure that ACL utilities are installed on your system. On Debian-based systems, you can install them with:

```bash
sudo apt-get install acl
```

On Red Hat-based systems:

```bash
sudo yum install acl
```

### Step 3: Enable ACLs on the Filesystem
If ACLs are not enabled by default, you can enable them by remounting the filesystem. For example:

```bash
sudo mount -o remount,acl /dev/sda1
```

To make this change permanent, add the `acl` option to the `/etc/fstab` file:

```bash
/dev/sda1 / ext4 defaults,acl 0 1
```

### Step 4: Check ACLs on a File or Directory
To view the ACLs on a file or directory, use the `getfacl` command:

```bash
getfacl filename
```

### Step 5: Set ACLs on a File or Directory
To set an ACL on a file or directory, use the `setfacl` command. The general syntax is:

```bash
setfacl -m u:username:permissions filename
```

For example, to give the user `john` read and write permissions on a file named `example.txt`:

```bash
setfacl -m u:john:rw example.txt
```

### Step 6: Set Default ACLs on a Directory
Default ACLs are inherited by all files and subdirectories created within a directory. To set a default ACL, use the `-d` option:

```bash
setfacl -d -m u:john:rw dirname
```

### Step 7: Remove ACLs
To remove an ACL entry, use the `-x` option with `setfacl`:

```bash
setfacl -x u:john filename
```

To remove all ACLs from a file or directory, use the `-b` option:

```bash
setfacl -b filename
```

### Step 8: Check Effective Rights Mask
The effective rights mask limits the maximum allowable permissions for users and groups. You can modify it with:

```bash
setfacl -m m::rwx filename
```

### Step 9: Recursively Apply ACLs
To apply ACLs recursively to all files and directories within a directory, use the `-R` option:

```bash
setfacl -R -m u:john:rw dirname
```

### Example Workflow

1. **Check existing ACLs on `example.txt`**:
    ```bash
    getfacl example.txt
    ```

2. **Grant read and write permissions to user `john` on `example.txt`**:
    ```bash
    setfacl -m u:john:rw example.txt
    ```

3. **Set default ACLs on `mydir` for user `john`**:
    ```bash
    setfacl -d -m u:john:rwx mydir
    ```

4. **Remove ACL for user `john` on `example.txt`**:
    ```bash
    setfacl -x u:john example.txt
    ```

5. **Remove all ACLs from `example.txt`**:
    ```bash
    setfacl -b example.txt
    ```

### Step 10: Verify ACL Changes
Always verify your changes to ensure they have been applied correctly:

```bash
getfacl example.txt
getfacl mydir
```

