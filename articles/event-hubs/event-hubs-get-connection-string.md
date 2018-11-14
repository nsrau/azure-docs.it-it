---
title: Ottenere la stringa di connessione ad Hub eventi di Azure | Microsoft Docs
description: Ottenere una stringa di connessione ad Hub eventi
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 10/15/2018
ms.author: shvija
ms.openlocfilehash: bfc82f2dc280c3528f38c9cb466473a76328e552
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285383"
---
# <a name="get-an-event-hubs-connection-string"></a>Ottenere una stringa di connessione ad Hub eventi

Per usare Hub eventi, è necessario creare uno spazio dei nomi di Hub eventi. Uno spazio dei nomi è un contenitore di ambito che può contenere più argomenti di Hub eventi/Kafka. Questo spazio dei nomi specifica un nome [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) univoco. Dopo avere creato uno spazio dei nomi, è possibile ottenere la stringa di connessione necessaria per comunicare con Hub eventi.

La stringa di connessione per Hub eventi di Azure contiene i componenti seguenti:

* FQDN = nome di dominio completo (FQDN) dello spazio dei nomi di Hub eventi creato (include il nome dello spazio dei nomi di Hub eventi seguito da servicebus.windows.net)
* SharedAccessKeyName = nome scelto per le chiavi SAS dell'applicazione
* SharedAccessKey = valore generato della chiave.

Il modello di stringa di connessione si presenta nel modo seguente
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Un esempio di stringa di connessione può avere l'aspetto seguente `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Questo articolo illustra in dettaglio vari modi per ottenere la stringa di connessione.

## <a name="get-connection-string-from-the-portal"></a>Ottenere la stringa di connessione dal portale

Dopo aver creato lo spazio dei nomi di Hub eventi, la sezione Panoramica del portale può presentare la stringa di connessione come illustrato di seguito:

![Stringa di connessione ad Hub eventi](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)

Quando si fa clic sul collegamento della stringa di connessione nella sezione Panoramica, viene visualizzata la scheda Criteri di firma di accesso condiviso come illustrato nella figura seguente:

![Criteri di firma di accesso condiviso di Hub eventi](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)

È possibile aggiungere nuovi criteri di firma di accesso condiviso e ottenere la stringa di connessione oppure è possibile usare i criteri predefiniti che sono già stati creati automaticamente. Quando vengono aperti i criteri, è possibile ottenere la stringa di connessione come illustrato nella figura seguente:

![Ottenere la stringa di connessione ad Hub eventi](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Ottenere la stringa di connessione con Azure PowerShell
È possibile usare Get-AzureRmEventHubNamespaceKey per ottenere la stringa di connessione per specificare il nome dei criteri o della regola come mostrato di seguito:

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

Per altre informazioni, fare riferimento ad [Azure Event Hubs PowerShell module](https://docs.microsoft.com/powershell/module/azurerm.eventhub/get-azurermeventhubkey) (Modulo PowerShell per Hub eventi di Azure).

## <a name="getting-the-connection-string-with-azure-cli"></a>Ottenere la stringa di connessione con l'interfaccia della riga di comando di Azure
Per ottenere la stringa di connessione per lo spazio dei nomi, è possibile usare il comando seguente:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Per altre informazioni, fare riferimento ad [Azure CLI for Event Hubs](https://docs.microsoft.com/cli/azure/eventhubs) (Interfaccia della riga di comando di Azure per Hub eventi).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md)
* [Create an Event Hub](event-hubs-create.md) (Creare un Hub eventi)
