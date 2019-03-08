# xgql

Schema aware, emmet-like expression shorthand for graphql queries

# Separator

Usage

    >query,subscriptions,mutations

    # produces

    query {
      id
      isTest
      hasArgs
    }

    subscriptions {
      # nothing because it has no fields
    }

    mutations {
      setString
    }

# Child `>`

If no prefix is provided then it will be no brackets surrounding it. This is only really useful at the root level.

Usage:

    query>id,isTest,hasArgs
    
    # expands into...

    query {
      id
      isTest
      hasArgs
    }

<!-- break -->

    query>id,isTest,hasArgs,test

    # will output...

    query {
      id
      isTest
      hasArgs
      test { # it knows to expand the child elements since it was explicitly stated in the expression.
        id
        isTest
        hasArgs
      }
    }

# Catch All `*`

    query>*

    # will output...

    query {
      id
      isTest
      hasArgs
      # it doesn't expand the other fields because they are nested types
    }

## Fields (Not-nullable) `*!`

    query>*!

    # produces...

    query {
      # nothing to show because no fields are non-nullable
    }

### Unions `|`

    query>union>_typename,|First|Second

    # will output...

    query {
      union {
        _typename
        ... on First {
          name
        }
        ... on Second {
          name
        }
      }
    }

<!-- break -->

    # For readability I'm this is on multiple lines
    query >
      union >
        _typename,
        |First >
          name,
          first
        ^
        |Second

    # will output...

    query {
      union {
        _typename
        ... on First {
          name,
          first
        }
        ... on Second {
          name
        }
      }
    }


### Nesting `>*`

    query>*>*

    # will output...

    query {
      id
      isTest
      hasArgs
      test { # it includes test now because we have told it to expand further down the tree
        id
        isTest
        hasArgs
      }
    }

<!-- break -->

     query>*1

    # will output...

    query {
      id
      isTest
      hasArgs
      test { # it includes test now because we have told it to expand further down the tree
        id
        isTest
        hasArgs
      }
    }

#### Expand All `~`

    query>*~ # expands all without looping

<!-- break -->

    query>*~1 # loops once

## Arguments `?;`

    query>hasArgs?string='abc',int=1; 

    # outputs...

    query {
      hasArgs(string: "abc", int: 1)
    }

# Climb Up `+`

    query>test>id+longDescriptionType>id

    # will result in...

    query {
      test {
        id
      }
      longDescriptionType {
        id
      }
    }