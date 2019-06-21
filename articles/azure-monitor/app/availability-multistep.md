---
title: Monitorare l'applicazione web con i test web in più passi e Azure Application Insights | Microsoft Docs
description: Test web in più passaggi di configurazione per monitorare le applicazioni web con Azure Application Insights
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: d8bfe92af4e8afc4edae76efb2e1cb7b287c7aa9
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305140"
---
# <a name="multi-step-web-tests"></a>Test Web in più passaggi

È possibile monitorare una sequenza di URL registrata e le interazioni con un sito Web tramite i test web in più passi. Questo articolo illustrerà il processo di creazione di un test web in più passaggi con Visual Studio Enterprise.

> [!NOTE]
> Test web in più passi hanno costi aggiuntivi associati a essi. Per informazioni su altre consultare il [Guida prezzo ufficiale](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="pre-requisites"></a>Prerequisiti

* Visual Studio 2017 Enterprise o versione successiva.
* Prestazioni di Visual Studio web e gli strumenti di test di carico.

Per individuare il prerequisito strumenti test. Avviare il **programma di installazione di Visual Studio** > **i singoli componenti** > **debug e test**  >   **Strumenti per test di carico e prestazioni Web**.

![Screenshot del programma di installazione di Visual Studio dell'interfaccia utente con i singoli componenti selezionata con una casella di controllo accanto all'elemento per gli strumenti di test di carico e prestazioni Web](./media/availability-multistep/web-performance-load-testing.png)

## <a name="record-a-multi-step-web-test"></a>Registrare un test web in più passi

Per creare un test in più passaggi, registrare lo scenario con Visual Studio Enterprise, quindi caricare la registrazione in Application Insights. Application Insights riprodurrà lo scenario a intervalli prestabiliti e verifica la risposta.

> [!IMPORTANT]
> * Non è possibile usare funzioni codificate o cicli nei test. Il test deve essere interamente contenuto nello script con estensione webtest. È tuttavia possibile usare plug-in standard.
> * I test Web in più passaggi supportano solo caratteri inglesi. Se si usa Visual Studio in altre lingue, aggiornare il file di definizione dei test Web per tradurre o escludere i caratteri non inglesi.

Usare Visual Studio Enterprise per registrare una sessione Web.

1. Creare un progetto di Test di carico e prestazioni Web. **File** > **nuove** > **progetto** > **Visual C#**   >  **Test**

    ![Nuovo progetto di Visual Studio dell'interfaccia utente](./media/availability-multistep/vs-web-performance-and-load-test.png)

2. Aprire il `.webtest` file e avviare la registrazione.

    ![La registrazione dell'interfaccia utente test di Visual Studio](./media/availability-multistep/open-web-test.png)

3. Fare clic sui passaggi che il test per simulare come parte della registrazione.

    ![Registrazione dell'interfaccia utente del browser](./media/availability-multistep/record.png)

4. Modificare il test per:

    * Aggiungere convalide per verificare i codici di testo e di risposta ricevuti.
    * Rimuovere tutte le interazioni non necessarie. È inoltre è stato possibile rimuovere le richieste dipendenti per le immagini o aggiungere siti di rilevamento che non sono utili per gli utenti di prendere in considerazione il test ha esito positivo.
    
    Tenere presente che è possibile modificare solo lo script di test, è possibile aggiungere codice personalizzato o chiamare altri test web. Non inserire cicli nel test. È possibile utilizzare i plug-in del test web standard.

5. Eseguire il test in Visual Studio per convalidare e verificarne il che corretto funzionamento.

    Il programma di esecuzione del test Web apre un browser Web e ripete le azioni registrate. Assicurarsi che tutto funzioni come previsto.

## <a name="upload-the-web-test"></a>Caricare il test web

1. Nel portale di Application Insights nel riquadro disponibilità selezionare **creare testare** > **tipo Test** > **test web in più passi**.

2. Impostare le posizioni di test, la frequenza e i parametri di avviso.

### <a name="frequency--location"></a>& Percorso o di frequenza

|Impostazione| Spiegazione
|----|----|----|
|**Frequenza di test**| impostare la frequenza di esecuzione del test da ogni località di test. Con una frequenza predefinita di cinque minuti e cinque località di test, il sito verrà testato in media ogni minuto.|
|**Posizioni di test**| Sono le posizioni da cui i server inviano richieste web all'URL. **Il numero minimo di posizioni di test consigliata è pari a cinque** per assicurare che sia possibile distinguere i problemi nel sito Web da problemi di rete. È possibile selezionare fino a 16 località.

### <a name="success-criteria"></a>Criteri di successo

|Impostazione| Spiegazione
|----|----|----|
| **Timeout test** |ridurre questo valore per ricevere avvisi in merito alle risposte lente. Il test viene conteggiato come non riuscito se le risposte dal sito non sono state ricevute entro questo periodo. Se è stata selezionata l'opzione **Analizza richieste dipendenti**, è necessario che tutti gli script, i file di stile, le immagini e le altre risorse dipendenti siano stati ricevuti entro questo periodo.|
| **Risposta HTTP** | codice di stato restituito che indica un'operazione riuscita. 200 è il codice che indica che è stata restituita una normale pagina Web.|
| **Corrispondenza contenuto** | Una stringa, ad esempio "Benvenuto" Verifichiamo che in ogni risposta ci una corrispondenza esatta di maiuscolo e minuscolo. Deve trattarsi di una stringa di testo normale, senza caratteri jolly. È importante ricordare che, se il contenuto cambia, potrebbe essere necessario aggiornare la stringa. **Sono supportati solo i caratteri in lingua inglese con corrispondenza contenuto** |

### <a name="alerts"></a>Avvisi

|Impostazione| Spiegazione
|----|----|----|
|**NEAR Real Time (anteprima)** | È consigliabile usare gli avvisi Near Real Time. Configurazione di questo tipo di avviso viene eseguita dopo aver creato il test di disponibilità.  |
|**Classico** | Si consiglia di non più usando gli avvisi classici per nuovi test di disponibilità.|
|**Soglia località di avviso**|Si consiglia un minimo di 3-5 posizioni. È la relazione tra località di avviso soglia e il numero di posizioni di test ottima **soglia località di avviso** = **numero di posizioni di test - 2, con un minimo di cinque posizioni di test.**|

## <a name="advanced-configuration"></a>Configurazione avanzata

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Inserimento di orari e numeri casuali nel test di

Si supponga di voler testare uno strumento che riceva dati dipendenti dal tempo, come ad esempio valori di scorte da un feed esterno. Quando si registra il test Web, è necessario usare tempi specifici impostandoli come parametri del test, StartTime e EndTime.

![La schermata di fantastiche app predefinite](./media/availability-multistep/app-insights-72webtest-parameters.png)

Quando si esegue il test, si vuole che EndTime sia sempre l'ora corrente e StartTime 15 minuti fa.

Il plug-in Web Test Data ora consente di gestire in modo impostare questi parametri.

1. Aggiungere un plug-in del test Web per ciascun valore di parametro desiderato. Nella barra degli strumenti del test Web scegliere **Aggiungi plug-in test Web**.
    
    ![Aggiungi plug-in Test Web](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    In questo esempio vengono usate due istanze di Plug-in data e ora, una per "15 minuti fa" e l'altra per "ora".

2. Aprire le proprietà di ciascun plug-in. Assegnare un nome al plug-in e impostarlo in modo che usi l'ora corrente. Per uno di essi, impostare Aggiungi minuti = -15.

    ![Parametri di contesto](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. Nei parametri del test Web, usare {{nome plug-in}} per fare riferimento a un nome di plug-in.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Caricare quindi il test nel portale. Userà i valori dinamici ogni volta che verrà eseguito.

### <a name="dealing-with-sign-in"></a>Gestione degli accessi

Se gli utenti accedono all'app, è possibile simulare l'accesso in vari modi per testare le pagine usate per l'accesso. L'approccio da preferire dipende dal tipo di sicurezza fornito dall'app.

In tutti i casi è consigliabile creare un account nell'applicazione solo a scopo di test. Se possibile, limitare le autorizzazioni dell'account di test in modo che i test Web non possano influire in alcun modo sugli utenti reali.

**Semplice nome utente e password** registra un test web nel modo consueto. Eliminare prima di tutto i cookie.

**Autenticazione SAML** usare il plug-in SAML disponibile per i test web. Accedere il plug-in da...

**Segreto client** se l'app ha un percorso di accesso che prevede un segreto client, usare tale route. Un servizio che offre l'accesso con segreto client è ad esempio Azure Active Directory (AAD). In AAD, il segreto client è la chiave dell'app.

Ecco un test Web di esempio di un'app Web di Azure che usa una chiave dell'app:

![Screenshot di esempio](./media/availability-multistep/client-secret.png)

Ottenere il token da AAD usando il segreto client (AppKey).
Estrarre il token di connessione dalla risposta.
Chiamare l'API usando il token di connessione nell'intestazione dell'autorizzazione.
Assicurarsi che il test web sia effettivamente un client, vale a dire, ha una propria app in AAD e usare i relativi valori clientId e app chiave. Anche il servizio sottoposto a test ha una propria app in AAD, il cui URI ID app è riportato nel campo risorsa del test Web.

### <a name="open-authentication"></a>Autenticazione aperta
Un esempio di autenticazione aperta è l'accesso con il proprio account Microsoft o Google. Molte app che usano OAuth offrono l'alternativa del segreto client ed è quindi consigliabile ricercare prima di tutto tale possibilità.

Se il test deve eseguire l'accesso con OAuth, procedere come indicato di seguito:

Usare uno strumento come Fiddler per esaminare il traffico tra il Web browser, il sito di autenticazione e l'app.
Eseguire due o più accessi usando computer o browser diversi oppure a distanza di tempo, per lasciar scadere i token.
Mettendo a confronto le diverse sessioni, identificare il token restituito dal sito di autenticazione e poi passato al server applicazioni dopo l'accesso.
Registrare un test Web usando Visual Studio.
Impostare i parametri dei token quando questi vengono restituiti dall'autenticatore e usarli per l'esecuzione di query nel sito. Visual Studio prova a impostare i parametri del test, ma non imposta correttamente i parametri dei token.

## <a name="troubleshooting"></a>risoluzione dei problemi

Dedicata [risoluzione dei problemi di articolo](troubleshoot-availability.md).

## <a name="next-steps"></a>Passaggi successivi

* [Avvisi di disponibilità](availability-alerts.md)
* [Test web di ping URL](monitor-web-app-availability.md)
