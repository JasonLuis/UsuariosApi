Existem três maneiras de resolver dependências:

* AddScoped;
* AddTransient;
* AddSingleton.

Nesse momento, vamos usar o `AddScoped` assim o `CadastroService` sempre vai ser instanciado quando houver uma requisição nova que demande uma instância de `CadastroService`. Com isso, se fazemos uma requisição nova e chamamos o `CadastroService`, vamos instanciar um novo.

Se utilizássemos o `AddSingleton` seria um único `CadastroService` para todas as requisições que chegassem, ou seja, seria a mesma instância.

Já o `AddTransient` vai fazer sempre uma instância nova, mesmo que seja na mesma requisição. Assim, se chega uma requisição e precisamos de uma instância de `CadastroService`, vamos instanciar uma nova. Mas, se chega uma requisição e precisamos do `CadastroService`, também vai instanciar uma nova.

Vamos usar o `AddScoped` já que não precisamos nos preocupar com essa questão de instanciar um novo ou manter um antigo, ou seja, o estado.