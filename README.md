# Front End Best Practices Manual

## Introduction

This document aims to outline recommended patterns for Next.js applications, focusing on maintaining a scalable folder structure, organizing components cleanly, handling application state management, and sharing data between components. It also includes a guide to best practices for coding in React.


In the realm of modern web development, efficiency, scalability, and maintainability are key to the success of any project. Adopting a well-organized folder structure from the onset not only streamlines the development process but also enhances collaboration among team members by providing a clear and intuitive layout of the codebase. This guide presents a recommended folder structure tailored for Next.js applications utilizing GraphQL, TypeScript, and TailwindCSS—a combination that offers robust backend capabilities, strong typing for safer code refactoring and error checks, and a utility-first CSS framework for rapid UI development.

## Recommended Folder Structure for a Next.js Application

The suggested structure is designed to accommodate the intricacies of building a scalable Next.js application, taking into consideration the need for an organized approach to manage components, styles, utilities, and more. This setup ensures that your project is not only easy to navigate but also adaptable to the evolving needs of the project.

```
your-nextjs-tailwind-ts-app/
│
├── pages/               # Core page components, leveraging the file-based routing of Next.js
│   ├── index.tsx        # Home page
│   ├── about.tsx        # About page
│   └── _app.tsx         # Custom App component for initializing pages, includes TailwindCSS import
│
├── public/              # Static files like images, fonts, and favicon, served at the root path
│   ├── images/          # Image files
│   ├── fonts/           # Font files
│   └── favicon.ico      # Favicon
│
├── components/          # Reusable UI components, categorized for better organization
│   ├── common/          # Common components like Header, Footer
│   │   ├── Header.tsx
│   │   └── Footer.tsx
│   ├── layout/          # Layout components for page structure
│   └── ui/              # Atomic UI components like buttons, cards, styled with Tailwind
│
├── hooks/               # Custom React hooks for shared logic across components
│
├── utils/               # Utility functions and helpers that provide common functionalities
│
├── services/            # Service layer for external API calls, encapsulating the business logic
│
├── context/             # Context API files for global state management across components
│
├── graphql/             # GraphQL queries and mutations, centralized for better manageability
│
├── lib/                 # Library code specific to the project, such as custom hooks or utilities
│
├── styles/              # Global stylesheets and TailwindCSS customizations
│   └── globals.css      # Global styles, including the import of TailwindCSS directives
│
├── types/               # TypeScript type definitions and interfaces for consistent typing
│
├── tailwind.config.js   # Custom TailwindCSS configuration for theme, plugins, and variants
├── postcss.config.js    # PostCSS configuration for processing TailwindCSS and other transformations
│
├── tsconfig.json        # TypeScript compiler configuration for strict typing rules and paths
│
├── .env.local           # Local environment variables for development
├── .env.production      # Production environment variables for deployment
├── package.json         # Package manifest for dependencies and scripts
├── next.config.js       # Next.js configuration for custom settings and optimizations
└── README.md            # Project documentation for setup, usage, and contribution guidelines
```

This meticulously crafted structure aims to provide a solid foundation for your Next.js application, where every file and folder has a specific purpose, and growth can be managed elegantly. By adopting this structure, you not only benefit from the modular and scalable architecture but also ensure that your project remains easy to understand and navigate, both for you and for any developer who might join your project in the future.

## Clean Code Best Practices for TypeScript

### 1. Variables

#### Use meaningful and pronounceable variable names

**Bad:**

```typescript
const ydm = new Date();
```

**Good:**

```typescript
const currentDate = new Date();
```

#### Use the same vocabulary for the same type of variable

**Bad:**

```typescript
function getUserFromDatabase() { /* ... */ }
function fetchUserRecord() { /* ... */ }
```

**Good:**

```typescript
function getUser() { /* ... */ }
```

#### Use searchable names and avoid magic numbers

**Bad:**

```typescript
setTimeout(() => {
  // operation
}, 86400000);
```

**Good:**

```typescript
const MILLISECONDS_IN_A_DAY = 86400000;

setTimeout(() => {
  // operation
}, MILLISECONDS_IN_A_DAY);
```

### 2. Functions

#### Function arguments (2 or fewer ideally)

**Bad:**

```typescript
function createUser(firstName: string, lastName: string, email: string, age: number) { /* ... */ }
```

**Good:**

```typescript
type CreateUserParams = {
  firstName: string;
  lastName: string;
  email: string;
  age: number;
};

function createUser({ firstName, lastName, email, age }: CreateUserParams) { /* ... */ }
```

#### Functions should do one thing

**Bad:**

```typescript
function handleUserRequest(request: Request) {
  // validate request
  // get user data from request
  // save user to database
  // send response
}
```

**Good:**

```typescript
function validateRequest(request: Request) { /* ... */ }
function getUserDataFromRequest(request: Request) { /* ... */ }
function saveUserToDatabase(user: User) { /* ... */ }
function sendResponse(response: Response) { /* ... */ }

async function handleUserRequest(request: Request) {
  validateRequest(request);
  const userData = getUserDataFromRequest(request);
  await saveUserToDatabase(userData);
  sendResponse("User saved");
}
```

### 3. Classes & Interfaces

#### Prefer composition over inheritance

**Bad:**

```typescript
class BasePage {
  initializePage() { /* Common initialization logic */ }
}

class HomePage extends BasePage {
  // HomePage specific logic
}

class AboutPage extends BasePage {
  // AboutPage specific logic
}
```

**Good:**

```typescript
function usePageInitialization() {
  // Hook for common initialization logic
}

function HomePage() {
  usePageInitialization();
  // HomePage specific logic
}

function AboutPage() {
  usePageInitialization();
  // AboutPage specific logic
}
```

#### Classes should be small and focused on a single responsibility

**Bad:**

```typescript
class UserHandler {
  validateUser(user: User) { /* ... */ }
  saveUser(user: User) { /* ... */ }
  sendEmailToUser(user: User) { /* ... */ }
}
```

**Good:**

```typescript
class UserValidator {
  validate(user: User) { /* ... */ }
}

class UserSaver {
  save(user: User) { /* ... */ }
}

class UserEmailer {
  sendEmail(user: User) { /* ... */ }
}
```

#### Use interfaces for defining complex types

**Good:**

```typescript
interface User {
  id: string;
  name: string;
  email: string;
}

function getUser(): User {
  // implementation
}
```

### 4. Concurrency

#### Prefer Promises and Async/Await over callbacks

**Good:**

```typescript
async function fetchUserData(userId: string): Promise<User> {
  const response = await fetch(`/api/users/${userId}`);
  const data = await response.json();
  return data;
}
```

### 5. Error Handling

#### Always use instances of `Error` for throwing or rejecting

```typescript
function calculateTotal(items: Item[]): number {
  if (items.length === 0) {
    throw new Error('No items provided');
  }

  // calculation logic
}
```

### 6. Formatting & Comments

#### Use Prettier and ESLint with TypeScript support for consistent formatting

_Set up Prettier and ESLint configuration files in your project root._

#### Use meaningful comments and avoid commented-out code

```typescript
// Adjusting time zone for user's locale
const userTime = currentTime.toLocaleTimeString('en-us', { timeZone: 'America/New_York' });
```

## Clean Code Best Practices for TailwindCSS

Incorporating TailwindCSS into your Next.js project not only speeds up the development process with its utility-first approach but also demands adherence to certain best practices for maintainability and scalability. Here are some TailwindCSS best practices, particularly focusing on how to manage classes effectively in a React project with tools like `classnames`, `tailwind-merge` (or `tw-merge`), and `cva` for a cleaner, more maintainable codebase.

### TailwindCSS Best Practices

#### 1. Using `classnames` for Conditional Classes

The `classnames` library is a simple yet powerful tool for conditionally joining classNames together. It's particularly useful in React projects where you need to dynamically apply classes based on component state or props.

**Example without `classnames`:**

```jsx
function Alert({ children, type }) {
  return (
    <div className={`p-4 ${type === 'error' ? 'bg-red-500' : 'bg-green-500'}`}>
      {children}
    </div>
  );
}
```

**Example with `classnames`:**

```jsx
import cn from 'classnames';

function Alert({ children, type }) {
  return (
    <div className={cn('p-4', {
      'bg-red-500': type === 'error',
      'bg-green-500': type === 'success'
    })}>
      {children}
    </div>
  );
}
```

#### 2. Combining Classes with `tailwind-merge` (or `tw-merge`)

`tailwind-merge` is a utility designed to merge TailwindCSS classes intelligently, avoiding duplicate classes and resolving conflicts between utility classes that affect the same CSS property.

**Example:**

```jsx
import twMerge from 'tailwind-merge';

function Button({ primary, children }) {
  const buttonClass = twMerge(
    'px-4 py-2 rounded focus:outline-none',
    primary ? 'bg-blue-500 text-white' : 'bg-gray-200 text-black'
  );

  return (
    <button className={buttonClass}>
      {children}
    </button>
  );
}
```

#### 3. Creating Variant Props with `cva`

`cva` (Class Variant API) is particularly useful for component libraries or design systems where you want to expose a consistent API for component variants.

**Example:**

```jsx
import { cva } from 'cva';

const buttonVariants = cva([
  'px-4 py-2 rounded focus:outline-none transition-colors',
  {
    variants: {
      color: {
        blue: 'text-white bg-blue-500 hover:bg-blue-600',
        red: 'text-white bg-red-500 hover:bg-red-600',
      },
      size: {
        small: 'text-xs',
        large: 'text-lg',
      },
    },
  },
]);

function Button({ variant, size, children }) {
  return (
    <button className={buttonVariants({ color: variant, size })}>
      {children}
    </button>
  );
}
```

### General Tips

- **Avoid excessive class strings**: Use tools like `@apply` in your CSS, `classnames`, `tailwind-merge`, and `cva` to manage complex class combinations and keep your JSX clean.

- **Customize your Tailwind configuration**: TailwindCSS is highly customizable. Tailor your `tailwind.config.js` to match your design system's needs, which can significantly reduce the need for custom CSS.

- **Leverage Tailwind Plugins**: Explore TailwindCSS plugins that can add functionality or pre-designed components to your project without bloating your code with additional classes.

- **Stay consistent**: Whether you're working alone or in a team, agree on conventions for using TailwindCSS in your project. Consistency in how classes are applied and managed goes a long way in maintaining a clean codebase.

By incorporating these best practices and tools into your Next.js and TailwindCSS workflow, you'll enjoy the rapid development pace that TailwindCSS offers while keeping your codebase organized and maintainable.

Incorporating clean code principles into your GraphQL usage within a Next.js project not only enhances the readability and maintainability of your code but also improves development efficiency. GraphQL, with its powerful data querying capabilities, can become complex and hard to manage without a disciplined approach. Here's how you can apply clean code principles to your GraphQL integrations.

## Clean Code for GraphQL

#### 1. Meaningful Query and Mutation Names

Naming is crucial in making your GraphQL queries and mutations understandable at a glance. Use clear, descriptive names that reflect what the operation does.

**Bad:**

```graphql
query GetUserData {
  user {
    id
    name
    email
  }
}
```

**Good:**

```graphql
query FetchCurrentUserProfile {
  currentUser {
    id
    name
    email
  }
}
```

#### 2. Use Fragments for Reusable Fields

GraphQL fragments allow you to construct sets of fields that you can include in multiple queries or mutations. This practice reduces duplication and keeps your queries organized.

**Bad:**

```graphql
query GetUser {
  user {
    id
    name
    email
    avatarUrl
  }
}

query GetUsers {
  users {
    id
    name
    email
    avatarUrl
  }
}
```

**Good:**

```graphql
fragment UserProfile on User {
  id
  name
  email
  avatarUrl
}

query GetUser {
  user {
    ...UserProfile
  }
}

query GetUsers {
  users {
    ...UserProfile
  }
}
```

#### 3. Specific Queries Over Generic Ones

Design your queries to fetch only the data that is needed. Avoid overly generic queries that return more information than necessary, as this can lead to performance issues and overfetching.

**Bad:**

```graphql
query GetUsers {
  users {
    id
    name
    email
    posts {
      id
      title
      content
    }
  }
}
```

**Good:**

```graphql
query GetUsersBasicInfo {
  users {
    id
    name
    email
  }
}
```

#### 4. Naming Conventions for Operations

Adopt a consistent naming convention for your queries and mutations to make them easily identifiable. For example, use prefixes like `fetch`, `update`, `create`, and `delete` to denote the operation's purpose.

**Example:**

```graphql
query FetchUserById($id: ID!) {
  user(id: $id) {
    ...UserProfile
  }
}

mutation UpdateUserProfile($id: ID!, $input: UserProfileInput!) {
  updateUser(id: $id, input: $input) {
    ...UserProfile
  }
}
```

#### 5. Organizing Schema Definitions

Keep your schema definitions organized and readable. Group related types and use comments to describe complex types or fields.

**Example:**

```graphql
"""
Represents a user of the application with fields commonly accessed in user profiles.
"""
type User {
  id: ID!
  name: String!
  email: String!
  avatarUrl: String
  # Other fields...
}

# Queries related to users
type Query {
  currentUser: User
  user(id: ID!): User
  users: [User!]
}

# Mutations related to users
type Mutation {
  updateUser(id: ID!, input: UserProfileInput!): User
}
```

#### 6. Error Handling in GraphQL Operations

Design your GraphQL schema to properly communicate errors to the client. Utilize the `errors` field in GraphQL responses, and consider defining error types in your schema for more complex error handling.

**Example:**

```graphql
type MutationResponse {
  code: String!
  success: Boolean!
  message: String!
}
```

By following these clean code principles for GraphQL, you'll ensure that your Next.js application's data layer is efficient, easy to understand, and maintain. These practices not only improve your development workflow but also enhance the experience for consumers of your GraphQL API by providing clear, efficient, and predictable data access patterns.
