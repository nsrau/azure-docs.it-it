---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238808"
---
| Identificatore limite | Limite | Commenti |
| --- | --- | --- |
| Numero massimo di credenziali dell'account di archiviazione |64 | |
| Numero massimo di contenitori di volumi |64 | |
| Numero massimo di volumi |255 | |
| Numero massimo di pianificazioni per ogni modello di larghezza di banda |168 |Una pianificazione per ogni ora, ogni giorno della settimana. |
| Dimensioni massime di un volume a livelli nei dispositivi fisici |64 TB per StorSimple 8100 e StorSimple 8600 |StorSimple 8100 e StorSimple 8600 sono dispositivi fisici. |
| Dimensioni massime di un volume a livelli su dispositivi virtuali di Azure |30 TB per 8010 StorSimple <br></br> 64 TB per 8020 StorSimple |StorSimple 8010 e 8020 StorSimple sono dispositivi virtuali di Azure che usano rispettivamente archiviazione Standard e archiviazione Premium. |
| Dimensioni massime di un volume aggiunto in locale nei dispositivi fisici |9 TB per 8100 di StorSimple <br></br> 24 TB per StorSimple 8600 |StorSimple 8100 e StorSimple 8600 sono dispositivi fisici. |
| Numero massimo di connessioni iSCSI |512 | |
| Numero massimo di connessioni iSCSI dagli iniziatori |512 | |
| Numero massimo di record di controllo di accesso per dispositivo |64 | |
| Numero massimo di volumi per criteri di backup |24 | |
| Numero massimo di backup mantenuti per criteri di backup |64 | |
| Numero massimo di pianificazioni per criteri di backup |10 | |
| Numero massimo di snapshot di qualsiasi tipo che è possibile mantenere per volume |256 |Questo quantità include snapshot locali e gli snapshot cloud. |
| Numero massimo di snapshot che possono essere presenti in qualsiasi dispositivo |10,000 | |
| Numero massimo di volumi che possono essere elaborati in parallelo per backup, ripristino o clonazione |16 |<ul><li>Se sono presenti più di 16 volumi, questi si elaborati in sequenza man mano che gli slot di elaborazione diventano disponibili.</li><li>Non è possibile eseguire nuovi backup di clonato o un volume a livelli ripristinato fino al termine dell'operazione. Per un volume locale, i backup sono consentiti quando il volume è online.</li></ul> |
| Ripristino e clonazione del tempo di ripristino per volumi a livelli |< 2 minuti |<ul><li>Il volume viene reso disponibile entro 2 minuti di un'operazione di ripristino o clonazione, indipendentemente dalla dimensione del volume.</li><li>Le prestazioni del volume inizialmente potrebbero essere più lente del normale come la maggior parte dei dati e metadati si trova ancora nel cloud. Le prestazioni potrebbero migliorare con il flusso di dati dal cloud al dispositivo StorSimple.</li><li>Il tempo totale necessario per scaricare i metadati dipende dalle dimensioni del volume allocato. I metadati vengono inseriti automaticamente nel dispositivo in background alla velocità di 5 minuti per TB di dati di volume allocato. Questa velocità può essere influenzata dalla larghezza di banda Internet nel cloud.</li><li>L'operazione di ripristino o clonazione è completa quando tutti i metadati sono sul dispositivo.</li><li>Impossibile eseguire operazioni di backup finché il ripristino o operazione di clonazione è stata completata. |
| Ripristinare il tempo di ripristino per i volumi aggiunti in locale |< 2 minuti |<ul><li>Il volume viene reso disponibile entro 2 minuti dall'operazione di ripristino, indipendentemente dalla dimensione del volume.</li><li>Le prestazioni del volume inizialmente potrebbero essere più lente del normale come la maggior parte dei dati e metadati si trova ancora nel cloud. Le prestazioni potrebbero migliorare con il flusso di dati dal cloud al dispositivo StorSimple.</li><li>Il tempo totale necessario per scaricare i metadati dipende dalle dimensioni del volume allocato. I metadati vengono inseriti automaticamente nel dispositivo in background alla velocità di 5 minuti per TB di dati di volume allocato. Questa velocità può essere influenzata dalla larghezza di banda Internet nel cloud.</li><li>A differenza dei volumi a livelli, se sono presenti volumi aggiunti in locale, i dati del volume vengono anche scaricati localmente nel dispositivo. L'operazione di ripristino è completa quando tutti i dati del volume sono stati portati sul dispositivo.</li><li>Le operazioni di ripristino potrebbero essere lunga e il tempo totale necessario per completare il ripristino dipende la dimensione del volume locale con provisioning, la larghezza di banda Internet e i dati esistenti nel dispositivo. Sono consentite operazioni di backup nel volume aggiunto in locale  mentre è in corso l'operazione di ripristino. |
| Disponibilità del ripristino di tipo thin |Ultimo failover | |
| Velocità effettiva di lettura/scrittura numero massimo di client, quando servita dal livello di unità SSD * |920/720 MB/sec in una singola interfaccia di rete 10 gigabit Ethernet |Fino a due volte con MPIO e due interfacce di rete. |
| Velocità effettiva di lettura/scrittura numero massimo di client, quando servita dal livello di unità disco rigido * |120/250 MB/sec | |
| Velocità effettiva di lettura/scrittura numero massimo di client, quando servita dal livello di cloud * |11/41 MB/sec |La velocità effettiva di lettura dipende dai client che generano e gestiscono una profondità della coda I/O sufficiente. |

&#42;Velocità effettiva massima per ogni tipo dei / o è stata misurata con scenari di scrittura al 100% e lettura al 100%. La velocità effettiva potrebbe essere basso e dipende dai / o combinare e le condizioni della rete.

