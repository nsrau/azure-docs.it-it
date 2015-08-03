<properties 
   pageTitle="Utilizzo di Gestione snapshot StorSimple per connettere e gestire i dispositivi StorSimple | Microsoft Azure"
   description="Viene descritto come utilizzare lo snap-in MMC di Gestione snapshot StorSimple per connettere e gestire i dispositivi StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/09/2015"
   ms.author="v-sharos" />

# Utilizzo di Gestione snapshot StorSimple per connettere e gestire i dispositivi StorSimple

## Panoramica

È possibile utilizzare i nodi del riquadro **Ambito** di Gestione snapshot StorSimple per verificare i dati dei dispositivi StorSimple importati e aggiornare i dispositivi di archiviazione connessi. Inoltre, quando si fa clic sul nodo **Dispositivi**, è possibile visualizzare un elenco dei dispositivi connessi e delle informazioni sullo stato corrispondenti nel riquadro **Risultati**.

![Dispositivi connessi](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Figura 1: Dispositivo di Gestione snapshot StorSimple connesso**

A seconda delle selezioni di **Visualizza**, nel riquadro **Risultati** vengono mostrate le informazioni seguenti su ciascun dispositivo. (Per ulteriori informazioni sulla configurazione di una visualizzazione, andare a [Menu Visualizza](storsimple-use-snapshot-manager.md#view-menu).)


| Colonna risultati |Descrizione |
|:----------------|:--------------------| 
| Nome | Il nome del dispositivo come configurato nel portale di gestione di Microsoft Azure|
| Modello | Il numero di modello del dispositivo|
| Versione | La versione software installata sul dispositivo |
| Stato | Se il dispositivo è disponibile |
| Ultima sincronizzazione | Data e ora dell'ultima sincronizzazione del dispositivo |
| N. di serie | Il numero di serie del dispositivo |
 
Se si fa clic con il pulsante destro del mouse sul nodo **Dispositivi** del riquadro **Ambito**, è possibile selezionare le azioni seguenti:

- Aggiunta o sostituzione di un dispositivo 
- Connessione di un dispositivo e verifica delle importazioni 
- Aggiornamento dei dispositivi connessi 

Se si fa clic sul nodo **Dispositivi**, quindi si fa clic con il pulsante destro del mouse sul nome del dispositivo nel riquadro **Risultati**, è possibile selezionare le azioni seguenti:

- Autenticazione di un dispositivo 
- Visualizzazione dei dettagli del dispositivo 
- Aggiornamento di un dispositivo 
- Eliminazione della configurazione di un dispositivo 
- Modifica della password di un dispositivo

>[AZURE.NOTE]Tutte queste azioni sono disponibili anche nel riquadro **Azioni**.
 
In questa esercitazione viene illustrato come utilizzare Gestione snapshot StorSimple per connettere e gestire i dispositivi ed eseguire le attività seguenti:

- Aggiunta o sostituzione di un dispositivo 
- Connessione di un dispositivo e verifica delle importazioni 
- Aggiornamento dei dispositivi connessi 
- Autenticazione di un dispositivo 
- Visualizzazione dei dettagli del dispositivo 
- Aggiornamento di un singolo dispositivo 
- Eliminazione della configurazione di un dispositivo 
- Modifica della password scaduta di un dispositivo
- Sostituzione di un dispositivo guasto

>[AZURE.NOTE]Per informazioni generali sull'utilizzo dell'interfaccia di Gestione snapshot StorSimple, andare a [Interfaccia utente di Gestione snapshot StorSimple](storsimple-use-snapshot-manager.md)


## Aggiunta o sostituzione di un dispositivo

Utilizzare la procedura seguente per aggiungere o sostituire un dispositivo StorSimple.

#### Per aggiungere o sostituire un dispositivo

1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.

2. Nel riquadro **Ambito**, fare clic con il pulsante destro del mouse sul nodo **Dispositivi**, quindi fare clic su **Configura un dispositivo**. Viene visualizzata la finestra di dialogo **Configura un dispositivo**.

    ![Configurazione di un dispositivo StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png)

3. Nella casella di riepilogo a discesa **Dispositivo**, selezionare l'indirizzo IP del dispositivo o del dispositivo virtuale.

4. Nella casella di testo **Password**, digitare la password di Gestione snapshot StorSimple creata per il dispositivo nel portale di gestione di Azure. Fare clic su **OK**. Gestione snapshot StorSimple esegue la ricerca del dispositivo identificato.

    - Se il dispositivo è disponibile, Gestione snapshot StorSimple aggiunge una connessione. 

    - Se il dispositivo non è disponibile per qualsiasi motivo, Gestione snapshot StorSimple restituisce un messaggio di errore. Fare clic su **OK** per chiudere il messaggio di errore, quindi fare clic su **Annulla** per chiudere la finestra di dialogo **Configura un dispositivo**.

## Connessione di un dispositivo e verifica delle importazioni

Utilizzare la procedura seguente per connettere un dispositivo StorSimple e verificare che i gruppi di volumi esistenti che dispongono di backup associati vengano importati.

#### Per connettere un dispositivo e verificare le importazioni

1. Per connettere un dispositivo a Gestione snapshot StorSimple, seguire le istruzioni descritte in Aggiunta o sostituzione di un dispositivo. Quando si connette a un dispositivo, Gestione snapshot StorSimple risponde nel modo seguente:

    - Se il dispositivo non è disponibile per qualsiasi motivo, Gestione snapshot StorSimple restituisce un messaggio di errore. 

   - Se il dispositivo è disponibile, Gestione snapshot StorSimple aggiunge una connessione. Quando si seleziona il dispositivo, viene visualizzato nel riquadro **Risultati** e il campo relativo allo stato indica che il dispositivo è **disponibile**. Gestione snapshot StorSimple importa i gruppi di volumi configurati per il dispositivo, purché dispongano di backup associati. I criteri di backup non vengono importati. I gruppi di volumi che non dispongono di backup associati non vengono importati.

2. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.

3. Fare clic con il pulsante destro del mouse nel riquadro **Ambito**, quindi fare clic su **Visualizza/Nascondi importazioni**.

    ![Selezione di Visualizza/Nascondi importazioni](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png)

4. Viene visualizzata la finestra di dialogo **Visualizza/Nascondi importazioni**, in cui viene mostrato lo stato dei gruppi di volumi importati e dei backup. Fare clic su **OK**.

Dopo che i gruppi di volumi e i backup sono stati importati correttamente, è possibile utilizzare Gestione snapshot StorSimple per gestirli, esattamente come vengono gestiti i gruppi di volumi e i backup creati e configurati con Gestione snapshot StorSimple.

## Aggiornamento dei dispositivi connessi

Utilizzare la procedura seguente per sincronizzare i dispositivi StorSimple connessi con Gestione snapshot StorSimple.

####Per aggiornare i dispositivi connessi

1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.

2. Nel riquadro **Ambito**, fare clic con il pulsante destro del mouse su **Dispositivi**, quindi fare clic su **Aggiorna dispositivi**. Ciò consente di sincronizzare i dispositivi connessi con Gestione snapshot StorSimple in modo che sia possibile visualizzare i gruppi di volumi e i backup, incluse tutte le aggiunte recenti.

    ![Aggiornamento dei dispositivi StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)
 
L’azione **Aggiorna dispositivi** consente di recuperare i nuovi gruppi di volumi e i backup associati dai dispositivi connessi. A differenza dell’azione **Ripeti analisi dei volumi** disponibile per il nodo **Volumi**, **Aggiorna dispositivi** non consente di ripristina il registro del backup.

## Autenticazione di un dispositivo

Utilizzare la procedura seguente per autenticare un dispositivo StorSimple con Gestione snapshot StorSimple.

#### Per autenticare un dispositivo

1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.

2. Nel riquadro **Ambito**, fare clic su **Dispositivi**.

3. Nel riquadro **Risultati**, fare clic con il pulsante destro del mouse sul nome del dispositivo, quindi fare clic su **Autentica**.

4. Viene visualizzata la finestra di dialogo **Autentica**. Digitare la password del dispositivo, quindi fare clic su **OK**.

    ![Finestra di dialogo Autentica](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png)
 
## Visualizzazione dei dettagli del dispositivo

Utilizzare la procedura seguente per visualizzare i dettagli di un dispositivo StorSimple e, se necessario, risincronizzare il dispositivo con Gestione snapshot StorSimple.

#### Per visualizzare e risincronizzare i dettagli del dispositivo

1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.

2. Nel riquadro **Ambito**, fare clic su **Dispositivi**.

3. Nel riquadro **Risultati**, fare clic con il pulsante destro del mouse sul nome del dispositivo, quindi fare clic su **Dettagli**.

4. Viene visualizzata la finestra di dialogo **Dettagli dispositivo**. In questa casella vengono mostrati il nome, il modello, la versione, il numero di serie, lo stato, il nome qualificato iSCSI (IQN) di destinazione e la data e l’ora dell’ultima sincronizzazione.

   - Fare clic su **Risincronizza** per sincronizzare il dispositivo.

   - Fare clic su **OK** o **Annulla** per chiudere la finestra di dialogo.

    ![Dettagli dispositivo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png)
 
## Aggiornamento di un singolo dispositivo

Utilizzare la procedura seguente per risincronizzare un singolo dispositivo StorSimple con Gestione Snapshot StorSimple.

#### Per aggiornare un dispositivo

1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager. 

2. Nel riquadro **Ambito**, fare clic su **Dispositivi**.

3. Nel riquadro **Risultati**, fare clic con il pulsante destro del mouse sul nome del dispositivo, quindi fare clic su **Aggiorna dispositivo**. Ciò consente di sincronizzare il dispositivo con Gestione snapshot StorSimple.

## Eliminazione della configurazione di un dispositivo

Utilizzare la procedura seguente per eliminare la configurazione di un singolo dispositivo StorSimple da Gestione snapshot StorSimple.

#### Per eliminare la configurazione di un dispositivo

1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager. 

2. Nel riquadro **Ambito**, fare clic su **Dispositivi**.

3. Nel riquadro **Risultati**, fare clic con il pulsante destro del mouse sul nome del dispositivo, quindi fare clic su **Elimina**.

4. Viene visualizzato il messaggio seguente. Fare clic su **Sì** per eliminare la configurazione oppure su **No** per annullare l'eliminazione.

    ![Eliminazione della configurazione del dispositivo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## Modifica della password scaduta di un dispositivo

È necessario immettere una password per autenticare un dispositivo StorSimple con Gestione snapshot StorSimple. È possibile configurare questa password quando si utilizza l'interfaccia Windows PowerShell per configurare il dispositivo. Tuttavia, la password può scadere. In questo caso, è possibile utilizzare il portale di gestione di Microsoft Azure per modificare la password. Quindi, poiché il dispositivo è stato configurato in Gestione snapshot StorSimple prima della scadenza della password, è necessario autenticare nuovamente il dispositivo in Gestione snapshot StorSimple.

#### Per modificare la password scaduta

1. Nel portale di gestione di Azure, avviare il servizio StorSimple Manager.

2. Fare clic su **Dispositivi** > **Configura** per il dispositivo.

3. Scorrere verso il basso fino alla sezione Gestione snapshot StorSimple. Immettere una password composta da 14-15 caratteri. Assicurarsi che la password contenga una combinazione di lettere maiuscole, minuscole, numeri e caratteri speciali.

4. Immettere nuovamente la password per confermarla.

5. Fare clic su **Save** nella parte inferiore della pagina.

#### Per eseguire nuovamente l'autenticazione del dispositivo

1. Avviare Gestione snapshot StorSimple.

2. Nel riquadro **Ambito**, fare clic su **Dispositivi**. Nel riquadro **Risultati** viene visualizzato un elenco dei dispositivi configurati.

3. Selezionare il dispositivo, fare clic con il pulsante destro del mouse, quindi fare clic su **Autentica**.

4. Nella finestra **Autentica**, immettere la nuova password.

5. Selezionare il dispositivo, fare clic con il pulsante destro del mouse, quindi selezionare **Aggiorna dispositivo**. Ciò consente di sincronizzare il dispositivo con Gestione snapshot StorSimple.

## Sostituzione di un dispositivo guasto

Se un dispositivo StorSimple è guasto e viene sostituito da un dispositivo di standby (failover), attenersi alla procedura seguente per eseguire la connessione al nuovo dispositivo e visualizzare i backup associati.

#### Per eseguire la connessione a un nuovo dispositivo dopo il failover

1. Riconfigurare la connessione iSCSI al nuovo dispositivo. Per istruzioni, andare a "Passaggio 7: montaggio, inizializzazione e formattazione di un volume" in [Distribuzione del dispositivo StorSimple locale](storsimple-deployment-walkthrough.md). 

>[AZURE.NOTE]Se il nuovo dispositivo StorSimple ha lo stesso indirizzo IP del precedente, potrebbe essere in grado di connettere la configurazione precedente.

2. Arrestare il servizio di gestione di Microsoft StorSimple:

    1. Avviare Server Manager.

    2. Nel Dashboard di Server Manager, selezionare **Servizi** dal menu **Strumenti**.

    3. Nella finestra **Servizi**, selezionare **Servizio di gestione di Microsoft StorSimple**.

    4. Nel riquadro a destra, sotto **Servizio di gestione Microsoft StorSimple**, fare clic su **Arresta il servizio**.

3. Rimuovere le informazioni di configurazione correlate al dispositivo precedente:

    1. In Esplora file, passare a C:\ProgramData\Microsoft\StorSimple\BACatalog. 

    2. Eliminare i file nella cartella BACatalog.

4. Riavviare il servizio di gestione di Microsoft StorSimple:

    1. Nel Dashboard di Server Manager, selezionare **Servizi** dal menu **Strumenti**. 

    2. Nella finestra **Servizi**, selezionare **Servizio di gestione di Microsoft StorSimple**.

    3. Nel riquadro a destra, sotto **Servizio di gestione Microsoft StorSimple**, fare clic su **Riavvia il servizio**.

5. Avviare Gestione snapshot StorSimple.

6. Per configurare il nuovo dispositivo StorSimple, completare i passaggi descritti in [Passaggio 2: connessione di un dispositivo StorSimple](storsimple-snapshot-manager-deployment.md#step-2:-connect-a-storsimple-device) in [Distribuire Gestione snapshot StorSimple](storsimple-snapshot-manager-deployment.md).

7. Fare clic con il pulsante destro del mouse sul nodo di livello superiore nel riquadro **Ambito** (Gestione snapshot StorSimple nell'esempio), quindi fare clic su **Visualizza/Nascondi importazioni**.

8. Quando i gruppi di volumi e i backup importati sono visibili in Gestione snapshot StorSimple, viene visualizzato un messaggio. Fare clic su **OK**.

## Passaggi successivi

[Utilizzo di Gestione snapshot StorSimple per visualizzare e gestire i volumi](storsimple-snapshot-manager-manage-volumes.md)

<!---HONumber=July15_HO4-->