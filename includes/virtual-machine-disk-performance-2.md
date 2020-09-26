---
title: includere file
description: includere file
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 8882625d28871135223dd30e3fd96a385a13e8fe
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377087"
---
![Documentazione di Dsv3](media/vm-disk-performance/dsv3-documentation.jpg)

Il numero massimo di velocità effettiva del disco non **memorizzato nella cache** è il limite massimo di archiviazione predefinito che la macchina virtuale è in grado di gestire. Il limite massimo di velocità effettiva di archiviazione **memorizzata nella cache** è un limite separato quando si Abilita la memorizzazione nella cache dell'host. L'abilitazione della memorizzazione nella cache dell'host può essere eseguita durante la creazione della macchina virtuale e il fissaggio dei dischi. È anche possibile modificare per attivare e disattivare la memorizzazione nella cache dell'host dei dischi in una macchina virtuale esistente:

![Memorizzazione nella cache dell'host](media/vm-disk-performance/host-caching.jpg)

La memorizzazione nella cache dell'host può essere regolata in base ai requisiti del carico di lavoro per ogni disco. È possibile impostare la memorizzazione nella cache dell'host in modo che sia di sola lettura per i carichi di lavoro che eseguono solo operazioni di lettura e di lettura/scrittura per i carichi di lavoro che eseguono il bilanciamento delle operazioni di lettura e scrittura. Se il carico di lavoro non segue uno di questi modelli, sfortunatamente non sarà possibile usare la memorizzazione nella cache dell'host. 

Procedere con un esempio con la macchina virtuale Standard_D8s_v3. Ad eccezione di questa volta, si Abilita la memorizzazione nella cache dell'host sui dischi e ora il limite di IOPS della VM è 16.000 IOPS. Collegato alla macchina virtuale si trovano tre dischi P30 sottostanti che possono gestire 5.000 IOPS.

Configurazione:
- Standard_D8s_v3 
    - IOPS memorizzati nella cache: 16.000
    - IOPS non memorizzati nella cache: 12.800
- Disco del sistema operativo P30 
    - IOPS: 5.000
    - Memorizzazione nella cache dell'host abilitata 
- 2 dischi dati P30
    - IOPS: 5.000
    - Memorizzazione nella cache dell'host abilitata

![Esempio di caching dell'host](media/vm-disk-performance/host-caching-example-without-remote.jpg)

A questo punto, l'applicazione che usa questa Standard_D8s_v3 macchina virtuale con Caching abilitato esegue una richiesta per 15.000 IOPS. Tali richieste vengono suddivise come 5.000 IOPS a ogni disco sottostante collegato e non si verifica alcuna riduzione delle prestazioni.

## <a name="combined-uncached-and-cached-limits"></a>Limiti combinati non memorizzati nella cache e memorizzati nella cache

I limiti di memorizzazione nella cache di una macchina virtuale sono separati dai limiti non memorizzati nella cache. Ciò significa che è possibile abilitare la memorizzazione nella cache dell'host sui dischi collegati a una macchina virtuale, ma anche non abilitare la memorizzazione nella cache dell'host su altri dischi per consentire alle macchine virtuali di ottenere un totale di i/o di archiviazione del limite memorizzato nella cache, oltre al limite non memorizzato nella cache. Di seguito viene illustrato un esempio che consente di consolidare il modo in cui questi limiti funzionano insieme e si continuerà con la configurazione della macchina virtuale Standard_D8s_v3 e dei dischi Premium.

Configurazione:
- Standard_D8s_v3 
    - IOPS memorizzati nella cache: 16.000
    - IOPS non memorizzati nella cache: 12.800
- Disco del sistema operativo P30 
    - IOPS: 5.000
    - Memorizzazione nella cache dell'host abilitata 
- 2 dischi dati P30 X 2
    - IOPS: 5.000
    - Memorizzazione nella cache dell'host abilitata
- 2 dischi dati P30 X 2
    - IOPS: 5.000
    - Caching dell'host disabilitato

![Esempio di memorizzazione nella cache dell'host con archiviazione remota](media/vm-disk-performance/host-caching-example-with-remote.jpg)

A questo punto, l'applicazione in esecuzione in Standard_D8s_v3 macchina virtuale con effettua una richiesta di 25.000 IOPS. Questa richiesta viene suddivisa come 5.000 IOPS per ogni disco sottostante collegato dove 3 di questi dischi usano la memorizzazione nella cache dell'host e 2 dischi non lo sono. Poiché i 3 che usano la memorizzazione nella cache dell'host si trovano entro i limiti memorizzati nella cache di 16.000, le richieste vengono completate correttamente e non si verifica alcuna riduzione delle prestazioni di archiviazione. Inoltre, poiché i 2 dischi che non usano la memorizzazione nella cache dell'host si trovano entro i limiti non memorizzati nella cache di 12.800, anche queste richieste vengono completate e non si verifica alcuna limitazione.

## <a name="metrics-for-disk-performance"></a>Metriche per le prestazioni del disco
In Azure sono disponibili metriche che forniscono informazioni dettagliate sulle prestazioni delle macchine virtuali e dei dischi. Queste metriche possono essere visualizzate visivamente tramite il portale di Azure oppure possono essere recuperate tramite una chiamata API. Le metriche vengono calcolate in base a intervalli di un minuto. Sono disponibili le metriche seguenti per ottenere informazioni sulle prestazioni di i/o su disco e VM:
- **Profondità coda del disco del sistema operativo** : numero di richieste di i/o in attesa correnti in attesa di lettura o scrittura nel disco del sistema operativo.
- **Byte letti da disco del sistema operativo/sec** : numero di byte letti in un secondo dal disco del sistema operativo.
- **Operazioni di lettura disco del sistema operativo/sec** : numero di operazioni di input lette in un secondo dal disco del sistema operativo.
- **Byte di scrittura disco del sistema operativo/sec** : numero di byte scritti in un secondo dal disco del sistema operativo.
- **Operazioni di scrittura disco del sistema operativo/sec** : numero di operazioni di output scritte in un secondo dal disco del sistema operativo.
- **Profondità della coda del disco dati** : numero di richieste di i/o in attesa correnti in attesa di lettura o scrittura nei dischi dati.
- **Byte di lettura disco dati/sec** : numero di byte letti in un secondo dai dischi dati.
- **Operazioni di lettura disco dati/sec** : numero di operazioni di input lette in un secondo da uno o più dischi dati.
- **Byte scrittura disco dati/sec** : numero di byte scritti in un secondo dai dischi dati.
- **Operazioni di scrittura disco dati/sec** : numero di operazioni di output scritte in un secondo da uno o più dischi dati.
- **Byte letti da disco/sec** : numero totale di byte letti in un secondo da tutti i dischi collegati a una macchina virtuale.
- **Operazioni di lettura disco/sec** : numero di operazioni di input lette in un secondo da tutti i dischi collegati a una macchina virtuale.
- **Byte scritti su disco/sec** : numero di byte scritti in un secondo da tutti i dischi collegati a una macchina virtuale.
- **Operazioni di scrittura su disco/sec** : numero di operazioni di output scritte in un secondo da tutti i dischi collegati a una macchina virtuale.

## <a name="storage-io-utilization-metrics"></a>Metriche di utilizzo IO di archiviazione
Metriche che consentono di diagnosticare la limitazione di i/o del disco:
- **Percentuale di IOPS del disco dati utilizzata** : percentuale calcolata dal IOPS del disco dati completata rispetto al IOPS del disco dati sottoposti a provisioning. Se questa quantità è pari al 100%, l'applicazione in esecuzione sarà IO limitata dal limite IOPS del disco dati.
- Percentuale di utilizzo della **larghezza di banda del disco dati** : percentuale calcolata dalla velocità effettiva del disco dati completata sulla velocità effettiva del disco dati sottoposta a provisioning. Se questa quantità è pari al 100%, l'applicazione in esecuzione sarà IO limitata dal limite di larghezza di banda del disco dati.
- **Percentuale di IOPS** del disco del sistema operativo: percentuale calcolata dall'IOPS del disco del sistema operativo completata rispetto al IOPS del disco del sistema operativo con provisioning. Se questa quantità è pari al 100%, l'applicazione in esecuzione sarà IO limitata dal limite IOPS del disco del sistema operativo.
- **Percentuale** di utilizzo della larghezza di banda del disco del sistema operativo: percentuale calcolata dalla velocità effettiva del disco del sistema operativo completata sulla velocità effettiva del disco del sistema operativo. Se questa quantità è pari al 100%, l'applicazione in esecuzione sarà IO limitata dal limite di larghezza di banda del disco del sistema operativo.

Metriche che consentono di diagnosticare la limitazione delle operazioni di i/o della macchina virtuale:
- **Percentuale di IOPS utilizzata nella cache della VM** : percentuale calcolata in base al numero totale di IOPS completati sul limite massimo di IOPS della macchina virtuale memorizzato nella cache. Se questa quantità è pari al 100%, l'applicazione in esecuzione sarà IO limitata dal limite di IOPs memorizzati nella cache della VM.
- Percentuale di utilizzo della **larghezza di banda della VM memorizzata nella** cache: percentuale calcolata in base alla velocità effettiva totale del disco completata sulla velocità effettiva massima della macchina virtuale Se questa quantità è pari al 100%, l'applicazione in esecuzione sarà IO limitata dal limite di larghezza di banda memorizzato nella cache della macchina virtuale.
- Percentuale di IOPS di **VM non memorizzata nella cache** : la percentuale calcolata in base al numero totale di IOPS in una macchina virtuale è stata completata sul limite massimo di IOPS della macchina virtuale non memorizzato nella cache. Se questa quantità è pari al 100%, l'applicazione in esecuzione sarà IO limitata dal limite di IOPs non memorizzato nella cache della macchina virtuale.
- Percentuale di utilizzo della **larghezza di banda della VM non memorizzata nella cache** : percentuale calcolata in base alla velocità effettiva totale del disco in una macchina virtuale completata tramite la velocità effettiva massima di provisioning della macchina virtuale. Se questa quantità è pari al 100%, l'applicazione in esecuzione sarà IO limitata dal limite di larghezza di banda non memorizzato nella cache della macchina virtuale.