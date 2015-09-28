<properties 
   pageTitle="Utilizzare il dashboard del dispositivo StorSimple Manager | Microsoft Azure"
   description="Descrive il dashboard del dispositivo del servizio StorSimple Manager e come utilizzarlo per visualizzare le metriche di archiviazione, gli iniziatori connessi e individuare il numero di serie del dispositivo e l’IQN."
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
   ms.date="09/15/2015"
   ms.author="alkohli" />

# Utilizzare il dashboard del dispositivo StorSimple Manager

## Panoramica

Il dashboard del dispositivo StorSimple Manager offre una panoramica delle informazioni per un determinato dispositivo StorSimple, a differenza del dashboard del servizio che fornisce informazioni su tutti i dispositivi inclusi nella soluzione Microsoft Azure StorSimple.

![Pagina dashboard del dispositivo](./media/storsimple-device-dashboard/HCS_DeviceDashboardPage.png)

La scheda dashboard contiene le informazioni seguenti:

- **Area del grafico**– è possibile visualizzare le metriche di archiviazione rilevanti nell'area del grafico nella parte superiore del dashboard. In questo grafico, è possibile visualizzare le metriche per l'archiviazione primaria totale (la quantità di dati scritti dall'host per il dispositivo) e l'archiviazione cloud totale utilizzata dal dispositivo in un periodo di tempo.

     In questo contesto,*archiviazione primaria*si riferisce alla quantità totale dei dati scritti dall'host. Può includere sia i dati archiviati localmente e dati a più livelli nel cloud. L’*Archiviazione cloud*d'altra parte, è una misura della quantità totale di dati archiviati nel cloud. Sono inclusi i backup e i dati a più livelli. Si noti che i dati archiviati nel cloud sono deduplicati e compressi, mentre l'archiviazione primaria indica la quantità di spazio di archiviazione utilizzato prima della loro deduplicazione e compressione.. (È possibile confrontare i due numeri per avere un'idea del tasso di compressione). Per entrambe le archiviazioni primarie e cloud, gli importi mostrati si baseranno sulla frequenza di rilevamento che si configura. Ad esempio, se si sceglie una frequenza di una settimana, il grafico mostrerà dati per ogni giorno della settimana precedente.
 
	 È possibile configurare il grafico come segue:

	 - Per visualizzare la quantità di spazio di archiviazione cloud utilizzato nel corso del tempo, selezionare l’opzione **ARCHIVIAZIONE CLOUD UTILIZZATA**. Per visualizzare l'archiviazione totale scritta dall'host, selezionare l’opzione**ARCHIVIAZIONE PRIMARIA UTILIZZATA**. Nella figura, entrambe le opzioni sono selezionate. Pertanto, il grafico mostra la quantità di archiviazione per l’archiviazione cloud e per l’archiviazione primaria. 
	 - Utilizzare il menu a discesa nell'angolo in alto a destra del grafico per specificare una scala temporale di 1 settimana, 1 mese, 3 mesi o 1 anno. Si noti che il grafico di primo livello viene aggiornato solo una volta al giorno e pertanto rifletterà i totali del giorno precedente.

     Per ulteriori informazioni, vedere [Utilizzo del servizio StorSimple Manager per monitorare il dispositivo StorSimple](storsimple-monitor-device.md).

- **Panoramica sull'utilizzo**-nell'area di **panoramica dell'utilizzo**, è possibile visualizzare la quantità di spazio di archiviazione primaria utilizzato, la quantità di archiviazione sottoposto a provisioning e la capacità di archiviazione massima per il dispositivo. Confrontando i numeri di utilizzo alla quantità massima di archiviazione disponibile, è possibile visualizzare immediatamente se è necessario ottenere memoria aggiuntiva. Si noti che questa panoramica viene aggiornata ogni 15 minuti, e a causa della differenza nella frequenza di aggiornamento, potrebbe mostrare numeri diversi da quelli visualizzati nell'area del grafico mostrato sopra, che viene aggiornato ogni giorno. Per ulteriori informazioni, vedere [Utilizzo del servizio StorSimple Manager per monitorare il dispositivo StorSimple](storsimple-monitor-device.md).


- **Avvisi**-nell'area **avvisi** viene fornita una panoramica degli avvisi per il dispositivo. Gli avvisi sono raggruppati in base alla gravità e viene fornito un conteggio del numero di avvisi a ogni livello di gravità. Cliccando sulla gravità dell'avviso si apre una scheda avvisi che mostra solo gli avvisi di tale livello di gravità per il dispositivo.

- **Processi**: l'area relativa ai **processi** mostra il risultato della recente attività di processo. Questo garantisce che il sistema funziona come previsto, o può far sapere all’utente se è necessario adottare misure correttive. Per ulteriori informazioni sui processi completati di recente, fare clic su**processi completati nelle ultime 24 ore**.

- L’area**riepilogo rapido**a destra del dashboard fornisce informazioni utili come ad esempio il modello di dispositivo, il numero di serie, stato, descrizione e numero di volumi.

È inoltre possibile configurare il failover e visualizzare gli iniziatori connessi dal dashboard del dispositivo.

Le attività comuni che possono essere eseguite in questa pagina sono:

- Visualizzare gli iniziatori connessi

- Trovare il numero di serie del dispositivo

- Trovare l’IQN di destinazione del dispositivo

## Visualizzare gli iniziatori connessi

È possibile visualizzare gli iniziatori iSCSI connessi al dispositivo facendo clic sul collegamento**Visualizza iniziatori connessi** fornito nell’area**riepilogo rapido**del dashboard del dispositivo. Questa pagina fornisce un elenco tabulare degli iniziatori connessi correttamente al dispositivo. Per ogni iniziatore, è possibile vedere:

- iSCSI Qualified Name (IQN) dell'iniziatore connesso.

- Il nome del record di controllo di accesso (ACR) che consente questo iniziatore connesso.

- L'indirizzo IP dell'iniziatore connesso.

- Le interfacce di rete a cui l'iniziatore è connesso nel dispositivo di archiviazione. Questi possono variare da DATA 0 a DATA 5.

- Tutti i volumi che l'iniziatore connesso è autorizzato ad accedere in base alla configurazione ACR corrente.

Se si visualizzano iniziatori imprevisti nell'elenco o non si visualizzano quelli previsti, verificare la configurazione ACR. Un massimo di 512 iniziatori può connettersi al dispositivo.

## Trovare il numero di serie del dispositivo

Il numero di serie del dispositivo potrebbe essere necessario quando si configura Microsoft Multipath i/o (MPIO) sul dispositivo. Eseguire i passaggi seguenti per trovare il numero di serie del dispositivo.

#### Per trovare il numero di serie del dispositivo

1. Passare a**Dispositivi**>**Dashboard**.

2. Nel riquadro a destra del dashboard, individuare l’area**riepilogo rapido**.

3. Scorrere verso il basso e individuare il numero di serie.

## Trovare l’IQN di destinazione del dispositivo

L’IQN di destinazione del dispositivo potrebbe essere necessario quando si configura il Challenge Handshake Authentication Protocol (CHAP) sul dispositivo StorSimple. Eseguire i passaggi seguenti per trovare l’IQN di destinazione del dispositivo.

### Per trovare l’IQN di destinazione del dispositivo

1. Passare a**Dispositivi**>**Dashboard**.

1. Nel riquadro a destra del dashboard, individuare l’area**riepilogo rapido**.

1. Scorrere verso il basso e individuare l’IQN di destinazione.

## Passaggi successivi

- Ulteriori informazioni sul [dashboard del servizio StorSimple Manager](storsimple-service-dashboard.md).
- Ulteriori informazioni sull’[utilizzo del servizio StorSimple Manager per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=Sept15_HO3-->