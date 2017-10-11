---
title: Eseguire il backup del server o della workstation di Windows in Azure (modello classico) | Microsoft Docs
description: Eseguire il backup dei server o dei client di Windows nell'insieme di credenziali di backup in Azure. Scorrere le nozioni di base per la protezione dei file e delle cartelle in un insieme di credenziali di backup tramite l'agente di Backup di Azure.
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: insieme di credenziali di backup; backup di un server Windows; backup di Windows;
ms.assetid: 3b543bfd-8978-4f11-816a-0498fe14a8ba
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: a8daa6a4655b72936b6299c0fa5b80459ffa5da3
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="back-up-a-windows-server-or-workstation-to-azure-using-the-classic-portal"></a>Eseguire il backup del server o della workstation di Windows Server tramite il portale classico
> [!div class="op_single_selector"]
> * [Portale classico](backup-configure-vault-classic.md)
> * [Portale di Azure](backup-configure-vault.md)
>
>

L'articolo illustra le procedure necessarie per preparare l'ambiente ed eseguire il backup di un server (o di una workstation) di Windows in Azure. Contiene anche considerazioni sulla distribuzione della soluzione di backup. Se si vuole provare Azure Backup per la prima volta, questo articolo illustra rapidamente come fare.

Azure offre due diversi modelli di distribuzione per creare e usare le risorse: Resource Manager e distribuzione classica. Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.

## <a name="before-you-start"></a>Prima di iniziare
Per eseguire il backup di un server o un client in Azure, è necessario un account Azure. Se non si ha un account, è possibile crearne uno [gratuito](https://azure.microsoft.com/free/) in pochi minuti.

## <a name="create-a-backup-vault"></a>Creare un insieme di credenziali per il backup
Per eseguire il backup di file e cartelle da un server o un client è necessario creare un insieme di credenziali di backup nell'area geografica in cui si intende archiviare i dati.

> [!IMPORTANT]
> A partire da marzo 2017, non è più possibile usare il portale classico per creare insiemi di credenziali di backup.
>
> È ora possibile aggiornare gli insiemi di credenziali di Backup ad insiemi di credenziali dei servizi di ripristino. Per altre informazioni, vedere l'articolo [Aggiornare un insieme di credenziali di Backup a un insieme di credenziali di Servizi di ripristino](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft consiglia di aggiornare gli insiemi di credenziali di Backup a insiemi di credenziali dei servizi di ripristino.<br/> Dopo il **15 ottobre 2017** non sarà più possibile usare PowerShell per creare insiemi di credenziali di backup. <br/> **A partire dal 1° novembre 2017**:
>- Eventuali insiemi di credenziali di Backup rimanenti verranno automaticamente aggiornati a insiemi di credenziali di servizi di ripristino
>- e non sarà più possibile accedere ai dati di backup nel portale classico. Sarà possibile invece usare il portale di Azure per accedere ai dati di backup negli insiemi di credenziali di servizi di ripristino.
>


## <a name="download-the-vault-credential-file"></a>Scaricare il file delle credenziali di insieme
Per eseguire il backup dei dati in Azure è necessario autenticare il computer locale con un insieme di credenziali di backup. L'autenticazione viene eseguita tramite le *credenziali dell'insieme di credenziali*. Il file delle credenziali di insieme delle credenziali viene scaricato dal portale classico tramite un canale sicuro. La chiave privata del certificato non viene conservata nel portale o nel servizio.

### <a name="to-download-the-vault-credential-file-to-a-local-machine"></a>Per scaricare le credenziali dell'insieme di credenziali in un computer locale
1. Fare clic su **Servizi di ripristino**nel riquadro di spostamento sinistro e selezionare l'insieme di credenziali per il backup creato.

    ![Completamento infrarossi](./media/backup-configure-vault-classic/rs-left-nav.png)
2. Nella pagina Avvio rapido fare clic su **Scarica credenziali di insieme**.

   Il portale genera una credenziale dell'insieme di credenziali usando una combinazione del nome dell'insieme e della data corrente. Il file delle credenziali dell'insieme di credenziali viene usato solo durante il flusso di lavoro di registrazione e scade dopo 48 ore.

   Il file delle credenziali dell'insieme di credenziali può essere scaricato dal portale.
3. Fare clic su **Salva** per scaricare il file delle credenziali dell'insieme di credenziali nella cartella Downloads dell'account locale. È anche possibile scegliere **Salva con nome** dal menu **Salva** per specificare un percorso per il file dell'insieme di credenziali.

   > [!NOTE]
   > Assicurarsi che il file delle credenziali dell'insieme di credenziali sia salvato in un percorso accessibile dal computer. Se viene archiviato in una condivisione di file o in un Server Message Block, controllare di avere le autorizzazioni per accedervi.
   >
   >

## <a name="download-install-and-register-the-backup-agent"></a>Scaricare, installare e registrare l'agente di Backup
Dopo aver creato l'insieme di credenziali per il backup e aver scaricato il file dell'insieme di credenziali, è necessario installare un agente in ogni computer Windows.

### <a name="to-download-install-and-register-the-agent"></a>Per scaricare, installare e registrare l'agente
1. Fare clic su **Servizi di ripristino**e selezionare l'insieme di credenziali per il backup da registrare con un server.
2. Nella pagina Avvio rapido fare clic su **Agente per Windows Server, System Center Data Protection Manager o client Windows**. Fare quindi clic su **Salva**.

    ![Salva agente](./media/backup-configure-vault-classic/agent.png)
3. Dopo aver scaricato il file MARSagentinstaller.exe, fare clic su **Esegui** oppure fare doppio clic sul file **MARSAgentInstaller.exe** nel percorso in cui è stato salvato.
4. Scegliere la cartella di installazione e la cartella della cache necessarie per l'agente e fare clic su **Avanti**. Il percorso della cache specificato deve avere uno spazio disponibile pari almeno al 5% dei dati di backup.
5. È possibile continuare a connettersi a Internet tramite le impostazioni predefinite del proxy.             Se si usa un server proxy per connettersi a Internet, nella pagina Configurazione proxy selezionare la casella di controllo **Utilizzare le impostazioni personalizzate del proxy** e specificare i dettagli del server proxy. Se si usa un proxy autenticato, digitare il nome utente e la password e fare clic su **Avanti**.
6. Fare clic su **Installa** per avviare l'installazione dell'agente. Per completare l'installazione, l'agente Backup installerà .NET Framework 4.5 e Windows PowerShell, se non è già installato.
7. Dopo aver installato l'agente fare clic su **Continua con la registrazione** per continuare con il flusso di lavoro.
8. Nella pagina Identificazione insieme di credenziali trovare e selezionare il file dell'insieme di credenziali scaricato in precedenza.

    Il file dell'insieme di credenziali è valido soltanto per 48 ore dopo essere stato scaricato dal portale. Se si verifica un errore in questa pagina, ad esempio "Il file delle credenziali dell'insieme di credenziali specificato è scaduto", accedere al portale e scaricare nuovamente il file delle credenziali dell'insieme.

    Verificare che il file delle credenziali dell'insieme sia disponibile in un percorso accessibile dall'applicazione di installazione. Se si verificano errori relativi all'accesso, copiare il file delle credenziali di insieme in un percorso temporaneo nello stesso computer e ripetere l'operazione.

    Se si verifica un errore di credenziali dell'insieme, ad esempio "È stato fornito un insieme di credenziali non valido", il file è danneggiato o non ha le credenziali più recenti associate al servizio di ripristino. Ripetere l'operazione dopo avere scaricato un nuovo file di archivio delle credenziali dal portale. Questo errore si può verificare anche quando l'utente fa clic ripetutamente sull'opzione **Scarica credenziale dell'insieme di credenziali** in rapida successione. In questo caso è valido solo l'ultimo file delle credenziali dell'insieme di credenziali.
9. Nella pagina Impostazione crittografia è possibile generare o specificare una passphrase composta da almeno 16 caratteri. Ricordarsi di salvare la passphrase in un luogo sicuro.
10. Fare clic su **Finish**. La Registrazione guidata server registra il server con Backup.

    > [!WARNING]
    > Se la passphrase viene persa o dimenticata, Microsoft non potrà offrire assistenza per il recupero dei dati di backup. L'utente è proprietario della passphrase di crittografia e Microsoft non ha visibilità sulla passphrase in uso. Salvare il file in un luogo sicuro, in quanto sarà necessario durante un'operazione di ripristino.
    >
    >

11. Dopo avere impostato la chiave di crittografia, lasciare selezionata la casella di controllo **Avvia agente dei servizi di ripristino di Microsoft Azure** e fare clic su **Chiudi**.

## <a name="complete-the-initial-backup"></a>Completare il backup iniziale
Il backup iniziale comprende due attività fondamentali:

* Creazione della pianificazione dei backup
* Primo backup di file e cartelle

Al termine del backup iniziale, il criterio di backup crea i punti di backup da usare per ripristinare i dati. I criteri di backup procedono in base alla pianificazione definita.

### <a name="to-schedule-the-backup"></a>Per pianificare il backup
1. Aprire l'agente Backup di Microsoft Azure. L'agente verrà aperto automaticamente se si lascia selezionata la casella di controllo **Avvia agente dei servizi di ripristino di Microsoft Azure** quando si chiude la Registrazione guidata server. È possibile trovarlo se si cerca **Backup di Microsoft Azure**nel computer.

    ![Avviare Azure Backup Agent](./media/backup-configure-vault-classic/snap-in-search.png)
2. Nell'agente di Backup fare clic su **Pianifica backup**.

    ![Pianificare un backup di Windows Server](./media/backup-configure-vault-classic/schedule-backup-close.png)
3. Nella pagina Guida introduttiva della Pianificazione guidata backup fare clic su **Avanti**.
4. Nella pagina Seleziona elementi per backup fare clic su **Aggiungi elementi**.
5. Selezionare i file e le cartelle di cui si vuole eseguire il backup e fare clic su **OK**.
6. Fare clic su **Avanti**.
7. Nella pagina **Specificare la pianificazione del backup** specificare la **pianificazione del backup** e fare clic su **Avanti**.

    È possibile pianificare backup giornalieri, da eseguire non più di tre volte al giorno, o settimanali.

    ![Elementi per il backup di Windows Server](./media/backup-configure-vault-classic/specify-backup-schedule-close.png)

   > [!NOTE]
   > Per altre informazioni su come specificare la pianificazione del backup vedere l'articolo [Usare Backup di Azure per sostituire l'infrastruttura basata su nastro](backup-azure-backup-cloud-as-tape.md).
   >
   >

8. Nella pagina **Seleziona criteri di conservazione** selezionare i **criteri di conservazione** per la copia di backup.

    I criteri di conservazione specificano il periodo di tempo per cui il backup verrà archiviato. Anziché specificare solo un "criterio semplice" per tutti i punti di backup, è possibile specificare criteri di conservazione diversi in base al momento in cui viene eseguito il backup. È possibile modificare i criteri di conservazione giornalieri, settimanali, mensili e annuali in base alle proprie esigenze.
9. Nella pagina Scegliere il tipo di backup iniziale selezionare il tipo di backup iniziale. Lasciare selezionata l'opzione **Automaticamente tramite la rete** e fare clic su **Avanti**.

    È possibile eseguire il backup automaticamente in rete oppure offline. Il resto di questo articolo descrive il processo di backup automatico. Se si preferisce eseguire un backup offline, vedere l'articolo [Flusso di lavoro di backup offline in Backup di Azure](backup-azure-backup-import-export.md) per altre informazioni.
10. Nella pagina Conferma esaminare le informazioni e fare clic su **Fine**.
11. Dopo aver creato la pianificazione del backup tramite la procedura guidata, fare clic su **Chiudi**.

### <a name="enable-network-throttling-optional"></a>Abilitare la limitazione della larghezza di banda (facoltativo)
L'agente di Backup consente di limitare la larghezza di banda. La limitazione controlla l'uso della larghezza di banda della rete durante il trasferimento dati. Questo controllo può essere utile se è necessario eseguire il backup dei dati durante l'orario di lavoro, ma senza che il processo di backup interferisca con il resto del traffico Internet. La limitazione si applica alle attività di backup e ripristino.

**Per abilitare la limitazione larghezza di banda**

1. Nell'agente di Backup fare clic su **Modifica proprietà**.

    ![Modifica proprietà](./media/backup-configure-vault-classic/change-properties.png)
2. Nella scheda **Limitazione larghezza di banda rete** selezionare la casella di controllo **Abilita la limitazione all'utilizzo della larghezza di banda Internet per le operazioni di backup**.

    ![Limitazione della larghezza di banda della rete](./media/backup-configure-vault-classic/throttling-dialog.png)
3. Dopo aver abilitato la limitazione, specificare la larghezza di banda consentita per trasferire i dati di backup durante le **ore lavorative** e le **ore non lavorative**.

    I valori della larghezza di banda partono da 512 kilobit al secondo (Kbps) e possono arrivare fino a 1.023 megabyte al secondo (Mbps). È anche possibile definire l'inizio e la fine delle **ore lavorative**e i giorni della settimana da considerare come giorni lavorativi. Gli orari al di fuori delle ore lavorative definite vengono considerati ore non lavorative.
4. Fare clic su **OK**.

### <a name="to-back-up-now"></a>Per esegui immediatamente il backup
1. Nell'agente di Backup fare clic su **Esegui backup** per completare il seeding iniziale sulla rete.

    ![Eseguire ora il backup di Windows Server](./media/backup-configure-vault-classic/backup-now.png)
2. Nella pagina Conferma riesaminare le impostazioni che l'Esecuzione guidata backup userà per il backup del computer. Fare clic su **Backup**.
3. Fare clic su **Chiudi** per chiudere la procedura guidata. Se quest'operazione viene svolta prima che venga completato il processo di backup, l'esecuzione guidata proseguirà in background.

Al termine del backup iniziale, nella console Backup comparirà lo stato **Processo completato** .

![Completamento infrarossi](./media/backup-configure-vault-classic/ircomplete.png)

## <a name="next-steps"></a>Passaggi successivi
* Sottoscrivere un [account Azure gratuito](https://azure.microsoft.com/free/).

Per altre informazioni sul backup di macchine virtuali o altri carichi di lavoro, vedere:

* [Eseguire il backup di macchine virtuali IaaS](backup-azure-vms-prepare.md)
* [Eseguire il backup dei carichi di lavoro con il server di Backup di Microsoft Azure](backup-azure-microsoft-azure-backup.md)
* [Eseguire il backup dei carichi di lavoro in Azure con Data Protection Manager](backup-azure-dpm-introduction.md)
