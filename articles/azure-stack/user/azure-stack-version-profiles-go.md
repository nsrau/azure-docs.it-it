---
title: Usando i profili delle versioni API con GO in Azure Stack | Microsoft Docs
description: Imparare a utilizzare i profili delle versioni API con GO in Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 30969dcb7549f4107eb72b54d444a639c35b7ba0
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264783"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Usare i profili delle versioni API con Go in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

## <a name="go-and-version-profiles"></a>Profili di go e versione

Un profilo è una combinazione di diversi tipi di risorse con versioni diverse da servizi diversi. Usa un profilo consente di combinare e abbinare tra diversi tipi di risorse. I profili è possono specificare:

 - Stabilità dell'applicazione da blocchi a specifiche versioni di API.
 - Compatibilità dell'applicazione con Azure Stack e a livello di area Data Center di Azure.

In Go SDK, sono disponibili sotto i profili di profili / percorso, con la rispettiva versione nel **AAAA-MM-GG** formato. Ora, il più recente Azure Stack è versione del profilo **2017-03-09**. Per importare un determinato servizio da un profilo, è necessario importare il modulo corrispondente dal profilo. Ad esempio, per importare **Compute** servizio **2017-03-09** profilo:

````go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/compute/mgmt/compute" 
````

## <a name="install-azure-sdk-for-go"></a>Installare Azure SDK per Go

  1. Installare Git. Per istruzioni, vedere [Guida introduttiva - installazione di Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
  2. Installare il [linguaggio di programmazione Go](https://golang.org/dl).  
  I profili delle API per Azure richiederà Go versione 1.9 o versione successiva.
  3. Installare il SDK di Azure passare e le relative dipendenze eseguendo il comando bash seguente:
  ```
    go get -u -d github.com/Azure/azure-sdk-for-go/...
  ```

### <a name="the-go-sdk"></a>GO SDK

È possibile trovare altre informazioni sul SDK di Azure passare a:
- Azure SDK in corrispondenza di andare [installazione di Azure SDK per Go](https://docs.microsoft.com/go/azure/azure-sdk-go-install).
- Go SDK di Azure è disponibile pubblicamente in GitHub all'indirizzo [azure sdk per go](https://github.com/Azure/azure-sdk-for-go).

### <a name="go-autorest-dependencies"></a>Dipendenze di go-AutoRest

GO SDK varia in base i moduli di Azure. Go-AutoRest per inviare richieste REST agli endpoint di Azure Resource Manager. È necessario importare le dipendenze del modulo Azure Go-AutoRest dal [Go su GitHub Azure-AutoRest](https://github.com/Azure/go-autorest). È possibile trovare i comandi bash installa il **installare** sezione.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Come usare i profili di GO SDK in Azure Stack

Per eseguire un esempio di codice di Go su Azure Stack:
  1. Installare Azure SDK per Go e le relative dipendenze. Per istruzioni vedere la sezione precedente [installare Azure SDK per Go](#install-azure-sdk-for-go).
  2. Ottenere le informazioni sui metadati dall'endpoint di Resource Manager. L'endpoint restituisce un file JSON con le informazioni necessarie per eseguire il codice Go.

  > [!Note]  
  > Il **ResourceManagerUrl** in Azure Stack Development Kit (ASDK) è: `https://management.local.azurestack.external/`  
  > Il **ResourceManagerUrl** in sistemi integrati è: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
  > Per recuperare i metadati richiesti: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
  File JSON di esempio:

  ```json
  { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
    "graphEndpoint": "https://graph.windows.net/",  
    "portal Endpoint": "https://portal.local.azurestack.external/", 
    "authentication": {
      "loginEndpoint": "https://login.windows.net/", 
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
    }
  }
  ```

  3. Se non è disponibile, creare una sottoscrizione e salvare l'ID sottoscrizione da usare in un secondo momento. Per informazioni sulla creazione di una sottoscrizione, vedere [creare le sottoscrizioni di offerte in Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
  4. Creare un'entità servizio con ambito "Sottoscrizione" e **proprietario** ruolo. ID e segreto, salvare le entità servizio. Per informazioni sulla creazione di un'entità servizio per Azure Stack, vedere [creare un'entità servizio](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-azure-ad). L'ambiente Azure Stack è configurato.
  5. Importare un modulo del servizio dal profilo Go SDK nel codice. La versione corrente del profilo Azure Stack **2017-03-09**. Ad esempio, per importare il modulo di rete dal **2017-03-09** tipo di profilo: 

  ````go
    package main 
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
  ````
  
  6. Nella funzione, creare e autenticare un client con un **New** chiamata di funzione di Client. Per creare un client di rete virtuale, è possibile usare il codice seguente:  

  ````go
  package main 

  import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 

  func main() { 
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
  ````

  Impostare `<baseURI>` per il **ResourceManagerUrl** valore usato nel secondo passaggio.
  Impostare `<subscriptionID>` per il **SubscriptionID** valore salvato dal passaggio tre.
  Per creare token, vedere autenticazione sezione riportata di seguito.  

  7. Richiamare metodi dell'API usando il client che è stato creato nel passaggio precedente. Ad esempio, per creare una rete virtuale usando il client dal passaggio precedente: 
  
````go
package main

import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 
func main() { 
  vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
  vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

  vnetClient .CreateOrUpdate( ) 
````
  
  Per un esempio completo di creazione di una rete virtuale in Azure Stack tramite profilo Go SDK, vedere [esempio](#example).

## <a name="authentication"></a>Authentication

Per ottenere la proprietà provider di autorizzazioni da Azure Active Directory usando Go SDK, installare i moduli di Go-AutoRest. Questi moduli devono essere già installati con l'installazione di "Go SDK"; in caso contrario, installare il [pacchetto di autenticazione in GitHub](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

Provider di autorizzazioni deve essere impostato come provider di autorizzazioni per il client di risorsa. Esistono diversi metodi per ottenere un provider di autorizzazioni; per un elenco completo, vedere qui.

In questa sezione presenta un modo comune per ottenere i token di provider di autorizzazioni in Azure Stack usando le credenziali del client:

  1. Se un'entità servizio con ruolo di proprietario della sottoscrizione è disponibile, ignorare questo passaggio. In caso contrario, creare un'entità servizio [istruzioni]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) e assegnare un ruolo "proprietario" con ambito limitato alla sottoscrizione [istruzioni]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal). Salvare l'ID applicazione dell'entità servizio e il segreto. 

  2. Importazione **adal** pacchetto da Go AutoRest nel codice. 
  
  ````go
  package main
  import "github.com/Azure/go-autorest/autorest/adal" 
  ````

  3. Creare un oauthConfig tramite il metodo NewOAuthConfig dalla **adal** modulo. 
  
  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/ada1" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
  ````
   
  Impostare `<activeDirectoryEndpoint>` al valore di "loginEndpoint" proprietà dai metadati ResourceManagerUrl recuperata nella sezione precedente di questo documento.
  Impostare `<tenantID>` valore per l'ID tenant di Azure Stack. 

  4. Infine, creare un token dell'entità servizio usando il metodo NewServicePrincipalToken dal modulo adal. 

  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/adal" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
  ````
  
  Impostare `<activeDirectoryResourceID>` a uno dei valori di "destinatari" elenco dai metadati ResourceManagerUrl recuperato nella sezione precedente di questo documento.  
  Impostare `<clientID>` all'applicazione dell'entità servizio salvato ID entità servizio è stato creato nella sezione precedente di questo documento.  
  Impostare `<clientSecret>` all'applicazione dell'entità servizio salvato segreto entità servizio è stato creato nella sezione precedente di questo documento.  

## <a name="example"></a>Esempio

In questa sezione viene illustrato un esempio di codice di Go per creare una rete virtuale in Azure Stack. Per esempi completi di Go SDK, vedere [repository degli esempi di Azure Go SDk](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Esempi di Stack di Azure sono disponibili in hybrid / percorso all'interno di cartelle del servizio del repository.

> [!Note]  
> Per eseguire il codice in questo esempio, verificare che la sottoscrizione usata disponga **Network** elencato come provider di risorse **Registered**. Per verificarlo, cercare la sottoscrizione nel portale di Azure Stack e fare clic su **i provider di risorse.**

1. Importare i pacchetti necessari nel codice. Si devono utilizzare il profilo più recenti disponibile in Azure Stack per importare il modulo di rete. 
  
  ````go
  package main

  import (
      "context"
      "fmt"
      "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
      "github.com/Azure/go-autorest/autorest"
      "github.com/Azure/go-autorest/autorest/adal"
      "github.com/Azure/go-autorest/autorest/to"
  )
  ````

2. Definire le variabili di ambiente. Per creare una rete virtuale che è necessario disporre di un gruppo di risorse. 

  ````go
  var (
      activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
      tenantID = "yourAzureStackTenantID"
      clientID = "yourServicePrincipalApplicationID"
      clientSecret = "yourServicePrincipalSecret"
      activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
      subscriptionID = "yourSubscriptionID"
      baseURI = "yourResourceManagerURL"
      resourceGroupName = "existingResourceGroupName"
  )
  ````

3. Ora che sono stati definiti le variabili di ambiente, aggiungere un metodo per creare token di autenticazione tramite **adal** pacchetto. Vedere i dettagli sull'autenticazione nella sezione precedente.
  
  ````go
  //CreateToken creates a service principal token
  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
  }
  ````

4. Aggiungere il metodo principale. Il metodo principale innanzitutto di ottenere un token tramite il metodo definito nel passaggio precedente. Quindi, crea un client mediante il modulo di rete dal profilo. Infine, consente di creare una rete virtuale. 
  
````go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
    "github.com/Azure/go-autorest/autorest"
    "github.com/Azure/go-autorest/autorest/adal"
    "github.com/Azure/go-autorest/autorest/to"
)

var (
    activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
    tenantID = "yourAzureStackTenantID"
    clientID = "yourServicePrincipalApplicationID"
    clientSecret = "yourServicePrincipalSecret"
    activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
    subscriptionID = "yourSubscriptionID"
    baseURI = "yourResourceManagerURL"
    resourceGroupName = "existingResourceGroupName"
)

//CreateToken creates a service principal token
func CreateToken() (adal.OAuthTokenProvider, error) {
    var token adal.OAuthTokenProvider
    oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
    token, err = adal.NewServicePrincipalToken(
        *oauthConfig,
        clientID,
        clientSecret,
        activeDirectoryResourceID)
    return token, err
}

func main() {
    token, _ := CreateToken()
    vnetClient := network.NewVirtualNetworksClientWithBaseURI(baseURI, subscriptionID)
    vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
    future, _ := vnetClient.CreateOrUpdate(
        context.Background(),
        resourceGroupName,
        "sampleVnetName",
        network.VirtualNetwork{
            Location: to.StringPtr("local"),
            VirtualNetworkPropertiesFormat: &network.VirtualNetworkPropertiesFormat{
                AddressSpace: &network.AddressSpace{
                    AddressPrefixes: &[]string{"10.0.0.0/8"},
                },
                Subnets: &[]network.Subnet{
                    {
                        Name: to.StringPtr("subnetName"),
                        SubnetPropertiesFormat: &network.SubnetPropertiesFormat{
                            AddressPrefix: to.StringPtr("10.0.0.0/16"),
                        },
                    },
                },
            },
        })
    err := future.WaitForCompletion(context.Background(), vnetClient.Client)
    if err != nil {
        fmt.Printf(err.Error())
        return
    }
}

````

## <a name="next-steps"></a>Passaggi successivi
* [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md)
* [Configurare l'ambiente di PowerShell dell'utente Azure Stack](azure-stack-powershell-configure-user.md)  
