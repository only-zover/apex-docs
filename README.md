<h1 align="center">Interface de Controle</h1>
<p align="center"><i>Repositório para documentação do aplicativo</i></p>
<br>

## Sumário

* [Sobre](#sobre)
* [Aplicação APEX](#aplicação-apex)
* [Páginas APEX](#páginas-apex)
   * [Página de Log-in](#página-de-log-in-9999)
   * [Início](#início-1)
   * [Usuários](#usuários-2)
   * [Roles atribuídas](#roles-atribuídas-3)
   * [Criar usuário](#criar-usuário-4)
   * [Bloquear usuário](#bloquear-usuário)

<hr>

##  Sobre

O aplicativo foi desenvolvido usando Oracle APEX na versão 23.1.4 e tem como objetivo principal simplificar a utilização das funcionalidades do Oracle Database 19c por meio de uma interface gráfica intuitiva. Ele proporciona visualizações de dados e botões que facilitam a manipulação das informações armazenadas no banco de dados.

## Aplicação APEX

- **Aplicativo 154 - Interface de Controle**
  - *Descrição:* Aplicação principal
  - *Páginas:* Página Global, Início, Usuários, Roles atribuídas, Criar usuário, Bloquear usuário, Excluir usuário, Alterar senha, Criar role, Excluir role, Atribuir / Revogar role, Role para role, Roles e privilegiados, Página de Log-in

- **Aplicativo 140 - Alterar senha**
  - *Descrição:* Aplicativo para alterar a senha do usuário. É acessado pelo e-mail enviado no aplicativo principal.
  - *Páginas:* Página Global, Alterar senha, Página de Log-in

<hr>

## Páginas APEX

### Página de Log-in (9999)

- *Descrição:* Página para autenticação de usuários.
  
- *Campos:*
  - *USERNAME:* Nome de usuário do banco de dados.
  - *PASSWORD:* A senha estabelecida pelo usuário ou enviada por e-mail na criação do mesmo.
- *Botões:*
  - *LOGIN:* Efetua os processos padrão de login do Oracle APEX.
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

### Início (1)

- *Descrição:* Página inicial, aonde estão os cards que levam as outras páginas e também possui o nome do usuário atual logado.
  
- *Cards:* Usuários, Alterar Senha, Roles Atribuídas, Roles e Privilegiados. 
  - Cada card redireciona o usuário a página correspondente.

<hr>

###  Usuários (2)

- *Descrição:* Página aonde é possível visualizar os usuários do banco de dados da tabela `dba_roles`. Usuário com permissão tem acesso a alteração de dados.
  
- *Botões (Restrito):* 
  - *CRIAR:* Abre a página **Criar usuário (4)**.
  - *BLOQUEAR / Desbloquear:* Abre a página **Bloquear usuário (5)**.
  - *EXCLUIR:* Abre a página **Excluir usuário (6)**.
- *Origem dos dados:* 

```sql
/*
  Para conseguir os dados de usuários foi feita a seguinte consulta SQL em
  Região > Origem > Consulta SQL, a partir da região Administração de usuários.
*/
SELECT *
FROM dba_users
ORDER BY username ASC;
```

<hr>

### Roles atribuídas (3)

- *Descrição:* Página aonde é possível visualizar as roles e a quais roles essas estão atribuidas no banco de dados, por meio da view `VW_GIS_ROLES_ATRIBUIDAS`. Usuário com permissão tem acesso a alteração de dados.
  
- *Botões (Restrito):* CRIAR, BLOQUEAR, EXCLUIR. 
  - *CRIAR*: Abre a página **Criar role (8)**.
  - *ATRIBUIR_REVOGAR*: Abre a página **Atribuir / Revogar role (10)**.
  - *EXCLUIR*: Abre a página **Excluir role (9)**.
- *Origem dos dados*: View `VW_GIS_ROLES_ATRIBUIDAS`, passado por parâmetro no campo Nome da Tabela, em Região > Origem >.  

<hr>

### Criar usuário (4)

- *Descrição:* Nessa página é possível criar novos usuários passando por parâmetro o nome e e-mail.
  
- *Botões (Restrito):* 
  - *CRIAR:* Executa a Ação Dinâmica _Gerar senha_ e a Cadeia de Execução _Criação do usuário_.
  - *CANCELAR:* Fecha a caixa de diálogo.
- *Implementações:*
  - *Escrever e-mail no input (Definir valor):* Responsável por escrever automáticamente o nome de usuário e o dominio no campo de e-mail.
  ```javascript
  apex.item('P4_USUARIO').getValue() + '@londrina.pr.gov.br'
  ```
  - *Gerar senha (Definir valor):* é responsável por gerar um senha aleatória de 10 caracteres, possuindo letras e números.
  ```javascript
  Math.random().toString(36).slice(-10);
  ```
  - *Se usuário existe (Validação):* Verifica se o usuário já existe no banco de dados.
  ```sql
  SELECT null
  FROM dba_users
  WHERE username = UPPER(:P4_USUARIO);
  ```
  - *Criar usuário (Processo Executar Código):* Executa a função de criar usuário no banco de dados. Faz parte da cadeia de execução Criação do usuário.
  ```sql
  pk_gis.criar_usuario(:P4_USUARIO, :P4_SENHA);
  ```
  - *Enviar e-mail (Processo Enviar E-mail):* Envia um e-mail utilizando parametros específicos.
  ![image](https://github.com/only-zover/apex-docs/assets/78991372/d9705103-cd27-476e-a9c9-eb555f502abc)
  ![image](https://github.com/only-zover/apex-docs/assets/78991372/8095119b-6f13-4732-a61e-0feb2aaf0af0)
  - Em Componentes Compartilhados > Modelos de E-mail > Email novo usuario:
  ![image](https://github.com/only-zover/apex-docs/assets/78991372/745ba4ec-66b4-4338-96f4-eb37a75ccc6c)
  ```html
  <!--Esse código vai em: Cabeçalho-->
  <b style="font-size: 24px;">Alteração de senha</b>
  <!--Corpo do Texto-->
  <div>
  <h1>Seu usuário foi criado.</h1>
  <p>
    <b>Nome de usuário:</b> #USUARIO#
    <br>
    <b>Senha:</b> #SENHA#
  </p>
  <h2>Clique no botão abaixo para alterar sua senha padrão.</h2>
  <br>
  <a href="https://appdev.londrina.pr.gov.br/app/r/siglon/alterar-senha/"><button>Alterar senha</button></a>
  </div>
  <style>
    button {
        margin-top: 0.5rem;
        padding: 0.6rem;
        width: 21rem;
        font-weight: bold;
    }

    div {
        text-align: center;
    }

    a button {
        display: inline-block;
        padding: 10px 20px;
        background-color: #3498db;
        color: #fff;
        font-size: 16px;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        text-decoration: none; /* Remova o sublinhado do link */
        transition: background-color 0.3s;
    }

    /* Altere a cor de fundo ao passar o mouse */
    a button:hover {
        background-color: #2980b9;
    }
  </style>
  ```

  <br>

### Bloquear usuário

- *Item P5_USUARIO (Lista de seleção):* Seleciona o usuário para ser bloqueado baseado em uma consulta SQL.
  ```SQL
  SELECT username u, username a
  FROM dba_users
  WHERE default_tablespace = 'SDE_DATA'
  ORDER BY 1;
  ```

- *Bloquear usuário (Processo Executar código):* Executa o função para bloquear usuário.
```sql
pk_gis.bloquear_usuario(:P5_USUARIO);
```

- *Desbloquear usuário (Processo Executar código): Executa o função para desbloquear usuário.*
```sql
pk_gis.desbloquear_usuario(:P5_USUARIO);
```
