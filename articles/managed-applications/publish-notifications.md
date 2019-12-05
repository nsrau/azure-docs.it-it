---
title: Applicazioni gestite di Azure con notifiche
description: Configurare un'applicazione gestita con endpoint webhook per ricevere notifiche relative a creazione, aggiornamenti, eliminazioni ed errori nelle istanze di applicazioni gestite.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: 8cf9fc0b3d9c13ebc5309be6d27c7be0f2e60878
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805689"
---
# <a name="azure-managed-applications-with-notifications"></a>Applicazioni gestite di Azure con notifiche

Le notifiche delle applicazioni gestite di Azure consentono agli editori di automatizzare le azioni in base agli eventi del ciclo di vita delle istanze di applicazioni gestite. Gli editori possono specificare endpoint del webhook di notifica personalizzati per ricevere notifiche degli eventi sulle istanze di applicazioni gestite nuove ed esistenti. Consente al server di pubblicazione di configurare flussi di lavoro personalizzati al momento del provisioning, degli aggiornamenti e dell'eliminazione dell'applicazione.

## <a name="getting-started"></a>Inizia ora
Per iniziare a ricevere le applicazioni gestite, creare un endpoint HTTPS pubblico e specificarlo quando si pubblica la definizione dell'applicazione del catalogo di servizi o l'offerta del Marketplace.

Ecco la serie consigliata di passaggi per iniziare subito a usare:
1. Attivare un endpoint HTTPS pubblico che registra le richieste POST in ingresso e restituisce `200 OK`.
2. Aggiungere l'endpoint alla definizione dell'applicazione del catalogo di servizi o all'offerta del Marketplace come illustrato di seguito.
3. Creare un'istanza dell'applicazione gestita che faccia riferimento alla definizione dell'applicazione o all'offerta del Marketplace.
4. Verificare che le notifiche vengano ricevute correttamente.
5. Abilitare l'autorizzazione come illustrato nella sezione **autenticazione dell'endpoint** riportata di seguito.
6. Seguire la documentazione **dello schema di notifica** riportata di seguito per analizzare le richieste di notifica e implementare la logica di business in base alla notifica.

## <a name="adding-service-catalog-application-definition-notifications"></a>Aggiunta di notifiche di definizione dell'applicazione del catalogo di servizi
#### <a name="azure-portal"></a>Portale di Azure
Per iniziare, leggere [pubblicare un'applicazione del catalogo di servizi tramite portale di Azure](./publish-portal.md) .

![Notifiche di definizione dell'applicazione del catalogo di servizi nel portale](./media/publish-notifications/service-catalog-notifications.png)
#### <a name="rest-api"></a>API REST

> [!NOTE]
> Attualmente è supportato un solo endpoint come parte di **notificationEndpoints** nelle proprietà della definizione dell'applicazione

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
## <a name="adding-marketplace-managed-application-notifications"></a>Aggiunta di notifiche di applicazioni gestite del Marketplace
Per altre informazioni, vedere [creare un'offerta di applicazione Azure](../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md).

![Notifiche di definizione dell'applicazione del catalogo di servizi nel portale](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Trigger di evento
Nella tabella seguente vengono descritte tutte le possibili combinazioni di EventType + ProvisioningState e dei relativi trigger:

EventType | ProvisioningState | Trigger per la notifica
---|---|---
PUT | Accepted | Il gruppo di risorse gestite è stato creato e proiettato correttamente dopo l'inserimento dell'applicazione. (Prima dell'avvio della distribuzione all'interno del RG gestito).
PUT | Succeeded | Il provisioning completo dell'applicazione gestita è riuscito dopo un'operazione PUT.
PUT | Failed | Errore di inserimento del provisioning dell'istanza dell'applicazione in qualsiasi momento.
PATCH | Succeeded | Dopo la corretta correzione dell'istanza dell'applicazione gestita per aggiornare i tag, i criteri di accesso JIT o l'identità gestita.
DELETE | Eliminazione | Non appena l'utente avvia un'eliminazione di un'istanza dell'app gestita.
DELETE | Eliminato | Dopo l'eliminazione completa e corretta dell'applicazione gestita.
DELETE | Failed | Dopo qualsiasi errore durante il processo di deprovisioning che blocca l'eliminazione.
## <a name="notification-schema"></a>Schema di notifica
Quando si avvia l'endpoint del webhook per gestire le notifiche, è necessario analizzare il payload per ottenere proprietà importanti e quindi agire sulla notifica. Il catalogo di servizi e le notifiche delle applicazioni gestite da Marketplace forniscono molte delle stesse proprietà con la piccola differenza descritta di seguito.

#### <a name="service-catalog-application-notification-schema"></a>Schema di notifica dell'applicazione del catalogo di servizi
Di seguito è riportato un esempio di notifica del catalogo di servizi dopo un provisioning riuscito di un'istanza dell'applicazione gestita.
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

#### <a name="marketplace-application-notification-schema"></a>Schema di notifica dell'applicazione Marketplace

Di seguito è riportato un esempio di notifica del catalogo di servizi dopo un provisioning riuscito di un'istanza dell'applicazione gestita.
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

Parametro | Description
---|---
eventType | Tipo di evento che ha attivato la notifica. (ad esempio, "PUT", "PATCH", "DELETE")
applicationId | Identificatore di risorsa completo dell'applicazione gestita per la quale è stata attivata la notifica. 
eventTime | Timestamp dell'evento che ha attivato la notifica. (Data e ora in formato UTC ISO 8601).
provisioningState | Stato di provisioning dell'istanza dell'applicazione gestita. (ad esempio, "succeeded", "failed", "Deleting", "Deleted")
billingDetails | Dettagli di fatturazione dell'istanza dell'applicazione gestita. Contiene il resourceUsageId che può essere usato per eseguire query sul Marketplace per i dettagli di utilizzo.
error | *Specificato solo quando ProvisioningState non è riuscito*. Contiene il codice di errore, il messaggio e i dettagli del problema che ha causato l'errore.
applicationDefinitionId | *Specificato solo per le applicazioni gestite del catalogo di servizi*. Rappresenta l'identificatore di risorsa completo della definizione di applicazione per cui è stato effettuato il provisioning dell'istanza dell'applicazione gestita.
piano | *Specificato solo per le applicazioni gestite da Marketplace*. Rappresenta il server di pubblicazione, l'offerta, lo SKU e la versione dell'istanza dell'applicazione gestita.

## <a name="endpoint-authentication"></a>Autenticazione dell'endpoint
Per proteggere l'endpoint del webhook e garantire l'autenticità della notifica:
- Specificare un parametro di query all'inizio dell'URI del webhook, ad esempio https://your-endpoint.com?sig=Guid. Con ogni notifica, verificare che il parametro di query `sig` abbia il valore previsto `Guid`.
- Eseguire un'richiesta GET sull'istanza dell'applicazione gestita con applicationId. Verificare che provisioningState corrisponda al provisioningState della notifica per garantire la coerenza.

## <a name="notification-retries"></a>Tentativi di notifica

Il servizio di notifica dell'applicazione gestita prevede una risposta `200 OK` dall'endpoint del webhook alla notifica. Il servizio di notifica tenterà di nuovo se l'endpoint del webhook restituisce un codice di errore HTTP > = 500, 429 o se l'endpoint è temporaneamente irraggiungibile. Se l'endpoint del webhook non diventa disponibile entro 10 ore, il messaggio di notifica verrà eliminato e i tentativi verranno interrotti.

