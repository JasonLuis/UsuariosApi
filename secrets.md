Como usaremos os Secrets? Primeiro, abriremos um Terminal na pasta do nosso projeto. Ao digitarmos "ls" e pressionarmos "Enter", identificaremos que estamos na raiz do projeto:

> Authorization/ Migrations/ Program.cs UsuariosApi.csproj bin/

> Controllers/ Models/ Properties/ appsettings.Development.json obj/

> Data/ Profiles/ Services/ appsettings.json

> daniel.mastub@note-mastub MINGw64 ~/source/repos/usuarioApi/usuariosApi (Aula-5)

Usaremos o comando "dotnet user-secrets init" para inicializar um Secret no nosso projeto.

Voltando ao VS Studio, encontraremos um `<UserSecretsId>` no arquivo "UsuariosApi". Se observarmos o diretório da Microsoft, ele define alguns `UserSecrets` na região do "AppData" no Windows.

Primeiro, criaremos um Secret para, em seguida, verificar o seu conteúdo. Em "Program.cs", moveremos o conteúdo da `SymmetricSecurityKey` para dentro de um Secret.

Para fazer isso, executaremos o comando "dotnet user-secrets set" no Terminal. Queremos criar um segredo chamado "SymmetricSecurityKey" com o valor da chave "9ASHDA98H9ah9ha9H9A89n0f". A linha completa a ser digitada no Terminal é a seguinte:

> $ dotnet user-secrets set "SymmetricSecurityKey" "9ASHDA98H9ah9ha9H9A89n0f"

A mensagem exibida no Terminal será:

> Successfully saved SymmetricSecurityKey = 9ASHDA98H9ah9ha9H9A89n0f to the secret store.

Se abrirmos o Explorador de Arquivos do Windows, poderemos encontrar uma pasta com o arquivo "secrets.json" ao percorrermos o seguinte caminho: "Este Computador > OS (C:) > Users > daniel.mastub > AppData > Roaming > Microsoft > UserSecrets".

Lembrando que esta é a sequência de pastas no computador do instrutor. Portanto, o nome de usuário será diferente na sua máquina.

Abrindo o arquivo "secrets.json", encontraremos o conteúdo da chave gravado em uma variável que será lida posteriormente:

```
{
	"SymmetricSecurityKey": "9ASHDA98H9ah9ha9H9A89n0f"
}
```

E como fazemos isso com o banco? Do mesmo modo: digitando "dotnet user-secrets set" no Terminal. No entanto, no arquivo appsettings.json, repare que temos uma `ConnectionString` dentro da qual há um `UsuarioConnection`. Como indicaremos esse encadeamento de configurações?

Primeiro, copiaremos e colaremos "ConnectionStrings" no Terminal, seguido de dois pontos e "UsuarioConnection". Tudo isso ficará entre aspas e sem espaços. Na sequência, colocamos o valor entre aspas. O resultado é o seguinte:

> $ dotnet user-secrets set "ConnectionStrings:UsuarioConnection" "server=localhost;database=usuariodb;user=root;password=root"

Após pressionar o "Enter", receberemos a mensagem abaixo:

> Successfully saved ConnectionStrings:UsuarioConnection = server=localhost;database=usuariodb;user=root;password=root to the secret store.

Se reabrirmos o arquivo "secrets.json" no Explorador de Arquivos, perceberemos que ele foi atualizado:

```
{
	"SymmetricSecurityKey": "9ASHDA98H9ah9ha9H9A89n0f"
	"ConnectionStrings:UsuarioConnection": "server=localhost;database=usuariodb;user=root;password=root"
}
```

E como usaremos o conteúdo deste arquivo? Acessaremos essas informações por meio do campo de configuração do arquivo "Program.cs".

Por isso, escreveremos `builder.Configuration` no lugar da chave de token presente no `SymmetricSecurityKey`, e indicaremos que desejamos obter a configuração chamada `"SymmetricSecurityKey"`.

```
// Trecho de código suprimido

ValidateIssuerSigningKey = true,
IssuerSigningKey = new SymmetricSecurityKey
	(Encoding.UTF8.GetBytes(builder.Configuration
	["SymmetricSecurityKey"])),
	
// Trecho de código suprimido
```

Faremos o mesmo para a `ConnectionString`:

```
// Trecho de código suprimido

var connString = builder.Configuration
	["SymmetricSecurityKey"];
	
// Trecho de código suprimido
```

Copiaremos a configuração "ConnectionStrings:UsuarioConnection" do arquivo "secrets.json" e a colaremos entre os colchetes, substituindo o `"SymmetricSecurityKey"`.

```
// Trecho de código suprimido

var connString = builder.Configuration
	["ConnectionStrings:UsuarioConnection"];
	
// Trecho de código suprimido
```

Por fim, faremos o mesmo no arquivo "TokenService.cs". Criaremos um construtor para o `TokenService()`. Com ele, poderemos inicializar a nossa configuração a partir do `_configuration` do próprio .NET.

Isso porque temos acesso via `builder` no "Program.cs", mas precisaremos fazer a injeção no "TokenService.cs".

```
public TokenService()
{
		_configuration = configuration;
}
```

Pressionando o atalho "CTRL + ." para resolver o erro em `_configuration`, clicaremos em "Gerar variável configuration" > "Gerar campo configuration".

Com isso, surgira um objeto privado acima do `public TokenService()`. Substituiremos a palavra `object` por `IConfiguration`:

```
private IConfiguration _configuration;
```

Logo abaixo, acrescentaremos o parâmetro `IConfiguration configuration` ao `TokenService()`. O resultado é o seguinte:

```
private IConfiguration _configuration;

public TokenService(IConfiguration configuration)
{
		_configuration = configuration;
}

```

Em seguida, desceremos até o `Encoding` e substituiremos a chave `"9ASHDA98H9ah9ha9H9A89n0f"` por `_configuration["SymmetricSecurityKey"]`, resultando em:

```
var chave = new SymmetricSecurityKey
	(Encoding.UTF8.GetBytes(_configuration
	["SymmetricSecurityKey"]));
```

Agora, executaremos a aplicação para validar o fluxo. Clicaremos no botão "Iniciar Sem Depurar" no topo da interface do VS Code ou pressionaremos o atalho "Ctrl + F5" no Windows ou "Option + Command + Enter" no Mac.

Faremos o cadastro de um usuário novo atualizando o Post para http://localhost:5212/usuario/Cadastro e cadastraremos o "Username": "paulo", que também nasceu em 2018 e tem a mesma senha dos usuários anteriores.

Clicaremos no botão "Send" para fazer o cadastro e, em seguida, alteraremos o Post para http://localhost:5212/usuario/login, clicando em "Send" na sequência.

Obteremos um token na parte inferior da interface. Por fim, vamos validá-lo copiando e colando o token na área de acesso. Obteremos o status "404 Not Found" porque a data de nascimento está em "2018-01-01". Se tentarmos fazer o login com algum usuário mais velho, como o "david", obteremos o status "200 OK".

Com isso, conseguimos retirar todas as informações sensíveis do código e as movemos para uma variável que será lida no momento da execução. Nos vemos na próxima!