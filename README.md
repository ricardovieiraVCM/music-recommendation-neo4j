# 🎵 Music Recommendation System with Neo4j

Este projeto demonstra um sistema simples de recomendação musical utilizando
banco de dados em grafos com **Neo4j Aura** e consultas **Cypher**.

A recomendação é baseada nos gêneros musicais curtidos pelo usuário e retorna
músicas que ele ainda não ouviu.

---

## 🧠 Modelagem do Grafo

### Entidades
- **User**: usuários da plataforma
- **Music**: músicas disponíveis
- **Artist**: artistas
- **Genre**: gêneros musicais

### Relacionamentos
- `(:User)-[:LIKES]->(:Genre)`
- `(:User)-[:LISTENED {rating}]->(:Music)`
- `(:Music)-[:BELONGS_TO]->(:Genre)`
- `(:Artist)-[:COMPOSED]->(:Music)`

---

## 📐 Modelo Visual
O modelo do grafo foi criado utilizando o **Neo4j Arrows**.

📁 Veja em: `model/graph-model.png`

---

## ⚙️ Tecnologias
- Neo4j Aura
- Cypher
- Neo4j Browser
- Arrows.app

---

## 🚀 Como Executar
1. Execute os arquivos da pasta `cypher/` na ordem:
   - `01_create_nodes.cypher`
   - `02_create_relationships.cypher`
   - `03_recommendation.cypher`

2. Visualize os resultados no Neo4j Browser.

---

## 📌 Exemplo de Recomendação
Usuário: **Ricardo**  
Gênero preferido: **Rock**  
Resultado esperado: músicas de Rock ainda não ouvidas.

---

## 👤 Autor
Ricardo Vieira
// USERS
CREATE (:User {id: 1, name: 'Ricardo'});
CREATE (:User {id: 2, name: 'Ana'});

// GENRES
CREATE (:Genre {name: 'Rock'});
CREATE (:Genre {name: 'Pop'});

// ARTISTS
CREATE (:Artist {id: 1, name: 'Queen'});
CREATE (:Artist {id: 2, name: 'Nirvana'});

// MUSICS
CREATE (:Music {id: 1, title: 'Bohemian Rhapsody'});
CREATE (:Music {id: 2, title: 'Smells Like Teen Spirit'});
CREATE (:Music {id: 3, title: 'Back In Black'});
// USER LIKES GENRE
MATCH (u:User {name: 'Ricardo'}), (g:Genre {name: 'Rock'})
CREATE (u)-[:LIKES]->(g);

// ARTIST COMPOSED MUSIC
MATCH (a:Artist {name: 'Queen'}), (m:Music {title: 'Bohemian Rhapsody'})
CREATE (a)-[:COMPOSED]->(m);

MATCH (a:Artist {name: 'Nirvana'}), (m:Music {title: 'Smells Like Teen Spirit'})
CREATE (a)-[:COMPOSED]->(m);

// MUSIC BELONGS TO GENRE
MATCH (m:Music {title: 'Bohemian Rhapsody'}), (g:Genre {name: 'Rock'})
CREATE (m)-[:BELONGS_TO]->(g);

MATCH (m:Music {title: 'Smells Like Teen Spirit'}), (g:Genre {name: 'Rock'})
CREATE (m)-[:BELONGS_TO]->(g);

MATCH (m:Music {title: 'Back In Black'}), (g:Genre {name: 'Rock'})
CREATE (m)-[:BELONGS_TO]->(g);

// USER LISTENED MUSIC
MATCH (u:User {name: 'Ricardo'}), (m:Music {title: 'Bohemian Rhapsody'})
CREATE (u)-[:LISTENED {rating: 5}]->(m);
// RECOMMEND MUSIC BASED ON LIKED GENRE
MATCH (u:User {name: 'Ricardo'})-[:LIKES]->(g:Genre)<-[:BELONGS_TO]-(m:Music)
WHERE NOT (u)-[:LISTENED]->(m)
RETURN m.title AS recomendacao;
