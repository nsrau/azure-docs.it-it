---
title: Monitoraggio con test Web in più passi - Azure Application Insights
description: Configurare test Web in più passi per monitorare le applicazioni Web con Azure Application Insights
ms.topic: conceptual
ms.date: 05/26/2020
ms.openlocfilehash: 04361f7b3306c5f7c164a849d8b05d7cf4756999
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873341"
---
# <a name="multi-step-web-tests"></a>Test Web in più passaggi

È possibile monitorare una sequenza registrata di URL e interazioni con un sito Web tramite test Web in più passi. Questo articolo illustra il processo di creazione di un test Web in più passi con Visual Studio Enterprise.

> [!NOTE]
> I test Web in più passi dipendono dai file webtest di Visual Studio. È stato [annunciato](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/) che Visual Studio 2019 sarà l'ultima versione con la funzionalità test Web. È importante comprendere che, anche se non vengono aggiunte nuove funzionalità, la funzionalità test Web in Visual Studio 2019 è ancora attualmente supportata e continuerà a esserlo durante il ciclo di vita del prodotto. Il team del prodotto Monitoraggio di Azure ha affrontato le domande relative al futuro dei test di disponibilità in più passi [qui](https://github.com/MicrosoftDocs/azure-docs/issues/26050#issuecomment-468814101).  
> </br>
> I test Web in più passi **non sono supportati** nel cloud di [Azure per enti pubblici](https://docs.microsoft.com/azure/azure-government/).


## <a name="pre-requisites"></a>Prerequisiti

* Visual Studio 2017 Enterprise o versioni successive.
* Strumenti per test di carico e delle prestazioni Web di Visual Studio.

Per individuare il prerequisito degli strumenti di test. Avviare il **Programma di installazione di Visual Studio** > **Singoli componenti** > **Debug e test** > **Strumenti per test di carico e delle prestazioni Web**.

![Screenshot dell'interfaccia utente del programma di installazione di Visual Studio con Singoli componenti selezionato con una casella di controllo accanto all'elemento per Strumenti per test di carico e delle prestazioni Web](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> Ai test Web in più passi sono associati costi aggiuntivi. Per altre informazioni, vedere la [guida ai prezzi ufficiale](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="record-a-multi-step-web-test"></a>Registrare un test Web in più passi 

> [!WARNING]
> Non è più consigliabile usare la registrazione in più passi. La registrazione è stata sviluppata per le pagine HTML statiche con interazioni di base e non fornisce un'esperienza funzionale per le pagine Web moderne.

Per istruzioni sulla creazione di test Web di Visual Studio, vedere la [documentazione ufficiale di Visual Studio 2019](https://docs.microsoft.com/visualstudio/test/how-to-create-a-web-service-test?view=vs-2019).

## <a name="upload-the-web-test"></a>Caricare il test Web

1. Nel portale di Application Insights nel riquadro Disponibilità selezionare **Crea test** > **Tipo di test** > **Test Web in più passi**.

2. Impostare le posizioni di test, la frequenza e i parametri di avviso.

### <a name="frequency--location"></a>Frequenza e località

|Impostazione| Spiegazione
|----|----|----|
|**Frequenza test**| impostare la frequenza di esecuzione del test da ogni località di test. Con una frequenza predefinita di cinque minuti e cinque località di test, il sito verrà testato in media ogni minuto.|
|**Località di test**| Sono le posizioni da cui i server inviano richieste Web all'URL indicato. **Il numero minimo di località di test consigliate è cinque** per essere certi di poter distinguere i problemi del sito Web da quelli della rete. È possibile selezionare fino a 16 località.

### <a name="success-criteria"></a>Criteri di superamento

|Impostazione| Spiegazione
|----|----|----|
| **Timeout test** |ridurre questo valore per ricevere avvisi in merito alle risposte lente. Il test viene conteggiato come non riuscito se le risposte dal sito non sono state ricevute entro questo periodo. Se è stata selezionata l'opzione **Analizza richieste dipendenti**, è necessario che tutti gli script, i file di stile, le immagini e le altre risorse dipendenti siano stati ricevuti entro questo periodo.|
| **Risposta HTTP** | codice di stato restituito che indica un'operazione riuscita. 200 è il codice che indica che è stata restituita una normale pagina Web.|
| **Il contenuto corrisponde a** | Una stringa, ad esempio "Benvenuto". Verifichiamo che in ogni risposta ci una corrispondenza esatta di maiuscolo e minuscolo. Deve trattarsi di una stringa di testo normale, senza caratteri jolly. È importante ricordare che, se il contenuto cambia, potrebbe essere necessario aggiornare la stringa. **Con la corrispondenza del contenuto sono supportati solo i caratteri inglesi** |

### <a name="alerts"></a>Avvisi

|Impostazione| Spiegazione
|----|----|----|
|**Near real-time (anteprima)** | Si consiglia di usare gli avvisi near real-time. La configurazione di questo tipo di avviso viene eseguita dopo la creazione del test di disponibilità.  |
|**Classico** | Non è più consigliabile usare gli avvisi classici per i nuovi test di disponibilità.|
|**Soglia località di avviso**|Si consiglia un minimo di 3-5 posizioni. Il rapporto ottimale tra la soglia località di avviso e il numero di località di test è dato da **soglia località di avviso** = **numero di località di test - 2, con un numero minimo pari a cinque località di test.**|

## <a name="configuration"></a>Configurazione

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Inserimento di orari e numeri casuali nel test

Si supponga di voler testare uno strumento che riceva dati dipendenti dal tempo, come ad esempio valori di scorte da un feed esterno. Quando si registra il test Web, è necessario usare tempi specifici impostandoli come parametri del test, StartTime e EndTime.

![Screenshot di my awesome stock app](./media/availability-multistep/app-insights-72webtest-parameters.png)

Quando si esegue il test, si vuole che EndTime sia sempre l'ora corrente e StartTime 15 minuti fa.

Il plug-in data e ora del test Web consente di gestire i parametri di questi valori.

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

|Nome proprietà| Descrizione|
|----|-----|
| URI destinatario | URI del destinatario per il token SAML.  Si tratta dell'URI per il Servizio di controllo di accesso, inclusi lo spazio dei nomi e il nome host del servizio. |
| Password certificato | Password del certificato client che consentirà l'accesso alla chiave privata incorporata. |
| Certificato client  | Valore del certificato client con la chiave privata nel formato con codifica Base64. |
| Identificatore nome | Identificatore nome per il token |
| Non dopo | Periodo di validità del token.  Il valore predefinito è 5 minuti. |
| Non prima | Periodo di validità di un token creato nel passato (per risolvere gli sfasamenti dell'ora).  Il valore predefinito è (negativo) 5 minuti. |
| Nome parametro di contesto di destinazione | Parametro di contesto che riceverà l'asserzione generata. |


**Segreto client** Se l'app ha un percorso di accesso che prevede un segreto client, usare tale percorso. Un servizio che offre l'accesso con segreto client è ad esempio Azure Active Directory (AAD). In AAD, il segreto client è la chiave dell'app.

Ecco un test Web di esempio di un'app Web di Azure che usa una chiave dell'app:

![Screenshot di esempio](./media/availability-multistep/client-secret.png)

Ottenere il token da AAD usando il segreto client (AppKey).
Estrarre il token di connessione dalla risposta.
Chiamare l'API usando il token di connessione nell'intestazione dell'autorizzazione.
Verificare che il test Web sia effettivamente un client, ovvero che abbia una propria app in AAD, e usare i relativi valori ClientId e AppKey. Anche il servizio sottoposto a test ha una propria app in AAD, il cui URI ID app è riportato nel campo risorsa del test Web.

### <a name="open-authentication"></a>Autenticazione aperta
Un esempio di autenticazione aperta è l'accesso con il proprio account Microsoft o Google. Molte app che usano OAuth offrono l'alternativa del segreto client ed è quindi consigliabile ricercare prima di tutto tale possibilità.

Se il test deve eseguire l'accesso con OAuth, procedere come indicato di seguito:

Usare uno strumento come Fiddler per esaminare il traffico tra il Web browser, il sito di autenticazione e l'app.
Eseguire due o più accessi usando computer o browser diversi oppure a distanza di tempo, per lasciar scadere i token.
Mettendo a confronto le diverse sessioni, identificare il token restituito dal sito di autenticazione e poi passato al server applicazioni dopo l'accesso.
Registrare un test Web usando Visual Studio.
Impostare i parametri dei token quando questi vengono restituiti dall'autenticatore e usarli per l'esecuzione di query nel sito. Visual Studio prova a impostare i parametri del test, ma non imposta correttamente i parametri dei token.

## <a name="troubleshooting"></a>Risoluzione dei problemi

[Articolo sulla risoluzione dei problemi](troubleshoot-availability.md) dedicato.

## <a name="next-steps"></a>Passaggi successivi

* [Avvisi di disponibilità](availability-alerts.md)
* [Test Web di ping URL](monitor-web-app-availability.md)
