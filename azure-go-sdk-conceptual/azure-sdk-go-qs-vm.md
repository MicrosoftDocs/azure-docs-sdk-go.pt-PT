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
# <a name="quickstart-deploy-an-azure-virtual-machine-from-a-template-with-the-azure-sdk-for-go"></a><span data-ttu-id="2bc66-104">Início rápido: implementar uma máquina virtual do Azure a partir de um modelo com o Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="2bc66-104">Quickstart: Deploy an Azure virtual machine from a template with the Azure SDK for Go</span></span>

<span data-ttu-id="2bc66-105">Este guia de início rápido centra-se na implementação de recursos a partir de um modelo com o Azure SDK para Go.</span><span class="sxs-lookup"><span data-stu-id="2bc66-105">This quickstart focuses on deploying resources from a template with the Azure SDK for Go.</span></span> <span data-ttu-id="2bc66-106">Os modelos são os instantâneos de todos os recursos contidos dentro de um [grupo de recursos do Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview).</span><span class="sxs-lookup"><span data-stu-id="2bc66-106">Templates are snapshots of all of the resources contained within an [Azure resource group](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview).</span></span> <span data-ttu-id="2bc66-107">Ao longo do percurso, fica familiarizado com a funcionalidade e convenções do SDK ao executar uma tarefa útil.</span><span class="sxs-lookup"><span data-stu-id="2bc66-107">Along the way, you'll become familiar with the functionality and conventions of the SDK while performing a useful task.</span></span>

<span data-ttu-id="2bc66-108">No final deste início rápido, tem uma VM em execução na qual inicia a sessão com um nome de utilizador e palavra-passe.</span><span class="sxs-lookup"><span data-stu-id="2bc66-108">At the end of this quickstart, you have a running VM that you log into with a username and password.</span></span>

[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

<span data-ttu-id="2bc66-109">Se utilizar uma instalação local da CLI do Azure, este início rápido necessita da versão 2.0.24 da CLI ou posterior.</span><span class="sxs-lookup"><span data-stu-id="2bc66-109">If you use a local install of the Azure CLI, this quickstart requires CLI version 2.0.24 or later.</span></span> <span data-ttu-id="2bc66-110">Execute `az --version` para certificar-se de que a sua instalação da CLI cumpre este requisito.</span><span class="sxs-lookup"><span data-stu-id="2bc66-110">Run `az --version` to make sure your CLI install meets this requirement.</span></span> <span data-ttu-id="2bc66-111">Se precisar de instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli).</span><span class="sxs-lookup"><span data-stu-id="2bc66-111">If you need to install or upgrade, see [Install the Azure CLI 2.0](/cli/azure/install-azure-cli).</span></span>

## <a name="install-the-azure-sdk-for-go"></a><span data-ttu-id="2bc66-112">Instale o Azure SDK para Go</span><span class="sxs-lookup"><span data-stu-id="2bc66-112">Install the Azure SDK for Go</span></span> 

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

## <a name="create-a-service-principal"></a><span data-ttu-id="2bc66-113">Criar um principal de serviço</span><span class="sxs-lookup"><span data-stu-id="2bc66-113">Create a service principal</span></span>

<span data-ttu-id="2bc66-114">Para iniciar sessão com uma aplicação de forma não interativa, precisa de um principal de serviço.</span><span class="sxs-lookup"><span data-stu-id="2bc66-114">To log in non-interactively with an application, you need a service principal.</span></span> <span data-ttu-id="2bc66-115">Os principais de serviço fazem parte da Autenticação Baseada em Funções (RBAC) que cria uma identidade de utilizador exclusiva.</span><span class="sxs-lookup"><span data-stu-id="2bc66-115">Service principals are part of Role-Based Authentication (RBAC), which creates a unique user identity.</span></span> <span data-ttu-id="2bc66-116">Para criar um novo principal de serviço com a CLI, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="2bc66-116">To create a new service principal with the CLI, run the following command:</span></span>

```azurecli-interactive
az ad sp create-for-rbac --name az-go-vm-quickstart
```

<span data-ttu-id="2bc66-117">__Certifique-se__ de que regista os valores `appId`, `password`, e `tenant` no resultado.</span><span class="sxs-lookup"><span data-stu-id="2bc66-117">__Make sure__ to record the `appId`, `password`, and `tenant` values in the output.</span></span> <span data-ttu-id="2bc66-118">Estes valores são utilizados pela aplicação para autenticar com o Azure.</span><span class="sxs-lookup"><span data-stu-id="2bc66-118">These values are used by the application to authenticate with Azure.</span></span>

<span data-ttu-id="2bc66-119">Para mais informações sobre como criar e gerir Principais de Serviço com a CLI 2.0 do Azure, consulte [Criar um principal de serviço do Azure com a CLI 2.0 do Azure](/cli/azure/create-an-azure-service-principal-azure-cli).</span><span class="sxs-lookup"><span data-stu-id="2bc66-119">For more information on creating and managing Service Principals with the Azure CLI 2.0, see [Create an Azure service principal with Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="2bc66-120">Obter o código</span><span class="sxs-lookup"><span data-stu-id="2bc66-120">Get the code</span></span>

<span data-ttu-id="2bc66-121">Obter o código de início rápido e todas as dependências com `go get`.</span><span class="sxs-lookup"><span data-stu-id="2bc66-121">Get the quickstart code and all of its dependencies with `go get`.</span></span>

```bash
go get -u -d github.com/azure-samples/azure-sdk-for-go-samples/quickstart/deploy-vm/...
```

<span data-ttu-id="2bc66-122">Este código compila, mas não funciona corretamente enquanto não fornecer as informações da sua conta do Azure e o principal de serviço criado.</span><span class="sxs-lookup"><span data-stu-id="2bc66-122">This code compiles, but doesn't run correctly until you provide it information about your Azure account and the created service principal.</span></span> <span data-ttu-id="2bc66-123">No `main.go` há uma variável, `config`, que contém um struct `authInfo`.</span><span class="sxs-lookup"><span data-stu-id="2bc66-123">In `main.go` there is a variable, `config`, which contains an `authInfo` struct.</span></span> <span data-ttu-id="2bc66-124">Este struct precisa que os valores do campo sejam substituídos para poder realizar a autenticação corretamente.</span><span class="sxs-lookup"><span data-stu-id="2bc66-124">This struct needs to have its field values replaced in order to authenticate correctly.</span></span>

```go
    config = authInfo{ // Your application credentials
        TenantID:               "", // Azure account tenantID
        SubscriptionID:         "", // Azure subscription subscriptionID
        ServicePrincipalID:     "", // Service principal appId
        ServicePrincipalSecret: "", // Service principal password/secret
    }
```

* <span data-ttu-id="2bc66-125">`SubscriptionID`: o seu ID de subscrição, que pode ser obtido a partir do comando da CLI</span><span class="sxs-lookup"><span data-stu-id="2bc66-125">`SubscriptionID`: Your subscription ID, which can be obtained from the CLI command</span></span>

  ```azurecli-interactive
  az account show --query id -o tsv
  ```

* <span data-ttu-id="2bc66-126">`TenantID`: o seu ID de inquilino, o valor `tenant` registado ao criar o principal de serviço</span><span class="sxs-lookup"><span data-stu-id="2bc66-126">`TenantID`: Your tenant ID, the `tenant` value recorded when creating the service principal</span></span>
* <span data-ttu-id="2bc66-127">`ServicePrincipalID`: o valor `appId` registado ao criar o principal de serviço</span><span class="sxs-lookup"><span data-stu-id="2bc66-127">`ServicePrincipalID`: The `appId` value recorded when creating the service principal</span></span>
* <span data-ttu-id="2bc66-128">`ServicePrincipalSecret`: o valor `password` registado ao criar o principal de serviço</span><span class="sxs-lookup"><span data-stu-id="2bc66-128">`ServicePrincipalSecret`: The `password` value recorded when creating the service principal</span></span>

<span data-ttu-id="2bc66-129">Também precisa de editar um valor no ficheiro `vm-quickstart-params.json`.</span><span class="sxs-lookup"><span data-stu-id="2bc66-129">You also need to edit a value in the `vm-quickstart-params.json` file.</span></span>

```json
    "vm_password": {
        "value": "_"
    }
```

* <span data-ttu-id="2bc66-130">`vm_password`: a palavra-passe da conta de utilizador de VM.</span><span class="sxs-lookup"><span data-stu-id="2bc66-130">`vm_password`: The password for the VM user account.</span></span> <span data-ttu-id="2bc66-131">Tem de ter 6-72 carateres de comprimento e conter 3 dos seguintes carateres:</span><span class="sxs-lookup"><span data-stu-id="2bc66-131">It must be 6-72 characters in length and contain 3 of the following characters:</span></span>
  * <span data-ttu-id="2bc66-132">uma letra minúscula</span><span class="sxs-lookup"><span data-stu-id="2bc66-132">A lowercase letter</span></span>
  * <span data-ttu-id="2bc66-133">uma letra maiúscula</span><span class="sxs-lookup"><span data-stu-id="2bc66-133">An uppercase letter</span></span>
  * <span data-ttu-id="2bc66-134">um número</span><span class="sxs-lookup"><span data-stu-id="2bc66-134">A number</span></span>
  * <span data-ttu-id="2bc66-135">um símbolo</span><span class="sxs-lookup"><span data-stu-id="2bc66-135">A symbol</span></span>

## <a name="running-the-code"></a><span data-ttu-id="2bc66-136">Executar o código</span><span class="sxs-lookup"><span data-stu-id="2bc66-136">Running the code</span></span>

<span data-ttu-id="2bc66-137">Execute o início rápido com o comando `go run`.</span><span class="sxs-lookup"><span data-stu-id="2bc66-137">Run the quickstart with the `go run` command.</span></span>

```bash
cd $GOPATH/src/github.com/azure-samples/azure-sdk-for-go-samples/quickstart/deploy-vm
go run main.go
```

<span data-ttu-id="2bc66-138">Se existir uma falha na implementação, recebe uma mensagem a indicar que houve um problema, mas sem nenhum detalhe específico.</span><span class="sxs-lookup"><span data-stu-id="2bc66-138">If there is a failure in the deployment, you get a message indicating that there was an issue, but without any specific details.</span></span> <span data-ttu-id="2bc66-139">Com a CLI do Azure, obtenha os detalhes da falha de implementação com o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="2bc66-139">Using the Azure CLI, get the details of the deployment failure with the following command:</span></span>

```azurecli-interactive
az group deployment show -g GoVMQuickstart -n VMDeployQuickstart
```

<span data-ttu-id="2bc66-140">Se a implementação for bem sucedida, vai ver uma mensagem com o nome de utilizador, endereço IP e a palavra-passe para registar na máquina virtual recém-criada.</span><span class="sxs-lookup"><span data-stu-id="2bc66-140">If the deployment is successful, you see a message giving the username, IP address, and password for logging into the newly created virtual machine.</span></span> <span data-ttu-id="2bc66-141">SSH a esta máquina para confirmar que está operacional e em funcionamento.</span><span class="sxs-lookup"><span data-stu-id="2bc66-141">SSH into this machine to confirm that it is up and running.</span></span>

## <a name="cleaning-up"></a><span data-ttu-id="2bc66-142">Limpeza</span><span class="sxs-lookup"><span data-stu-id="2bc66-142">Cleaning up</span></span>

<span data-ttu-id="2bc66-143">Limpe os recursos criados durante o início rápido ao eliminar o grupo de recursos com a CLI.</span><span class="sxs-lookup"><span data-stu-id="2bc66-143">Clean up the resources created during this quickstart by deleting the resource group with the CLI.</span></span>

```azurecli-interactive
az group delete -n GoVMQuickstart
```

## <a name="code-in-depth"></a><span data-ttu-id="2bc66-144">Programe em profundidade</span><span class="sxs-lookup"><span data-stu-id="2bc66-144">Code in depth</span></span>

<span data-ttu-id="2bc66-145">O que o código de início rápido faz está num bloco de variáveis e de várias funções pequenas, cada das quais é abordada aqui.</span><span class="sxs-lookup"><span data-stu-id="2bc66-145">What the quickstart code does is broken down into a block of variables and several small functions, each of which are discussed here.</span></span>

### <a name="variable-assignments-and-structs"></a><span data-ttu-id="2bc66-146">Atribuições de variáveis e structs</span><span class="sxs-lookup"><span data-stu-id="2bc66-146">Variable assignments and structs</span></span>

<span data-ttu-id="2bc66-147">Dado que o início rápido é autónomo, utiliza variáveis globais em vez de opções de linha de comandos ou variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="2bc66-147">Since quickstart is self-contained, it uses global variables rather than command-line options or environment variables.</span></span>

```go
type authInfo struct {
        TenantID               string
        SubscriptionID         string
        ServicePrincipalID     string
        ServicePrincipalSecret string
}
```

<span data-ttu-id="2bc66-148">O struct `authInfo` é declarado para encapsular todas as informações necessárias para a autorização com os serviços do Azure.</span><span class="sxs-lookup"><span data-stu-id="2bc66-148">The `authInfo` struct is declared to encapsulate all of the information needed for authorization with Azure services.</span></span>

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

<span data-ttu-id="2bc66-149">Os valores são declarados, o que dá os nomes dos recursos criados.</span><span class="sxs-lookup"><span data-stu-id="2bc66-149">Values are declared which give the names of created resources.</span></span> <span data-ttu-id="2bc66-150">A localização também está especificada aqui, que pode alterar para ver como as implementações se comportam noutros datacenters.</span><span class="sxs-lookup"><span data-stu-id="2bc66-150">The location is also specified here, which you can change to see how deployments behave in other datacenters.</span></span> <span data-ttu-id="2bc66-151">Nem todos os datacenters têm todos os recursos necessários disponíveis.</span><span class="sxs-lookup"><span data-stu-id="2bc66-151">Not every datacenter has all of the required resources available.</span></span>

<span data-ttu-id="2bc66-152">As constantes `templateFile` e `parametersFile` apontam para os ficheiros necessários para a implementação.</span><span class="sxs-lookup"><span data-stu-id="2bc66-152">The `templateFile` and `parametersFile` constants point to the files needed for deployment.</span></span> <span data-ttu-id="2bc66-153">O token do principal de serviço é abordado mais tarde, estando a variável `ctx` num [contexto Go](https://blog.golang.org/context) para as operações de rede.</span><span class="sxs-lookup"><span data-stu-id="2bc66-153">The service principal token is covered later, and the `ctx` variable is a [Go context](https://blog.golang.org/context) for the network operations.</span></span>

### <a name="init-and-authorization"></a><span data-ttu-id="2bc66-154">init() e autorização</span><span class="sxs-lookup"><span data-stu-id="2bc66-154">init() and authorization</span></span>

<span data-ttu-id="2bc66-155">O método `init()` para o código configura a autorização.</span><span class="sxs-lookup"><span data-stu-id="2bc66-155">The `init()` method for the code sets up authorization.</span></span> <span data-ttu-id="2bc66-156">Dado que a autorização é uma condição prévia para tudo no início rápido, faz sentido tê-la como parte da inicialização.</span><span class="sxs-lookup"><span data-stu-id="2bc66-156">Since authorization is a precondition for everything in the quickstart, it makes sense to have it as part of initialization.</span></span> 

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

<span data-ttu-id="2bc66-157">Este código conclui dois passos para a autorização:</span><span class="sxs-lookup"><span data-stu-id="2bc66-157">This code completes two steps for authorization:</span></span>

* <span data-ttu-id="2bc66-158">As informações de configuração do OAuth para `TenantID` são obtidas pela interface do Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="2bc66-158">OAuth configuration information for the `TenantID` is retrieved by interfacing with Azure Active Directory.</span></span> <span data-ttu-id="2bc66-159">O objeto [`azure.PublicCloud`](https://godoc.org/github.com/Azure/go-autorest/autorest/azure#PublicCloud) contém pontos finais utilizados na configuração do Azure standard.</span><span class="sxs-lookup"><span data-stu-id="2bc66-159">The [`azure.PublicCloud`](https://godoc.org/github.com/Azure/go-autorest/autorest/azure#PublicCloud) object contains endpoints used in the standard Azure configuration.</span></span>
* <span data-ttu-id="2bc66-160">A função [`adal.NewServicePrincipalToken()`](https://godoc.org/github.com/Azure/go-autorest/autorest/adal#NewServicePrincipalToken) é chamada.</span><span class="sxs-lookup"><span data-stu-id="2bc66-160">The [`adal.NewServicePrincipalToken()`](https://godoc.org/github.com/Azure/go-autorest/autorest/adal#NewServicePrincipalToken) function is called.</span></span> <span data-ttu-id="2bc66-161">Esta função recebe as informações de OAuth juntamente com o início de sessão do principal de serviço, bem como o estilo de gestão do Azure que está a ser utilizado.</span><span class="sxs-lookup"><span data-stu-id="2bc66-161">This function takes the OAuth information along with the service principal login, as well as which style of Azure management is being used.</span></span> <span data-ttu-id="2bc66-162">A menos que tenha requisitos específicos e saiba o que está a fazer, este valor deve ser sempre `.ResourceManagerEndpoint`.</span><span class="sxs-lookup"><span data-stu-id="2bc66-162">Unless you have specific requirements and know what you're doing, this value should always be `.ResourceManagerEndpoint`.</span></span>

### <a name="flow-of-operations-in-main"></a><span data-ttu-id="2bc66-163">Fluxo de operações em main()</span><span class="sxs-lookup"><span data-stu-id="2bc66-163">Flow of operations in main()</span></span>

<span data-ttu-id="2bc66-164">A função `main()` é simples, apenas indica o fluxo de operações e realiza uma verificação de erros.</span><span class="sxs-lookup"><span data-stu-id="2bc66-164">The `main()` function is simple, only indicating the flow of operations and performing error-checking.</span></span>

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

<span data-ttu-id="2bc66-165">Os passos que o código executa são, por ordem:</span><span class="sxs-lookup"><span data-stu-id="2bc66-165">The steps that the code runs through are, in order:</span></span>

* <span data-ttu-id="2bc66-166">Criar o grupo de recursos para o qual implementar (`createGroup()`)</span><span class="sxs-lookup"><span data-stu-id="2bc66-166">Create the resource group to deploy to (`createGroup()`)</span></span>
* <span data-ttu-id="2bc66-167">Criar a implementação dentro deste grupo (`createDeployment()`)</span><span class="sxs-lookup"><span data-stu-id="2bc66-167">Create the deployment within this group (`createDeployment()`)</span></span>
* <span data-ttu-id="2bc66-168">Obter e exibir informações de início de sessão para a VM implementada (`getLogin()`)</span><span class="sxs-lookup"><span data-stu-id="2bc66-168">Obtain and display login information for the deployed VM (`getLogin()`)</span></span>

### <a name="creating-the-resource-group"></a><span data-ttu-id="2bc66-169">Criar o grupo de recursos</span><span class="sxs-lookup"><span data-stu-id="2bc66-169">Creating the resource group</span></span>

<span data-ttu-id="2bc66-170">A função `createGroup()` cria o grupo de recursos.</span><span class="sxs-lookup"><span data-stu-id="2bc66-170">The `createGroup()` function creates the resource group.</span></span> <span data-ttu-id="2bc66-171">Ao olhar para o fluxo e argumentos de chamada demonstra a forma como as interações de serviço estão estruturadas no SDK.</span><span class="sxs-lookup"><span data-stu-id="2bc66-171">Looking at the call flow and arguments demonstrates the way that service interactions are structured in the SDK.</span></span>

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

<span data-ttu-id="2bc66-172">O fluxo geral de interação com um serviço do Azure é:</span><span class="sxs-lookup"><span data-stu-id="2bc66-172">The general flow of interacting with an Azure service is:</span></span>

* <span data-ttu-id="2bc66-173">Criar o cliente com o método `service.NewXClient()`, em que `X` é o tipo de recursos de `service` com o qual quer interagir.</span><span class="sxs-lookup"><span data-stu-id="2bc66-173">Create the client using the `service.NewXClient()` method, where `X` is the resource type of the `service` that you want to interact with.</span></span> <span data-ttu-id="2bc66-174">Esta função recebe sempre um ID de subscrição.</span><span class="sxs-lookup"><span data-stu-id="2bc66-174">This function always takes a subscription ID.</span></span>
* <span data-ttu-id="2bc66-175">Defina o método de autorização para o cliente, permitindo que interaja com a API remota.</span><span class="sxs-lookup"><span data-stu-id="2bc66-175">Set the authorization method for the client, allowing it to interact with the remote API.</span></span>
* <span data-ttu-id="2bc66-176">Faça a chamada de método no cliente correspondente à API remota.</span><span class="sxs-lookup"><span data-stu-id="2bc66-176">Make the method call on the client corresponding to the remote API.</span></span> <span data-ttu-id="2bc66-177">Os métodos do cliente do serviço normalmente aceitam o nome do recurso e um objeto de metadados.</span><span class="sxs-lookup"><span data-stu-id="2bc66-177">Service client methods usually take the name of the resource and a metadata object.</span></span>

<span data-ttu-id="2bc66-178">A função [`to.StringPtr()`](https://godoc.org/github.com/Azure/go-autorest/autorest/to#StringPtr) é utilizada para realizar uma conversão de tipo aqui.</span><span class="sxs-lookup"><span data-stu-id="2bc66-178">The [`to.StringPtr()`](https://godoc.org/github.com/Azure/go-autorest/autorest/to#StringPtr) function is used to perform a type conversion here.</span></span> <span data-ttu-id="2bc66-179">Os structs de parâmetros para os métodos do SDK quase exclusivamente aceitam apontadores, pelo que esses métodos são fornecidos para tornar as conversões de tipo mais fáceis.</span><span class="sxs-lookup"><span data-stu-id="2bc66-179">The parameters structs for methods of the SDK almost exclusively take pointers, so these methods are provided to make the type conversions easy.</span></span> <span data-ttu-id="2bc66-180">Consulte a documentação do módulo [autorest/to](https://godoc.org/github.com/Azure/go-autorest/autorest/to) para a lista completa e os conversores de conveniência de comportamento.</span><span class="sxs-lookup"><span data-stu-id="2bc66-180">See the documentation for the [autorest/to](https://godoc.org/github.com/Azure/go-autorest/autorest/to) module for the complete list and behavior of convenience converters.</span></span>

<span data-ttu-id="2bc66-181">A operação `groupsClient.CreateOrUpdate()` devolve um apontador para um struct de dados que representa o grupo de recursos.</span><span class="sxs-lookup"><span data-stu-id="2bc66-181">The `groupsClient.CreateOrUpdate()` operation returns a pointer to a data struct representing the resource group.</span></span> <span data-ttu-id="2bc66-182">Um valor de retorno direto deste tipo indica uma operação de curto-prazo que deverá ser síncrona.</span><span class="sxs-lookup"><span data-stu-id="2bc66-182">A direct return value of this kind indicates a short-running operation that is meant to be synchronous.</span></span> <span data-ttu-id="2bc66-183">Na secção seguinte irá ver um exemplo de uma operação de longo-prazo e como interagir com elas.</span><span class="sxs-lookup"><span data-stu-id="2bc66-183">In the next section, you'll see an example of a long-running operation and how to interact with them.</span></span>

### <a name="performing-the-deployment"></a><span data-ttu-id="2bc66-184">Realizar a implementação</span><span class="sxs-lookup"><span data-stu-id="2bc66-184">Performing the deployment</span></span>

<span data-ttu-id="2bc66-185">Uma vez criado o grupo para conter os seus recursos, é o momento de executar a implementação.</span><span class="sxs-lookup"><span data-stu-id="2bc66-185">Once the group to contain its resources is created, it's time to run the deployment.</span></span> <span data-ttu-id="2bc66-186">Este código é dividido em várias secções mais pequenas para dar ênfase a partes diferentes da sua lógica.</span><span class="sxs-lookup"><span data-stu-id="2bc66-186">This code is broken up into smaller sections to emphasize different parts of its logic.</span></span>


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

<span data-ttu-id="2bc66-187">Os ficheiros de implementação são carregados por `readJSON`, cujos detalhes são ignorados aqui.</span><span class="sxs-lookup"><span data-stu-id="2bc66-187">The deployment files are loaded by `readJSON`, the details of which are skipped here.</span></span> <span data-ttu-id="2bc66-188">Esta função devolve um `*map[string]interface{}`, o tipo utilizado para construir os metadados para a chamada de implementação dos recursos.</span><span class="sxs-lookup"><span data-stu-id="2bc66-188">This function returns a `*map[string]interface{}`, the type used in constructing the metadata for the resource deployment call.</span></span>

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

<span data-ttu-id="2bc66-189">Este código segue o mesmo padrão da criação do grupo de recursos.</span><span class="sxs-lookup"><span data-stu-id="2bc66-189">This code follows the same pattern as with creating the resource group.</span></span> <span data-ttu-id="2bc66-190">É criado um novo cliente, dada a capacidade de autenticar com o Azure e, em seguida, é chamado um método.</span><span class="sxs-lookup"><span data-stu-id="2bc66-190">A new client is created, given the ability to authenticate with Azure, and then a method is called.</span></span> <span data-ttu-id="2bc66-191">O método tem inclusive o mesmo nome (`CreateOrUpdate`) do método correspondente para os grupos de recursos.</span><span class="sxs-lookup"><span data-stu-id="2bc66-191">The method even has the same name (`CreateOrUpdate`) as the corresponding method for resource groups.</span></span> <span data-ttu-id="2bc66-192">Este padrão é visto com frequência no SDK.</span><span class="sxs-lookup"><span data-stu-id="2bc66-192">This pattern is seen again and again in the SDK.</span></span> <span data-ttu-id="2bc66-193">Os métodos que realizam trabalho semelhante costumam ter o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="2bc66-193">Methods that perform similar work normally have the same name.</span></span>

<span data-ttu-id="2bc66-194">A grande diferença está no valor de retorno do método `deploymentsClient.CreateOrUpdate()`.</span><span class="sxs-lookup"><span data-stu-id="2bc66-194">The biggest difference comes in the return value of the `deploymentsClient.CreateOrUpdate()` method.</span></span> <span data-ttu-id="2bc66-195">O valor é um objeto `Future`, que segue o [padrão de design futuro](https://en.wikipedia.org/wiki/Futures_and_promises).</span><span class="sxs-lookup"><span data-stu-id="2bc66-195">This value is a `Future` object, which follows the [future design pattern](https://en.wikipedia.org/wiki/Futures_and_promises).</span></span> <span data-ttu-id="2bc66-196">Os futuros representam uma operação de longo-prazo no Azure que pretende consultar ocasionalmente enquanto realiza outros trabalhos.</span><span class="sxs-lookup"><span data-stu-id="2bc66-196">Futures represent a long-running operation in Azure that you may want to occasionally poll while performing other work.</span></span>

```go
        //...
        err = deploymentFuture.Future.WaitForCompletion(ctx, deploymentsClient.BaseClient.Client)
        if err != nil {
                log.Fatalf("Error while waiting for deployment creation: %v", err)
        }
        return deploymentFuture.Result(deploymentsClient)
}
```

<span data-ttu-id="2bc66-197">Neste exemplo, o melhor a fazer é aguardar que a operação seja concluída.</span><span class="sxs-lookup"><span data-stu-id="2bc66-197">For this example, the best thing to do is to wait for the operation to complete.</span></span> <span data-ttu-id="2bc66-198">Aguardar por um futuro requer um [objeto de contexto](https://blog.golang.org/context) e o cliente que criou o objeto Futuro.</span><span class="sxs-lookup"><span data-stu-id="2bc66-198">Waiting on a future requires both a [context object](https://blog.golang.org/context) and the client that created the Future object.</span></span> <span data-ttu-id="2bc66-199">Existem duas possíveis origens do erro: um erro causado do lado do cliente ao tentar invocar o método, e uma resposta de erro do servidor.</span><span class="sxs-lookup"><span data-stu-id="2bc66-199">There are two possible error sources here: An error caused on the client side when trying to invoke the method, and an error response from the server.</span></span> <span data-ttu-id="2bc66-200">A última opção é devolvida como parte da chamada `deploymentFuture.Result()`.</span><span class="sxs-lookup"><span data-stu-id="2bc66-200">The latter is returned as part of the `deploymentFuture.Result()` call.</span></span>

### <a name="obtaining-the-assigned-ip-address"></a><span data-ttu-id="2bc66-201">Obter o endereço IP atribuído</span><span class="sxs-lookup"><span data-stu-id="2bc66-201">Obtaining the assigned IP address</span></span>

<span data-ttu-id="2bc66-202">Para fazer alguma coisa com a VM recém-criada, necessita do endereço IP atribuído.</span><span class="sxs-lookup"><span data-stu-id="2bc66-202">To do anything with the newly created VM, you need the assigned IP address.</span></span> <span data-ttu-id="2bc66-203">Os endereços IP são os seus próprios recursos do Azure, vinculados aos recursos do Controlador de Interface de Rede (NIC).</span><span class="sxs-lookup"><span data-stu-id="2bc66-203">IP addresses are their own separate Azure resource, bound to Network Interface Controller (NIC) resources.</span></span>

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

<span data-ttu-id="2bc66-204">Este método depende das informações armazenadas no ficheiro de parâmetros.</span><span class="sxs-lookup"><span data-stu-id="2bc66-204">This method relies on information that is stored in the parameters file.</span></span> <span data-ttu-id="2bc66-205">O código pode consultar diretamente a VM para obter o seu NIC, consultar o NIC para obter o seu recurso de IP e depois consultar o recurso de IP diretamente.</span><span class="sxs-lookup"><span data-stu-id="2bc66-205">The code could query the VM directly to get its NIC, query the NIC to get its IP resource, and then query the IP resource directly.</span></span> <span data-ttu-id="2bc66-206">Trata-se de uma longa cadeia de dependências e operações a resolver, o que a torna dispendiosa.</span><span class="sxs-lookup"><span data-stu-id="2bc66-206">That's a long chain of dependencies and operations to resolve, making it expensive.</span></span> <span data-ttu-id="2bc66-207">Dado que a informação de JSON é local, pode ser carregada como alternativa.</span><span class="sxs-lookup"><span data-stu-id="2bc66-207">Since the JSON information is local, it can be loaded instead.</span></span>

<span data-ttu-id="2bc66-208">Os valores para o utilizador e palavra-passe de VM são igualmente carregados a partir do JSON.</span><span class="sxs-lookup"><span data-stu-id="2bc66-208">The values for the VM user and password are likewise loaded from the JSON.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2bc66-209">Passos seguintes</span><span class="sxs-lookup"><span data-stu-id="2bc66-209">Next steps</span></span>

<span data-ttu-id="2bc66-210">Neste início rápido, utilizou um modelo existente e implementou-o através do Go.</span><span class="sxs-lookup"><span data-stu-id="2bc66-210">In this quickstart, you took an existing template and deployed it through Go.</span></span> <span data-ttu-id="2bc66-211">Em seguida, ligou-se à VM recém-criada por SSH para garantir que está em execução.</span><span class="sxs-lookup"><span data-stu-id="2bc66-211">Then you connected to the newly created VM via SSH to ensure that it's running.</span></span>

<span data-ttu-id="2bc66-212">Para saber mais sobre o trabalho com máquinas virtuais no ambiente do Azure com o Go, veja [Exemplos de computação no Azure para o Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute) ou [Exemplos de gestão de recursos do Azure para o Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/resources).</span><span class="sxs-lookup"><span data-stu-id="2bc66-212">To continue learning about working with virtual machines in the Azure environment with Go, take a look at the [Azure compute samples for Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute) or [Azure resource management samples for Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/resources).</span></span>
