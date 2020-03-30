---
title: Monitorare con test Web in più passaggi - Azure Application InsightsMonitor with multi-step web tests - Azure Application Insights
description: Configurare test Web in più passaggi per monitorare le applicazioni Web con Azure Application InsightsSet up multi-step web tests to monitor your web applications with Azure Application Insights
ms.topic: conceptual
ms.date: 10/23/2019
ms.reviewer: sdash
ms.openlocfilehash: 3b8baad127b16a1bd9d071d0c3d4df68da8c3304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655941"
---
# <a name="multi-step-web-tests"></a>Test Web in più passaggi

È possibile monitorare una sequenza registrata di URL e interazioni con un sito Web tramite test Web in più passaggi. Questo articolo illustra il processo di creazione di un test Web in più passaggi con Visual Studio Enterprise.

> [!NOTE]
> I test Web in più passaggi dipendono dai file di Webtest di Visual Studio.Multi-step web tests depend on Visual Studio webtest files. È stato [annunciato](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/) che Visual Studio 2019 sarà l'ultima versione con funzionalità webtest. È importante comprendere che, sebbene non verranno aggiunte nuove funzionalità, la funzionalità webtest in Visual Studio 2019 è ancora attualmente supportata e continuerà a essere supportata durante il ciclo di vita del supporto del prodotto. Il team del prodotto Azure Monitor ha affrontato le domande relative al futuro dei test di disponibilità in più passaggi [qui](https://github.com/MicrosoftDocs/azure-docs/issues/26050#issuecomment-468814101).  

## <a name="pre-requisites"></a>Prerequisiti

* Visual Studio 2017 Enterprise o versione successiva.
* Strumenti per il test di carico e prestazioni Web di Visual Studio.

Per individuare gli strumenti di test pre-requisiti. Avviare gli strumenti di debug**Individual components** > **e test** > di test delle prestazioni Web e di test delle prestazioni Web e del**test**di carico del programma di **installazione** > di Visual Studio .

![Screenshot dell'interfaccia utente del programma di installazione di Visual Studio con singoli componenti selezionati con una casella di controllo accanto all'elemento per gli strumenti di test delle prestazioni Web e del carico](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> Ai test Web a più fasi sono associati costi aggiuntivi. Per saperne di più consultare la [guida ufficiale ai prezzi](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="record-a-multi-step-web-test"></a>Registrare un test Web in più passaggi 

> [!WARNING]
> Non è più consigliabile utilizzare il registratore a più fasi. Il registratore è stato sviluppato per le pagine HTML statiche con interazioni di base e non fornisce un'esperienza funzionale per le pagine web moderne.

Per indicazioni sulla creazione di test Web di Visual Studio, consultare la documentazione ufficiale di [Visual Studio 2019.](https://docs.microsoft.com/visualstudio/test/how-to-create-a-web-service-test?view=vs-2019)

## <a name="upload-the-web-test"></a>Caricare il test Web

1. In the Application Insights portal on the Availability pane select **Create Test** > **Test type** > **Multi-step web test**.

2. Impostare le posizioni di test, la frequenza e i parametri di avviso.

### <a name="frequency--location"></a>Frequenza & posizione

|Impostazione| Spiegazione
|----|----|----|
|**Frequenza di prova**| impostare la frequenza di esecuzione del test da ogni località di test. Con una frequenza predefinita di cinque minuti e cinque località di test, il sito verrà testato in media ogni minuto.|
|**Posizioni dei test**| Sono i luoghi da cui i nostri server inviano richieste web al tuo URL. **Il nostro numero minimo di posizioni di prova consigliate è cinque** per assicurare che tu possa distinguere i problemi nel tuo sito web da problemi di rete. È possibile selezionare fino a 16 località.

### <a name="success-criteria"></a>Criteri di installazione riuscita

|Impostazione| Spiegazione
|----|----|----|
| **Timeout test** |ridurre questo valore per ricevere avvisi in merito alle risposte lente. Il test viene conteggiato come non riuscito se le risposte dal sito non sono state ricevute entro questo periodo. Se è stata selezionata l'opzione **Analizza richieste dipendenti**, è necessario che tutti gli script, i file di stile, le immagini e le altre risorse dipendenti siano stati ricevuti entro questo periodo.|
| **Risposta HTTP** | codice di stato restituito che indica un'operazione riuscita. 200 è il codice che indica che è stata restituita una normale pagina Web.|
| **Corrispondenza contenuto** | Una stringa, come "Benvenuto!" Verifichiamo che in ogni risposta ci una corrispondenza esatta di maiuscolo e minuscolo. Deve trattarsi di una stringa di testo normale, senza caratteri jolly. È importante ricordare che, se il contenuto cambia, potrebbe essere necessario aggiornare la stringa. **Solo i caratteri inglesi sono supportati con la corrispondenza del contenuto** |

### <a name="alerts"></a>Avvisi

|Impostazione| Spiegazione
|----|----|----|
|**Quasi in tempo reale (anteprima)** | Ti consigliamo di utilizzare avvisi quasi in tempo reale. La configurazione di questo tipo di avviso viene eseguita dopo la creazione del test di disponibilità.  |
|**Classico** | Non è più consigliabile usare gli avvisi classici per i nuovi test di disponibilità.|
|**Soglia posizione avviso**|Si consiglia un minimo di 3-5 posizioni. La relazione ottimale tra la soglia della posizione di avviso e il numero di posizioni di test è il numero di posizioni di **posizione** = **di avviso di posizioni di test - 2, con un minimo di cinque posizioni di test.**|

## <a name="configuration"></a>Configurazione

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Collegamento di tempo e numeri casuali nel test

Si supponga di voler testare uno strumento che riceva dati dipendenti dal tempo, come ad esempio valori di scorte da un feed esterno. Quando si registra il test Web, è necessario usare tempi specifici impostandoli come parametri del test, StartTime e EndTime.

![Il mio impressionante stock app screenshot](./media/availability-multistep/app-insights-72webtest-parameters.png)

Quando si esegue il test, si vuole che EndTime sia sempre l'ora corrente e StartTime 15 minuti fa.

Il plug-in Data/ora test Web consente di gestire i parametri.

1. Aggiungere un plug-in del test Web per ciascun valore di parametro desiderato. Nella barra degli strumenti del test Web scegliere **Aggiungi plug-in test Web**.
    
    ![Aggiungi plug-in test Web](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    In questo esempio vengono usate due istanze di Plug-in data e ora, una per "15 minuti fa" e l'altra per "ora".

2. Aprire le proprietà di ciascun plug-in. Assegnare un nome al plug-in e impostarlo in modo che usi l'ora corrente. Per uno di essi, impostare Aggiungi minuti = -15.

    ![Parametri di contesto](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. Nei parametri del test Web, usare {{nome plug-in}} per fare riferimento a un nome di plug-in.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Caricare quindi il test nel portale. Userà i valori dinamici ogni volta che verrà eseguito.

### <a name="dealing-with-sign-in"></a>Gestione degli accessi

Se gli utenti accedono all'app, è possibile simulare l'accesso in vari modi per testare le pagine usate per l'accesso. L'approccio da preferire dipende dal tipo di sicurezza fornito dall'app.

In tutti i casi è consigliabile creare un account nell'applicazione solo a scopo di test. Se possibile, limitare le autorizzazioni dell'account di test in modo che i test Web non possano influire in alcun modo sugli utenti reali.

**Nome utente e password semplici** Registrare un test web nel modo consueto. Eliminare prima di tutto i cookie.

**Autenticazione SAML**

|Nome proprietà| Descrizione|
|----|-----|
| URI gruppo di destinatari | URI del gruppo di destinatari per il token SAML.  Si tratta dell'URI per il servizio di controllo di accesso (ACS, Access Control Service), incluso lo spazio dei nomi ACS e il nome host. |
| Password certificato | Password per il certificato client che concederà l'accesso alla chiave privata incorporata. |
| Certificato client  | Valore del certificato client con chiave privata in formato codificato Base64. |
| Identificatore nome | Identificatore del nome per il token |
| Non dopo | Intervallo di tempo per il quale il token sarà valido.  Il valore predefinito è 5 minuti. |
| Non prima | L'intervallo di tempo per il quale un token creato in passato sarà valido (per risolvere gli sfapazioni di tempo).  Il valore predefinito è 5 minuti. |
| Nome parametro contesto di destinazione | Parametro di contesto che riceverà l'asserzione generata. |


**Segreto client** Se l'app ha una route di accesso che coinvolge un segreto client, usa tale route. Un servizio che offre l'accesso con segreto client è ad esempio Azure Active Directory (AAD). In AAD, il segreto client è la chiave dell'app.

Ecco un test Web di esempio di un'app Web di Azure che usa una chiave dell'app:

![Schermata di esempio](./media/availability-multistep/client-secret.png)

Ottenere il token da AAD usando il segreto client (AppKey).
Estrarre il token di connessione dalla risposta.
Chiamare l'API usando il token di connessione nell'intestazione dell'autorizzazione.
Assicurarsi che il test Web sia un client effettivo, ovvero che disponga di una propria app in AAD, e usare la chiave clientId e la chiave dell'app. Anche il servizio sottoposto a test ha una propria app in AAD, il cui URI ID app è riportato nel campo risorsa del test Web.

### <a name="open-authentication"></a>Autenticazione aperta
Un esempio di autenticazione aperta è l'accesso con il proprio account Microsoft o Google. Molte app che usano OAuth offrono l'alternativa del segreto client ed è quindi consigliabile ricercare prima di tutto tale possibilità.

Se il test deve eseguire l'accesso con OAuth, procedere come indicato di seguito:

Usare uno strumento come Fiddler per esaminare il traffico tra il Web browser, il sito di autenticazione e l'app.
Eseguire due o più accessi usando computer o browser diversi oppure a distanza di tempo, per lasciar scadere i token.
Mettendo a confronto le diverse sessioni, identificare il token restituito dal sito di autenticazione e poi passato al server applicazioni dopo l'accesso.
Registrare un test Web usando Visual Studio.
Impostare i parametri dei token quando questi vengono restituiti dall'autenticatore e usarli per l'esecuzione di query nel sito. Visual Studio prova a impostare i parametri del test, ma non imposta correttamente i parametri dei token.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Articolo dedicato [sulla risoluzione dei problemi](troubleshoot-availability.md).

## <a name="next-steps"></a>Passaggi successivi

* [Avvisi di disponibilitàAvailability Alerts](availability-alerts.md)
* [Test Web di ping url](monitor-web-app-availability.md)
