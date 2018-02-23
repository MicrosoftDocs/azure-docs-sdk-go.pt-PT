<span data-ttu-id="ceec7-101">O SDK do Azure para Go é compatível com versões do Go 1.8 e posteriores.</span><span class="sxs-lookup"><span data-stu-id="ceec7-101">The Azure SDK for Go is compatible with Go versions 1.8 and later.</span></span> <span data-ttu-id="ceec7-102">Para ambientes com [Perfis do Azure Stack](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-version-profiles), a versão 1.9 Go é o requisito mínimo.</span><span class="sxs-lookup"><span data-stu-id="ceec7-102">For environments using [Azure Stack Profiles](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-version-profiles), Go version 1.9 is the minimum requirement.</span></span> <span data-ttu-id="ceec7-103">Se o Go não estiver disponível no seu sistema, siga [as instruções de instalação do Go](https://golang.org/doc/install).</span><span class="sxs-lookup"><span data-stu-id="ceec7-103">If you do not have Go available on your system, follow [the Go installation instructions](https://golang.org/doc/install).</span></span>

<span data-ttu-id="ceec7-104">Pode obter o SDK do Azure para Go e as respetivas dependências através de `go get`.</span><span class="sxs-lookup"><span data-stu-id="ceec7-104">You can obtain the Azure SDK for Go and its dependencies via `go get`.</span></span>

```bash
go get -u -d github.com/Azure/azure-sdk-for-go/...
```

> [!WARNING]
> <span data-ttu-id="ceec7-105">Certifique-se de que capitaliza `Azure` no URL.</span><span class="sxs-lookup"><span data-stu-id="ceec7-105">Make sure that you capitalize `Azure` in the URL.</span></span> <span data-ttu-id="ceec7-106">Caso contrário, pode causar problemas na importação de maiúsculas e minúsculas ao trabalhar com o SDK.</span><span class="sxs-lookup"><span data-stu-id="ceec7-106">Doing otherwise can cause case-related import problems when working with the SDK.</span></span> <span data-ttu-id="ceec7-107">Também terá de capitalizar `Azure` nas suas declarações de importação.</span><span class="sxs-lookup"><span data-stu-id="ceec7-107">You also need to capitalize `Azure` in your import statements.</span></span>

