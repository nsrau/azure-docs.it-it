---
title: includere file
description: includere file
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/23/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0335481566ae3f28ac0f1e6bddce7050a65e7dc2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516996"
---
### <a name="what-can-i-achieve-with-a-cluster"></a>Cosa si può ottenere con un cluster?

Per un cluster di hub eventi, quanto è possibile inserire e trasmettere in streaming dipende da diversi fattori, ad esempio produttori, consumer, velocità di inserimento ed elaborazione e molto altro ancora. 

La tabella seguente illustra i risultati del benchmark ottenuti durante il test:

| Forma del payload | Destinatari | Larghezza di banda in ingresso| Messaggi in ingresso | Larghezza di banda in uscita | Messaggi in uscita | Unità elaborate totali | Unità elaborate per unità di capacità |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Batch di 100 x 1 KB | 2 | 400 MB/sec | 400.000 messaggi/sec | 800 MB/sec | 800.000 messaggi/sec | 400 unità elaborate | 100 unità elaborate | 
| Batch di 10 x 10 KB | 2 | 666 MB/sec | 66.600 messaggi/sec | 1,33 GB/sec | 133.000 messaggi/sec | 666 unità elaborate | 166 unità elaborate |
| Batch di 6 x 32 KB | 1 | 1,05 GB/sec | 34.000 messaggi/sec | 1,05 GB/sec | 34.000 messaggi/sec | 1000 unità elaborate | 250 unità elaborate |

Nei test sono stati usati i criteri seguenti:

- È stato usato un cluster di hub eventi di livello dedicato con quattro unità di capacità. 
- L'hub eventi usato per l'inserimento aveva 200 partizioni. 
- I dati inseriti da tutte le partizioni vengono ricevuti da due applicazioni riceventi.

### <a name="can-i-scale-updown-my-cluster"></a>È possibile aumentare/ridurre il cluster?

Al termine della creazione, i cluster vengono fatturati per almeno 4 ore di utilizzo. Nella versione di anteprima dell'esperienza self-service è possibile inviare una richiesta di [supporto](https://ms.portal.azure.com/#create/Microsoft.Support) al team di hub eventi con la **Technical**  >  **Quota**  >  **richiesta di quota tecnica per aumentare** o ridurre il cluster dedicato per aumentare o ridurre il cluster. Potrebbero essere richiesti fino a 7 giorni per completare la richiesta di ridimensionamento del cluster. 

### <a name="how-does-geo-dr-work-with-my-cluster"></a>Come funziona il ripristino di emergenza geografico con il cluster?

È possibile associare uno spazio dei nomi in un cluster a livello dedicato con un altro spazio dei nomi in un cluster di livello dedicato. Non è consigliabile associare uno spazio dei nomi a livello dedicato con uno spazio dei nomi nell'offerta standard, perché il limite di velocità effettiva sarà incompatibile e comporterà errori. 

### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>È possibile eseguire la migrazione degli spazi dei nomi standard in modo che appartengano a un cluster di livello dedicato?
Attualmente non è supportato un processo di migrazione automatizzato per la migrazione dei dati di hub eventi da uno spazio dei nomi standard a uno dedicato. 
