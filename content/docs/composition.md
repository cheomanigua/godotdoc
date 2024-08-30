---
weight: 300
title: "Composition"
description: ""
icon: "article"
date: "2024-08-28T13:51:43+02:00"
lastmod: "22024-08-28T13:51:43+02:00"
draft: false
toc: true
---

A good game arquitecture is to structure the game in small components

1. What are components
    - Small blocks of useful functionality
    - Work independently of another component
    - Can be re-configured for easy prototyping
    - Ideally known as little as possible outside of the component

2. Accessing components
    - get_node("SomeNode") or $SomeNode
    - %SomeNode
    - creating a class
    - using the physics engine (Areas, etc.)
    - groups
    - autoloads
    - @export NodePath

3. Component communication
Can be done with minimal coupling using:
    - signals
    - contracts
    - signal relays
    - propagate_call


### Node Paths
- You can access nodes by using `$Node` or `get_node("Node")`. Knowing this, then:
| | |
|-|-|
| `$NodeA/NodeB` | access children |
| `$".."` or `get_parent()` | access parent |
| `$".."/NodeA` | access sibling |
| `$"."` or `self` | access current node |

- Scene's Unique Nodes can be found with `%Node` from anywhere in the scene:
| | |
|-|-|
| `%Node` | access node everywhere |

