---
title: The Boxfile
---

The Boxfile is a yaml config file housed in the root of your project's repo that defines what components should exist in your app and all configuration related to your app’s deployment and infrastructure. It allows you to custom-configure your app's environment to your project's specific needs.

#### Important Things to Know About the Boxfile
- Your boxfile must be named `boxfile.yml` in order to be recognized.
- The `boxfile.yml` must be placed at the root of your project's repo.
- The boxfile must be valid yaml markup. You can brush up on your yaml at [yaml.org](http://yaml.org/start.html) or check your syntax at [yamllint.com](http://www.yamllint.com/).
- While boxfiles aren't required, without one, Nanobox doesn't know how to build, configure, or run your app.

**Note**: In a local Nanobox dev app, application components are created and destroyed by adding and removing them from your boxfile. In production, components can only be removed through your dashboard.

####Sample Boxfile
```yaml
code.build:
  engine: nanobox-io/ruby
  config:
    ruby_runtime: ruby-2.2
  lib_dirs:
    - vendor
  reuse_libs: true

code.deploy:
  transform:
    - 'bundle exec rake fix-yaml'

web.site:
  start: 'bundle exec puma -c config/puma.conf'
  network_dirs:
    data.storage:
      - usr/uploads

worker.jobs:
  start: 'ruby workers/image_processor.rb'
  network_dirs:
    data.storage:
      - usr/uploads

data.db:
  image: nanobox-io/postgresql

data.storage:
  image: nanobox-io/gluster

data.queue:
  image: nanobox-io/redis
```

## The Boxfile Structure

Your app is made up of components. A component is a piece of your app's infrastructure tasked with a specific function. Each component is configured separately in the Boxfile.

Yaml is used in the Boxfile because of its simplicity and clarity when defining relationships between data. A component's configuration is indented and nested under the component ID. Each level and relationship of data is defined by indentation.

#### Boxfile Structure

```yaml
web.site: #<------------------ Component ID
  start: 'rails s'        #|
  network-dirs:           #|-- Component Settings
    data.storage:         #|
      - usr/uploads       #|


data.postgres: #<------------- Component ID
  image: 'postgresql'     #|
  config:                 #|-- Component Settings
    pg_version: 9.4       #|
                          #|
```

#### Things to Note About yaml
- **Yaml is very picky about indentation**. Indentation is what is used to define the relationship structure of information in your Boxfile.
- **Tabs are not valid yaml**. All indentation should be done using double spaces.

### Component IDs
Every component in your app has a component ID which consists of two parts:

- Component Type - [web, worker, or data](#component-types).
- Unique identifier - Completely up to you.

Component IDs must be unique.

#### IMPORTANT: Component IDs Cannot be Changed
Component IDs cannot be changed once a boxfile is deployed and the components are created. If they are changed, they will be recognized as new components.

#### Component IDs
```yaml
# Pattern
type.unique-identifer:

# Examples
web.site:
web.api:
worker.jobs:
worker.image-processor:
data.db:
data.storage:
```

### Component Types
There are three types of components:

- **web**: A code component with publicly accessible ports.
- **worker**: A backend code component with no publicly accessible ports.
- **data**: A component that houses data of some sort.

## Sections of the Boxfile
Boxfiles consist of a handful of sections or "nodes": env, dev, web, worker, data. These are covered in detail in the next few docs, but here are some quick descriptions:

[`code.build`](/app-config/boxfile/code-build/) - Defines the build, environment, and configuration for web and worker components.

[`code.deploy`](/app-config/boxfile/code-deploy/) - Defines deploy hooks and possible code transformations.

[`dev`](/app-config/boxfile/dev) - Defines settings unique to your [local nanobox dev environment](/local-dev/).  

[`web`](/app-config/boxfile/web/) - Defines settings unique to each web component.  

[`worker`](/app-config/boxfile/worker/) - Defines settings unique to each worker component.  

[`data`](/app-config/boxfile/data/) - Defines settings unique to a specific data component.  
