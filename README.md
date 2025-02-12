# 📌 DSList - Catálogo de Jogos  

## 📝 Sobre o Projeto  
O **DSList** é uma API desenvolvida em **Java com Spring Boot**, que permite a criação e gerenciamento de listas de jogos. O projeto segue o padrão REST e utiliza um banco de dados relacional para armazenar as informações dos jogos e suas respectivas listas.

## 🚀 Tecnologias Utilizadas  
- **Java 17**  
- **Spring Boot** (Spring Web, Spring Data JPA)  
- **Hibernate**  
- **PostgreSQL / H2 Database**  
- **Maven**  

## 🏛️ Modelo de Domínio  
O projeto contém três principais entidades:  

- **Game**: Representa um jogo, contendo atributos como título, gênero, ano de lançamento, entre outros.  
- **GameList**: Representa uma lista de jogos criada pelo usuário.  
- **Belonging**: Representa a relação entre um jogo e uma lista, armazenando a posição do jogo dentro da lista.    

### Modelo de domínio DSList
classDiagram
    Game --> Belonging
    GameList --> Belonging

    class Game {
        id: Long
        title: String
        year: Integer
        genre: String
        platforms: String
        score: Double
        imgUrl: String
        shortDescription: String
        longDescription: String
    }

    class GameList {
        id: Long
        name: String
    }

    class Belonging {
        position: Integer
    }


## Trechos de código

### Plug-in Maven
```xml
<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-resources-plugin</artifactId>
	<version>3.1.0</version> <!--$NO-MVN-MAN-VER$ -->
</plugin>
```

### application.properties
```properties
spring.profiles.active=${APP_PROFILE:test}
spring.jpa.open-in-view=false

cors.origins=${CORS_ORIGINS:http://localhost:5173,http://localhost:3000}
```

### application-test.properties
```properties
# H2 Connection
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.username=sa
spring.datasource.password=

# H2 Client
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

# Show SQL
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

### application-dev.properties
```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/dscatalog
spring.datasource.username=postgres
spring.datasource.password=1234567

spring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect
spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
spring.jpa.hibernate.ddl-auto=none
```

### application-prod.properties
```properties
spring.datasource.url=${DB_URL}
spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}

spring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect
spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
spring.jpa.hibernate.ddl-auto=none
```

### system.properties
```properties
java.runtime.version=17
```

### WebConfig
```java
@Configuration
public class WebConfig {

	@Value("${cors.origins}")
	private String corsOrigins;
	
	@Bean
	public WebMvcConfigurer corsConfigurer() {
		return new WebMvcConfigurer() {
			@Override
			public void addCorsMappings(CorsRegistry registry) {
				registry.addMapping("/**").allowedMethods("*").allowedOrigins(corsOrigins);
			}
		};
	}
}
```

### GameRepository
```java
@Query(nativeQuery = true, value = """
	SELECT tb_game.id, tb_game.title, tb_game.game_year AS year, tb_game.img_url AS imgUrl,
	tb_game.short_description AS shortDescription, tb_belonging.position
	FROM tb_game
	INNER JOIN tb_belonging ON tb_game.id = tb_belonging.game_id
	WHERE tb_belonging.list_id = :listId
	ORDER BY tb_belonging.position
	""")
List<GameMinProjection> searchByList(Long listId);
```

### GameListRepository
```java
@Modifying
@Query(nativeQuery = true, value = "UPDATE tb_belonging SET position = :newPosition WHERE list_id = :listId AND game_id = :gameId")
void updateBelongingPosition(Long listId, Long gameId, Integer newPosition);
```

### import.sql
```sql
INSERT INTO tb_game_list (name) VALUES ('Aventura e RPG');
INSERT INTO tb_game_list (name) VALUES ('Jogos de plataforma');

INSERT INTO tb_game (title, score, game_year, genre, platforms, img_url, short_description, long_description) VALUES
('Mass Effect Trilogy', 4.8, 2012, 'Role-playing (RPG), Shooter', 'XBox, Playstation, PC', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/1.png', 'Lorem ipsum dolor sit amet consectetur adipisicing elit.', 'Lorem ipsum dolor sit amet consectetur adipisicing elit.');

INSERT INTO tb_game (title, score, game_year, genre, platforms, img_url, short_description, long_description) VALUES
('Red Dead Redemption 2', 4.7, 2018, 'Role-playing (RPG), Adventure', 'XBox, Playstation, PC', 'https://raw.githubusercontent.com/devsuperior/java-spring-dslist/main/resources/2.png', 'Lorem ipsum dolor sit amet consectetur adipisicing elit.', 'Lorem ipsum dolor sit amet consectetur adipisicing elit.');

INSERT INTO tb_belonging (list_id, game_id, position) VALUES (1, 1, 0);
INSERT INTO tb_belonging (list_id, game_id, position) VALUES (1, 2, 1);
INSERT INTO tb_belonging (list_id, game_id, position) VALUES (1, 3, 2);
INSERT INTO tb_belonging (list_id, game_id, position) VALUES (1, 4, 3);
INSERT INTO tb_belonging (list_id, game_id, position) VALUES (1, 5, 4);
```

---

### Contato

Caso tenha alguma dúvida ou sugestão, sinta-se à vontade para entrar em contato comigo através do meu GitHub ou LinkedIn:

- GitHub: [gtiAnderson](https://github.com/gtiAnderson)
- LinkedIn: [andersonnmoreira](https://www.linkedin.com/in/andersonnmoreira/)
