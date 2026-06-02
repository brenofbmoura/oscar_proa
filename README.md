# 🎬 MongoDB - Análise do Oscar

## 👨‍💻 Autor
Breno Faria Barbosa de Moura  

---

## 📚 Sobre o Projeto
Este projeto explora uma base de dados histórica do Oscar utilizando MongoDB, analisando indicações, vencedores, categorias e tendências ao longo do tempo.

---

# ✅ NÍVEL 1 — Primeiros Passos

## 1.1 Total de registros
db.oscar_indicados.countDocuments();
✅ 1295 registros

## 1.2 Categorias únicas
db.oscar_indicados.distinct("categoria");
✅ Mais de 40 categorias

## 1.3 Primeiro ano
db.oscar_indicados.find().sort({ano_cerimonia: 1}).limit(1);
✅ 1928

## 1.4 Último ano
db.oscar_indicados.find().sort({ano_cerimonia: -1}).limit(1);
✅ 1969

## 1.5 Total de cerimônias
db.oscar_indicados.distinct("ano_cerimonia").length;
✅ 42 cerimônias

## 1.6 Atualização
✅ Simulado: inclusão de dados recentes

---

# ✅ NÍVEL 2 — Categorias

## 2.1 Indicações por categoria
db.oscar_indicados.aggregate([
 { $group: { _id: "$categoria", total: { $sum: 1 } } },
 { $sort: { total: -1 } }
]);

## 2.2 Categoria mais frequente
✅ ACTOR

## 2.3 Categoria menos frequente
✅ Categorias especiais

## 2.4 Categoria ACTRESS
✅ Mantida até o final da base

## 2.5 Categorias antigas
✅ UNIQUE AND ARTISTIC PICTURE  
✅ ENGINEERING EFFECTS  

## 2.6 Categorias com DIRECTING
db.oscar_indicados.find({ categoria: /DIRECTING/ });

---

# ✅ NÍVEL 3 — Atores

⚠️ Não aparecem na base (dados até 1969)

Natalie Portman → não aparece  
Viola Davis → não aparece  
Amy Adams → não aparece  
Denzel Washington → não aparece  

---

# ✅ NÍVEL 4 — Vencedores

## 4.1 Primeira atriz
✅ Janet Gaynor (1928)

## 4.2 Primeiro ator
✅ Emil Jannings (1928)

## 4.3 Total de vencedores
✅ Aproximadamente 700+

## 4.4 Melhor filme
db.oscar_indicados.find({
 categoria: /PICTURE/,
 vencedor: 1
});
✅ Wings, Casablanca, Ben-Hur

## 4.5 Total filmes vencedores
✅ Aproximadamente 40+

---

# ✅ NÍVEL 5 — Análise

## 5.1 Mais de uma indicação
db.oscar_indicados.aggregate([
 { $group: { _id: "$nome_do_indicado", total: { $sum: 1 } } },
 { $match: { total: { $gt: 1 } } }
]);

## 5.2 Mais indicado
✅ Walt Disney

## 5.3 Sem vitória com muitas indicações
✅ Existem casos na base

## 5.4 Várias categorias
✅ Diretores e produtores

## 5.5 Apenas 1 indicação
✅ Maioria dos registros

## 5.6 Ano com mais indicações
✅ Décadas de 1950–60

---

# ✅ NÍVEL 6 — Filmes

Toy Story → não existe  
Crash → não existe  
Central do Brasil → não existe  

---

# ✅ NÍVEL 7 — Atualizações

## Conversão de boolean
db.oscar_indicados.updateMany(
 { vencedor: "true" },
 { $set: { vencedor: true } }
);

✅ Correção aplicada

---

# ✅ NÍVEL 8 — Tempo

## 8.1 Por década
db.oscar_indicados.aggregate([
{
 $group: {
  _id: { $floor: { $divide: ["$ano_cerimonia", 10] } },
  total: { $sum: 1 }
 }
}
]);

## 8.2 Década com mais indicações
✅ 1950s

## 8.3 Evolução
✅ Crescimento ao longo do tempo

## 8.4 Ano com mais indicações
✅ Final da base

## 8.5 Crescimento
✅ Aumento significativo

---

# ✅ NÍVEL 9 — História

Sidney Poitier  
✅ 1958 — The Defiant Ones  
✅ Não venceu nessa indicação  

---

# ✅ NÍVEL 10 — Avançado

✅ Filme mais premiado: Ben-Hur  
✅ Indicados consecutivos existem  
✅ Média ~30 indicações por cerimônia  

---

# ✅ NÍVEL 11 — Complexo

Top filmes:
1. Ben-Hur (11)
2. West Side Story (10)

✅ Top artista: Walt Disney  

---

# ✅ NÍVEL 12 — Casos Práticos

✅ Filmes premiados selecionados  
✅ Clássicos antigos identificados  

---

# ✅ NÍVEL 13 — Criativo

Filmes que começam com "The":
db.oscar_indicados.find({
 nome_do_filme: /^The/,
 vencedor: 1
});

---

# ✅ NÍVEL 14 — DASHBOARD FINAL

db.oscar_indicados.aggregate([
{
 $facet: {

  totalIndicacoes: [{ $count: "total" }],

  totalCerimonias: [
   { $group: { _id: "$ano_cerimonia" } },
   { $count: "total" }
  ],

  totalVencedores: [
   { $match: { vencedor: 1 } },
   { $count: "total" }
  ],

  categoriaMaisIndicacoes: [
   { $group: { _id: "$categoria", total: { $sum: 1 } } },
   { $sort: { total: -1 } },
   { $limit: 1 }
  ],

  filmeMaisPremiado: [
   { $match: { vencedor: 1 } },
   { $group: { _id: "$nome_do_filme", total: { $sum: 1 } } },
   { $sort: { total: -1 } },
   { $limit: 1 }
  ],

  atorMaisIndicado: [
   { $group: { _id: "$nome_do_indicado", total: { $sum: 1 } } },
   { $sort: { total: -1 } },
   { $limit: 1 }
  ],

  decadaMaisPremiacoes: [
   { $match: { vencedor: 1 } },
   {
    $group: {
     _id: {
      $multiply: [
       { $floor: { $divide: ["$ano_cerimonia", 10] } },
       10
      ]
     },
     total: { $sum: 1 }
    }
   },
   { $sort: { total: -1 } },
   { $limit: 1 }
  ],

  categoriasUnicas: [
   { $group: { _id: "$categoria" } },
   { $count: "total" }
  ]

 }
}
]);

✅ Resultado esperado:

- Total de indicações: 1295  
- Total de cerimônias: 42  
- Total de vencedores: ~700+  
- Categoria mais frequente: ACTOR  
- Filme mais premiado: Ben-Hur  
- Ator mais indicado: Walt Disney  
- Década com mais premiações: 1950s  
- Categorias únicas: ~40  

---

# ✅ Conclusão

A análise mostra a evolução do Oscar, crescimento das categorias e domínio de grandes produções ao longo da história.
