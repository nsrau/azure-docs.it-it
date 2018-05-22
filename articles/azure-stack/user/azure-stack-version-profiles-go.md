---
title: Utilizzo dei profili versione API con GO nello Stack di Azure | Documenti Microsoft
description: Informazioni sull'utilizzo dei profili versione API con Vai nello Stack di Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: dd2d0c46c0829a73d32c96b506b9f2111eda3c84
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Usare i profili di versione API con Vai nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

## <a name="go-and-version-profiles"></a>Profili go e versione

Un profilo è una combinazione di diversi tipi di risorse con versioni diverse da servizi diversi. Usa un profilo consente di combinare e vi è corrispondenza tra tipi diversi di risorse. Possono fornire profili:

 - Stabilità dell'applicazione grazie al blocco a versioni specifiche di API.
 - Compatibilità dell'applicazione con Stack di Azure e Data Center regionale di Azure.

Nel SDK di Go, i profili sono disponibili con i profili / percorso con la versione nel **AAAA-MM-GG** formato. Ora, il più recente Stack Azure versione del profilo viene **2017-03-09**. Per importare un servizio specifico da un profilo, è necessario importare il modulo corrispondente dal profilo. Ad esempio, per importare **calcolo** servizio **2017-03-09** profilo:

````go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/compute/mgmt/compute" 
````

## <a name="install-azure-sdk-for-go"></a>Installare Azure SDK per Go

  1. Installare Git. Per istruzioni, vedere [Getting Started - installazione Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
  2. Installare il [Go linguaggio di programmazione](https://golang.org/dl).  
  I profili di API per Azure richiederà Go 1,9 o versione successiva.
  3. Installare il SDK di Azure andare e le relative dipendenze eseguendo il seguente comando bash:
  ```
    go get -u -d github.com/Azure/azure-sdk-for-go/...
  ```

### <a name="the-go-sdk"></a>il SDK ANDARE

È possibile trovare ulteriori informazioni sul SDK di Azure passare a:
- Azure passare SDK alla [installare Azure SDK per Go](https://docs.microsoft.com/go/azure/azure-sdk-go-install).
- Azure passare SDK è disponibile pubblicamente in GitHub all'indirizzo [azure sdk per go](https://github.com/Azure/azure-sdk-for-go).

### <a name="go-autorest-dependencies"></a>Go-AutoRest dipendenze

il SDK GO dipende i moduli di Azure andare-AutoRest per inviare richieste REST agli endpoint di gestione risorse di Azure. È necessario importare le dipendenze del modulo Azure Go-AutoRest dal [Azure Go-AutoRest su GitHub](https://github.com/Azure/go-autorest). È possibile trovare i comandi bash installa il **installare** sezione.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Come usare i profili di SDK passare nello Stack di Azure

Per eseguire un esempio di codice Go nello Stack di Azure:
  1. Installare Azure SDK per Go e le relative dipendenze. Per istruzioni, vedere la sezione precedente [installare Azure SDK per Go](#install-azure-sdk-for-go).
  2. Ottenere le informazioni sui metadati dall'endpoint di gestione risorse. L'endpoint restituisce un file JSON con le informazioni necessarie per eseguire il codice di Go.

  > [!Note]  
  > Il **ResourceManagerUrl** in Azure Stack Development Kit (ASDK) è: `https://management.local.azurestack.external/`  
  > Il **ResourceManagerUrl** in sistemi integrati: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
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

  3. Se non è disponibile, creare una sottoscrizione e salvare l'ID sottoscrizione da utilizzare in un secondo momento. Per informazioni sulla creazione di una sottoscrizione, vedere [creare le sottoscrizioni di offerte nello Stack di Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
  4. Creare un'entità servizio con ambito "Subscription" e **proprietario** ruolo. ID e segreto, salvare le entità servizio. Per informazioni sulla creazione di un'entità servizio per lo Stack di Azure, vedere [creare l'entità servizio](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-azure-ad). L'ambiente dello Stack di Azure è configurato.
  5. Importare un modulo del servizio dal profilo SDK andare nel codice. La versione corrente del profilo Azure Stack **2017-03-09**. Ad esempio, per importare il modulo di rete dalla **2017-03-09** tipo di profilo: 

  ````go
    package main 
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
  ````
  
  6. Nella funzione, creare ed eseguire l'autenticazione di un client con un **New** chiamata di funzione di Client. Per creare un client di rete virtuale, è possibile utilizzare il codice seguente:  

  ````go
  package main 

  import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 

  func main() { 
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
  ````

  Impostare `<baseURI>` per il **ResourceManagerUrl** valore utilizzato nel passaggio 2.
  Impostare `<subscriptionID>` per il **SubscriptionID** valore salvato al passaggio 3.
  Per creare token, vedere autenticazione sezione riportata di seguito.  

  7. Richiamare i metodi API con il client che è stato creato nel passaggio precedente. Ad esempio, per creare una rete virtuale tramite il client dal passaggio precedente: 
  
````go
package main

import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 
func main() { 
  vnetC1ient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
  vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

  vnetClient .CreateOrUpdate( ) 
````
  
  Per un esempio completo di creazione di una rete virtuale nello Stack di Azure utilizzando il profilo SDK Go, vedere [esempio](#example).

## <a name="authentication"></a>Authentication

Per ottenere la proprietà Authorizer da Azure Active Directory con Go SDK, installare i moduli AutoRest Go. Questi moduli devono essere già installati con l'installazione di "Vai SDK"; in caso contrario, installare il [pacchetto di autenticazione su GitHub](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

Provider di autorizzazioni deve essere impostato come provider di autorizzazioni per il client di risorsa. Esistono diversi metodi per ottenere un provider di autorizzazioni; per un elenco completo, vedere di seguito.

In questa sezione viene presentato un modo comune per ottenere i token authorizer nello Stack di Azure utilizzando le credenziali del client:

  1. Se un'entità servizio con il ruolo di proprietario della sottoscrizione è disponibile, ignorare questo passaggio. In caso contrario, creare un'entità servizio [istruzioni]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) e assegnare un ruolo di "proprietario" con ambito limitato alla sottoscrizione [istruzioni]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal). Salvare l'ID applicazione dell'entità servizio e il segreto. 

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
   
  Impostare `<activeDirectoryEndpoint>` al valore di "loginEndpoint" proprietà dai metadati ResourceManagerUrl recuperato nella sezione precedente di questo documento.
  Impostare `<tenantID>` valore per l'ID del tenant Azure Stack. 

  4. Infine, creare un token dell'entità servizio utilizzando il metodo NewServicePrincipalToken dal modulo adal. 

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
  
  Impostare `<activeDirectoryResourceID>` su uno dei valori in "audience" elenco dai metadati ResourceManagerUrl recuperato nella sezione precedente di questo documento.  
  Impostare `<clientID>` all'applicazione dell'entità servizio ID salvati quando un'entità servizio è stata creata nella sezione precedente di questo documento.  
  Impostare `<clientSecret>` all'applicazione dell'entità servizio segreto salvati quando un'entità servizio è stata creata nella sezione precedente di questo documento.  

## <a name="example"></a>Esempio

In questa sezione viene illustrato un esempio di codice Go per creare una rete virtuale nello Stack di Azure. Per esempi completi di andare SDK, vedere [repository di esempi di Azure andare SDk](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Esempi di Stack di Azure sono disponibili in ibrida / percorso all'interno di cartelle del servizio del repository.

> [!Note]  
> Per eseguire il codice in questo esempio, verificare che la sottoscrizione utilizzata disponga **rete** elencato come provider di risorse **registrati**. Per verificarlo, cercare la sottoscrizione nel portale di Azure Stack e fare clic su **i provider di risorse.**

1. Importare pacchetti richiesti nel codice. Utilizzare il profilo più recente nello Stack di Azure per importare il modulo di rete. 
  
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

3. Dopo avere definito le variabili di ambiente, aggiungere un metodo per creare token di autenticazione tramite **adal** pacchetto. Vedere i dettagli sui tipi di autenticazione nella sezione precedente.
  
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

4. Aggiungere il metodo principale. Il metodo main innanzitutto di ottenere un token tramite il metodo definito nel passaggio precedente. Quindi, viene creato un client tramite il modulo di rete dal profilo. Infine, crea una rete virtuale. 
  
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
