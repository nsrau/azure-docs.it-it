---
title: Come ripristinare un server nel Database di Azure per PostgreSQL | Microsoft Docs
description: Questo articolo illustra come eseguire la procedura di backup e ripristino di un server in Database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure.
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: fa63ccedb455e4972bfdd0e15069ad5e932b0fa3
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017

---

# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-cli"></a>Come eseguire la procedura di backup e ripristino di un server in Database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- Un [server e un database di Database di Azure per PostgreSQL](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="backup-happens-automatically"></a>Il backup viene eseguito automaticamente
Quando si usa Database di Azure per PostgreSQL, il servizio di database esegue automaticamente il backup del servizio ogni 5 minuti. 

La disponibilità dei backup è di 7 giorni per il livello Basic e 35 giorni per il livello Standard. Per altre informazioni, vedere [Livelli di servizio di Database di Azure per PostgreSQL](concepts-service-tiers.md)

L'uso di questa funzionalità di backup automatico consente di ripristinare il server e tutti i relativi database in un nuovo server a un momento precedente.

## <a name="restore-a-database-to-a-previous-point-in-time-using-the-azure-cli"></a>Ripristinare un database a un momento precedente usando l'interfaccia della riga di comando di Azure
Database di Azure per PostgreSQL consente di ripristinare il server a un momento precedente. I dati ripristinati vengono copiati in un nuovo server e il server esistente viene lasciato invariato. Se, ad esempio, una tabella è stata involontariamente eliminata a mezzogiorno di oggi, è possibile eseguire il ripristino a un qualsiasi momento prima di mezzogiorno. Recuperare quindi la tabella e i dati mancanti dalla copia ripristinata del server. 

Usare il comando dell'interfaccia della riga di comando di Azure [az postgres server restore](/cli/azure/postgres/server#restore) per eseguire il ripristino.

### <a name="run-the-restore-command"></a>Eseguire il comando di ripristino
```azurecli-interactive
az postgres server restore --resource-group myResourceGroup --name mypgserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mypgserver-20170401
```

Il comando `az postgres server restore` richiede i parametri seguenti:
| Impostazione | Valore consigliato | Descrizione  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Il gruppo di risorse in cui si trova il server di origine.  |
| name | mypgserver-restored | Il nome del nuovo server creato con il comando di ripristino. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Selezionare un punto di ripristino temporizzato. La data e l'ora devono trovarsi all'interno del periodo di memorizzazione dei backup del server di origine. Usare il formato ISO8601 per la data e l'ora. È possibile usare il fuso orario locale, ad esempio `2017-04-13T05:59:00-08:00`, o il formato UTC Zulu `2017-04-13T13:59:00Z`. |
| source-server | mypgserver-20170401 | Il nome o l'ID del server di origine da cui eseguire il ripristino. |

Il ripristino temporizzato di un server crea un nuovo server, ovvero una copia del server originale nel momento specificato. I valori relativi al percorso e al piano tariffario del server ripristinato sono gli stessi del server di origine. Il comando è sincrono e il risultato verrà restituito dopo il ripristino del server. 

Una volta terminato il ripristino, individuare il nuovo server creato. Verificare che i dati siano stati ripristinati come previsto.

## <a name="next-steps"></a>Passaggi successivi
[Raccolte connessioni per il Database di Azure per PostgreSQL](concepts-connection-libraries.md)

