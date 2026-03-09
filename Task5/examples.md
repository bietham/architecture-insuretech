# Task5 — Примеры GraphQL-запросов

## 1) Эквивалент GET /clients/{id}
```graphql
query GetClient($id: ID!) {
  client(id: $id) {
    id
    name
    age
  }
}
```

## 2) Эквивалент GET /clients/{id}/documents
```graphql
query GetClientDocuments($id: ID!) {
  client(id: $id) {
    id
    documents {
      id
      type
      number
      issueDate
      expiryDate
    }
  }
}
```

## 3) Эквивалент GET /clients/{id}/relatives
```graphql
query GetClientRelatives($id: ID!) {
  client(id: $id) {
    id
    relatives {
      id
      relationType
      name
      age
    }
  }
}
```

## 4) Один запрос вместо N REST-вызовов (пример сценария)
```graphql
query SalesScenario($id: ID!) {
  client(id: $id) {
    id
    name
    documents { type number }
    relatives { relationType name }
  }
}
```
