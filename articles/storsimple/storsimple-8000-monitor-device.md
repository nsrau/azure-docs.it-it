---
title: Monitorare il dispositivo StorSimple serie 8000 | Microsoft Docs
description: "Viene descritto come usare il servizio Gestione dispositivi di StorSimple per monitorare uso, prestazioni I/O e uso della capacità."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/02/2017
ms.author: alkohli
ms.openlocfilehash: c8f731502d6589bfa908aa26cf418a65b18be635
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>Usare il servizio Gestione dispositivi di StorSimple per monitorare il dispositivo StorSimple
## <a name="overview"></a>Panoramica
È possibile usare il servizio Gestione dispositivi di StorSimple per monitorare dispositivi specifici all'interno della soluzione StorSimple. È possibile creare grafici personalizzati basati su prestazioni I/O, uso della capacità, velocità effettiva della rete e metriche delle prestazioni del dispositivo, per poi aggiungerli al dashboard. Per altre informazioni, vedere [customize your portal dashboard](../azure-portal/azure-portal-dashboards.md) (personalizzare il dashboard del portale).

Per visualizzare le informazioni di monitoraggio per un dispositivo specifico, nel portale di Azure selezionare il servizio Gestione dispositivi di StorSimple. Selezionare il dispositivo dall'elenco dei dispositivi e passare a **Monitoraggio**. Sarà quindi possibile visualizzare i grafici **Capacità**, **Utilizzo** e **Prestazioni** per il dispositivo selezionato.

## <a name="capacity"></a>Capacità
Il grafico **Capacità** tiene traccia dello spazio disponibile e dello spazio restante sul dispositivo. La capacità residua viene quindi visualizzata come aggiunta in locale o a livelli.

La capacità fornita e quella residua sono ulteriormente suddivise in volumi aggiunti in locale e a più livelli. Per ogni volume vengono visualizzati la capacità di cui è stato eseguito il provisioning e la capacità residua nel dispositivo.

![Capacità I/O](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Utilizzo
**Utilizzo** tiene traccia delle metriche correlate alla quantità di spazio di archiviazione di dati usato da volumi, contenitori del volume o dispositivo. È possibile creare report basati sull'utilizzo della capacità dell'archiviazione primaria, dell'archiviazione cloud o dell'archiviazione del dispositivo. L’utilizzo della capacità può essere misurata per un volume specifico, un contenitore del volume specifico o tutti i contenitori del volume.
Per impostazione predefinita, viene segnalato l'uso nelle ultime 24 ore. Per modificare l'intervallo di calcolo dell'uso nel grafico, effettuare una selezione tra:
* Ultime 24 ore
* Ultimi 7 giorni
* Ultimi 30 giorni
* Ultimi 90 giorni
* Ultimo anno


La capacità primaria, del cloud e di archiviazione locale usate possono essere descritte come segue:

### <a name="primary-storage-usage"></a>Uso delle risorse di archiviazione primarie
Questi grafici mostrano la quantità di dati scritti in volumi StorSimple prima che i dati vengano deduplicati e compressi. È possibile visualizzare l'archiviazione primaria usata da tutti i volumi in un contenitore di volumi o per singolo volume. L'archiviazione primaria usata è ulteriormente suddivisa tra archiviazione a livelli primaria e archiviazione aggiunta in locale usate.

I grafici seguenti mostrano l'uso della capacità di archiviazione primaria di un dispositivo StorSimple prima e dopo la creazione di uno snapshot cloud. Dato che si tratta solo dei dati del volume, uno snapshot cloud non deve modificare l'archiviazione primaria. Come si può notare, il grafico non mostra alcuna differenza nell'uso dell'archiviazione primaria a livelli o aggiunta in locale dopo la creazione di uno snapshot nel cloud. Lo snapshot cloud è stato avviato all'incirca alle 11:50 in quel dispositivo.

![Utilizzo della capacità primario dopo snapshot cloud](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

Eseguendo ora I/O nell'host connesso al dispositivo StorSimple verrà visualizzato un aumento dell'archiviazione primaria a livelli o aggiunta in locale usata in base ai volumi (a livelli o aggiunti in locale) su cui i dati vengono scritti. Di seguito sono riportati i grafici relativi all'uso dell'archiviazione primaria per un dispositivo StorSimple. Su questo dispositivo, l'host di StorSimple ha avviato la scrittura su un volume a livelli nel dispositivo alle ore 14:30 circa. È possibile visualizzare il picco di byte/s in scrittura corrispondenti all'I/O in esecuzione nell'host.

![Prestazioni durante l'esecuzione I/O su volumi a livelli](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Esaminando l'archiviazione a livelli primaria usata si noterà che è aumentata, mentre l'uso dell'archiviazione primaria aggiunta in locale è rimasto invariato poiché non sono presenti scritture per i volumi aggiunti in locale nel dispositivo.

![Uso della capacità primaria con I/O eseguiti in volumi a più livelli](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Se si esegue l'aggiornamento 3 o versioni successive, è possibile suddividere l'uso della capacità di archiviazione primaria per un singolo volume, tutti i volumi, tutti i volumi a livelli e tutti i volumi aggiunti in locale, come illustrato di seguito. La suddivisione in base a tutti i volumi aggiunti in locale consente di verificare rapidamente la quantità di livello locale usata.

![Uso della capacità primaria per tutti i volumi a più livelli](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Utilizzo della capacità primaria per tutti i volumi aggiunti in locale](./media/storsimple-8000-monitor-device/monitor-usage4.png)

È inoltre possibile fare clic su ogni volume in elenco e verificare l'uso corrispondente.

![Utilizzo della capacità primaria per tutti i volumi aggiunti in locale](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Uso dell'archiviazione cloud
Questi grafici mostrano la quantità di spazio di archiviazione cloud utilizzato. Questi dati sono deduplicati e compressi. Questo quantità include snapshot cloud che potrebbero contenere dati che non sono riflessi in nessun volume principale e vengono mantenuti per scopi di legacy o di memorizzazione necessari. È possibile confrontare il consumo di memoria del database primario e del cloud per avere un'idea della frequenza di riduzione dei dati, anche se il numero non sarà esatto.

I grafici seguenti mostrano l'uso di archiviazione cloud di un dispositivo StorSimple dopo la creazione di uno snapshot cloud.

* Lo snapshot cloud sul dispositivo è iniziato alle 11:50 circa ed è possibile vedere che, prima dello snapshot cloud, non era usata alcuna archiviazione cloud. 
* Una volta completato lo snapshot cloud, l'uso di archiviazione cloud è di 0,89 GB. 
* Durante l'esecuzione dello snapshot cloud è inoltre visibile un picco corrispondente nell'I/O dal dispositivo al cloud.

    ![Uso dell'archiviazione cloud prima dello snapshot cloud](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Uso dell'archiviazione cloud dopo lo snapshot cloud](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![I/O dal dispositivo al cloud durante uno snapshot cloud](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Uso dell'archiviazione locale
I grafici mostrano l'uso totale per il dispositivo, che sarà maggiore dell'uso di archiviazione primaria poiché include il livello lineare di unità SSD. Questo livello contiene una quantità di dati presente anche negli altri livelli del dispositivo. La capacità del livello di unità SSD lineare è ciclica in modo che all’arrivo di nuovi dati, i dati precedenti vengono spostati nel livello HDD (e vengono deduplicati e compressi) e successivamente al cloud.

Nel corso del tempo, l'uso dell'archiviazione primaria e l'uso dell'archiviazione locale molto probabilmente aumenteranno contemporaneamente fino a quando i dati inizieranno ad essere archiviati a livelli nel cloud. A questo punto, l'uso dell'archiviazione locale probabilmente inizierà a stabilizzarsi, mentre l'uso dell'archiviazione primaria aumenterà mano a mano che vengono scritti più dati.

I grafici seguenti mostrano l'uso della capacità di archiviazione primaria di un dispositivo StorSimple durante la creazione di uno snapshot cloud. Lo snapshot cloud è stato avviato alle 11:50 e l'uso dell'archiviazione locale è diminuito in quel momento. L'archiviazione locale usata è diminuita da 1.445 GB a 1.09 GB. Ciò indica che probabilmente i dati non compressi nel livello SSD lineare sono deduplicati, compresso e spostati nel livello di unità disco rigido. Si noti che se il dispositivo è già presente una grande quantità di dati nei livelli sia le unità SSD e unità disco rigido, potrebbe non essere visualizzata questa riduzione. In questo esempio, il dispositivo dispone di una piccola quantità di dati.

![Uso dell'archiviazione locale dopo lo snapshot cloud](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Prestazioni
**Prestazioni** tiene traccia delle metriche correlate al numero di operazioni di lettura e scrittura tra le interfacce dell'iniziatore iSCSI nel server host e il dispositivo o il dispositivo e il cloud. Questa operazione può essere misurata per un volume specifico, un contenitore del volume specifico o tutti i contenitori del volume. Il grafico Prestazioni include anche l'uso della CPU e la velocità effettiva della rete per le diverse interfacce di rete presenti nel dispositivo.

### <a name="io-performance-for-initiator-to-device"></a>Prestazioni I/O da iniziatore a dispositivo
Il grafico seguente mostra le operazioni di I/O dall'iniziatore al dispositivo per tutti i volumi per un dispositivo di produzione. Le metriche tracciate indicano valori di lettura e scrittura in byte al secondo. È possibile anche visualizzare un grafico I/O di lettura, scrittura e in attesa o delle latenze di lettura e scrittura.

![Prestazioni I/O da iniziatore a dispositivo](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>Prestazioni I/O dal dispositivo al cloud
Per lo stesso dispositivo vengono tracciate le operazioni di I/O per i dati dal dispositivo al cloud per tutti i contenitori del volume. Su questo dispositivo, i dati solo nel livello lineare e non sono stati inseriti vuoti nel cloud. Non sono presenti operazioni di lettura / scrittura che si verificano dal dispositivo al cloud. Perciò i picchi nel grafico sono a intervalli di 5 minuti che corrisponde alla frequenza con cui viene controllato l'heartbeat tra il dispositivo e il servizio.

Per lo stesso dispositivo, è stato creato uno snapshot cloud per i dati di volume con avvio alle 11:50. Ciò ha restituito dati che passano dal dispositivo al cloud. Le operazioni di scrittura sono state soddisfatte al cloud in tale intervallo di tempo. Il grafico I/O mostra un picco nei byte/s di scrittura, corrispondente all'ora in cui è stato creato lo snapshot.

![I/O dal dispositivo al cloud durante uno snapshot cloud](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Velocità effettiva della rete per le interfacce di rete del dispositivo
**Velocità effettiva della rete** tiene traccia delle metriche correlate alla quantità di dati trasferiti dalle interfacce di rete dell'iniziatore iSCSI nel server host e nel dispositivo e tra il dispositivo e il cloud. È possibile monitorare la metrica per ognuna delle interfacce di rete iSCSI sul dispositivo.

I grafici seguenti mostrano la velocità effettiva di rete per la rete Data 0, 1 1 GbE sul dispositivo, abilitata sia per il cloud (impostazione predefinita) che per iSCSI. In questo dispositivo, il 14 giugno alle ore 9 circa i dati sono stati archiviati a livelli nel cloud (alla stessa ora non è stato creato alcuno snapshot cloud che facesse riferimento al tiering come meccanismo di migrazione dei dati nel cloud), portando all'abilitazione I/O nel cloud. È presente un picco corrispondente nel grafico della velocità effettiva di rete per la stessa ora e la maggior parte del traffico di rete è in uscita nel cloud.

![Velocità effettiva della rete per Data 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Esaminando il grafico della velocità effettiva dell'interfaccia Data 1, un'altra interfaccia di rete da 1 GbE abilitata solo per iSCSI, non si rileva praticamente alcun traffico di rete durante questo intervallo.

![Velocità effettiva della rete per Data 1](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>Utilizzo della CPU per dispositivo
**Uso della CPU** tiene traccia delle metriche relative alla CPU usata nel dispositivo. Il grafico seguente mostra le statistiche di utilizzo della CPU per un dispositivo nell'ambiente di produzione.

![Utilizzo della CPU per dispositivo](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Passaggi successivi
* Imparare a [usare il dashboard dispositivo del servizio Gestione dispositivi StorSimple](storsimple-device-dashboard.md).
* Informazioni su come [usare il servizio Gestione dispositivi StorSimple per gestire il dispositivo StorSimple](storsimple-manager-service-administration.md).

