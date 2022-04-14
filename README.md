# Desafio TDD - Bootcamp Devsuperior
Desenvolvimento de um CRUD usando TDD

## Objetivo: Implementar as funcionalidades necessárias para que os testes do projeto passem.

A aplicação tem as seguintes entidades:

![image](https://user-images.githubusercontent.com/16855746/163425290-3eede9c3-f4ef-494a-9ce3-4f5e0388f8f5.png)

Este é um sistema de eventos e cidades com uma relação N-1 entre ele

## Teste de integração

### Teste de Integração CityControllerIT

### Teste deve passar se a requisição retornar as Cidades ordenadas por nome
```java
@Test
public void findAllShouldReturnAllResourcesSortedByName() throws Exception {		
  ResultActions result =
      mockMvc.perform(get("/cities?page=0$size=10&sort=name,asc")
        .contentType(MediaType.APPLICATION_JSON));		
  result.andExpect(status().isOk());
  result.andExpect(jsonPath("$.content[0].name").value("Belo Horizonte"));
  result.andExpect(jsonPath("$.content[1].name").value("Belém"));
  result.andExpect(jsonPath("$.content[2].name").value("Brasília"));
}
```

### Teste deve passar se a requisição de Inserir nova cidade adicionar o recurso novo
```java
@Test
public void insertShouldInsertResource() throws Exception {

  CityDTO dto = new CityDTO(null, "Recife");
  String jsonBody = objectMapper.writeValueAsString(dto);

  ResultActions result =
      mockMvc.perform(post("/cities")
        .content(jsonBody)
        .contentType(MediaType.APPLICATION_JSON)
        .accept(MediaType.APPLICATION_JSON));

  result.andExpect(status().isCreated());
  result.andExpect(jsonPath("$.id").exists());
  result.andExpect(jsonPath("$.name").value("Recife"));
}
```

### Teste deve passar se a requisição de Delete retornar No Content quando uma Cidade for Independente
```java
@Test
public void deleteShouldReturnNoContentWhenIndependentId() throws Exception {		

  Long independentId = 5L;

  ResultActions result =
      mockMvc.perform(delete("/cities/{id}", independentId));


  result.andExpect(status().isNoContent());
}
```

### Teste deve passar se a requisição de Delete retornar um Not Found quando uma Cidade Não existir
```java
@Test
public void deleteShouldReturnNotFoundWhenNonExistingId() throws Exception {		

  Long nonExistingId = 50L;

  ResultActions result =
      mockMvc.perform(delete("/cities/{id}", nonExistingId));

  result.andExpect(status().isNotFound());
}
```
### Teste deve passar se a requisição de Delete retornar Bad Request quando uma cidade for Dependente
```java
@Test
@Transactional(propagation = Propagation.NEVER) 
public void deleteShouldReturnBadRequestWhenDependentId() throws Exception {		

  Long dependentId = 1L;

  ResultActions result =
      mockMvc.perform(delete("/cities/{id}", dependentId));

  result.andExpect(status().isBadRequest());
}
```

### Teste de Integração EventControllerIT

### Teste deve passar se a requisição de Atualizar retornar um Recurso quando um Evento existir
```java
@Test
public void updateShouldUpdateResourceWhenIdExists() throws Exception {

  long existingId = 1L;

  EventDTO dto = new EventDTO(null, "Expo XP", LocalDate.of(2021, 5, 18), "https://expoxp.com.br", 7L);
  String jsonBody = objectMapper.writeValueAsString(dto);

  ResultActions result =
      mockMvc.perform(put("/events/{id}", existingId)
        .content(jsonBody)
        .contentType(MediaType.APPLICATION_JSON)
        .accept(MediaType.APPLICATION_JSON));

  result.andExpect(status().isOk());
  result.andExpect(jsonPath("$.id").exists());
  result.andExpect(jsonPath("$.id").value(1L));		
  result.andExpect(jsonPath("$.name").value("Expo XP"));
  result.andExpect(jsonPath("$.date").value("2021-05-18"));
  result.andExpect(jsonPath("$.url").value("https://expoxp.com.br"));
  result.andExpect(jsonPath("$.cityId").value(7L));
}
```

### Teste deve passar se a requisição de Atualizar retornar Not Found quando um Evento não existir
```java
@Test
public void updateShouldReturnNotFoundWhenIdDoesNotExist() throws Exception {

  long nonExistingId = 1000L;

  EventDTO dto = new EventDTO(null, "Expo XP", LocalDate.of(2021, 5, 18), "https://expoxp.com.br", 7L);
  String jsonBody = objectMapper.writeValueAsString(dto);

  ResultActions result =
      mockMvc.perform(put("/events/{id}", nonExistingId)
        .content(jsonBody)
        .contentType(MediaType.APPLICATION_JSON)
        .accept(MediaType.APPLICATION_JSON));

  result.andExpect(status().isNotFound());
}
```

### A ideia do case é ter todos os testes escritos e a partir dele desenvolver a aplicação.
### Obrigado!
