# Language reference

The Structurizr DSL provides a way to define a software architecture model (based upon the [C4 model](https://c4model.com)) as text, using a domain specific language (DSL). The [Structurizr CLI](https://github.com/structurizr/cli) (command line interface) provides tooling to parse DSL workspace definitions, upload them to the Structurizr cloud service/on-premises installation, and export diagrams to other formats (e.g. PlantUML and WebSequenceDiagrams).

See [https://structurizr.com/dsl](https://structurizr.com/dsl) for a demo of the DSL.

## Table of contents

- [General rules](#general-rules)
- [Comments](#comments)
- [Identifiers](#identifiers)
- [Includes](#includes)
- [Grammar](#grammar)
	- [workspace](#workspace)
		- [model](#model)
			- [enterprise](#enterprise)
			- [person](#person)
			- [softwareSystem](#softwareSystem)
				- [container](#container)
					- [component](#component)
			- [deploymentEnvironment](#deploymentEnvironment)
				- [deploymentNode](#deploymentNode)
					- [infrastructureNode](#infrastructureNode)
					- [containerInstance](#containerInstance)
			- [-> (relationship)](#relationship)
		- [views](#views)
			- [systemLandscape](#systemLandscape-view)
				- [include](#include)
				- [exclude](#exclude)
				- [autoLayout](#autoLayout)
				- [animationStep](#animationStep)
			- [systemContext](#systemContext-view)
				- [include](#include)
				- [exclude](#exclude)
				- [autoLayout](#autoLayout)
				- [animationStep](#animationStep)
			- [container](#container-view)
				- [include](#include)
				- [exclude](#exclude)
				- [autoLayout](#autoLayout)
				- [animationStep](#animationStep)
			- [component](#component-view)
				- [include](#include)
				- [exclude](#exclude)
				- [autoLayout](#autoLayout)
				- [animationStep](#animationStep)
			- [filtered](#filtered-view)
			- [dynamic](#dynamic-view)
				- [autoLayout](#autoLayout)
			- [deployment](#deployment-view)
				- [include](#include)
				- [exclude](#exclude)
				- [autoLayout](#autoLayout)
				- [animationStep](#animationStep)
			- [styles](#styles)
				- [element](#element-style)
				- [relationship](#relationship-style)
			- [themes](#themes)
			- [branding](#branding)

## General rules

- Line breaks are important, whitespace/indentation isn't.
- Keywords are case-insensitive (e.g. you can use ```softwareSystem``` or ```softwaresystem```).
- Double quote characters (```"..."```) are optional when a property contains no whitespace.
- Opening curly brace symbols (```{```) must be on the same line (i.e. the last token of the statement, not on a line of their own).
- Closing curly brace symbols (```}```) must be on a line of their own.
- Use ```""``` as a placeholder for an earlier optional property that you'd like to skip.
- Each view must have a unique "key".
- See [Structurizr - Notation](https://structurizr.com/help/notation) for details of how tags and styling works.

## Comments

Comments can be defined as follows:

```
/**
	multi-line comment
*/
```

```
# single line comment
```

```
// single line comment
```

## Identifiers

By default, all elements and relationships are anonymous, in that they can't be referenced from within the DSL. For example, the following statements will create a person and a software system, but neither can be referenced within the DSL. 

```
person "User" "A user of my software system."
softwareSystem "Software System" "My software system"
```

To create a relationship between the two elements, we need to be able to reference them. We can do this by defining an identifier, in the same way that you'd define a variable in many programming languages.

```
p = person "User" "A user of my software system."
ss = softwareSystem "Software System" "My software system"
```

Now we can use these identifiers when creating relationships, specifying which elements should be included/excluded from views, etc.

```
p -> ss "Uses"
```

Identifiers are only needed where you plan to reference the element/relationship.

## Includes

The ```!include``` keyword can be used to include another file, to provide some degree of modularity, and to reuse definition fragments between workspaces.

```
!include <file>
```

The file must be referenced using a relative path, and must be located within the same directory as the parent file, or a subdirectory of it. For example:

```
!include child.dsl
!include subdirectory/child.dsl
``` 

The content of any included files is simply inlined into the parent document. 

## Grammar

The following describes the language grammar, with angle brackets (```<...>```) used to show required properties, and square brackets (```[...]```) used to show optional properties.

Most statements are of the form: ```keyword <required properties> [optional properties]```

```
workspace [name] [description] {

    /**
        multi-line comment
    */

    # single line comment
    // single line comment

    !include <file>

    model {

        enterprise <name> {
            [<identifier> = ]person <name> [description] [tags]
            [<identifier> = ]softwareSystem = softwareSystem <name> [description] [tags] {
                [<identifier> = ]container <name> [description] [technology] [tags] {
                    [<identifier> = ]component <name> [description] [technology] [tags]
                }
            }
        }

		[<identifier> = ]person <name> [description] [tags]
		[<identifier> = ]softwareSystem = softwareSystem <name> [description] [tags] {
			[<identifier> = ]container <name> [description] [technology] [tags] {
				[<identifier> = ]component <name> [description] [technology] [tags]
			}
		}

        <identifier> -> <identifier> [description] [technology] [tags]

        deploymentEnvironment <name> {
            [<identifier> = ]deploymentNode <name> [description] [technology] [tags] {
                [<identifier> = ]deploymentNode <name> [description] [technology] [tags] {
                    [<identifier> = ]infrastructureNode <name> [description] [technology] [tags]
                        [<identifier> = ]containerInstance <identifier>
                }
            }
        }

    }
         
    views {

        systemLandscape <key> [description] {
        	include <*|identifier> [identifier...]
            exclude <identifier> [identifier...]
            autoLayout [tb|bt|lr|rl] [rankSeparation] [nodeSeparation]
            animationStep <identifier> [identifier...]
        }

        systemContext <software system identifier> <key> [description] {
            include <*|identifier> [identifier...]
            exclude <identifier> [identifier...]
            autoLayout [tb|bt|lr|rl] [rankSeparation] [nodeSeparation]
            animationStep <identifier> [identifier...]
        }

        container <software system identifier> <key> [description] {
            include <*|identifier> [identifier...]
            exclude <identifier> [identifier...]
            autoLayout [tb|bt|lr|rl] [rankSeparation] [nodeSeparation]
            animationStep <identifier> [identifier...]
        }

        component <container identifier> <key> [description] {
            include <*|identifier> [identifier...]
            exclude <identifier> [identifier...]
            autoLayout [tb|bt|lr|rl] [rankSeparation] [nodeSeparation]
            animationStep <identifier> [identifier...]
        }

        filtered <baseKey> <include|exclude> <tags> <key> [description]

        dynamic <*|software system identifier|container identifier> <key> [description] {
            <identifier> -> <identifier> [description]
            autoLayout [tb|bt|lr|rl] [rankSeparation] [nodeSeparation]
        }

        deployment <*|software system identifier> <environment name> <key> [description] {
            include <*|identifier> [identifier...]
			exclude <identifier> [identifier...]
            autoLayout [tb|bt|lr|rl] [rankSeparation] [nodeSeparation]
            animationStep <identifier> [identifier...]
        }

        styles {
            element <tag> {
                shape <Box|RoundedBox|Circle|Ellipse|Hexagon|Cylinder|Pipe|Person|Robot|Folder|WebBrowser|MobileDevicePortrait|MobileDeviceLandscape|Component>
                icon <file>
                width <integer>
                height <integer>
                background <#rrggbb>
                color <#rrggbb>
                colour <#rrggbb>
                stroke <#rrggbb>
                fontSize <integer>
                border <solid|dashed|dotted>
                opacity <integer: 0-100>
                metadata <true|false>
                description <true|false>
            }

            relationship <tag> {
                thickness <integer>
                color #777777
                colour #777777
                dashed <true|false>
                routing <Direct|Orthogonal|Curved>
                fontSize <integer>
                width <integer>
                position <integer: 0-100>
                opacity <integer: 0-100>
            }
        }

        themes <themeUrl> [themeUrl] ... [themeUrl]

        branding {
            logo <file>
            font <name> [url]
        }

    }

}
```

### workspace

```workspace``` is the top level language construct, and the wrapper for the [model](#model) and [views](#views). A workspace can optionally be given a name and description.

```
workspace [name] [description] {
	...
}
```

### model

Each workspace must contain a ```model``` block, inside which elements and relationships are defined.

```
model {
	...
}
```

The ```model``` block can contain the following children:

- [enterprise](#enterprise)
- [person](#person)
- [softwareSystem](#softwareSystem)
- [-> (relationship)](#relationship)
- [deploymentEnvironment](#deploymentEnvironment)

## enterprise

The ```enterprise``` keyword provides a way to define a named "enterprise" (e.g. an organisation). Any people or software systems defined inside this block will be deemed to be "internal", while all others will be deemed to be "external". On System Landscape and System Context diagrams, an enterprise is represented as a dashed box. Only a single enterprise can be defined within a model.

```
enterprise <name> {
	...
}
```

The ```enterprise``` block can contain the following children:

- [person](#person)
- [softwareSystem](#softwareSystem)
- [-> (relationship)](#relationship)

## person

The ```person``` keyword defines a person (e.g. a user, actor, role, or persona).

```
person <name> [description] [tags]
```

## softwareSystem

The ```softwareSystem``` keyword defines a software system.

```
softwareSystem <name> [description] [tags]
```

Add braces if you would like to define child containers:

```
softwareSystem <name> [description] [tags] {
	...
}
```

## container

The ```container``` keyword defines a container, within a software system.

```
container <name> [description] [technology] [tags]
```

Add braces if you would like to define child components:

```
container <name> [description] [technology] [tags] {
	...
}
```

## component

The ```component``` keyword defines a component, within a container.

```
component <name> [description] [technology] [tags]
```

## deploymentEnvironment

The ```deploymentEnvironment``` keyword provides a way to define a deployment environment (e.g. development, testing, staging, live, etc).

```
deploymentEnvironment <name> {
	...
}
```

A deployment environment can contain one or more [deploymentNode](#deploymentNode) elements.

## deploymentNode

The ```deploymentNode``` keyword is used to define a deployment node.

```
deploymentNode <name> [description] [technology] [tags] {
	...
}
```

Deployment nodes can be nested, so a deployment node can contain other deployment nodes. A deployment node can also contain [infrastructureNode](#infrastructureNode) and [containerInstance](#containerInstance) elements.


## infrastructureNode

The ```infrastructureNode``` keyword defines an infrastructure node, which is typically something like a load balancer, firewall, DNS service, etc.

```
infrastructureNode <name> [description] [technology] [tags]
```

## containerInstance

The ```containerInstance``` keyword defines an instance of the specified container that is deployed on the parent deployment node.

```
containerInstance <identifier>
```

The ```identifier``` must represent a container.

## relationship

```->``` is used to define a uni-directional relationship between two elements.

```
<identifier> -> <identifier> [description] [technology] [tags]
```

### views

Each workspace can also contain one or more views, defined with the ```views``` block.

```
views {
	...
}
```

The ```views``` block can contain the following:

- [systemLandscape](#systemLandscape-view)
- [systemContext](#systemContext-view)
- [container](#container-view)
- [component](#component-view)
- [filtered](#filtered-view)
- [dynamic](#dynamic-view)
- [deployment](#deployment-view)
- [styles](#styles)
- [themes](#themes)
- [branding](#branding)

### systemLandscape view

The ```systemLandscape``` keyword is used to define a [System Landscape view](https://c4model.com/#SystemLandscapeDiagram).

```
systemLandscape <key> [description] {
	...
}
```

The following keywords can be used within the ```systemLandscape``` block:

- [include](#include)
- [exclude](#exclude)
- [autoLayout](#autoLayout)
- [animationStep](#animationStep)

### systemContext view

The ```systemContext``` keyword is used to define a [System Context view](https://c4model.com/#SystemContextDiagram) for the specified software system.

```
systemContext <software system identifier> <key> [description] {
	...
}
```

The following keywords can be used within the ```systemContext``` block:

- [include](#include)
- [exclude](#exclude)
- [autoLayout](#autoLayout)
- [animationStep](#animationStep)

### container view

The ```container``` keyword is used to define a [Container view](https://c4model.com/#ContainerDiagram) for the specified software system.

```
container <software system identifier> <key> [description] {
	...
}
```

The following keywords can be used within the ```container``` block:

- [include](#include)
- [exclude](#exclude)
- [autoLayout](#autoLayout)
- [animationStep](#animationStep)

### component view

The ```component``` keyword is used to define a [Component view](https://c4model.com/#ComponentDiagram) for the specified container.

```
component <container identifier> <key> [description] {
	...
}
```

The following keywords can be used within the ```component``` block:

- [include](#include)
- [exclude](#exclude)
- [autoLayout](#autoLayout)
- [animationStep](#animationStep)

### filtered view

The ```filtered``` keyword is used to define a [Filtered view](https://structurizr.com/help/filtered-views) on top of the specified view.

```
filtered <baseKey> <include|exclude> <tags> <key> [description]
```

The ```baseKey``` specifies the key of the System Landscape, System Context, Container, or Component view on which this filtered view should be based. The mode (```include``` or ```exclude```) defines whether the view should include or exclude elements/relationships based upon the ```tags``` provided.

### dynamic view

The ```dynamic``` keyword defines a [Dynamic view](https://c4model.com/#DynamicDiagram) for the specified scope.

```
dynamic <*|software system identifier|container identifier> <key> [description] {
	...
}
```

The first property defines the scope of the view, and therefore what can be added to the view, as follows:

- ```*``` scope: People and software systems.
- Software system scope: People, other software systems, and containers belonging to the software system. 
- Container scope: People, other software systems, other containers, and components belonging to the container. 

Unlike the other diagram types, Dynamic views are created by specifying the relationships that should be added to the view, within the ```dynamic``` block, as follows:

```
<identifier> -> <identifier> [description]
```

If a relationship between the two elements does not exist, it will be automatically created.

The following keywords can also be used within the ```dynamic``` block:

- [autoLayout](#autoLayout)

### deployment view

The ```deployment``` keyword defines a [Deployment view](https://c4model.com/#DeploymentDiagram) for the specified scope and deployment environment.

```
deployment <*|software system identifier> <environment name> <key> [description] {
	...
}
```

The first property defines the scope of the view, and the second property defines the deployment environment. The combination of these two properties determines what can be added to the view, as follows:

- ```*``` scope: All deployment nodes, infrastructure nodes, and container instances within the deployment environment.
- Software system scope: All deployment nodes and infrastructure nodes within the deployment environment. Container instances within the deployment environment that belong to the software system.

The following keywords can be used within the ```deployment``` block:

- [include](#include)
- [exclude](#exclude)
- [autoLayout](#autoLayout)
- [animationStep](#animationStep)

### include

To include elements on a view, use one or more ```include``` statements inside the block defining the view.

```
include <*|identifier> [identifier...]
```

Elements and relationships can either be specified using individual identifiers, or using the wildcard (```*```) identifier, which operates differently depending upon the type of diagram.

- System Landscape view: Include all people and software systems.
- System Context view: Include all people and software systems that are directly connected to the software system in scope.
- Container view: Include all containers within the software system in scope plus and all people, software systems, and containers directly connected to them. 
- Component view: Include all components within the container in scope plus and all people, software systems, containers, and components directly connected to them.
- Filtered view: (not applicable)
- Dynamic view: (not applicable)
- Deployment view: Include all deployment nodes, infrastructure nodes, and container instances deifned within the deployment environment and (optional) software system in scope.

### exclude

To exclude specific elements or relationships, use one or more ```exclude``` statements inside the block defining the view.

```
exclude <identifier> [identifier...]
```

### autoLayout

To enable automatic layout mode for the diagram, use the ```autoLayout``` statement inside the block defining the view.

```
autoLayout [tb|bt|lr|rl] [rankSeparation] [nodeSeparation]
```

The first property is the rank direction:

- ```tb```: Top to bottom (default)
- ```bt```: Bottom to top
- ```lr```: Left to right
- ```rl```: Right to left

The second property is the separation of ranks in pixels (default: ```300```), while the third property is the separation of nodes in the same rank in pixels (default: ```300```).

### animationStep

The ```animationStep``` keyword defines an animation step consisting of the specified elements.

```
animationStep <identifier> [identifier...]
```

### styles

```styles``` is the wrapper for one or more element/relationship styles, which are used when rendering diagrams.

```
styles {
	...
}
```

The ```styles``` block can contain the following:

- [element](#element-style)
- [relationship](#relationship-style)

### element style

The ```element``` keyword is used to define an element style. All nested properties (```shape```, ```icon```, etc) are optional, see [Structurizr - Notation](https://structurizr.com/help/notation) for more details.

```
element <tag> {
    shape <Box|RoundedBox|Circle|Ellipse|Hexagon|Cylinder|Pipe|Person|Robot|Folder|WebBrowser|MobileDevicePortrait|MobileDeviceLandscape|Component>
    icon <file>
    width <integer>
    height <integer>
    background <#rrggbb>
    color <#rrggbb>
    colour <#rrggbb>
    stroke <#rrggbb>
    fontSize <integer>
    border <solid|dashed|dotted>
    opacity <integer: 0-100>
    metadata <true|false>
    description <true|false>
}
```
            
### relationship style

The ```relationship``` keyword is used to define a relationship style. All nested properties (```thickness```, ```color```, etc) are optional, see [Structurizr - Notation](https://structurizr.com/help/notation) for more details.

```
relationship <tag> {
    thickness <integer>
    color #777777
    colour #777777
    dashed <true|false>
    routing <Direct|Orthogonal|Curved>
    fontSize <integer>
    width <integer>
    position <integer: 0-100>
    opacity <integer: 0-100>
}
```

### themes

The ```themes``` keyword can be used to specify one or more themes that should be used when rendering diagrams. See [Structurizr - Themes](https://structurizr.com/help/themes) for more details.

```
themes <themeUrl> [themeUrl] ... [themeUrl]
```

### branding

The ```branding``` keyword allows you to define some custom branding that should be usedwhen rendering diagrams and documentation. See [Structurizr - Branding](https://structurizr.com/help/branding) for more details.

```
branding {
	logo <file>
	font <name> [url]
}
```

