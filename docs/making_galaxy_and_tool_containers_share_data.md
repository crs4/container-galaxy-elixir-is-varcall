

# Recipe for getting Galaxy to work with other users' container images

## Problem

Files and directories created by galaxy on shared file system are:
  1. owned by galaxy user 1450
  2. have permissions 600 or 700, respectively
This makes them inaccessible to containers running as other users (e.g.,
biocontainers user with id 1000)


## This solution

1) Make galaxy create group-accessible files and directories by setting a more
permissive umask of 002 (results in permissions 660 or 770)

2) Set setgid bit on galaxy's database/files directory (which has galaxy's gid
1450), so that new files and directories created under that directory will
inherit galaxy's group id 1450.

3) Set the k8s runner to run containers with supplementary group 1450.

4) While at the moment it doesn't seem strictly necessary, explicitly setting the k8s
runner to run containers with umask 002 would make the solution more robust.
However, I'm not sure whether it's possible (in the sense that I didn't see a
way to tell k8s to set a specific umask on a container being launched.


This setup should result in:
* containers may access and modify galaxy's files and directory because they are
  in the galaxy group;
* new files and directories created by the tools are owned by the tools, but
  1) they have galaxy's gid (thanks to the setgid bit) and they are
  group-accessible (thanks to the umask of 002)


## Implementation


1) Modify /etc/supervisor/conf.d/galaxy.conf to run galaxy processes with umask 002

2) Set g+rwxS on galaxy's database/files directory.  Not sure where to change
code to implement this.  At the moment I'm doing it manually.

3) On deployment, using the galaxy-stable chart ver. 2.0.2, configure the
runner to   set

    job_conf:
      runners:
        enable_k8: "true"
        k8s_fs_group_id: "1450"
        k8s_supplemental_group_id: "1450"

It would be better to path the kubernetes runner to do this automatically.

