


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

>[!NOTE]
>
> Quando Usar?
* Muitos dados e Baixa Latência.

>[!NOTE]
>
> Clientes que usam
* A própria Amazon.com utiliza.

>[!NOTE]
>
> TradeOff

* Modelagem dos Dados no DynamoDB bem complexa.

### Conceitos Basicos

* Chave Valor.
* Backup e Restore em função do tempo (point in time)
*```` Multi-AZ````, alta disponibilidade e durabilidade por padrão.
* Integrado ao ````CloudTrail```` - todas as chamadas de API são registradas como eventos.
* ````Global Tables```` permite a utilização de multiplas regiões e ````multi-master database````
* DynamoDB Stream camptura eventos da tabela ````Lambda pode executar ações````.
* ````DAX (DynamoDB Accelerator)```` - Cache em memória, resposta em microssegundos.
* Database 100% gerenciado.
* Uma tabela por dominio.
* Distribuido Globalmente
* Ideal para Micro Serviços.
* Não faz ````JOIN````
* Dor de cabeça para alterar a estrutura da tabela.
* Tabela composta de duas PK
* Primary Key vai ser composta por (Partition Key e SortKey)
* Faça a pesquisa sempre pela chave
* A partition Key não é opcional na consulta, a Sort Key sim.
* Inverter a ordem de Partition Key e Sort Key é possível.
* Padrões de nomes das colunas ````pk```` para ````Partition Key```` e ````sk```` para ````Sort Key ````.
* Na ````pk```` só conseguimos utilizar ````=````, na ````sk```` que conseguimos usar ``` BEGINS_WITH ```
* A pesquisa sempre será feita pela chave.

### Exemplos de tabelas

- Só com uma ````pk Partition Key```` 

![foo bar](Imagens/Exemplo%20de%20Tabela%20no%20DynamoDB.png)

- Composta  ````pk Partition Key e sk Sort Key```` 

![foo bar](Imagens/Exemplo%20de%20Tabela%20no%20DynamoDB%20Chave%20Composta.png)

>[!TIP]
>
> EX: Modelagem de um sistema pequeno:

### Tabelas no sistema

![foo bar](Imagens/Modelagem%20de%20um%20sistema%20pequeno.png "Sistema pequeno")


>[!TIP]
>
> Modelagem: ERD > Padrões de acesso > Design<br>
> Esquecer: Normalização, JOINS e Uma entidade por tabela.

### Padrões de Acesso:
1. Buscar Perfil de um usuário
2. Buscar pedidos de um usuário.
3. Buscar um pedido e seus itens.
4. Buscar pedidos de um usuário pelo status 


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

>[!IMPORTANT]
>
>É padrão chamar as primary key de (pk) <br>
>Se for composta, ficaria Primary Key(pk) e Sort Key (sk) 

>[!NOTE]
>
>Por que?

>[!TIP]
>
> Porque os atributos podem variar na Primary Key e Sort Key, então não faz sentido dar um nome para essa "coluna".

----

#### 3. Buscar um pedido e seus itens.

![foo bar](Imagens/Buscar%20um%20pedido%20e%20seus%20itens.png  "Pedido e os Itens"   )


>[!NOTE]
>
>Como fazer essa consulta?



>[!IMPORTANT]
>
> ````pk```` só pode usar ````=````<br>
> ````sk```` podemos usar ````>, <, =````

* Inverter as colunas (pk) e (sk)

>[!TIP]
>
>Usando um index secundário global ````"GSI"```` conseguimos inverter as colunas da ````pk```` e trazer uma coluna não chave.

```sql
sk='ORDER#5eaf12' and BEGINS_WITH(pk, 'ITEM#')
```

----

#### 4. Buscar pedidos de um usuário pelo status.

![foo bar](Imagens/Buscar%20Pedidos%20de%20um%20usuari%20pelo%20status.png)

* Criamos um novo index GSI

```sql
pk = 'USER#normandesjr' and BEGINS_WITH(status, 'PLACED#')
```

>[!NOTE]
>
> Podemos criar no máximo ````20```` índices ````GSI's```` por tabela.
> Muitos índices são bem complicados de se manter.
> 
>Os Índices ````GSI```` vão ter um tempo de ````(ms) milisegundos````de latência.
>
> É possivel ter a consulta fortemente consistente, porém será pago o preço por isso e não é barato.

<br>

----
<br>

Referência: 
<br>
[What is DynamoDB?](https://www.dynamodbguide.com/what-is-dynamo-db/)
<br>
[Como modelar com DynamoDB | ⚡ Zup Insights ](https://www.youtube.com/watch?v=kSnpuKr3Ajw  "DynamoDB"   )
<br>
[DynamoDB - Webfor Treinamentos](https://www.youtube.com/watch?v=bTLoK2eHwi4  "Como modelar com DynamoDB"   )
<br>
[What is Amazon DynamoDB?](https://docs.aws.amazon.com/pt_br/amazondynamodb/latest/developerguide/Introduction.html)
<br>
[Docker AWSCLI V1](https://github.com/normandesjr/awscliv1)
<br>
[Overloading Global Secondary Indexes](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-gsi-overloading.html)
<br>
[Take advantage of sparse indexes](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/bp-indexes-general-sparse-indexes.html)
<br>
[Conceitos básicos sobre DynamoDB | 📎 Zup Clipes ✂️](https://www.youtube.com/watch?v=9AkzibsIZUk)


----