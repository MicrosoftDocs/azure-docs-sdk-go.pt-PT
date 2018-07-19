---
title: Autenticação com o Azure SDK para Go
description: Saiba mais sobre os métodos de autenticação disponíveis no Azure SDK para Go e como utilizá-los.
services: azure
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 04/03/2018
ms.topic: conceptual
ms.prod: azure
ms.technology: azure-sdk-go
ms.devlang: go
ms.service: active-directory
ms.component: authentication
ms.openlocfilehash: f5e76fc745512a3a52172f560c3a24f510e96feb
ms.sourcegitcommit: d1790b317a8fcb4d672c654dac2a925a976589d4
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/14/2018
ms.locfileid: "39039544"
---
# <a name="authentication-methods-in-the-azure-sdk-for-go"></a>Métodos de autenticação no Azure SDK para Go

O Azure SDK para Go oferece diversos tipos e métodos de autenticação que a sua aplicação pode utilizar. Os métodos de autenticação suportados incluem a solicitação de informações a partir de variáveis do ambiente e a autenticação interativa baseada na Web. Este artigo apresenta os tipos de autenticação disponíveis no SDK e os métodos para utilizá-los. Também irá aprender as melhores práticas para selecionar o tipo de autenticação mais adequado para a sua aplicação.

## <a name="available-authentication-types-and-methods"></a>Tipos e métodos de autenticação disponíveis

O Azure SDK para Go oferece vários tipos diferentes de autenticação que utilizam conjuntos de credenciais diferentes. Cada um destes tipos de autenticação está disponível através de métodos de autenticação diferentes que são a forma como o SDK processa estas credenciais como entradas de dados. A tabela seguinte descreve os tipos de autenticação disponíveis e as situações em que são recomendados para utilização na sua aplicação.

| Tipo de autenticação | Recomendado quando... |
|---------------------|---------------------|
| Autenticação baseada em certificado | Tem um certificado X509 configurado para um utilizador ou principal de serviço do Azure Active Directory (AAD). Para obter mais informações, consulte [Introdução à autenticação baseada em certificado no Azure Active Directory]. |
| Credenciais de cliente | Tem um principal de serviço configurado para esta aplicação ou uma classe de aplicações a que pertence. Para obter mais informações, veja [Criar um principal de serviço com a CLI do Azure]. |
| Identidade de Serviço Gerida (MSI) | A aplicação está a ser executada num recurso do Azure configurado com Identidade de Serviço Gerida (MSI). Para obter mais informações, consulte [Identidade de Serviço Gerida (MSI) para recursos do Azure]. |
| Token de dispositivo | A aplicação destina-se a ser utilizada __apenas__ de forma interativa e terá diversos utilizadores, potencialmente a partir de múltiplos inquilinos do AAD. Os utilizadores têm acesso a um browser para iniciar sessão. Para obter mais informações, consulte [Utilizar a autenticação de token de dispositivo](#use-device-token-authentication).|
| Nome de utilizador/palavra-passe | Tem uma aplicação interativa que não pode utilizar qualquer outro método de autenticação. Os utilizadores não têm a autenticação multifator ativada para o início de sessão do AAD. |

> [!IMPORTANT]
> Se utilizar outro tipo de autenticação que não as credenciais do cliente, a aplicação tem de estar registada no Azure Active Directory. Para obter mais informações, consulte [Integrar aplicações no Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications).
>
> [!NOTE]
> A menos que tenha requisitos especiais, evite a autenticação de nome de utilizador/palavra-passe. Em situações em que o início de sessão baseado no utilizador é adequado, a autenticação de token de dispositivo pode ser utilizada como alternativa.

[Introdução à autenticação baseada em certificado no Azure Active Directory]: /azure/active-directory/active-directory-certificate-based-authentication-get-started
[Criar um principal de serviço com a CLI do Azure]: /cli/azure/create-an-azure-service-principal-azure-cli
[Identidade de Serviço Gerida (MSI) para recursos do Azure]: /azure/active-directory/managed-service-identity/overview

Estes tipos de autenticação estão disponíveis através de métodos diferentes. A [_autenticação baseada no ambiente_ ](#use-environment-based-authentication) lê as credenciais diretamente a partir do ambiente do programa. A [_autenticação baseada em ficheiro_ ](#use-file-based-authentication) carrega um ficheiro que contém as credenciais do principal de serviço. A [_autenticação baseada em cliente_ ](#use-an-authentication-client) utiliza um objeto no código do Go e torna-o responsável por fornecer as credenciais durante a execução do programa. Por fim, a [_autenticação de token de dispositivo_](#use-device-token-authentication) exige que os utilizadores iniciem sessão interativamente através de um browser com um token e não pode ser utilizada com a autenticação baseada no ambiente ou em ficheiros.

Todas as funções de autenticação e tipos estão disponíveis no pacote `github.com/Azure/go-autorest/autorest/azure/auth`.

> [!NOTE]
> Exceto se tiver requisitos especiais, evite utilizar a autenticação baseada no cliente. Este método de autenticação encoraja práticas incorretas. Em particular, utilizar a autenticação baseada em cliente convida à criação de credenciais hard-coded. Escrever código personalizado para autenticação também poderá resultar em erros em versões futuras do SDK caso os requisitos de autenticação mudem.

## <a name="use-environment-based-authentication"></a>Utilizar a autenticação baseada no ambiente

Se estiver a executar a aplicação num ambiente rigorosamente controlado, tal como num contentor, a autenticação baseada no ambiente é uma opção de natural. Configure o ambiente da shell antes de executar a aplicação e o Go SDK lê estas variáveis de ambiente em runtime para efetuar a autenticação com o Azure.

Autenticação baseada no ambiente suporta todos os métodos de autenticação, exceto os tokens de dispositivo, avaliados pela seguinte ordem: credenciais de cliente, certificados, nome de utilizador/palavra-passe e Identidade de Serviço Gerida (MSI). Se uma variável de ambiente necessária não estiver definida ou o SDK obtiver uma recusa do serviço de autenticação, é utilizado o tipo de autenticação seguinte. Se o SDK não conseguir efetuar a autenticação a partir do ambiente, devolve um erro.

A tabela seguinte fornece detalhes sobre as variáveis de ambiente que têm de ser definidas para cada tipo de autenticação suportado pela autenticação baseada no ambiente.

| Tipo de autenticação | Variável de ambiente | Descrição |
| ------------------- | -------------------- | ----------- |
| __Credenciais de cliente__ | `AZURE_TENANT_ID` | O ID do inquilino do Active Directory a que o principal de serviço pertence. |
| | `AZURE_CLIENT_ID` | O nome ou o ID do principal de serviço. |
| | `AZURE_CLIENT_SECRET` | O segredo associado ao principal de serviço. |
| __Certificado__ | `AZURE_TENANT_ID` | O ID do inquilino do Active Directory com o qual o certificado está registado. |
| | `AZURE_CLIENT_ID` | O ID de cliente de aplicação associado ao certificado. |
| | `AZURE_CERTIFICATE_PATH` | O caminho para o ficheiro de certificado de cliente. |
| | `AZURE_CERTIFICATE_PASSWORD` | A palavra-passe do certificado de cliente. |
| __Nome de Utilizador/Palavra-passe__ | `AZURE_TENANT_ID` | O ID do inquilino do Active Directory a que o utilizador pertence. |
| | `AZURE_CLIENT_ID` | O ID do cliente de aplicação. |
| | `AZURE_USERNAME` | O nome de utilizador para início de sessão. |
| | `AZURE_PASSWORD` | A palavra-passe para início de sessão. |
| __MSI__ | | A MSI não necessita de quaisquer credenciais para ser definida. A aplicação tem de estar em execução num recurso do Azure configurado para utilizar MSI. Para obter mais detalhes, consulte [Identidade de Serviço Gerida (MSI) para recursos do Azure]. |

Se precisar de ligar a um ponto final de cloud ou de gestão diferente da cloud pública predefinida do Azure, também pode definir as variáveis de ambiente indicadas a seguir. Os motivos mais comuns para defini-las são a utilização do Azure Stack, de uma cloud numa região geográfica diferente ou do modelo de implementação Clássico do Azure.

| Variável de ambiente | Descrição  |
|----------------------|--------------|
| `AZURE_ENVIRONMENT` | O nome do ambiente de cloud para ligação. |
| `AZURE_AD_RESOURCE` | O ID de recurso de Active Directory a utilizar na ligação. Deve ser um URI que aponta para o ponto final de gestão. |

Quando utilizar a autenticação baseada no ambiente, chame a função [NewAuthorizerFromEnvironment](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromEnvironment) para obter o objeto authorizer. Este objeto é então definido na propriedade `Authorizer` dos clientes para permitir o acesso ao Azure.

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
authorizer, err := auth.NewAuthorizerFromEnvironment()
```

### <a name="authentication-on-azure-stack"></a>Autenticação no Azure Stack

Para autenticar no Azure Stack, tem de definir as seguintes variáveis:

| Variável de ambiente | Descrição  |
|----------------------|--------------|
| `AZURE_AD_ENDPOINT` | O ponto final do Active Directory. |
| `AZURE_AD_RESOURCE` | O ID de recurso do Active Directory. |

Estas variáveis podem ser obtidas a partir das informações de metadados do Azure Stack. Para obter os metadados, abra um browser web no seu ambiente do Azure Stack e utilize o url: `(ResourceManagerURL)/metadata/endpoints?api-version=1.0`

O `ResourceManagerURL` varia com base no nome de região, nome da máquina e nome de domínio completamente qualificado externo (FQDN) da sua implementação do Azure Stack:

| Ambiente | ResourceManagerURL |
|----------------------|--------------|
| Kit de Desenvolvimento | `https://management.local.azurestack.external/` |
| Sistemas Integrados | `https://management.(region).ext-(machine-name).(FQDN)` |

Para obter mais detalhes sobre como utilizar o SDK for Go no Azure Stack, consulte [Utilizar perfis de versão de API com o Go no Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles-go)

## <a name="use-file-based-authentication"></a>Utilizar a autenticação baseada em ficheiro

A Autenticação baseada em ficheiro só funciona com credenciais de cliente armazenadas num formato de ficheiro local gerado pela [CLI do Azure](/cli/azure). Pode criar facilmente este ficheiro quando criar um novo principal de serviço com o parâmetro `--sdk-auth`. Se pretender utilizar a autenticação baseada em ficheiro, certifique-se de que este argumento é fornecido ao criar um principal de serviço. Uma vez que a CLI imprime a saída de dados `stdout`, redirecione-a para um ficheiro.

```azurecli
az ad sp create-for-rbac --sdk-auth > azure.auth
```

Defina a variável de ambiente `AZURE_AUTH_LOCATION` para onde está localizado o ficheiro de autorização. Esta variável de ambiente é lida pela aplicação e as credenciais que contém são analisadas. Se tiver de selecionar o ficheiro de autorização em runtime, manipule o ambiente do programa utilizando a função [os.Setenv](https://golang.org/pkg/os/#Setenv).

Para carregar as informações de autenticação, chame a função [NewAuthorizerFromFile](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewAuthorizerFromFile). Ao contrário da autorização baseada no ambiente, a autorização baseada em ficheiro necessita de um ponto final de recurso.

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
authorizer, err := NewAuthorizerFromFile(azure.PublicCloud.ResourceManagerEndpoint)
```

Para saber mais sobre a utilização de principais de serviço e a gestão das permissões de acesso, veja [Criar um principal de serviço com a CLI do Azure].

## <a name="use-device-token-authentication"></a>Utilizar a autenticação de token de dispositivo

Se pretender que os utilizadores iniciem sessão interativamente, a melhor forma para oferecer essa funcionalidade é através da autenticação de token de dispositivo. Este fluxo de autenticação disponibiliza um token ao utilizador para ser colado num site de início de sessão da Microsoft, onde podem autenticar com uma conta do Azure Active Directory (AAD). Este método de autenticação suporta contas com a autenticação multifator ativada, ao contrário da autenticação de nome de utilizador/palavra-passe padrão.

Para utilizar a autenticação de token de dispositivo, crie um objeto authorizer [DeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig) com a função [NewDeviceFlowConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#NewDeviceFlowConfig). Chame [Authorizer](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig.Authorizer) no objeto resultante para iniciar o processo de autenticação. Os blocos de autenticação do fluxo do dispositivo programam a execução até todo o fluxo de autenticação ser concluído.

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
deviceConfig := auth.NewDeviceFlowConfig(applicationID, tenantID)
authorizer, err := deviceConfig.Authorizer()
```

## <a name="use-an-authentication-client"></a>Utilizar um cliente de autenticação

Se necessitar de um tipo de autenticação específico e quiser que o seu programa execute o carregamento das informações de autenticação do utilizador, pode utilizar qualquer cliente que esteja em conformidade com a interface [auth.AuthorizerConfig](https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#AuthorizerConfig). Utilize um tipo que implemente esta interface quando pretender um programa interativo, utilizar ficheiros de configuração especializados ou tiver um requisito que o impede de utilizar outro método de autenticação.

> [!WARNING]
> Nunca utilize credenciais do Azure hard-coded numa aplicação. Colocar segredos no binário de uma aplicação torna mais fácil para um atacante extrai-las, quer a aplicação esteja a ser executada ou não. Tal coloca em risco todos os recursos do Azure para os quais as credenciais estão autorizadas!

A tabela seguinte lista os tipos no SDK que estão em conformidade com a interface `AuthorizerConfig`.

| Tipo de autenticação | Tipo de authorizer |
|---------------------|-----------------------|
| Autenticação baseada em certificado | [ClientCertificateConfig] |
| Credenciais de cliente | [ClientCredentialsConfig] |
| Identidade de Serviço Gerida (MSI) | [MSIConfig] |
| Nome de utilizador/palavra-passe | [UsernamePasswordConfig] |

[ClientCertificateConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#ClientCertificateConfig
[ClientCredentialsConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#ClientCredentialsConfig
[MSIConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#MSIConfig
[DeviceFlowConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#DeviceFlowConfig
[UsernamePasswordConfig]: https://godoc.org/github.com/Azure/go-autorest/autorest/azure/auth#UsernamePasswordConfig

Crie um autenticador com a função `New` associada e, em seguida, chame `Authorize` no objeto resultante para efetuar a autenticação. Por exemplo, para utilizar a autenticação baseada em certificado:

```go
import "github.com/Azure/go-autorest/autorest/azure/auth"
certificateAuthorizer := auth.NewClientCertificateConfig(certificatePath, certificatePassword, clientID, tenantID)
authorizerToken, err := certificateAuthorizer.Authorize()
```
