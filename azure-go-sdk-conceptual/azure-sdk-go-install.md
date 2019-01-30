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
ms.openlocfilehash: 2799e3a6c637036eeaf7b20adf8aa55a8a4ab400
ms.sourcegitcommit: 4db332f5e43a5b43032ff9017805d5fd5a650d86
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2019
ms.locfileid: "55145537"
---
# <a name="install-the-azure-sdk-for-go"></a>Instale o Azure SDK para Go

Bem-vindo ao SDK do Azure para Go! O SDK permite-lhe gerir e interagir com os serviços do Azure a partir das suas aplicações Go.

## <a name="get-the-azure-sdk-for-go"></a>Obter o SDK do Azure para Go

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

Alguns serviços do Azure têm o seu próprio SDK do Go e não estão incluídos no pacote principal de SDK do Azure para Go. A seguinte tabela lista os serviços com os seus próprios SDKs e os nomes dos pacotes. Estes pacotes consideram-se todos em pré-visualização.

| Serviço | Pacote |
|---------|---------|
| Blob Storage | [github.com/Azure/azure-storage-blob-go](https://github.com/Azure/azure-storage-blob-go) |
| Armazenamento de Ficheiros | [github.com/Azure/azure-storage-file-go](https://github.com/Azure/azure-storage-file-go) |
| Fila de Armazenamento | [github.com/Azure/azure-storage-queue-go](https://github.com/Azure/azure-storage-queue-go) |
| Hub de Eventos | [github.com/Azure/azure-event-hubs-go](https://github.com/Azure/azure-event-hubs-go) |
| Service Bus | [github.com/Azure/azure-service-bus-go](https://github.com/Azure/azure-service-bus-go) |
| Application Insights | [github.com/Microsoft/ApplicationInsights-go](https://github.com/Microsoft/ApplicationInsights-go) |

## <a name="vendor-the-azure-sdk-for-go"></a>Fornecedor do SDK do Azure para Go

O vendoring do SDK do Azure para Go pode ser realizado através do [dep](https://github.com/golang/dep). Por motivos de estabilidade, recomenda-se o vendoring. Para utilizar `dep` no seu próprio projeto, adicione `github.com/Azure/azure-sdk-for-go` a uma secção `[[constraint]]` do seu `Gopkg.toml`. Por exemplo, para realizar o vendoring na versão `14.0.0`, adicione a entrada seguinte:

```toml
[[constraint]]
name = "github.com/Azure/azure-sdk-for-go"
version = "14.0.0"
```

## <a name="include-the-azure-sdk-for-go-in-your-project"></a>Inclua o SDK do Azure para Go no seu projeto

Para utilizar os serviços do Azure a partir do código Go, importe quaisquer serviços com que interage e os módulos `autorest` necessários.
Obtém uma lista completa dos módulos disponíveis do GoDoc para [serviços disponíveis](https://godoc.org/github.com/Azure/azure-sdk-for-go) e [pacotes do AutoRest](https://godoc.org/github.com/Azure/go-autorest). Os pacotes mais comuns que precisa do `go-autorest` são:

| Pacote | Descrição |
|---------|-------------|
| [github.com/Azure/go-autorest/autorest][autorest] | Objetos para processar a autenticação de cliente do serviço |
| [github.com/Azure/go-autorest/autorest/azure][autorest/azure] | Constantes para interações com os serviços do Azure |
| [github.com/Azure/go-autorest/autorest/adal][autorest/adal] | Mecanismos de autenticação para aceder aos serviços do Azure |
| [github.com/Azure/go-autorest/autorest/to][autorest/to] | Escreva os programas auxiliares de asserção para trabalhar com estruturas de dados do SDK do Azure |

[autorest]: https://godoc.org/github.com/Azure/go-autorest/autorest
[autorest/azure]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure
[autorest/adal]: https://godoc.org/github.com/Azure/go-autorest/autorest/adal
[autorest/to]: https://godoc.org/github.com/Azure/go-autorest/autorest/to

Os pacotes do Go e os serviços do Azure têm versões independentes. As versões dos serviços fazem parte do caminho de importação do módulo, abaixo do módulo `services`. O caminho completo para o módulo é o nome do serviço, seguido da versão no formato `YYYY-MM-DD`, seguido novamente do nome do serviço. Por exemplo, para importar a versão `2017-03-30` do Serviço de computação:

```go
import "github.com/Azure/azure-sdk-for-go/services/compute/mgmt/2017-03-30/compute"
```

Recomenda-se que utilize a versão mais recente de um serviço quando iniciar o desenvolvimento e que seja consistente nessa utilização.
Os requisitos dos serviços podem mudar entre versões, o que pode quebrar o seu código, mesmo que não haja atualizações do SDK para Go durante esse período.

Se precisar de um instantâneo coletivo dos serviços, também pode selecionar uma versão de perfil único. Neste momento, o único perfil bloqueado é a versão `2017-03-09`, que pode não ter as funcionalidades mais recentes dos serviços. Os perfis encontram-se sob o módulo `profiles`, com a respetiva versão no formato `YYYY-MM-DD`. Os serviços são agrupados sob a respetiva versão de perfil. Por exemplo, para importar o módulo de gestão dos Recursos do Azure do perfil `2017-03-09`:

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/resources/mgmt/resources"
```

> [!WARNING]
> Também estão disponíveis os perfis `preview` e `latest`. Não é recomendável utilizá-los. Estes perfis são versões contínuas e podem alterar o comportamento do serviço em qualquer altura.

## <a name="next-steps"></a>Passos seguintes

Para começar a utilizar o SDK do Azure para Go, experimente um início rápido.

* [Implementar uma máquina virtual a partir de um modelo](azure-sdk-go-qs-vm.md)
* [Transferir objetos para o Armazenamento de Blobs do Azure com o SDK de Blob do Azure para Go](/azure/storage/blobs/storage-quickstart-blobs-go?toc=%2fgo%2fazure%2ftoc.json)
* [Ligar à Base de Dados do Azure para PostgreSQL](/azure/postgresql/connect-go?toc=%2fgo%2fazure%2ftoc.json)

Se pretender começar a utilizar outros serviços no SDK Go imediatamente, veja alguns códigos de exemplo disponíveis.

* [Autenticar com os serviços do Azure](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/internal/iam)
* [Implementar novas máquinas virtuais com autenticação SSH](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)
* [Implementar uma imagem de contentor para o Azure Container Instances](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)
* [Criar um cluster no Serviço Kubernetes do Azure](https://github.com/Azure-Samples/azure-sdk-for-go-samples/blob/master/compute)
* [Trabalhar com serviços de Armazenamento do Azure](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage)
* [Todos os exemplos do SDK do Azure para Go](https://github.com/azure-samples/azure-sdk-for-go-samples)
