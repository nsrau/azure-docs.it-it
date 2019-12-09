---
title: Monitoraggio con test Web in più passaggi-applicazione Azure Insights
description: Configurare test Web in più passaggi per monitorare le applicazioni Web con applicazione Azure Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/23/2019
ms.reviewer: sdash
ms.openlocfilehash: 8e630f324a7a0ebdfcc74941e760b80fabefa8d3
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928959"
---
# <a name="multi-step-web-tests"></a>Test Web in più passi

È possibile monitorare una sequenza registrata di URL e interazioni con un sito Web tramite test Web in più passaggi. In questo articolo viene illustrato il processo di creazione di un test Web in più passaggi con Visual Studio Enterprise.

> [!NOTE]
> I test Web in più passaggi dipendono dai file di test Web di Visual Studio. È stato [annunciato](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/) che Visual Studio 2019 sarà l'ultima versione con funzionalità di test Web. È importante comprendere che, sebbene non vengano aggiunte nuove funzionalità, la funzionalità di test Web in Visual Studio 2019 è ancora attualmente supportata e continuerà a essere supportata durante il ciclo di vita del supporto del prodotto. Il team del prodotto monitoraggio di Azure ha risolto le domande relative al futuro dei test di [disponibilità in più passaggi](https://github.com/MicrosoftDocs/azure-docs/issues/26050#issuecomment-468814101).  

## <a name="pre-requisites"></a>Prerequisiti

* Visual Studio 2017 Enterprise o versione successiva.
* Strumenti per test di carico e prestazioni Web di Visual Studio.

Per individuare il prerequisito degli strumenti di test. Avviare il **Programma di installazione di Visual Studio** > **singoli componenti** > il **debug e il test** > **strumenti per test di carico e prestazioni Web**.

![Screenshot dell'interfaccia utente del programma di installazione di Visual Studio con singoli componenti selezionati con una casella di controllo accanto all'elemento per gli strumenti di test di carico e prestazioni Web](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> Ai test Web in più passaggi sono associati costi aggiuntivi. Per ulteriori informazioni, consultare la [Guida ai prezzi ufficiale](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="record-a-multi-step-web-test"></a>Registrare un test Web in più passaggi 

> [!WARNING]
> Non è più consigliabile usare la registrazione in più passaggi. Il registratore è stato sviluppato per le pagine HTML statiche con interazioni di base e non fornisce un'esperienza funzionale per le pagine Web moderne.

Per istruzioni sulla creazione di test Web di Visual Studio, vedere la [documentazione ufficiale di Visual studio 2019](https://docs.microsoft.com/visualstudio/test/how-to-create-a-web-service-test?view=vs-2019).

## <a name="upload-the-web-test"></a>Caricare il test Web

1. Nel portale di Application Insights nel riquadro disponibilità selezionare **Crea test** > **tipo di test** > **test Web**in più passaggi.

2. Impostare i percorsi dei test, la frequenza e i parametri di avviso.

### <a name="frequency--location"></a>Località & frequenza

|Impostazione| Spiegazione
|----|----|----|
|**Frequenza test**| impostare la frequenza di esecuzione del test da ogni località di test. Con una frequenza predefinita di cinque minuti e cinque località di test, il sito verrà testato in media ogni minuto.|
|**Percorsi test**| Sono le posizioni da cui i server inviano richieste Web all'URL. **Il numero minimo di posizioni di test consigliate è cinque** per garantire che sia possibile distinguere i problemi nel sito Web dai problemi di rete. È possibile selezionare fino a 16 località.

### <a name="success-criteria"></a>Criteri di superamento

|Impostazione| Spiegazione
|----|----|----|
| **Timeout test** |ridurre questo valore per ricevere avvisi in merito alle risposte lente. Il test viene conteggiato come non riuscito se le risposte dal sito non sono state ricevute entro questo periodo. Se è stata selezionata l'opzione **Analizza richieste dipendenti**, è necessario che tutti gli script, i file di stile, le immagini e le altre risorse dipendenti siano stati ricevuti entro questo periodo.|
| **Risposta HTTP** | codice di stato restituito che indica un'operazione riuscita. 200 è il codice che indica che è stata restituita una normale pagina Web.|
| **Corrispondenza contenuto** | Stringa, ad esempio "Welcome!" Verifichiamo che in ogni risposta ci una corrispondenza esatta di maiuscolo e minuscolo. Deve trattarsi di una stringa di testo normale, senza caratteri jolly. È importante ricordare che, se il contenuto cambia, potrebbe essere necessario aggiornare la stringa. **Con la corrispondenza del contenuto sono supportati solo i caratteri inglesi** |

### <a name="alerts"></a>Avvisi

|Impostazione| Spiegazione
|----|----|----|
|**Quasi in tempo reale (anteprima)** | Si consiglia di usare gli avvisi near-realtime. La configurazione di questo tipo di avviso viene eseguita dopo la creazione del test di disponibilità.  |
|**Classico** | Non è più consigliabile usare gli avvisi classici per i nuovi test di disponibilità.|
|**Soglia posizione avviso**|Si consiglia un minimo di 3-5 posizioni. La relazione ottimale tra la soglia della posizione di avviso e il numero di percorsi di test è la **soglia di posizione degli avvisi** = **numero di percorsi di test-2, con almeno cinque posizioni di test.**|

## <a name="configuration"></a>Configurazione

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Inserimento di tempi e numeri casuali nel test

Si supponga di voler testare uno strumento che riceva dati dipendenti dal tempo, come ad esempio valori di scorte da un feed esterno. Quando si registra il test Web, è necessario usare tempi specifici impostandoli come parametri del test, StartTime e EndTime.

![Schermata My Awesome Stock app](./media/availability-multistep/app-insights-72webtest-parameters.png)

Quando si esegue il test, si vuole che EndTime sia sempre l'ora corrente e StartTime 15 minuti fa.

Il plug-in data e ora del test Web consente di gestire i parametrizzazioni.

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

**Nome utente e password semplici** Registrare un test Web nel modo consueto. Eliminare prima di tutto i cookie.

**Autenticazione SAML**

|Nome proprietà| Description|
|----|-----|
| URI del gruppo di destinatari | URI del destinatario per il token SAML.  Si tratta dell'URI per il servizio di controllo di accesso (ACS), inclusi lo spazio dei nomi ACS e il nome host. |
| Password certificato | Password del certificato client che consentirà l'accesso alla chiave privata incorporata. |
| Certificato client  | Il valore del certificato client con la chiave privata nel formato con codifica Base64. |
| ID nome | Identificatore del nome per il token. |
| Non dopo | TimeSpan per il quale il token sarà valido.  Il valore predefinito è 5 minuti. |
| Non prima | TimeSpan per il quale un token creato in passato sarà valido (per risolvere gli sfasamento dell'ora).  Il valore predefinito è (negativo) 5 minuti. |
| Nome parametro di contesto di destinazione | Parametro di contesto che riceverà l'asserzione generata. |


**Segreto client** Se l'app ha una route di accesso che include un segreto client, usare tale route. Un servizio che offre l'accesso con segreto client è ad esempio Azure Active Directory (AAD). In AAD, il segreto client è la chiave dell'app.

Ecco un test Web di esempio di un'app Web di Azure che usa una chiave dell'app:

![Screenshot di esempio](./media/availability-multistep/client-secret.png)

Ottenere il token da AAD usando il segreto client (AppKey).
Estrarre il token di connessione dalla risposta.
Chiamare l'API usando il token di connessione nell'intestazione dell'autorizzazione.
Verificare che il test Web sia un client effettivo, ovvero che disponga di una propria app in AAD e che usi la chiave ClientID + app. Anche il servizio sottoposto a test ha una propria app in AAD, il cui URI ID app è riportato nel campo risorsa del test Web.

### <a name="open-authentication"></a>Autenticazione aperta
Un esempio di autenticazione aperta è l'accesso con il proprio account Microsoft o Google. Molte app che usano OAuth offrono l'alternativa del segreto client ed è quindi consigliabile ricercare prima di tutto tale possibilità.

Se il test deve eseguire l'accesso con OAuth, procedere come indicato di seguito:

Usare uno strumento come Fiddler per esaminare il traffico tra il Web browser, il sito di autenticazione e l'app.
Eseguire due o più accessi usando computer o browser diversi oppure a distanza di tempo, per lasciar scadere i token.
Mettendo a confronto le diverse sessioni, identificare il token restituito dal sito di autenticazione e poi passato al server applicazioni dopo l'accesso.
Registrare un test Web usando Visual Studio.
Impostare i parametri dei token quando questi vengono restituiti dall'autenticatore e usarli per l'esecuzione di query nel sito. Visual Studio prova a impostare i parametri del test, ma non imposta correttamente i parametri dei token.

## <a name="troubleshooting"></a>risoluzione dei problemi

Articolo dedicato alla [risoluzione dei problemi](troubleshoot-availability.md).

## <a name="next-steps"></a>Passaggi successivi

* [Avvisi di disponibilità](availability-alerts.md)
* [Test Web di ping URL](monitor-web-app-availability.md)
