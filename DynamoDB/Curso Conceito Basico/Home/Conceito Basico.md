
Referência: 
<br>
[DynamoDB](https://www.youtube.com/watch?v=kSnpuKr3Ajw  "DynamoDB"   )
<br>
[Como modelar com DynamoDB](https://www.youtube.com/watch?v=bTLoK2eHwi4  "Como modelar com DynamoDB"   )


----

# DynamoDB - Not only SQL (NOSQL)


* Tabelas como em outros banco de dados

* Itens cada tabela contém zero ou mais itens. Um item é um grupo de atributos identificável exclusivamente entre todos os itens.

* Atributos cada item é composto de um ou mais atributos. Um atributo é um elemento de dados fundamental, algo que não precisa ser dividido. São similares a colunas em banco de  dados relacionais.

* Atributos Aninhados O DynamoDB oferece suporte a atributos aninhados até 32 níveis de profundidade.

* ìndices Secundários Permite consultar os dados na tabela usando uma chave alternativa, além de consultas com base na chave primária.

* Chave Primária Pode ser chave primária simples, de um atributo, ou uma chave composta por dois atributos.

* As tabelas do DynamoDB pode ou não ter schemas.

* As tabelas podem ter atributos distintos, por isso não é chamado de coluna.

----
<br>

> Quando Usar?
* Muitos dados e Baixa Latência.

> Clientes que usam
* A própria Amazon.com utiliza.

> TradeOff
* Modelagem dos Dados no DynamoDB bem complexa.

### Conceitos Basicos

* Chave Valor.
* Tabela composta de duas PK
* Primary Key vai ser composta por (Partition Key e SortKey)
* Faça a pesquisa sempre pela chave
* A partition Key não é opcional na consulta, a Sort Key sim.
* Inverter a ordem de Partition Key e Sort Key é possível.

> EX: Modelagem de um sistema pequeno:

### Tabelas no sistema

![foo bar](Imagens/Modelagem%20de%20um%20sistema%20pequeno.png "Sistema pequeno")


> Modelagem: ERD > Padrões de acesso > Design

> Esquecer: Normalização, JOINS e Uma entidade por tabela.

### Padrões de Acesso:
1. Buscar Perfil de um usuário
2. Buscar pedidos de um usuário.
3. Buscar um pedido e seus itens.
4. Buscar pedidos de um usuário pelo status.

----
#### 1. Buscar perfil de um usuário.

![foo bar](Imagens/Buscar%20perfil%20de%20um%20usuário.png  "Perfil de Usuário"   )

> Query para realizar a busca.

```sql
pk='normandesjr'
```
----
#### 2. Buscar pedidos de um usuário.

![foo bar](Imagens/Buscar%20pedidos%20de%20um%20usuário.png  "Pedido de um Usuário"   )

> Como fazer essa query?

```sql
pk='normandesjr' and BEGINS_WITH(sk, '#PROFILE#')
pk='normandesjr' and BEGINS_WITH(sk, 'ORDER#')
```

> É padrão chamar as primary key de (pk) 

> Se for composta, ficaria Primary Key(pk) e Sort Key (sk)

> Por que?

> Porque os atributos podem variar na Primary Key e Sort Key, então não faz sentido dar um nome para essa "coluna".

----

#### 3. Buscar um pedido e seus itens.

![foo bar](Imagens/Buscar%20um%20pedido%20e%20seus%20itens.png  "Pedido e os Itens"   )



> Como fazer essa consulta?

> (pk) só pode usar (=)

> (sk) podemos usar (>, <, =)

* Inverter as colunas (pk) e (sk)

> Usando um index secundário global conseguimos inverter as colunas da (pk)



```sql
sk='ORDER#5eaf12' and BEGINS_WITH(pk, 'ITEM#')
```

