---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67180545"
---
| Identificatore limite | Limite | Commenti |
| --- | --- | --- |
| Numero massimo di credenziali dell'account di archiviazione |64 | |
| Numero massimo di contenitori di volumi |64 | |
| Numero massimo di volumi |255 | |
| Numero massimo di pianificazioni per ogni modello di larghezza di banda |168 |Un programma per ogni ora, ogni giorno della settimana. |
| Dimensioni massime di un volume a livelli nei dispositivi fisici |64 TB per StorSimple 8100 e StorSimple 8600 |StorSimple 8100 e StorSimple 8600 sono dispositivi fisici. |
| Dimensioni massime di un volume a livelli su dispositivi virtuali di Azure |30 TB per StorSimple 8010 <br></br> 64 TB per StorSimple 8020 |StorSimple 8010 e StorSimple 8020 sono dispositivi virtuali in Azure che usano rispettivamente l'archiviazione Standard e l'archiviazione Premium.StorSimple 8010 and StorSimple 8020 are virtual devices in Azure that use Standard storage and Premium storage, respectively. |
| Dimensioni massime di un volume aggiunto in locale nei dispositivi fisici |9 TB per StorSimple 8100 <br></br> 24 TB per StorSimple 8600 |StorSimple 8100 e StorSimple 8600 sono dispositivi fisici. |
| Numero massimo di connessioni iSCSI |512 | |
| Numero massimo di connessioni iSCSI dagli iniziatori |512 | |
| Numero massimo di record di controllo di accesso per dispositivo |64 | |
| Numero massimo di volumi per criteri di backup |24 | |
| Numero massimo di backup mantenuti per criteri di backup |64 | |
| Numero massimo di pianificazioni per criteri di backup |10 | |
| Numero massimo di snapshot di qualsiasi tipo che è possibile mantenere per volume |256 |Questa quantità include snapshot locali e snapshot cloud. |
| Numero massimo di snapshot che possono essere presenti in qualsiasi dispositivo |10,000 | |
| Numero massimo di volumi che possono essere elaborati in parallelo per backup, ripristino o clonazione |16 |<ul><li>Se sono presenti più di 16 volumi, vengono elaborati in sequenza man mano che gli slot di elaborazione diventano disponibili.</li><li>I nuovi backup di un volume a livelli clonato o ripristinato non possono essere eseguiti fino al termine dell'operazione. Per un volume locale, i backup sono consentiti dopo che il volume è online.</li></ul> |
| Ripristino e clonazione del tempo di ripristino per volumi a livelli |<2 minuti |<ul><li>Il volume viene reso disponibile entro 2 minuti da un'operazione di ripristino o clonazione, indipendentemente dalle dimensioni del volume.</li><li>Le prestazioni del volume potrebbero inizialmente essere più lente del normale poiché la maggior parte dei dati e dei metadati risiede ancora nel cloud. Le prestazioni potrebbero aumentare man mano che i dati scorrono dal cloud al dispositivo StorSimple.Performance might increase as data flows from the cloud to the StorSimple device.</li><li>Il tempo totale necessario per scaricare i metadati dipende dalle dimensioni del volume allocato. I metadati vengono inseriti automaticamente nel dispositivo in background alla velocità di 5 minuti per TB di dati di volume allocato. Questa velocità potrebbe essere influenzata dalla larghezza di banda Internet per il cloud.</li><li>L'operazione di ripristino o clonazione è completa quando tutti i metadati sono sul dispositivo.</li><li>Le operazioni di backup non possono essere eseguite fino al completamento dell'operazione di ripristino o clonazione. |
| Ripristinare il tempo di ripristino per i volumi aggiunti in locale |<2 minuti |<ul><li>Il volume viene reso disponibile entro 2 minuti dall'operazione di ripristino, indipendentemente dalla dimensione del volume.</li><li>Le prestazioni del volume potrebbero inizialmente essere più lente del normale poiché la maggior parte dei dati e dei metadati risiede ancora nel cloud. Le prestazioni potrebbero aumentare man mano che i dati scorrono dal cloud al dispositivo StorSimple.Performance might increase as data flows from the cloud to the StorSimple device.</li><li>Il tempo totale necessario per scaricare i metadati dipende dalle dimensioni del volume allocato. I metadati vengono inseriti automaticamente nel dispositivo in background alla velocità di 5 minuti per TB di dati di volume allocato. Questa velocità potrebbe essere influenzata dalla larghezza di banda Internet per il cloud.</li><li>A differenza dei volumi a livelli, se sono presenti volumi aggiunti in locale, anche i dati del volume vengono scaricati localmente nel dispositivo. L'operazione di ripristino è completa quando tutti i dati del volume sono stati portati sul dispositivo.</li><li>Le operazioni di ripristino potrebbero essere lunghe e il tempo totale necessario per completare il ripristino dipenderà dalle dimensioni del volume locale di cui è stato eseguito il provisioning, dalla larghezza di banda Internet e dai dati esistenti nel dispositivo. Sono consentite operazioni di backup nel volume aggiunto in locale  mentre è in corso l'operazione di ripristino. |
| Disponibilità del ripristino di tipo thin |Ultimo failover | |
| Velocità effettiva massima di lettura/scrittura del client, se servita dal livello SSD |920/720 MB/sec con una singola interfaccia di rete Ethernet a 10 gigabit |Fino a due volte con MPIO e due interfacce di rete. |
| Velocità effettiva massima di lettura/scrittura del client, se servita dal livello HDD |120/250 MB/sec | |
| Velocità effettiva massima di lettura/scrittura del client, se servita dal livello cloud |11/41 MB/sec |La velocità effettiva di lettura dipende dai client che generano e gestiscono una profondità della coda I/O sufficiente. |

&#42;velocità effettiva massima per tipo di I/O è stata misurata con scenari di lettura e scrittura al 100%. La velocità effettiva potrebbe essere inferiore e dipende dalla combinazione di I/O e dalle condizioni di rete.

