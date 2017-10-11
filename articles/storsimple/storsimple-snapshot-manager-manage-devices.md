---
title: Gestione dei dispositivi con StorSimple Snapshot Manager | Microsoft Docs
description: Viene descritto come utilizzare lo snap-in MMC di Gestione snapshot StorSimple per connettere e gestire i dispositivi StorSimple.
services: storsimple
documentationcenter: 
author: SharS
manager: timlt
editor: 
ms.assetid: 966ecbe3-a7fa-4752-825f-6694dd949946
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: f5e3186a4271e0be781f367fa75ada195c58c960
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Utilizzo di Gestione snapshot StorSimple per connettere e gestire i dispositivi StorSimple
## <a name="overview"></a>Panoramica
È possibile utilizzare i nodi del riquadro **Ambito** di Gestione snapshot StorSimple per verificare i dati dei dispositivi StorSimple importati e aggiornare i dispositivi di archiviazione connessi. Quando si fa clic sul nodo **Dispositivi**, è possibile visualizzare un elenco dei dispositivi connessi e delle informazioni sullo stato corrispondenti nel riquadro **Risultati**.

![Dispositivi connessi](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Figura 1: Dispositivo di Gestione snapshot StorSimple connesso** 

A seconda delle selezioni di **Visualizza**, nel riquadro **Risultati** vengono mostrate le informazioni seguenti su ciascun dispositivo. (Per ulteriori informazioni sulla configurazione di una visualizzazione, andare a [Menu Visualizza](storsimple-use-snapshot-manager.md#view-menu).)

| Colonna risultati | Descrizione |
|:--- |:--- |
| Nome |Il nome del dispositivo come configurato nel portale di gestione di Azure classico |
| Modello |Il numero di modello del dispositivo |
| Versione |La versione software installata sul dispositivo |
| Stato |Se il dispositivo è disponibile |
| Ultima sincronizzazione |Data e ora dell'ultima sincronizzazione del dispositivo |
| N. di serie |Il numero di serie del dispositivo |

Se si fa clic con il pulsante destro del mouse sul nodo **Dispositivi** del riquadro **Ambito**, è possibile selezionare le azioni seguenti:

* Aggiunta o sostituzione di un dispositivo
* Connessione di un dispositivo e verifica delle importazioni
* Aggiornamento dei dispositivi connessi

Se si fa clic sul nodo **Dispositivi**, quindi si fa clic con il pulsante destro del mouse sul nome del dispositivo nel riquadro **Risultati**, è possibile selezionare le azioni seguenti:

* Autenticazione di un dispositivo
* Visualizzazione dei dettagli del dispositivo
* Aggiornamento di un dispositivo
* Eliminazione della configurazione di un dispositivo
* Modifica della password di un dispositivo

> [!NOTE]
> Tutte queste azioni sono disponibili anche nel riquadro **Azioni** .


In questa esercitazione viene illustrato come utilizzare Gestione snapshot StorSimple per connettere e gestire i dispositivi ed eseguire le attività seguenti:

* Aggiunta o sostituzione di un dispositivo
* Connessione di un dispositivo e verifica delle importazioni
* Aggiornamento dei dispositivi connessi
* Autenticazione di un dispositivo
* Visualizzazione dei dettagli del dispositivo
* Aggiornamento di un singolo dispositivo
* Eliminazione della configurazione di un dispositivo
* Modifica della password scaduta di un dispositivo
* Sostituzione di un dispositivo guasto

> [!NOTE]
> Per informazioni generali sull'utilizzo dell'interfaccia di Gestione snapshot StorSimple, andare a [Interfaccia utente di Gestione snapshot StorSimple](storsimple-use-snapshot-manager.md).


## <a name="add-or-replace-a-device"></a>Aggiunta o sostituzione di un dispositivo
Utilizzare la procedura seguente per aggiungere o sostituire un dispositivo StorSimple.

#### <a name="to-add-or-replace-a-device"></a>Per aggiungere o sostituire un dispositivo
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito**, fare clic con il pulsante destro del mouse sul nodo **Dispositivi**, quindi fare clic su **Configura un dispositivo**. Viene visualizzata la finestra di dialogo **Configura un dispositivo** .
   
    ![Configurazione di un dispositivo StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 
3. Nella casella di riepilogo a discesa **Dispositivo** , selezionare l'indirizzo IP del dispositivo o del dispositivo virtuale. 
4. Nella casella di testo **Password** , digitare la password di Gestione snapshot StorSimple creata per il dispositivo nel portale di Azure classico. Fare clic su **OK**. Gestione snapshot StorSimple esegue la ricerca del dispositivo identificato. 
   
   * Se il dispositivo è disponibile, Gestione snapshot StorSimple aggiunge una connessione.
   * Se il dispositivo non è disponibile per qualsiasi motivo, Gestione snapshot StorSimple restituisce un messaggio di errore. Fare clic su **OK** per chiudere il messaggio di errore e quindi fare clic su **Annulla** per chiudere la finestra di dialogo **Configurare un dispositivo**.

## <a name="connect-a-device-and-verify-imports"></a>Connessione di un dispositivo e verifica delle importazioni
Utilizzare la procedura seguente per connettere un dispositivo StorSimple e verificare che i gruppi di volumi esistenti che dispongono di backup associati vengano importati.

#### <a name="to-connect-a-device-and-verify-imports"></a>Per connettere un dispositivo e verificare le importazioni
1. Per connettere un dispositivo a Gestione snapshot StorSimple, seguire le istruzioni descritte in Aggiunta o sostituzione di un dispositivo. Quando si connette a un dispositivo, Gestione snapshot StorSimple risponde nel modo seguente:
   
   * Se il dispositivo non è disponibile per qualsiasi motivo, Gestione snapshot StorSimple restituisce un messaggio di errore. 
   
   * Se il dispositivo è disponibile, Gestione snapshot StorSimple aggiunge una connessione. Quando si seleziona il dispositivo, viene visualizzato nel riquadro **Risultati** e il campo relativo allo stato indica che il dispositivo è **Disponibile**. Gestione snapshot StorSimple importa i gruppi di volumi configurati per il dispositivo, purché dispongano di backup associati. I criteri di backup non vengono importati. I gruppi di volumi che non dispongono di backup associati non vengono importati.
2. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
3. Fare clic con il pulsante destro del mouse nel riquadro **Ambito**, quindi fare clic su **Attiva/disattiva visualizzazione importazioni**.
   
    ![Selezione di Visualizza/Nascondi importazioni](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 
4. Viene visualizzata la finestra di dialogo **Visualizza/Nascondi importazioni** , in cui viene mostrato lo stato dei gruppi di volumi importati e dei backup. Fare clic su **OK**.

Dopo che i gruppi di volumi e i backup sono stati importati correttamente, è possibile utilizzare Gestione snapshot StorSimple per gestirli, esattamente come vengono gestiti i gruppi di volumi e i backup creati e configurati con Gestione snapshot StorSimple. 

## <a name="refresh-connected-devices"></a>Aggiornamento dei dispositivi connessi
Utilizzare la procedura seguente per sincronizzare i dispositivi StorSimple connessi con Gestione snapshot StorSimple.

#### <a name="to-refresh-connected-devices"></a>Per aggiornare i dispositivi connessi
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito** fare clic con il pulsante destro del mouse su **Dispositivi**, quindi fare clic su **Aggiorna dispositivi**. Ciò consente di sincronizzare i dispositivi connessi con Gestione snapshot StorSimple in modo che sia possibile visualizzare i gruppi di volumi e i backup, incluse tutte le aggiunte recenti. 
   
    ![Aggiornamento dei dispositivi StorSimple](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)

L’azione **Aggiorna dispositivi** consente di recuperare i nuovi gruppi di volumi e i backup associati dai dispositivi connessi. A differenza dell'azione **Nuova scansione volumi** disponibile per il nodo **Volumi**, **Aggiorna dispositivi** non consente di ripristina il registro del backup.

## <a name="authenticate-a-device"></a>Autenticazione di un dispositivo
Utilizzare la procedura seguente per autenticare un dispositivo StorSimple con Gestione snapshot StorSimple.

#### <a name="to-authenticate-a-device"></a>Per autenticare un dispositivo
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito** fare clic su **Dispositivi**.
3. Nel riquadro **Risultati** fare clic con il pulsante destro del mouse sul nome del dispositivo, quindi fare clic su **Autentica**.
4. Viene visualizzata la finestra di dialogo **Autentica** . Digitare la password del dispositivo, quindi fare clic su **OK**.
   
    ![Finestra di dialogo Autentica](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 

## <a name="view-device-details"></a>Visualizzazione dei dettagli del dispositivo
Utilizzare la procedura seguente per visualizzare i dettagli di un dispositivo StorSimple e, se necessario, risincronizzare il dispositivo con Gestione snapshot StorSimple.

#### <a name="to-view-and-resynchronize-device-details"></a>Per visualizzare e risincronizzare i dettagli del dispositivo
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito** fare clic su **Dispositivi**.
3. Nel riquadro **Risultati** fare clic con il pulsante destro del mouse sul nome del dispositivo, quindi fare clic su **Dettagli**.

4. Viene visualizzata la finestra di dialogo **Dettagli dispositivo**. In questa casella vengono mostrati il nome, il modello, la versione, il numero di serie, lo stato, il nome qualificato iSCSI (IQN) di destinazione e la data e l’ora dell’ultima sincronizzazione.

* Fare clic su **Risincronizza** per sincronizzare il dispositivo.
* Fare clic su **OK** o **Annulla** per chiudere la finestra di dialogo.
  
  ![Dettagli dispositivo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 

## <a name="refresh-an-individual-device"></a>Aggiornamento di un singolo dispositivo
Utilizzare la procedura seguente per risincronizzare un singolo dispositivo StorSimple con Gestione Snapshot StorSimple.

#### <a name="to-refresh-a-device"></a>Per aggiornare un dispositivo
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager. 
2. Nel riquadro **Ambito** fare clic su **Dispositivi**. 
3. Nel riquadro **Risultati**, fare clic con il pulsante destro del mouse sul nome del dispositivo, quindi fare clic su **Aggiorna dispositivo**. Ciò consente di sincronizzare il dispositivo con Gestione snapshot StorSimple.

## <a name="delete-a-device-configuration"></a>Eliminazione della configurazione di un dispositivo
Utilizzare la procedura seguente per eliminare la configurazione di un singolo dispositivo StorSimple da Gestione snapshot StorSimple.

#### <a name="to-delete-a-device-configuration"></a>Per eliminare la configurazione di un dispositivo
1. Fare clic sull’icona del desktop per avviare StorSimple Snapshot Manager.
2. Nel riquadro **Ambito** fare clic su **Dispositivi**. 
3. Nel riquadro **Risultati** fare clic con il pulsante destro del mouse sul nome del dispositivo, quindi fare clic su **Elimina**. 
4. Viene visualizzato il messaggio seguente. Fare clic su **Sì** per eliminare la configurazione oppure su **No** per annullare l'eliminazione.
   
    ![Eliminazione della configurazione del dispositivo](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Modifica della password scaduta di un dispositivo
È necessario immettere una password per autenticare un dispositivo StorSimple con Gestione snapshot StorSimple. È possibile configurare questa password quando si utilizza l'interfaccia Windows PowerShell per configurare il dispositivo. Tuttavia, la password può scadere. In questo caso, è possibile utilizzare il portale di Azure classico per modificare la password. Quindi, poiché il dispositivo è stato configurato in Gestione snapshot StorSimple prima della scadenza della password, è necessario autenticare nuovamente il dispositivo in Gestione snapshot StorSimple.

#### <a name="to-change-the-expired-password"></a>Per modificare la password scaduta
1. Nel portale di Azure classico, avviare il servizio StorSimple Manager.
2. Fare clic su **Dispositivi** > **Configura** per il dispositivo.
3. Scorrere verso il basso fino alla sezione Gestione snapshot StorSimple. Immettere una password composta da 14-15 caratteri. Assicurarsi che la password contenga una combinazione di lettere maiuscole, minuscole, numeri e caratteri speciali.
4. Immettere nuovamente la password per confermarla.
5. Fare clic su **Save** nella parte inferiore della pagina.

#### <a name="to-re-authenticate-the-device"></a>Per eseguire nuovamente l'autenticazione del dispositivo
1. Avviare Gestione snapshot StorSimple.
2. Nel riquadro **Ambito** fare clic su **Dispositivi**. Nel riquadro **Risultati** viene visualizzato un elenco dei dispositivi configurati.
3. Selezionare il dispositivo, fare clic con il pulsante destro del mouse, quindi fare clic su **Autentica**.
4. Nella finestra **Autentica** , immettere la nuova password.
5. Selezionare il dispositivo, fare clic con il pulsante destro del mouse, quindi selezionare **Aggiorna dispositivo**. Ciò consente di sincronizzare il dispositivo con Gestione snapshot StorSimple.

## <a name="replace-a-failed-device"></a>Sostituzione di un dispositivo guasto
Se un dispositivo StorSimple è guasto e viene sostituito da un dispositivo di standby (failover), attenersi alla procedura seguente per eseguire la connessione al nuovo dispositivo e visualizzare i backup associati.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Per eseguire la connessione a un nuovo dispositivo dopo il failover
1. Riconfigurare la connessione iSCSI al nuovo dispositivo. Per istruzioni, andare a "Passaggio 7: montaggio, inizializzazione e formattazione di un volume" in [Distribuzione del dispositivo StorSimple locale](storsimple-8000-deployment-walkthrough-u2.md).

> [!NOTE]
> Se il nuovo dispositivo StorSimple ha lo stesso indirizzo IP del precedente, potrebbe essere in grado di connettere la configurazione precedente.


1. Arrestare il servizio di gestione di Microsoft StorSimple:
   
   1. Avviare Server Manager.
   2. Nel Dashboard di Server Manager, selezionare **Servizi** dal menu **Strumenti**.
   3. Nella finestra **Servizi** selezionare **Microsoft StorSimple Management Service**.
   4. Nel riquadro a destra, sotto **Microsoft StorSimple Management Service**, fare clic su **Arresta il servizio**.
2. Rimuovere le informazioni di configurazione correlate al dispositivo precedente:
   
   1. In Esplora file, passare a C:\ProgramData\Microsoft\StorSimple\BACatalog.
   2. Eliminare i file nella cartella BACatalog.
3. Riavviare il servizio di gestione di Microsoft StorSimple:
   
   1. Nel Dashboard di Server Manager, selezionare **Servizi** dal menu **Strumenti**.
   2. Nella finestra **Servizi** selezionare **Microsoft StorSimple Management Service**.
   3. Nel riquadro a destra, sotto **Microsoft StorSimple Management Service**, fare clic su **Riavvia il servizio**.
4. Avviare Gestione snapshot StorSimple.
5. Per configurare il nuovo dispositivo StorSimple, completare i passaggi descritti in Passaggio 2: connessione di un dispositivo StorSimple in [Distribuire Gestione snapshot StorSimple](storsimple-snapshot-manager-deployment.md).
6. Fare clic con il pulsante destro del mouse sul nodo di livello superiore nel riquadro **Ambito** (StorSimple Snapshot Manager nell'esempio), quindi fare clic su **Attiva/disattiva visualizzazione importazioni**. 
7. Quando i gruppi di volumi e i backup importati sono visibili in Gestione snapshot StorSimple, viene visualizzato un messaggio. Fare clic su **OK**.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [Usare Gestione Snapshot StorSimple per amministrare la soluzione di StorSimple](storsimple-snapshot-manager-admin.md).
* Informazioni su come [utilizzare Gestione Snapshot StorSimple per visualizzare e gestire volumi](storsimple-snapshot-manager-manage-volumes.md).

