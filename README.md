<h1 align="center">Interface de Controle</h1>
<p align="center"><i>Repositório para documentação do aplicativo</i></p>

##  Sobre

O aplicativo foi desenvolvido usando Oracle APEX na versão 23.1.4 e tem como objetivo principal simplificar a utilização das funcionalidades do Oracle Database 19c por meio de uma interface gráfica intuitiva. Ele proporciona visualizações de dados e botões que facilitam a manipulação das informações armazenadas no banco de dados.

## Estrutura do Software

### Aplicação APEX

- **Aplicativo 154 - Interface de Controle**
  - *Descrição:* Aplicação principal
  - *Páginas:* Página Global, Início, Usuários, Roles atribuídas, Criar usuário, Bloquear usuário, Excluir usuário, Alterar senha, Criar role, Excluir role, Atribuir / Revogar role, Role para role, Roles e privilegiados, Página de Log-in

- **Aplicativo 140 - Alterar senha**
  - *Descrição:* Aplicativo para alterar a senha do usuário. É acessado pelo e-mail enviado no aplicativo principal.
  - *Páginas:* Página Global, Alterar senha, Página de Log-in

<hr>

### Páginas APEX

#### Página de Log-in (9999)

- *Descrição:* Página para autenticação de usuários.
- *Campos:* P9999_USERNAME, P9999_PASSWORD
  - *USERNAME:* Nome de usuário do banco de dados.
  - *PASSWORD:* A senha estabelecida pelo usuário ou enviada por e-mail na criação do mesmo.
- *Botões:* LOGIN
  - *LOGIN:* Ao ser clicado, efetua os processos padrão de login do Oracle APEX.
- *Código implementado:*

```javascript
/*
  Esse código é responsável por substituir
  o caractere "." (ponto final) do campo USERNAME.
*/
apex.jQuery(apex.gPageContext$).on("apexpagesubmit", function() {
  const item = apex.item("P9999_USERNAME");
  item.setValue(item.getValue().replace('.', ''));
});
```   

<hr>

#### Início (1)

- *Descrição:* Página inicial, aonde estão os cards que levam as outras páginas e também possui o nome do usuário atual logado.
- *Cards:* Usuários, Alterar Senha, Roles Atribuídas, Roles e Privilegiados. 
  - Cada card redireciona o usuário a página correspondente.

<hr>

####  Usuários (2)

:shipit:
