# Mongoose-Repo (morepo)
A Mongoose Repository Class

[![Build Status](https://travis-ci.org/Geexteam/mongoose-repo.svg?branch=master)](https://travis-ci.org/Geexteam/mongoose-repo)
[![Dependency Status](https://gemnasium.com/badges/github.com/Geexteam/mongoose-repo.svg)](https://gemnasium.com/github.com/Geexteam/mongoose-repo)
[![NSP Status](https://nodesecurity.io/orgs/geex-team/projects/124c6964-bd3c-4b82-ae9b-633df613c8bb/badge)](https://nodesecurity.io/orgs/geex-team/projects/124c6964-bd3c-4b82-ae9b-633df613c8bb)


## Requirements ##
Make sure you're using Nodejs 8.x or higher, for `async/await` to work

If you're working on Nodejs 6.x, please install morepo version 0.8

## Installation ##

You can install this module with NPM:

    npm install --save morepo

## Getting started ##
You can use this class like it is or make a specific Repository class that extends this class

### Usecase 1: ###
```ES8
const mongoose = require('mongoose');
const Repository = require('morepo');

const postRepo = new Repository(mongoose.model('Post'));
```

### Usecase 2: ###
```ES8
const mongoose = require('mongoose');
const Repository = require('morepo');

class PostRepository extends Repository {

    constructor(model) {
        super(model || mongoose.model('Post'));
    }

    // Override an existing function to add some custom functionality
    async findByObjectId(objectId) {
        try {
            if (!objectId) {
                return {error: 'No ObjectId given'};
            }

            const body = await this.find(
                {_id: objectId},
                this.generateOptions({multiple: false})
            );
            if (body === null) {
                return {error: 'No posts found'};
            }
            return body;
        } catch (error) {

            return {error: error.message};
        }
    }

    generateOptions(options) {
        const base = {
            populate: [
                {path: 'author', select: '-password -registration_date -__v'},
                {path: 'comments'}
            ],
            sort: {date_created: 'descending'},
            multiple: true
        };
        return super.generateOptions(base, options);
    }
}

const postRepo = new PostRepository(mongoose.model('Post'));
```

## Functions ##
### Constructor ###
Creates a new instance of the Repository

#### Params ####
- model - Mongoose Model
- Options:
  - applyStatics - default: true
    Applies all static model functions to the Repo

#### Returns ####
New instance of the Repository

### create ###
#### Params ####
- obj - Object to save to the database

#### Returns ####
Given object saved to the database

### find ###
Executes the given query

#### Params ####
- query - Object
- Options:
  - select
  - populate
    - If something else than an Array, String or Object is given, the value is skipped
    - Examples
     Populate examples:
     String: `created_by`
     Object (with multilevel populate):
         ```JS
         {
            path: 'friends',
            // Get friends of friends - populate the 'friends' array for every friend
            populate: { path: 'friends' }
         }
         ```
     Array: `[One of the 2 above]`
  - limit
  - skip
  - sort
  - lean - default: false
  - count - default: false
  - multiple - default: true

#### Returns ####
Query result
If `multiple` is `true`: it'll return an `Array`, else it'll return an `Object`

### findByObjectId ###
Looks for 1 document based on the objectId

#### Params ####
- query - Object
- Options:
  - select
  - populate
  - lean - default: false

#### Returns ####
The request document

### findAll ###
Retrieves all documents in that collection

#### Params ####
none

#### Returns ####
Array with all documents in that collection

### update ###
Retrieves a document by it's ID, assigns the new values to it (thanks you lodash) and saves it

#### Params ####
- objectId - Object || String
- newValues - Object

#### Returns ####
The updated document

### remove ###
Removes the document identified by the given ObjectID from the collection

#### Params ####
- objectId - Object || String

#### Returns ####
The remove's result



## Roadmap ##

v1.0.0
- Tests
- Folder with examples
- Better Doc and readme
