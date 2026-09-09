# THM-Neighbour
## WriteUp PT-BR 🇧🇷

**TryHackMe — Neighbour CTF Walkthrough / WriteUp**

Neste desafio da TryHackMe, precisamos explorar uma vulnerabilidade conhecida como **IDOR (Insecure Direct Object Reference)**.

---

## 🎯 Objetivo

O objetivo do desafio é explorar uma vulnerabilidade **IDOR** para conseguir acessar informações de outro usuário sem possuir autorização adequada.

---

## 🚀 Iniciando o laboratório

Caso este seja seu primeiro CTF na TryHackMe, o primeiro passo é iniciar a máquina de laboratório.

Clique em **Start Machine** para iniciar a **LAB Machine**, que será o nosso alvo durante o desafio.

<img width="1296" height="809" alt="Iniciando a LAB Machine" src="https://github.com/user-attachments/assets/8c35a4a7-2734-48bd-847a-fe9a94b83c1b" />

Após iniciar a máquina, acessamos o endereço fornecido pela TryHackMe.

---

## 🔐 Página de Login

Como podemos ver, chegamos a uma página de login solicitando credenciais que, inicialmente, não temos.

Na parte inferior da página existe uma dica solicitando que pressionemos:

`Ctrl + U`

Esse atalho permite visualizar o **código-fonte da página (View Source)**.

<img width="956" height="722" alt="Página de login" src="https://github.com/user-attachments/assets/55bd375c-1b18-4394-a284-d9bb6c28ed08" />

---

## 🔎 Analisando o código-fonte

Ao utilizar **Ctrl + U** ou acessar **View Page Source**, podemos analisar o código HTML da página.

Durante a análise, encontramos as **credenciais fornecidas para acesso como usuário Guest**.

Com essas credenciais, podemos realizar o login normalmente.

---

## 👤 Login como Guest

Após realizar o login utilizando a conta **Guest**, somos direcionados para a página de perfil.

Observando a URL, encontramos algo semelhante a:

`http://10.10.***.***/profile.php?user=guest`

Aqui temos um ponto interessante.

O parâmetro GET:

`user=guest`

está sendo utilizado para determinar qual perfil será exibido.

---

## 🔓 Identificando a vulnerabilidade IDOR

Como sabemos que o objetivo deste desafio envolve uma vulnerabilidade **IDOR**, podemos testar se a aplicação valida corretamente se o usuário autenticado possui autorização para acessar outros perfis.

Atualmente temos:

`profile.php?user=guest`

Mas o que acontece se alterarmos diretamente o parâmetro `user`?

Vamos substituir:

`user=guest`

por:

`user=admin`

A URL ficará assim:

`http://10.10.***.***/profile.php?user=admin`

Digitamos a nova URL na barra de endereço e pressionamos **Enter**.

---

## 🚩 Acessando o perfil Admin

A aplicação permite acessar o perfil **admin**, mesmo estando autenticado apenas como usuário **Guest**.

Isso confirma a existência da vulnerabilidade **IDOR**.

A aplicação utiliza um identificador controlado pelo usuário (`user`) para acessar um objeto interno — neste caso, um perfil — sem verificar corretamente se o usuário autenticado possui autorização para acessar aquele recurso.

Como resultado, conseguimos acessar o perfil administrativo e encontrar a **flag do desafio**.

**BOOM! 🚩 Conseguimos a Flag!**

---

## 🧠 O que aprendemos?

O **IDOR (Insecure Direct Object Reference)** ocorre quando uma aplicação expõe uma referência direta a um recurso interno e não realiza uma verificação de autorização adequada antes de retornar esse recurso.

Neste desafio, a aplicação recebe o usuário através do parâmetro:

`?user=guest`

Ao alterarmos manualmente esse valor para:

`?user=admin`

o servidor retorna o perfil administrativo sem verificar se nossa sessão possui permissão para acessá-lo.

O problema, portanto, não está simplesmente no fato de conseguirmos modificar a URL. O problema real está na **ausência de uma validação de autorização no lado do servidor**.

### Exemplo vulnerável

```text
/profile.php?user=guest
```

Alterando para:

```text
/profile.php?user=admin
```

conseguimos acessar um recurso que deveria estar protegido.

---

## 🛡️ Como evitar IDOR?

Para prevenir esse tipo de vulnerabilidade, a aplicação deve realizar verificações de autorização no **backend** para cada recurso solicitado.

Por exemplo, antes de retornar determinado perfil, o servidor deve verificar se o usuário autenticado realmente possui permissão para acessar aquele recurso.

Apenas esconder identificadores, alterar nomes de parâmetros ou utilizar IDs mais difíceis de descobrir **não resolve o problema**. A autorização precisa ser validada pelo servidor.

---

## ✅ Conclusão

Esse foi um desafio simples, mas muito útil para compreender na prática como uma falha de **controle de acesso** pode permitir que um usuário visualize recursos pertencentes a outros usuários.

O principal ponto deste CTF é entender que:

> **Autenticação responde quem você é. Autorização determina o que você pode acessar.**

Neste caso, estávamos autenticados como **Guest**, mas a aplicação falhou ao verificar se tínhamos autorização para acessar o perfil **Admin**.

Obrigado à **TryHackMe** por disponibilizar ambientes práticos para estudo e desenvolvimento de habilidades em cibersegurança.

**Happy Hacking! 🚩**
