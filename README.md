# Ansible role for RedHat GlusterFS

Role deploy [GlusterFS](https://github.com/mrlesmithjr/ansible-glusterfs-client)

## Variables

```yaml
---
glusterfs_version: 8

# Defines Ansible group which contains GlusterFS servers
glusterfs_server_group: glusterfs

# Defines if GlusterFS should be configured
glusterfs_config: false

# Defines bricks and volumes to create
glusterfs_bricks:
#  - name: shareddata
#    bricks:
#      - "{{ glusterfs_bricks_dir+'/'+'shareddata/brick' }}"

# Defines the mountpoint for gluster bricks and volumes to be created
glusterfs_bricks_dir: /export

# Defines list of cluster nodes
glusterfs_cluster_nodes: |
  {% set cluster = [] %}
  {% for host in groups[glusterfs_server_group] %}
  {%   if hostvars[host]['glusterfs_repl_int_address'] is defined %}
  {%     set _ = cluster.append(hostvars[host]['glusterfs_repl_int_address']) %}
  {%   endif %}
  {% endfor %}
  {{ cluster }}

glusterfs_debian_repo_key: "{{ glusterfs_debian_repo_url+'/rsa.pub' }}"
glusterfs_debian_repo_url: "{{ 'https://download.gluster.org/pub/gluster/glusterfs/'+glusterfs_version|string }}"
glusterfs_debian_repo: "{{ 'deb '+glusterfs_debian_repo_url+'/LATEST/Debian/'+ansible_distribution_release|lower+'/amd64/apt '+ansible_distribution_release|lower+' main' }}"

# Defines the node to be considered as GlusterFS primary
glusterfs_primary: "{{ groups[glusterfs_server_group][0] }}"
```

## Dependencies

None

## Examples

Simple glusterfs node without clustering

```yaml
glusterfs:
  vars:
    glusterfs_version: 8
  hosts:
    gluster01:
      ansible_host: 192.168.1.200
```

GlusterFS clustering example

```yaml
glusterfs:
  vars:
    glusterfs_version: 8
    glusterfs_server_group: glusterfs
    glusterfs_bricks_dir: /data
    glusterfs_config: true
  hosts:
    gluster01:
      ansible_host: 192.168.1.200
    gluster02:
      ansible_host: 192.168.1.200
    gluster03:
      ansible_host: 192.168.1.200
```

Playbook example
```yaml
- hosts: glusterfs
  role:
    - role: glusterfs
```

## License

MIT
