---
title: Ferramentas para programadores Go
description: Ferramentas para trabalhar com o SDK do Azure para Go e serviços do Azure
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 01/30/2018
ms.topic: conceptual
ms.prod: azure
ms.technology: azure-sdk-go
ms.devlang: go
ms.openlocfilehash: 1e122ab161766023ea146329a5edb13143699b8b
ms.sourcegitcommit: b81b17cbb934399c195bfdcb87137aee935f5234
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34755537"
---
# <a name="tools-for-developers-using-the-azure-sdk-for-go"></a><span data-ttu-id="7007c-103">Ferramentas para programadores que utilizam o SDK do Azure para Go</span><span class="sxs-lookup"><span data-stu-id="7007c-103">Tools for developers using the Azure SDK for Go</span></span>

<span data-ttu-id="7007c-104">Para escrever código Go de forma eficaz e funcionar perfeitamente com serviços do Azure, aqui estão algumas ferramentas recomendadas.</span><span class="sxs-lookup"><span data-stu-id="7007c-104">To be effective at writing Go code and have it work seamlessly with Azure services, here are some recommended tools.</span></span>

## <a name="azure-cli-20"></a><span data-ttu-id="7007c-105">CLI 2.0 do Azure</span><span class="sxs-lookup"><span data-stu-id="7007c-105">Azure CLI 2.0</span></span>

<span data-ttu-id="7007c-106">A CLI 2.0 do Azure oferece uma interface de linha de comandos para criar e configurar recursos do Azure nas suas subscrições.</span><span class="sxs-lookup"><span data-stu-id="7007c-106">The Azure 2.0 CLI provides a command-line interface to create and configure Azure resources in your subscriptions.</span></span> <span data-ttu-id="7007c-107">A CLI pode ajudá-lo a começar a criar recursos comuns e partilhados do Azure rapidamente, para que se possa concentrar na utilização de serviços mais complexos.</span><span class="sxs-lookup"><span data-stu-id="7007c-107">The CLI can help you get started building common and shared Azure resources quickly, so that you can focus on more complex usage of services.</span></span> <span data-ttu-id="7007c-108">A CLI tem funcionalidades de consulta e filtragem, para que possa encaminhar os resultados diretamente para as ferramentas de linha de comandos favoritas.</span><span class="sxs-lookup"><span data-stu-id="7007c-108">The CLI has query and filtering features so you can pipe output directly to your favorite command-line tools.</span></span> <span data-ttu-id="7007c-109">A CLI está disponível para instalação no sistema local, como uma imagem do Docker, ou através do [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="7007c-109">The CLI is available for installation on your local system, as a Docker image, or through [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview).</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="7007c-110">Instale a CLI 2.0 do Azure</span><span class="sxs-lookup"><span data-stu-id="7007c-110">Install the Azure CLI 2.0</span></span>](/cli/azure/install-azure-cli)

## <a name="visual-studio-code"></a><span data-ttu-id="7007c-111">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7007c-111">Visual Studio Code</span></span>

<span data-ttu-id="7007c-112">O Visual Studio Code é um editor simples que um tem suporte abrangente para a linguagem Go através de extensões.</span><span class="sxs-lookup"><span data-stu-id="7007c-112">Visual Studio Code is a lightweight editor that has comprehensive support for the Go language through extensions.</span></span> <span data-ttu-id="7007c-113">Estas extensões incluem suporte para funcionalidades como a conclusão automática, `impl` modelos, refatorização e depuração.</span><span class="sxs-lookup"><span data-stu-id="7007c-113">These extensions include support for features like autocomplete, `impl` templates, refactoring, and debugging.</span></span> <span data-ttu-id="7007c-114">O Visual Studio Code também oferece várias extensões para ferramentas de programador comuns, como o controlo de origem, e oferece até mesmo extensões para interações diretas com serviços do Azure.</span><span class="sxs-lookup"><span data-stu-id="7007c-114">Visual Studio Code also offers many extensions for common developer tools such as source control, and even offers extensions for direct interactions with Azure services.</span></span> <span data-ttu-id="7007c-115">A Microsoft tem uma extensão-meta oficial que inclui estas extensões do Azure, incluindo uma interface interativa para a CLI do Azure.</span><span class="sxs-lookup"><span data-stu-id="7007c-115">Microsoft maintains an official meta-extension including these Azure extensions, including an interactive interface for the Azure CLI.</span></span>

* [<span data-ttu-id="7007c-116">Instalar Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7007c-116">Install Visual Studio Code</span></span>](https://code.visualstudio.com/Download)
* [<span data-ttu-id="7007c-117">Obter a extensão Go do Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7007c-117">Get the Visual Studio Code Go extension</span></span>](https://code.visualstudio.com/docs/languages/go)
* [<span data-ttu-id="7007c-118">Obter a extensão das Ferramentas do Azure</span><span class="sxs-lookup"><span data-stu-id="7007c-118">Get the Azure Tools extension</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack)

## <a name="dependency-management-with-dep"></a><span data-ttu-id="7007c-119">Gestão de dependências com dep</span><span class="sxs-lookup"><span data-stu-id="7007c-119">Dependency management with dep</span></span>

<span data-ttu-id="7007c-120">Existem várias formas de gerir as suas dependências de pacote e fazer vendoring com Go, uma vez que ainda não existe uma solução oficial.</span><span class="sxs-lookup"><span data-stu-id="7007c-120">There are many ways to manage your package dependencies and do vendoring with Go, since there is no official solution yet.</span></span> <span data-ttu-id="7007c-121">A forma recomendada de fazer esta gestão é com o gestor de dependências `dep`.</span><span class="sxs-lookup"><span data-stu-id="7007c-121">The recommended way to perform this management is with the `dep` dependency manager.</span></span> <span data-ttu-id="7007c-122">O SDK do Azure para Go utiliza o dep para o respetivo "vendoring" e obtém garantidamente dependências de forma correta para qualquer outro projeto com o dep.</span><span class="sxs-lookup"><span data-stu-id="7007c-122">The Azure SDK for Go uses dep for its vendoring, and is guaranteed to correctly get dependencies for any other project using dep.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="7007c-123">Obter o gestor de dependências do dep</span><span class="sxs-lookup"><span data-stu-id="7007c-123">Get the dep dependency manager</span></span>](https://github.com/tools/godep)