---
title: Distribuire lo strumento di diagnostica per desktop virtuale Windows-Azure
description: Come distribuire lo strumento di diagnostica UX per desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: helohr
ms.openlocfilehash: d5f0dbf916096b608495c0cc1017d919616653d4
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899697"
---
# <a name="deploy-the-diagnostics-tool"></a>Distribuire lo strumento di diagnostica

Ecco cosa può fare lo strumento di diagnostica per desktop virtuale di Windows:

- Individuare le attività di diagnostica (gestione, connessione o feed) per un singolo utente in un periodo di una settimana.
- Raccogliere informazioni sull'host della sessione per le attività di connessione dall'area di lavoro Log Analytics.
- Esaminare i dettagli delle prestazioni di una macchina virtuale (VM) per un determinato host.
- Vedere quali utenti hanno eseguito l'accesso all'host sessione.
- Inviare un messaggio agli utenti attivi in un host sessione specifico.
- Disconnettersi gli utenti da un host sessione.

## <a name="prerequisites"></a>Prerequisiti

È necessario creare una registrazione dell'app Azure Active Directory e un'area di lavoro Log Analytics prima di poter distribuire il modello di Azure Resource Manager per lo strumento. L'utente o l'amministratore necessita di queste autorizzazioni per eseguire questa operazione:

- Proprietario della sottoscrizione di Azure
- Autorizzazione per la creazione di risorse nella sottoscrizione di Azure
- Autorizzazione per la creazione di un'app Azure AD
- Diritti di proprietario o collaboratore Servizi Desktop remoto

Prima di iniziare, è necessario installare anche questi due moduli di PowerShell:

- [Modulo di Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.4.0)
- [Modulo Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)

Assicurarsi che l'ID sottoscrizione sia pronto per l'accesso.

Dopo aver eseguito tutte le operazioni in ordine, è possibile creare la registrazione dell'app Azure AD.

## <a name="create-an-azure-active-directory-app-registration"></a>Creare una registrazione dell'app Azure Active Directory

Questa sezione illustra come usare PowerShell per creare l'app Azure Active Directory con un'entità servizio e ottenere le autorizzazioni per l'API.

>[!NOTE]
>Le autorizzazioni API sono desktop virtuali di Windows, Log Analytics e Microsoft Graph le autorizzazioni API vengono aggiunte all'applicazione Azure Active Directory.

1. Aprire PowerShell come amministratore.
2. Passare al [repository di GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) per i modelli di Servizi Desktop remoto ed eseguire lo script **create ad app Registration for Diagnostics. ps1** in PowerShell.
3.  Quando lo script richiede di assegnare un nome all'app, immettere un nome univoco per l'app.
4.  Lo script richiederà quindi di accedere con un account amministrativo. Immettere le credenziali di un utente con [accesso amministratore delegato](delegated-access-virtual-desktop.md). L'amministratore deve disporre dei diritti di proprietario o di collaboratore Servizi Desktop remoto.

Dopo che lo script è stato eseguito correttamente, nel relativo output dovrebbe essere visualizzato quanto segue:

-  Un messaggio che conferma che l'app dispone ora di un'assegnazione di ruolo dell'entità servizio.
-  ID client di stampa e chiave privata client necessari per la distribuzione dello strumento di diagnostica.

Ora che l'app è stata registrata, è necessario configurare l'area di lavoro Log Analytics.

## <a name="configure-your-log-analytics-workspace"></a>Configurare l'area di lavoro Log Analytics

Per la migliore esperienza possibile, è consigliabile configurare l'area di lavoro di Log Analytics con i contatori delle prestazioni seguenti che consentono di derivare le istruzioni dell'esperienza utente in una sessione remota. Per un elenco di contatori consigliati con le soglie suggerite, vedere soglie dei [contatori delle prestazioni di Windows](deploy-diagnostics.md#windows-performance-counter-thresholds).

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>Creare un'area di lavoro di Azure Log Analytics usando PowerShell

È possibile eseguire uno script di PowerShell per creare un'area di lavoro di Log Analytics e configurare i contatori delle prestazioni di Windows consigliati per monitorare le prestazioni dell'app e dell'esperienza utente.

>[!NOTE]
>Se è già presente un'area di lavoro Log Analytics esistente senza lo script di PowerShell che si vuole usare, andare avanti per convalidare [i risultati dello script nel portale di Azure](#validate-the-script-results-in-the-azure-portal).

Per eseguire lo script di PowerShell:

1.  Aprire PowerShell come amministratore.
2.  Passare al [repository di GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) per i modelli di Servizi Desktop remoto ed eseguire lo script **create LogAnalyticsWorkspace for Diagnostics. ps1** in PowerShell.
3. Per i parametri inserire i valori seguenti:

    - Per **ResourceGroupName**, immettere il nome del gruppo di risorse.
    - Per **LogAnalyticsWorkspaceName**, immettere un nome univoco per l'area di lavoro log Analytics.
    - Per **località**immettere l'area di Azure in uso.
    - Immettere l' **ID sottoscrizione di Azure**, che è possibile trovare nella portale di Azure in **sottoscrizioni**.

4. Immettere le credenziali di un utente con accesso amministratore delegato.
5. Accedere al portale di Azure con le stesse credenziali dell'utente.
6. Annotare o memorizzare l'ID LogAnalyticsWorkspace per un momento successivo.
7. Se si configura l'area di lavoro Log Analytics con lo script di PowerShell, i contatori delle prestazioni devono essere già configurati ed è possibile passare alla [convalida dei risultati dello script nel portale di Azure](#validate-the-script-results-in-the-azure-portal). In caso contrario, passare alla sezione successiva.

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>Configurare i contatori delle prestazioni di Windows nell'area di lavoro Log Analytics esistente

Questa sezione è destinata agli utenti che vogliono usare un'area di lavoro di Azure Log Analytics esistente creata senza lo script di PowerShell nella sezione precedente. Se lo script non è stato usato, è necessario configurare manualmente i contatori delle prestazioni di Windows consigliati.

Di seguito viene illustrato come configurare manualmente i contatori delle prestazioni consigliati:

1. Aprire il browser Internet e accedere al [portale di Azure](https://portal.azure.com/) con l'account amministrativo.
2. Passare quindi a **log Analytics aree di lavoro** per esaminare i contatori delle prestazioni di Windows configurati.
3. Nella sezione **Impostazioni** selezionare **Impostazioni avanzate**.
4. Passare quindi ai**contatori delle prestazioni di Windows** **Data** > e aggiungere i contatori seguenti:

    -   Disco logico (\*)\|% di spazio disponibile
    -   Disco logico (C:)\\AVG Lunghezza coda disco
    -   Memoria (\*)\\MByte disponibili
    -   Informazioni processore (\*)\\tempo processore
    -   Ritardo input utente per sessione (\*)\\-ritardo input massimo

Altre informazioni sui contatori delle prestazioni [nelle origini dati delle prestazioni di Windows e Linux in monitoraggio di Azure](/azure/azure-monitor/platform/data-sources-performance-counters).

>[!NOTE]
>Tutti i contatori aggiuntivi configurati non verranno visualizzati nello strumento di diagnostica. Per fare in modo che venga visualizzato nello strumento di diagnostica, è necessario configurare il file di configurazione dello strumento. Le istruzioni su come eseguire questa operazione con l'amministrazione avanzata saranno disponibili in GitHub in un secondo momento.

## <a name="validate-the-script-results-in-the-azure-portal"></a>Convalidare i risultati dello script nella portale di Azure

Prima di continuare a distribuire lo strumento di diagnostica, è consigliabile verificare che l'applicazione Azure Active Directory disponga di autorizzazioni API e che l'area di lavoro Log Analytics disponga dei contatori delle prestazioni di Windows preconfigurati.

### <a name="review-your-app-registration"></a>Verificare la registrazione dell'app

Per assicurarsi che la registrazione dell'app disponga delle autorizzazioni API:

1. Aprire un browser e connettersi alla [portale di Azure](https://portal.azure.com/) con l'account amministrativo.
2. Passare a **registrazioni app** e cercare la registrazione app Azure ad.

      ![Pagina delle autorizzazioni dell'API.](media/api-permissions-page.png)


### <a name="review-your-log-analytics-workspace"></a>Esaminare l'area di lavoro Log Analytics

Per assicurarsi che l'area di lavoro di Log Analytics disponga dei contatori delle prestazioni di Windows preconfigurati:

1. Nella [portale di Azure](https://portal.azure.com/)passare a **log Analytics aree di lavoro** per esaminare i contatori delle prestazioni di Windows configurati.
2. In **Impostazioni**selezionare **Impostazioni avanzate**.
3. Passare quindi ai**contatori delle prestazioni di Windows** **Data** > .
4. Verificare che i contatori seguenti siano preconfigurati:

   - Disco logico (\*)\|% di spazio disponibile: Visualizza la quantità di spazio libero disponibile sul disco come percentuale.
   - Disco logico (C:)\\AVG Lunghezza coda del disco: Lunghezza della richiesta di trasferimento del disco per l'unità C. Il valore non deve superare 2 per più di un breve periodo di tempo.
   - Memoria (\*)\\MByte disponibili: Memoria disponibile per il sistema in megabyte.
   - Informazioni processore (\*)\\tempo processore: la percentuale di tempo impiegato dal processore per eseguire un thread non inattivo.
   - Ritardo input utente per sessione (\*)\\-ritardo input massimo

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>Connettersi alle macchine virtuali nell'area di lavoro Log Analytics

Per poter visualizzare l'integrità delle macchine virtuali, è necessario abilitare la connessione Log Analytics. Per connettere le macchine virtuali, seguire questa procedura:

1. Aprire un browser e accedere al [portale di Azure](https://portal.azure.com/) con l'account amministrativo.
2. Passare all'area di lavoro Log Analytics.
3. Nel riquadro sinistro, in origini dati dell'area di lavoro, selezionare **macchine virtuali**.
4. Selezionare il nome della macchina virtuale a cui ci si vuole connettere.
5. Selezionare **Connessione**.

## <a name="deploy-the-diagnostics-tool"></a>Distribuire lo strumento di diagnostica

Per distribuire il modello di gestione risorse di Azure per lo strumento di diagnostica:

1.  Passare alla pagina Azure RDS-Templates di GitHub.
2.  Distribuire il modello in Azure e seguire le istruzioni riportate nel modello. Verificare che siano disponibili le informazioni seguenti:

    -   ID client
    -   Segreto client
    -   ID dell'area di lavoro Log Analytics

3.  Una volta specificati i parametri di input, accettare i termini e le condizioni, quindi selezionare **Acquista**.

La distribuzione importerà da 2 a 3 minuti. Al termine della distribuzione, passare al gruppo di risorse e assicurarsi che le risorse app Web e piano di servizio app siano presenti.

Successivamente, è necessario impostare l'URI di reindirizzamento.

### <a name="set-the-redirect-uri"></a>Impostare l'URI di Reindirizzamento

Per impostare l'URI di reindirizzamento:

1.  Nella [portale di Azure](https://portal.azure.com/)passare a **Servizi app** e individuare l'applicazione creata.
2.  Passare alla pagina Overview (panoramica) e copiare l'URL trovato.
3.  Passare a **registrazioni** per l'app e selezionare l'app che si vuole distribuire.
4.  Nel pannello sinistro, in Gestisci sezione, selezionare **autenticazione**.
5.  Immettere l'URI di reindirizzamento desiderato nella casella di testo **URI di reindirizzamento** , quindi selezionare **Salva** nell'angolo in alto a sinistra del menu.
6. Selezionare **Web** nel menu a discesa in tipo.
7. Immettere l'URL dalla pagina di panoramica dell'app e aggiungere **/Security/SignIn-callback** alla fine. Ad esempio: `https://<yourappname>.azurewebsites.net/security/signin-callback`.

   ![Pagina URI di Reindirizzamento](media/redirect-uri-page.png)

8. Passare ora alle risorse di Azure, selezionare la risorsa app Azure Services con il nome specificato nel modello e passare all'URL associato. Se, ad esempio, il nome dell'app usato nel modello era `contosoapp45`, l'URL associato è. <https://contosoapp45.azurewebsites.net>
9. Accedere usando l'account utente appropriato di Azure Active Directory.
10.   Selezionare **Accetto**.

## <a name="distribute-the-diagnostics-tool"></a>Distribuire lo strumento di diagnostica

Prima di rendere disponibile lo strumento di diagnostica agli utenti, verificare che dispongano delle autorizzazioni seguenti:

- Gli utenti hanno bisogno dell'accesso in lettura per log Analytics. Per altre informazioni, vedere [Introduzione a ruoli, autorizzazioni e sicurezza con monitoraggio di Azure](/azure/azure-monitor/platform/roles-permissions-security).
-  Per gli utenti è inoltre necessario l'accesso in lettura per il tenant desktop virtuale Windows (ruolo lettura Servizi Desktop remoto). Per ulteriori informazioni, vedere [l'accesso delegato nell'anteprima di desktop virtuale di Windows](delegated-access-virtual-desktop.md).

È anche necessario fornire agli utenti le informazioni seguenti:

- URL dell'app
- I nomi del singolo tenant del gruppo di tenant a cui possono accedere.

## <a name="use-the-diagnostics-tool"></a>Usare lo strumento di diagnostica

Dopo aver effettuato l'accesso al proprio account usando le informazioni ricevute dall'organizzazione, fare in modo che l'UPN sia pronto per l'utente per cui si vogliono eseguire query sulle attività. Una ricerca fornirà tutte le attività sottoposte al tipo di attività specificato nell'ultima settimana.

### <a name="how-to-read-activity-search-results"></a>Come leggere i risultati della ricerca di attività

Le attività sono ordinate in base al timestamp, con l'attività più recente. Se i risultati restituiscono un errore, controllare innanzitutto se si tratta di un errore del servizio. Per gli errori del servizio, creare un ticket di supporto con le informazioni sulle attività che consentono di eseguire il debug del problema. Tutti gli altri tipi di errore possono in genere essere risolti dall'utente o dall'amministratore. Per un elenco degli scenari di errore più comuni e per informazioni su come risolverli, vedere [identificare i problemi con la funzionalità di diagnostica](diagnostics-role-service.md#common-error-scenarios).

>[!NOTE]
>Gli errori del servizio sono detti "errori esterni" nella documentazione collegata. Questo verrà modificato quando si aggiorna il riferimento a PowerShell.

Le attività di connessione potrebbero avere più di un errore. È possibile espandere il tipo di attività per visualizzare eventuali altri errori che l'utente ha riscontrato. Selezionare il nome del codice di errore per aprire una finestra di dialogo per visualizzare altre informazioni su di essa.

### <a name="investigate-the-session-host"></a>Esaminare l'host sessione 

Nei risultati della ricerca trovare e selezionare l'host sessione per cui si vogliono ottenere informazioni.

È possibile analizzare l'integrità dell'host sessione:

- In base a una soglia predefinita, è possibile recuperare le informazioni sull'integrità dell'host della sessione che Log Analytics query.
- Quando non è presente alcuna attività o l'host sessione non è connesso a Log Analytics, le informazioni non saranno disponibili.

È anche possibile interagire con gli utenti nell'host sessione:

- È possibile disconnettersi o inviare un messaggio agli utenti che hanno eseguito l'accesso.
- L'utente cercato in origine è selezionato per impostazione predefinita, ma è anche possibile selezionare altri utenti per inviare messaggi o disconnettersi più utenti contemporaneamente.

### <a name="windows-performance-counter-thresholds"></a>Soglie del contatore delle prestazioni di Windows

- Disco logico (\*)\|% di spazio disponibile:

    - Visualizza la percentuale dello spazio totale utilizzabile nel disco logico che è disponibile.
    - Soglia: Minore del 20% è contrassegnato come non integro.

- Disco logico (C:)\\AVG Lunghezza coda del disco:

    - Rappresenta le condizioni del sistema di archiviazione.
    - Soglia: Maggiore di 5 è contrassegnato come non integro.

- Memoria (\*)\\MByte disponibili:

    - Memoria disponibile per il sistema.
    - Soglia: Inferiore a 500 megabyte contrassegnati come non integri.

- Informazioni processore (\*)\\tempo processore:

    - Soglia: Superiore al 80% è contrassegnato come non integro.

- Ritardo input [utente per sessione (\*)\\: ritardo input massimo](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters):

    - Soglia: Superiore a 2000 ms è contrassegnato come non integro.
