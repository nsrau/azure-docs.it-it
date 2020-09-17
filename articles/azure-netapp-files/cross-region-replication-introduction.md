---
title: Replica tra aree di volumi Azure NetApp Files | Microsoft Docs
description: Viene descritto il Azure NetApp Files della replica tra aree, le coppie di aree supportate, gli obiettivi a livello di servizio, la durabilità dei dati e il modello di costo.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: e7d424435c29eb3e7a6779a7036816a7bedd2085
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708806"
---
# <a name="cross-region-replication-of-azure-netapp-files-volumes"></a>Replica tra aree di volumi Azure NetApp Files

La funzionalità di replica Azure NetApp Files garantisce la protezione dei dati tramite la replica del volume tra più aree. È possibile replicare in modo asincrono i dati da un volume Azure NetApp Files (origine) in un'area a un altro volume Azure NetApp Files (destinazione) in un'altra area.  Questa funzionalità consente di eseguire il failover dell'applicazione critica in caso di interruzione o emergenza a livello di area.

> [!IMPORTANT]
> La funzionalità di replica tra aree è attualmente disponibile in anteprima pubblica. È necessario inviare una richiesta di attesa per l'accesso alla funzionalità tramite la pagina Azure NetApp Files l'invio dell'oggetto di attesa per la [replica tra aree](https://aka.ms/anfcrrpreviewsignup). Attendere un messaggio di posta elettronica di conferma ufficiale dal team di Azure NetApp Files prima di usare la funzionalità di replica tra aree.

## <a name="supported-region-pairs"></a>Coppie di aree supportate

Azure NetApp Files replica del volume è attualmente disponibile nelle seguenti coppie di aree fisse:  

* Stati Uniti occidentali e Stati Uniti orientali
* Stati Uniti occidentali 2 e Stati Uniti orientali 
* Stati Uniti centro-meridionali e Stati Uniti centrali 
* Stati Uniti centro-meridionali e Stati Uniti orientali
* Stati Uniti centro-meridionali e Stati Uniti orientali 2 
* Stati Uniti orientali 2 e Stati Uniti centrali 
* Europa settentrionale e Europa occidentale
* Regno Unito meridionale e Regno Unito occidentale
* Australia orientale e Australia sudorientale
* Canada centrale e Canada orientale
* Giappone orientale e Giappone occidentale
* Regno Unito meridionale e Germania centro-occidentale
* Asia sudorientale e Australia orientale

## <a name="service-level-objectives"></a>Obiettivi a livello di servizio

Gli obiettivi del punto di ripristino (RPO) o la perdita massima di dati tollerabili sono definiti come due volte la pianificazione della replica.  Il RPO effettivo rilevato può variare in base a fattori quali la dimensione totale del set di dati e la frequenza di modifica, la percentuale di sovrascritture dei dati e la larghezza di banda di replica disponibile per il trasferimento.   

* Per la pianificazione della replica di 10 minuti, il valore massimo di RPO è 20 minuti.  
* Per la pianificazione della replica oraria, il valore massimo di RPO è due ore.  
* Per la pianificazione della replica giornaliera, il numero massimo di RPO è di due giorni.  

L'obiettivo del tempo di ripristino (RTO) o il tempo di inattività massimo tollerabile per le applicazioni aziendali è determinato dai fattori che comportano l'applicazione e forniscono l'accesso ai dati nel secondo sito. La parte di archiviazione di RTO per l'interrotta della relazione di peering per attivare il volume di destinazione e fornire l'accesso ai dati di lettura e scrittura nel secondo sito deve essere completata entro un minuto.

## <a name="cost-model-for-cross-region-replication"></a>Modello di costo per la replica tra aree  

Con Azure NetApp Files replica tra aree, si paga solo la quantità di dati replicati. Non sono previsti addebiti per l'installazione o una tariffa di utilizzo minima. Il prezzo di replica si basa sulla frequenza di replica e sull'area del volume di *destinazione* scelto durante la configurazione della replica iniziale. Per ulteriori informazioni, vedere la pagina relativa ai [prezzi Azure NetApp files](https://azure.microsoft.com/pricing/details/netapp/) .  

Il normale costo della capacità di archiviazione Azure NetApp Files si applica al volume di destinazione della replica (detto anche volume di *protezione dati* ). 

### <a name="pricing-examples"></a>Esempi di prezzi

L'importo della replica tra aree fatturato in un mese è basato sulla quantità di dati replicati tramite la funzionalità di replica tra aree durante il mese. La quantità di dati replicati viene misurata in GiB. Rappresenta la somma dei dati replicati in due aree durante tutte le repliche regolari dai volumi di origine ai volumi di destinazione e durante tutte le resincronizzazioni di replica dai volumi di destinazione ai volumi di origine.

#### <a name="example-1-month-1-baseline-replication-and-incremental-replications"></a>Esempio 1: replica di base del mese 1 e repliche incrementali

Si presuppongano le situazioni seguenti:

* Il volume di *origine* viene dal livello di servizio Azure NetApp files *Premium* . Ha una dimensione della quota del volume di 1000 GiB e una dimensione del volume consumata di 500 GiB all'inizio del primo giorno del mese. Il volume si trova nell'area *Stati Uniti centro-meridionali* .
* Il volume di *destinazione* è del livello di servizio Azure NetApp files *standard* . Si trova nell'area *Stati Uniti orientali 2* .
* È stata configurata una replica tra aree basate su base *oraria* tra i due volumi precedenti. Il prezzo della replica è quindi $0,12 per GiB.
* Per semplicità, si supponga che il volume di origine abbia una costante modifica dei dati 0,5-GiB ogni ora, ma le dimensioni totali del volume utilizzato non aumentano (rimane a 500 GiB). 

Dopo l'installazione iniziale, la replica di base viene eseguita immediatamente.  

* Quantità di dati replicati durante la replica di base: `500 GiB`
* Addebiti per la replica di base: `500 GiB * $0.12 = $60`

Dopo la replica di base, vengono replicati solo i blocchi modificati. Pertanto, solo 0,5 GiB di dati verranno replicati ogni ora nelle successive repliche incrementali.

* Somma della quantità di dati replicata tra le repliche incrementali per un mese di 30 giorni: `0.5 GiB * 24 hours * 30 days = 360 GiB`
* Addebiti per la replica incrementale: `360 GiB * $0.12 = $43.2`

Alla fine del mese 1, l'addebito totale della replica tra aree è il seguente:  

*  Costo totale della replica tra aree del mese 1: `$60 + $43.2 = $103.2`

Il normale costo della capacità di archiviazione Azure NetApp Files si applica al volume di destinazione. Tuttavia, il volume di destinazione può usare un livello di archiviazione diverso dal livello del volume di origine (e più basso).

#### <a name="example-2-month-2-incremental-replications-and-resync-replications"></a>Esempio 2: mese 2 repliche incrementali e resync  

Si supponga di avere un volume di origine, un volume di destinazione e una relazione di replica tra i due set, come descritto nell'esempio 1. Per 29 giorni del secondo mese (mese di 30 giorni), le repliche orarie si sono verificate come previsto.

* Somma della quantità di dati replicata tra le repliche incrementali per 29 giorni: `0.5 GiB * 24 hours * 29 days = 348 GiB`

Si supponga che l'ultimo giorno del mese si sia verificata un'interruzione non pianificata nell'area di origine ed è stato eseguito il failover al volume di destinazione. Dopo 2 ore, l'area di origine è stata ripristinata ed è stata eseguita una replica di risincronizzazione dal volume di destinazione al volume di origine. Durante le 2 ore, 0,8 GiB della modifica dei dati si sono verificati nel volume di destinazione ed è necessario risincronizzarli con l'origine.

* Somma della quantità di dati replicata tra le repliche regolari per 22 ore nell'ultimo giorno: `0.5 GiB * 22 hours = 11 GiB`
* Quantità di dati replicati durante una replica di risincronizzazione: `0.8 GiB`

Quindi, entro la fine del mese 2, il costo totale della replica tra aree è il seguente:  

* Costo totale della replica tra aree del mese 2: `(348 GiB + 11 GiB + 0.8 GiB) * $0.12 = $43.18`

Il normale costo della capacità di archiviazione Azure NetApp Files per il mese 2 si applica al volume di destinazione.

## <a name="next-steps"></a>Passaggi successivi
* [Requisiti e considerazioni per l'uso della replica tra aree](cross-region-replication-requirements-considerations.md)
* [Creazione del peering di replica](cross-region-replication-create-peering.md)
* [Visualizzare lo stato di integrità della relazione di replica](cross-region-replication-display-health-status.md)
* [Gestire il ripristino di emergenza](cross-region-replication-manage-disaster-recovery.md)
* [Metriche di replica del volume](azure-netapp-files-metrics.md#replication)
* [Risolvere i problemi di replica tra aree](troubleshoot-cross-region-replication.md)


