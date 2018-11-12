---
title: Come gestire set di argomenti di grandi dimensioni in Griglia di eventi di Azure e pubblicarvi eventi con Domini eventi
description: Viene illustrato come creare e gestire argomenti in Griglia di eventi di Azure e pubblicarvi eventi con Domini eventi.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: d6da1ee603c85556693b145ba17d1e0cd0dfabd7
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2018
ms.locfileid: "51034541"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Gestire argomenti e pubblicare eventi con Domini eventi

Questo articolo illustra come:

* Creare un dominio di Griglia di eventi
* Sottoscrivere argomenti
* List keys
* Pubblicare eventi in un dominio

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Creare un dominio eventi

È possibile creare un dominio eventi tramite l'estensione `eventgrid` per l'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Dopo aver creato un dominio, è possibile usarlo per gestire set di argomenti di grandi dimensioni.

```azurecli-interactive
# if you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name>
  -l <location>
```

In seguito alla creazione verrà restituito quanto segue:

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

Prendere nota del valore di `endpoint` e `id` perché verranno richiesti per gestire il dominio e pubblicare eventi.

## <a name="create-topics-and-subscriptions"></a>Creare argomenti e sottoscrizioni

Il servizio Griglia di eventi crea e gestisce automaticamente l'argomento corrispondente in un dominio in base alla chiamata per creare una sottoscrizione di eventi per un argomento del dominio. Non è previsto alcun passaggio apposito per creare un argomento in un dominio. Analogamente, quando viene eliminata l'ultima sottoscrizione di eventi per un argomento, viene eliminato anche l'argomento.

La procedura di sottoscrizione di un argomento in un dominio è identica a quella per sottoscrivere qualsiasi altra risorsa di Azure:

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/<my-topic>" \
  --endpoint https://contoso.azurewebsites.net/api/f1?code=code
```

L'ID di risorsa specificato è lo stesso ID restituito durante la creazione del dominio in precedenza. Per specificare l'argomento che si vuole sottoscrivere, aggiungere `/topics/<my-topic>` alla fine dell'ID risorsa.

Per creare una sottoscrizione di eventi con ambito di dominio che riceva tutti gli eventi nel dominio, assegnare al dominio l'argomento `resource-id` senza specificarne altri, ad esempio `/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>`.

Se è necessario un endpoint di test per sottoscrivere gli eventi, è sempre possibile distribuire un'[app Web preesistente](https://github.com/Azure-Samples/azure-event-grid-viewer) che visualizza gli eventi in ingresso. È possibile inviare gli eventi al sito Web di test all'indirizzo `https://<your-site-name>.azurewebsites.net/api/updates`.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

Le autorizzazioni impostate per un argomento vengono archiviate in Azure Active Directory e devono essere eliminate in modo esplicito. L'eliminazione di una sottoscrizione di eventi non implica la revoca di un accesso utente per creare le sottoscrizioni di eventi se l'utente ha accesso in scrittura per un argomento.

## <a name="manage-access-to-topics"></a>Gestire l'accesso agli argomenti

Per gestire l'accesso agli argomenti, si usa l'[assegnazione di ruolo](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli). Per l'assegnazione di ruolo si usa il controllo dell'accesso basato sul ruolo per limitare le operazioni sulle risorse di Azure agli utenti autorizzati in un determinato ambito.

In Griglia di eventi sono disponibili due ruoli predefiniti che è possibile usare per assegnare agli utenti l'accesso ai diversi argomenti in un dominio. Questi ruoli sono `EventGrid EventSubscription Contributor (Preview)`, che consente di creare ed eliminare le sottoscrizioni, e `EventGrid EventSubscription Reader (Preview)`, che consente solo di visualizzare l'elenco delle sottoscrizioni di eventi.

Il comando seguente consente all'utente `alice@contoso.com` solo di creare ed eliminare le sottoscrizioni per l'argomento `foo`:

```azurecli-interactive
az role assignment create --assignee alice@contoso.com --role "EventGrid EventSubscription Contributor (Preview)" --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/foo
```

Vedere [Sicurezza e autenticazione di Griglia di eventi di Azure](./security-authentication.md) per maggiori informazioni su:

* Controllo di accesso per la gestione
* Tipi di operazioni
* Creazione di definizioni di ruolo personalizzate

## <a name="publish-events-to-an-event-grid-domain"></a>Pubblicare eventi in un dominio di Griglia di eventi

La procedura di pubblicazione di eventi in un dominio è identica a quella di [pubblicazione in un argomento personalizzato](./post-to-custom-topic.md). L'unica differenza è che è necessario specificare l'argomento al quale si vuole recapitare l'evento. Con la matrice di eventi seguente l'evento con `"id": "1111"` viene inviato all'argomento `foo`, mentre l'evento con `"id": "2222"` viene inviato all'argomento `bar`:

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

Per ottenere le chiavi per usarle in un dominio:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

Usare quindi il metodo preferito per effettuare una richiesta HTTP POST per la pubblicazione di eventi nel dominio di Griglia di eventi.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui concetti generali relativi ai domini eventi e sulla loro utilità, vedere la [panoramica concettuale dei domini eventi](./event-domains.md).