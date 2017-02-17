---
title: Limiti di sistema StorSimple serie 8000 | Documentazione Microsoft
description: Descrive i limiti di sistema e le dimensioni consigliate per le connessioni e i componenti StorSimple serie 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: c7392678-0924-46c6-9c59-1665cb9b6586
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/30/2017
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: d81b20ca17ddf723dcc11148f7b44e763dde100b
ms.openlocfilehash: 1af0f36ce1161b35a2f75d74774ed145c8bc9328


---
# <a name="storsimple-system-limits"></a>Limiti di sistema StorSimple
## <a name="overview"></a>Panoramica
StorSimple fornisce una risorsa di archiviazione scalabile e flessibile per il datacenter. Esistono tuttavia alcuni limiti che è necessario tenere in considerazione quando si pianifica, distribuisce e utilizza una soluzione StorSimple. Nella tabella seguente vengono descritti questi limiti e vengono forniti alcuni suggerimenti per ottenere il massimo dalla soluzione StorSimple.

| Identificatore limite | Limite | Commenti |
| --- | --- | --- |
| Numero massimo di credenziali dell'account di archiviazione |64 | |
| Numero massimo di contenitori di volumi |64 | |
| Numero massimo di volumi |255 | |
| Numero massimo di volumi aggiunti in locale |32 | |
| Numero massimo di pianificazioni per ogni modello di larghezza di banda |168 |Una pianificazione per ogni ora, ogni giorno della settimana (24*7). |
| Dimensioni massime di un volume a livelli nei dispositivi fisici |64 TB per 8100 e 8600 |8100 e 8600 sono dispositivi fisici. |
| Dimensioni massime di un volume a livelli su dispositivi virtuali di Azure |30 TB per 8010  <br></br> 64 TB per 8020 |8010 e 8020 sono dispositivi virtuali di Azure che usano rispettivamente archiviazione Standard e Premium. |
| Dimensioni massime di un volume aggiunto in locale nei dispositivi fisici |8,5 TB per 8100 <br></br> 22.5 TB per 8600 |8100 e 8600 sono dispositivi fisici. |
| Numero massimo di connessioni iSCSI |512 | |
| Numero massimo di connessioni iSCSI dagli iniziatori |512 | |
| Numero massimo di record di controllo di accesso per dispositivo |64 | |
| Numero massimo di volumi per criteri di backup |20 | |
| Numero massimo di backup mantenuti per pianificazione (in un criterio di backup) |64 | |
| Numero massimo di pianificazioni per criteri di backup |10 | |
| Numero massimo di snapshot di qualsiasi tipo che è possibile mantenere per volume |256 |Questo numero include gli snapshot locali e quelli cloud. |
| Numero massimo di snapshot che possono essere presenti in qualsiasi dispositivo |10.000 | |
| Numero massimo di volumi che possono essere elaborati in parallelo per backup, ripristino o clonazione |16 |<ul><li>Se sono presenti più di 16 volumi, questi verranno elaborati in sequenza man mano che gli slot di elaborazione si rendono disponibili.</li><li>Non è possibile eseguire nuovi backup di un volume a livelli clonato o ripristinato fino a quando l'operazione non è completata. Tuttavia, per un volume locale, i backup sono consentiti dopo che il volume è online.</li></ul> |
| Ripristino e clonazione del tempo di ripristino per volumi a livelli |< 2 minuti |<ul><li>Il volume viene reso disponibile entro 2 minuti dall'operazione di ripristino o clonazione, indipendentemente dalla dimensione del volume.</li><li>È possibile che inizialmente le prestazioni del volume siano più lente del normale, poiché la maggior parte dei dati e dei metadati si trova ancora nel cloud. Le prestazioni possono migliorare con il flusso dei dati dal cloud al dispositivo StorSimple.</li><li>Il tempo totale necessario per scaricare i metadati dipende dalle dimensioni del volume allocato. I metadati vengono inseriti automaticamente nel dispositivo in background alla velocità di 5 minuti per TB di dati di volume allocato. La velocità può dipendere dalla larghezza di banda di Internet nel cloud.</li><li>L'operazione di ripristino o clonazione è completa quando tutti i metadati sono sul dispositivo.</li><li>Non è possibile eseguire le operazioni di backup fino a quando l'operazione di ripristino o clonazione non è stata completata. |
| Ripristinare il tempo di ripristino per i volumi aggiunti in locale |< 2 minuti |<ul><li>Il volume viene reso disponibile entro 2 minuti dall'operazione di ripristino, indipendentemente dalla dimensione del volume.</li><li>È possibile che inizialmente le prestazioni del volume siano più lente del normale, poiché la maggior parte dei dati e dei metadati si trova ancora nel cloud. Le prestazioni possono migliorare con il flusso dei dati dal cloud al dispositivo StorSimple.</li><li>Il tempo totale necessario per scaricare i metadati dipende dalle dimensioni del volume allocato. I metadati vengono inseriti automaticamente nel dispositivo in background alla velocità di 5 minuti per TB di dati di volume allocato. La velocità può dipendere dalla larghezza di banda di Internet nel cloud.</li><li>A differenza dei volumi a livelli, per i volumi aggiunti in locale, i dati del volume vengono anche scaricati localmente nel dispositivo. L'operazione di ripristino è completa quando tutti i dati del volume sono stati portati sul dispositivo.</li><li>Le operazioni di ripristino possono richiedere molto tempo. Il tempo totale necessario per completare il ripristino dipende dalle dimensioni del volume locale con provisioning, dalla larghezza di banda di Internet e dai dati esistenti nel dispositivo. Sono consentite operazioni di backup nel volume aggiunto in locale  mentre è in corso l'operazione di ripristino. |
| Velocità di elaborazione per gli snapshot cloud |15 minuti/TB |<ul><li>Tempo minimo per preparare lo snapshot cloud per il caricamento, per ogni TB di dati del volume allocati nel backup. </li><li> Il tempo totale dello snapshot cloud viene calcolato aggiungendo questo tempo al tempo di caricamento dello snapshot, che dipende dalla larghezza di banda Internet per la connessione al cloud. |
| Massima velocità effettiva in lettura/scrittura del client (quando servita dal livello SSD)* |920/720 MB/s con una singola interfaccia di rete 10 GbE |Fino a 2x con MPIO e due interfacce di rete. |
| Massima velocità effettiva in lettura/scrittura del client (quando servita dal livello HDD)* |120/250 MB/s | |
| Massima velocità effettiva in lettura/scrittura del client (quando servita dal livello Cloud)* per l'aggiornamento 3 e successivi** |40/60 MB/s per i volumi a livelli<br><br>60/80 MB/s per i volumi a livelli con opzione di archiviazione selezionata durante la creazione del volume |La velocità effettiva di lettura dipende dai client che generano e gestiscono una profondità della coda I/O sufficiente. <br><br>La velocità ottenuta dipende dalla velocità dell'account di archiviazione sottostante utilizzato. |

&#42; La velocità effettiva massima per ciascun tipo di I/O è stata misurata con scenari di scrittura al 100% e scenari di lettura al 100%. La velocità effettiva potrebbe essere inferiore a seconda delle condizioni della rete e della combinazione I/O.

&#42;&#42; I dati sulle prestazioni precedenti l'aggiornamento 3 potrebbero essere inferiori.

## <a name="next-steps"></a>Passaggi successivi
Consultare i [requisiti di sistema di StorSimple](storsimple-system-requirements.md). 




<!--HONumber=Feb17_HO1-->


