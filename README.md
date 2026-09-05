# Exp-04-Spring-Boot-with-REST-API-and-Hibernate-Integration

## AIM:

To develop a Spring Boot application to store and retrieve data from a Movies database using Object Relational Mapping (ORM) with Hibernate and expose it via REST APIs.

---
***Name:** Bakkiyalakshmi E

**Reg no:** 212223220012
---

## ALGORITHM:

1. Create a Spring Boot project with the following dependencies:

   * Spring Web
   * Spring Data JPA
   * H2 Database

2. Configure `application.properties` with the H2 database connection and JPA settings.

3. Create a `Movie` entity with fields such as `id`, `title`, `genre`, `rating`, and `year`.

4. Create a `MovieRepository` interface extending `JpaRepository`.

5. Create a `MovieController` to define REST endpoints for CRUD operations:

   * GET `/movies`
   * GET `/movies/{id}`
   * POST `/movies`
   * PUT `/movies/{id}`
   * DELETE `/movies/{id}`

6. Run the Spring Boot application.

7. Test the REST APIs using Postman or a web browser.

## PROJECT STRUCTURE:

```text
MovieManagementSystem
└── src
    └── main
        ├── java
        │   └── com.example.movie_management_system
        │       ├── MovieManagementSystemApplication.java
        │       ├── entity
        │       │   └── Movie.java
        │       ├── repository
        │       │   └── MovieRepository.java
        │       └── controller
        │           └── MovieController.java
        └── resources
            └── application.properties
```

## PROGRAM CODE:

### 1. application.properties

```properties
spring.application.name=Movie Management System

spring.datasource.url=jdbc:h2:mem:moviedb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

spring.jpa.hibernate.ddl-auto=create
spring.jpa.show-sql=true

spring.h2.console.enabled=true
spring.h2.console.path=/h2-console
```

### 2. Movie.java

```java
package com.example.movie_management_system.entity;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class Movie {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;
    private String genre;

    @Column(name = "release_year")
    private int year;

    private double rating;

    public Movie() {
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getGenre() {
        return genre;
    }

    public void setGenre(String genre) {
        this.genre = genre;
    }

    public int getYear() {
        return year;
    }

    public void setYear(int year) {
        this.year = year;
    }

    public double getRating() {
        return rating;
    }

    public void setRating(double rating) {
        this.rating = rating;
    }
}
```

### 3. MovieRepository.java

```java
package com.example.movie_management_system.repository;

import com.example.movie_management_system.entity.Movie;
import org.springframework.data.jpa.repository.JpaRepository;

public interface MovieRepository extends JpaRepository<Movie, Long> {
}
```

### 4. MovieController.java

```java
package com.example.movie_management_system.controller;

import com.example.movie_management_system.entity.Movie;
import com.example.movie_management_system.repository.MovieRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/movies")
public class MovieController {

    @Autowired
    private MovieRepository repo;

    @GetMapping
    public List<Movie> getAllMovies() {
        return repo.findAll();
    }

    @GetMapping("/{id}")
    public ResponseEntity<Movie> getMovieById(@PathVariable Long id) {
        return repo.findById(id)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
    }

    @PostMapping
    public Movie addMovie(@RequestBody Movie movie) {
        return repo.save(movie);
    }

    @PutMapping("/{id}")
    public ResponseEntity<Movie> updateMovie(
            @PathVariable Long id,
            @RequestBody Movie movieDetails) {

        return repo.findById(id).map(movie -> {

            movie.setTitle(movieDetails.getTitle());
            movie.setGenre(movieDetails.getGenre());
            movie.setYear(movieDetails.getYear());
            movie.setRating(movieDetails.getRating());

            return ResponseEntity.ok(repo.save(movie));

        }).orElse(ResponseEntity.notFound().build());
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteMovie(@PathVariable Long id) {

        if (repo.existsById(id)) {
            repo.deleteById(id);
            return ResponseEntity.ok().build();
        }

        return ResponseEntity.notFound().build();
    }
}
```

### 5. MovieManagementSystemApplication.java

```java
package com.example.movie_management_system;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MovieManagementSystemApplication {

    public static void main(String[] args) {
        SpringApplication.run(MovieManagementSystemApplication.class, args);
    }
}
```

## REST API TESTING:

### 1. Add Movie - POST

**URL:**

```text
http://localhost:8080/movies
```

**Method:**

```text
POST
```

**Request Body:**

```json
{
    "title": "Leo",
    "genre": "Action",
    "year": 2023,
    "rating": 8.5
}
```

**Output:**

```json
{
    "id": 1,
    "title": "Leo",
    "genre": "Action",
    "year": 2023,
    "rating": 8.5
}
```

### 2. Get All Movies - GET

**URL:**

```text
http://localhost:8080/movies
```

**Method:**

```text
GET
```

**Output:**

```json
[
    {
        "id": 1,
        "title": "Leo",
        "genre": "Action",
        "year": 2023,
        "rating": 8.5
    }
]
```

### 3. Get Movie by ID - GET

**URL:**

```text
http://localhost:8080/movies/1
```

**Method:**

```text
GET
```

**Output:**

```json
{
    "id": 1,
    "title": "Leo",
    "genre": "Action",
    "year": 2023,
    "rating": 8.5
}
```

### 4. Update Movie - PUT

**URL:**

```text
http://localhost:8080/movies/1
```

**Method:**

```text
PUT
```

**Request Body:**

```json
{
    "title": "Leo",
    "genre": "Action Thriller",
    "year": 2023,
    "rating": 9.0
}
```

**Output:**

```json
{
    "id": 1,
    "title": "Leo",
    "genre": "Action Thriller",
    "year": 2023,
    "rating": 9.0
}
```

### 5. Delete Movie - DELETE

**URL:**

```text
http://localhost:8080/movies/1
```

**Method:**

```text
DELETE
```

**Output:**

```text
200 OK
```

## OUTPUT:
<img width="1917" height="1020" alt="Screenshot 2026-09-05 140621" src="https://github.com/user-attachments/assets/6f66903d-7162-4117-9115-ecc15a40ced1" />
<img width="1917" height="1018" alt="Screenshot 2026-09-05 141454" src="https://github.com/user-attachments/assets/d4206c8c-6bad-4084-8ac1-e91a2369bcc9" />
<img width="1917" height="1023" alt="Screenshot 2026-09-05 141432" src="https://github.com/user-attachments/assets/2277d8ba-1131-4268-9605-bcc4bb0145d2" />
<img width="1917" height="1025" alt="Screenshot 2026-09-05 141511" src="https://github.com/user-attachments/assets/924bf93c-efb1-4ca5-a356-95db8ed83557" />
<img width="1917" height="1017" alt="Screenshot 2026-09-05 141807" src="https://github.com/user-attachments/assets/3bbfdc6e-976e-44a2-b0e3-a9c0dc938668" />



## RESULT:

Thus, the Spring Boot application was successfully developed using Hibernate ORM and REST APIs. The Movie database operations such as adding, retrieving, updating, and deleting movie records were successfully performed.
