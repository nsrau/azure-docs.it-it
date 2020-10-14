---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 42d1e0f056457ba54e0102a4d23f42a81ebea08d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90068737"
---
**In che modo si viene avvisati del ritiro degli SDK?**

Microsoft invierà un preavviso di 12 mesi prima fine del supporto dell'SDK in fase di ritiro per agevolare la transizione a un SDK supportato. Verrà inviata una notifica tramite diversi canali di comunicazione, ossia il portale di Azure, gli aggiornamenti di Azure e la comunicazione diretta con gli amministratori del servizio assegnati.

**È possibile creare applicazioni usando una versione di Azure Cosmos DB SDK che sta per essere ritirata durante il periodo di 12 mesi?** 

Sì, durante il periodo di preavviso di 12 mesi sarà possibile creare, distribuire e modificare applicazioni usando la versione di Azure Cosmos DB SDK che sta per essere ritirata. Durante il periodo di preavviso di 12 mesi, è consigliabile eseguire la migrazione a una versione più recente supportata di Azure Cosmos DB SDK. 

**Dopo la data di ritiro, che cosa succede alle applicazioni che usano una versione di Azure Cosmos DB SDK non supportata?** 

Dopo la data di ritiro, Azure Cosmos DB non eseguirà più correzioni dei bug, non aggiungerà nuove funzionalità, né fornirà supporto per le versioni ritirate dell'SDK. Se si preferisce non eseguire l'aggiornamento, le richieste inviate dalle versioni ritirate dell'SDK continueranno a essere gestite dal servizio Azure Cosmos DB. 

**Quali versioni dell'SDK avranno le funzionalità e gli aggiornamenti più recenti?**

Le nuove funzionalità e gli aggiornamenti verranno aggiunti solo alla versione secondaria più recente della versione principale più recente supportata dell'SDK. È consigliabile usare sempre la versione più recente per sfruttare le nuove funzionalità, i miglioramenti delle prestazioni e le correzioni dei bug. Se si usa una versione di SDK precedente, non ritirata, le richieste inviate ad Azure Cosmos DB continueranno a funzionare, ma non sarà possibile accedere alle nuove funzionalità.  

**Come si deve procedere se non è possibile aggiornare l'applicazione prima della data del ritiro?**

Si consiglia di effettuare l'aggiornamento alla versione di SDK più recente quanto prima. Una volta che un SDK è stato contrassegnato per il ritiro, si avranno a disposizione 12 mesi per aggiornare l'applicazione. Se non si è in grado di eseguire l'aggiornamento entro la data di ritiro, le richieste inviate dalle versioni ritirate dell'SDK continueranno a essere gestite da Azure Cosmos DB, quindi le applicazioni in esecuzione continueranno a funzionare. Tuttavia, Azure Cosmos DB non eseguirà più correzioni dei bug, non aggiungerà nuove funzionalità, né non fornirà supporto per le versioni ritirate dell'SDK. 

Se si ha un piano di supporto e si richiede supporto tecnico, [contattare](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) Microsoft inviando un ticket di supporto.
    


