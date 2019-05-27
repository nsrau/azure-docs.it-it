---
title: Ottenere la stringa di connessione - Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce istruzioni su come ottenere la stringa di connessione che i client possono usare per connettersi a Hub eventi di Azure.
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/19/2019
ms.author: spelluru
ms.openlocfilehash: edd197fb6d578df064c67a422767e3e70a0c8142
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66158892"
---
# <a name="get-an-event-hubs-connection-string"></a>Ottenere una stringa di connessione ad Hub eventi

Per usare Hub eventi, è necessario creare uno spazio dei nomi di Hub eventi. Uno spazio dei nomi è un contenitore di ambito per più argomenti di hub eventi o Kafka. Questo spazio dei nomi specifica un nome [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) univoco. Dopo avere creato uno spazio dei nomi, è possibile ottenere la stringa di connessione necessaria per comunicare con Hub eventi.

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
1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Scegliere **Tutti i servizi** dal menu di spostamento a sinistra. 
3. Nella sezione **Analytics** selezionare **Hub eventi**. 
4. Selezionare l'hub eventi dal relativo elenco.
6. Nella pagina **Spazio dei nomi di Hub eventi** selezionare **Criteri di accesso condivisi** nel menu a sinistra.

    ![Voce di menu Criteri di accesso condivisi](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Selezionare un **criterio di accesso condiviso** nell'elenco dei criteri. Il criterio predefinito è denominato: **RootManageSharedAccessPolicy**. È possibile aggiungere un criterio con le autorizzazioni appropriate (lettura, scrittura) e usare quel criterio. 

    ![Criteri di accesso condiviso di Hub eventi](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Selezionare il pulsante di **copia** accanto al campo **Chiave primaria della stringa di connessione**. 

    ![Hub eventi - Ottenere la stringa di connessione](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Ottenere la stringa di connessione con Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

È possibile usare [Get-AzEventHubNamespaceKey](/powershell/module/az.eventhub/get-azeventhubkey) per ottenere la stringa di connessione per il nome specifico dei criteri o della regola, come mostrato di seguito:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Ottenere la stringa di connessione con l'interfaccia della riga di comando di Azure
Per ottenere la stringa di connessione per lo spazio dei nomi, è possibile usare il comando seguente:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Per altre informazioni sui comandi dell'interfaccia della riga di comando di Azure per Hub eventi, vedere [Azure CLI for Event Hubs](/cli/azure/eventhubs) (Interfaccia della riga di comando di Azure per Hub eventi).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md)
* [Create an Event Hub](event-hubs-create.md) (Creare un Hub eventi)
