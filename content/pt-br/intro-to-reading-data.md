---
title: Lendo dados da rede Solana
objectives:
- Entender contas e seus endereços
- Entender SOL e lamports
- Usar web3.js para conectar-se a Solana e ler o saldo de uma conta
---

## Resumo

- **SOL** é o nome do token nativo da Solana. Cada SOL é feito de 1 bilhão de **Lamports**.
- **Contas** armazenam tokens, NFTs, programas e dados. Por enquanto, vamos nos concentrar em contas que armazenam SOL.
- **Endereços** apontam para contas na rede Solana. Qualquer pessoa pode ler os dados em um determinado endereço. A maioria dos endereços também são **chaves públicas**

# Visão Geral

## Contas

Todos os dados da Solana são armazenados em contas. Contas podem armazenar:

- SOL
- Outros tokens, como USDC
- NFTs
- Programas, como o programa de avaliação de filmes que desenvolvemos neste curso!
- Dados de programas, como a avaliação de um determinado filme para o programa acima!

### SOL

SOL é o token nativo da Solana. SOL é usado para pagar as taxas de transações, pagar o aluguel de contas e muito mais. SOL pode ser representado com o símbolo `◎`. Cada SOL é composto por 1 bilhão de **Lamports**. Da mesma forma como apps de finança fazem cálculos em centavos (para dólares) ou pence (para libras), apps da Solana geralmente fazem cálculos usando Lamports e convertem para SOL apenas para exibir dados.

### Endereços

Endereços são identificadores únicos de contas. Endereços são geralmente exibidos como strings codificadas em base-58 como `dDCQNnDmNbFVi8cQhKAgXhyhXeJ625tvwsunRyRc7c8`. A maioria dos endereços na Solana também são **chaves públicas**. Como mencionado no capítulo anterior, quem controla a chave secreta correspondente controla a conta - por exemplo, a pessoa com a chave secreta pode enviar tokens da conta.

## Lendo do Blockchain Solana

### Instalação

Nós usamos um pacote npm chamado `@solana/web3.js` para fazer a maior parte das interações com Solana. Também vamos instalar o TypeScript e o esrun para que possamos executar a linha de comando:

```bash
npm install typescript @solana/web3.js @digitak/esrun 
```

### Conectando-se à rede

Cada interação com a rede Solana usando `@solana/web3.js` vai acontecer através de um objeto `Connection`. O objeto `Connection` estabelece uma conexão com uma rede Solana específica, chamada de 'cluster'.

Por enquanto nós vamos usar o cluster `Devnet` ao invés de `Mainnet`. Como o nome sugere, o cluster `Devnet` é designado para uso de desenvolvedores e testes.

```typescript
import { Connection, clusterApiUrl } from "@solana/web3.js";

const connection = new Connection(clusterApiUrl("devnet"));
console.log(`✅ Conectado!`)
```

Executando esse TypeScript (`npx esrun example.ts`) mostra:

```
✅ Conectado!
```

### Lendo da rede

To read the balance of an account:
Para obter o saldo de uma conta:

```typescrip
import { Connection, PublicKey, clusterApiUrl } from "@solana/web3.js";

const connection = new Connection(clusterApiUrl("devnet"));
const address = new PublicKey('CenYq6bDRB7p73EjsPEpiYN7uveyPUTdXkDkgUduboaN');
const balance = await connection.getBalance(address);

console.log(`O saldo da conta ${address} é ${balance} lamports`); 
console.log(`✅ Concluído!`)
```

O saldo retornado está em *lamports*. Um lamport é a menor unidade de SOL, como centavos são para dólares americanos ou pence é para libras britânicas. Um único lamport representa 0,000000001 SOL. Na maioria das vezes, transferimos, gastamos, armazenamos e manipulamos SOL como Lamports, convertendo para SOL apenas para exibir para os usuários. Web3.js fornece a constante `LAMPORTS_PER_SOL` para fazer conversões rápidas.

```typescript
import { Connection, PublicKey, clusterApiUrl, LAMPORTS_PER_SOL } from "@solana/web3.js";

const connection = new Connection(clusterApiUrl("devnet"));
const address = new PublicKey('CenYq6bDRB7p73EjsPEpiYN7uveyPUTdXkDkgUduboaN');
const balance = await connection.getBalance(address);
const balanceInSol = balance / LAMPORTS_PER_SOL;

console.log(`O saldo da conta ${address} é ${balanceInSol} SOL`); 
console.log(`✅ Concluído!`)
```

Executando `npx esrun example.ts` vai exibir:

```
O saldo da conta CenYq6bDRB7p73EjsPEpiYN7uveyPUTdXkDkgUduboaN é 0.00114144 SOL
✅ Concluído!
```

...e assim, nós estamos lendo dados da rede Solana!

# Laboratório

Vamos praticar o que aprendemos, criando um site simples que permite que os usuários consultem o saldo de um determinado endereço.

O resultado final vai ser parecido com isso:

![Screenshot of demo solution](../../assets/intro-frontend-demo.png)

Para nos mantermos no tópico, não vamos começar do zero, então [baixe o código inicial](https://github.com/Unboxed-Software/solana-intro-frontend/tree/starter). O projeto inicial usa Next.js e Typescript. Se você está acostumado com outras tecnologias, não se preocupe! Os princípios da web3 e Solana que você aprenderá durante essas lições são aplicáveis a qualquer tecnologia frontend com a qual você esteja mais confortável.

### 1. Familiarize-se

Assim que você obter o código inicial, dê uma explorada. Instale as dependências com `npm install` e execute com `npm run dev`. Perceba que não importa o que você coloque no campo de endereço, quando clicar em "Check SOL Balance" o saldo será um valor fixo de 1000.

Estruturalmente, o app é composto por `index.tsx` e `AddressForm.tsx`. Quando um usuário envia o formulário, a função `addressSubmittedHandler` em `index.tsx` é chamada. É aqui que vamos adicionar a lógica para atualizar o resto da interface.

### 2. Instale as dependências

Use `npm install @solana/web3.js` para instalar nossa dependência na biblioteca web3 da Solana.

### 3. Defina o saldo do endereço

Primeiro, importe `@solana/web3.js` no topo do arquivo `index.tsx`

Agora que a biblioteca está disponível, vamos para a função `addressSubmittedHandler()` e criar uma instância de `PublicKey` usando o valor do endereço do formulário. Em seguida, criamos uma instância de `Connection` e usamos isso para chamar `getBalance()`. Passe o valor da chave pública que você acabou de criar. Para concluir, chamamos `setBalance()`, passando o resultado de `getBalance`. Se você estiver pronto para isso, tente fazer sem copiar o trecho de código abaixo.

```typescript
import type { NextPage } from 'next'
import { useState } from 'react'
import styles from '../styles/Home.module.css'
import AddressForm from '../components/AddressForm'
import * as web3 from '@solana/web3.js'

const Home: NextPage = () => {
  const [balance, setBalance] = useState(0)
  const [address, setAddress] = useState('')

  const addressSubmittedHandler = async (address: string) => {
    setAddress(address)
    const key = new web3.PublicKey(address)
    const connection = new web3.Connection(web3.clusterApiUrl('devnet'));
    const balance = await connection.getBalance(key);
    setBalance(balance / web3.LAMPORTS_PER_SOL);
  }
  ...
}
```

Na maioria das vezes que estamos lidando com SOL, o sistema vai usar lamports ao invés de SOL. Como computadores lidam melhor com números inteiros do que frações, normalmente vamos fazer a maior parte das nossas transações com lamports, convertendo para SOL apenas para exibir o resultado para os usuário. É por isso que pegamos o saldo retornado pela Solana e dividimos por `LAMPORTS_PER_SOL`.

Antes de definir o estado, também convertemos para SOL usando a constante `LAMPORTS_PER_SOL`.

Nesse ponto você deve ser capaz de colocar um endereço válido no campo do formulário e clicar em "Check SOL Balance" para ver tanto o endereço quanto o saldo abaixo dele.

### 4. Lidando com endereços inválidos

Estamos quase concluindo. O único problema restante é que ao usar um endereço inválido, não exibimos nenhuma mensagem de erro ou alteramos o saldo exibido. Se você abrir o console de desenvolvedor, verá a mensagem `Error: Invalid public key input`. Ao usar o construtor `PublicKey`, você precisa passar um endereço válido ou receberá esse erro.

Para resolver isso, vamos envolver tudo em um bloco `try-catch` e alertar o usuário se o endereço for inválida.

```typescript
const addressSubmittedHandler = async (address: string) => {
  try {
    setAddress(address);
    const key = new web3.PublicKey(address);
    const connection = new web3.Connection(web3.clusterApiUrl("devnet"));
    const balance = await connection.getBalance(key)
    setBalance(balance / web3.LAMPORTS_PER_SOL);
  } catch (error) {
    setAddress("");
    setBalance(0);
    alert(error);
  }
};
```

Perceba que no block `catch` nós também limpamos o endereço e o saldo para evitar confusão.

Nós conseguimos! Agora temos um site funcional que lê o saldo SOL da rede Solana. Você está no caminho certo para alcançar suas metas ambiciosas na Solana. Se você precisar gastar um pouco mais de tempo conferindo o código para entendê-lo, dê uma olhada no [código da solução completa](https://github.com/Unboxed-Software/solana-intro-frontend). Segure firme, essas lições vão acelerar rapidamente.

# Desafio

Como esse é o primeiro desafio, vamos mantê-lo simples. Vá em frente e adicione ao frontend que já criamos uma nova linha abaixo de "Balance". Faça com que essa linha exiba se a conta é ou não uma conta executável. Dica: existe um método chamado `getAccountInfo()`.

Como estamos usando a DevNet, o endereço da sua carteira da mainnet _não_ será executável, então se você quiser um endereço que _seja_ executável para testes, use `CenYq6bDRB7p73EjsPEpiYN7uveyPUTdXkDkgUduboaN`.

![Screenshot da solução do desafio](../../assets/intro-frontend-challenge.png)

Se você ficar travado, fique a vontade para ver o [código da solução](https://github.com/Unboxed-Software/solana-intro-frontend/tree/challenge-solution).
