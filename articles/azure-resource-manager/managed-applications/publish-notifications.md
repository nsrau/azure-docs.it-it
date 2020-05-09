---
title: App gestite con notifiche
description: Configurare le applicazioni gestite con gli endpoint del webhook per ricevere notifiche relative a creazione, aggiornamenti, eliminazioni ed errori nelle istanze di applicazioni gestite.
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: ff058d7b51bd2e5efd80db69e5928d58fc5a7725
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76715679"
---
# <a name="azure-managed-applications-with-notifications"></a>Applicazioni gestite di Azure con notifiche

Le notifiche delle applicazioni gestite di Azure consentono agli editori di automatizzare le azioni in base agli eventi del ciclo di vita delle istanze di applicazioni gestite. Gli editori possono specificare endpoint del webhook di notifica personalizzati per ricevere notifiche degli eventi sulle istanze di applicazioni gestite nuove ed esistenti. Gli editori possono impostare flussi di lavoro personalizzati al momento del provisioning, degli aggiornamenti e dell'eliminazione dell'applicazione.

## <a name="getting-started"></a>Introduzione
Per iniziare a ricevere le applicazioni gestite, creare un endpoint HTTPS pubblico e specificarlo quando si pubblica la definizione dell'applicazione del catalogo di servizi o l'offerta di Azure Marketplace.

Ecco i passaggi consigliati per iniziare rapidamente:
1. Attivare un endpoint HTTPS pubblico che registra le richieste POST in ingresso e restituisce `200 OK`il risultato.
2. Aggiungere l'endpoint alla definizione dell'applicazione del catalogo di servizi o all'offerta di Azure Marketplace, come illustrato più avanti in questo articolo.
3. Creare un'istanza dell'applicazione gestita che faccia riferimento alla definizione dell'applicazione o all'offerta di Azure Marketplace.
4. Verificare che le notifiche vengano ricevute.
5. Abilitare l'autorizzazione come illustrato nella sezione **autenticazione endpoint** di questo articolo.
6. Seguire le istruzioni nella sezione **schema di notifica** di questo articolo per analizzare le richieste di notifica e implementare la logica di business in base alla notifica.

## <a name="add-service-catalog-application-definition-notifications"></a>Aggiungere notifiche per la definizione dell'applicazione del catalogo di servizi
#### <a name="azure-portal"></a>Portale di Azure
Per iniziare, vedere [pubblicare un'applicazione del catalogo di servizi tramite portale di Azure](./publish-portal.md).

![Notifiche di definizione dell'applicazione del catalogo di servizi nel portale di Azure](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>API REST

> [!NOTE]
> Attualmente, è possibile specificare un solo endpoint nell'oggetto `notificationEndpoints` nelle proprietà della definizione dell'applicazione.

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
## <a name="add-azure-marketplace-managed-application-notifications"></a>Aggiungere le notifiche delle applicazioni gestite di Azure Marketplace
Per altre informazioni, vedere [creare un'offerta di applicazione Azure](../../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md).

![Notifiche delle applicazioni gestite di Azure Marketplace nel portale di Azure](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Trigger di evento
Nella tabella seguente vengono descritte tutte le possibili combinazioni di EventType e ProvisioningState e dei relativi trigger:

EventType | ProvisioningState | Trigger per la notifica
---|---|---
PUT | Accepted | Il gruppo di risorse gestite è stato creato e proiettato correttamente dopo l'inserimento dell'applicazione (prima che la distribuzione all'interno del gruppo di risorse gestite venga avviata).
PUT | Operazione riuscita | Il provisioning completo dell'applicazione gestita è riuscito dopo un'operazione PUT.
PUT | Operazione non riuscita | Errore di inserimento del provisioning dell'istanza dell'applicazione in qualsiasi momento.
PATCH | Operazione riuscita | Dopo una PATCH corretta nell'istanza dell'applicazione gestita per aggiornare i tag, i criteri di accesso JIT o l'identità gestita.
DELETE | Deleting | Non appena l'utente avvia un'eliminazione di un'istanza dell'app gestita.
DELETE | Deleted | Dopo l'eliminazione completa e corretta dell'applicazione gestita.
DELETE | Operazione non riuscita | Dopo qualsiasi errore durante il processo di deprovisioning che blocca l'eliminazione.
## <a name="notification-schema"></a>Schema di notifica
Quando si avvia l'endpoint del webhook per gestire le notifiche, è necessario analizzare il payload per ottenere proprietà importanti e quindi agire sulla notifica. Il catalogo di servizi e le notifiche delle applicazioni gestite di Azure Marketplace forniscono molte delle stesse proprietà. Nella tabella riportata di seguito vengono descritte due differenze minime.

#### <a name="service-catalog-application-notification-schema"></a>Schema di notifica dell'applicazione del catalogo di servizi
Di seguito è riportato un esempio di notifica del catalogo del servizio dopo il completamento del provisioning di un'istanza dell'applicazione gestita:
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

Se il provisioning ha esito negativo, viene inviata una notifica con i dettagli dell'errore all'endpoint specificato.

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

#### <a name="azure-marketplace-application-notification-schema"></a>Schema di notifica dell'applicazione Azure Marketplace

Di seguito è riportato un esempio di notifica del catalogo del servizio dopo il completamento del provisioning di un'istanza dell'applicazione gestita:
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

Se il provisioning ha esito negativo, viene inviata una notifica con i dettagli dell'errore all'endpoint specificato.

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
eventType | Tipo di evento che ha attivato la notifica. (Ad esempio, PUT, PATCH, DELETE).
applicationId | Identificatore di risorsa completo dell'applicazione gestita per la quale è stata attivata la notifica.
eventTime | Timestamp dell'evento che ha attivato la notifica. (Data e ora in formato UTC ISO 8601).
provisioningState | Stato di provisioning dell'istanza dell'applicazione gestita. (Ad esempio, Succeeded, failed, Deleting, Deleted.)
error | *Specificata solo se ProvisioningState non è riuscito*. Contiene il codice di errore, il messaggio e i dettagli del problema che ha causato l'errore.
applicationDefinitionId | *Specificata solo per le applicazioni gestite del catalogo di servizi*. Rappresenta l'identificatore di risorsa completo della definizione di applicazione per cui è stato effettuato il provisioning dell'istanza dell'applicazione gestita.
piano | *Specificata solo per le applicazioni gestite di Azure Marketplace*. Rappresenta il server di pubblicazione, l'offerta, lo SKU e la versione dell'istanza dell'applicazione gestita.
billingDetails | *Specificata solo per le applicazioni gestite di Azure Marketplace.* Dettagli di fatturazione dell'istanza dell'applicazione gestita. Contiene la resourceUsageId che è possibile usare per eseguire query su Azure Marketplace per i dettagli di utilizzo.

## <a name="endpoint-authentication"></a>Autenticazione dell'endpoint
Per proteggere l'endpoint del webhook e garantire l'autenticità della notifica:
1. Specificare un parametro di query all'inizio dell'URI del webhook, come indicato di\:seguito: https//your-endpoint.com? sig = GUID. Con ogni notifica, verificare che il parametro `sig` di query includa il `Guid`valore previsto.
2. Eseguire un'richiesta GET sull'istanza dell'applicazione gestita usando applicationId. Verificare che provisioningState corrisponda al provisioningState della notifica per garantire la coerenza.

## <a name="notification-retries"></a>Tentativi di notifica

Il servizio di notifica dell'applicazione gestita prevede `200 OK` una risposta dall'endpoint del webhook alla notifica. Il servizio di notifica tenterà di nuovo se l'endpoint del webhook restituisce un codice di errore HTTP maggiore o uguale a 500, se restituisce un codice di errore 429 o se l'endpoint è temporaneamente irraggiungibile. Se l'endpoint del webhook non diventa disponibile entro 10 ore, il messaggio di notifica verrà eliminato e i tentativi verranno interrotti.
