
# Online Store Product Catalogue REST API

## Project Overview

This project is a RESTful API for managing an online store product catalogue.

Developed for CIT4242 Advanced Programming, Assignment 1.

## Technologies

- Java 21
- Spring Boot 3.5.16
- Maven
- Spring Web
- Spring Data JPA
- Hibernate
- PostgreSQL
- Jakarta Bean Validation

## Features

- Create products
- Retrieve all products
- Retrieve a product by ID
- Update existing products
- Delete products
- Filter products by category
- Search products by name
- Validate product information
- Handle errors consistently

## Project Structure

src/main/java/com/gaziza/onlinestore/
- controller/ - REST endpoints
- service/ - Business logic
- repository/ - Database operations
- entity/ - JPA entity
- dto/ - Request and response records
- exception/ - Custom exceptions and error handling

## Database Configuration

The application uses PostgreSQL.

Create a database and user:

```sql
CREATE DATABASE onlinestore;
CREATE USER onlinestore_user WITH PASSWORD 'your_password';
GRANT ALL PRIVILEGES ON DATABASE onlinestore TO onlinestore_user;
```

Connect to the database as an administrator and grant schema permissions:

```sql
GRANT USAGE, CREATE ON SCHEMA public TO onlinestore_user;
```

Configure `src/main/resources/application.properties`:

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/onlinestore
spring.datasource.username=onlinestore_user
spring.datasource.password=${DB_PASSWORD}
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
server.port=8080
```

The database password is provided through an environment variable.

## How to Run

Requirements:
- Java 21
- Maven
- PostgreSQL

Open a terminal in the project directory.

Set your PostgreSQL password:

```bash
export DB_PASSWORD="your_password"
```

Build the project:

```bash
mvn clean package
```

Start the application:

```bash
mvn spring-boot:run
```

The API is available at:

http://localhost:8080/api/products

## REST API Endpoints

### 1. Get All Products

GET /api/products

Example:

```bash
curl -i http://localhost:8080/api/products
```

Response: HTTP 200 OK

```json
[]
```

An empty list is returned if there are no products.

### 2. Get Product by ID

GET /api/products/{id}

```bash
curl -i http://localhost:8080/api/products/1
```

Response: HTTP 200 OK

```json
{
  "id": 1,
  "name": "Nike Running Shoes",
  "description": "Comfortable running shoes",
  "price": 89.99,
  "category": "Footwear",
  "inStock": true,
  "createdAt": "2026-11-09T01:29:40",
  "updatedAt": "2026-11-09T01:29:40"
}
```

Returns HTTP 404 if the product does not exist.

### 3. Create Product

POST /api/products

```bash
curl -i -X POST http://localhost:8080/api/products \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Nike Running Shoes",
    "description": "Comfortable running shoes",
    "price": 89.99,
    "category": "Footwear",
    "inStock": true
  }'
```

Response: HTTP 201 Created

A ProductResponse JSON object is returned with the
generated ID, timestamps, and other product fields.

The response also includes a Location header.

### 4. Update Product

PUT /api/products/{id}

```bash
curl -i -X PUT http://localhost:8080/api/products/1 \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Nike Air Zoom",
    "description": "Updated running shoes",
    "price": 119.99,
    "category": "Footwear",
    "inStock": true
  }'
```

Response: HTTP 200 OK

```json
{
  "id": 1,
  "name": "Nike Air Zoom",
  "description": "Updated running shoes",
  "price": 119.99,
  "category": "Footwear",
  "inStock": true,
  "createdAt": "2026-11-09T01:29:40",
  "updatedAt": "2026-11-09T01:40:32"
}
```

Returns HTTP 404 if the product does not exist.

### 5. Delete Product

DELETE /api/products/{id}

```bash
curl -i -X DELETE http://localhost:8080/api/products/1
```

Response: HTTP 204 No Content

No response body is returned.

Returns HTTP 404 if the product does not exist.

### 6. Filter by Category

GET /api/products/category/{category}

```bash
curl -i http://localhost:8080/api/products/category/Footwear
```

Response: HTTP 200 OK

Returns a JSON array of matching products.

### 7. Search by Name

GET /api/products/search?name=...

```bash
curl -i "http://localhost:8080/api/products/search?name=Nike"
```

Response: HTTP 200 OK

Returns a JSON array of matching products.

If nothing matches, the response is an empty array.

## Validation

Request DTOs validate product information:

- Name: required, 2–100 characters
- Description: optional, maximum 1000 characters
- Price: required and positive
- Category: required
- inStock: defaults to true when not provided

Invalid input returns HTTP 400 Bad Request.

## Error Handling

The API uses a global exception handler.

Example validation error:

```json
{
  "status": 400,
  "message": "Validation failed",
  "timestamp": "2026-11-09T01:39:02",
  "errors": {
    "name": "Name is required",
    "price": "Price must be positive"
  }
}
```

Example missing-product error:

```json
{
  "status": 404,
  "message": "Product not found with id: 9999",
  "timestamp": "2026-11-09T16:28:06",
  "errors": {}
}
```

## Assumptions

- Product IDs are automatically generated by PostgreSQL.
- The client cannot set IDs or timestamps.
- Prices use BigDecimal.
- Product availability defaults to true.
- Category is stored as a String.
- Category filtering and name searching are case-insensitive.
- Search endpoints return empty arrays when no products match.
- PostgreSQL runs locally on port 5432.
- Hibernate updates the database schema automatically.

## AI Assistance Acknowledgement

AI assistance was used for development guidance,
debugging, explanations, and documentation support.
All submitted code should be reviewed and understood
by the student.
```
