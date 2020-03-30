---
title: Distribuire lo strumento di diagnostica per Desktop virtuale di Windows - AzureDeploy the diagnostics tool for Windows Virtual Desktop - Azure
description: Come distribuire lo strumento di diagnostica UX per Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4eb63fe4bd8f8a8b0961aa6a7fccb8de9b7c2f16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123399"
---
# <a name="deploy-the-diagnostics-tool"></a>Distribuire lo strumento di diagnostica

>[!IMPORTANT]
>A partire dal 16 marzo 2020, le query diagnostiche che hanno influito sull'esperienza utente a causa dell'aumento della domanda sul servizio. In questo modo lo strumento smettere di funzionare perché si basa su tali query per funzionare. Questo articolo verrà aggiornato quando le query di diagnostica sono nuovamente disponibili.
>
>Fino ad allora, ti consigliamo vivamente di [usare Log Analytics](diagnostics-log-analytics.md) per il monitoraggio continuo.

Ecco cosa può fare lo strumento di diagnostica per Windows Virtual Desktop:

- Cerca le attività di diagnostica (gestione, connessione o feed) per un singolo utente per un periodo di una settimana.
- Raccogliere le informazioni sull'host della sessione per le attività di connessione dall'area di lavoro di Log Analytics.Gather session host information for connection activities from your Log Analytics workspace.
- Esaminare i dettagli sulle prestazioni della macchina virtuale (VM) per un host specifico.
- Vedere quali utenti hanno eseguito l'accesso all'host della sessione.
- Inviare un messaggio agli utenti attivi su un host di sessione specifico.
- Disconnettere gli utenti da un host di sessione.

## <a name="prerequisites"></a>Prerequisiti

È necessario creare una registrazione dell'app di Azure Active Directory e un'area di lavoro di Log Analytics prima di poter distribuire il modello di Azure Resource Manager per lo strumento. L'utente o l'amministratore necessitano di queste autorizzazioni per eseguire questa operazione:You or the administrator need these permissions to do that:

- Proprietario della sottoscrizione di Azure
- Autorizzazione per creare risorse nella sottoscrizione di AzurePermission to create resources in your Azure subscription
- Autorizzazione per creare un'app di Azure ADPermission to create an Azure AD app
- Diritti di proprietario o collaboratore di Servizi Desktop remoto

È inoltre necessario installare questi due moduli di PowerShell prima di iniziare:You also need to install these two PowerShell modules before you get started:

- [Modulo di Azure PowerShell](/powershell/azure/install-az-ps?view=azps-2.4.0/)
- [Modulo di Azure ADAzure AD module](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0/)

Assicurati di avere il tuo ID di abbonamento pronto per quando accedi.

Dopo aver creato tutti gli elementi in ordine, è possibile creare la registrazione dell'app Azure AD.

## <a name="create-an-azure-active-directory-app-registration"></a>Creare una registrazione dell'app Azure Active DirectoryCreate an Azure Active Directory app registration

Questa sezione illustra come usare PowerShell per creare l'app Azure Active Directory con un'entità servizio e ottenere le autorizzazioni API per tale applicazione.

>[!NOTE]
>Le autorizzazioni API sono le autorizzazioni di Windows Virtual Desktop, Log Analytics e Microsoft Graph API vengono aggiunte all'applicazione Azure Active Directory.

1. Aprire PowerShell come amministratore.
2. Accedere ad Azure con un account con autorizzazioni proprietario o collaboratore per la sottoscrizione di Azure che si vuole usare per lo strumento di diagnostica:Sign in to Azure with an account that has Owner or Contributor permissions on the Azure subscription you would like to use for the diagnostics tool:
   ```powershell
   Login-AzAccount
   ```
3. Accedere ad Azure AD con lo stesso account:Sign in to Azure AD with the same account:
   ```powershell
   Connect-AzureAD
   ```
4. Passare al repository GitHub dei modelli RDS ed eseguire lo script **CreateADAppRegistrationforDiagnostics.ps1** in PowerShell.Go to the [RDS-Templates GitHub repo](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) and run the CreateADAppRegistrationforDiagnostics.ps1 script in PowerShell.
5.  Quando lo script ti chiede di assegnare un nome all'app, immetti un nome univoco per l'app.


Dopo che lo script viene eseguito correttamente, dovrebbe mostrare quanto segue nel relativo output:

-  Un messaggio che conferma l'app dispone ora di un'assegnazione di ruolo dell'entità servizio.
-  L'ID client e la chiave segreta client necessari per la distribuzione dello strumento di diagnostica.

Dopo aver registrato l'app, è necessario configurare l'area di lavoro di Log Analytics.Now that you've registered your app, it's time to configure your Log Analytics workspace.

## <a name="configure-your-log-analytics-workspace"></a>Configurare l'area di lavoro di Log Analytics

Per un'esperienza ottimale, è consigliabile configurare l'area di lavoro di Log Analytics con i contatori delle prestazioni seguenti che consentono di derivare le istruzioni dell'esperienza utente in una sessione remota. Per un elenco dei contatori consigliati con le soglie consigliate, vedere [Soglie dei contatori delle prestazioni](deploy-diagnostics.md#windows-performance-counter-thresholds)di Windows .

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>Creare un'area di lavoro di Azure Log Analytics usando PowerShellCreate an Azure Log Analytics workspace using PowerShell

È possibile eseguire uno script di PowerShell per creare un'area di lavoro di Log Analytics e configurare i contatori delle prestazioni di Windows consigliati per monitorare l'esperienza utente e le prestazioni dell'app.

>[!NOTE]
>Se si dispone già di un'area di lavoro di Log Analytics esistente realizzata senza lo script di PowerShell che si vuole usare, passare a Convalida i risultati dello script nel portale di [Azure.](#validate-the-script-results-in-the-azure-portal)

Per eseguire lo script di PowerShell:

1.  Aprire PowerShell come amministratore.
2.  Passare al repository GitHub dei modelli RDS ed eseguire lo script **CreateLogAnalyticsWorkspaceforDiagnostics.ps1** in PowerShell.Go to the [RDS-Templates GitHub repo](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) and run the CreateLogAnalyticsWorkspaceforDiagnostics.ps1 script in PowerShell.
3. Per i parametri inserire i valori seguenti:

    - In **ResourceGroupName**immettere il nome del gruppo di risorse.
    - Per **LogAnalyticsWorkspaceName**, immettere un nome univoco per l'area di lavoro di Log Analytics.
    - In **Posizione**immettere l'area di Azure in uso.
    - Immettere **l'ID sottoscrizione di Azure**, che è possibile trovare nel portale di Azure in **Sottoscrizioni**.

4. Immettere le credenziali di un utente con accesso amministratore delegato.
5. Accedere al portale di Azure con le credenziali dello stesso utente.
6. Annotare o memorizzare l'ID LogAnalyticsWorkspace per un secondo momento.
7. Se si configura l'area di lavoro log Analytics con lo script di PowerShell, i contatori delle prestazioni devono essere già configurati ed è possibile passare a Convalida i risultati dello script nel portale di [Azure.](#validate-the-script-results-in-the-azure-portal) In caso contrario, passare alla sezione successiva.

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>Configurare i contatori delle prestazioni di Windows nell'area di lavoro di Log Analytics esistenteConfigure Windows performance counters in your existing Log Analytics workspace

Questa sezione è per gli utenti che vogliono usare un'area di lavoro di Azure Log Analytics esistente creata senza lo script di PowerShell nella sezione precedente. Se non è stato utilizzato lo script, è necessario configurare manualmente i contatori delle prestazioni di Windows consigliati.

Ecco come configurare manualmente i contatori delle prestazioni consigliati:Here's how to manually configure the recommended performance counters:

1. Aprire il browser Internet e accedere al portale di [Azure](https://portal.azure.com/) con l'account amministrativo.
2. Passare quindi alle **aree** di lavoro di Log Analytics per esaminare i contatori delle prestazioni di Windows configurati.
3. Nella sezione **Impostazioni** selezionare **Impostazioni avanzate**.
4. Successivamente, passare a **Dati** > **di Windows Performance Counters** e aggiungere i seguenti contatori:

    -   Disco\*logico(\\) %Spazio libero
    -   Disco logico(C:)\\Lunghezza media coda del disco
    -   Memoria(\*\\) Mbyte disponibili
    -   Informazioni sul\*\\processore( ) Tempo processore
    -   Ritardo input utente\*\\per sessione( ) Ritardo massimo ingresso

Per altre informazioni sui contatori delle prestazioni, vedere [Origini dati sulle prestazioni di Windows e Linux in Monitoraggio di Azure.Learn](/azure/azure-monitor/platform/data-sources-performance-counters)more about the performance counters at Windows and Linux performance data sources in Azure Monitor .

>[!NOTE]
>Eventuali contatori aggiuntivi configurati non verranno visualizzati nello strumento di diagnostica stesso. Per visualizzarlo nello strumento di diagnostica, è necessario configurare il file di configurazione dello strumento. Le istruzioni su come eseguire questa operazione con l'amministrazione avanzata saranno disponibili in GitHub in un secondo momento.

## <a name="validate-the-script-results-in-the-azure-portal"></a>Convalidare i risultati dello script nel portale di AzureValidate the script results in the Azure portal

Prima di continuare a distribuire lo strumento di diagnostica, è consigliabile verificare che l'applicazione Azure Active Directory disponga delle autorizzazioni API e che l'area di lavoro di Log Analytics disponga dei contatori delle prestazioni di Windows preconfigurati.

### <a name="review-your-app-registration"></a>Esaminare la registrazione dell'appReview your app registration

Per assicurarti che la registrazione dell'app abbia le autorizzazioni API:

1. Aprire un browser e connettersi al portale di [Azure](https://portal.azure.com/) con l'account amministrativo.
2. Passare a **Azure Active Directory**.
3. Vai a **Registrazioni app** e seleziona **Tutte le applicazioni**.
4. Cercare la registrazione dell'app Azure AD con lo stesso nome di app immesso nel passaggio 5 di Creare una [registrazione dell'app Azure Active Directory.](deploy-diagnostics.md#create-an-azure-active-directory-app-registration)

### <a name="review-your-log-analytics-workspace"></a>Esaminare l'area di lavoro di Log AnalyticsReview your Log Analytics workspace

Per assicurarsi che nell'area di lavoro di Log Analytics siano configurati i contatori delle prestazioni di Windows preconfigurati:

1. Nel [portale di Azure](https://portal.azure.com/)passare alle **aree** di lavoro di Log Analytics per esaminare i contatori delle prestazioni di Windows configurati.
2. In **Impostazioni**selezionare **Impostazioni avanzate**.
3. Successivamente, passare a **Data** > **Windows Performance Counters**.
4. Assicurarsi che i seguenti contatori siano preconfigurati:

   - LogicalDisk(\*\\) %Free Space (Spazio libero): consente di visualizzare la quantità di spazio libero dello spazio totale utilizzabile sul disco come percentuale.
   - LogicalDisk(C:)\\Lunghezza media coda del disco: la lunghezza della richiesta di trasferimento su disco per l'unità C. Il valore non deve superare 2 per più di un breve periodo di tempo.
   - Memory(\*\\) Mbyte disponibili: la memoria disponibile per il sistema in megabyte.
   - Processor\*Information(\\) Tempo processore: la percentuale di tempo trascorso dal processore per eseguire un thread non inattivo.
   - Ritardo input utente\*\\per sessione( ) Ritardo massimo ingresso

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>Connettersi alle macchine virtuali nell'area di lavoro di Log AnalyticsConnect to VMs in your Log Analytics workspace

Per poter visualizzare l'integrità delle macchine virtuali, è necessario abilitare la connessione di Log Analytics.In order to be able to view the health of VMs, you'll need to enable the Log Analytics connection. Seguire questi passaggi per connettere le macchine virtuali:Follow these steps to connect your VMs:

1. Aprire un browser e accedere al portale di [Azure](https://portal.azure.com/) con l'account amministrativo.
2. Passare all'area di lavoro di Log Analytics.Go to your Log Analytics Workspace.
3. Nel riquadro sinistro, in Origini dati area di lavoro, selezionare **macchine virtuali**.
4. Selezionare il nome della macchina virtuale a cui connettersi.
5. Selezionare **Connetti**.

## <a name="deploy-the-diagnostics-tool"></a>Distribuire lo strumento di diagnostica

Per distribuire il modello Gestione risorse di Azure per lo strumento di diagnostica:To deploy the Azure Resource Management template for the diagnostics tool:

1.  Passare alla [pagina GitHub Azure RDS-Templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy).
2.  Distribuire il modello in Azure e seguire le istruzioni nel modello. Assicurarsi di avere a disposizione le seguenti informazioni:

    -   ID client
    -   Segreto client
    -   ID dell'area di lavoro Log Analytics

3.  Una volta forniti i parametri di input, accettare i termini e le condizioni, quindi selezionare **Acquista**.

La distribuzione richiederà da 2 a 3 minuti. Dopo la distribuzione, passare al gruppo di risorse e verificare che siano presenti le risorse del piano di servizio dell'app Web e dell'app.

Dopo di che, è necessario impostare l'URI di reindirizzamento.

### <a name="set-the-redirect-uri"></a>Impostare l'URI di reindirizzamento

Per impostare l'URI di reindirizzamento:

1.  Nel [portale di Azure](https://portal.azure.com/)passare a **Servizi app** e individuare l'applicazione creata.
2.  Vai alla pagina di panoramica e copia l'URL che trovi lì.
3.  Passare alle **registrazioni delle app** e selezionare l'app da distribuire.
4.  Nel riquadro sinistro, nella sezione Gestisci, selezionare **Autenticazione.**
5.  Inserisci l'URI di reindirizzamento desiderato nella casella di testo URI di **reindirizzamento,** quindi seleziona **Salva** nell'angolo in alto a sinistra del menu.
6. Selezionare **Web** nel menu a discesa in Tipo.
7. Immettere l'URL dalla pagina di panoramica dell'app e aggiungere **/security/signin-callback** alla fine. Ad esempio `https://<yourappname>.azurewebsites.net/security/signin-callback`.

   ![Pagina URI di reindirizzamento](media/redirect-uri-page.png)

8. Passare ora alle risorse di Azure, selezionare la risorsa Servizi app di Azure con il nome specificato nel modello e passare all'URL associato. Ad esempio, se il nome dell'app `contosoapp45`utilizzato nel modello <https://contosoapp45.azurewebsites.net>è , l'URL associato è .
9. Accedere usando l'account utente appropriato di Azure Active Directory.
10.   Selezionare **Accetto**.

## <a name="distribute-the-diagnostics-tool"></a>Distribuire lo strumento di diagnosticaDistribute the diagnostics tool

Prima di rendere disponibile lo strumento di diagnostica agli utenti, assicurarsi che dispongano delle autorizzazioni seguenti:

- Gli utenti devono disporre dell'accesso in lettura per l'analisi dei log. Per altre informazioni, vedere Introduzione a ruoli, autorizzazioni e sicurezza con Monitoraggio di Azure.For more information, see [Get started with roles, permissions, and security with Azure Monitor](/azure/azure-monitor/platform/roles-permissions-security).
-  Gli utenti devono inoltre disporre dell'accesso in lettura per il tenant di Windows Virtual Desktop (ruolo Lettore RDS). Per ulteriori informazioni, vedere [Accesso delegato in Windows Virtual Desktop](delegated-access-virtual-desktop.md).

È inoltre necessario fornire agli utenti le seguenti informazioni:

- URL dell'app
- I nomi del singolo tenant del gruppo tenant a cui possono accedere.

## <a name="use-the-diagnostics-tool"></a>Usare lo strumento di diagnostica

Dopo aver eseguito l'accesso all'account utilizzando le informazioni ricevute dall'organizzazione, avere l'UPN pronto per l'utente per il quale si desidera eseguire una query sulle attività. Una ricerca ti darà tutte le attività sotto il tipo di attività specificato che si è verificato nell'ultima settimana.

### <a name="how-to-read-activity-search-results"></a>Come leggere i risultati della ricerca di attività

Le attività vengono ordinate in base al timestamp, con l'attività più recente. Se i risultati restituiscono un errore, verificare innanzitutto se si tratta di un errore del servizio. Per gli errori del servizio, creare un ticket di supporto con le informazioni sull'attività per eseguire il debug del problema. Tutti gli altri tipi di errore possono in genere essere risolti dall'utente o dall'amministratore. Per un elenco degli scenari di errore più comuni e su come risolverli, vedere [Identificare e diagnosticare i problemi](diagnostics-role-service.md#common-error-scenarios).

>[!NOTE]
>Gli errori di servizio sono chiamati "errori esterni" nella documentazione collegata. Questo verrà modificato quando si aggiorna il riferimento di PowerShell.This will be changed when we update the PowerShell reference.

Le attività di connessione potrebbero avere più di un errore. È possibile espandere il tipo di attività per visualizzare eventuali altri errori rilevati dall'utente. Selezionare il nome del codice di errore per aprire una finestra di dialogo per visualizzare ulteriori informazioni su di esso.

### <a name="investigate-the-session-host"></a>Esaminare l'host della sessioneInvestigate the session host 

Nei risultati della ricerca individuare e selezionare l'host della sessione per il quale si desiderano informazioni.

È possibile analizzare l'integrità dell'host della sessione:You can analyze session host health:

- In base a una soglia predefinita, è possibile recuperare le informazioni sull'integrità dell'host della sessione query da Log Analytics.Based on a predefined threshold, you can retrieve the session host health information that Log Analytics queries.
- Quando non è presente alcuna attività o l'host della sessione non è connesso a Log Analytics, le informazioni non saranno disponibili.

È inoltre possibile interagire con gli utenti nell'host della sessione:You can also interact with users on the session host:

- È possibile disconnettersi o inviare un messaggio agli utenti connessi.
- L'utente che hai cercato in origine è selezionato per impostazione predefinita, ma puoi anche selezionare altri utenti per inviare messaggi o disconnettere più utenti contemporaneamente.

### <a name="windows-performance-counter-thresholds"></a>Soglie dei contatori delle prestazioni di Windows

- Disco\*Logico(\\) %Spazio libero:

    - Visualizza la percentuale dello spazio totale utilizzabile sul disco logico libero.
    - Soglia: meno del 20% è contrassegnato come non integro.

- Disco logico(C:)\\Lunghezza media coda del disco:

    - Rappresenta le condizioni del sistema di archiviazione.
    - Soglia: superiore a 5 è contrassegnato come non integro.

- Memoria(\*\\) Mbyte disponibili:

    - La memoria disponibile per il sistema.
    - Soglia: meno di 500 megabyte contrassegnati come non integri.

- Informazioni sul\*\\processore( ) Tempo processore:

    - Soglia: superiore all'80% è contrassegnato come non integro.

- [Ritardo input utente\*\\per sessione( ) Ritardo input massimo](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/):

    - Soglia: superiore a 2000 ms è contrassegnato come non integro.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come monitorare i log attività, vedere [Usare la diagnostica con Log Analytics.](diagnostics-log-analytics.md)
- Per informazioni sugli scenari di errore comuni, vedere [Identificare e diagnosticare i problemi.](diagnostics-role-service.md)
