---
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 09/05/2018
ms.topic: include
ms.prod: azure
ms.technology: azure-cli
ms.openlocfilehash: 5df14f939efdd0550b49261c88c8dc6518ada459
ms.sourcegitcommit: 8b9e10b960150dc08f046ab840d6a5627410db29
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44059276"
---
<span data-ttu-id="15d0b-101">O [SDK do Azure para Go](https://github.com/Azure/azure-sdk-for-go) é compatível com versões 1.8 e superiores do Go.</span><span class="sxs-lookup"><span data-stu-id="15d0b-101">The [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) is compatible with Go versions 1.8 and higher.</span></span> <span data-ttu-id="15d0b-102">Para ambientes com [Perfis do Azure Stack](/azure/azure-stack/user/azure-stack-version-profiles-go), a versão 1.9 Go é o requisito mínimo.</span><span class="sxs-lookup"><span data-stu-id="15d0b-102">For environments using [Azure Stack Profiles](/azure/azure-stack/user/azure-stack-version-profiles-go), Go version 1.9 is the minimum requirement.</span></span>
<span data-ttu-id="15d0b-103">Se precisar de instalar o Go, siga as [instruções de instalação do Go](https://golang.org/doc/install).</span><span class="sxs-lookup"><span data-stu-id="15d0b-103">If you need to install Go, follow [the Go installation instructions](https://golang.org/doc/install).</span></span>

<span data-ttu-id="15d0b-104">Pode transferir o SDK do Azure para Go e as respetivas dependências através de `go get`.</span><span class="sxs-lookup"><span data-stu-id="15d0b-104">You can download the Azure SDK for Go and its dependencies via `go get`.</span></span>

```bash
go get -u -d github.com/Azure/azure-sdk-for-go/...
```

> [!WARNING]
> <span data-ttu-id="15d0b-105">Certifique-se de que capitaliza `Azure` no URL.</span><span class="sxs-lookup"><span data-stu-id="15d0b-105">Make sure that you capitalize `Azure` in the URL.</span></span> <span data-ttu-id="15d0b-106">Caso contrário, pode causar problemas na importação de maiúsculas e minúsculas ao trabalhar com o SDK.</span><span class="sxs-lookup"><span data-stu-id="15d0b-106">Doing otherwise can cause case-related import problems when working with the SDK.</span></span> <span data-ttu-id="15d0b-107">Também terá de capitalizar `Azure` nas suas declarações de importação.</span><span class="sxs-lookup"><span data-stu-id="15d0b-107">You also need to capitalize `Azure` in your import statements.</span></span>
