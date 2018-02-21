---
title: Eseguire il backup di Windows Server in Azure | Microsoft Docs
description: "Questa esercitazione descrive nel dettaglio l’esecuzione di backup di istanze locali di Windows Server in un insieme di credenziali dei servizi di ripristino."
services: back up
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
keywords: eseguire il backup di windows server; eseguire il backup di windows server; eseguire il backup e il ripristino di emergenza
ms.assetid: 
ms.service: back up
ms.workload: storage-back up-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/23/2017
ms.author: saurabhsensharma;markgal;
ms.custom: mvc
ms.openlocfilehash: 0dbf3850c7fcccb1a02e70a19b498522a4ce0e79
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="back-up-windows-server-to-azure"></a>Eseguire il backup di Windows Server in Azure


È possibile usare Backup di Azure per proteggere Windows Server da eventuali danneggiamenti, attacchi e situazioni di emergenza. Backup di Azure è uno strumento semplice, noto come agente di Servizi di ripristino di Microsoft Azure (MARS). L'agente di MARS è installato nel server di Windows per proteggere file e cartelle e informazioni di configurazione del server tramite lo stato del sistema di Windows Server. In questa esercitazione viene illustrato come usare l'agente di MARS per eseguire il backup di Windows Server in Azure. In questa esercitazione si apprenderà come: 


> [!div class="checklist"]
> * Scaricare e configurare l'agente di MARS
> * Configurare l'ora e la pianificazione di conservazione per i backup del server
> * Eseguire un backup ad hoc


## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Prima di eseguire il backup di Windows Server, è necessario creare una posizione per i backup o i punti di ripristino in cui archiviare i dati. Un [insieme di credenziali dei servizi di ripristino](backup-azure-recovery-services-vault-overview.md) è un contenitore di Azure in cui vengono archiviati i backup di Windows Server. Seguire questa procedura per creare un insieme di credenziali dei servizi di ripristino nel portale di Azure. 

1. Nel menu a sinistra selezionare **Tutti i servizi** e digitare **Servizi di ripristino** nell'elenco dei servizi. Fare clic su **Insiemi di credenziali dei servizi di ripristino**.

   ![aprire l'insieme di credenziali dei servizi di ripristino](./media/tutorial-backup-windows-server-to-azure/full-browser-open-rs-vault_2.png)

2.  Scegliere **Aggiungi** dal menu **Insiemi di credenziali dei servizi di ripristino**.

   ![specificare informazioni per l'insieme di credenziali](./media/tutorial-backup-windows-server-to-azure/provide-vault-detail-2.png)

3.  Nel menu **Insieme di credenziali dei servizi di ripristino**

    - digitare *myRecoveryServicesVault* in **Nome**.
    - L’ID di sottoscrizione corrente viene visualizzato in **Sottoscrizione**.
    - Per **Gruppo di risorse**, selezionare **Usa esistente** e scegliere *myResourceGroup*. Se *myResourceGroup* non esiste, selezionare **Crea nuovo** e digitare *myResourceGroup*. 
    - Dal menu a discesa **Percorso** scegliere *Europa occidentale*.
    - Fare clic su **Crea** per creare l'insieme di credenziali dei servizi di ripristino.
 
L'insieme di credenziali, dopo essere stato creato, viene visualizzato negli insiemi di credenziali di Servizi di ripristino.

## <a name="download-recovery-services-agent"></a>Scaricare l'agente di Servizi di ripristino

L'agente di Servizi di ripristino di Microsoft Azure (MARS) crea un'associazione tra Windows Server e l'insieme di credenziali dei servizi di ripristino. La procedura seguente illustra come scaricare l'agente nel server.

1.  Dall'elenco degli insiemi di credenziali dei servizi di ripristino selezionare **myRecoveryServicesVault** per aprire il relativo dashboard.

   ![specificare informazioni per l'insieme di credenziali](./media/tutorial-backup-windows-server-to-azure/open-vault-from-list.png)

2.  Selezionare **Backup** dal menu del dashboard dell'insieme di credenziali.

3.  Nel menu **Obiettivo del backup**:

    - per **Posizione di esecuzione del carico di lavoro**, selezionare**locale**, 
    - per **Elementi di cui eseguire il backup**, selezionare **File e cartelle** e **Stato del sistema** 

    ![specificare informazioni per l'insieme di credenziali](./media/tutorial-backup-windows-server-to-azure/backup-goal.png)
    
4.  Fare clic su **Preparare l'infrastruttura** per aprire il menu **Prepare infrastruttura**.
5.  Nel menu **Preparare l'infrastruttura** fare clic su **Scaricare l'agente per Windows Server o Windows Client** per scaricare il file *MARSAgentInstaller.exe*. 

    ![Preparare l'infrastruttura](./media/tutorial-backup-windows-server-to-azure/prepare-infrastructure.png)

    Il programma di installazione apre un finestra separata del browser e scarica il file **MARSAgentInstaller.exe**.
 
6.  Prima di eseguire il file scaricato, fare clic sul pulsante **Scarica** nel pannello Preparare l'infrastruttura per scaricare e salvare il file dell'**Insieme di credenziali**. Questo file è necessario per connettere l'agente di MARS all'insieme di credenziali dei servizi di ripristino.

    ![Preparare l'infrastruttura](./media/tutorial-backup-windows-server-to-azure/download-vault-credentials.png)
 
## <a name="install-and-register-the-agent"></a>Installare e registrare l'agente

1. Individuare e fare doppio clic sul file scaricato **MARSagentinstaller.exe**.
2. Si apre l'**Installazione guidata di Agente servizi di ripristino di Microsoft Azure**. Durante la procedura guidata, specificare le informazioni seguenti quando richiesto e scegliere **Registra**.
    - Percorso per l'installazione e la cartella della cache.
    - Informazioni sul server proxy se si usa un server proxy per connettersi a Internet.
    - Il nome utente e la password se si usa un proxy autenticato.

    ![Preparare l'infrastruttura](./media/tutorial-backup-windows-server-to-azure/mars-installer.png) 

3. Al termine della procedura guidata, fare clic su **Continua con la registrazione** e specificare il file dell'**Insieme di credenziali** che è stato scaricato nella procedura precedente.
 
4. Quando richiesto, specificare una passphrase di crittografia per crittografare i backup da Windows Server. Salvare la passphrase in una posizione sicura perché Microsoft non può recuperarla in caso di perdita.

5. Fare clic su **Fine**. 

## <a name="configure-backup-and-retention"></a>Configurare il backup e la conservazione

Usare l'agente di Servizi di ripristino di Microsoft Azure per la pianificazione dei backup di Windows Server in Azure. Eseguire la procedura seguente nel server in cui è stato scaricato l'agente.

1. Aprire l'agente di Servizi di ripristino di Microsoft Azure. È possibile trovarlo se si cerca **Backup di Microsoft Azure**nel computer.

2.  Nella console dell'agente di Servizi di ripristino fare clic su **Pianifica backup** nel **riquadro Azioni**.

    ![Preparare l'infrastruttura](./media/tutorial-backup-windows-server-to-azure/mars-schedule-backup.png)

3. Fare clic su **Avanti** per passare alla pagina **Seleziona elementi per backup**.

4. Fare clic su **Aggiungi elementi** e dalla finestra di dialogo visualizzata selezionare **Stato del sistema** e i file o le cartelle di cui si vuole eseguire il backup. Fare quindi clic su **OK**.

5. Fare clic su **Avanti**.

6. Nella pagina **Specificare la pianificazione del backup (stato del sistema)**  specificare l'ora del giorno o la settimana in cui deve essere eseguito il backup dello stato del sistema e fare clic su **Avanti**. 

7.  Nella pagina **Selezionare i criteri di conservazione (stato del sistema)** selezionare i criteri di conservazione per lo stato del sistema e fare cluc su **Avanti**.
8. Analogamente, selezionare il criterio di pianificazione e conservazione dei backup per i file e le cartelle selezionate. 
8.  Nella pagina **Scegliere il tipo di backup iniziale** lasciare selezionata l'opzione **Automaticamente tramite rete** e fare clic su **Avanti**.
9.  Nella pagina **Conferma** esaminare le informazioni e fare clic su **Fine**.
10. Dopo aver creato la pianificazione del backup tramite la procedura guidata, fare clic su **Chiudi**.

## <a name="perform-an-ad-hoc-back-up"></a>Eseguire un backup ad hoc

È stata definita la pianificazione di esecuzione dei processi di backup. Non è stato tuttavia eseguito il backup del server. Per un ripristino di emergenza è consigliabile eseguire un backup su richiesta per garantire la resilienza dei dati del server.

1.  Nella console dell'agente di Servizi di ripristino di Microsoft Azure, fare clic su **Esegui backup**.

    ![Preparare l'infrastruttura](./media/tutorial-backup-windows-server-to-azure/backup-now.png)

2.  Nell'**Esecuzione guidata backup**, selezionare **File e cartelle** o **Stato del sistema** e fare clic su **Avanti** 
3. Nella pagina **Conferma** riesaminare le impostazioni che la procedura guidata di **Esegui backup** userà per il backup del server. Fare clic su **Backup**.
4.  Fare clic su **Chiudi** per chiudere la procedura guidata. Se si chiude la procedura guidata prima che venga completato il processo di backup, l'esecuzione guidata proseguirà in background.
4.  Al termine del backup iniziale, viene visualizzato lo stato **Processo completato** nel riquadro **Processi** della console dell'agente di MARS.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato usato il portale di Azure per eseguire le operazioni seguenti: 
 
> [!div class="checklist"] 
> * Creare un insieme di credenziali di Servizi di ripristino 
> * Scaricare l'agente di Servizi di ripristino di Microsoft Azure 
> * Installare l'agente 
> * Configurare il backup per Windows Server 
> * Eseguire un backup su richiesta 

Passare all'esercitazione successiva per recuperare i file Azure in Windows Server

> [!div class="nextstepaction"] 
> [Restore files from Azure to Windows Server](./tutorial-backup-restore-files-windows-server.md) (Ripristinare i file da Azure in Windows Server) 

