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
ms.openlocfilehash: ad77bdff881770512a828b19dc7af4821f4a55ad
ms.sourcegitcommit: f08abf902b48f8173aa6e261084ff2cfc9043305
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/03/2018
---
# <a name="install-the-azure-sdk-for-go"></a>Instale o Azure SDK para Go

Bem-vindo ao SDK do Azure para Go! O SDK permite-lhe gerir e interagir com os serviços do Azure a partir das suas aplicações Go.

## <a name="get-the-azure-sdk-for-go"></a>Obter o SDK do Azure para Go

[!INCLUDE [azure-sdk-go-get](includes/azure-sdk-go-get.md)]

Trabalhar com Blobs de Armazenamento do Azure exige um SDK separado.

```bash
go get -u -d github.com/Azure/azure-storage-blob-go/...
```

## <a name="vendor-the-azure-sdk-for-go"></a>Fornecedor do SDK do Azure para Go

O vendoring do SDK do Azure para Go pode ser realizado através do [dep](https://github.com/golang/dep). Por motivos de estabilidade, recomenda-se o vendoring. Para poder utilizar o suporte `dep`, adicione `github.com/Azure/azure-sdk-for-go` a uma secção `[[constraint]]` do seu `Gopkg.toml`. Por exemplo, para realizar o vendoring na versão `14.0.0`, adicione a entrada seguinte:

```
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

Os módulos dos serviços do Azure têm versões independentemente das APIs do SDK para os mesmos. Estas versões fazem parte do caminho de importação do módulo e são provenientes de uma _versão de serviço_ ou de um _perfil_. Atualmente, é recomendado que utilize uma versão de serviço específica para o desenvolvimento e a versão. Os serviços encontram-se no módulo `services`. O caminho completo para a importação é o nome do serviço, seguido da versão no formato `YYYY-MM-DD`, seguido do nome do serviço novamente. Por exemplo, para incluir a versão `2017-03-30` do Serviço de computação:

```go
import "github.com/Azure/azure-sdk-for-go/services/compute/mgmt/2017-03-30/compute"
```

Recomenda-se atualmente que utilize a versão mais recente de um serviço, a menos que tenha um motivo para fazer de outra forma.

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

* [Autenticar com os serviços do Azure](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/iam)
* [Implementar novas máquinas virtuais com autenticação SSH](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/compute)
* [Implementar uma imagem de contentor para o Azure Container Instances](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/containerinstance)
* [Criar um cluster no Serviço Kubernetes do Azure](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/containerservice)
* [Trabalhar com serviços de Armazenamento do Azure](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage)
* [Todos os exemplos do SDK do Azure para Go](https://github.com/azure-samples/azure-sdk-for-go-samples)
