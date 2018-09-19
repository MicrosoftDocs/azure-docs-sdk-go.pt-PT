---
title: Instale o Azure SDK para Go
description: Como instalar, realizar vendoring e configurar o SDK do Azure para Go.
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 03/14/2018
ms.topic: conceptual
ms.prod: azure
ms.technology: azure-sdk-go
ms.devlang: go
ms.openlocfilehash: 013a771345d96f0fa8dbece3218a01650744f70b
ms.sourcegitcommit: 8b9e10b960150dc08f046ab840d6a5627410db29
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44059191"
---
# <a name="install-the-azure-sdk-for-go"></a><span data-ttu-id="dceb7-103">Instale o Azure SDK para Go</span><span class="sxs-lookup"><span data-stu-id="dceb7-103">Install the Azure SDK for Go</span></span>

<span data-ttu-id="dceb7-104">Bem-vindo ao SDK do Azure para Go!</span><span class="sxs-lookup"><span data-stu-id="dceb7-104">Welcome to the Azure SDK for Go!</span></span> <span data-ttu-id="dceb7-105">O SDK permite-lhe gerir e interagir com os serviços do Azure a partir das suas aplicações Go.</span><span class="sxs-lookup"><span data-stu-id="dceb7-105">The SDK allows you to manage and interact with Azure services from your Go applications.</span></span>

## <a name="get-the-azure-sdk-for-go"></a><span data-ttu-id="dceb7-106">Obter o SDK do Azure para Go</span><span class="sxs-lookup"><span data-stu-id="dceb7-106">Get the Azure SDK for Go</span></span>

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

<span data-ttu-id="dceb7-107">Alguns serviços do Azure têm o seu próprio SDK do Go e não estão incluídos no pacote principal de SDK do Azure para Go.</span><span class="sxs-lookup"><span data-stu-id="dceb7-107">Some Azure services have their own Go SDK and aren't included in the core Azure SDK for Go package.</span></span> <span data-ttu-id="dceb7-108">A seguinte tabela lista os serviços com os seus próprios SDKs e os nomes dos pacotes.</span><span class="sxs-lookup"><span data-stu-id="dceb7-108">The following table lists the services with their own SDKs and their package names.</span></span> <span data-ttu-id="dceb7-109">Estes pacotes consideram-se todos em pré-visualização.</span><span class="sxs-lookup"><span data-stu-id="dceb7-109">These packages are all considered to be in preview.</span></span>

| <span data-ttu-id="dceb7-110">Serviço</span><span class="sxs-lookup"><span data-stu-id="dceb7-110">Service</span></span> | <span data-ttu-id="dceb7-111">Pacote</span><span class="sxs-lookup"><span data-stu-id="dceb7-111">Package</span></span> |
|---------|---------|
| <span data-ttu-id="dceb7-112">Blob Storage</span><span class="sxs-lookup"><span data-stu-id="dceb7-112">Blob Storage</span></span> | [<span data-ttu-id="dceb7-113">github.com/Azure/azure-storage-blob-go</span><span class="sxs-lookup"><span data-stu-id="dceb7-113">github.com/Azure/azure-storage-blob-go</span></span>](https://github.com/Azure/azure-storage-blob-go) |
| <span data-ttu-id="dceb7-114">Armazenamento de Ficheiros</span><span class="sxs-lookup"><span data-stu-id="dceb7-114">File Storage</span></span> | [<span data-ttu-id="dceb7-115">github.com/Azure/azure-storage-file-go</span><span class="sxs-lookup"><span data-stu-id="dceb7-115">github.com/Azure/azure-storage-file-go</span></span>](https://github.com/Azure/azure-storage-file-go) |
| <span data-ttu-id="dceb7-116">Fila de Armazenamento</span><span class="sxs-lookup"><span data-stu-id="dceb7-116">Storage Queue</span></span> | [<span data-ttu-id="dceb7-117">github.com/Azure/azure-storage-queue-go</span><span class="sxs-lookup"><span data-stu-id="dceb7-117">github.com/Azure/azure-storage-queue-go</span></span>](https://github.com/Azure/azure-storage-queue-go) |
| <span data-ttu-id="dceb7-118">Hub de Eventos</span><span class="sxs-lookup"><span data-stu-id="dceb7-118">Event Hub</span></span> | [<span data-ttu-id="dceb7-119">github.com/Azure/azure-event-hubs-go</span><span class="sxs-lookup"><span data-stu-id="dceb7-119">github.com/Azure/azure-event-hubs-go</span></span>](https://github.com/Azure/azure-event-hubs-go) |
| <span data-ttu-id="dceb7-120">Service Bus</span><span class="sxs-lookup"><span data-stu-id="dceb7-120">Service Bus</span></span> | [<span data-ttu-id="dceb7-121">github.com/Azure/azure-service-bus-go</span><span class="sxs-lookup"><span data-stu-id="dceb7-121">github.com/Azure/azure-service-bus-go</span></span>](https://github.com/Azure/azure-service-bus-go) |
| <span data-ttu-id="dceb7-122">Application Insights</span><span class="sxs-lookup"><span data-stu-id="dceb7-122">Application Insights</span></span> | [<span data-ttu-id="dceb7-123">github.com/Microsoft/ApplicationInsights-go</span><span class="sxs-lookup"><span data-stu-id="dceb7-123">github.com/Microsoft/ApplicationInsights-go</span></span>](https://github.com/Microsoft/ApplicationInsights-go) |

## <a name="vendor-the-azure-sdk-for-go"></a><span data-ttu-id="dceb7-124">Fornecedor do SDK do Azure para Go</span><span class="sxs-lookup"><span data-stu-id="dceb7-124">Vendor the Azure SDK for Go</span></span>

<span data-ttu-id="dceb7-125">O vendoring do SDK do Azure para Go pode ser realizado através do [dep](https://github.com/golang/dep).</span><span class="sxs-lookup"><span data-stu-id="dceb7-125">The Azure SDK for Go may be vendored through [dep](https://github.com/golang/dep).</span></span> <span data-ttu-id="dceb7-126">Por motivos de estabilidade, recomenda-se o vendoring.</span><span class="sxs-lookup"><span data-stu-id="dceb7-126">For stability reasons, vendoring is recommended.</span></span> <span data-ttu-id="dceb7-127">Para utilizar `dep` no seu próprio projeto, adicione `github.com/Azure/azure-sdk-for-go` a uma secção `[[constraint]]` do seu `Gopkg.toml`.</span><span class="sxs-lookup"><span data-stu-id="dceb7-127">To use `dep` in your own project, add `github.com/Azure/azure-sdk-for-go` to a `[[constraint]]` section of your `Gopkg.toml`.</span></span> <span data-ttu-id="dceb7-128">Por exemplo, para realizar o vendoring na versão `14.0.0`, adicione a entrada seguinte:</span><span class="sxs-lookup"><span data-stu-id="dceb7-128">For example, to vendor on version `14.0.0`, add the following entry:</span></span>

```toml
[[constraint]]
name = "github.com/Azure/azure-sdk-for-go"
version = "14.0.0"
```

## <a name="include-the-azure-sdk-for-go-in-your-project"></a><span data-ttu-id="dceb7-129">Inclua o SDK do Azure para Go no seu projeto</span><span class="sxs-lookup"><span data-stu-id="dceb7-129">Include the Azure SDK for Go in your project</span></span>

<span data-ttu-id="dceb7-130">Para utilizar os serviços do Azure a partir do código Go, importe quaisquer serviços com que interage e os módulos `autorest` necessários.</span><span class="sxs-lookup"><span data-stu-id="dceb7-130">To use Azure services from your Go code, import any services you interact with and the required `autorest` modules.</span></span>
<span data-ttu-id="dceb7-131">Obtém uma lista completa dos módulos disponíveis do GoDoc para [serviços disponíveis](https://godoc.org/github.com/Azure/azure-sdk-for-go) e [pacotes do AutoRest](https://godoc.org/github.com/Azure/go-autorest).</span><span class="sxs-lookup"><span data-stu-id="dceb7-131">You get a complete list of the available modules from GoDoc for [available services](https://godoc.org/github.com/Azure/azure-sdk-for-go) and [AutoRest packages](https://godoc.org/github.com/Azure/go-autorest).</span></span> <span data-ttu-id="dceb7-132">Os pacotes mais comuns que precisa do `go-autorest` são:</span><span class="sxs-lookup"><span data-stu-id="dceb7-132">The most common packages you need from `go-autorest` are:</span></span>

| <span data-ttu-id="dceb7-133">Pacote</span><span class="sxs-lookup"><span data-stu-id="dceb7-133">Package</span></span> | <span data-ttu-id="dceb7-134">Descrição</span><span class="sxs-lookup"><span data-stu-id="dceb7-134">Description</span></span> |
|---------|-------------|
| <span data-ttu-id="dceb7-135">[github.com/Azure/go-autorest/autorest][autorest]</span><span class="sxs-lookup"><span data-stu-id="dceb7-135">[github.com/Azure/go-autorest/autorest][autorest]</span></span> | <span data-ttu-id="dceb7-136">Objetos para processar a autenticação de cliente do serviço</span><span class="sxs-lookup"><span data-stu-id="dceb7-136">Objects for handling service client authentication</span></span> |
| <span data-ttu-id="dceb7-137">[github.com/Azure/go-autorest/autorest/azure][autorest/azure]</span><span class="sxs-lookup"><span data-stu-id="dceb7-137">[github.com/Azure/go-autorest/autorest/azure][autorest/azure]</span></span> | <span data-ttu-id="dceb7-138">Constantes para interações com os serviços do Azure</span><span class="sxs-lookup"><span data-stu-id="dceb7-138">Constants for interactions with Azure services</span></span> |
| <span data-ttu-id="dceb7-139">[github.com/Azure/go-autorest/autorest/adal][autorest/adal]</span><span class="sxs-lookup"><span data-stu-id="dceb7-139">[github.com/Azure/go-autorest/autorest/adal][autorest/adal]</span></span> | <span data-ttu-id="dceb7-140">Mecanismos de autenticação para aceder aos serviços do Azure</span><span class="sxs-lookup"><span data-stu-id="dceb7-140">Authentication mechanisms for accessing Azure services</span></span> |
| <span data-ttu-id="dceb7-141">[github.com/Azure/go-autorest/autorest/to][autorest/to]</span><span class="sxs-lookup"><span data-stu-id="dceb7-141">[github.com/Azure/go-autorest/autorest/to][autorest/to]</span></span> | <span data-ttu-id="dceb7-142">Escreva os programas auxiliares de asserção para trabalhar com estruturas de dados do SDK do Azure</span><span class="sxs-lookup"><span data-stu-id="dceb7-142">Type assertion helpers for working with Azure SDK data structures</span></span> |

[autorest]: https://godoc.org/github.com/Azure/go-autorest/autorest
[autorest/azure]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure
[autorest/adal]: https://godoc.org/github.com/Azure/go-autorest/autorest/adal
[autorest/to]: https://godoc.org/github.com/Azure/go-autorest/autorest/to

<span data-ttu-id="dceb7-143">Os pacotes do Go e os serviços do Azure têm versões independentes.</span><span class="sxs-lookup"><span data-stu-id="dceb7-143">Go packages and Azure services are versioned independently.</span></span> <span data-ttu-id="dceb7-144">As versões dos serviços fazem parte do caminho de importação do módulo, abaixo do módulo `services`.</span><span class="sxs-lookup"><span data-stu-id="dceb7-144">The service versions are part of the module import path, underneath the `services` module.</span></span> <span data-ttu-id="dceb7-145">O caminho completo para o módulo é o nome do serviço, seguido da versão no formato `YYYY-MM-DD`, seguido novamente do nome do serviço.</span><span class="sxs-lookup"><span data-stu-id="dceb7-145">The full path for the module is the name of the service, followed by the version in `YYYY-MM-DD` format, followed by the service name again.</span></span> <span data-ttu-id="dceb7-146">Por exemplo, para importar a versão `2017-03-30` do Serviço de computação:</span><span class="sxs-lookup"><span data-stu-id="dceb7-146">For example, to import the `2017-03-30` version of the Compute service:</span></span>

```go
import "github.com/Azure/azure-sdk-for-go/services/compute/mgmt/2017-03-30/compute"
```

<span data-ttu-id="dceb7-147">Recomenda-se que utilize a versão mais recente de um serviço quando iniciar o desenvolvimento e que seja consistente nessa utilização.</span><span class="sxs-lookup"><span data-stu-id="dceb7-147">It's recommended that you use the latest version of a service when starting development and keep it consistent.</span></span>
<span data-ttu-id="dceb7-148">Os requisitos dos serviços podem mudar entre versões, o que pode quebrar o seu código, mesmo que não haja atualizações do SDK para Go durante esse período.</span><span class="sxs-lookup"><span data-stu-id="dceb7-148">Service requirements may change between versions that could break your code, even if there are no Go SDK updates during that time.</span></span>

<span data-ttu-id="dceb7-149">Se precisar de um instantâneo coletivo dos serviços, também pode selecionar uma versão de perfil único.</span><span class="sxs-lookup"><span data-stu-id="dceb7-149">If you need a collective snapshot of services, you can also select a single profile version.</span></span> <span data-ttu-id="dceb7-150">Neste momento, o único perfil bloqueado é a versão `2017-03-09`, que pode não ter as funcionalidades mais recentes dos serviços.</span><span class="sxs-lookup"><span data-stu-id="dceb7-150">Right now, the only locked profile is version `2017-03-09`, which may not have the latest features of services.</span></span> <span data-ttu-id="dceb7-151">Os perfis encontram-se sob o módulo `profiles`, com a respetiva versão no formato `YYYY-MM-DD`.</span><span class="sxs-lookup"><span data-stu-id="dceb7-151">Profiles are located under the `profiles` module, with their version in the `YYYY-MM-DD` format.</span></span> <span data-ttu-id="dceb7-152">Os serviços são agrupados sob a respetiva versão de perfil.</span><span class="sxs-lookup"><span data-stu-id="dceb7-152">Services are grouped under their profile version.</span></span> <span data-ttu-id="dceb7-153">Por exemplo, para importar o módulo de gestão dos Recursos do Azure do perfil `2017-03-09`:</span><span class="sxs-lookup"><span data-stu-id="dceb7-153">For example, to import the Azure Resources management module from the `2017-03-09` profile:</span></span>

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/resources/mgmt/resources"
```

> [!WARNING]
> <span data-ttu-id="dceb7-154">Também estão disponíveis os perfis `preview` e `latest`.</span><span class="sxs-lookup"><span data-stu-id="dceb7-154">There are also `preview` and `latest` profiles available.</span></span> <span data-ttu-id="dceb7-155">Não é recomendável utilizá-los.</span><span class="sxs-lookup"><span data-stu-id="dceb7-155">Using them is not recommended.</span></span> <span data-ttu-id="dceb7-156">Estes perfis são versões contínuas e podem alterar o comportamento do serviço em qualquer altura.</span><span class="sxs-lookup"><span data-stu-id="dceb7-156">These profiles are rolling versions and service behavior may change at any time.</span></span>

## <a name="next-steps"></a><span data-ttu-id="dceb7-157">Passos seguintes</span><span class="sxs-lookup"><span data-stu-id="dceb7-157">Next steps</span></span>

<span data-ttu-id="dceb7-158">Para começar a utilizar o SDK do Azure para Go, experimente um início rápido.</span><span class="sxs-lookup"><span data-stu-id="dceb7-158">To begin using the Azure SDK for Go, try out a quickstart.</span></span>

* [<span data-ttu-id="dceb7-159">Implementar uma máquina virtual a partir de um modelo</span><span class="sxs-lookup"><span data-stu-id="dceb7-159">Deploy a virtual machine from a template</span></span>](azure-sdk-go-qs-vm.md)
* [<span data-ttu-id="dceb7-160">Transferir objetos para o Armazenamento de Blobs do Azure com o SDK de Blob do Azure para Go</span><span class="sxs-lookup"><span data-stu-id="dceb7-160">Transfer objects to Azure Blob Storage with the Azure Blob SDK for Go</span></span>](/azure/storage/blobs/storage-quickstart-blobs-go?toc=%2fgo%2fazure%2ftoc.json)
* [<span data-ttu-id="dceb7-161">Ligar à Base de Dados do Azure para PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="dceb7-161">Connect to Azure Database for PostgreSQL</span></span>](/azure/postgresql/connect-go?toc=%2fgo%2fazure%2ftoc.json)

<span data-ttu-id="dceb7-162">Se pretender começar a utilizar outros serviços no SDK Go imediatamente, veja alguns códigos de exemplo disponíveis.</span><span class="sxs-lookup"><span data-stu-id="dceb7-162">If you want to get started with other services in the Go SDK immediately, take a look at some of the available sample code.</span></span>

* [<span data-ttu-id="dceb7-163">Autenticar com os serviços do Azure</span><span class="sxs-lookup"><span data-stu-id="dceb7-163">Authenticate with Azure services</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/iam)
* [<span data-ttu-id="dceb7-164">Implementar novas máquinas virtuais com autenticação SSH</span><span class="sxs-lookup"><span data-stu-id="dceb7-164">Deploy new virtual machines with SSH authentication</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)
* [<span data-ttu-id="dceb7-165">Implementar uma imagem de contentor para o Azure Container Instances</span><span class="sxs-lookup"><span data-stu-id="dceb7-165">Deploy a container image to Azure Container Instances</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/containerinstance)
* [<span data-ttu-id="dceb7-166">Criar um cluster no Serviço Kubernetes do Azure</span><span class="sxs-lookup"><span data-stu-id="dceb7-166">Create a cluster in Azure Kubernetes Service</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/containerservice)
* [<span data-ttu-id="dceb7-167">Trabalhar com serviços de Armazenamento do Azure</span><span class="sxs-lookup"><span data-stu-id="dceb7-167">Work with Azure Storage services</span></span>](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage)
* [<span data-ttu-id="dceb7-168">Todos os exemplos do SDK do Azure para Go</span><span class="sxs-lookup"><span data-stu-id="dceb7-168">All samples for the Azure SDK for Go</span></span>](https://github.com/azure-samples/azure-sdk-for-go-samples)
