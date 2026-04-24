# Day 04: REST vs GraphQL
> *Two philosophies for API design*

**Month 1: Foundations > Week 1: Basics of System Design**
**Tags:** `Theory` `JavaScript` `Python`
**Estimated Time:** 90-120 minutes

---

## Theory

### REST - The Classic Approach

REST maps every resource to a URL and uses HTTP verbs to operate on it. Building a profile page requires **3 separate requests**:

- `GET /users/42` returns 15 fields — you only needed 3 (**over-fetching**)
- `GET /users/42/posts` — separate trip just for posts (**under-fetching** from the first call)
- `GET /posts/1/comments` — yet another trip for comments

Each trip adds network latency. On a mobile phone on 3G, 3 round trips can mean 1-2 extra seconds.

### GraphQL - Ask for Exactly What You Need

Facebook built GraphQL in 2012 for their mobile app (slow networks, millions of users). One endpoint, one request:

```graphql
query {
  user(id: 42) {
    name
    avatar
    posts(limit: 5) {
      title
      likeCount
      comments { text }
    }
  }
}
```

One network trip. You get exactly what you asked for — no extra fields, no missing data.

### When to Choose Each

| Situation | Choose |
|-----------|--------|
| Simple CRUD app or public API | REST |
| Mobile app on limited bandwidth | GraphQL |
| Multiple clients needing different shapes | GraphQL |
| Team already knows REST | REST |
| Real-time updates | GraphQL Subscriptions |

### The Hidden GraphQL Cost

GraphQL shifts complexity to the backend. Every client can craft any query — so you need query depth limits, complexity analysis, and per-field authorization to prevent abuse. A nested query like `user -> friends -> friends -> posts` could be infinitely deep without guards.

---

## Real-World Analogy - The Restaurant Menu Analogy

Imagine two restaurants:

**REST restaurant** has a fixed menu. Order "Meal Set A" and you receive soup, bread, salad, main, and dessert — whether you want all of it or not. Simple kitchen, predictable output. But you always get the full set even if you just wanted soup.

**GraphQL restaurant** takes custom orders. "I want just the soup and the chocolate cake." The kitchen assembles exactly what you asked for — no leftovers, no missing items.

REST = fixed meal sets (every endpoint has a fixed response shape).
GraphQL = build-your-own plate (each client defines its own response shape).

Most companies start with REST and only add GraphQL when clients on different platforms (web, mobile, TV apps) start complaining that the fixed sets don't fit their specific needs.

---

## Key Concepts

- **REST Endpoints**
- **GraphQL Queries**
- **Over-fetching**
- **Under-fetching**
- **Schema**
- **Resolvers**
- **Mutations**
- **Subscriptions**

---

## JavaScript Example

```javascript
// REST: multiple round trips + over-fetching
async function profileREST(userId) {
  const user     = await fetch(`/api/users/${userId}`).then(r => r.json());
  // Problem: user has 15 fields, we needed 3
  const posts    = await fetch(`/api/users/${userId}/posts`).then(r => r.json());
  const comments = await fetch(`/api/posts/${posts[0].id}/comments`).then(r => r.json());
  console.log(`Over-fetched: ${Object.keys(user).length} user fields for 3 needed`);
  return { name: user.name, posts, comments };
}

// GraphQL: one trip, exact fields
async function profileGraphQL(userId) {
  const query = `
    query($id: ID!) {
      user(id: $id) {
        name
        avatar
        posts(limit: 5) {
          title
          likeCount
          comments { text }
        }
      }
    }
  `;
  const { data } = await fetch('/graphql', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ query, variables: { id: userId } })
  }).then(r => r.json());
  return data.user;
}

// Simple GraphQL server with Apollo
const { ApolloServer, gql } = require('apollo-server');
const typeDefs = gql`
  type User { id: ID!, name: String!, posts: [Post!]! }
  type Post  { id: ID!, title: String!, likeCount: Int! }
  type Query { user(id: ID!): User }
  type Mutation { createPost(title: String!, authorId: ID!): Post! }
`;
const resolvers = {
  Query:    { user: (_, { id }) => ({ id, name: 'Priya' }) },
  User:     { posts: () => [{ id: 1, title: 'My Post', likeCount: 42 }] },
  Mutation: { createPost: (_, { title }) => ({ id: 99, title, likeCount: 0 }) }
};
new ApolloServer({ typeDefs, resolvers }).listen()
  .then(({ url }) => console.log(`GraphQL at ${url}`));
```

---

## Python Example

```python
import json

DB_USERS = {
    1: {'id':1,'name':'Priya','city':'Mumbai','age':15,
        'bio':'Student','phone':'111','pref':{},'joined':'2024'},
}
DB_POSTS = {1: [{'id':10,'title':'System Design','body':'...','likes':5}]}

# REST - returns ALL fields (over-fetching)
def rest_get_user(uid):
    user = DB_USERS.get(uid, {})
    print(f"REST returned {len(user)} fields (caller needed 2)")
    return user

def rest_get_posts(uid):
    return DB_POSTS.get(uid, [])

def build_profile_rest(uid):
    print("REST: 2 separate requests...")
    user  = rest_get_user(uid)
    posts = rest_get_posts(uid)
    return {'name': user['name'], 'city': user['city'], 'posts': posts}

# GraphQL - returns ONLY requested fields
def graphql_resolve(fields: dict, uid: int) -> dict:
    user = DB_USERS.get(uid, {})
    result = {}
    for field, spec in fields.items():
        if field == 'posts':
            raw = DB_POSTS.get(uid, [])
            result['posts'] = [{k: p[k] for k in spec if k in p} for p in raw]
        elif field in user:
            result[field] = user[field]
    print(f"GraphQL returned exactly {len(result)} requested fields")
    return result

print("=== REST ===")
print(json.dumps(build_profile_rest(1), indent=2))

print("\n=== GraphQL ===")
profile = graphql_resolve({'name': True, 'city': True, 'posts': ['title','likes']}, 1)
print(json.dumps(profile, indent=2))
```

---

## Homework

1. Design REST endpoints for a blog, then rewrite as a GraphQL schema with queries and mutations
2. Query your GitHub profile via both api.github.com (REST) and api.github.com/graphql — compare field counts
3. Research: what is the N+1 query problem in GraphQL? Write a code example that triggers it
4. When would you NOT choose GraphQL? List 3 scenarios with clear reasoning

---

## Resources

- GraphQL official docs - graphql.org/learn
- How to GraphQL (free full course) - howtographql.com
- REST API Design Best Practices - restfulapi.net
- GraphQL vs REST - Apollo Blog - apollographql.com/blog

---

*<- [Day 03](day_03.md) | [Index](README.md) | [Day 05](day_05.md) ->*
