---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 5c5ed4e1bbe54c642202c19e1beb61de94b4f751
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88515261"
---
**1. In che modo i clienti verranno avvisati circa l'SDK in fase di ritiro?**

Microsoft fornirà una notifica di anticipo di 12 mesi alla fine del supporto dell'SDK di ritiro per facilitare una transizione senza problemi a un SDK supportato. Inoltre, i clienti riceveranno una notifica tramite diversi canali di comunicazione: portale di Azure, gli aggiornamenti di Azure e la comunicazione diretta agli amministratori del servizio assegnati.

**2. i clienti possono creare applicazioni che usano un Azure Cosmos DB SDK "da ritirare" durante il periodo di 12 mesi?** 

Sì, i clienti avranno accesso completo per creare, distribuire e modificare applicazioni usando il Azure Cosmos DB SDK "in fase di ritiro" durante il periodo di preavviso di 12 mesi. Durante il periodo di preavviso di 12 mesi, i clienti sono invitati a eseguire la migrazione a una versione supportata più recente di Azure Cosmos DB SDK, a seconda dei casi. 

**3. dopo la data di ritiro, cosa accade alle applicazioni che usano l'SDK Azure Cosmos DB non supportato?** 

Dopo la data di ritiro, Azure Cosmos DB non effettuerà più correzioni di bug, aggiungono nuove funzionalità e fornirà supporto per le versioni di SDK ritirate. Se si preferisce non eseguire l'aggiornamento, le richieste inviate dalle versioni ritirate dell'SDK continueranno a essere gestite dal servizio Azure Cosmos DB. 

**4. quali versioni dell'SDK avranno le funzionalità e gli aggiornamenti più recenti?**

Le nuove funzionalità e gli aggiornamenti verranno aggiunti solo alla versione secondaria più recente della versione più recente dell'SDK principale supportata. È consigliabile usare sempre la versione più recente per sfruttare i vantaggi delle nuove funzionalità, miglioramenti delle prestazioni e correzioni di bug. Se si usa una versione precedente, non ritirata, dell'SDK, le richieste a Azure Cosmos DB continueranno a funzionare, ma non sarà possibile accedere a nuove funzionalità.  

**5. cosa devo fare se non è possibile aggiornare l'applicazione prima di una data di riduzione?**

Si consiglia di effettuare l'aggiornamento alla versione di SDK più recente quanto prima. Una volta che un SDK è stato contrassegnato per il ritiro, si avranno 12 mesi per aggiornare l'applicazione. Se non si è in grado di eseguire l'aggiornamento in base alla data di ritiro, le richieste inviate dalle versioni ritirate dell'SDK continueranno a essere gestite da Azure Cosmos DB, quindi le applicazioni in esecuzione continueranno a funzionare. Tuttavia, Azure Cosmos DB non effettueranno più correzioni di bug, aggiungono nuove funzionalità e forniranno il supporto per le versioni di SDK ritirate. 

Se si dispone di un piano di supporto e si richiede supporto tecnico, [Contattaci](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) inviando un ticket di supporto.
    


