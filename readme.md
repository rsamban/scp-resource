# Scp Resource
Downloads files from ssh server
## Resource Type Configuration
```
resource_types:
- name: myscp
  type: docker-image
  source:
    repository: rsamban/scp-resource

```
## Source Configuration
To download products from ssh server

```
resources:
- name: pcf-products
  type: myscp
  source:
    host:
    username:
    private_key:
    password:
    source_dir:
    source_file:
```
`host:` Required. Host name.  
`username:` Required. User name.  
`private_key:` Optional. private key for key authentication. Only private_key or password must be passed.  
`password:` Optional. for password authentication. Only private_key or password must be passed.  
`source_dir:` Required. directory where the source file resides.
`source_file:` Optional. If not specified, all the files in the `source_dir` will be copied. Regular expressions are supported. (e.g: to download all the txt files, you can specify `*.txt` )

Parameters for the the source:  
The value speficied in parameter section for given variable will override the value specified in source.
```
params:
  host:
  username:
  private_key:
  password:
  source_dir:
  source_file:
```


## Behavior
Example usage: (authentication with password)
```
resource_types:
- name: myscp
  type: docker-image
  source:
    repository: rsamban/scp-resource
resources:
- name: download-files
  type: myscp
  source:
    host: 172.28.81.3
    username: ubuntu
    password: welcome1
    source_dir: temp
jobs:
- name: download-products
  plan:
  - aggregate:
    - get: download-files
      params:
        host: 172.28.81.4 # overrides host specified in resources section.
    - task: scptest
      config:
        platform: linux
        image_resource:
          type: registry-image
          source:
            repository: rsamban/scp-resource
        inputs:
        - name: download-files
        outputs:
        - names: products
        run:
          path: sh
          args:
            - cp download-files/* products; echo "files copied."
```
