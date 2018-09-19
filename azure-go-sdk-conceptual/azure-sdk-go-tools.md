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
# <a name="tools-for-developers-using-the-azure-sdk-for-go"></a>Ferramentas para programadores que utilizam o SDK do Azure para Go

Para escrever código Go de forma eficaz e funcionar perfeitamente com serviços do Azure, aqui estão algumas ferramentas recomendadas.

## <a name="azure-cli"></a>CLI do Azure

A CLI do Azure oferece uma interface de linha de comandos para criar e configurar recursos do Azure nas suas subscrições. A CLI pode ajudá-lo a começar a criar recursos comuns e partilhados do Azure rapidamente, para que se possa concentrar na utilização de serviços mais complexos. A CLI tem funcionalidades de consulta e filtragem, para que possa encaminhar os resultados diretamente para as ferramentas de linha de comandos favoritas. A CLI está disponível para instalação no sistema local, como uma imagem do Docker, ou através do [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

> [!div class="nextstepaction"]
> [Instalar a CLI do Azure](/cli/azure/install-azure-cli)

## <a name="visual-studio-code"></a>Visual Studio Code

O Visual Studio Code é um editor simples que oferece suporte para Go. Esta extensão disponibiliza funcionalidades como conclusão automática, modelos de `impl`, refatorização e depuração. O Visual Studio Code também oferece suporte para acesso no editor ao controlo de código fonte e extensões para trabalhar com os serviços do Azure.

* [Instalar Visual Studio Code](https://code.visualstudio.com/Download)
* [Obter a extensão Go do Visual Studio Code](https://code.visualstudio.com/docs/languages/go)
* [Obter a extensão Visual Studio Code Tools do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack)

## <a name="cicd-with-azure-devops-project"></a>CI/CD com o projeto de DevOps do Azure

Os pipelines de Projeto de DevOps do Azure permitem-lhe configurar um sistema de integração contínua para as suas aplicações Go. Precisa apenas de um repositório Git e pode implementar e testar diretamente no Azure.

> [!div class="nextstepaction"]
> [Aprenda a criar um pipeline de CI/CD com o Projeto de DevOps do Azure](/azure/devops-project/azure-devops-project-go)

## <a name="dependency-management-with-dep"></a>Gestão de dependências com dep

O SDK do Azure para Go utiliza o dep para a gestão de dependências. O comando dep permite-lhe aplicar requisitos de fornecedor à sua aplicação Go, o que evita conflitos de versão e garante o correto funcionamento do seu projeto.

> [!div class="nextstepaction"]
> [Obter o gestor de dependências do dep](https://github.com/golang/dep)
