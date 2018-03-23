---
title: Ferramentas para programadores Go
description: Ferramentas para trabalhar com o SDK do Azure para Go e serviços do Azure
author: sptramer
ms.author: sttramer
ms.date: 01/30/2018
ms.topic: article
ms.devlang: go
manager: carmonm
ms.openlocfilehash: 054965eb1ea4f1a7556e2968dfbe07b2db69d26f
ms.sourcegitcommit: fcc1786d59d2e32c97a9a8e0748e06f564a961bd
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2018
---
# <a name="tools-for-developers-using-the-azure-sdk-for-go"></a>Ferramentas para programadores que utilizam o SDK do Azure para Go

Para escrever código Go de forma eficaz e funcionar perfeitamente com serviços do Azure, aqui estão algumas ferramentas recomendadas.

## <a name="azure-cli-20"></a>CLI 2.0 do Azure

A CLI 2.0 do Azure oferece uma interface de linha de comandos para criar e configurar recursos do Azure nas suas subscrições. A CLI pode ajudá-lo a começar a criar recursos comuns e partilhados do Azure rapidamente, para que se possa concentrar na utilização de serviços mais complexos. A CLI tem funcionalidades de consulta e filtragem, para que possa encaminhar os resultados diretamente para as ferramentas de linha de comandos favoritas. A CLI está disponível para instalação no sistema local, como uma imagem do Docker, ou através do [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview).

> [!div class="nextstepaction"]
> [Instale a CLI 2.0 do Azure](/cli/azure/install-azure-cli)

## <a name="visual-studio-code"></a>Visual Studio Code

O Visual Studio Code é um editor simples que um tem suporte abrangente para a linguagem Go através de extensões. Estas extensões incluem suporte para funcionalidades como a conclusão automática, `impl` modelos, refatorização e depuração. O Visual Studio Code também oferece várias extensões para ferramentas de programador comuns, como o controlo de origem, e oferece até mesmo extensões para interações diretas com serviços do Azure. A Microsoft tem uma extensão-meta oficial que inclui estas extensões do Azure, incluindo uma interface interativa para a CLI do Azure.

* [Instalar Visual Studio Code](https://code.visualstudio.com/Download)
* [Obter a extensão Go do Visual Studio Code](https://code.visualstudio.com/docs/languages/go)
* [Obter a extensão das Ferramentas do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-azureextensionpack)

## <a name="dependency-management-with-dep"></a>Gestão de dependências com dep

Existem várias formas de gerir as suas dependências de pacote e fazer vendoring com Go, uma vez que ainda não existe uma solução oficial. A forma recomendada de fazer esta gestão é com o gestor de dependências `dep`. O SDK do Azure para Go utiliza o dep para o respetivo "vendoring" e obtém garantidamente dependências de forma correta para qualquer outro projeto com o dep.

> [!div class="nextstepaction"]
> [Obter o gestor de dependências do dep](https://github.com/tools/godep)

## <a name="telemetry-with-application-insights"></a>Telemetria com o Application Insights

O [Application Insights](https://azure.microsoft.com/en-us/services/application-insights/) é um produto de análise que lhe permite recolher facilmente informações de telemetria das suas aplicações e integra-se com o ecossistema do Azure, o Visual Studio Team Services e o GitHub. Pode ser utilizado em muitas aplicações e a Microsoft oferece um SDK Go para trabalhar com o Application Insights.

> [!div class="nextstepaction"]
> [Obter o SDK Go para o Application Insights](https://github.com/Microsoft/ApplicationInsights-Go) 
