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

#### Início (1)

- *Descrição:* Página inicial, aonde estão os cards que levam as outras páginas e também possui o nome do usuário atual logado.
  
- *Cards:* Usuários, Alterar Senha, Roles Atribuídas, Roles e Privilegiados. 
  - Cada card redireciona o usuário a página correspondente.

<hr>

####  Usuários (2)

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

#### Roles atribuídas (3)

- *Descrição:* Página aonde é possível visualizar as roles e a quais roles essas estão atribuidas no banco de dados, por meio da view `VW_GIS_ROLES_ATRIBUIDAS`. Usuário com permissão tem acesso a alteração de dados.
  
- *Botões (Restrito):* CRIAR, BLOQUEAR, EXCLUIR. 
  - *CRIAR*: Abre a página **Criar role (8)**.
  - *ATRIBUIR_REVOGAR*: Abre a página **Atribuir / Revogar role (10)**.
  - *EXCLUIR*: Abre a página **Excluir role (9)**.
- *Origem dos dados*: View `VW_GIS_ROLES_ATRIBUIDAS`, passado por parâmetro no campo Nome da Tabela, em Região > Origem >.  

<hr>

#### Criar usuário (4)

- *Descrição:* Nessa página é possível criar novos usuários passando por parâmetro o nome e e-mail.
  
- *Botões (Restrito):* 
  - *CRIAR:* Executa a Ação Dinâmica _Gerar senha_ e a Cadeia de Execução _Criação do usuário_.
  - *CANCELAR:* Fecha a caixa de diálogo.
- *Origem dos dados:* View `VW_GIS_ROLES_ATRIBUIDAS`, passado por parâmetro no campo Nome da Tabela, em Região > Origem >.  
