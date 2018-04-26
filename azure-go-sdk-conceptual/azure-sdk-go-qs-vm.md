---
title: Implementar uma máquina virtual do Azure a partir do Go
description: Implemente uma máquina virtual utilizando o Azure SDK para Go.
author: sptramer
ms.author: sttramer
ms.date: 04/03/2018
ms.topic: quickstart
ms.devlang: go
manager: carmonm
ms.openlocfilehash: 565580e9e6c6ced543bd00bbaa01383834d9a41c
ms.sourcegitcommit: 2b2884ea7673c95ba45b3d6eec647200e75bfc5b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2018
---
# <a name="quickstart-deploy-an-azure-virtual-machine-from-a-template-with-the-azure-sdk-for-go"></a>Início rápido: implementar uma máquina virtual do Azure a partir de um modelo com o Azure SDK for Go

Este guia de início rápido centra-se na implementação de recursos a partir de um modelo com o Azure SDK para Go. Os modelos são os instantâneos de todos os recursos contidos dentro de um [grupo de recursos do Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview). Ao longo do percurso, fica familiarizado com a funcionalidade e convenções do SDK ao executar uma tarefa útil.

No final deste início rápido, tem uma VM em execução na qual inicia a sessão com um nome de utilizador e palavra-passe.

[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

Se utilizar uma instalação local da CLI do Azure, este início rápido necessita da versão __2.0.28__ da CLI ou posterior. Execute `az --version` para certificar-se de que a sua instalação da CLI cumpre este requisito. Se precisar de instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli).

## <a name="install-the-azure-sdk-for-go"></a>Instale o Azure SDK para Go 

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

## <a name="create-a-service-principal"></a>Criar um principal de serviço


Para iniciar sessão com uma aplicação de forma não interativa, precisa de um principal de serviço. Os principais de serviço fazem parte do controlo de acesso baseado em funções (RBAC) que cria uma identidade de utilizador exclusiva. Para criar um novo principal de serviço com a CLI, execute o seguinte comando:

```azurecli-interactive
az ad sp create-for-rbac --name az-go-vm-quickstart --sdk-auth > quickstart.auth
```

Defina a variável de ambiente `AZURE_AUTH_LOCATION` para ser o caminho completo para este ficheiro. Em seguida, o SDK localiza e lê as credenciais diretamente a partir deste ficheiro, sem que tenha de fazer quaisquer alterações ou registar informações do principal de serviço.

## <a name="get-the-code"></a>Obter o código

Obter o código de início rápido e todas as dependências com `go get`.

```bash
go get -u -d github.com/azure-samples/azure-sdk-for-go-samples/quickstarts/deploy-vm/...
```

Não precisa de modificar o código de origem se a variável `AZURE_AUTH_LOCATION` estiver definida corretamente. Quando o programa é executado, carrega todas as informações de autenticação necessárias a partir daí.

## <a name="running-the-code"></a>Executar o código

Execute o início rápido com o comando `go run`.

```bash
cd $GOPATH/src/github.com/azure-samples/azure-sdk-for-go-samples/quickstarts/deploy-vm
go run main.go
```

Se existir uma falha na implementação, recebe uma mensagem a indicar que houve um problema, mas esta poderá não incluir detalhes suficientes. Com a CLI do Azure, obtenha os detalhes completos da falha de implementação com o seguinte comando:

```azurecli-interactive
az group deployment show -g GoVMQuickstart -n VMDeployQuickstart
```

Se a implementação for bem sucedida, vai ver uma mensagem com o nome de utilizador, endereço IP e a palavra-passe para registar na máquina virtual recém-criada. SSH a esta máquina para confirmar que está operacional e em funcionamento.

## <a name="cleaning-up"></a>Limpeza

Limpe os recursos criados durante o início rápido ao eliminar o grupo de recursos com a CLI.

```azurecli-interactive
az group delete -n GoVMQuickstart
```

## <a name="code-in-depth"></a>Programe em profundidade

O que o código de início rápido faz está num bloco de variáveis e de várias funções pequenas, cada das quais é abordada aqui.

### <a name="variables-constants-and-types"></a>Variáveis, constantes e tipos

Uma vez que o início rápido é autónomo, utiliza constantes e variáveis globais.

```go
const (
    resourceGroupName     = "GoVMQuickstart"
    resourceGroupLocation = "eastus"

    deploymentName = "VMDeployQuickstart"
    templateFile   = "vm-quickstart-template.json"
    parametersFile = "vm-quickstart-params.json"
)

// Information loaded from the authorization file to identify the client
type clientInfo struct {
    SubscriptionID string
    VMPassword     string
}

var (
    ctx        = context.Background()
    clientData clientInfo
    authorizer autorest.Authorizer
)
```

Os valores são declarados, o que dá os nomes dos recursos criados. A localização também está especificada aqui, que pode alterar para ver como as implementações se comportam noutros datacenters. Nem todos os datacenters têm todos os recursos necessários disponíveis.

O tipo `clientInfo` é declarado para encapsular todas as informações que têm de ser carregadas independentemente a partir do ficheiro de autenticação para configurar clientes no SDK e definir a palavra-passe da VM.

As constantes `templateFile` e `parametersFile` apontam para os ficheiros necessários para a implementação. O valor `authorizer` será configurado pelo SDK do Go para autenticação e a variável `ctx` é um [Contexto do Go](https://blog.golang.org/context) para as operações de rede.

### <a name="authentication-and-initialization"></a>Autenticação e inicialização

A função `init` configura a autenticação. Dado que a autenticação é uma condição prévia para tudo o que está no início rápido, faz sentido tê-la como parte da inicialização. Também carrega algumas informações necessárias a partir do ficheiro de autenticação para configurar os clientes e a VM.

```go
func init() {
    var err error
    authorizer, err = auth.NewAuthorizerFromFile(azure.PublicCloud.ResourceManagerEndpoint)
    if err != nil {
        log.Fatalf("Failed to get OAuth config: %v", err)
    }

    authInfo, err := readJSON(os.Getenv("AZURE_AUTH_LOCATION"))
    clientData.SubscriptionID = (*authInfo)["subscriptionId"].(string)
    clientData.VMPassword = (*authInfo)["clientSecret"].(string)
}
```

Primeiro, [auth.NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile) é chamado para carregar as informações de autenticação a partir do ficheiro que se encontra em `AZURE_AUTH_LOCATION`. Em seguida, este ficheiro é carregado manualmente pela função `readJSON` (omitida aqui) para solicitar os dois valores necessários à execução do resto do programa: o ID de subscrição do cliente e o segredo do principal de serviço, que também é utilizado para a palavra-passe da VM.

> [!WARNING]
> Para manter este início rápido simples, a palavra-passe do principal de serviço é reutilizada. Na produção, certifique-se de que __nunca__ reutiliza uma palavra-passe que dê acesso aos seus recursos do Azure.

### <a name="flow-of-operations-in-main"></a>Fluxo de operações em main()

A função `main` é simples, apenas indica o fluxo de operações e realiza uma verificação de erros.

```go
func main() {
    group, err := createGroup()
    if err != nil {
        log.Fatalf("failed to create group: %v", err)
    }
    log.Printf("Created group: %v", *group.Name)

    log.Printf("Starting deployment: %s", deploymentName)
    result, err := createDeployment()
    if err != nil {
        log.Fatalf("Failed to deploy: %v", err)
    }
    if result.Name != nil {
        log.Printf("Completed deployment %v: %v", deploymentName, *result.Properties.ProvisioningState)
    } else {
        log.Printf("Completed deployment %v (no data returned to SDK)", deploymentName)
    }
    getLogin()
}
```

Os passos que o código executa são, por ordem:

* Criar o grupo de recursos para o qual implementar (`createGroup`)
* Criar a implementação dentro deste grupo (`createDeployment`)
* Obter e exibir informações de início de sessão para a VM implementada (`getLogin`)

### <a name="creating-the-resource-group"></a>Criar o grupo de recursos

A função `createGroup` cria o grupo de recursos. Ao olhar para o fluxo e argumentos de chamada demonstra a forma como as interações de serviço estão estruturadas no SDK.

```go
func createGroup() (group resources.Group, err error) {
    groupsClient := resources.NewGroupsClient(clientData.SubscriptionID)
    groupsClient.Authorizer = authorizer

        return groupsClient.CreateOrUpdate(
                ctx,
                resourceGroupName,
                resources.Group{
                        Location: to.StringPtr(resourceGroupLocation)})
}
```

O fluxo geral de interação com um serviço do Azure é:

* Criar o cliente com o método `service.New*Client()`, em que `*` é o tipo de recursos de `service` com o qual quer interagir. Esta função recebe sempre um ID de subscrição.
* Defina o método de autorização para o cliente, permitindo que interaja com a API remota.
* Faça a chamada de método no cliente correspondente à API remota. Os métodos do cliente do serviço normalmente aceitam o nome do recurso e um objeto de metadados.

A função [`to.StringPtr`](https://godoc.org/github.com/Azure/go-autorest/autorest/to#StringPtr) é utilizada para realizar uma conversão de tipo aqui. Os parâmetros dos métodos de SDK quase exclusivamente aceitam ponteiros, pelo que são fornecidos métodos de conveniência para facilitar as conversões de tipo. Veja a documentação do módulo [autorest/to](https://godoc.org/github.com/Azure/go-autorest/autorest/to) para obter a lista completa de conversores de conveniência e o respetivo comportamento.

O método `groupsClient.CreateOrUpdate` devolve um ponteiro para um tipo de dados que representa o grupo de recursos. Um valor de retorno direto deste tipo indica uma operação de curto-prazo que deverá ser síncrona. Na secção seguinte, verá um exemplo de uma operação de longa duração e como interagir com ela.

### <a name="performing-the-deployment"></a>Realizar a implementação

Uma vez criado o grupo de recursos, é o momento de executar a implementação. Este código é dividido em várias secções mais pequenas para dar ênfase a partes diferentes da sua lógica.

```go
func createDeployment() (deployment resources.DeploymentExtended, err error) {
    template, err := readJSON(templateFile)
    if err != nil {
        return
    }
    params, err := readJSON(parametersFile)
    if err != nil {
        return
    }
    (*params)["vm_password"] = map[string]string{
        "value": clientData.VMPassword,
    }
        // ...
```

Os ficheiros de implementação são carregados por `readJSON`, cujos detalhes são ignorados aqui. Esta função devolve um `*map[string]interface{}`, o tipo utilizado para construir os metadados para a chamada de implementação dos recursos. A palavra-passe da VM também é definida manualmente nos parâmetros da implementação.

```go
        // ...

    deploymentsClient := resources.NewDeploymentsClient(clientData.SubscriptionID)
    deploymentsClient.Authorizer = authorizer

    deploymentFuture, err := deploymentsClient.CreateOrUpdate(
        ctx,
        resourceGroupName,
        deploymentName,
        resources.Deployment{
            Properties: &resources.DeploymentProperties{
                Template:   template,
                Parameters: params,
                Mode:       resources.Incremental,
            },
        },
    )
    if err != nil {
        return
    }
```

Este código segue o mesmo padrão da criação do grupo de recursos. É criado um novo cliente, dada a capacidade de autenticar com o Azure e, em seguida, é chamado um método. O método tem inclusive o mesmo nome (`CreateOrUpdate`) do método correspondente para os grupos de recursos. Este padrão é utilizado em todo o SDK. Os métodos que realizam trabalho semelhante costumam ter o mesmo nome.

A grande diferença está no valor de retorno do método `deploymentsClient.CreateOrUpdate`. Este valor é do tipo [Futuro](https://godoc.org/github.com/Azure/go-autorest/autorest/azure#Future), que segue o [padrão de design futuro](https://en.wikipedia.org/wiki/Futures_and_promises). Os futuros representam uma operação de longa duração no Azure que pode consultar, cancelar ou bloquear após a respetiva conclusão.

```go
        //...
    err = deploymentFuture.Future.WaitForCompletion(ctx, deploymentsClient.BaseClient.Client)
    if err != nil {
        return
    }
    deployment, err = deploymentFuture.Result(deploymentsClient)

    // Work around possible bugs or late-stage failures
    if deployment.Name == nil || err != nil {
        deployment, _ = deploymentsClient.Get(ctx, resourceGroupName, deploymentName)
    }
    return
```

Neste exemplo, o melhor a fazer é aguardar que a operação seja concluída. Aguardar por um futuro requer um [objeto de contexto](https://blog.golang.org/context) e o cliente que criou o `Future`. Existem duas possíveis origens do erro: um erro causado do lado do cliente ao tentar invocar o método, e uma resposta de erro do servidor. A última opção é devolvida como parte da chamada `deploymentFuture.Result`.

Uma vez obtidas as informações de implementação, há uma solução para possíveis erros, em que as informações de implementação possam estar vazias, com uma chamada manual para `deploymentsClient.Get` para garantir que os dados são povoados.

### <a name="obtaining-the-assigned-ip-address"></a>Obter o endereço IP atribuído

Para fazer alguma coisa com a VM recém-criada, necessita do endereço IP atribuído. Os endereços IP são os seus próprios recursos do Azure, vinculados aos recursos do Controlador de Interface de Rede (NIC).

```go
func getLogin() {
    params, err := readJSON(parametersFile)
    if err != nil {
        log.Fatalf("Unable to read parameters. Get login information with `az network public-ip list -g %s", resourceGroupName)
    }

    addressClient := network.NewPublicIPAddressesClient(clientData.SubscriptionID)
    addressClient.Authorizer = authorizer
    ipName := (*params)["publicIPAddresses_QuickstartVM_ip_name"].(map[string]interface{})
    ipAddress, err := addressClient.Get(ctx, resourceGroupName, ipName["value"].(string), "")
    if err != nil {
        log.Fatalf("Unable to get IP information. Try using `az network public-ip list -g %s", resourceGroupName)
    }

    vmUser := (*params)["vm_user"].(map[string]interface{})

    log.Printf("Log in with ssh: %s@%s, password: %s",
        vmUser["value"].(string),
        *ipAddress.PublicIPAddressPropertiesFormat.IPAddress,
        clientData.VMPassword)
}
```

Este método depende das informações armazenadas no ficheiro de parâmetros. O código pode consultar diretamente a VM para obter o seu NIC, consultar o NIC para obter o seu recurso de IP e depois consultar o recurso de IP diretamente. Trata-se de uma longa cadeia de dependências e operações a resolver, o que a torna dispendiosa. Dado que a informação de JSON é local, pode ser carregada como alternativa.

O valor para o utilizador da VM também é carregado a partir do JSON. A palavra-passe da VM foi carregada anteriormente a partir do ficheiro de autenticação.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, utilizou um modelo existente e implementou-o através do Go. Em seguida, ligou-se à VM recém-criada por SSH para garantir que está em execução.

Para saber mais sobre o trabalho com máquinas virtuais no ambiente do Azure com o Go, veja [Exemplos de computação no Azure para o Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute) ou [Exemplos de gestão de recursos do Azure para o Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/resources).

Para saber mais sobre os métodos de autenticação disponíveis no SDK e os tipos de autenticação que suportam, veja [Autenticação com o SDK do Azure para Go](azure-sdk-go-authorization.md).
