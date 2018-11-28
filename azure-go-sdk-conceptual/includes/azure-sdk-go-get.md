---
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 09/05/2018
ms.topic: include
ms.prod: azure
ms.technology: azure-cli
ms.openlocfilehash: 5df14f939efdd0550b49261c88c8dc6518ada459
ms.sourcegitcommit: 887b15afcdeaf926a5f3d21b64e4045167fd062c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2018
ms.locfileid: "52293540"
---
O [SDK do Azure para Go](https://github.com/Azure/azure-sdk-for-go) é compatível com versões 1.8 e superiores do Go. Para ambientes com [Perfis do Azure Stack](/azure/azure-stack/user/azure-stack-version-profiles-go), a versão 1.9 Go é o requisito mínimo.
Se precisar de instalar o Go, siga as [instruções de instalação do Go](https://golang.org/doc/install).

Pode transferir o SDK do Azure para Go e as respetivas dependências através de `go get`.

```bash
go get -u -d github.com/Azure/azure-sdk-for-go/...
```

> [!WARNING]
> Certifique-se de que capitaliza `Azure` no URL. Caso contrário, pode causar problemas na importação de maiúsculas e minúsculas ao trabalhar com o SDK. Também terá de capitalizar `Azure` nas suas declarações de importação.
