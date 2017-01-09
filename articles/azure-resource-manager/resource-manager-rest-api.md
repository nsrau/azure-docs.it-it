---
title: API REST di Resource Manager | Microsoft Docs
description: Panoramica dell&quot;autenticazione ed esempi di utilizzo delle API REST di Resource Manager
services: azure-resource-manager
documentationcenter: na
author: navalev
manager: timlt
editor: 
ms.assetid: e8d7a1d2-1e82-4212-8288-8697341408c5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2016
ms.author: navale;tomfitz;
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: b3faa84d83f7e2dda9a704473657a1607b402c8a


---
# <a name="resource-manager-rest-apis"></a>API REST di Resource Manager
> [!div class="op_single_selector"]
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [Interfaccia della riga di comando di Azure](xplat-cli-azure-resource-manager.md)
> * [Portale](resource-group-portal.md) 
> * [API REST](resource-manager-rest-api.md)
> 
> 

Ogni chiamata ad Azure Resource Manager, ogni modello sviluppato e ogni account di archiviazione configurato sono basati su una o più chiamate all'API REST di Azure Resource Manager. Questo argomento è dedicato a tali API e spiega come chiamarle senza usare alcun SDK. Questa opzione è molto utile se si vuole il controllo completo di tutte le richieste ad Azure oppure se l'SDK per il linguaggio preferito non è disponibile o non supporta le operazioni che si vuole eseguire.

Questo articolo non esamina ogni API esposta in Azure, ma ne usa alcune come esempio per illustrare come procedere e connettersi a esse. Una volta acquisite le nozioni di base, è possibile continuare leggendo [Azure Resource Manager REST API Reference (Informazioni di riferimento sull'API REST di Azure Resource Manager)](https://docs.microsoft.com/rest/api/resources/) che contiene informazioni dettagliate su come usare le altre API.

## <a name="authentication"></a>Autenticazione
L'autenticazione per ARM viene gestita da Azure Active Directory (AD). Per connettersi a un'API, prima di tutto è necessario eseguire l'autenticazione con Azure AD per ricevere un token di autenticazione che è possibile passare a ogni richiesta. Poiché verrà descritta una chiamata di base direttamente alle API REST, si presupporrà anche che non si voglia eseguire la normale autenticazione in cui una schermata popup richiede nome utente e password e magari anche altri meccanismi di autenticazione usati negli scenari di autenticazione a due fattori. Verranno quindi create una cosiddetta applicazione Azure AD e un'entità servizio che verrà usata per l'accesso. Si ricordi tuttavia che Azure AD supporta diverse procedure di autenticazione e che tutte possono essere usate per recuperare il token di autenticazione necessario per le richieste API successive.
Per istruzioni dettagliate, vedere [Create Azure AD Application and Service Principle](resource-group-create-service-principal-portal.md) (Creare un'applicazione e un'entità servizio di Azure AD).

### <a name="generating-an-access-token"></a>Generazione di un token di accesso
L'autenticazione in Azure AD viene eseguita chiamando Azure AD, all'indirizzo login.microsoftonline.com. Per eseguire l'autenticazione, sono necessarie le seguenti informazioni:

* ID tenant di Azure AD (nome della directory Azure AD usata per l'accesso, spesso, ma non necessariamente, lo stesso della società)
* ID applicazione (ottenuto durante il passaggio di creazione dell'applicazione Azure AD)
* Password (selezionata durante la creazione dell'applicazione Azure AD)

Nella richiesta HTTP seguente verificare la sostituzione di "Azure AD Tenant ID", "Application ID" e "Password" con i valori corretti.

**Richiesta HTTP generica:**

```HTTP
POST /<Azure AD Tenant ID>/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

Se l'autenticazione riesce, verrà restituita una risposta simile a questa:

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
access_token nella risposta precedente è stato abbreviato per renderlo più leggibile.

**Generazione del token di accesso con Bash:**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**Generazione del token di accesso con Powershell:**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

La risposta contiene un token di accesso, informazioni sul periodo di validità del token e informazioni sulla risorsa per cui è possibile usare il token.
Il token di accesso ricevuto nella chiamata HTTP precedente deve essere passato per tutte le richieste all'API ARM come intestazione denominata "Authorization" con il valore "Bearer YOUR_ACCESS_TOKEN". Si noti lo spazio tra "Bearer" e il token di accesso.

Come si può osservare dal risultato HTTP precedente, il token è valido per un periodo di tempo specifico durante il quale è consigliabile memorizzarlo nella cache per poterlo usare di nuovo. Anche se è possibile eseguire l'autenticazione in Azure AD per ogni chiamata API, non sarebbe per nulla efficiente.

## <a name="calling-arm-rest-apis"></a>Chiamata di API REST ARM
[La documentazione sulle API REST di Azure Resource Manager è disponibile qui](https://docs.microsoft.com/rest/api/resources/). L'uso di ogni singola API esula dall'ambito di questa esercitazione. Questa documentazione userà solo alcune API per illustrare l'utilizzo di base delle API. In seguito sarà possibile consultare la documentazione ufficiale.

### <a name="list-all-subscriptions"></a>Elenco di tutte le sottoscrizioni
Una delle operazioni più semplici da eseguire consiste nell'elencare le sottoscrizioni disponibili a cui è possibile accedere. Nella richiesta seguente è possibile osservare che il token di accesso viene passato come intestazione.

Sostituire YOUR_ACCESS_TOKEN con il token di accesso vero e proprio.

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

Il risultato sarà un elenco di sottoscrizioni a cui questa entità servizio può accedere.

Gli ID sottoscrizione seguenti sono stati abbreviati per renderli più leggibili.

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### <a name="list-all-resource-groups-in-a-specific-subscription"></a>Elencare tutti i gruppi di risorse in una sottoscrizione specifica
Tutte le risorse disponibili con le API ARM vengono annidate in un gruppo di risorse. Verrà eseguita una query di Azure Resource Manager per trovare i gruppi di risorse esistenti nella sottoscrizione usando la richiesta GET HTTP seguente. Si noti che questa volta l'ID sottoscrizione viene passato come parte dell'URL.

Sostituire YOUR_ACCESS_TOKEN e SUBSCRIPTION_ID con il token di accesso e l'ID sottoscrizione effettivi.

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

La risposta ottenuta dipenderà dalla presenza o meno di gruppi di risorse definiti e, se presenti, dal numero dei gruppi.

Gli ID sottoscrizione seguenti sono stati abbreviati per renderli più leggibili.

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Finora è stata solo eseguita una query delle API ARM per trovare informazioni. Ora verranno invece create alcune risorse, iniziando con la più semplice, il gruppo di risorse. La richiesta HTTP seguente crea un nuovo gruppo di risorse in un'area/posizione scelta e aggiunge uno o più tag al gruppo. L'esempio seguente in realtà aggiunge un solo tag.

Sostituire YOUR_ACCESS_TOKEN, SUBSCRIPTION_ID, RESOURCE_GROUP_NAME con il token di accesso, l'ID sottoscrizione e il nome del gruppo di risorse effettivi che si vuole creare.

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

Se l'operazione riesce, si otterrà una risposta simile a questa:

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

È stato creato un gruppo di risorse in Azure. Congratulazioni.

### <a name="deploy-resources-to-a-resource-group-using-an-arm-template"></a>Distribuire risorse in un gruppo di risorse usando un modello ARM
Con ARM, è possibile distribuire le risorse usando i modelli ARM. Un modello ARM definisce diverse risorse e le relative dipendenze. In questa sezione si presuppone che l'utente abbia familiarità con i modelli ARM e verrà illustrato come fare in modo che la chiamata API inizi la distribuzione di un modello. La documentazione dettagliata dei modelli ARM è disponibile qui.

La distribuzione di un modello ARM non è molto diversa dalla chiamata alle altre API. Un aspetto importante è che la distribuzione di un modello può richiedere molto tempo, a seconda del contenuto del modello, e che verrà restituita solo la chiamata API. Sarà lo sviluppatore a decidere se eseguire una query per conoscere lo stato della distribuzione e sapere quando la distribuzione è terminata.

Per questo esempio, verrà usato un modello Azure Resource Manager esposto pubblicamente disponibile su [GitHub](https://github.com/Azure/azure-quickstart-templates). Il modello che verrà usato distribuirà una VM Linux nell'area Stati Uniti occidentali. Anche se questo modello sarà disponibile in un repository pubblico come GitHub, è anche possibile scegliere di passare il modello completo come parte della richiesta. Si noti che nell'ambito della richiesta vengono forniti i valori dei parametri che verranno usati nel modello scelto.

Sostituire SUBSCRIPTION_ID, RESOURCE_GROUP_NAME, DEPLOYMENT_NAME, YOUR_ACCESS_TOKEN, GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME, ADMIN_USER_NAME,ADMIN_PASSWORD e DNS_NAME_FOR_PUBLIC_IP con i valori appropriati per la richiesta.

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

La lunga risposta JSON per questa richiesta è stata omessa per migliorare la leggibilità della documentazione. La risposta conterrà informazioni sulla distribuzione basata su modelli appena creata.




<!--HONumber=Nov16_HO3-->


