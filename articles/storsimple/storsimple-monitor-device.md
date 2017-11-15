---
title: Monitorare il dispositivo StorSimple | Microsoft Docs
description: "Viene descritto come utilizzare il servizio StorSimple Manager per monitorare prestazioni I/O, utilizzo della capacità, velocità effettiva della rete e prestazioni del dispositivo."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: bd4f7704-4f6f-47d0-927a-b1c91eabc453
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 0e1387bfb3487c148c8befcf3f8bf23bba35661f
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-monitor-your-storsimple-device"></a>Utilizzare il servizio StorSimple Manager per monitorare il dispositivo StorSimple
> [!NOTE]
> Il portale classico per StorSimple è deprecato. Le istanze di Gestione dispositivi StorSimple passeranno automaticamente al nuovo portale di Azure in base alla pianificazione per la funzionalità deprecata. Si riceveranno un messaggio di posta elettronica e una notifica del portale relativi a questo passaggio. Anche questo documento verrà ritirato a breve. Per la versione di questo articolo per il nuovo portale di Azure, vedere [Utilizzare il servizio StorSimple Manager per monitorare il dispositivo StorSimple](storsimple-8000-monitor-device.md). Per domande relative al passaggio, vedere [Domande frequenti: spostamento nel portale di Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Panoramica
È possibile utilizzare il servizio StorSimple Manager per monitorare  dispositivi specifici all'interno della soluzione StorSimple. È possibile creare grafici personalizzati basati su prestazioni I/O, utilizzo della capacità, velocità effettiva della rete e metriche delle prestazioni del dispositivo. 

Per visualizzare le informazioni di monitoraggio per un dispositivo specifico, nel portale di Azure classico, selezionare il servizio StorSimple Manager. Fare clic sulla scheda **Monitor** e quindi selezionare dall'elenco dei dispositivi. La pagina **Monitor** contiene le informazioni seguenti:

## <a name="io-performance"></a>Prestazione I/O
**La prestazione I/O** tiene traccia delle metriche correlate al numero di operazioni di lettura e scrittura tra le interfacce dell'iniziatore iSCSI nel server host e il dispositivo o il dispositivo e il cloud. Questa operazione può essere misurata per un volume specifico, un contenitore del volume specifico o tutti i contenitori del volume.

Il grafico seguente mostra le operazioni di I/O dall'iniziatore al dispositivo per tutti i volumi per un dispositivo di produzione. Le metriche tracciate vengono letti e scrivere byte al secondo, leggere e scrivere le operazioni dei / o al secondo, leggere e scrivere le latenze.

![Prestazioni dei / o dall'iniziatore a dispositivo](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_InitiatorTODevice_For_AllVolumesM.png)

Per lo stesso dispositivo vengono tracciate le operazioni di I/O per i dati dal dispositivo al cloud per tutti i contenitori del volume. Su questo dispositivo, i dati solo nel livello lineare e non sono stati inseriti vuoti nel cloud. Non sono presenti operazioni di lettura / scrittura che si verificano dal dispositivo al cloud. Perciò i picchi nel grafico sono a intervalli di 5 minuti che corrisponde alla frequenza con cui viene controllato l'heartbeat tra il dispositivo e il servizio. 

![Prestazioni dei / o dal dispositivo al cloud](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainersM.png)

Per lo stesso dispositivo, è stato creato uno snapshot cloud per i dati di volume partire 2:00 pm. Ciò ha restituito dati che passano dal dispositivo al cloud. Lettura-scrittura sono state soddisfatte al cloud in tale intervallo di tempo. Il grafico dei / o Mostra un picco in varie metriche corrispondente all'ora quando è stato creato lo snapshot. 

![Prestazioni dei / o per dispositivo nel cloud dopo snapshot cloud](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainers2M.png)

## <a name="capacity-utilization"></a>Utilizzo della capacità
**Utilizzo della capacità** tiene traccia delle metriche correlate alla quantità di spazio di archiviazione di dati utilizzato da volumi, contenitori del volume o dispositivo. È possibile creare report basati sull'utilizzo della capacità dell'archiviazione primaria, dell'archiviazione cloud o dell'archiviazione del dispositivo. L’utilizzo della capacità può essere misurata per un volume specifico, un contenitore del volume specifico o tutti i contenitori del volume.

La capacità primaria, del cloud e del dispositivo possono essere descritte come segue:

### <a name="primary-storage-capacity-utilization"></a>Utilizzo della capacità di archiviazione primaria
Questi grafici mostrano la quantità di dati scritti in volumi StorSimple prima che i dati vengano deduplicati e compressi. È possibile visualizzare l'utilizzo di archiviazione primaria da tutti i volumi o per un singolo volume.

Quando si visualizzano i grafici di utilizzo della capacità del volume dell’archiviazione primaria per tutti i volumi e dei singoli volumi e si riepilogano i dati primari in entrambi i casi, può esserci una mancata corrispondenza tra i due numeri. I dati primari totali in tutti i volumi possono non aggiungere la somma totale dei dati primari dei singoli volumi. Questo può essere dovuto a uno dei seguenti motivi:

* **Dati dello snapshot inclusi per tutti i volumi**: questo comportamento si verifica solo se si esegue una versione precedente all'aggiornamento 3. I dati primari visualizzati per tutti i volumi sono la somma dei dati primari per ogni volume e i dati dello snapshot. I dati primari visualizzati per un determinato volume corrisponde alla sola quantità di dati allocati sul volume (e non include i dati dello snapshot del volume corrispondente).
  
    Questo può essere spiegato dall'equazione seguente:
  
    *Dati primari (tutti i volumi) = Somma (dati primari (volume i) + dimensioni dei dati dello snapshot (volume i) )*
  
    *dove, Dati primari (volume i) = dimensioni dei dati primari allocati al volume i*
  
    Se gli snapshot vengono eliminati tramite il servizio, l'eliminazione viene eseguita in modo asincrono in background. L’aggiornamento della dimensione del volume dei dati in seguito all'eliminazione dello snapshot potrebbe richiedere del tempo. 
  
    Se si esegue l'aggiornamento 3 o versioni successive, i dati dello snapshot non sono inclusi nei dati di volume. E l'utilizzo primario viene calcolato come segue:
  
    *Dati primari (tutti i volumi) = Somma (dati primari (volume i)
  
    *dove, Dati primari (volume i) = dimensioni dei dati primari allocati al volume i*
* **Volumi con monitoraggio disabilitato incluso in tutti i volumi**: se si dispone di volumi nel dispositivo per i quali il monitoraggio è disattivato, i dati di monitoraggio per i singoli volumi non saranno disponibili nei grafici. Tuttavia, i dati per tutti i volumi nel grafico includeranno i volumi per i quali il monitoraggio è disattivato. 
* **Volumi eliminati con backup associati live inclusi per tutti i volumi**: se i volumi contenenti i dati di snapshot vengono eliminati, ma esistono ancora gli snapshot associati, è probabile che si visualizzi una mancata corrispondenza.
* **Volumi eliminati inclusi per tutti i volumi**: In alcuni casi, potrebbero essere presenti volumi precedenti anche se questi sono stati eliminati. L'effetto di eliminazione non viene visualizzato e il dispositivo potrebbe mostrare minore capacità disponibile. È necessario contattare il supporto Microsoft per rimuovere tali volumi.

I grafici seguenti mostrano l'utilizzo della capacità di archiviazione primaria di un dispositivo StorSimple prima e dopo che è stato creato uno snapshot cloud. Dato che si tratta solo dei dati del volume, uno snapshot cloud non deve modificare l'archiviazione primaria. Come si può notare, il grafico non mostra alcuna differenza nell'utilizzo capacità primario a seguito del completamento di uno snapshot nel cloud. Lo snapshot cloud è stato avviato all'incirca alle 14:00 in quel dispositivo.

![Utilizzo della capacità primario prima snapshot cloud](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes2M.png)

![Utilizzo della capacità primario dopo snapshot cloud](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes1M.png)

Se si esegue l'aggiornamento 2 o versioni successive, è possibile suddividere l'utilizzo della capacità di archiviazione primaria per un singolo volume, tutti i volumi, tutti i volumi a livelli e tutti i volumi aggiunti in locale, come illustrato di seguito. La suddivisione in base a tutti i volumi aggiunti in locale consente di verificare rapidamente la quantità di livello locale usata.

![Utilizzo della capacità primaria per tutti i volumi aggiunti in locale](./media/storsimple-monitor-device/localvolumes.png)

### <a name="cloud-storage-capacity-utilization"></a>Utilizzo della capacità di archiviazione cloud
Questi grafici mostrano la quantità di spazio di archiviazione cloud utilizzato. Questi dati sono deduplicati e compressi. Questo quantità include snapshot cloud che potrebbero contenere dati che non sono riflessi in nessun volume principale e vengono mantenuti per scopi di legacy o di memorizzazione necessari. È possibile confrontare il consumo di memoria del database primario e del cloud per avere un'idea della frequenza di riduzione dei dati, anche se il numero non sarà esatto. I grafici seguenti mostrano l'utilizzo di capacità di archiviazione cloud di un dispositivo StorSimple prima e dopo che è stato creato uno snapshot cloud. Lo snapshot cloud avviata in circa 2:00 del dispositivo ed è possibile visualizzare l'utilizzo della capacità del cloud è aumentato fino allo stesso tempo crescenti da MB 5.73 a 4.04 GB.

![Utilizzo della capacità cloud prima snapshot cloud](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers2M.png)

![Utilizzo della capacità cloud dopo snapshot cloud](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers1M.png)

### <a name="device-storage-capacity-utilization"></a>Utilizzo della capacità di archiviazione del dispositivo
Questi grafici mostrano l'utilizzo totale del dispositivo, che sarà maggiore dell’utilizzo di archiviazione primario poiché include il livello lineare di unità SSD. Questo livello contiene una quantità di dati presente anche negli altri livelli del dispositivo. La capacità del livello di unità SSD lineare è ciclica in modo che all’arrivo di nuovi dati, i dati precedenti vengono spostati nel livello HDD (e vengono deduplicati e compressi) e successivamente al cloud.

Nel corso del tempo, l’utilizzo della capacità primaria e l'utilizzo della capacità del dispositivo molto probabilmente aumenteranno contemporaneamente fino a quando i dati inizieranno ad essere archiviati a  livelli nel cloud. A questo punto, l'utilizzo della capacità del dispositivo probabilmente inizierà a stabilizzarsi ma l'utilizzo della capacità primaria aumenterà mano a mano che vengono scritti più dati.

I grafici seguenti mostrano l'utilizzo della capacità di archiviazione primaria di un dispositivo StorSimple prima e dopo che è stato creato uno snapshot cloud. Lo snapshot cloud avviato alle 2:00 pm e l'utilizzo della capacità di dispositivo avviato diminuendo in quel momento. L'utilizzo della capacità di archiviazione dispositivo si è arrestato da GB 11.58 7.48 GB. Ciò indica che probabilmente i dati non compressi nel livello SSD lineare sono deduplicati, compresso e spostati nel livello di unità disco rigido. Si noti che se il dispositivo è già presente una grande quantità di dati nei livelli sia le unità SSD e unità disco rigido, potrebbe non essere visualizzata questa riduzione. In questo esempio, il dispositivo dispone di una piccola quantità di dati.

![Utilizzo della capacità di dispositivo prima di snapshot cloud](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil2M.png)

![Utilizzo della capacità di dispositivo dopo snapshot cloud](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil1M.png)

## <a name="network-throughput"></a>Velocità effettiva della rete
**Velocità effettiva della rete** tiene traccia delle metriche correlate alla quantità di dati trasferiti dalle interfacce di rete dell'iniziatore iSCSI nel server host e nel dispositivo e tra il dispositivo e il cloud. È possibile monitorare la metrica per ognuna delle interfacce di rete iSCSI sul dispositivo.

I grafici riportati di seguito mostrano la velocità effettiva della rete per Data 0 e Data 4, entrambe interfacce di rete a 1 GbE sul dispositivo. In questo caso, dati 0 è stato abilitato al cloud mentre 4 dati è stata abilitate per iSCSI. È possibile visualizzare in ingresso sia il traffico in uscita per il dispositivo StorSimple. La linea retta nel grafico a partire dalle 15:24 è dovuta al fatto che raccogliamo i dati solo ogni 5 minuti e deve essere ignorata. 

![Velocità effettiva della rete per Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data0M.png)

![Velocità effettiva della rete per Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data4M.png)

## <a name="device-performance"></a>Prestazioni del dispositivo
**Prestazioni del dispositivo** tiene traccia delle metriche relative alle prestazioni del dispositivo. Il grafico seguente mostra le statistiche di utilizzo della CPU per un dispositivo nell'ambiente di produzione.

![Utilizzo della CPU per dispositivo](./media/storsimple-monitor-device/StorSimple_DeviceMonitor_DevicePerformance1M.png)

## <a name="next-steps"></a>Passaggi successivi
* Imparare a [utilizzare il dashboard del dispositivo del servizio StorSimple Manager](storsimple-device-dashboard.md).
* Informazioni su come [usare il servizio StorSimple Manager per gestire il dispositivo StorSimple](storsimple-manager-service-administration.md).

