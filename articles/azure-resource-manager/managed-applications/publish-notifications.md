---
title: App gestite con notifiche
description: Configurare le applicazioni gestite con endpoint webhook per ricevere notifiche su creazione, aggiornamenti, eliminazioni ed errori nelle istanze dell'applicazione gestita.
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: ff058d7b51bd2e5efd80db69e5928d58fc5a7725
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76715679"
---
# <a name="azure-managed-applications-with-notifications"></a>Applicazioni gestite di Azure con notificheAzure managed applications with notifications

Le notifiche delle applicazioni gestite di Azure consentono agli editori di automatizzare le azioni in base agli eventi del ciclo di vita delle istanze dell'applicazione gestita. Gli editori possono specificare endpoint webhook di notifica personalizzati per ricevere notifiche di eventi sulle istanze dell'applicazione gestita nuove ed esistenti. I publisher possono impostare flussi di lavoro personalizzati al momento del provisioning, degli aggiornamenti e dell'eliminazione delle applicazioni.

## <a name="getting-started"></a>Introduzione
Per iniziare a ricevere applicazioni gestite, creare un endpoint HTTPS pubblico e specificarlo quando si pubblica la definizione dell'applicazione del catalogo dei servizi o l'offerta di Azure Marketplace.To start receiving managed applications, spin up a public HTTPS endpoint and specify it when you publish the service catalog application definition or Azure Marketplace offer.

Ecco i passaggi consigliati per iniziare rapidamente:
1. È stato eseguito l'up up di `200 OK`un endpoint HTTPS pubblico che registra le richieste POST in ingresso e restituisce .
2. Aggiungere l'endpoint alla definizione dell'applicazione del catalogo dei servizi o all'offerta di Azure Marketplace come illustrato più avanti in questo articolo.
3. Creare un'istanza dell'applicazione gestita che faccia riferimento alla definizione dell'applicazione o all'offerta di Azure Marketplace.Create a managed application instance that references the application definition or Azure Marketplace offer.
4. Verificare che le notifiche vengano ricevute.
5. Abilitare l'autorizzazione come illustrato nella sezione **Autenticazione endpoint** di questo articolo.
6. Seguire le istruzioni nella sezione **Schema di notifica** di questo articolo per analizzare le richieste di notifica e implementare la logica di business in base alla notifica.

## <a name="add-service-catalog-application-definition-notifications"></a>Aggiungere notifiche di definizione dell'applicazione del catalogo dei serviziAdd service catalog application definition notifications
#### <a name="azure-portal"></a>Portale di Azure
Per iniziare, vedere [Pubblicare un'applicazione del catalogo](./publish-portal.md)di servizi tramite il portale di Azure.To get started, see Publish a service catalog application through Azure portal.

![Notifiche di definizione dell'applicazione del catalogo dei servizi nel portale di AzureService catalog application definition notifications in the Azure portal](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>API REST

> [!NOTE]
> Attualmente, è possibile fornire un `notificationEndpoints` solo endpoint nelle proprietà di definizione dell'applicazione.

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="add-azure-marketplace-managed-application-notifications"></a>Aggiungere notifiche delle applicazioni gestite di Azure MarketplaceAdd Azure Marketplace managed application notifications
Per altre informazioni, vedere [Creare un'offerta](../../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md)di applicazione Azure.For more information, see Create an Azure application offer .

![Notifiche delle applicazioni gestite di Azure Marketplace nel portale di AzureAzure Marketplace managed application notifications in the Azure portal](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Trigger di evento
Nella tabella seguente vengono descritte tutte le possibili combinazioni di EventType e ProvisioningState e i relativi trigger:

EventType | ProvisioningState | Trigger per la notifica
---|---|---
PUT | Accepted | Il gruppo di risorse gestite è stato creato e proiettato correttamente dopo l'applicazione PUT (prima che la distribuzione all'interno del gruppo di risorse gestite venga avviata).
PUT | Operazione completata | Il provisioning completo dell'applicazione gestita è riuscito dopo un'operazione PUT.
PUT | Operazione non riuscita | Errore di PUT di provisioning dell'istanza dell'applicazione in qualsiasi momento.
PATCH | Operazione completata | Dopo una patch corretta nell'istanza dell'applicazione gestita per aggiornare i tag, i criteri di accesso JIT o l'identità gestita.
Elimina | Deleting | Non appena l'utente avvia un'eliminazione di un'istanza dell'app gestita.
Elimina | Deleted | Dopo l'eliminazione completa e corretta dell'applicazione gestita.
Elimina | Operazione non riuscita | Dopo qualsiasi errore durante il processo di deprovisioning che blocca l'eliminazione.
## <a name="notification-schema"></a>Schema di notifica
Quando si crea un giro dell'endpoint webhook per gestire le notifiche, è necessario analizzare il payload per ottenere proprietà importanti per agire sulla notifica. Il catalogo dei servizi e le notifiche delle applicazioni gestite di Azure Marketplace forniscono molte delle stesse proprietà. Due piccole differenze sono descritte nella tabella che segue i campioni.

#### <a name="service-catalog-application-notification-schema"></a>Schema di notifica dell'applicazione del catalogo dei serviziService catalog application notification schema
Ecco una notifica del catalogo dei servizi di esempio dopo il corretto provisioning di un'istanza dell'applicazione gestita:Here's a sample service catalog notification after the successful provisioning of a managed application instance:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

Se il provisioning non riesce, verrà inviata una notifica con i dettagli dell'errore all'endpoint specificato.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="azure-marketplace-application-notification-schema"></a>Schema di notifica delle applicazioni di Azure MarketplaceAzure Marketplace application notification schema

Ecco una notifica del catalogo dei servizi di esempio dopo il corretto provisioning di un'istanza dell'applicazione gestita:Here's a sample service catalog notification after the successful provisioning of a managed application instance:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

Se il provisioning non riesce, verrà inviata una notifica con i dettagli dell'errore all'endpoint specificato.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

Parametro | Descrizione
---|---
eventType | Tipo di evento che ha attivato la notifica. (Ad esempio, PUT, PATCH, DELETE.)
applicationId | Identificatore di risorsa completo dell'applicazione gestita per cui è stata attivata la notifica.
eventTime | Timestamp dell'evento che ha attivato la notifica. (Data e ora in formato UTC ISO 8601.)
provisioningState | Stato di provisioning dell'istanza dell'applicazione gestita. (Ad esempio, Riuscito, Non riuscito, Eliminazione, Eliminato.)
error | *Specificato solo quando provisioningState è Failed*. Contiene il codice di errore, il messaggio e i dettagli del problema che ha causato l'errore.
applicationDefinitionId (iddefinizione dell'applicazione) | *Specificato solo per le applicazioni gestite del catalogo dei servizi.* Rappresenta l'identificatore di risorsa completo della definizione di applicazione per la quale è stato eseguito il provisioning dell'istanza dell'applicazione gestita.
piano | *Specificato solo per le applicazioni gestite di Azure Marketplace.* Rappresenta l'editore, l'offerta, lo SKU e la versione dell'istanza dell'applicazione gestita.
billingDettagli | *Specificato solo per le applicazioni gestite di Azure Marketplace.* Dettagli di fatturazione dell'istanza dell'applicazione gestita. Contiene il resourceUsageId che è possibile usare per eseguire una query su Azure Marketplace per i dettagli sull'utilizzo.

## <a name="endpoint-authentication"></a>Autenticazione endpoint
Per proteggere l'endpoint webhook e garantire l'autenticità della notifica:
1. Specificare un parametro di query sopra l'URI webhook, in questo modo: https\://your-endpoint.com?sig/Guid. Per ogni notifica, verificare `sig` che il `Guid`parametro di query abbia il valore previsto.
2. Eseguire un GET nell'istanza dell'applicazione gestita utilizzando applicationId. Verificare che provisioningState corrisponda allo stato provisioning della notifica per garantire la coerenza.

## <a name="notification-retries"></a>Tentativi di notifica

Il servizio di notifica `200 OK` dell'applicazione gestita prevede una risposta dall'endpoint webhook alla notifica. Il servizio di notifica tenterà se l'endpoint webhook restituisce un codice di errore HTTP maggiore o uguale a 500, se restituisce un codice di errore 429 o se l'endpoint è temporaneamente irraggiungibile. Se l'endpoint webhook non diventa disponibile entro 10 ore, il messaggio di notifica verrà eliminato e i tentativi verranno interrotti.
