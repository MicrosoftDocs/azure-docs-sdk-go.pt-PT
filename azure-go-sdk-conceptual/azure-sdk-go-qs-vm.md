---
title: Implementar uma máquina virtual do Azure a partir do Go
description: Implemente uma máquina virtual utilizando o SDK do Azure para Go.
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 09/05/2018
ms.topic: quickstart
ms.technology: azure-sdk-go
ms.service: virtual-machines
ms.devlang: go
ms.openlocfilehash: a7970be0857fd414d776241b033af0c23457790c
ms.sourcegitcommit: 8b9e10b960150dc08f046ab840d6a5627410db29
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44059140"
---
# <a name="quickstart-deploy-an-azure-virtual-machine-from-a-template-with-the-azure-sdk-for-go"></a><span data-ttu-id="84945-103">Início rápido: implementar uma máquina virtual do Azure a partir de um modelo com o Azure SDK for Go</span><span class="sxs-lookup"><span data-stu-id="84945-103">Quickstart: Deploy an Azure virtual machine from a template with the Azure SDK for Go</span></span>

<span data-ttu-id="84945-104">Este início rápido mostra-lhe como implementar recursos a partir de um modelo do Azure Resource Manager com o SDK do Azure para Go.</span><span class="sxs-lookup"><span data-stu-id="84945-104">This quickstart shows you how to deploy resources from an Azure Resource Manager template, using the Azure SDK for Go.</span></span> <span data-ttu-id="84945-105">Os modelos são instantâneos de todos os recursos contidos num [grupo de recursos do Azure](/azure/azure-resource-manager/resource-group-overview).</span><span class="sxs-lookup"><span data-stu-id="84945-105">Templates are snapshots of all of the resources within an [Azure resource group](/azure/azure-resource-manager/resource-group-overview).</span></span> <span data-ttu-id="84945-106">Ao longo do percurso, irá familiarizar-se com a funcionalidade e as convenções do SDK.</span><span class="sxs-lookup"><span data-stu-id="84945-106">Along the way, you'll become familiar with the functionality and conventions of the SDK.</span></span>

<span data-ttu-id="84945-107">No final deste início rápido, tem uma VM em execução na qual inicia a sessão com um nome de utilizador e palavra-passe.</span><span class="sxs-lookup"><span data-stu-id="84945-107">At the end of this quickstart, you have a running VM that you log into with a username and password.</span></span>

> [!NOTE]
> <span data-ttu-id="84945-108">Para ver a criação de uma VM no Go que não envolva a utilização de um modelo do Resource Manager, existe um [exemplo indispensável](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute/vm.go) que demonstra como criar e configurar todos os recursos da VM com o SDK.</span><span class="sxs-lookup"><span data-stu-id="84945-108">To see the creation of a VM in Go without the use of a Resource Manager template, there is an [imperative sample](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute/vm.go) demonstrating how to build and configure all VM resources with the SDK.</span></span> <span data-ttu-id="84945-109">A utilização de um modelo neste exemplo permite centrar a atenção nas convenções do SDK sem entrar em grandes pormenores sobre a arquitetura do serviço do Azure.</span><span class="sxs-lookup"><span data-stu-id="84945-109">Using a template in this sample allows a focus on SDK conventions without getting into too many details about Azure service architecture.</span></span>

[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](includes/cloud-shell-try-it.md)]

<span data-ttu-id="84945-110">Se utilizar uma instalação local da CLI do Azure, este início rápido necessita da versão __2.0.28__ da CLI ou posterior.</span><span class="sxs-lookup"><span data-stu-id="84945-110">If you use a local install of the Azure CLI, this quickstart requires CLI version __2.0.28__ or later.</span></span> <span data-ttu-id="84945-111">Execute `az --version` para certificar-se de que a sua instalação da CLI cumpre este requisito.</span><span class="sxs-lookup"><span data-stu-id="84945-111">Run `az --version` to make sure your CLI install meets this requirement.</span></span> <span data-ttu-id="84945-112">Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).</span><span class="sxs-lookup"><span data-stu-id="84945-112">If you need to install or upgrade, see [Install the Azure CLI](/cli/azure/install-azure-cli).</span></span>

## <a name="install-the-azure-sdk-for-go"></a><span data-ttu-id="84945-113">Instale o Azure SDK para Go</span><span class="sxs-lookup"><span data-stu-id="84945-113">Install the Azure SDK for Go</span></span>

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

## <a name="create-a-service-principal"></a><span data-ttu-id="84945-114">Criar um principal de serviço</span><span class="sxs-lookup"><span data-stu-id="84945-114">Create a service principal</span></span>

<span data-ttu-id="84945-115">Para iniciar sessão com uma aplicação de forma não interativa no Azure, precisa de um principal de serviço.</span><span class="sxs-lookup"><span data-stu-id="84945-115">To sign in non-interactively to Azure with an application, you need a service principal.</span></span> <span data-ttu-id="84945-116">Os principais de serviço fazem parte do controlo de acesso baseado em funções (RBAC) que cria uma identidade de utilizador exclusiva.</span><span class="sxs-lookup"><span data-stu-id="84945-116">Service principals are part of role-based access control (RBAC), which creates a unique user identity.</span></span> <span data-ttu-id="84945-117">Para criar um novo principal de serviço com a CLI, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="84945-117">To create a new service principal with the CLI, run the following command:</span></span>

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth > quickstart.auth
```

<span data-ttu-id="84945-118">Defina a variável de ambiente `AZURE_AUTH_LOCATION` para ser o caminho completo para este ficheiro.</span><span class="sxs-lookup"><span data-stu-id="84945-118">Set the environment variable `AZURE_AUTH_LOCATION` to be the full path to this file.</span></span> <span data-ttu-id="84945-119">Em seguida, o SDK localiza e lê as credenciais diretamente a partir deste ficheiro, sem que tenha de fazer quaisquer alterações ou registar informações do principal de serviço.</span><span class="sxs-lookup"><span data-stu-id="84945-119">Then the SDK locates and reads the credentials directly from this file, without you having to make any changes or record information from the service principal.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="84945-120">Obter o código</span><span class="sxs-lookup"><span data-stu-id="84945-120">Get the code</span></span>

<span data-ttu-id="84945-121">Obter o código de início rápido e todas as dependências com `go get`.</span><span class="sxs-lookup"><span data-stu-id="84945-121">Get the quickstart code and all of its dependencies with `go get`.</span></span>

```bash
go get -u -d github.com/azure-samples/azure-sdk-for-go-samples/quickstarts/deploy-vm/...
```

<span data-ttu-id="84945-122">Não precisa de modificar o código de origem se a variável `AZURE_AUTH_LOCATION` estiver definida corretamente.</span><span class="sxs-lookup"><span data-stu-id="84945-122">You don't need to make any source code modifications if the `AZURE_AUTH_LOCATION` variable is properly set.</span></span> <span data-ttu-id="84945-123">Quando o programa é executado, carrega todas as informações de autenticação necessárias a partir daí.</span><span class="sxs-lookup"><span data-stu-id="84945-123">When the program runs, it loads all the necessary authentication information from there.</span></span>

## <a name="running-the-code"></a><span data-ttu-id="84945-124">Executar o código</span><span class="sxs-lookup"><span data-stu-id="84945-124">Running the code</span></span>

<span data-ttu-id="84945-125">Execute o início rápido com o comando `go run`.</span><span class="sxs-lookup"><span data-stu-id="84945-125">Run the quickstart with the `go run` command.</span></span>

```bash
cd $GOPATH/src/github.com/azure-samples/azure-sdk-for-go-samples/quickstarts/deploy-vm
go run main.go
```

<span data-ttu-id="84945-126">Se a implementação for bem sucedida, vai ver uma mensagem com o nome de utilizador, endereço IP e a palavra-passe para registar na máquina virtual recém-criada.</span><span class="sxs-lookup"><span data-stu-id="84945-126">If the deployment is successful, you see a message giving the username, IP address, and password for logging into the newly created virtual machine.</span></span> <span data-ttu-id="84945-127">Execute o protocolo SSH nesta máquina para verificar se está operacional.</span><span class="sxs-lookup"><span data-stu-id="84945-127">SSH into this machine to see if it's up and running.</span></span> 

## <a name="cleaning-up"></a><span data-ttu-id="84945-128">Limpeza</span><span class="sxs-lookup"><span data-stu-id="84945-128">Cleaning up</span></span>

<span data-ttu-id="84945-129">Limpe os recursos criados durante o início rápido ao eliminar o grupo de recursos com a CLI.</span><span class="sxs-lookup"><span data-stu-id="84945-129">Clean up the resources created during this quickstart by deleting the resource group with the CLI.</span></span>

```azurecli-interactive
az group delete -n GoVMQuickstart
```

<span data-ttu-id="84945-130">Elimine também o principal de serviço criado.</span><span class="sxs-lookup"><span data-stu-id="84945-130">Also delete the service principal that was created.</span></span> <span data-ttu-id="84945-131">O ficheiro `quickstart.auth` contém uma chave JSON para `clientId`.</span><span class="sxs-lookup"><span data-stu-id="84945-131">In the `quickstart.auth` file, there's a JSON key for `clientId`.</span></span> <span data-ttu-id="84945-132">Copie este valor para a variável de ambiente `CLIENT_ID_VALUE` e execute o seguinte comando da CLI do Azure:</span><span class="sxs-lookup"><span data-stu-id="84945-132">Copy this value to the `CLIENT_ID_VALUE` environment variable and run the following Azure CLI command:</span></span>

```azurecli-interactive
az ad sp delete --id ${CLIENT_ID_VALUE}
```

<span data-ttu-id="84945-133">Onde tem de fornecer o valor para `CLIENT_ID_VALUE` a partir de `quickstart.auth`.</span><span class="sxs-lookup"><span data-stu-id="84945-133">Where you supply the value for `CLIENT_ID_VALUE` from `quickstart.auth`.</span></span>

> [!WARNING]
> <span data-ttu-id="84945-134">Se não eliminar o principal de serviço desta aplicação, este permanecerá ativo no inquilino do Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="84945-134">Failing to delete the service principal for this application leaves it active in your Azure Active Directory tenant.</span></span>
> <span data-ttu-id="84945-135">Apesar de o nome e a palavra-passe do principal de serviço serem gerados como UUIDs, certifique-se de que cumpre as boas práticas de segurança ao eliminar os principais de serviço e as Aplicações do Azure Active Directory obsoletos.</span><span class="sxs-lookup"><span data-stu-id="84945-135">While both the name and password for the service principal are generated as UUIDs, make sure that you follow good security practices by deleting any unused service principals and Azure Active Directory Applications.</span></span>

## <a name="code-in-depth"></a><span data-ttu-id="84945-136">Programe em profundidade</span><span class="sxs-lookup"><span data-stu-id="84945-136">Code in depth</span></span>

<span data-ttu-id="84945-137">O que o código de início rápido faz está num bloco de variáveis e de várias funções pequenas, cada das quais é abordada aqui.</span><span class="sxs-lookup"><span data-stu-id="84945-137">What the quickstart code does is broken down into a block of variables and several small functions, each of which are discussed here.</span></span>

### <a name="variables-constants-and-types"></a><span data-ttu-id="84945-138">Variáveis, constantes e tipos</span><span class="sxs-lookup"><span data-stu-id="84945-138">Variables, constants, and types</span></span>

<span data-ttu-id="84945-139">Uma vez que o início rápido é autónomo, utiliza constantes e variáveis globais.</span><span class="sxs-lookup"><span data-stu-id="84945-139">Since quickstart is self-contained, it uses global constants and variables.</span></span>

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

<span data-ttu-id="84945-140">Os valores são declarados, o que dá os nomes dos recursos criados.</span><span class="sxs-lookup"><span data-stu-id="84945-140">Values are declared which give the names of created resources.</span></span> <span data-ttu-id="84945-141">A localização também está especificada aqui, que pode alterar para ver como as implementações se comportam noutros datacenters.</span><span class="sxs-lookup"><span data-stu-id="84945-141">The location is also specified here, which you can change to see how deployments behave in other datacenters.</span></span> <span data-ttu-id="84945-142">Nem todos os datacenters têm todos os recursos necessários disponíveis.</span><span class="sxs-lookup"><span data-stu-id="84945-142">Not every datacenter has all of the required resources available.</span></span>

<span data-ttu-id="84945-143">O tipo `clientInfo` contém as informações carregadas a partir do ficheiro de autenticação para configurar os clientes no SDK e definir a palavra-passe da VM.</span><span class="sxs-lookup"><span data-stu-id="84945-143">The `clientInfo` type holds the information loaded from the authentication file to set up clients in the SDK and set the VM password.</span></span>

<span data-ttu-id="84945-144">As constantes `templateFile` e `parametersFile` apontam para os ficheiros necessários para a implementação.</span><span class="sxs-lookup"><span data-stu-id="84945-144">The `templateFile` and `parametersFile` constants point to the files needed for deployment.</span></span> <span data-ttu-id="84945-145">O valor `authorizer` será configurado pelo SDK do Go para autenticação e a variável `ctx` é um [Contexto do Go](https://blog.golang.org/context) para as operações de rede.</span><span class="sxs-lookup"><span data-stu-id="84945-145">The `authorizer` will be configured by the Go SDK for authentication, and the `ctx` variable is a [Go context](https://blog.golang.org/context) for the network operations.</span></span>

### <a name="authentication-and-initialization"></a><span data-ttu-id="84945-146">Autenticação e inicialização</span><span class="sxs-lookup"><span data-stu-id="84945-146">Authentication and initialization</span></span>

<span data-ttu-id="84945-147">A função `init` configura a autenticação.</span><span class="sxs-lookup"><span data-stu-id="84945-147">The `init` function sets up authentication.</span></span> <span data-ttu-id="84945-148">Dado que a autenticação é uma condição prévia para tudo o que está no início rápido, faz sentido tê-la como parte da inicialização.</span><span class="sxs-lookup"><span data-stu-id="84945-148">Since authentication is a precondition for everything in the quickstart, it makes sense to have it as part of initialization.</span></span> <span data-ttu-id="84945-149">Também carrega algumas informações necessárias a partir do ficheiro de autenticação para configurar os clientes e a VM.</span><span class="sxs-lookup"><span data-stu-id="84945-149">It also loads some information needed from the authentication file to configure clients and the VM.</span></span>

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

<span data-ttu-id="84945-150">Primeiro, [auth.NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile) é chamado para carregar as informações de autenticação a partir do ficheiro que se encontra em `AZURE_AUTH_LOCATION`.</span><span class="sxs-lookup"><span data-stu-id="84945-150">First, [auth.NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile) is called to load the authentication information from the file located at `AZURE_AUTH_LOCATION`.</span></span> <span data-ttu-id="84945-151">Em seguida, este ficheiro é carregado manualmente pela função `readJSON` (omitida aqui) para solicitar os dois valores necessários à execução do resto do programa: o ID de subscrição do cliente e o segredo do principal de serviço, que também é utilizado para a palavra-passe da VM.</span><span class="sxs-lookup"><span data-stu-id="84945-151">Next, this file is loaded manually by the `readJSON` function (omitted here) to pull the two values needed to run the rest of the program: The subscription ID of the client, and the service principal's secret, which is also used for the VM's password.</span></span>

> [!WARNING]
> <span data-ttu-id="84945-152">Para manter este início rápido simples, a palavra-passe do principal de serviço é reutilizada.</span><span class="sxs-lookup"><span data-stu-id="84945-152">To keep the quickstart simple, the service principal password is reused.</span></span> <span data-ttu-id="84945-153">Na produção, certifique-se de que __nunca__ reutiliza uma palavra-passe que dê acesso aos seus recursos do Azure.</span><span class="sxs-lookup"><span data-stu-id="84945-153">In production, take care to __never__ reuse a password which gives access to your Azure resources.</span></span>

### <a name="flow-of-operations-in-main"></a><span data-ttu-id="84945-154">Fluxo de operações em main()</span><span class="sxs-lookup"><span data-stu-id="84945-154">Flow of operations in main()</span></span>

<span data-ttu-id="84945-155">A função `main` é simples, apenas indica o fluxo de operações e realiza uma verificação de erros.</span><span class="sxs-lookup"><span data-stu-id="84945-155">The `main` function is simple, only indicating the flow of operations and performing error-checking.</span></span>

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

<span data-ttu-id="84945-156">Os passos que o código executa são, por ordem:</span><span class="sxs-lookup"><span data-stu-id="84945-156">The steps that the code runs through are, in order:</span></span>

* <span data-ttu-id="84945-157">Criar o grupo de recursos para o qual implementar (`createGroup`)</span><span class="sxs-lookup"><span data-stu-id="84945-157">Create the resource group to deploy to (`createGroup`)</span></span>
* <span data-ttu-id="84945-158">Criar a implementação dentro deste grupo (`createDeployment`)</span><span class="sxs-lookup"><span data-stu-id="84945-158">Create the deployment within this group (`createDeployment`)</span></span>
* <span data-ttu-id="84945-159">Obter e apresentar informações de início de sessão para a VM implementada (`getLogin`)</span><span class="sxs-lookup"><span data-stu-id="84945-159">Get and display login information for the deployed VM (`getLogin`)</span></span>

### <a name="create-the-resource-group"></a><span data-ttu-id="84945-160">Criar o grupo de recursos</span><span class="sxs-lookup"><span data-stu-id="84945-160">Create the resource group</span></span>

<span data-ttu-id="84945-161">A função `createGroup` cria o grupo de recursos.</span><span class="sxs-lookup"><span data-stu-id="84945-161">The `createGroup` function creates the resource group.</span></span> <span data-ttu-id="84945-162">Ao olhar para o fluxo e argumentos de chamada demonstra a forma como as interações de serviço estão estruturadas no SDK.</span><span class="sxs-lookup"><span data-stu-id="84945-162">Looking at the call flow and arguments demonstrates the way that service interactions are structured in the SDK.</span></span>

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

<span data-ttu-id="84945-163">O fluxo geral de interação com um serviço do Azure é:</span><span class="sxs-lookup"><span data-stu-id="84945-163">The general flow of interacting with an Azure service is:</span></span>

* <span data-ttu-id="84945-164">Criar o cliente com o método `service.New*Client()`, em que `*` é o tipo de recursos de `service` com o qual quer interagir.</span><span class="sxs-lookup"><span data-stu-id="84945-164">Create the client using the `service.New*Client()` method, where `*` is the resource type of the `service` that you want to interact with.</span></span> <span data-ttu-id="84945-165">Esta função recebe sempre um ID de subscrição.</span><span class="sxs-lookup"><span data-stu-id="84945-165">This function always takes a subscription ID.</span></span>
* <span data-ttu-id="84945-166">Defina o método de autorização para o cliente, permitindo que interaja com a API remota.</span><span class="sxs-lookup"><span data-stu-id="84945-166">Set the authorization method for the client, allowing it to interact with the remote API.</span></span>
* <span data-ttu-id="84945-167">Faça a chamada de método no cliente correspondente à API remota.</span><span class="sxs-lookup"><span data-stu-id="84945-167">Make the method call on the client corresponding to the remote API.</span></span> <span data-ttu-id="84945-168">Os métodos do cliente do serviço normalmente aceitam o nome do recurso e um objeto de metadados.</span><span class="sxs-lookup"><span data-stu-id="84945-168">Service client methods usually take the name of the resource and a metadata object.</span></span>

<span data-ttu-id="84945-169">A função [`to.StringPtr`](https://godoc.org/github.com/Azure/go-autorest/autorest/to#StringPtr) é utilizada para realizar uma conversão de tipo aqui.</span><span class="sxs-lookup"><span data-stu-id="84945-169">The [`to.StringPtr`](https://godoc.org/github.com/Azure/go-autorest/autorest/to#StringPtr) function is used to perform a type conversion here.</span></span> <span data-ttu-id="84945-170">Os parâmetros dos métodos de SDK quase exclusivamente aceitam ponteiros, pelo que são fornecidos métodos de conveniência para facilitar as conversões de tipo.</span><span class="sxs-lookup"><span data-stu-id="84945-170">The parameters for SDK methods almost exclusively take pointers, so convenience methods are provided to make the type conversions easy.</span></span> <span data-ttu-id="84945-171">Veja a documentação do módulo [autorest/to](https://godoc.org/github.com/Azure/go-autorest/autorest/to) para obter a lista completa de conversores de conveniência e o respetivo comportamento.</span><span class="sxs-lookup"><span data-stu-id="84945-171">See the documentation for the [autorest/to](https://godoc.org/github.com/Azure/go-autorest/autorest/to) module for the complete list of convenience converters and their behavior.</span></span>

<span data-ttu-id="84945-172">O método `groupsClient.CreateOrUpdate` devolve um ponteiro para um tipo de dados que representa o grupo de recursos.</span><span class="sxs-lookup"><span data-stu-id="84945-172">The `groupsClient.CreateOrUpdate` method returns a pointer to a data type representing the resource group.</span></span> <span data-ttu-id="84945-173">Um valor de retorno direto deste tipo indica uma operação de curto-prazo que deverá ser síncrona.</span><span class="sxs-lookup"><span data-stu-id="84945-173">A direct return value of this kind indicates a short-running operation that is meant to be synchronous.</span></span> <span data-ttu-id="84945-174">Na secção seguinte, verá um exemplo de uma operação de longa duração e como interagir com ela.</span><span class="sxs-lookup"><span data-stu-id="84945-174">In the next section, you'll see an example of a long-running operation and how to interact with it.</span></span>

### <a name="perform-the-deployment"></a><span data-ttu-id="84945-175">Executar a implementação</span><span class="sxs-lookup"><span data-stu-id="84945-175">Perform the deployment</span></span>

<span data-ttu-id="84945-176">Uma vez criado o grupo de recursos, é o momento de executar a implementação.</span><span class="sxs-lookup"><span data-stu-id="84945-176">Once the resource group is created, it's time to run the deployment.</span></span> <span data-ttu-id="84945-177">Este código é dividido em várias secções mais pequenas para dar ênfase a partes diferentes da sua lógica.</span><span class="sxs-lookup"><span data-stu-id="84945-177">This code is broken up into smaller sections to emphasize different parts of its logic.</span></span>

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

<span data-ttu-id="84945-178">Os ficheiros de implementação são carregados por `readJSON`, cujos detalhes são ignorados aqui.</span><span class="sxs-lookup"><span data-stu-id="84945-178">The deployment files are loaded by `readJSON`, the details of which are skipped here.</span></span> <span data-ttu-id="84945-179">Esta função devolve um `*map[string]interface{}`, o tipo utilizado para construir os metadados para a chamada de implementação dos recursos.</span><span class="sxs-lookup"><span data-stu-id="84945-179">This function returns a `*map[string]interface{}`, the type used in constructing the metadata for the resource deployment call.</span></span> <span data-ttu-id="84945-180">A palavra-passe da VM também é definida manualmente nos parâmetros da implementação.</span><span class="sxs-lookup"><span data-stu-id="84945-180">The VM's password is also set manually on the deployment parameters.</span></span>

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

<span data-ttu-id="84945-181">Este código segue o mesmo padrão da criação do grupo de recursos.</span><span class="sxs-lookup"><span data-stu-id="84945-181">This code follows the same pattern as creating the resource group.</span></span> <span data-ttu-id="84945-182">É criado um novo cliente, dada a capacidade de autenticar com o Azure e, em seguida, é chamado um método.</span><span class="sxs-lookup"><span data-stu-id="84945-182">A new client is created, given the ability to authenticate with Azure, and then a method is called.</span></span>
<span data-ttu-id="84945-183">O método tem inclusive o mesmo nome (`CreateOrUpdate`) do método correspondente para os grupos de recursos.</span><span class="sxs-lookup"><span data-stu-id="84945-183">The method even has the same name (`CreateOrUpdate`) as the corresponding method for resource groups.</span></span> <span data-ttu-id="84945-184">Este padrão é utilizado em todo o SDK.</span><span class="sxs-lookup"><span data-stu-id="84945-184">This pattern is seen throughout the SDK.</span></span>
<span data-ttu-id="84945-185">Os métodos que realizam trabalho semelhante costumam ter o mesmo nome.</span><span class="sxs-lookup"><span data-stu-id="84945-185">Methods that perform similar work normally have the same name.</span></span>

<span data-ttu-id="84945-186">A grande diferença está no valor de retorno do método `deploymentsClient.CreateOrUpdate`.</span><span class="sxs-lookup"><span data-stu-id="84945-186">The biggest difference comes in the return value of the `deploymentsClient.CreateOrUpdate` method.</span></span> <span data-ttu-id="84945-187">Este valor é do tipo [Futuro](https://godoc.org/github.com/Azure/go-autorest/autorest/azure#Future), que segue o [padrão de design futuro](https://en.wikipedia.org/wiki/Futures_and_promises).</span><span class="sxs-lookup"><span data-stu-id="84945-187">This value is of the [Future](https://godoc.org/github.com/Azure/go-autorest/autorest/azure#Future) type, which follows the [future design pattern](https://en.wikipedia.org/wiki/Futures_and_promises).</span></span> <span data-ttu-id="84945-188">Os futuros representam uma operação de longa duração no Azure que pode consultar, cancelar ou bloquear após a respetiva conclusão.</span><span class="sxs-lookup"><span data-stu-id="84945-188">Futures represent a long-running operation in Azure that you can poll, cancel, or block on their completion.</span></span>

```go
        //...
    err = deploymentFuture.Future.WaitForCompletion(ctx, deploymentsClient.BaseClient.Client)
    if err != nil {
        return
    }
    return deploymentFuture.Result(deploymentsClient)
}
```

<span data-ttu-id="84945-189">Neste exemplo, o melhor a fazer é aguardar que a operação seja concluída.</span><span class="sxs-lookup"><span data-stu-id="84945-189">For this example, the best thing to do is to wait for the operation to complete.</span></span> <span data-ttu-id="84945-190">Aguardar por um futuro requer um [objeto de contexto](https://blog.golang.org/context) e o cliente que criou o `Future`.</span><span class="sxs-lookup"><span data-stu-id="84945-190">Waiting on a future requires both a [context object](https://blog.golang.org/context) and the client that created the `Future`.</span></span> <span data-ttu-id="84945-191">Existem duas possíveis origens do erro: um erro causado do lado do cliente ao tentar invocar o método, e uma resposta de erro do servidor.</span><span class="sxs-lookup"><span data-stu-id="84945-191">There are two possible error sources here: An error caused on the client side when trying to invoke the method, and an error response from the server.</span></span> <span data-ttu-id="84945-192">A última opção é devolvida como parte da chamada `deploymentFuture.Result`.</span><span class="sxs-lookup"><span data-stu-id="84945-192">The latter is returned as part of the `deploymentFuture.Result` call.</span></span>

### <a name="get-the-assigned-ip-address"></a><span data-ttu-id="84945-193">Obter o endereço IP atribuído</span><span class="sxs-lookup"><span data-stu-id="84945-193">Get the assigned IP address</span></span>

<span data-ttu-id="84945-194">Para fazer alguma coisa com a VM recém-criada, necessita do endereço IP atribuído.</span><span class="sxs-lookup"><span data-stu-id="84945-194">To do anything with the newly created VM, you need the assigned IP address.</span></span> <span data-ttu-id="84945-195">Os endereços IP são os seus próprios recursos do Azure, vinculados aos recursos do Controlador de Interface de Rede (NIC).</span><span class="sxs-lookup"><span data-stu-id="84945-195">IP addresses are their own separate Azure resource, bound to Network Interface Controller (NIC) resources.</span></span>

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

<span data-ttu-id="84945-196">Este método depende das informações armazenadas no ficheiro de parâmetros.</span><span class="sxs-lookup"><span data-stu-id="84945-196">This method relies on information that is stored in the parameters file.</span></span> <span data-ttu-id="84945-197">O código pode consultar diretamente a VM para obter o seu NIC, consultar o NIC para obter o seu recurso de IP e depois consultar o recurso de IP diretamente.</span><span class="sxs-lookup"><span data-stu-id="84945-197">The code could query the VM directly to get its NIC, query the NIC to get its IP resource, and then query the IP resource directly.</span></span> <span data-ttu-id="84945-198">Trata-se de uma longa cadeia de dependências e operações a resolver, o que a torna dispendiosa.</span><span class="sxs-lookup"><span data-stu-id="84945-198">That's a long chain of dependencies and operations to resolve, making it expensive.</span></span> <span data-ttu-id="84945-199">Dado que a informação de JSON é local, pode ser carregada como alternativa.</span><span class="sxs-lookup"><span data-stu-id="84945-199">Since the JSON information is local, it can be loaded instead.</span></span>

<span data-ttu-id="84945-200">O valor para o utilizador da VM também é carregado a partir do JSON.</span><span class="sxs-lookup"><span data-stu-id="84945-200">The value for the VM user is also loaded from the JSON.</span></span> <span data-ttu-id="84945-201">A palavra-passe da VM foi carregada anteriormente a partir do ficheiro de autenticação.</span><span class="sxs-lookup"><span data-stu-id="84945-201">The VM password was loaded earlier from the authentication file.</span></span>

## <a name="next-steps"></a><span data-ttu-id="84945-202">Passos seguintes</span><span class="sxs-lookup"><span data-stu-id="84945-202">Next steps</span></span>

<span data-ttu-id="84945-203">Neste início rápido, utilizou um modelo existente e implementou-o através do Go.</span><span class="sxs-lookup"><span data-stu-id="84945-203">In this quickstart, you took an existing template and deployed it through Go.</span></span> <span data-ttu-id="84945-204">Em seguida, ligou-se à VM recém-criada por SSH.</span><span class="sxs-lookup"><span data-stu-id="84945-204">Then you connected to the newly created VM via SSH.</span></span>

<span data-ttu-id="84945-205">Para saber mais sobre o trabalho com máquinas virtuais no ambiente do Azure com o Go, veja [Exemplos de computação no Azure para o Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute) ou [Exemplos de gestão de recursos do Azure para o Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/resources).</span><span class="sxs-lookup"><span data-stu-id="84945-205">To continue learning about working with virtual machines in the Azure environment with Go, take a look at the [Azure compute samples for Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute) or [Azure resource management samples for Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/resources).</span></span>

<span data-ttu-id="84945-206">Para saber mais sobre os métodos de autenticação disponíveis no SDK e os tipos de autenticação que suportam, veja [Autenticação com o SDK do Azure para Go](azure-sdk-go-authorization.md).</span><span class="sxs-lookup"><span data-stu-id="84945-206">To learn more about the available authentication methods in the SDK, and which authentication types they support, see [Authentication with the Azure SDK for Go](azure-sdk-go-authorization.md).</span></span>
