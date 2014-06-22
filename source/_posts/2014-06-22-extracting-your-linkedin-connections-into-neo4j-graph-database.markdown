---
layout: post
title: "Extracting your LinkedIn connections into Neo4j graph database"
date: 2014-06-22 17:36:30 +1000
comments: true
categories: neo4j
---

Today I was playing with exploring my LinkedIn network - just for fun and as a pretext to play with Neo4j.

I've managed to ask simple questions like *"With whom do I have the most contacts in common?"* or *"What is the most popular first name in my network?"* (Piotr & Marcin)

## Challange #1 - How to get the data?

I have decided to use [inmaps](http://inmaps.linkedinlabs.com/network) from Linkedin. Below you can see screenshot of my network:

![alt tag](/images/linked_network.jpg)

By using Chrome Developer Tools you can see Network traffic made by inmaps. There are 2 interesting resources for us:

```
http://inmaps.linkedinlabs.com/network_data
{
  "edges":[
    {"dest":"Um6QrFGUXX","src":"Uv1KrWpoXX"}, ...
  ]
}


http://inmaps.linkedinlabs.com/connections_data

{ 
  "Um6QrFGUXX":{
    "firstName":"Marcin",
    "lastName":"Nowak",
    "headline":"Developer"
  },
  "Uv1KrWpoXX":{
    "firstName":"Piotr",
    "lastName":"Kowalski",
    "headline":"Manager"
  },
  ...
}

```

I've saved content of both on my file system. This is the data that we can now import into Neo4j.

## Importing data into Neo4j

First you need Neo4j, you can get it here: http://www.neo4j.org/download

I've used simple Ruby script similar to this in order to import data:

{% codeblock import.rb %}
# gem 'neo4j-core', "~>3.0.0.alpha"


@session = Neo4j::Session.open(:server_db, "http://localhost:7474")

def get_person id
  Neo4j::Label.find_nodes(:person, :id, id).first
end

def upsert_person key, value
  id = key
  properties = { 
    id: key,
    firstname: value["firstName"],
    lastname: value["lastName"],
    headline: value["headline"]
  }

  existing = get_person(id)
  if existing
    existing.update_props(properties)
  else
    person = Neo4j::Node.create(properties, :person)
  end
end

def upsert_connection(id, other_id, type)
  properties = {type: type}
  
  person = get_person(id)
  connection = get_person(other_id)
  
  existing = person.rels(type: :connected, dir: :incoming, between: connection).first
  if existing
    existing.update_props(properties)
  else
    person.create_rel(:connected, connection, properties)
  end

end

network_data = JSON.parse(File.read('network_data.json'));
connections_data = JSON.parse(File.read('connections_data.json'));

connections_data.each do |key, value|
  upsert_person key,value
end

connections_data["edges"].each do |edge|
  upsert_connection(edge["dest"],edge["src"],"src") 
end


{% endcodeblock %}

## Exploring the data

Once data was imported I've went to http://localhost:7474/browser/ and started experimenting with queries.

### With whom do I have the most contacts in common?

```
MATCH    (user)-[r]-(friend)
WITH     user, count(friend) AS friends
ORDER BY friends DESC
WHERE    friends > 90 
RETURN   user.firstname, user.lastname, user.headline, friends
```


### What is the most popular first name in my network?

```
MATCH    (user)
WITH     user.firstname as firstname, 
         collect(DISTINCT user.lastname) as lastnames,  
         count(DISTINCT user.lastname) as c
ORDER    BY c DESC     
RETURN   firstname, lastnames, c

("Piotr", "Marcin", "Pawel", "Anna", "Paul", "Lukasz")
```

### What are the most popular headlines in my network?

```
MATCH    (user)
WITH     left(upper(trim(user.headline)), 12) as headline, 
         collect(DISTINCT (user.firstname + user.lastname)) as names,  
         count(DISTINCT (user.firstname + user.lastname)) as c
ORDER    BY c DESC
RETURN   headline, names, c


("RECRUITMENT", "SOFTWARE DEV", "SOFTWARE ENG")

```

That's it for today ;)
