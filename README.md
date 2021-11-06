# dp-docker-builder
Builds DataPower docker images with slightly more config then the IBM official base image.

## How to use
specify version variable, this needs to match an IBM image.
```yaml
  vars:
    dp_version: 10.0.1.4
```
or 
```yaml
  vars:
    dp_version: 2018.4.1.17
```
Build image and push to docker hub, Follow prompts to provide docker login username/password.
```
ansible-playbook dp-docker.yml
```

# How it works
Jinja templated Dockerfiles determines IBM base version tag and `drouter/config`. 
`drouter/config` path differs between version 2018 and 10, the template selects the correct COPY command.

```jinja
{% if '2018' in version -%}
COPY $PWD/config /drouter/config
USER root
RUN chown -R drouter:drouter /drouter/config 
{%- else -%}
COPY $PWD/config /opt/ibm/datapower/drouter/config
USER root
RUN chown -R drouter:drouter /opt/ibm/datapower/drouter/config
{%- endif %}
```
This additional config has rest-mgmt and web-mgmt interfaces configured
