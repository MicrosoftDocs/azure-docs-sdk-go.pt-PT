---
title: Ferramentas para programadores que utilizam o SDK do Azure para Go
description: Ferramentas para trabalhar com o SDK do Azure para Go e serviços do Azure
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 09/05/2018
ms.topic: conceptual
ms.prod: azure
ms.technology: azure-sdk-go
ms.devlang: go
ms.openlocfilehash: 70cf7d645f47df29e8e42599a0acd75858144783
ms.sourcegitcommit: 8b9e10b960150dc08f046ab840d6a5627410db29
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44059208"
---
# <a name="tools-for-developers-using-the-azure-sdk-for-go"></a><span data-ttu-id="38d7d-103">Ferramentas para programadores que utilizam o SDK do Azure para Go</span><span class="sxs-lookup"><span data-stu-id="38d7d-103">Tools for developers using the Azure SDK for Go</span></span>

<span data-ttu-id="38d7d-104">Para escrever código Go de forma eficaz e funcionar perfeitamente com serviços do Azure, aqui estão algumas ferramentas recomendadas.</span><span class="sxs-lookup"><span data-stu-id="38d7d-104">To be effective at writing Go code and have it work seamlessly with Azure services, here are some recommended tools.</span></span>

## <a name="azure-cli"></a><span data-ttu-id="38d7d-105">CLI do Azure</span><span class="sxs-lookup"><span data-stu-id="38d7d-105">Azure CLI</span></span>

<span data-ttu-id="38d7d-106">A CLI do Azure oferece uma interface de linha de comandos para criar e configurar recursos do Azure nas suas subscrições.</span><span class="sxs-lookup"><span data-stu-id="38d7d-106">The Azure CLI provides a command-line interface to create and configure Azure resources in your subscriptions.</span></span> <span data-ttu-id="38d7d-107">A CLI pode ajudá-lo a começar a criar recursos comuns e partilhados do Azure rapidamente, para que se possa concentrar na utilização de serviços mais complexos.</span><span class="sxs-lookup"><span data-stu-id="38d7d-107">The CLI can help you get started building common and shared Azure resources quickly, so that you can focus on more complex usage of services.</span></span> <span data-ttu-id="38d7d-108">A CLI tem funcionalidades de consulta e filtragem, para que possa encaminhar os resultados diretamente para as ferramentas de linha de comandos favoritas.</span><span class="sxs-lookup"><span data-stu-id="38d7d-108">The CLI has query and filtering features so you can pipe output directly to your favorite command-line tools.</span></span> <span data-ttu-id="38d7d-109">A CLI está disponível para instalação no sistema local, como uma imagem do Docker, ou através do [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="38d7d-109">The CLI is available for installation on your local system, as a Docker image, or through [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="38d7d-110">Instalar a CLI do Azure</span><span class="sxs-lookup"><span data-stu-id="38d7d-110">Install the Azure CLI</span></span>](/cli/azure/install-azure-cli)

## <a name="visual-studio-code"></a><span data-ttu-id="38d7d-111">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="38d7d-111">Visual Studio Code</span></span>

<span data-ttu-id="38d7d-112">O Visual Studio Code é um editor simples que oferece suporte para Go.</span><span class="sxs-lookup"><span data-stu-id="38d7d-112">Visual Studio Code is a lightweight editor that offers Go support.</span></span> <span data-ttu-id="38d7d-113">Esta extensão disponibiliza funcionalidades como conclusão automática, modelos de `impl`, refatorização e depuração.</span><span class="sxs-lookup"><span data-stu-id="38d7d-113">This extension offers features like autocomplete, `impl` templates, refactoring, and debugging.</span></span> <span data-ttu-id="38d7d-114">O Visual Studio Code também oferece suporte para acesso no editor ao controlo de código fonte e extensões para trabalhar com os serviços do Azure.</span><span class="sxs-lookup"><span data-stu-id="38d7d-114">Visual Studio Code also offers support for in-editor access to source control, and extensions for working with Azure services.</span></span>

* [<span data-ttu-id="38d7d-115">Instalar Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="38d7d-115">Install Visual Studio Code</span></span>](https://code.visualstudio.com/Download)
* [<span data-ttu-id="38d7d-116">Obter a extensão Go do Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="38d7d-116">Get the Visual Studio Code Go extension</span></span>](https://code.visualstudio.com/docs/languages/go)
* [<span data-ttu-id="38d7d-117">Obter a extensão Visual Studio Code Tools do Azure</span><span class="sxs-lookup"><span data-stu-id="38d7d-117">Get the Visual Studio Code Azure Tools extension</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack)

## <a name="cicd-with-azure-devops-project"></a><span data-ttu-id="38d7d-118">CI/CD com o projeto de DevOps do Azure</span><span class="sxs-lookup"><span data-stu-id="38d7d-118">CI/CD with Azure DevOps Project</span></span>

<span data-ttu-id="38d7d-119">Os pipelines de Projeto de DevOps do Azure permitem-lhe configurar um sistema de integração contínua para as suas aplicações Go.</span><span class="sxs-lookup"><span data-stu-id="38d7d-119">Azure DevOps Project pipelines allow you to set up a continuous integration system for your Go applications.</span></span> <span data-ttu-id="38d7d-120">Precisa apenas de um repositório Git e pode implementar e testar diretamente no Azure.</span><span class="sxs-lookup"><span data-stu-id="38d7d-120">All it takes is a git repo, and you can deploy and test directly on Azure.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="38d7d-121">Aprenda a criar um pipeline de CI/CD com o Projeto de DevOps do Azure</span><span class="sxs-lookup"><span data-stu-id="38d7d-121">Learn how to create a CI/CD pipeline with Azure DevOps Project</span></span>](/azure/devops-project/azure-devops-project-go)

## <a name="dependency-management-with-dep"></a><span data-ttu-id="38d7d-122">Gestão de dependências com dep</span><span class="sxs-lookup"><span data-stu-id="38d7d-122">Dependency management with dep</span></span>

<span data-ttu-id="38d7d-123">O SDK do Azure para Go utiliza o dep para a gestão de dependências.</span><span class="sxs-lookup"><span data-stu-id="38d7d-123">The Azure SDK for Go uses dep for dependency management.</span></span> <span data-ttu-id="38d7d-124">O comando dep permite-lhe aplicar requisitos de fornecedor à sua aplicação Go, o que evita conflitos de versão e garante o correto funcionamento do seu projeto.</span><span class="sxs-lookup"><span data-stu-id="38d7d-124">The dep command allows you to pull and vendor requirements for your Go application, avoiding version conflicts and ensuring that your project works correctly.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="38d7d-125">Obter o gestor de dependências do dep</span><span class="sxs-lookup"><span data-stu-id="38d7d-125">Get the dep dependency manager</span></span>](https://github.com/golang/dep)
