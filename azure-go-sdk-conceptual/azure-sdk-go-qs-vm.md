---
title: "Implementar uma máquina virtual do Azure a partir do Go"
description: "Implemente uma máquina virtual utilizando o Azure SDK para Go."
keywords: azure, virtual machine, vm, go, golang, azure sdk
author: sptramer
ms.author: sttramer
ms.date: 02/08/2018
ms.topic: quickstart
ms.devlang: go
manager: routlaw
ms.openlocfilehash: e530d944deca40e9e6c29b6c2768e2367822714e
ms.sourcegitcommit: aaa8c37880332625f858a38f5918e6cf581bf48d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2018
---
# <a name="quickstart-deploy-an-azure-virtual-machine-from-a-template-with-the-azure-sdk-for-go"></a>Início rápido: implementar uma máquina virtual do Azure a partir de um modelo com o Azure SDK for Go

Este guia de início rápido centra-se na implementação de recursos a partir de um modelo com o Azure SDK para Go. Os modelos são os instantâneos de todos os recursos contidos dentro de um [grupo de recursos do Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview). Ao longo do percurso, fica familiarizado com a funcionalidade e convenções do SDK ao executar uma tarefa útil.

No final deste início rápido, tem uma VM em execução na qual inicia a sessão com um nome de utilizador e palavra-passe.

[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

Se utilizar uma instalação local da CLI do Azure, este início rápido necessita da versão 2.0.24 da CLI ou posterior. Execute `az --version` para certificar-se de que a sua instalação da CLI cumpre este requisito. Se precisar de instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli).

## <a name="install-the-azure-sdk-for-go"></a>Instale o Azure SDK para Go 

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

## <a name="create-a-service-principal"></a>Criar um principal de serviço

Para iniciar sessão com uma aplicação de forma não interativa, precisa de um principal de serviço. Os principais de serviço fazem parte da Autenticação Baseada em Funções (RBAC) que cria uma identidade de utilizador exclusiva. Para criar um novo principal de serviço com a CLI, execute o seguinte comando:

```azurecli-interactive
az ad sp create-for-rbac --name az-go-vm-quickstart
```

__Certifique-se__ de que regista os valores `appId`, `password`, e `tenant` no resultado. Estes valores são utilizados pela aplicação para autenticar com o Azure.

Para mais informações sobre como criar e gerir Principais de Serviço com a CLI 2.0 do Azure, consulte [Criar um principal de serviço do Azure com a CLI 2.0 do Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

## <a name="get-the-code"></a>Obter o código

Obter o código de início rápido e todas as dependências com `go get`.

```bash
go get -u -d github.com/azure-samples/azure-sdk-for-go-samples/quickstart/deploy-vm/...
```

Este código compila, mas não funciona corretamente enquanto não fornecer as informações da sua conta do Azure e o principal de serviço criado. No `main.go` há uma variável, `config`, que contém um struct `authInfo`. Este struct precisa que os valores do campo sejam substituídos para poder realizar a autenticação corretamente.

```go
    config = authInfo{ // Your application credentials
        TenantID:               "", // Azure account tenantID
        SubscriptionID:         "", // Azure subscription subscriptionID
        ServicePrincipalID:     "", // Service principal appId
        ServicePrincipalSecret: "", // Service principal password/secret
    }
```

* `SubscriptionID`: o seu ID de subscrição, que pode ser obtido a partir do comando da CLI

  ```azurecli-interactive
  az account show --query id -o tsv
  ```

* `TenantID`: o seu ID de inquilino, o valor `tenant` registado ao criar o principal de serviço
* `ServicePrincipalID`: o valor `appId` registado ao criar o principal de serviço
* `ServicePrincipalSecret`: o valor `password` registado ao criar o principal de serviço

Também precisa de editar um valor no ficheiro `vm-quickstart-params.json`.

```json
    "vm_password": {
        "value": "_"
    }
```

* `vm_password`: a palavra-passe da conta de utilizador de VM. Tem de ter 6-72 carateres de comprimento e conter 3 dos seguintes carateres:
  * uma letra minúscula
  * uma letra maiúscula
  * um número
  * um símbolo

## <a name="running-the-code"></a>Executar o código

Execute o início rápido com o comando `go run`.

```bash
cd $GOPATH/src/github.com/azure-samples/azure-sdk-for-go-samples/quickstart/deploy-vm
go run main.go
```

Se existir uma falha na implementação, recebe uma mensagem a indicar que houve um problema, mas sem nenhum detalhe específico. Com a CLI do Azure, obtenha os detalhes da falha de implementação com o seguinte comando:

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

### <a name="variable-assignments-and-structs"></a>Atribuições de variáveis e structs

Dado que o início rápido é autónomo, utiliza variáveis globais em vez de opções de linha de comandos ou variáveis de ambiente.

```go
type authInfo struct {
        TenantID               string
        SubscriptionID         string
        ServicePrincipalID     string
        ServicePrincipalSecret string
}
```

O struct `authInfo` é declarado para encapsular todas as informações necessárias para a autorização com os serviços do Azure.

```go
const (
    resourceGroupName     = "GoVMQuickstart"
    resourceGroupLocation = "eastus"

    deploymentName = "VMDeployQuickstart"
    templateFile   = "vm-quickstart-template.json"
    parametersFile = "vm-quickstart-params.json"
)

var (
    config = authInfo{ // Your application credentials
        TenantID:               "", // Azure account tenantID
        SubscriptionID:         "", // Azure subscription subscriptionID
        ServicePrincipalID:     "", // Service principal appId
        ServicePrincipalSecret: "", // Service principal password/secret
    }

    ctx = context.Background()

    token *adal.ServicePrincipalToken
)
```

Os valores são declarados, o que dá os nomes dos recursos criados. A localização também está especificada aqui, que pode alterar para ver como as implementações se comportam noutros datacenters. Nem todos os datacenters têm todos os recursos necessários disponíveis.

As constantes `templateFile` e `parametersFile` apontam para os ficheiros necessários para a implementação. O token do principal de serviço é abordado mais tarde, estando a variável `ctx` num [contexto Go](https://blog.golang.org/context) para as operações de rede.

### <a name="init-and-authorization"></a>init() e autorização

O método `init()` para o código configura a autorização. Dado que a autorização é uma condição prévia para tudo no início rápido, faz sentido tê-la como parte da inicialização. 

```go
// Authenticate with the Azure services over OAuth, using a service principal.
func init() {
    oauthConfig, err := adal.NewOAuthConfig(azure.PublicCloud.ActiveDirectoryEndpoint, config.TenantID)
    if err != nil {
        log.Fatalf("Failed to get OAuth config: %v\n", err)
    }
    token, err = adal.NewServicePrincipalToken(
        *oauthConfig,
        config.ServicePrincipalID,
        config.ServicePrincipalSecret,
        azure.PublicCloud.ResourceManagerEndpoint)
    if err != nil {
        log.Fatalf("faled to get token: %v\n", err)
    }
}
```

Este código conclui dois passos para a autorização:

* As informações de configuração do OAuth para `TenantID` são obtidas pela interface do Azure Active Directory. O objeto [`azure.PublicCloud`](https://godoc.org/github.com/Azure/go-autorest/autorest/azure#PublicCloud) contém pontos finais utilizados na configuração do Azure standard.
* A função [`adal.NewServicePrincipalToken()`](https://godoc.org/github.com/Azure/go-autorest/autorest/adal#NewServicePrincipalToken) é chamada. Esta função recebe as informações de OAuth juntamente com o início de sessão do principal de serviço, bem como o estilo de gestão do Azure que está a ser utilizado. A menos que tenha requisitos específicos e saiba o que está a fazer, este valor deve ser sempre `.ResourceManagerEndpoint`.

### <a name="flow-of-operations-in-main"></a>Fluxo de operações em main()

A função `main()` é simples, apenas indica o fluxo de operações e realiza uma verificação de erros.

```go
func main() {
    group, err := createGroup()
    if err != nil {
        log.Fatalf("failed to create group: %v", err)
    }
    log.Printf("created group: %v\n", *group.Name)

    log.Println("starting deployment")
    result, err := createDeployment()
    if err != nil {
        log.Fatalf("Failed to deploy correctly: %v", err)
    }
    log.Printf("Completed deployment: %v", *result.Name)
    getLogin()
}
```

Os passos que o código executa são, por ordem:

* Criar o grupo de recursos para o qual implementar (`createGroup()`)
* Criar a implementação dentro deste grupo (`createDeployment()`)
* Obter e exibir informações de início de sessão para a VM implementada (`getLogin()`)

### <a name="creating-the-resource-group"></a>Criar o grupo de recursos

A função `createGroup()` cria o grupo de recursos. Ao olhar para o fluxo e argumentos de chamada demonstra a forma como as interações de serviço estão estruturadas no SDK.

```go
func createGroup() (group resources.Group, err error) {
        groupsClient := resources.NewGroupsClient(config.SubscriptionID)
        groupsClient.Authorizer = autorest.NewBearerAuthorizer(token)

        return groupsClient.CreateOrUpdate(
                ctx,
                resourceGroupName,
                resources.Group{
                        Location: to.StringPtr(resourceGroupLocation)})
}
```

O fluxo geral de interação com um serviço do Azure é:

* Criar o cliente com o método `service.NewXClient()`, em que `X` é o tipo de recursos de `service` com o qual quer interagir. Esta função recebe sempre um ID de subscrição.
* Defina o método de autorização para o cliente, permitindo que interaja com a API remota.
* Faça a chamada de método no cliente correspondente à API remota. Os métodos do cliente do serviço normalmente aceitam o nome do recurso e um objeto de metadados.

A função [`to.StringPtr()`](https://godoc.org/github.com/Azure/go-autorest/autorest/to#StringPtr) é utilizada para realizar uma conversão de tipo aqui. Os structs de parâmetros para os métodos do SDK quase exclusivamente aceitam apontadores, pelo que esses métodos são fornecidos para tornar as conversões de tipo mais fáceis. Consulte a documentação do módulo [autorest/to](https://godoc.org/github.com/Azure/go-autorest/autorest/to) para a lista completa e os conversores de conveniência de comportamento.

A operação `groupsClient.CreateOrUpdate()` devolve um apontador para um struct de dados que representa o grupo de recursos. Um valor de retorno direto deste tipo indica uma operação de curto-prazo que deverá ser síncrona. Na secção seguinte irá ver um exemplo de uma operação de longo-prazo e como interagir com elas.

### <a name="performing-the-deployment"></a>Realizar a implementação

Uma vez criado o grupo para conter os seus recursos, é o momento de executar a implementação. Este código é dividido em várias secções mais pequenas para dar ênfase a partes diferentes da sua lógica.


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

        // ...
```

Os ficheiros de implementação são carregados por `readJSON`, cujos detalhes são ignorados aqui. Esta função devolve um `*map[string]interface{}`, o tipo utilizado para construir os metadados para a chamada de implementação dos recursos.

```go
        // ...
        
        deploymentsClient := resources.NewDeploymentsClient(config.SubscriptionID)
        deploymentsClient.Authorizer = autorest.NewBearerAuthorizer(token)

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
                log.Fatalf("Failed to create deployment: %v", err)
        }
        //...
```

Este código segue o mesmo padrão da criação do grupo de recursos. É criado um novo cliente, dada a capacidade de autenticar com o Azure e, em seguida, é chamado um método. O método tem inclusive o mesmo nome (`CreateOrUpdate`) do método correspondente para os grupos de recursos. Este padrão é visto com frequência no SDK. Os métodos que realizam trabalho semelhante costumam ter o mesmo nome.

A grande diferença está no valor de retorno do método `deploymentsClient.CreateOrUpdate()`. O valor é um objeto `Future`, que segue o [padrão de design futuro](https://en.wikipedia.org/wiki/Futures_and_promises). Os futuros representam uma operação de longo-prazo no Azure que pretende consultar ocasionalmente enquanto realiza outros trabalhos.

```go
        //...
        err = deploymentFuture.Future.WaitForCompletion(ctx, deploymentsClient.BaseClient.Client)
        if err != nil {
                log.Fatalf("Error while waiting for deployment creation: %v", err)
        }
        return deploymentFuture.Result(deploymentsClient)
}
```

Neste exemplo, o melhor a fazer é aguardar que a operação seja concluída. Aguardar por um futuro requer um [objeto de contexto](https://blog.golang.org/context) e o cliente que criou o objeto Futuro. Existem duas possíveis origens do erro: um erro causado do lado do cliente ao tentar invocar o método, e uma resposta de erro do servidor. A última opção é devolvida como parte da chamada `deploymentFuture.Result()`.

### <a name="obtaining-the-assigned-ip-address"></a>Obter o endereço IP atribuído

Para fazer alguma coisa com a VM recém-criada, necessita do endereço IP atribuído. Os endereços IP são os seus próprios recursos do Azure, vinculados aos recursos do Controlador de Interface de Rede (NIC).

```go
func getLogin() {
        params, err := readJSON(parametersFile)
        if err != nil {
                log.Fatalf("Unable to read parameters. Get login information with `az network public-ip list -g %s", resourceGroupName)
        }

        addressClient := network.NewPublicIPAddressesClient(config.SubscriptionID)
        addressClient.Authorizer = autorest.NewBearerAuthorizer(token)
        ipName := (*params)["publicIPAddresses_QuickstartVM_ip_name"].(map[string]interface{})
        ipAddress, err := addressClient.Get(ctx, resourceGroupName, ipName["value"].(string), "")
        if err != nil {
                log.Fatalf("Unable to get IP information. Try using `az network public-ip list -g %s", resourceGroupName)
        }

        vmUser := (*params)["vm_user"].(map[string]interface{})
        vmPass := (*params)["vm_password"].(map[string]interface{})

        log.Printf("Log in with ssh: %s@%s, password: %s",
                vmUser["value"].(string),
                *ipAddress.PublicIPAddressPropertiesFormat.IPAddress,
                vmPass["value"].(string))
}
```

Este método depende das informações armazenadas no ficheiro de parâmetros. O código pode consultar diretamente a VM para obter o seu NIC, consultar o NIC para obter o seu recurso de IP e depois consultar o recurso de IP diretamente. Trata-se de uma longa cadeia de dependências e operações a resolver, o que a torna dispendiosa. Dado que a informação de JSON é local, pode ser carregada como alternativa.

Os valores para o utilizador e palavra-passe de VM são igualmente carregados a partir do JSON.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, utilizou um modelo existente e implementou-o através do Go. Em seguida, ligou-se à VM recém-criada por SSH para garantir que está em execução.

Para saber mais sobre o trabalho com máquinas virtuais no ambiente do Azure com o Go, veja [Exemplos de computação no Azure para o Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute) ou [Exemplos de gestão de recursos do Azure para o Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/resources).
