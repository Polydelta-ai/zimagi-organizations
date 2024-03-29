<p align="center">
  <img width="460" height="150" src="assets/zimagi-logo.png">
</p>
<hr/>

<h1 align="center"><b>Organization</b> <i>Data Module</i></h1>
<br/>

## Overview
<hr/>
<br/>

This module provides a foundation for building organization based data models within the Zimagi platform.

<br/>

## Components
<hr/>
<br/>

### Roles
<br/>

| _Name_ | _Description_ |
| ---- | ----------- |
| **organization-admin** | _Administrative privileges over organization data_ |
| **organization-auditor** | _Viewing privileges over organization data_ |
<br/>

### Data Models
<br/>

| _Name_ | _Description_ |
| ---- | ----------- |
| **location** | _Basic location model with city, state, and zipcode_ |
| **organization** | _Basic organization model meant to be extended_ |
<br/>

### Orchestration Profiles
<br/>

| _Name_ | _Description_ |
| ---- | ----------- |
| **build** | _Builds data centric Zimagi specifications_ |
<br/>

## How to Use
<hr/>
<br/>

_From the Linux command line interface:_

```bash
# Install the Zimagi application and remote client

pip install zimagi

# Initialize Zimagi platform and make any configuration updates

zimagi env get
#
# The first time executed the zimagi command will provide a link
# to the autogenerated configuration file for the user.  Make any
# changes to this file and run the zimagi command again to continue
# initializing the platform
#

# Install this module into the Zimagi platform

zimagi module add \
    https://github.com/Polydelta-ai/zimagi-organizations.git \
    reference=main

# Work with the Zimagi location data models
# (also available through the Command API)

zimagi location list --help
zimagi location get --help
zimagi location save --help
zimagi location remove --help
zimagi location clear --help

# Access location data through the Data API

# List or search locations
curl https://localhost:5323/location
# Get JSON list of specific fields for searchable locations
curl https://localhost:5323/location/json
# Get a CSV export of specific fields for searchable locations
curl https://localhost:5323/location/csv
# Get a total count of searchable locations with field name (non NULL)
curl https://localhost:5323/location/count/{field_name}
# Get a list of unique field values for searchable locations (non NULL)
curl https://localhost:5323/location/values/{field_name}

# Work with the Zimagi organization data models
# (also available through the Command API)

zimagi organization list --help
zimagi organization get --help
zimagi organization save --help
zimagi organization remove --help
zimagi organization clear --help

# Access organization data through the Data API

# List or search organizations
curl https://localhost:5323/organization
# Get JSON list of specific fields for searchable organizations
curl https://localhost:5323/organization/json
# Get a CSV export of specific fields for searchable organizations
curl https://localhost:5323/organization/csv
# Get a total count of searchable organizations with field name (non NULL)
curl https://localhost:5323/organization/count/{field_name}
# Get a list of unique field values for searchable organizations (non NULL)
curl https://localhost:5323/organization/values/{field_name}
```

_From a Python script:_

```bash
# First ensure zimagi is added to environment or requirements.txt
zimagi>=0.11.0
```

```python
# Import the zimagi package
import zimagi

# Create a message handler for streaming Command API messages
# (if you need or want)
def message_callback(message):
    message.display()

# Create API gateway (using default/development settings)
command_api = zimagi.CommandClient(
    user = "admin",
    token = "uy5c8xiahf93j2pl8s00e6nb32h87dn3",
    encryption_key = "RFJwNYpqA4zihE8jVkivppZfGVDPnzcq",
    host = "localhost",
    port = 5123,
    message_callback = message_callback
)
data_api = zimagi.DataClient(
    user = "admin",
    token = "uy5c8xiahf93j2pl8s00e6nb32h87dn3",
    encryption_key = "RFJwNYpqA4zihE8jVkivppZfGVDPnzcq",
    host = "localhost",
    port = 5323
)

# Add Zimagi module
command_api.extend('https://github.com/Polydelta-ai/zimagi-organizations.git', 'main',
    provider = 'git'
)

# Execute any Zimagi command
#
# Can reference commands using:
#
# * space separated strings "user list" (CLI format)
# * slash separated strings "user/list" (API format)
# * dot separated strings   "user.list"
#
response = command_api.execute('organization.list')
#
# OR
#
response = command_api.organization__list() # __ converted to /

#
# Organization SDK interface
#

# Save organizations
data_api.create('organization',
    name = "Testing LTD",
    location = {
        'city': 'Washington'
        'state': 'DC',
        'zipcode': 20024
    }
)

# List and search access organizations (hyperlinked related objects)
results = data_api.list('organization', name__icontains = "federal")

# Return field collections in various formats
json_results = data_api.json('organization', fields = ['name', 'location__city', 'location__state'])
df = data_api.csv('organization', fields = ['name', 'location__zipcode'])

# Access organization data
data = data_api.get('organization', "{key}")

# Get organization field information
results = data_api.values('organization', 'name')
count = data_api.count('organization', 'name')

# Remove organizations
command_api.remove('organization', "{key}")
command_api.clear('organization')
```