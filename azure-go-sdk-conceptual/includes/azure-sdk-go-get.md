---
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 02/14/2018
ms.topic: include
ms.prod: azure
ms.technology: azure-cli
ms.openlocfilehash: d021dd8ef4744b7c50b296b231bf63481f92411a
ms.sourcegitcommit: 2a3bd491e087a1d0e7d269bed896c029357d62a6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988075"
---
O [SDK do Azure para Go](https://github.com/Azure/azure-sdk-for-go) é compatível com versões do Go 1.8 e posteriores. Para ambientes com [Perfis do Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-version-profiles), a versão 1.9 Go é o requisito mínimo.
Se o Go não estiver disponível no seu sistema, siga [as instruções de instalação do Go](https://golang.org/doc/install).

Pode obter o SDK do Azure para Go e as respetivas dependências através de `go get`.

```bash
go get -u -d github.com/Azure/azure-sdk-for-go/...
```

> [!WARNING]
> Certifique-se de que capitaliza `Azure` no URL. Caso contrário, pode causar problemas na importação de maiúsculas e minúsculas ao trabalhar com o SDK. Também terá de capitalizar `Azure` nas suas declarações de importação.
