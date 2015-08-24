<properties 
   pageTitle="Utilizzare il servizio StorSimple Manager per monitorare il dispositivo StorSimple | Microsoft Azure"
   description="Viene descritto come utilizzare il servizio StorSimple Manager per monitorare prestazioni I/O, utilizzo della capacità, velocità effettiva della rete e prestazioni del dispositivo."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/10/2015"
   ms.author="alkohli" />

# Utilizzare il servizio StorSimple Manager per monitorare il dispositivo StorSimple 

## Panoramica

È possibile utilizzare il servizio StorSimple Manager per monitorare dispositivi specifici all'interno della soluzione StorSimple. È possibile creare grafici personalizzati basati su prestazioni I/O, utilizzo della capacità, velocità effettiva della rete e metriche delle prestazioni del dispositivo.

Per visualizzare le informazioni di monitoraggio per un dispositivo specifico, nel portale di gestione, selezionare il servizio StorSimple Manager, fare clic sulla scheda**Monitor** e quindi selezionare dall'elenco dei dispositivi. La pagina **Monitor** contiene le informazioni seguenti:

## Prestazione I/O 

**La prestazione I/O**tiene traccia delle metriche correlate al numero di operazioni di lettura e scrittura tra le interfacce dell'iniziatore iSCSI nel server host e il dispositivo o il dispositivo e il cloud. Questa operazione può essere misurata per un volume specifico, un contenitore del volume specifico o tutti i contenitori del volume.

## Utilizzo della capacità 

**Utilizzo della capacità**tiene traccia delle metriche correlate alla quantità di spazio di archiviazione di dati utilizzato da volumi, contenitori del volume o dispositivo. È possibile creare report basati sull'utilizzo della capacità dell'archiviazione primaria, dell'archiviazione cloud o dell'archiviazione del dispositivo. L’utilizzo della capacità può essere misurata per un volume specifico, un contenitore del volume specifico o tutti i contenitori del volume.

- **Utilizzo della capacità di archiviazione primaria**mostra la quantità di dati scritti in volumi StorSimple prima che i dati vengano deduplicati e comprimessi.

- **Utilizzo della capacità di archiviazione del cloud**mostra la quantità di spazio di archiviazione cloud utilizzato. Questi dati sono deduplicati e compressi. Questo quantità include snapshot cloud che potrebbero contenere dati che non sono riflessi in nessun volume principale e vengono mantenuti per scopi di legacy o di memorizzazione necessari. È possibile confrontare il consumo di memoria del database primario e del cloud per avere un'idea della frequenza di riduzione dei dati, anche se il numero non sarà esatto.

- **Utilizzo della capacità di archiviazione dispositivo**mostra l'utilizzo totale del dispositivo, che sarà maggiore dell’utilizzo di archiviazione primario poiché include il livello lineare di unità SSD. Questo livello contiene una quantità di dati presente anche negli altri livelli del dispositivo. La capacità del livello di unità SSD lineare è ciclica in modo che all’arrivo di nuovi dati, i dati precedenti vengono spostati nel cloud (e vengono deduplicati e compressi).

Nel corso del tempo, l’utilizzo della capacità primaria e l'utilizzo della capacità del dispositivo molto probabilmente aumenteranno contemporaneamente fino a quando i dati inizieranno ad essere archiviati a livelli nel cloud. A questo punto, l'utilizzo della capacità del dispositivo probabilmente inizierà a stabilizzarsi ma l'utilizzo della capacità primaria aumenterà mano a mano che vengono scritti più dati.

## Velocità effettiva della rete

**Velocità effettiva della rete**tiene traccia delle metriche correlate alla quantità di dati trasferiti dalle interfacce di rete dell'iniziatore iSCSI nel server host e nel dispositivo e tra il dispositivo e il cloud. È possibile monitorare la metrica per ognuna delle interfacce di rete iSCSI sul dispositivo.

## Prestazioni del dispositivo 

**Prestazioni del dispositivo**tiene traccia delle metriche relative alle prestazioni del dispositivo.

## Passaggi successivi

[Imparare a utilizzare il dashboard del dispositivo del servizio StorSimple Manager](storsimple-device-dashboard.md).

<!---HONumber=August15_HO7-->