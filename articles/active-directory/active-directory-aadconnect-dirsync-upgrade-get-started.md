<properties
   pageTitle="Azure AD Connect: aggiornamento da DirSync | Microsoft Azure"
   description="Informazioni su come eseguire l'aggiornamento da DirSync ad Azure AD Connect. Questo articolo illustra i passaggi per l'aggiornamento da DirSync ad Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="08/19/2016"
   ms.author="andkjell;shoatman;billmath"/>

# Azure AD Connect: aggiornamento da DirSync
Azure AD Connect è il successore di DirSync. Questo argomento illustra come eseguire l'aggiornamento da DirSync. Questi passaggi non si applicano all'aggiornamento da un'altra versione di Azure AD Connect o da Azure AD Sync.

Prima di avviare l'installazione di Azure AD Connect, verificare di avere [scaricato Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) e completare i passaggi obbligatori illustrati in [Azure AD Connect: hardware e prerequisiti](active-directory-aadconnect-prerequisites.md). In particolare, è consigliabile leggere i punti seguenti, perché queste aree sono diverse rispetto a DirSync:

- Versioni di .NET e PowerShell richieste. Le versioni disponibili nel server devono essere più recenti di quelle richieste per DirSync.
- Configurazione del server proxy. Se si usa un server proxy per accedere a Internet, è necessario configurare questa impostazione prima dell'aggiornamento. In DirSync viene sempre usato il server proxy configurato per l'utente che ne esegue l'installazione, mentre Azure AD Connect usa le impostazioni del computer.
- È necessario che gli URL siano aperti nel server proxy. Per gli scenari di base questi URL sono supportati anche da DirSync. I requisiti sono gli stessi. Se si vogliono usare le nuove funzionalità incluse in Azure AD Connect, è necessario aprire alcuni nuovi URL.

Se non si esegue l'aggiornamento da DirSync, vedere la [documentazione correlata](#related-documentation) per altri scenari.

## Aggiornamento da DirSync
In base alla distribuzione corrente di DirSync, sono disponibili diverse opzioni di aggiornamento. Se il tempo di aggiornamento previsto è inferiore a 3 ore, è consigliabile eseguire un aggiornamento sul posto. Se il tempo di aggiornamento previsto è superiore a 3 ore, è consigliabile eseguire una distribuzione parallela in un altro server. Se secondo la stima sono disponibili più di 50.000 oggetti, per eseguire l'aggiornamento saranno necessarie più di 3 ore.

Scenario |  
---- | ----
[Aggiornamento sul posto](#in-place-upgrade) | Opzione preferita se l'aggiornamento richiede meno di 3 ore.
[Distribuzione parallela](#parallel-deployment) | Opzione preferita se l'aggiornamento richiede più di 3 ore.

>[AZURE.NOTE] Quando si prevede di eseguire l'aggiornamento da DirSync ad Azure AD Connect, non disinstallare DirSync manualmente prima dell'aggiornamento. Azure AD Connect leggerà ed eseguirà la migrazione della configurazione da DirSync e lo disinstallerà dopo aver esaminato il server.

**Aggiornamento sul posto** Il tempo previsto per completare l'aggiornamento viene visualizzato nella procedura guidata. Questa stima è basata sul presupposto che per completare l'aggiornamento di un database con 50.000 oggetti (utenti, contatti e gruppi) sono necessarie 3 ore. Se il numero di oggetti nel database risulta minore di 50.000, Azure AD Connect suggerisce un aggiornamento sul posto. Se si decide di continuare, le impostazioni correnti vengono applicate automaticamente durante l'aggiornamento e il server riprende la sincronizzazione attiva.

Se si vogliono eseguire una migrazione della configurazione e una distribuzione parallela, è possibile ignorare l'indicazione di eseguire l'aggiornamento sul posto. È ad esempio possibile aggiornare l'hardware e il sistema operativo. Per altre informazioni, vedere la sezione [Distribuzione parallela](#parallel-deployment).

**Distribuzione parallela** Se sono disponibili più di 50.000 oggetti, è consigliabile eseguire una distribuzione parallela. In questo modo, si evitano eventuali ritardi operativi agli utenti. L'installazione di Azure AD Connect cerca di stimare i tempi di inattività per l'aggiornamento, ma se l'aggiornamento di DirSync è già stato eseguito in passato, l'esperienza personale costituirà l'indicazione migliore.

### Configurazioni supportate di DirSync da aggiornare
Le modifiche della configurazione seguenti, supportate con DirSync, vengono aggiornate:

- Filtro unità organizzativa e dominio
- ID alternativo (UPN)
- Sincronizzazione password e impostazioni ibride di Exchange
- Impostazioni di Azure AD e di foresta/dominio
- Opzioni di filtro basate sugli attributi dell'utente

Non è possibile aggiornare la modifica seguente. Se è presente questa configurazione, l'aggiornamento viene bloccato:

- Modifiche di DirSync non supportate, ad esempio attributi rimossi e uso di una DLL di estensione personalizzata

![Aggiornamento bloccato](./media/active-directory-aadconnect-dirsync-upgrade-get-started/analysisblocked.png)

In questi casi è consigliabile installare un nuovo server Azure AD Connect in [modalità di staging](active-directory-aadconnectsync-operations.md#staging-mode) e verificare la configurazione precedente di DirSync e quella nuova di Azure AD Connect. Riapplicare le modifiche utilizzando la configurazione personalizzata, come descritto in [configurazione personalizzata della sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Le password usate da DirSync per gli account del servizio non possono essere recuperate e non ne viene eseguita la migrazione. Tali password vengono reimpostate durante l'aggiornamento.

### Passaggi generali per l'aggiornamento da DirSync ad Azure AD Connect

1. Avvio di Azure AD Connect
2. Analisi della configurazione di DirSync corrente
3. Raccolta della password amministratore globale di Azure AD
4. Raccolta delle credenziali per un account amministratore dell'organizzazione (usato solo durante l'installazione di Azure AD Connect)
5. Installazione di Azure AD Connect
    * Disinstallare DirSync o disabilitarlo temporaneamente
	* Installare Azure AD Connect
	* Avviare facoltativamente la sincronizzazione

Nei casi seguenti sono necessari altri passaggi:

* Si usa attualmente una versione completa di SQL Server, locale o remota
* Nell'ambito si trovano più di 50.000 oggetti per la sincronizzazione

## Aggiornamento sul posto

1. Avviare il programma di installazione di Azure AD Connect (MSI).
2. Leggere e accettare le condizioni di licenza e l'informativa sulla privacy. ![Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Welcome.png)
3. Fare clic su Avanti per avviare l'analisi dell'installazione di DirSync esistente. ![Analisi dell'installazione della sincronizzazione di directory esistente](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Analyze.png)
4. Al termine dell'analisi vengono visualizzate indicazioni su come procedere.
    - Se si usa SQL Server Express e sono disponibili meno di 50.000 oggetti, viene visualizzata la schermata seguente: ![Analisi completata. È possibile eseguire l'aggiornamento da DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReady.png)
    - Se si usa una versione completa di SQL Server per DirSync, viene invece visualizzata questa pagina: ![Analisi completata. È possibile eseguire l'aggiornamento da DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png) Le informazioni visualizzate riguardano il server di database SQL Server esistente usato da DirSync. Se necessario, apportare le modifiche appropriate. Fare clic su **Avanti** per continuare l'installazione.
    - Se sono disponibili più di 50.000 oggetti, viene invece visualizzata questa schermata: ![Analisi completata. È possibile eseguire l'aggiornamento da DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png) Per continuare con un aggiornamento sul posto, fare clic sulla casella di controllo accanto al messaggio **Continua ad aggiornare DirSync in questo computer**. Per eseguire invece una [distribuzione parallela](#parallel-deployment), esportare le impostazioni di configurazione di DirSync e spostarle nel nuovo server.
5. Fornire la password per l'account attualmente usato per la connessione ad Azure AD. Deve essere l'account usato attualmente da DirSync. ![Immettere le credenziali di Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToAzureAD.png) Se viene visualizzato un errore e si hanno problemi di connettività, vedere [Risolvere i problemi di connettività con Azure AD Connect](active-directory-aadconnect-troubleshoot-connectivity.md).
6. Fornire un account amministratore dell'organizzazione per Active Directory. ![Immettere le credenziali di ADDS.](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToADDS.png)
7. È ora possibile procedere alla configurazione. Quando si fa clic su **Aggiorna**, DirSync viene disinstallato e Azure AD Connect viene configurato e inizia la sincronizzazione. ![Pronto per la configurazione](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ReadyToConfigure.png)
8. Al termine dell'installazione, disconnettersi e accedere di nuovo a Windows prima di usare Synchronization Service Manager o l'editor delle regole di sincronizzazione oppure di apportare altre modifiche alla configurazione.

## Distribuzione parallela

### Esportare la configurazione di DirSync
**Distribuzione parallela con più di 50.000 oggetti**

Se sono presenti più di 50.000 oggetti, l'installazione di Azure AD Connect consiglia una distribuzione parallela.

Viene visualizzata una schermata simile alla seguente:

![Analisi completata](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Per procedere con la distribuzione parallela, è necessario seguire questa procedura:

- Fare clic sul pulsante **Esporta impostazioni**. Quando si installa Azure AD Connect in un server separato, viene eseguita la migrazione di queste impostazioni dall'installazione di DirSync corrente alla nuova installazione di Azure AD Connect.

Una volta esportate le impostazioni, è possibile chiudere la procedura guidata di Azure AD Connect nel server DirSync. Continuare con il passaggio successivo, per [installare Azure AD Connect in un server separato](#installation-of-azure-ad-connect-on-separate-server).

**Distribuzione parallela con meno di 50.000 oggetti**

Se sono presenti meno di 50.000 oggetti ma si desidera comunque eseguire una distribuzione parallela, seguire questa procedura:

1. Eseguire il programma di installazione di Azure AD Connect (MSI).
2. Quando viene visualizzata la schermata **Azure AD Connect**, chiudere l'installazione facendo clic su "X" nell'angolo superiore destro della finestra.
3. Aprire un prompt dei comandi.
4. Dall'installazione locale di Azure AD Connect (percorso predefinito: C:\\Programmi\\Microsoft Azure Active Directory Connect) eseguire il comando seguente: `AzureADConnect.exe /ForceExport`.
5. Fare clic sul pulsante **Esporta impostazioni**. Quando si installa Azure AD Connect in un server separato, viene eseguita la migrazione di queste impostazioni dall'installazione di DirSync corrente alla nuova installazione di Azure AD Connect.

![Analisi completata](./media/active-directory-aadconnect-dirsync-upgrade-get-started/forceexport.png)

Una volta esportate le impostazioni, è possibile chiudere la procedura guidata di Azure AD Connect nel server DirSync. Continuare con il passaggio successivo, per [installare Azure AD Connect in un server separato](#installation-of-azure-ad-connect-on-separate-server).

### Installare Azure AD Connect in un server separato

Quando si installa Azure AD Connect in un nuovo server, il sistema presuppone che si voglia eseguire un'installazione pulita di Azure AD Connect. Poiché si vuole usare la configurazione di DirSync, è necessario eseguire alcuni passaggi aggiuntivi:

1. Eseguire il programma di installazione di Azure AD Connect (MSI).
2. Quando viene visualizzata la schermata **Azure AD Connect**, chiudere l'installazione facendo clic su "X" nell'angolo superiore destro della finestra.
3. Aprire un prompt dei comandi.
4. Dall'installazione locale di Azure AD Connect (percorso predefinito: C:\\Programmi\\Microsoft Azure Active Directory Connect) eseguire il comando seguente: `AzureADConnect.exe /migrate`. Viene avviata l'installazione guidata di Azure AD Connect e viene visualizzata la schermata seguente: ![Immettere le credenziali di Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ImportSettings.png)
5. Selezionare il file di impostazioni esportato dall'installazione di DirSync.
6. Configurare tutte le opzioni avanzate, tra cui:
    - Un percorso di installazione personalizzato per Azure AD Connect.
	- Un'istanza esistente di SQL Server (per impostazione predefinita, Azure AD Connect installa SQL Server 2012 Express). Non usare la stessa istanza di database come server DirSync.
	- Un account di servizio usato per connettersi a SQL Server (se il database di SQL Server è remoto, deve essere un account di servizio del dominio). Queste opzioni possono essere visualizzate in questa schermata: ![Immettere le credenziali di Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/advancedsettings.png)
7. Fare clic su **Next**.
8. Nella pagina **Pronto per la configurazione** lasciare selezionata l'opzione **Avvia il processo di sincronizzazione non appena viene completata la configurazione**. Il server è in [modalità di staging](active-directory-aadconnectsync-operations.md#staging-mode), quindi le modifiche non vengono esportate in Azure AD.
9. Fare clic su **Installa**.
10. Al termine dell'installazione, disconnettersi e accedere di nuovo a Windows prima di usare Synchronization Service Manager o l'editor delle regole di sincronizzazione oppure di apportare altre modifiche alla configurazione.

>[AZURE.NOTE] Viene avviata la sincronizzazione tra Windows Server Active Directory e Azure Active Directory, ma non vengono esportate modifiche in Azure AD. Le modifiche possono essere esportare attivamente da un solo strumento di sincronizzazione alla volta. Questo stato è detto [modalità di staging](active-directory-aadconnectsync-operations.md#staging-mode).

### Verificare che Azure AD Connect sia pronto per avviare la sincronizzazione

Per verificare se Azure AD Connect è pronto a subentrare a DirSync, dal menu di avvio è necessario aprire **Synchronization Service Manager** nel gruppo **Azure AD Connect**.

Nell'applicazione passare alla scheda **Operazioni**. In questa scheda verificare che le operazioni seguenti siano state completate:

- Importazione in AD Connector
- Importazione in Azure AD Connector
- Sincronizzazione completa in AD Connector
- Sincronizzazione completa in Azure AD Connector

![Importazione e sincronizzazione completate](./media/active-directory-aadconnect-dirsync-upgrade-get-started/importsynccompleted.png)

Esaminare il risultato di tali operazioni e assicurarsi che non siano presenti errori.

Per visualizzare e controllare le modifiche che verranno esportate in Azure AD, vedere come verificare la configurazione in [Modalità di staging](active-directory-aadconnectsync-operations.md#staging-mode). Apportare le modifiche di configurazione necessarie fino a quando non si verificano eventi imprevisti.

Dopo avere completato questi passaggi e ottenuto il risultato desiderato, si è pronti a passare da DirSync ad Azure AD.

### Disinstallare DirSync (vecchio server)

- In **Programmi e funzionalità** trovare **Strumento di sincronizzazione di Microsoft Azure Active Directory**.
- Disinstallare lo **Strumento di sincronizzazione di Microsoft Azure Active Directory**.
- La disinstallazione può richiedere fino a 15 minuti.

Se si vuole disinstallare DirSync in un secondo momento, è anche possibile arrestare temporaneamente il server o disabilitare il servizio. Se si verifica un problema, questo metodo consente di riattivarlo. Tuttavia, non è previsto che il passaggio successivo non riesca, quindi non dovrebbe essere necessario usare questo metodo.

Quando DirSync è disinstallato o disabilitato, non è disponibile alcun server attivo per l'esportazione in Azure AD. Il passaggio successivo per abilitare Azure AD Connect deve essere completato prima che eventuali modifiche nell'istanza di Active Directory locale continuino a essere sincronizzate con Azure AD.

### Abilitare Azure AD Connect (nuovo server)
Al termine dell'installazione, la riapertura di Azure AD Connect consentirà di apportare modifiche aggiuntive alla configurazione. Avviare **Azure AD Connect** dal menu start o dal collegamento sul desktop. Assicurarsi di non eseguire nuovamente l'installazione di MSI.

Dovrebbe essere visualizzata la seguente schermata:

![Attività aggiuntive](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AdditionalTasks.png)

- Selezionare **Configurazione della modalità di gestione temporanea**.
- Disattivare la gestione temporanea deselezionando la casella di controllo **Modalità di gestione temporanea abilitata**.

![Immettere le credenziali di Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/configurestaging.png)

- Fare clic sul pulsante **Avanti**.
- Nella pagina di conferma fare clic sul pulsante **Installa**.

Azure AD Connect è ora il server attivo.

## Passaggi successivi
Dopo aver installato Azure AD Connect è possibile [verificare l'installazione e assegnare le licenze](active-directory-aadconnect-whats-next.md).

Altre informazioni su queste nuove funzionalità abilitate con l'installazione: [Aggiornamento automatico](active-directory-aadconnect-feature-automatic-upgrade.md), [Impedire eliminazioni accidentali](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) e [Azure AD Connect Health](active-directory-aadconnect-health-sync.md).

Altre informazioni su questi argomenti comuni: [utilità di pianificazione e come attivare la sincronizzazione](active-directory-aadconnectsync-feature-scheduler.md).

Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

## Documentazione correlata

Argomento |  
--------- | ---------
Panoramica di Azure AD Connect | [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md)
Eseguire l'aggiornamento da una versione precedente di Connect | [Eseguire l'aggiornamento da una versione precedente di Connect](active-directory-aadconnect-upgrade-previous-version.md)
Eseguire l'installazione mediante le impostazioni rapide | [Installazione rapida di Azure AD Connect](active-directory-aadconnect-get-started-express.md)
Eseguire l'installazione con le impostazioni personalizzate | [Installazione personalizzata di Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
Account usati per l'installazione | [Altre informazioni sugli account e le autorizzazioni di Azure AD Connect](active-directory-aadconnect-accounts-permissions.md)

<!---HONumber=AcomDC_0824_2016-->