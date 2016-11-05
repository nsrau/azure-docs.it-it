---
title: Configurare la funzionalità MPIO nell'host dell'array virtuale StorSimple | Microsoft Docs
description: L'articolo descrive come configurare Multipath I/O (MPIO) per l'array virtuale StorSimple connesso a un host che esegue Windows Server 2012 R2.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/20/2016
ms.author: alkohli

---
# Configurare Multipath I/O in host Windows Server per l'array virtuale StorSimple
## Panoramica
In questo articolo viene descritto come installare la funzionalità Multipath I/O (MPIO) nell'host Windows Server, applicare impostazioni di configurazione specifiche per i volumi StorSimple e infine verificare MPIO per i volumi StorSimple. Nella procedura si presuppone che l'array virtuale 1200 StorSimple con due interfacce di rete sia connesso a un host Windows Server con altrettante interfacce di rete. Le informazioni contenute in questo articolo si applicano solo all'array virtuale. Per informazioni su dispositivi StorSimple serie 8000, consultare la [configurazione MPIO per host StorSimple](storsimple-configure-mpio-windows-server.md).

La funzionalità MPIO in Windows Server consente configurazioni di archiviazione a disponibilità elevata e a tolleranza di errore. MPIO utilizza componenti di percorso fisico ridondanti, adattatori, cavi e interruttori, per creare percorsi logici tra il server e il dispositivo di archiviazione. Se si verifica un errore del componente, che causa l’errore di un percorso logico, la logica dei percorsi multipli utilizza un percorso alternativo per l’I/O, in modo che le applicazioni possano continuare ad accedere ai dati. Inoltre, in base alla configurazione, MPIO può anche migliorare le prestazioni bilanciando nuovamente il carico tra tutti questi percorsi. Per ulteriori informazioni, vedere [Panoramica di Multipath I/O](https://technet.microsoft.com/library/cc725907.aspx "Panoramica e funzionalità di MPIO").

Per sfruttare al massimo la disponibilità elevata della soluzione StorSimple, configurare MPIO negli host Windows Server connessi all'array virtuale StorSimple 1200 (detto anche dispositivo virtuale locale). A questo punto, i server host saranno in grado di tollerare un errore di collegamento, di rete o di interfaccia.

Per configurare la funzionalità MPIO è necessario seguire questa procedura:

* Prerequisiti di configurazione
* Passaggio 1: Installare MPIO nell'host Windows Server
* Passaggio 2: Configurare MPIO per volumi StorSimple
* Passaggio 3: Montare i volumi StorSimple nell'host

Ognuno dei passaggi precedenti viene illustrato nelle sezioni seguenti.

## Prerequisiti
Questa sezione illustra nel dettaglio i prerequisiti di configurazione dell'host Windows Server e dell'array virtuale.

### Dall'host Windows Server
* Assicurarsi che l'host Windows Server abbia due interfacce di rete abilitate.

### Dall'array virtuale StorSimple
* L'array virtuale deve essere configurato come un server iSCSI. Per maggiori informazioni, vedere [Configurare l'array virtuale come server iSCSI](storsimple-ova-deploy3-iscsi-setup.md). Sull'array devono essere configurate una o più interfacce di rete.   
* Le interfacce di rete dell'array virtuale devono essere raggiungibili da un host Windows Server.
* È necessario creare uno o più volumi sull'array virtuale StorSimple. Per ulteriori informazioni, vedere l'[aggiunta di un volume](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume) nell'array virtuale StorSimple 1200. In questa procedura, sono stati creati 3 volumi (2 aggiunti localmente e 1 a più livelli come illustrato di seguito) sull'array virtuale.
  
    ![mpio0](./media/storsimple-ova-configure-mpio-windows-server/mpio0.png)

### Configurazione hardware per l'array virtuale StorSimple
La figura seguente illustra la configurazione hardware per la disponibilità elevata e il bilanciamento del carico dei percorsi multipli per l'host Windows Server e per l'array virtuale StorSimple utilizzati in questa procedura.

![Configurazione hardware di MPIO](./media/storsimple-ova-configure-mpio-windows-server/1200hardwareconfig.png)

Come illustrato nella figura precedente:

* L'array virtuale StorSimple con provisioning in Hyper-V è un dispositivo attivo a nodo singolo configurato come server iSCSI.
* Sull'array sono abilitate due interfacce di rete virtuali. Nell'interfaccia utente Web locale dell'array virtuale 1200, verificare che siano abilitate due interfacce di rete accedendo alle **impostazioni di rete** come illustrato di seguito:
  
    ![Interfacce di rete abilitate su 1200](./media/storsimple-ova-configure-mpio-windows-server/mpio9.png)
  
    Prendere nota degli indirizzi IPv4 delle interfacce di rete abilitate (Ethernet, Ethernet 2 per impostazione predefinita) e salvarli per un uso successivo nell'host.
* Sull'host Windows Server sono abilitate due interfacce di rete. Se le interfacce connesse per l'host e l'array virtuale sono sulla stessa subnet, i percorsi disponibili saranno 4. In questa procedura si è verificata questa circostanza. Tuttavia, se le interfacce dell'host e dell'array virtuale si trovano su subnet IP diverse (e non instradabili), i percorsi disponibili saranno solo 2.

## Passaggio 1: Installare MPIO nell'host Windows Server
MPIO è una funzionalità facoltativa in Windows Server, e non è installata per impostazione predefinita. Deve essere installata come funzionalità tramite Server Manager. Per installare questa funzionalità nell'host Windows Server, completare la procedura seguente.

[!INCLUDE [storsimple-install-mpio-windows-server-host](../../includes/storsimple-install-mpio-windows-server-host.md)]

## Passaggio 2: Configurare MPIO per volumi StorSimple
Per identificare i volumi StorSimple, è necessario configurare MPIO. Per configurare MPIO in modo che riconosca i volumi StorSimple, effettuare le seguenti operazioni.

[!INCLUDE [storsimple-configure-mpio-volumes](../../includes/storsimple-configure-mpio-volumes.md)]

## Passaggio 3: Montare i volumi StorSimple nell'host
Dopo la configurazione di MPIO in Windows Server, i volumi creati nell'array StorSimple possono essere montati per sfruttare al meglio la ridondanza della funzionalità MPIO. Per montare un volume, attenersi alla seguente procedura.

#### Per montare i volumi nell'host
1. Aprire la finestra **Proprietà iniziatore iSCSI** sull'host Windows Server. Fare clic su **Server Manager > Dashboard > Strumenti > Iniziatore iSCSI**.
2. Nella finestra di dialogo **Proprietà iniziatore iSCSI** fare clic sulla scheda Individuazione, quindi fare clic su **Individua portale destinazione**.
3. Nella finestra di dialogo **Individua portale destinazione**, effettuare le operazioni seguenti:
   
   * Immettere l'indirizzo IP della prima interfaccia di rete abilitata sull'array virtuale StorSimple. Per impostazione predefinita, si tratta dell'interfaccia **Ethernet**. 
   * Fare clic su **OK** per tornare alla finestra di dialogo **Proprietà iniziatore iSCSI**.
     
     > [!IMPORTANT]
     > **Se si utilizza una rete privata per le connessioni iSCSI, immettere l'indirizzo IP della porta DATA connessa alla rete privata.**
     > 
     > 
4. Ripetere i passaggi 2-3 per una seconda interfaccia di rete (ad esempio, Ethernet 2) sull'array.
5. Selezionare la scheda **Destinazioni** nella finestra di dialogo **Proprietà iniziatore iSCSI**. Nell'array virtuale ogni area di volume deve essere visualizzata come destinazione in **Destinazioni individuate**. In questo caso, vengono individuate tre destinazioni, corrispondenti a tre volumi.
   
    ![mpio1](./media/storsimple-ova-configure-mpio-windows-server/mpio1.png)
6. Fare clic su **Connetti** per stabilire una sessione iSCSI con l'array StorSimple. Verrà visualizzata una finestra di dialogo **Connessione alla destinazione**. Selezionare la casella di controllo **Consenti percorsi multipli**. Fare clic su **Advanced**.
   
    ![mpio2](./media/storsimple-ova-configure-mpio-windows-server/mpio2.png)
7. Nella finestra di dialogo **Impostazioni avanzate**, effettuare le seguenti operazioni:
   
   * Nell’elenco a discesa **Scheda locale**, selezionare **Iniziatore iSCSI Microsoft**.
   * Nell'elenco a discesa **IP iniziatore**, selezionare l’indirizzo IP dell'host.
   * Nell’elenco a discesa **IP portale destinazione**, selezionare l’IP dell’interfaccia dell'array.
   * Fare clic su **OK** per tornare alla finestra di dialogo **Proprietà iniziatore iSCSI**.
     
     ![mpio3](./media/storsimple-ova-configure-mpio-windows-server/mpio3.png)
8. Fare clic su **Proprietà**.
   
    ![mpio4](./media/storsimple-ova-configure-mpio-windows-server/mpio4.png)
9. Nella finestra di dialogo **Proprietà**, fare clic su **Aggiungi sessione**.
   
   ![mpio5](./media/storsimple-ova-configure-mpio-windows-server/mpio5.png)
10. Nella finestra di dialogo **Connessione alla destinazione**, selezionare la casella di controllo **Consenti percorsi multipli**. Fare clic su **Advanced**.
11. Nella finestra di dialogo **Impostazioni avanzate**:                                        
    
    * Nell’elenco a discesa **Scheda locale**, selezionare Iniziatore iSCSI Microsoft.
    * Nell’elenco a discesa **IP iniziatore**, selezionare l’indirizzo IP corrispondente all'host. In questo caso, si sta eseguendo la connessione di due interfacce di rete sull'array a una singola interfaccia di rete sull'host. Pertanto, questa interfaccia è identica a quella fornita per la prima sessione.
    * Nell’elenco a discesa **IP portale destinazione**, selezionare l’indirizzo IP per la seconda interfaccia di dati abilitata sull'array.
    * Fare clic su **OK** per tornare alla finestra di dialogo Proprietà iniziatore iSCSI. È stata aggiunta una seconda sessione alla destinazione.
      
       ![mpio11](./media/storsimple-ova-configure-mpio-windows-server/mpio11.png)
    * Dopo aver aggiunto le sessioni (percorsi) desiderate, nella finestra di dialogo **Proprietà iniziatore iSCSI**, selezionare la destinazione e fare clic su **Proprietà**. Nella scheda Sessioni della finestra di dialogo **Proprietà**, notare i quatto identificatori di sessione che corrispondono alle possibili permutazioni di percorso. Per annullare una sessione, selezionare la casella di controllo accanto a un identificatore di sessione, quindi fare clic su **Disconnetti**.
    * Per visualizzare i dispositivi presentati all'interno delle sessioni, selezionare la scheda **Dispositivi**. Per configurare il criterio MPIO per un dispositivo selezionato, fare clic su **MPIO**. **
    * Viene visualizzata la finestra di dialogo Dettagli**. Nella scheda **MPIO**, è possibile selezionare le impostazioni **Criterio di bilanciamento del carico** appropriate. È inoltre possibile visualizzare il tipo di percorso **Attivo** o **Standby**.
12. Ripetere i passaggi da 8 a 11 per aggiungere altre sessioni (percorsi) alla destinazione. Con due interfacce nell'host e due sull'array virtuale, è possibile aggiungere un totale di quattro sessioni per ogni destinazione.
    
    ![mpio14](./media/storsimple-ova-configure-mpio-windows-server/mpio14.png)
13. È necessario ripetere questi passaggi per ogni volume (aree di volume come destinazione).
    
    ![mpio15](./media/storsimple-ova-configure-mpio-windows-server/mpio15.png)
14. Aprire **Gestione computer** passando a **Server Manager > Dashboard > Gestione computer**. Nel riquadro sinistro, fare clic su **Archiviazione > Gestione disco**. I volumi creati nell'array virtuale StorSimple visibili per questo host verranno visualizzati in **Gestione disco** come nuovi dischi.
15. Inizializzare il disco e creare un nuovo volume. Durante il processo di formattazione, selezionare una dimensione di unità di allocazione (AUS, allocation unit size) di 64 KB. Ripetere il processo per tutti i volumi disponibili.
    
    ![Gestione disco](./media/storsimple-ova-configure-mpio-windows-server/mpio20.png)
16. In **Gestione disco**, fare clic con il pulsante destro del mouse su **Disco** e selezionare **Proprietà**.
17. Nella finestra di dialogo **Proprietà dispositivo disco percorsi multipli** fare clic sulla scheda **MPIO**.
    
    ![Proprietà del disco](./media/storsimple-ova-configure-mpio-windows-server/mpio21.png)
18. Nella sezione **Nome DSM** fare clic su **Dettagli** e verificare che i parametri impostati siano quelli predefiniti. I parametri predefiniti sono:
    
    * Periodo di verifica percorso = 30
    * Numero di tentativi = 3
    * Periodo di rimozione PDO = 20
    * Intervallo tentativi = 1
    * Verifica percorso attivata = deselezionata.
    
    > [!NOTE]
    > **Non modificare i parametri predefiniti.**
    > 
    > 

## Passaggi successivi
Altre informazioni sull'[uso del servizio StorSimple Manager per amministrare l'array virtuale StorSimple](storsimple-ova-manager-service-administration.md).

<!---HONumber=AcomDC_0622_2016-->