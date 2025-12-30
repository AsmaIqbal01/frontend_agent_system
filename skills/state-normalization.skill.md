# State Normalization Skill

## Skill Identity
- **Name**: State Normalization Skill
- **Type**: Data Architecture Skill
- **Domain**: State Management, Data Modeling
- **Version**: 1.0.0
- **Responsibility**: Transform nested/duplicated data into flat, normalized structures

---

## Purpose

This skill analyzes data structures and transforms them from nested, denormalized formats into flat, normalized structures using the `byId` + `allIds` pattern. It eliminates data duplication, simplifies updates, prevents inconsistencies, and improves performance.

---

## When to Use This Skill

Use this skill when:
- ✅ Data has nested relationships (users with posts, posts with comments)
- ✅ Same entity appears in multiple places (duplication risk)
- ✅ Need to update entity in one place and reflect everywhere
- ✅ Rendering lists of entities with relationships
- ✅ Building complex data models (e.g., social feed, e-commerce catalog, project management)
- ✅ State updates are slow due to deep nesting

Do NOT use this skill for:
- ❌ Simple, flat data (already normalized)
- ❌ Data with no relationships or duplication
- ❌ Temporary UI state (form inputs, modals open/closed)
- ❌ Derived/computed values (calculate on-the-fly instead)

---

## Inputs

### Required Inputs
1. **Denormalized Data Structure**: The nested/duplicated data to normalize
2. **Entity Relationships**: How entities relate to each other
3. **Primary Keys**: Unique identifier for each entity type (usually `id`)

### Optional Inputs
4. **Update Patterns**: How data will be updated (helps design selectors)
5. **Query Patterns**: How data will be accessed (affects index structure)

---

## Outputs

### Primary Outputs
1. **Normalized Schema**: Flat structure with `entities.byId` and `entities.allIds`
2. **Entity Relationships**: Foreign key references between entities
3. **Selector Functions**: How to reconstruct nested data when needed

### Secondary Outputs
4. **Normalization Function**: Code to transform API response → normalized state
5. **Denormalization Function**: Code to transform normalized state → UI-ready data
6. **Update Patterns**: How to update normalized entities efficiently

---

## Core Concept: Normalization

### What is Normalization?

**Before (Denormalized)**:
```javascript
// Nested structure with duplication
const posts = [
  {
    id: 1,
    title: "Post 1",
    author: { id: 101, name: "Alice", avatar: "..." },
    comments: [
      {
        id: 201,
        text: "Great post!",
        author: { id: 102, name: "Bob", avatar: "..." }
      },
      {
        id: 202,
        text: "Thanks for sharing",
        author: { id: 101, name: "Alice", avatar: "..." } // DUPLICATE
      }
    ]
  },
  {
    id: 2,
    title: "Post 2",
    author: { id: 101, name: "Alice", avatar: "..." }, // DUPLICATE
    comments: []
  }
]
```

**Problems**:
1. **Duplication**: Alice's data repeated 3 times
2. **Inconsistency Risk**: If Alice updates avatar, must update 3 places
3. **Deep Nesting**: Hard to access/update specific entity
4. **Performance**: Updating nested data causes unnecessary re-renders

**After (Normalized)**:
```javascript
// Flat structure with references
const state = {
  posts: {
    byId: {
      1: { id: 1, title: "Post 1", authorId: 101, commentIds: [201, 202] },
      2: { id: 2, title: "Post 2", authorId: 101, commentIds: [] }
    },
    allIds: [1, 2]
  },
  users: {
    byId: {
      101: { id: 101, name: "Alice", avatar: "..." },
      102: { id: 102, name: "Bob", avatar: "..." }
    },
    allIds: [101, 102]
  },
  comments: {
    byId: {
      201: { id: 201, text: "Great post!", authorId: 102, postId: 1 },
      202: { id: 202, text: "Thanks for sharing", authorId: 101, postId: 1 }
    },
    allIds: [201, 202]
  }
}
```

**Benefits**:
1. **No Duplication**: Alice's data stored once
2. **Consistent Updates**: Update user 101 once, reflects everywhere
3. **Flat Access**: `state.users.byId[101]` - direct access
4. **Efficient Updates**: Update single entity without touching others

---

## Decision-Making Criteria

### 1. Should This Data Be Normalized?

**Decision Tree**:
```
Is the data nested with relationships?
├─ No → Does the same entity appear multiple times?
│   ├─ No → ❌ Don't normalize (already flat, no duplication)
│   └─ Yes → ✅ Normalize (eliminate duplication)
└─ Yes → Will you need to update entities independently?
    ├─ No → Consider keeping nested if read-only
    └─ Yes → ✅ Normalize (easier updates)
```

**Examples**:

**✅ Normalize These**:
- Social media feed (posts with authors, comments, likes)
- E-commerce catalog (products with categories, reviews, related products)
- Project management (projects with tasks, assignees, comments)
- Messaging app (conversations with messages, participants)
- Organization chart (employees with managers, departments)

**❌ Don't Normalize These**:
- Form inputs (`{ email: "", password: "" }`) - no relationships
- UI state (`{ sidebarOpen: true, theme: "dark" }`) - no entities
- Single-level lists (`[{ id: 1, name: "Item 1" }]`) - already flat
- Static config (`{ apiUrl: "...", timeout: 5000 }`) - no updates

---

### 2. What Are the Entities?

**Question**: What are the distinct "things" in the data?

**Identification Process**:
1. Look for objects with unique IDs
2. Look for objects that appear multiple times
3. Look for objects that can be updated independently

**Example - Blog Platform**:

API Response:
```javascript
{
  id: 1,
  title: "My Blog Post",
  author: { id: 101, name: "Alice" },
  category: { id: 5, name: "Tech" },
  tags: [
    { id: 10, name: "JavaScript" },
    { id: 11, name: "React" }
  ],
  comments: [
    { id: 201, text: "...", author: { id: 102, name: "Bob" } }
  ]
}
```

**Identified Entities**:
1. **Post** (id: 1)
2. **User** (id: 101, 102) - appears as author in post and comment
3. **Category** (id: 5)
4. **Tag** (id: 10, 11)
5. **Comment** (id: 201)

**Normalized Structure**:
```javascript
{
  posts: { byId: {...}, allIds: [...] },
  users: { byId: {...}, allIds: [...] },
  categories: { byId: {...}, allIds: [...] },
  tags: { byId: {...}, allIds: [...] },
  comments: { byId: {...}, allIds: [...] }
}
```

---

### 3. What Are the Relationships?

**Relationship Types**:

**One-to-One**:
- Post has one author
- Store foreign key: `{ authorId: 101 }`

**One-to-Many**:
- Post has many comments
- Store array of IDs: `{ commentIds: [201, 202, 203] }`

**Many-to-Many**:
- Post has many tags, tag has many posts
- Store arrays on both sides:
  - Post: `{ tagIds: [10, 11] }`
  - Tag: `{ postIds: [1, 2, 3] }` (optional, if needed)

**Example - E-commerce**:
```javascript
// Product
{
  id: 1,
  name: "Laptop",
  categoryId: 5,           // One-to-One: product belongs to one category
  reviewIds: [101, 102],   // One-to-Many: product has many reviews
  relatedIds: [2, 3, 4]    // Many-to-Many: product related to many products
}

// Category
{
  id: 5,
  name: "Electronics",
  productIds: [1, 2, 3]    // One-to-Many: category has many products
}

// Review
{
  id: 101,
  rating: 5,
  text: "Great!",
  productId: 1,            // Many-to-One: review belongs to one product
  authorId: 201            // Many-to-One: review has one author
}
```

---

### 4. Bi-directional vs Uni-directional References?

**Question**: Should relationships be stored on one side or both?

**Uni-directional** (One side only):
- Simpler to maintain
- Updates only happen in one place
- Traverse in one direction only

**Bi-directional** (Both sides):
- Easier to query in both directions
- Must keep both sides in sync
- More complex updates

**Decision Matrix**:

| Access Pattern | Recommendation |
|----------------|----------------|
| Only need "post.comments" | **Uni-directional**: Store `commentIds` on post |
| Only need "comment.post" | **Uni-directional**: Store `postId` on comment |
| Need both directions frequently | **Bi-directional**: Store both `commentIds` and `postId` |
| Performance-critical queries | **Bi-directional**: Avoid lookups |

**Example**:
```javascript
// Uni-directional: Post → Comments
posts.byId[1] = { commentIds: [201, 202] }
comments.byId[201] = { postId: 1 }
comments.byId[202] = { postId: 1 }

// To get post's comments: easy
const comments = post.commentIds.map(id => state.comments.byId[id])

// To get comment's post: easy
const post = state.posts.byId[comment.postId]

// To get all comments for a post: already have commentIds ✅
// To find all posts by a user: need to scan all posts ❌

// Bi-directional: Add postIds to user if needed
users.byId[101] = { postIds: [1, 2] }
posts.byId[1] = { authorId: 101 }
```

---

## Normalization Patterns

### Pattern 1: API Response Normalization

**Use When**: Transforming nested API response to normalized state

**Process**:
1. Identify all entity types in response
2. Create `byId` and `allIds` for each entity
3. Replace nested objects with ID references
4. Merge into existing state

**Example - Fetch User with Posts**:

**API Response**:
```javascript
{
  id: 101,
  name: "Alice",
  email: "alice@example.com",
  posts: [
    {
      id: 1,
      title: "First Post",
      content: "...",
      comments: [
        { id: 201, text: "Great!", authorId: 102 }
      ]
    },
    {
      id: 2,
      title: "Second Post",
      content: "...",
      comments: []
    }
  ]
}
```

**Normalization Function**:
```javascript
function normalizeUser(apiResponse) {
  const users = { byId: {}, allIds: [] }
  const posts = { byId: {}, allIds: [] }
  const comments = { byId: {}, allIds: [] }

  // Extract user
  users.byId[apiResponse.id] = {
    id: apiResponse.id,
    name: apiResponse.name,
    email: apiResponse.email,
    postIds: apiResponse.posts.map(p => p.id)
  }
  users.allIds.push(apiResponse.id)

  // Extract posts
  apiResponse.posts.forEach(post => {
    posts.byId[post.id] = {
      id: post.id,
      title: post.title,
      content: post.content,
      authorId: apiResponse.id,
      commentIds: post.comments.map(c => c.id)
    }
    posts.allIds.push(post.id)

    // Extract comments
    post.comments.forEach(comment => {
      comments.byId[comment.id] = {
        ...comment,
        postId: post.id
      }
      comments.allIds.push(comment.id)
    })
  })

  return { users, posts, comments }
}

// Usage
const normalized = normalizeUser(apiResponse)
// Merge into state:
state.users = mergeEntities(state.users, normalized.users)
state.posts = mergeEntities(state.posts, normalized.posts)
state.comments = mergeEntities(state.comments, normalized.comments)
```

**Result**:
```javascript
{
  users: {
    byId: {
      101: {
        id: 101,
        name: "Alice",
        email: "alice@example.com",
        postIds: [1, 2]
      }
    },
    allIds: [101]
  },
  posts: {
    byId: {
      1: {
        id: 1,
        title: "First Post",
        content: "...",
        authorId: 101,
        commentIds: [201]
      },
      2: {
        id: 2,
        title: "Second Post",
        content: "...",
        authorId: 101,
        commentIds: []
      }
    },
    allIds: [1, 2]
  },
  comments: {
    byId: {
      201: {
        id: 201,
        text: "Great!",
        authorId: 102,
        postId: 1
      }
    },
    allIds: [201]
  }
}
```

---

### Pattern 2: Update Operations

**Use When**: Updating, adding, or deleting entities

**Add Entity**:
```javascript
// Add new post
function addPost(state, post) {
  return {
    ...state,
    posts: {
      byId: {
        ...state.posts.byId,
        [post.id]: post
      },
      allIds: [...state.posts.allIds, post.id]
    }
  }
}
```

**Update Entity**:
```javascript
// Update post title
function updatePost(state, postId, updates) {
  return {
    ...state,
    posts: {
      ...state.posts,
      byId: {
        ...state.posts.byId,
        [postId]: {
          ...state.posts.byId[postId],
          ...updates
        }
      }
    }
  }
}
```

**Delete Entity**:
```javascript
// Delete post
function deletePost(state, postId) {
  const { [postId]: deleted, ...remainingPosts } = state.posts.byId

  return {
    ...state,
    posts: {
      byId: remainingPosts,
      allIds: state.posts.allIds.filter(id => id !== postId)
    }
  }
}
```

**Update Relationship** (Add comment to post):
```javascript
function addCommentToPost(state, postId, comment) {
  return {
    ...state,
    // Add comment entity
    comments: {
      byId: {
        ...state.comments.byId,
        [comment.id]: { ...comment, postId }
      },
      allIds: [...state.comments.allIds, comment.id]
    },
    // Update post's commentIds
    posts: {
      ...state.posts,
      byId: {
        ...state.posts.byId,
        [postId]: {
          ...state.posts.byId[postId],
          commentIds: [
            ...state.posts.byId[postId].commentIds,
            comment.id
          ]
        }
      }
    }
  }
}
```

---

### Pattern 3: Selector Functions (Denormalization)

**Use When**: Reconstructing nested data for UI from normalized state

**Simple Selector** (Get entity by ID):
```javascript
function getPostById(state, postId) {
  return state.posts.byId[postId]
}
```

**Relationship Selector** (Get post with author):
```javascript
function getPostWithAuthor(state, postId) {
  const post = state.posts.byId[postId]
  const author = state.users.byId[post.authorId]

  return {
    ...post,
    author
  }
}
```

**Collection Selector** (Get all posts with authors):
```javascript
function getAllPostsWithAuthors(state) {
  return state.posts.allIds.map(postId => {
    const post = state.posts.byId[postId]
    const author = state.users.byId[post.authorId]

    return {
      ...post,
      author
    }
  })
}
```

**Deep Nesting Selector** (Get post with author and comments with authors):
```javascript
function getPostWithDetails(state, postId) {
  const post = state.posts.byId[postId]
  const author = state.users.byId[post.authorId]
  const comments = post.commentIds.map(commentId => {
    const comment = state.comments.byId[commentId]
    const commentAuthor = state.users.byId[comment.authorId]

    return {
      ...comment,
      author: commentAuthor
    }
  })

  return {
    ...post,
    author,
    comments
  }
}
```

**Memoized Selector** (Performance optimization):
```javascript
import { createSelector } from 'reselect' // or similar

const getPostsById = state => state.posts.byId
const getUsersById = state => state.users.byId
const getAllPostIds = state => state.posts.allIds

const getAllPostsWithAuthors = createSelector(
  [getPostsById, getUsersById, getAllPostIds],
  (postsById, usersById, allPostIds) => {
    return allPostIds.map(id => ({
      ...postsById[id],
      author: usersById[postsById[id].authorId]
    }))
  }
)
// Only recalculates if posts or users change
```

---

### Pattern 4: Filtering and Sorting

**Use When**: Need to filter/sort entities efficiently

**Filter by Property**:
```javascript
// Get all published posts
function getPublishedPosts(state) {
  return state.posts.allIds
    .map(id => state.posts.byId[id])
    .filter(post => post.published === true)
}
```

**Filter by Relationship**:
```javascript
// Get all posts by a specific author
function getPostsByAuthor(state, authorId) {
  return state.posts.allIds
    .map(id => state.posts.byId[id])
    .filter(post => post.authorId === authorId)
}

// Optimized: Store postIds on user (bi-directional reference)
function getPostsByAuthorOptimized(state, authorId) {
  const user = state.users.byId[authorId]
  return user.postIds.map(id => state.posts.byId[id])
}
```

**Sort Entities**:
```javascript
// Get posts sorted by date
function getPostsSortedByDate(state) {
  return state.posts.allIds
    .map(id => state.posts.byId[id])
    .sort((a, b) => new Date(b.createdAt) - new Date(a.createdAt))
}
```

**Complex Query** (Filter + Sort + Populate):
```javascript
// Get published posts by author, sorted by date, with author details
function getPublishedPostsByAuthor(state, authorId) {
  return state.posts.allIds
    .map(id => state.posts.byId[id])
    .filter(post => post.authorId === authorId && post.published)
    .sort((a, b) => new Date(b.createdAt) - new Date(a.createdAt))
    .map(post => ({
      ...post,
      author: state.users.byId[post.authorId]
    }))
}
```

---

### Pattern 5: Pagination with Normalized State

**Use When**: Handling paginated data

**Structure**:
```javascript
{
  posts: {
    byId: { 1: {...}, 2: {...}, ... },
    allIds: [1, 2, 3, ...],  // All fetched posts
    pages: {
      1: [1, 2, 3, 4, 5],    // Post IDs for page 1
      2: [6, 7, 8, 9, 10],   // Post IDs for page 2
      // ...
    },
    totalPages: 10,
    currentPage: 1
  }
}
```

**Fetch Page**:
```javascript
function fetchPostsPage(state, page, posts) {
  const normalized = normalizePosts(posts)

  return {
    ...state,
    posts: {
      byId: {
        ...state.posts.byId,
        ...normalized.byId
      },
      allIds: [
        ...state.posts.allIds,
        ...normalized.allIds.filter(id => !state.posts.allIds.includes(id))
      ],
      pages: {
        ...state.posts.pages,
        [page]: normalized.allIds
      },
      currentPage: page
    }
  }
}
```

**Get Page Posts**:
```javascript
function getPostsForPage(state, page) {
  const postIds = state.posts.pages[page] || []
  return postIds.map(id => state.posts.byId[id])
}
```

---

## Framework-Agnostic Implementation

### Redux
```javascript
// Reducer
const postsReducer = (state = { byId: {}, allIds: [] }, action) => {
  switch (action.type) {
    case 'ADD_POST':
      return {
        byId: { ...state.byId, [action.post.id]: action.post },
        allIds: [...state.allIds, action.post.id]
      }
    default:
      return state
  }
}
```

### Zustand
```javascript
const useStore = create((set) => ({
  posts: { byId: {}, allIds: [] },
  addPost: (post) => set((state) => ({
    posts: {
      byId: { ...state.posts.byId, [post.id]: post },
      allIds: [...state.posts.allIds, post.id]
    }
  }))
}))
```

### React Context
```javascript
const [state, setState] = useState({
  posts: { byId: {}, allIds: [] }
})

const addPost = (post) => {
  setState(prev => ({
    posts: {
      byId: { ...prev.posts.byId, [post.id]: post },
      allIds: [...prev.posts.allIds, post.id]
    }
  }))
}
```

### Vue (Pinia)
```javascript
export const usePostsStore = defineStore('posts', {
  state: () => ({
    byId: {},
    allIds: []
  }),
  actions: {
    addPost(post) {
      this.byId[post.id] = post
      this.allIds.push(post.id)
    }
  }
})
```

---

## Examples

### Example 1: Social Media Feed

**Input**: Nested feed data from API

**API Response**:
```javascript
[
  {
    id: 1,
    content: "Hello World!",
    author: { id: 101, name: "Alice", avatar: "a.jpg" },
    likes: [
      { id: 1001, userId: 102 },
      { id: 1002, userId: 103 }
    ],
    comments: [
      {
        id: 201,
        text: "Nice post!",
        author: { id: 102, name: "Bob", avatar: "b.jpg" }
      }
    ]
  }
]
```

**Normalized State**:
```javascript
{
  posts: {
    byId: {
      1: {
        id: 1,
        content: "Hello World!",
        authorId: 101,
        likeIds: [1001, 1002],
        commentIds: [201]
      }
    },
    allIds: [1]
  },
  users: {
    byId: {
      101: { id: 101, name: "Alice", avatar: "a.jpg" },
      102: { id: 102, name: "Bob", avatar: "b.jpg" }
    },
    allIds: [101, 102]
  },
  likes: {
    byId: {
      1001: { id: 1001, postId: 1, userId: 102 },
      1002: { id: 1002, postId: 1, userId: 103 }
    },
    allIds: [1001, 1002]
  },
  comments: {
    byId: {
      201: {
        id: 201,
        text: "Nice post!",
        postId: 1,
        authorId: 102
      }
    },
    allIds: [201]
  }
}
```

**Selector - Get Feed Post**:
```javascript
function getFeedPost(state, postId) {
  const post = state.posts.byId[postId]
  const author = state.users.byId[post.authorId]
  const likes = post.likeIds.map(id => state.likes.byId[id])
  const comments = post.commentIds.map(id => {
    const comment = state.comments.byId[id]
    return {
      ...comment,
      author: state.users.byId[comment.authorId]
    }
  })

  return { ...post, author, likes, comments }
}
```

---

### Example 2: E-commerce Product Catalog

**Input**: Products with categories, reviews, and related products

**Normalized State**:
```javascript
{
  products: {
    byId: {
      1: {
        id: 1,
        name: "Laptop",
        price: 999,
        categoryId: 5,
        reviewIds: [101, 102],
        relatedProductIds: [2, 3]
      },
      2: { id: 2, name: "Mouse", price: 29, categoryId: 6, reviewIds: [], relatedProductIds: [1, 3] }
    },
    allIds: [1, 2]
  },
  categories: {
    byId: {
      5: { id: 5, name: "Computers", productIds: [1] },
      6: { id: 6, name: "Accessories", productIds: [2] }
    },
    allIds: [5, 6]
  },
  reviews: {
    byId: {
      101: { id: 101, productId: 1, rating: 5, text: "Great!", authorId: 201 },
      102: { id: 102, productId: 1, rating: 4, text: "Good value", authorId: 202 }
    },
    allIds: [101, 102]
  },
  users: {
    byId: {
      201: { id: 201, name: "Customer 1" },
      202: { id: 202, name: "Customer 2" }
    },
    allIds: [201, 202]
  }
}
```

**Selector - Get Product Details**:
```javascript
function getProductWithDetails(state, productId) {
  const product = state.products.byId[productId]
  const category = state.categories.byId[product.categoryId]
  const reviews = product.reviewIds.map(id => ({
    ...state.reviews.byId[id],
    author: state.users.byId[state.reviews.byId[id].authorId]
  }))
  const relatedProducts = product.relatedProductIds.map(id =>
    state.products.byId[id]
  )

  return { ...product, category, reviews, relatedProducts }
}
```

---

## Quality Checks

### ✅ Well-Normalized State

**Indicators**:
- [ ] Each entity type has `byId` and `allIds` structure
- [ ] No duplicate data (same object stored in multiple places)
- [ ] Entities reference each other by ID, not nested objects
- [ ] Updates are simple (change one place, reflects everywhere)
- [ ] Direct access to any entity: `state.posts.byId[123]`
- [ ] Relationships are clear (foreign keys, ID arrays)
- [ ] No deep nesting (max 2 levels: `state.posts.byId[id].title`)

### ❌ Poor Normalization Indicators

**Warning Signs**:
- [ ] Same object appears in multiple places
- [ ] Deep nesting (3+ levels)
- [ ] Updating entity requires traversing nested structure
- [ ] Array.find() needed to access entity
- [ ] Inconsistent data (Alice's name is "Alice" in one place, "alice" in another)
- [ ] Performance issues when updating entities

---

## Success Criteria

This skill is successfully applied when:

1. ✅ **No Duplication**: Each entity stored exactly once
2. ✅ **Flat Structure**: Max 2-level nesting (`entities.byId[id]`)
3. ✅ **ID References**: Entities reference each other by ID
4. ✅ **Direct Access**: Can access any entity by ID in O(1) time
5. ✅ **Simple Updates**: Update entity in one place, no cascading changes
6. ✅ **Clear Relationships**: Foreign keys clearly show relationships
7. ✅ **Selector Functions**: Denormalization functions provided for UI
8. ✅ **Consistent Schema**: All entities follow same `byId` + `allIds` pattern
9. ✅ **Framework-Agnostic**: Pattern works with any state management solution
10. ✅ **Testable**: Normalization/denormalization functions are pure and testable

---

## Version History

- **1.0.0** (2024-01-15): Initial State Normalization Skill
  - Defined normalization concept and benefits
  - Created decision-making criteria for when to normalize
  - Documented 5 normalization patterns
  - Provided framework-agnostic implementations
  - Included 2 detailed examples (social media, e-commerce)
  - Established quality checks and success criteria
