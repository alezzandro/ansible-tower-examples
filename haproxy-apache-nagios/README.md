HAProxy + Apache + Nagios: Example Playbooks
-----------------------------------------------------------------------------
#### Note: the original playbooks and roles are developed by Ansible's guys.

Prerequisites:
- Expects CentOS/RHEL 7 hosts

This example is an extension of the original RHEL6 LAMP deployment. Here we'll install
and configure a web server with an HAProxy load balancer in front, and deploy
a simple webpage to the web servers. This set of playbooks also have the
capability to dynamically add and remove web server nodes from the deployment.
It also includes examples to do a rolling update of a stack without affecting
the service.

This playbook will also configure a Nagios monitoring node.

### Initial Site Setup

First we configure the entire stack by listing our hosts in the 'hosts'
inventory file, grouped by their purpose:

		[webservers]
		webserver1
		webserver2

		[lbservers]
		haproxy

		[monitoring]
		nagios

After which we execute the following command to deploy the site:

		ansible-playbook -i hosts site.yml

The deployment can be verified by accessing the IP address of your load
balancer host in a web browser: http://<ip-of-lb>:8888. Reloading the page
should have you hit different webservers.

The Nagios web interface can be reached at http://<ip-of-nagios>/nagios/

The default username and password are "nagiosadmin" / "nagiosadmin".

### Removing and Adding a Node

Removal and addition of nodes to the cluster is as simple as editing the
hosts inventory and re-running:

	ansible-playbook -i hosts site.yml

### Removing and Adding a Node to the monitoring

Removal and addition of nodes to nagios:

	ansible-playbook -i hosts remonitor_webserver.yml -e targethost=web01
	ansible-playbook -i hosts unmonitor_webserver.yml -e targethost=web01

### Removing and Adding a Node to the monitoring

Removal and addition of nodes to nagios:

	ansible-playbook -i hosts reinclude_webserver.yml -e targethost=web01
	ansible-playbook -i hosts exclude_webserver.yml -e targethost=web01

### Executing a custom command on a node

Just run the following command:

	ansible-playbook -i hosts execute_custom_command.yml -e targethost=web01

### Rolling Update

Rolling updates are the preferred way to update the web server software or
deployed application, since the load balancer can be dynamically configured
to take the hosts to be updated out of the pool. This will keep the service
running on other servers so that the users are not interrupted.

In this example the hosts are updated in serial fashion, which means that
only one server will be updated at one time. If you have a lot of web server
hosts, this behaviour can be changed by setting the 'serial' keyword in
webservers.yml file.

Just execute the following command:

	 ansible-playbook -i hosts rolling_update.yml
