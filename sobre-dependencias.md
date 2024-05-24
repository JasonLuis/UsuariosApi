Existem tr�s maneiras de resolver depend�ncias:

* AddScoped;
* AddTransient;
* AddSingleton.

Nesse momento, vamos usar o `AddScoped` assim o `CadastroService` sempre vai ser instanciado quando houver uma requisi��o nova que demande uma inst�ncia de `CadastroService`. Com isso, se fazemos uma requisi��o nova e chamamos o `CadastroService`, vamos instanciar um novo.

Se utiliz�ssemos o `AddSingleton` seria um �nico `CadastroService` para todas as requisi��es que chegassem, ou seja, seria a mesma inst�ncia.

J� o `AddTransient` vai fazer sempre uma inst�ncia nova, mesmo que seja na mesma requisi��o. Assim, se chega uma requisi��o e precisamos de uma inst�ncia de `CadastroService`, vamos instanciar uma nova. Mas, se chega uma requisi��o e precisamos do `CadastroService`, tamb�m vai instanciar uma nova.

Vamos usar o `AddScoped` j� que n�o precisamos nos preocupar com essa quest�o de instanciar um novo ou manter um antigo, ou seja, o estado.