shared haproxy
===============

HTTPS reverse proxy / load balancer to multiple http backend, could be shared by multiple independent playbooks
Limitations :
 - single certificate (you will need wildcard or multiple cnames)
 - each backend need need unique name (risk of overriding files)

 Haproxy config is splitted in multiple files in /etc/haproxy/conf.d/ then all files are assembled in /etc/haproxy/haproxy.conf after syntax check

Role Variables
--------------

Default vars :
hap_etc_path: "/etc/haproxy"  #   where is haproxy config directory
hap_conf_d_path: "{{ hap_etc_path }}/conf.d"   # where are config fragments stored
hap_update_global_files: no   # will common files be overriden
hap_default_ssl_pem_file: "/etc/ssl/private/haproxy.pem"  # where is stored sll certificate

Mandatory vars :
The only mandatory var is the backend definition as yaml dictionnary.

hap_backends:                   
  mybackends_web:              # backend name, need to be unique, defined in inventory as group of backend servers loadbalanced
    balance_mode: roundrobin   # balance mode, see http://cbonte.github.io/haproxy-dconv/configuration-1.6.html#balance
    acl_lines:                 # list of acl for the backend, see http://cbonte.github.io/haproxy-dconv/configuration-1.6.html#7.1    
      - path_beg /ping
      - path_beg /notify
    server_http_port: 8080     # port the backend servers are listening
    server_inventory_ip_varname: internal_ip     # which variable to use for destination IP/fqdn (generaly internal_ip for GCE, or ansible_ssh_host for non-cloud-mapped-whatever env.)
  blogbackends:
    balance_mode: roundrobin
    acl_lines:
      - path_beg /blog
    server_http_port: 80
    server_inventory_ip_varname: ansible_ssh_host

with this var, you will need groups mybackends_web and blogbackends in the inventory


License
-------

Licensed under the MIT License. See the [LICENSE](LICENSE) file for details.


Author Information
------------------

See my other "work on my computer" ansible things on https://www.github.com/bjolivot

