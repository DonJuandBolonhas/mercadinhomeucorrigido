# PROJECT_CONTEXT.md — Projeto M.E.U.

# VISÃO GERAL

M.E.U. é um sistema web de mercadinho universitário.

Permite:

* visualizar catálogo;
* visualizar detalhes de produtos;
* reservar produtos;
* gerenciar carrinho;
* agendar retirada;
* realizar checkout;
* escolher pagamento;
* acompanhar status da reserva.

---

# STACK OFICIAL

Frontend:

* HTML5
* CSS3
* JavaScript puro (Vanilla JS)

Backend:

* Firebase

Serviços Firebase:

* Firebase Authentication
* Firestore Database
* Firebase Hosting (futuro)

NÃO USAR:

* React
* NextJS
* Vue
* Angular
* Tailwind
* Bootstrap
* JQuery

Projeto baseado exclusivamente em:

HTML + CSS + JS puro.

---

# IDENTIDADE VISUAL

Tema visual obrigatório.

Background:

gradiente:

```txt
azul claro → bege suave
```

Footer:

```txt
verde escuro
```

Componentes:

* cards grandes
* cantos arredondados
* visual minimalista
* estilo institucional moderno

Tipografia:

```txt
Segoe UI
Roboto
Helvetica
Arial
```

---

# ARQUITETURA GERAL

Fluxo principal:

```txt
HOME
 ↓

CATÁLOGO
 ↓

PRODUTO
 ↓

CARRINHO
 ↓

CHECKOUT
 ↓

PEDIDO CONFIRMADO
 ↓

STATUS DA RESERVA
```

---

# ROTAS OFICIAIS

## Home

Arquivo:

```txt
index.html
```

Objetivo:

landing page principal.

---

## Catálogo

Arquivo:

```txt
catalogo.html
```

Objetivo:

listar produtos.

Origem dos dados:

Firestore.

Collection:

```txt
products
```

Funcionalidades:

* busca
* filtro categoria
* reservar produto
* abrir detalhes produto

Botões:

### Reservar

Adiciona produto ao:

```javascript
localStorage.cart
```

### Saber +

Redireciona:

```txt
produto.html?id={productId}
```

---

## Produto

Arquivo:

```txt
produto.html
```

Objetivo:

mostrar informações detalhadas.

Recebe:

```javascript
query param id
```

Exemplo:

```txt
produto.html?id=abc123
```

Busca produto:

```txt
Firestore products collection
```

Funções:

* carregar produto
* mostrar descrição
* preço
* imagem
* disponibilidade
* adicionar ao carrinho

Botão:

```txt
Adicionar ao carrinho
```

Atualiza:

```javascript
localStorage.cart
```

---

## Carrinho

Arquivo:

```txt
carrinho.html
```

Objetivo:

gerenciar produtos reservados.

Origem:

```javascript
localStorage.cart
```

Permite:

* aumentar quantidade
* diminuir quantidade
* remover item
* subtotal
* total

Também permite:

agendar retirada.

Campos:

* data
* horário

Gera:

```javascript
localStorage.checkoutData
```

Estrutura:

```javascript
{
    cart:[...],
    subtotal:42.30,
    discount:0,
    total:42.30,
    pickupDateTime:"2026-04-30T16:00"
}
```

Botão:

```txt
Finalizar Reserva
```

Redireciona:

```txt
checkout.html
```

---

## Checkout

Arquivo:

```txt
checkout.html
```

Objetivo:

finalizar reserva.

Origem:

```javascript
localStorage.checkoutData
```

---

### REGRA DE IDENTIFICAÇÃO

Usuário deve:

OU

estar logado.

OU

fornecer:

* nome
* telefone.

---

### Login

Firebase Authentication.

Métodos futuros:

* Email/Password
* Google Login

Sessão:

```javascript
Firebase Auth currentUser
```

---

### Visitante

Dados mínimos:

```javascript
localStorage.guestReservationInfo
```

Estrutura:

```javascript
{
    name:"",
    phone:"",
    email:""
}
```

---

### Pagamento

Métodos oficiais:

```txt
Dinheiro
Cartão de Débito
Cartão de Crédito
Pix
```

---

### Payload Final

Checkout cria:

```javascript
reservationPayload
```

Estrutura:

```javascript
{
    customer:{},
    products:[],
    subtotal:0,
    discount:0,
    total:0,
    paymentMethod:"",
    pickupDateTime:"",
    status:"pending",
    createdAt:""
}
```

Salvar:

Firestore collection:

```txt
reservations
```

Também salvar:

```javascript
localStorage.currentReservation
```

---

## Pedido Confirmado

Arquivo:

```txt
pedido-confirmado.html
```

Objetivo:

confirmar reserva criada.

Origem:

```javascript
localStorage.currentReservation
```

Exibe:

* status inicial
* resumo pedido
* total
* id reserva
* horário retirada

Botões:

```txt
Nova reserva
```

↓

```txt
catalogo.html
```

```txt
Ver reserva
```

↓

```txt
reserva-status.html
```

---

## Status da Reserva

Arquivo:

```txt
reserva-status.html
```

Objetivo:

acompanhar pedido.

Origem principal:

Firestore.

Fallback:

```javascript
localStorage.currentReservation
```

Exibe:

* status
* timeline
* produtos
* total
* pagamento
* cliente
* id pedido
* retirada

---

### STATUS OFICIAIS

NÃO INVENTAR NOVOS STATUS.

Valores permitidos:

```txt
pending
preparing
ready
completed
cancelled
```

---

### Significado

pending

→ reserva criada.

preparing

→ itens sendo separados.

ready

→ pronta retirada.

completed

→ retirada concluída.

cancelled

→ reserva cancelada.

---

# FIREBASE ARCHITECTURE

Configuração:

Firebase Modular SDK V9.

Imports padrão:

```javascript
import { initializeApp } from "firebase/app";
import { getFirestore } from "firebase/firestore";
import { getAuth } from "firebase/auth";
```

Arquivo futuro recomendado:

```txt
firebase.js
```

---

## firebase.js

Responsável por:

* initializeApp()
* Firestore instance
* Auth instance

Exportar:

```javascript
db
auth
```

---

# FIRESTORE COLLECTIONS

Usar schema definido em:

```txt
DATABASE_SCHEMA.md
```

Collections oficiais:

```txt
products
users
reservations
```

---

# LOCAL STORAGE ARCHITECTURE

## cart

Carrinho ativo.

---

## checkoutData

Dados intermediários checkout.

---

## currentReservation

Reserva atual.

---

## guestReservationInfo

Dados visitante.

---

# FLUXO COMPLETO DO CHECKOUT

CATÁLOGO:

produto reservado.

↓

salva em:

```txt
cart
```

↓

CARRINHO:

usuário ajusta itens.

↓

define retirada.

↓

gera:

```txt
checkoutData
```

↓

CHECKOUT:

usuário:

* login
  ou
* visitante

↓

seleciona pagamento.

↓

gera:

```txt
reservationPayload
```

↓

salva Firestore.

↓

salva:

```txt
currentReservation
```

↓

PEDIDO CONFIRMADO.

↓

STATUS DA RESERVA.

---

# REGRAS DE NEGÓCIO

Produto indisponível:

```txt
available=false
```

Não reservar.

---

Sem estoque:

```txt
stock<=0
```

Indisponível.

---

Reserva cancelada:

não volta para pending.

---

completed:

estado final.

---

Sempre salvar snapshot dos produtos dentro da reserva.

Nunca depender somente da collection products.

---

# PADRÕES DE CÓDIGO

Código obrigatório:

* organizado por seções
* comentários claros
* CSS interno permitido
* JS interno permitido
* sem dependências externas

Separar:

```txt
CONFIG
STATE
DOM
INIT
RENDER
EVENTS
HELPERS
FIREBASE
```

---

# FUTURAS FEATURES

Planejadas:

* login real Firebase
* painel admin
* gerenciamento estoque
* notificações
* histórico reservas
* PIX QRCode
* dashboard administrativo

---

# REGRA ABSOLUTA PARA IA

Não alterar:

* arquitetura
* collections
* enums
* rotas
* stack

Sem autorização explícita.

