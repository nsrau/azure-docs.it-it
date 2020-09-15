---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 42d1e0f056457ba54e0102a4d23f42a81ebea08d
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068737"
---
**Come verrà notificato il ritiro dell'SDK?**

Microsoft fornirà il preavviso di 12 mesi prima della fine del supporto dell'SDK di ritiro per facilitare una transizione senza problemi a un SDK supportato. Verrà inviata una notifica tramite diversi canali di comunicazione: il portale di Azure, gli aggiornamenti di Azure e la comunicazione diretta con gli amministratori del servizio assegnati.

**È possibile creare applicazioni utilizzando un Azure Cosmos DB SDK da ritirate durante il periodo di 12 mesi?** 

Sì, sarà possibile creare, distribuire e modificare applicazioni usando il Azure Cosmos DB SDK da ritirare durante il periodo di preavviso di 12 mesi. Si consiglia di eseguire la migrazione a una versione supportata più recente di Azure Cosmos DB SDK durante il periodo di preavviso di 12 mesi, a seconda dei casi. 

**Dopo la data di ritiro, cosa accade alle applicazioni che usano l'SDK Azure Cosmos DB non supportato?** 

Dopo la data di ritiro, Azure Cosmos DB non effettuerà più correzioni di bug, aggiungono nuove funzionalità o fornirà supporto per le versioni di SDK ritirate. Se si preferisce non eseguire l'aggiornamento, le richieste inviate dalle versioni ritirate dell'SDK continueranno a essere gestite dal servizio Azure Cosmos DB. 

**Quali versioni dell'SDK avranno le funzionalità e gli aggiornamenti più recenti?**

Le nuove funzionalità e gli aggiornamenti verranno aggiunti solo alla versione secondaria più recente della versione più recente dell'SDK principale supportata. È consigliabile usare sempre la versione più recente per sfruttare le nuove funzionalità, i miglioramenti delle prestazioni e le correzioni di bug. Se si usa una versione precedente di SDK non ritirata, le richieste a Azure Cosmos DB continueranno a funzionare, ma non sarà possibile accedere a nuove funzionalità.  

**Quali operazioni è necessario eseguire se non è possibile aggiornare l'applicazione prima di una data di taglio?**

Si consiglia di effettuare l'aggiornamento alla versione di SDK più recente quanto prima. Dopo che un SDK è stato contrassegnato per il ritiro, saranno necessari 12 mesi per aggiornare l'applicazione. Se non si è in grado di eseguire l'aggiornamento in base alla data di ritiro, le richieste inviate dalle versioni ritirate dell'SDK continueranno a essere gestite da Azure Cosmos DB, quindi le applicazioni in esecuzione continueranno a funzionare. Tuttavia, Azure Cosmos DB non effettueranno più correzioni di bug, aggiungono nuove funzionalità o forniranno il supporto per le versioni di SDK ritirate. 

Se hai un piano di supporto e Richiedi supporto tecnico, [Contattaci](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) inviando un ticket di supporto.
    


