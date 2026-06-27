# DATABASE_SCHEMA.md — Projeto M.E.U.

## Banco de Dados

Tecnologia:

Firebase Firestore (NoSQL)

Estrutura baseada em collections.

---

# COLLECTION: products

Armazena todos os produtos do catálogo.

Caminho:

```txt
products/{productId}
```

Exemplo:

```javascript
{
    name:"Biscoito Sol Água e Sal",

    description:"Biscoito Sol Água e Sal 350g - embalagem com 20 unidades",

    category:"Alimentos",

    price:7.99,

    image:"https://...",

    available:true,

    stock:35,

    brand:"Sol",

    unit:"unidade",

    createdAt:Timestamp,

    updatedAt:Timestamp
}
```

Campos:

| Campo       | Tipo      | Obrigatório | Descrição             |
| ----------- | --------- | ----------- | --------------------- |
| name        | string    | SIM         | Nome do produto       |
| description | string    | SIM         | Descrição detalhada   |
| category    | string    | SIM         | Categoria do produto  |
| price       | number    | SIM         | Valor unitário        |
| image       | string    | NÃO         | URL imagem            |
| available   | boolean   | SIM         | Produto disponível    |
| stock       | number    | SIM         | Quantidade em estoque |
| brand       | string    | NÃO         | Marca                 |
| unit        | string    | NÃO         | unidade/kg/litro      |
| createdAt   | timestamp | SIM         | criação               |
| updatedAt   | timestamp | SIM         | atualização           |

Categorias aceitas:

* Alimentos
* Bebidas
* Casa
* Higiene
* Papelaria
* Roupas
* Outros

---

# COLLECTION: users

Usuários autenticados.

Usa Firebase Authentication.

Caminho:

```txt
users/{uid}
```

Exemplo:

```javascript
{
    uid:"firebase_uid",

    name:"João Pedro",

    email:"joao@email.com",

    phone:"44999999999",

    role:"customer",

    createdAt:Timestamp
}
```

Campos:

| Campo     | Tipo      |
| --------- | --------- |
| uid       | string    |
| name      | string    |
| email     | string    |
| phone     | string    |
| role      | string    |
| createdAt | timestamp |

Roles possíveis:

* customer
* admin

---

# COLLECTION: reservations

Reserva principal do sistema.

Caminho:

```txt
reservations/{reservationId}
```

Exemplo:

```javascript
{
    id:"49201",

    customer:{
        uid:"firebase_uid",

        name:"João Pedro",

        email:"joao@email.com",

        phone:"44999999999"
    },

    products:[
        {
            productId:"abc1",

            name:"Tomate cereja",

            image:"https://...",

            quantity:2,

            unitPrice:8.90,

            subtotal:17.80
        }
    ],

    subtotal:42.30,

    discount:0,

    total:42.30,

    paymentMethod:"Dinheiro",

    pickupDateTime:Timestamp,

    status:"pending",

    createdAt:Timestamp,

    updatedAt:Timestamp
}
```

---

## Subobjeto customer

Pode existir usuário logado OU visitante.

Estrutura:

```javascript
customer:{
    uid:null,

    name:"João Pedro",

    email:"",

    phone:"44999999999"
}
```

uid:

* string → usuário autenticado
* null → visitante

---

## Subarray products

Snapshot do produto no momento da compra.

IMPORTANTE:

Nunca depender somente do products collection.

Salvar snapshot.

Estrutura:

```javascript
{
    productId:"abc1",

    name:"Tomate cereja",

    image:"...",

    quantity:2,

    unitPrice:8.90,

    subtotal:17.80
}
```

---

## STATUS OFICIAIS (ENUM)

NÃO INVENTAR NOVOS STATUS.

Valores permitidos:

```txt
pending
preparing
ready
completed
cancelled
```

Descrição:

pending
→ reserva criada.

preparing
→ equipe separando itens.

ready
→ pronta para retirada.

completed
→ retirada concluída.

cancelled
→ reserva cancelada.

---

## MÉTODOS DE PAGAMENTO (ENUM)

Valores oficiais:

```txt
Dinheiro
Cartão de Débito
Cartão de Crédito
Pix
```

---

# LOCAL STORAGE ARCHITECTURE

Usado no frontend.

---

## cart

Carrinho ativo.

```javascript
[
    {
        id:"abc1",

        name:"Tomate cereja",

        image:"...",

        price:8.90,

        quantity:2
    }
]
```

---

## checkoutData

Dados temporários checkout.

```javascript
{
    cart:[...],

    subtotal:42.30,

    total:42.30,

    pickupDateTime:"2026-04-30T16:00:00"
}
```

---

## currentReservation

Reserva atual exibida no acompanhamento.

```javascript
{
    id:"49201",

    status:"pending",

    products:[...],

    total:42.30
}
```

---

## guestReservationInfo

Dados mínimos visitante.

```javascript
{
    name:"João Pedro",

    phone:"44999999999",

    email:""
}
```

---

# REGRAS DE NEGÓCIO

1.

Produto indisponível:

```txt
available=false
```

Não pode ser reservado.

---

2.

Produto sem estoque:

```txt
stock <= 0
```

deve marcar indisponível.

---

3.

Checkout exige:

OU

usuário logado

OU

nome + telefone.

---

4.

Reserva cancelada:

não pode voltar para pending.

---

5.

completed é estado final.

---

6.

Ao concluir reserva:

limpar:

```txt
cart
checkoutData
```

---

7.

Sempre salvar snapshot do produto dentro da reserva.

Nunca depender somente da collection products.

---

# FUTURA COLLECTION ADMIN (opcional)

```txt
admin_logs
notifications
inventory_history
```

