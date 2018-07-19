---
title: Ferramentas para programadores Go
description: Ferramentas para trabalhar com o SDK do Azure para Go e serviços do Azure
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 07/13/2018
ms.topic: conceptual
ms.prod: azure
ms.technology: azure-sdk-go
ms.devlang: go
ms.openlocfilehash: dfa3912ac13e6f6d52d607f9dcc150f3a5b57602
ms.sourcegitcommit: d1790b317a8fcb4d672c654dac2a925a976589d4
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/14/2018
ms.locfileid: "39039510"
---
# <a name="tools-for-developers-using-the-azure-sdk-for-go"></a>Ferramentas para programadores que utilizam o SDK do Azure para Go

Para escrever código Go de forma eficaz e funcionar perfeitamente com serviços do Azure, aqui estão algumas ferramentas recomendadas.

## <a name="azure-cli"></a>CLI do Azure

A CLI do Azure oferece uma interface de linha de comandos para criar e configurar recursos do Azure nas suas subscrições. A CLI pode ajudá-lo a começar a criar recursos comuns e partilhados do Azure rapidamente, para que se possa concentrar na utilização de serviços mais complexos. A CLI tem funcionalidades de consulta e filtragem, para que possa encaminhar os resultados diretamente para as ferramentas de linha de comandos favoritas. A CLI está disponível para instalação no sistema local, como uma imagem do Docker, ou através do [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

> [!div class="nextstepaction"]
> [Instalar a CLI do Azure](/cli/azure/install-azure-cli)

## <a name="visual-studio-code"></a>Visual Studio Code

O Visual Studio Code é um editor simples que um tem suporte abrangente para a linguagem Go através de extensões. Estas extensões incluem suporte para funcionalidades como a conclusão automática, `impl` modelos, refatorização e depuração. O Visual Studio Code também oferece várias extensões para ferramentas de programador comuns, como o controlo de origem, e oferece até mesmo extensões para interações diretas com serviços do Azure. A Microsoft tem uma extensão-meta oficial que inclui estas extensões do Azure, incluindo uma interface interativa para a CLI do Azure.

* [Instalar Visual Studio Code](https://code.visualstudio.com/Download)
* [Obter a extensão Go do Visual Studio Code](https://code.visualstudio.com/docs/languages/go)
* [Obter a extensão das Ferramentas do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack)

## <a name="cicd-with-azure-devops-project"></a>CI/CD com o projeto de DevOps do Azure

Com o pipeline do Projeto de DevOps do Azure, pode configurar uma compilação contínua e implementar para as suas aplicações Go. Tudo o que precisa é de um repositório disponível de git e pode configurar para implementar e testar diretamente nos seus recursos do Azure. O pipeline de configuração é fácil de criar e gerir e, como é aprovisionado diretamente no Azure, pode controlá-lo da mesma forma que processa os seus outros recursos do Azure.

> [!div class="nextstepaction"]
> [Aprenda a criar um pipeline de CI/CD com o Projeto de DevOps do Azure](/devops-project/azure-devops-project-go)

## <a name="dependency-management-with-dep"></a>Gestão de dependências com dep

Existem várias formas de gerir as suas dependências de pacote e fazer vendoring com Go, uma vez que ainda não existe uma solução oficial. A forma recomendada de fazer esta gestão é com o gestor de dependências `dep`. O SDK do Azure para Go utiliza o dep para o respetivo "vendoring" e obtém garantidamente dependências de forma correta para qualquer outro projeto com o dep.

> [!div class="nextstepaction"]
> [Obter o gestor de dependências do dep](https://github.com/golang/dep)
