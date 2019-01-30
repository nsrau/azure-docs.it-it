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
ms.date: 01/19/2019
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 3c5a261eb5216c2dca93243d11b5d21b1d92846f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224157"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Usare i profili delle versioni API con Go in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

## <a name="go-and-version-profiles"></a>Profili di go e versione

Un profilo è una combinazione di diversi tipi di risorse con versioni diverse da servizi diversi. Usa un profilo consente di combinare e abbinare tra diversi tipi di risorse. I profili possono offrire i vantaggi seguenti:

- Stabilità dell'applicazione da blocchi a specifiche versioni di API.
- Compatibilità dell'applicazione con Azure Stack e a livello di area Data Center di Azure.

In Go SDK, i profili sono disponibili sotto il percorso di profili, con la rispettiva versione nel **AAAA-MM-GG** formato. Al momento, l'ultima versione del profilo di API di Azure Stack **2017-03-09**. Per importare un determinato servizio da un profilo, importare il modulo corrispondente dal profilo. Ad esempio, per importare **Compute** servizio **2017-03-09** profilo, usare il codice seguente:

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/compute/mgmt/compute"
```

## <a name="install-azure-sdk-for-go"></a>Installare Azure SDK per Go

1. Installare Git. Per istruzioni, vedere [Guida introduttiva - installazione di Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Installare il [linguaggio di programmazione Go](https://golang.org/dl). I profili delle API per Azure richiedono Go versione 1.9 o versione successiva.
3. Installare il SDK di Azure passare e le relative dipendenze eseguendo il comando bash seguente:

   ```bash
   go get -u -d github.com/Azure/azure-sdk-for-go/...
   ```

### <a name="the-go-sdk"></a>Go SDK

È possibile trovare altre informazioni su Azure GO SDK visitando i collegamenti seguenti:

- Azure SDK in corrispondenza di andare [installazione di Azure SDK per Go](/go/azure/azure-sdk-go-install).
- il SDK di Azure passare è pubblicamente disponibile su GitHub nel [azure sdk per go](https://github.com/Azure/azure-sdk-for-go) repository.

### <a name="go-autorest-dependencies"></a>Dipendenze di go-AutoRest

Go SDK varia a seconda di Azure **AutoRest-Go** moduli per inviare richieste REST agli endpoint di Azure Resource Manager. È necessario importare di Azure **AutoRest-Go** le dipendenze del modulo dal [Go su GitHub Azure-AutoRest](https://github.com/Azure/go-autorest). È possibile trovare i comandi bash installa il **installare** sezione.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Come usare i profili di Go SDK in Azure Stack

Per eseguire un esempio di codice di Go in Azure Stack, seguire questa procedura:

1. Installare Azure SDK per Go e le relative dipendenze. Per istruzioni, vedere la sezione precedente [installare Azure SDK per Go](#install-azure-sdk-for-go).
2. Ottenere le informazioni sui metadati dall'endpoint di Resource Manager. L'endpoint restituisce un file JSON con le informazioni necessarie per eseguire il codice Go.

   > [!NOTE]  
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

3. Se non è disponibile, creare una sottoscrizione e salvare l'ID sottoscrizione da usare in un secondo momento. Per informazioni sulla creazione di una sottoscrizione, vedere [creare le sottoscrizioni di offerte in Azure Stack](../azure-stack-subscribe-plan-provision-vm.md).

4. Creare un'entità con un servizio **abbonamento** ambito e **proprietario** ruolo. Salvare l'ID dell'entità servizio e il segreto. Per informazioni sulla creazione di un'entità servizio per Azure Stack, vedere [creare un'entità servizio](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad). L'ambiente Azure Stack è ora impostata.

5. Importare un modulo del servizio dal profilo Go SDK nel codice. La versione corrente del profilo Azure Stack **2017-03-09**. Ad esempio, per importare il modulo di rete dal **2017-03-09** tipo di profilo, usare il codice seguente:

   ```go
   package main
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
   ```

6. Nella funzione, creare e autenticare un client con un **New** chiamata di funzione di client. Per creare un client di rete virtuale, è possibile usare il codice seguente:  

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"

   func main() {
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
   ```

   Impostare `<baseURI>` per il **ResourceManagerUrl** valore usato nel passaggio 2. Impostare `<subscriptionID>` per il **SubscriptionID** valore salvato dal passaggio 3.

   Per creare il token, vedere la sezione seguente.  

7. Richiamare metodi dell'API usando il client che è stato creato nel passaggio precedente. Ad esempio, per creare una rete virtuale usando il client nel passaggio precedente, vedere l'esempio seguente:

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
   func main() {
   vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
   vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

   vnetClient .CreateOrUpdate( )
   ```

Per un esempio completo di creazione di una rete virtuale in Azure Stack tramite il profilo di Go SDK, vedere la [esempio](#example).

## <a name="authentication"></a>Authentication

Per ottenere il **Authorizer** proprietà da Azure Active Directory usando il SDK di Go, installare il **Go AutoRest** moduli. Questi moduli devono essere già installati con l'installazione di "Go SDK"; in caso contrario, installare il [pacchetto di autenticazione da GitHub](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

Provider di autorizzazioni deve essere impostato come provider di autorizzazioni per il client di risorsa. Esistono diversi modi per ottenere i token di provider di autorizzazioni in Azure Stack usando le credenziali del client:

1. Se un'entità servizio con ruolo di proprietario della sottoscrizione è disponibile, ignorare questo passaggio. In caso contrario, creare un [entità servizio](azure-stack-create-service-principals.md) e assegnare un ruolo "proprietario" [ha come ambito la sottoscrizione](azure-stack-create-service-principals.md#assign-the-service-principal-to-a-role). Salvare l'ID applicazione dell'entità servizio e il segreto.

2. Importa i **adal** pacchetto da Go AutoRest nel codice.

   ```go
   package main
   import "github.com/Azure/go-autorest/autorest/adal"
   ```

3. Creare un **oauthConfig** tramite il metodo NewOAuthConfig dalla **adal** modulo.

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/ada1"

   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
   }
   ```

   Impostare `<activeDirectoryEndpoint>` al valore della `loginEndpoint` proprietà dal `ResourceManagerUrl` metadati recuperati nella sezione precedente di questo documento. Impostare il `<tenantID>` valore per l'ID tenant di Azure Stack.

4. Infine, creare un token dell'entità servizio usando il `NewServicePrincipalToken` metodo dal **adal** modulo:

   ```go
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
   ```

    Impostare `<activeDirectoryResourceID>` a uno dei valori nell'elenco "audience" dal **ResourceManagerUrl** metadati recuperati nella sezione precedente di questo articolo.
    Impostare `<clientID>` all'applicazione dell'entità servizio ID salvato quando l'entità servizio è stato creato nella sezione precedente di questo articolo.
    Impostare `<clientSecret>` al segreto applicazione di un'entità servizio salvato quando l'entità servizio è stato creato nella sezione precedente di questo articolo.

## <a name="example"></a>Esempio

Questo esempio viene illustrato un esempio di codice di Go che crea una rete virtuale in Azure Stack. Per esempi completi di Go SDK, vedere la [repository degli esempi di Azure Go SDK](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Esempi di Stack di Azure sono disponibili nel percorso ibrida all'interno di cartelle del servizio del repository.

> [!NOTE]  
> Per eseguire il codice in questo esempio, verificare che la sottoscrizione usata sia la **Network** elencato come provider di risorse **Registered**. Per verificare, cercare la sottoscrizione nel portale di Azure Stack e selezionare **i provider di risorse.**

1. Importare i pacchetti necessari nel codice. Usare il profilo più recenti disponibile in Azure Stack per importare il modulo di rete:

   ```go
   package main

   import (
       "context"
       "fmt"
       "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
       "github.com/Azure/go-autorest/autorest"
       "github.com/Azure/go-autorest/autorest/adal"
       "github.com/Azure/go-autorest/autorest/to"
   )
   ```

2. Definire le variabili di ambiente. Per creare una rete virtuale, è necessario disporre di un gruppo di risorse.

   ```go
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
   ```

3. Ora che sono stati definiti le variabili di ambiente, aggiungere un metodo per creare un token di autenticazione usando il **adal** pacchetto. Per altre informazioni sull'autenticazione, vedere la sezione precedente.

   ```go
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
   ```

4. Aggiungere il `main` (metodo). Il `main` metodo innanzitutto di ottenere un token tramite il metodo definito nel passaggio precedente. Quindi, crea un client tramite un modulo di rete dal profilo. Infine, consente di creare una rete virtuale.

   ```go
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
   ```

## <a name="next-steps"></a>Passaggi successivi

- [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md)
- [Configurare l'ambiente di PowerShell dell'utente Azure Stack](azure-stack-powershell-configure-user.md)  
