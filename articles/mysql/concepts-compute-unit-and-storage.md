---
title: "Spiegazione delle unità di calcolo e delle unità di archiviazione del database di Azure per MySQL | Documentazione Microsoft"
description: "Spiega l&quot;unità di calcolo e l&quot;unità di archiviazione e ciò che accade quando si raggiunge l&quot;unità di calcolo o l&quot;unità di archiviazione massima."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql - database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2c10253698864b5e1964fbf15b3484752a551c3b
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---
# <a name="explaining-compute-unit-and-storage-unit"></a>Spiegazione dell'unità di calcolo e dell'unità di archiviazione
Questo articolo spiega i concetti dell'unità di calcolo e dell'unità di archiviazione e ciò che accade quando si raggiunge l'unità di calcolo o l'unità di archiviazione massima.

## <a name="what-are-compute-units"></a>Cosa sono le unità di calcolo?
Le unità di calcolo sono una misura della velocità effettiva di elaborazione della CPU garantita come disponibile per un singolo database di Azure per il server MySQL. Un'unità di calcolo è una misura combinata di risorse di CPU e memoria. In generale, 50 unità di calcolo equivalgono a metà memoria centrale, 100 unità di calcolo equivalgono a una memoria centrale e 2000 unità di calcolo equivalgono a 20 memorie centrali di velocità effettiva dell'elabprazione disponibile per il server.

La quantità di memoria per ogni unità di calcolo è ottimizzata per i livelli di servizio Basic, Standard e Premium. Raddoppiare le unità di calcolo aumentando il livello di prestazioni equivale a raddoppiare il set di risorse disponibili per quel singolo database di Azure per MySQL

Ad esempio, 2000 unità di calcolo Standard offrono una velocità effettiva della CPU e una memoria maggiori di 20 volte rispetto a 100 unità di calcolo Standard. Tuttavia, mentre 100 unità di calcolo Standard offrono la stessa velocità effettiva della CPU rispetto a 100 unità di calcolo Basic, la quantità di memoria che è già configurata nel livello Standard è il doppio della quantità di memoria configurata per il livello Basic e pertanto offre migliori prestazioni del carico di lavoro e latenza delle transazioni.

>[!IMPORTANT]
>Per una velocità effettiva delle prestazioni del carico di lavoro prevedibile e una concorrenza elevata degli utenti, si consiglia di scegliere il livello di servizio Standard.

È possibile modificare i [livelli di servizio](./concepts-service-tiers.md) in qualsiasi momento praticamente senza tempi di inattività dell'applicazione. Per molte aziende e app, la possibilità di crearne database uno-a-molti all'interno di ogni singolo database di Azure per il server MySQL e regolare le prestazioni verso l'alto o verso il basso su richiesta offre la flessibilità necessaria per gestire i costi.

>[!IMPORTANT]
>Attualmente supportiamo livelli di prestazioni con scalabilità verso l'alto o verso il basso all'interno di un livello di servizio. Ad esempio è possibile aumentare da 100 unità di calcolo Standard a 400 unità di calcolo Standard. Analogamente è possibile ridurre da 400 unità di calcolo Standard a 100 unità di calcolo Standard. In futuro sarà possibile anche applicare la scalabilità verso l'alto o verso il basso tra i livelli di servizio, ad esempio tra il livello Basic e il livello Standard.

## <a name="what-are-storage-units"></a>Cosa sono le unità di archiviazione?
Le unità di archiviazione sono una misura della capacità di archiviazione con provisioning che viene garantita come disponibile per un singolo database di Azure per il server MySQL. Ogni livello di servizio ha una quantità fissa di spazio di archiviazione che è inclusa nel prezzo del livello di servizio selezionato.

Facoltativamente, le unità di archiviazione possono essere ridimensionate indipendentemente dalle unità di calcolo, con incrementi di 125 GB, fino al massimo consentito di archiviazione, come descritto nella tabella seguente.

| **Funzionalità del livello di servizio** | **Basic** | **Standard** | **Premium\*** |
|---------------------------|-----------|--------------|---------------|
| Unità di archiviazione incluse | 50 GB | 125 GB | 250 GB |
| Archiviazione massima totale | 1050 GB | 10000 GB | 4000 GB |
| Garanzia di archiviazione IOPS | N/D  | Sì | Sì |
| Archiviazione IOPS massima | N/D  | 30.000 | 40.000 |

I livelli di servizio Standard e Premium forniscono inoltre garanzia IOPS con provisioning. La quantità di IOPS con provisioning disponibile dipende dal livello di servizio e dalla capacità di archiviazione configurata. Per i server distribuiti nel livello Standard, il numero di IOPS è fissato a 3 volte l'archiviazione di provisioning. 

Ad esempio, se si dispone di 125 GB di archiviazione, sono disponibili 375 IOPS con provisioning per il server. Il livello Premium offre una latenza molto bassa e spazio di archiviazione IOPS elevato. Per i server distribuiti nel livello Premium, il numero di IOPS con provisioning a bassa latenza può scalare tra cinque e dieci volte l'archiviazione con provisioning.

>[!IMPORTANT]
>Se il carico di lavoro ha un collo di bottiglia nelle unità di calcolo configurate, non sarà possibile sfruttare pienamente gli IOPS con provisioning disponibili. È consigliabile monitorare le unità di calcolo e considerare il ridimensionamento delle unità di calcolo se non si riesce a sfruttare al meglio gli IOPS con provisioning disponibili.

Scalare le unità di archiviazione incrementando lo spazio di archiviazione con provisioning equivale ad aumentare proporzionalmente gli IOPS con provisioning per i livelli Standard e Premium.

>[!IMPORTANT]
>Il livello Basic non fornisce la garanzia IOPS.

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>Come si determina il numero di unità di calcolo necessarie per il carico di lavoro?
Se si sta cercando di eseguire la migrazione di un database esistente locale o MySQL in esecuzione in una macchina virtuale, è possibile determinare il numero di unità di calcolo stimando quante memorie centrali di velocità effettiva di elaborazione sono richieste dal carico di lavoro. 

Se la macchina virtuale o locale sta attualmente utilizzando 4 memorie centrali (senza contare l'hyperthread CPU), è possibile iniziare configurando 400 unità di calcolo per il database di Azure per MySQL. Le unità di calcolo possono essere aumentate o ridotte in modo dinamico in base alle esigenze del carico di lavoro praticamente senza tempi di inattività dell'applicazione. È possibile anche monitorare il consumo delle unità di calcolo tramite il portale o l'interfaccia della riga di comando per dimensionare nel modo corretto le risorse per il server MySQL.

## <a name="how-can-i-determine-the-number-of-storage-units-needed-for-my-workload"></a>Come si determina il numero di unità di archiviazione necessarie per il carico di lavoro?
È possibile stimare la quantità di unità di archiviazione determinando innanzitutto la quantità di capacità di archiviazione necessaria. I livelli Basic, Standard e Premium hanno uno spazio di archiviazione incluso nello SKU.

Il secondo fattore importante è determinare il numero di IOPS richiesto. Il livello Basic fornisce un numero di IOPS variabile senza garanzia. Il livello Standard scala secondo un rapporto fisso di tre IOPS per ogni GB di spazio di archiviazione e fornisce la garanzia IOPS. È possibile anche monitorare il consumo di IOPS con provisioning tramite il portale o l'interfaccia della riga di comando per determinare l'utilizzo.

>[!IMPORTANT]
>Una volta eseguito il provisioning delle unità di archiviazione non possono più essere ridotte dinamicamente.

## <a name="what-happens-when-i-hit-my-maximum-compute-units-andor-storage-units"></a>Cosa accade quando si raggiunge il numero massimo di unità di calcolo e/o di unità di archiviazione?
I livelli di prestazioni vengono calibrati e gestiti per offrire le risorse necessarie per eseguire il carico di lavoro del database fino ai limiti massimi consentiti per il livello di servizio/livello di prestazioni selezionato.

Se il carico di lavoro raggiunge uno dei limiti di unità di calcolo/IOPS con provisioning, si continuerà a ricevere le risorse al massimo livello consentito, ma probabilmente si noterà un aumento delle latenze per le query. Con questi limiti non si verificheranno errori, ma solo un rallentamento nel carico di lavoro, a meno che il rallentamento non diventi così grave da provocare il timeout delle query.

Se si raggiungono i limiti del numero massimo di connessioni consentito, vengono visualizzati errori espliciti. Per altre informazioni sul limite delle risorse, vedere [Limiti delle risorse del database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits). <Need to write about the behavior if a user reaches the storage capacity limits>

## <a name="next-steps"></a>Passaggi successivi
[Livelli di servizio del database di Azure per MySQL](./concepts-service-tiers.md) 

