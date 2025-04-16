# Nomeados ao Oscar

Contém a base de indicados ao Oscar em formato SQL para treinar comandos CRUD. 

Abaixo, algumas atividades para trabalharmos.

--- 

* Atualize os registros da tabela com os dados do Oscar 2025
R: Dados atualizados
---

* Qual o **total** de registros na tabela indicados?
R: 11004

Q: 
```js
db.registros.countDocuments()
```
---

* Qual o número de indicações por categoria agrupadas por categoria?

R: 2

Q:
```js
db.indicados_ao_oscar.aggregate([
  {
    $group: {
      _id: "$categoria",  
      total_indicacoes: { $sum: 1 }  
    }
  },
  {
    $sort: { total_indicacoes: -1 } 
  }
]);
```

---

* Quantas vezes Natalie Portman foi indicada ao Oscar?

R: 3 vezes

Q:
```js
 db.oscar.find({"Nome": "Natalie Portman"}).countDocuments()
```

---

* Quantos Oscars Natalie Portman ganhou?
R: 1

Q:
```js
db.registros.countDocuments({
nome_do_indicado: "Natalie Portman",
vencedor: "true"
})
```
---

* Quantas vezes Viola Davis foi indicada ao Oscar?
R: 4

Q:
```js
db.registros.countDocuments({
nome_do_indicado:"Viola Davis"
})
```
---

* Quantos Oscars Viola Davis ganhou?
R: 1

Q:
```js
db.registros.countDocuments({
nome_do_indicado: "Viola Davis",
vencedor: "true"
})
```
---

* Amy Adams já ganhou algum Oscar?
R: Não

Q:
```js
db.registros.countDocuments({
nome_do_indicado: "Amy Adams",
vencedor: "true"
})
```
---

* Quais os atores/atrizes que foram indicados mais de uma vez?
Q:
```js
db.registros.aggregate([
  { $group: { _id: "$nome_do_indicado", total_indicacoes: { $sum: 1 } } },
  { $match: { total_indicacoes: { $gt: 1 } } }
])
```
---

* A série de filmes Toy Story ganhou Oscars em quais anos?
R: Duas vezes em 2011 e uma vez em 2020

Q:
```js
db.registros.find({
nome_do_filme:/Toy Story/,
vencedor: "true"
})
```
---

* A partir de que ano que a categoria "Actress" deixa de existir? 
R: 1928

Q:
```js
db.registros.find({
categoria:"ACTRESS",
}).sort({
ano_cerimonia:1
}).limit(1)
```
---

* Quem ganhou o primeiro Oscar para Melhor Atriz? Em que ano?
R:Janet Gaynor, em 1928.

Q:
```js
db.registros.find({
categoria:"ACTRESS",
vencedor:"true"
}).sort({
ano_cerimonia:1
}).limit(1)
```
---

* Na campo "Vencedor", altere todos os valores com "true" para 1 e todos os valores "false" para 0.
Q:
```js
db.registros.updateMany(
  {vencedor: "true"},
  {$set:{vencedor:1}},
);

db.registros.updateMany(
  {vencedor: "false"},
  {$set:{vencedor:0}},
)
```
---

* Em qual edição do Oscar "Crash" concorreu ao Oscar?
R: Cerimonia 78

Q:
```js
db.registros.find({
nome_do_filme: "Crash"
})
```
---

* O filme Central do Brasil aparece no Oscar?
R: Não

Q:
```js
db.registros.find({
nome_do_filme: "Central do Brasil"
})
```
---

* Inclua no banco 3 filmes que nunca foram nem nomeados ao Oscar, mas que merecem ser. 
Q:
```js
db.registros.insertMany([
  {
    _id: ObjectId(), 
    id_registro: null,
    ano_filmagem: 2005,
    ano_cerimonia: null,
    cerimonia: null,
    categoria: null,
    nome_do_indicado: null,
    nome_do_filme: "Madagascar",
    vencedor: null
  },
  {
    _id: ObjectId(),
    id_registro: null,
    ano_filmagem: 2006,
    ano_cerimonia: null,
    cerimonia: null,
    categoria: null,
    nome_do_indicado: null,
    nome_do_filme: "Putz! A Coisa Tá Feia",
    vencedor: null
  },
  {
    _id: ObjectId(),
    id_registro: null,
    ano_filmagem: 2022,
    ano_cerimonia: null,
    cerimonia: null,
    categoria: null,
    nome_do_indicado: null,
    nome_do_filme: "Filme Arremessando Alto",
    vencedor: null
  }
]);
```
---

* Denzel Washington já ganhou algum Oscar?
R: Sim, duas vezes. Em 1990 e 2002

Q:
```js
db.registros.find({
nome_do_indicado:"Denzel Washington",
vencedor: 1
})
```
---

* Quais os filmes que ganharam o Oscar de Melhor Filme?
R: Going My Way, The Lost Weekend, The Best Years of Our Lives, Gentleman's Agreement, Hamlet, All the King's Men, All about Eve, An American in Paris, The Greatest Show on Earth, From Here to Eternity, On the Waterfront, Marty, Around the World in 80 Days, The Bridge on the River Kwai, Gigi, Ben-Hur, The Apartment, West Side Story.

Q:
```js
db.registros.find(
  {
    categoria: "BEST MOTION PICTURE", 
    vencedor: 1 
  },
  {
    nome_do_filme: 1, 
    _id: 0 
  }
)
```
---

* Sidney Poitier foi o primeiro ator negro a ser indicado ao Oscar. Em que ano ele foi indicado? Por qual filme?
R: Ele foi indicado em 1959 e 1964, mas ganhou apenas em 1964, com o filme Lilies of the Field.

Q:
```js
db.registros.find({
  nome_do_indicado: "Sidney Poitier",
  categoria: "ACTOR"
});
```
---

* Quais os filmes que ganharam o Oscar de Melhor Filme e Melhor Diretor na mesma cerimonia?
R: R: Gentleman's Agreement, Marty, The Lost Weekend, The Bridge on the River Kwai, The Apartment, From Here to Eternity, All about Eve, The Best Years of Our Lives, West Side Story, Ben-Hur, On the Waterfront, Gigi, Going My Way

Q:
```js
db.registros.aggregate(
    { $match: { categoria: { $in: ["BEST MOTION PICTURE", "DIRECTING"] }, vencedor: 1 } },
    { $group: { _id: { cerimonia: "$cerimonia", filme: "$nome_do_filme" }, categorias: { $addToSet: "$categoria" } } },
    { $match: { categorias: { $all: ["BEST MOTION PICTURE", "DIRECTING"] } } },
);
```
---

* Denzel Washington e Jamie Foxx já concorreram ao Oscar no mesmo ano?
R: Segundo o banco de dados, os dois não concorreram ao oscar no mesmo ano 

Q:
```js
db.registros.find({
nome_do_indicado:"Denzel Washington"
});

db.registros.find({
nome_do_indicado:"Jamie Foxx"
});
```
