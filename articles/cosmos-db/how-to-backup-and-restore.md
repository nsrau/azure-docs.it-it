---
title: Come ripristinare i dati di Azure Cosmos DB da un backup
description: Questo articolo descrive come ripristinare i dati di Azure Cosmos DB da un backup, come contattare il supporto tecnico di Azure per il ripristino dei dati e le procedure da seguire dopo il ripristino dei dati.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: c32c333de94d1ed0089323e00e6dbbaaebb36488
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241039"
---
# <a name="restore-data-from-a-backup-in-azure-cosmos-db"></a>Ripristinare i dati da un backup in Azure Cosmos DB 

Se si elimina accidentalmente il database o un contenitore, è possibile [inviare un ticket di supporto]( https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) o [contattare il supporto di Azure]( https://azure.microsoft.com/support/options/) per ripristinare i dati dai backup automatici online. Supporto di Azure è disponibile per i piani selezionati solo, ad esempio **Standard**, **Developer**e i piani superiori a essi. Il supporto di Azure non è disponibile con il piano **Basic**. Per altre informazioni sui diversi piani di supporto, vedere la pagina [Piani di supporto per Azure](https://azure.microsoft.com/support/plans/). 

Per il ripristino di uno snapshot specifico del backup, Azure Cosmos DB richiede che i dati siano stati disponibili per la durata del ciclo di backup per tale snapshot.

## <a name="request-a-restore"></a>Richiedere un ripristino

Prima di richiedere un ripristino, è necessario avere a disposizione i dettagli seguenti:

* ID della sottoscrizione.

* A seconda del modo in cui i dati sono stati accidentalmente eliminati o modificati, prepararsi per avere a disposizione ulteriori informazioni. Si consiglia di procurarsi le informazioni richieste in anticipo, per ridurre al minimo lo scambio di comunicazioni che potrebbe essere deleterio nei casi in cui la tempestività è cruciale.

* Se viene eliminato l'intero account Azure Cosmos DB, è necessario specificare il nome dell'account eliminato. Se si crea un altro account con lo stesso nome dell'account eliminato, condividerlo con il team di supporto perché ciò permette di determinare l'account corretto da scegliere. È consigliabile aprire ticket di supporto diversi per ogni account eliminato, perché in questo modo si evita di fare confusione con lo stato del ripristino.

* Se vengono eliminati uno o più database, è necessario comunicare l'account Azure Cosmos, nonché i nomi dei database di Azure Cosmos e specificare se esiste un nuovo database con lo stesso nome.

* Se vengono eliminati uno o più contenitori, è necessario comunicare il nome dell'account Azure Cosmos, i nomi dei database e i nomi dei contenitori. Specificare inoltre se esiste un contenitore con lo stesso nome.

* Se è accidentalmente eliminato o danneggiato i dati, è necessario contattare [supporto tecnico di Azure](https://azure.microsoft.com/support/options/) entro 8 ore in modo che possa aiutare il team di Azure Cosmos DB si ripristinano i dati dai backup.
  
  * Se è stato accidentalmente eliminato il database o il contenitore, aprire un caso di supporto Sev B o Sev C Azure. 
  * Se è accidentalmente eliminato o danneggiato alcuni documenti all'interno del contenitore, aprire un caso di supporto A Sev. 

Quando si verifica un danneggiamento dei dati e se i documenti all'interno di un contenitore vengono modificati o eliminati, **eliminare il contenitore appena possibile**. Con l'eliminazione del contenitore è possibile evitare che Azure Cosmos DB sovrascriva i backup. Se per qualche motivo l'eliminazione non è possibile, è necessario aprire un ticket appena possibile. Oltre a nome dell'account Azure Cosmos, nomi di database e nomi dei contenitori, è necessario specificare il punto nel tempo per il ripristino dei dati. È importante essere il più precisi possibile per consentire a Microsoft di determinare i backup disponibili migliori nell'intervallo di tempo specificato. È anche importante specificare l'ora in formato UTC. 

Lo screenshot seguente illustra come creare una richiesta di supporto per un contenitore (raccolta/grafo/tabella) per ripristinare i dati tramite il portale di Azure. Specificare ulteriori dettagli, come tipo di dati, scopo del ripristino e ora di eliminazione dei dati per consentire a Microsoft di definire più facilmente la priorità della richiesta.

![Creare una richiesta di supporto per il backup usando il portale di Azure](./media/how-to-backup-and-restore/backup-support-request-portal.png)

## <a name="post-restore-actions"></a>Azioni successive al ripristino

Dopo aver ripristinato i dati, si riceverà una notifica riguardante il nome del nuovo account (in genere nel formato `<original-name>-restored1`) e l'ora del ripristino dell'account. L'account ripristinato avrà la stessa velocità effettiva di cui è stato effettuato il provisioning, gli stessi criteri di indicizzazione e sarà incluso nella stessa area dell'account originale. Un utente coamministratore o amministratore della sottoscrizione può visualizzare l'account ripristinato.

Dopo il ripristino dei dati, è necessario esaminare e convalidare i dati nell'account ripristinato e assicurarsi che contengano la versione prevista. Se tutto sembra corretto, è necessario eseguire la migrazione dei dati di nuovo nell'account originale tramite un [feed di modifiche di Azure Cosmos DB](change-feed.md) oppure [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

Si consiglia di eliminare il contenitore o il database immediatamente dopo la migrazione dei dati. Se non si eliminano i database o i contenitori ripristinati, questi elementi genereranno costi per unità richieste, archiviazione e dati in uscita.

## <a name="next-steps"></a>Passaggi successivi

È possibile scoprire come eseguire la migrazione dei dati di nuovo nell'account originale negli articoli seguenti:

* Per creare una richiesta di ripristino, contattare il supporto tecnico di Azure e [aprire un ticket dal portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Usare un feed di modifiche di Cosmos DB](change-feed.md) per spostare i dati in Azure Cosmos DB.

* [Usare Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) per spostare i dati in Azure Cosmos DB.
