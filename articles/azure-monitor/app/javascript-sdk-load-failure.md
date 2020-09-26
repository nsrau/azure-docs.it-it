---
title: Risoluzione degli errori di caricamento dell'SDK per le applicazioni Web JavaScript-applicazione Azure Insights
description: Come risolvere un errore di caricamento dell'SDK per le applicazioni Web JavaScript
ms.topic: conceptual
author: MSNev
ms.author: newylie
ms.date: 06/05/2020
ms.custom: devx-track-js
ms.openlocfilehash: 30c7caef4143b1a7cdba959971ff7689f986cb9e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91333257"
---
# <a name="troubleshooting-sdk-load-failure-for-javascript-web-apps"></a>Risoluzione degli errori di caricamento SDK per app Web JavaScript

<!-- 
Editor Note: This link name above "SDK Load Failure" has a direct references by https://go.microsoft.com/fwlink/?linkid=2128109 which is embedded in the snippet and from the JavaScript Page. If you change this text you MUST also update these references.
-->

L'eccezione di errore di caricamento dell'SDK viene creata e segnalata dal frammento di codice JavaScript (v3 o versione successiva) quando rileva che lo script SDK non è stato scaricato o inizializzato. Semplicisticamente, il client (browser) degli utenti finali non è stato in grado di scaricare il Application Insights SDK o di inizializzare dalla pagina di hosting identificata e pertanto non verrà segnalato alcun evento o telemetria.

![Panoramica degli errori del browser portale di Azure](./media/javascript-sdk-load-failure/overview.png)

> [!NOTE]
> Questa eccezione è supportata in tutti i principali browser che supportano l'API fetch () o XMLHttpRequest. Questa operazione esclude IE 8 e le versioni precedenti, pertanto non si otterrà questo tipo di eccezione segnalato da tali browser (a meno che l'ambiente non includa un riempimento di recupero).

![Dettagli eccezione del browser](./media/javascript-sdk-load-failure/exception.png)

I dettagli dello stack includono le informazioni di base sugli URL usati dall'utente finale.

| Nome                      | Descrizione                                                                                                  |
|---------------------------|--------------------------------------------------------------------------------------------------------------|
| &lt;Endpoint rete CDN &nbsp;&gt; | URL usato (e non riuscito) per scaricare l'SDK.                                                      |
| &lt;Collegamento alla guida &nbsp;&gt;    | URL che collega alla documentazione per la risoluzione dei problemi (Questa pagina).                                              |
| &lt;&nbsp;URL host&gt;     | URL completo della pagina utilizzata dall'utente finale.                                                    |
| &lt;&nbsp;URL endpoint&gt; | L'URL usato per segnalare l'eccezione, questo valore può essere utile per identificare se è stato eseguito l'accesso alla pagina di hosting dalla rete Internet pubblica o da un cloud privato.

I motivi più comuni per questa eccezione si verificano:

- Errore di connettività di rete intermittente.
- Application Insights l'interruzione della rete CDN.
- Impossibile inizializzare l'SDK dopo il caricamento dello script.
- Application Insights rete CDN JavaScript è stata bloccata.

Un [errore di connettività di rete intermittente](#intermittent-network-connectivity-failure) è il motivo più comune di questa eccezione, soprattutto negli scenari di roaming per dispositivi mobili in cui gli utenti perdono la connettività di rete in modo intermittente.

Nelle sezioni seguenti viene descritto come risolvere ogni potenziale causa radice dell'errore.

> [!NOTE]
> Molti dei passaggi per la risoluzione dei problemi presuppongono che l'applicazione abbia il controllo diretto dello script/tag del frammento e della relativa &lt; &gt; configurazione restituita come parte della pagina HTML di hosting. In caso contrario, i passaggi identificati non verranno applicati per lo scenario in uso.

## <a name="intermittent-network-connectivity-failure"></a>Errore di connettività di rete intermittente

**Se si verificano errori di connettività di rete intermittenti, è possibile che si verifichino meno soluzioni e che in genere si risolvono in un breve periodo di tempo.** Ad esempio, se l'utente ricarica il sito (Aggiorna la pagina), i file vengono scaricati e memorizzati nella cache in locale finché non viene rilasciata una versione aggiornata.

> [!NOTE]
> Se questa eccezione è persistente e si verifica in molti utenti (diagnosticata da un livello rapido e sostenuto di questa eccezione segnalata) insieme a una riduzione dei dati di telemetria del client normali, _non è probabile_ che i problemi di connettività di rete intermittenti siano la vera causa del problema ed è necessario continuare a diagnosticare con gli altri possibili problemi noti.

Questa situazione che ospita l'SDK sulla propria rete CDN è improbabile che fornisca o riduca le occorrenze di questa eccezione, in quanto la rete CDN sarà interessata dallo stesso problema.

Lo stesso accade anche quando si usa l'SDK tramite la soluzione pacchetti NPM. Tuttavia, dal punto di vista degli utenti finali quando si verifica questa situazione, l'intera applicazione non viene caricata/inizializzata (anziché _solo_ l'SDK di telemetria, che non è visibile visivamente), quindi probabilmente aggiornerà il sito fino a quando non viene caricato completamente.

È anche possibile provare a usare i [pacchetti NPM](#use-npm-packages-to-embed-the-application-insight-sdk) per incorporare il Application Insights SDK.

Per ridurre al minimo gli errori di connettività di rete intermittenti, sono state implementate le intestazioni Cache-Control su tutti i file della rete CDN in modo che, una volta che il browser dell'utente finale abbia scaricato la versione corrente dell'SDK, non sarà necessario eseguire di nuovo il download e il browser riutilizzerà la copia precedentemente ottenuta (vedere funzionamento della [memorizzazione nella cache](../../cdn/cdn-how-caching-works.md)). Se il controllo della memorizzazione nella cache ha esito negativo o è presente una nuova versione, il browser dell'utente finale dovrà scaricare la versione aggiornata. Per questo motivo, è possibile che venga visualizzato un livello di _"rumore"_ in background nello scenario di verifica dell'errore o un picco temporaneo quando si verifica una nuova versione e che viene reso disponibile a livello generale (distribuito nella rete CDN).
 
## <a name="application-insights-cdn-outage"></a>Interruzione Application Insights rete CDN

È possibile verificare se esiste un Application Insights interruzione della rete CDN tentando di accedere all'endpoint della rete CDN direttamente dal browser, ad esempio https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js) da una posizione diversa da quella degli utenti finali, probabilmente dal computer di sviluppo (presupponendo che l'organizzazione non abbia bloccato questo dominio).

Se si conferma che si verifica un'interruzione, è possibile [creare un nuovo ticket di supporto](https://azure.microsoft.com/support/create-ticket/) oppure provare a modificare l'URL usato per scaricare l'SDK.

### <a name="change-the-cdn-endpoint"></a>Modificare l'endpoint della rete CDN
  
Poiché il frammento e la relativa configurazione vengono restituiti dall'applicazione come parte di ogni pagina generata, è possibile modificare la configurazione del frammento `src` per usare un URL diverso per l'SDK. Con questo approccio, è possibile ignorare il problema di blocco della rete CDN perché il nuovo URL non deve essere bloccato.

Endpoint della rete CDN Application Insights JavaScript SDK correnti
- `https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js`
- `https://js.monitor.azure.com/scripts/b/ai.2.min.js`

> [!NOTE]
> L' `https://js.monitor.azure.com/` endpoint è un alias che consente di passare tra i provider della rete CDN entro circa 5 minuti, senza dover modificare alcuna configurazione. Questo consente di risolvere più rapidamente i problemi rilevati della rete CDN se un provider della rete CDN presenta problemi a livello di area o globale senza richiedere a tutti di modificare le impostazioni.

## <a name="sdk-failed-to-initialize-after-loading-the-script"></a>Impossibile inizializzare SDK dopo il caricamento dello script

Quando l'SDK non è in grado di inizializzare, lo &lt; script/ &gt; è stato scaricato correttamente dalla rete CDN, ma si verifica un errore durante l'inizializzazione. Ciò può essere dovuto a dipendenze mancanti o non valide o a una forma di eccezione JavaScript.

La prima cosa da verificare è se l'SDK è stato scaricato correttamente. se lo script non è stato scaricato, questo scenario __non__ è la ragione dell'eccezione di errore di caricamento dell'SDK.

Controllo rapido: usando un browser che supporta gli strumenti di sviluppo (F12), verificare nella scheda rete che lo script definito nella ```src``` configurazione del frammento sia stato scaricato con un codice di risposta 200 (esito positivo) o 304 (non modificato). È anche possibile usare uno strumento come Fiddler per esaminare il traffico di rete.

Le sezioni seguenti includono diverse opzioni per la creazione di report. si consiglia di creare un ticket di supporto o di generare un problema in GitHub.

 Regole di report di base:

- Se il problema riguarda solo un numero ridotto di utenti e uno specifico o un subset di versioni del browser (verificare i dettagli sull'eccezione segnalata), è probabile che si verifichi un solo utente finale o un ambiente, che probabilmente richiederà all'applicazione di fornire altre `polyfill` implementazioni. Per questi [file, inviare un problema in GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues).
- Se il problema riguarda l'intera applicazione e tutti gli utenti sono interessati, probabilmente si tratta di un problema relativo alla versione e pertanto è necessario [creare un nuovo ticket di supporto](https://azure.microsoft.com/support/create-ticket/).

### <a name="javascript-exceptions"></a>Eccezioni JavaScript

Per prima cosa, è possibile verificare la presenza di eccezioni JavaScript, usando un browser che supporta gli strumenti di sviluppo (F12) per caricare la pagina ed esaminare se si sono verificate eccezioni.

Se sono presenti eccezioni segnalate nello script SDK (ad esempio ai.2.min.js), questo può indicare che la configurazione passata nell'SDK contiene una configurazione richiesta imprevista o mancante oppure che nella rete CDN è stata distribuita una versione non funzionante.

Per verificare la presenza di errori di configurazione, modificare la configurazione passata al frammento (se non è già presente) in modo che includa solo la chiave di strumentazione come valore stringa.

> src: " https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js ",<br />
> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY"<br />
> }});<br />

Se quando si usa questa configurazione minima si sta ancora visualizzando un'eccezione JavaScript nello script SDK, [creare un nuovo ticket di supporto](https://azure.microsoft.com/support/create-ticket/) in quanto questa operazione richiederà il rollback della compilazione non funzionante, perché probabilmente si tratta di un problema con una versione appena distribuita.

Se l'eccezione scompare, il problema è probabilmente causato da una mancata corrispondenza del tipo o da un valore imprevisto. Iniziare ad aggiungere le opzioni di configurazione indietro una alla volta e testare finché l'eccezione non si ripete. Controllare quindi la documentazione relativa all'elemento che ha causato il problema. Se la documentazione non è chiara o è necessaria assistenza, [archiviare un problema in GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues).

Se la configurazione in precedenza è stata distribuita e funzionante ma è stata appena avviata la segnalazione di questa eccezione, potrebbe trattarsi di un problema con una versione appena distribuita, controllare se influisce solo su un piccolo set di utenti/browser e [inviare un problema in GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues) o  [creare un nuovo ticket di supporto](https://azure.microsoft.com/support/create-ticket/).

### <a name="enable-console-debugging"></a>Abilita il debug della console

Supponendo che non vi siano eccezioni generate, il passaggio successivo consiste nell'abilitare il debug della console aggiungendo l' `loggingLevelConsole` impostazione alla configurazione, in modo da inviare tutti gli errori e gli avvisi di inizializzazione alla console del browser (normalmente disponibile tramite gli strumenti di sviluppo (F12)). Gli eventuali errori segnalati devono essere autoesplicativi e, se è necessario ulteriore assistenza, segnalare [un problema in GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues).

> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY",<br />
> loggingLevelConsole: 2<br />
> }});<br />

> [!NOTE]
> Durante l'inizializzazione l'SDK esegue alcuni controlli di base per le dipendenze principali note. Se non vengono forniti dal runtime corrente, verranno segnalati gli errori come messaggi di avviso alla console, ma solo se `loggingLevelConsole` è maggiore di zero.

Se l'inizializzazione non riesce ancora, provare ad abilitare l' ```enableDebug``` impostazione di configurazione. In questo modo, tutti gli errori interni verranno generati come un'eccezione, che causerà la perdita di dati di telemetria. Poiché si tratta di un'impostazione solo per gli sviluppatori, è probabile che si verifichino problemi con le eccezioni generate come parte di alcuni controlli interni. sarà quindi necessario esaminare ogni eccezione per determinare il problema che causa l'esito negativo dell'SDK. Usare la versione non minimizzati dello script (si noti l'estensione sotto ". js" e non ".min.js") altrimenti le eccezioni saranno illeggibili.

> [!WARNING]
> Si tratta di un'impostazione solo per gli sviluppatori e non deve mai essere abilitata in un ambiente di produzione completo perché i dati di telemetria andranno persi.

> src: " https://az416426.vo.msecnd.net/scripts/b/ai.2.js ",<br />
> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY",<br />
> enableDebug: true<br />
> }});<br />

Se non vengono ancora fornite informazioni dettagliate, è necessario [inviare un problema in GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues) con i dettagli e un sito di esempio, se disponibile. Includere la versione del browser, il sistema operativo e i dettagli del framework JS per facilitare l'identificazione del problema.

## <a name="the-application-insights-javascript-cdn-has-been-blocked"></a>La rete CDN JavaScript Application Insights è stata bloccata

La rete CDN bloccata è possibile quando un endpoint della rete CDN Application Insights JavaScript SDK è stato segnalato e/o identificato come non sicuro. Quando si verifica questa situazione, l'endpoint verrà visualizzato in blocco pubblicamente e gli utenti di questi elenchi inizieranno a bloccare tutti gli accessi.

Per risolvere il problemi, è necessario che il proprietario dell'endpoint della rete CDN funzioni con l'entità elenco di blocchi che ha contrassegnato l'endpoint come non sicuro, in modo che possa essere rimosso dall'elenco pertinente.

Controllare se l'endpoint della rete CDN è stato identificato come non sicuro.
- [Report di Google Transparency](https://transparencyreport.google.com/safe-browsing/search)
- [VirusTotal](https://www.virustotal.com/gui/home/url)
- [Sucuri SiteCheck](https://sitecheck.sucuri.net/)

A seconda della frequenza con cui l'applicazione, il firewall o l'ambiente aggiorna le copie locali di questi elenchi, potrebbe richiedere una notevole quantità di tempo e/o richiedere l'intervento manuale da parte degli utenti finali o dei reparti IT aziendali per forzare un aggiornamento o consentire in modo esplicito agli endpoint della rete CDN di risolvere il problema.

Se l'endpoint della rete CDN viene identificato come unsafe, [creare un ticket di supporto](https://azure.microsoft.com/support/create-ticket/) per assicurarsi che il problema venga risolto il prima possibile.

Per ignorare *potenzialmente* questo problema più rapidamente, è possibile [modificare l'endpoint della rete CDN SDK](#change-the-cdn-endpoint).

### <a name="application-insights-javascript-cdn-is-blocked-by-end-user---blocked-by-browser-installed-blocker-personal-firewall"></a>Application Insights rete CDN JavaScript è bloccata (dall'utente finale bloccata dal browser; blocco installato; Personal Firewall)

Controllare se gli utenti finali hanno:

- È stato installato un plug-in del browser (in genere una forma di ad, malware o blocco popup).
- Bloccati (o non consentiti) gli endpoint della rete CDN Application Insights nel browser o nel proxy.
- È stata configurata una regola del firewall che causa il blocco del dominio della rete CDN per l'SDK (oppure la voce DNS non è stata risolta).

Se è stata configurata una di queste opzioni, sarà necessario lavorare con loro (o fornire la documentazione) per consentire gli endpoint della rete CDN.

È possibile che il plug-in installato usi il blocco public. In caso contrario, è molto probabile che si tratti di una soluzione configurata manualmente o che usi un blocco di dominio privato.

#### <a name="add-exceptions-for-cdn-endpoints"></a>Aggiungere eccezioni per gli endpoint della rete CDN

Collaborare con gli utenti finali o fornire la documentazione che informa che è necessario consentire il download degli script dal Application Insights gli endpoint della rete CDN, inserendoli nel plug-in del browser o nell'elenco di eccezioni delle regole firewall (l'utente può variare in base all'ambiente dell'utente finale).

Di seguito è riportato un esempio di come configurare [Chrome per consentire o bloccare l'accesso ai siti Web.](https://support.google.com/chrome/a/answer/7532419?hl=en)

### <a name="application-insights-cdn-is-blocked-by-corporate-firewall"></a>Application Insights rete CDN è bloccata (dal firewall aziendale)

Se gli utenti finali si trovano in una rete aziendale, è molto probabile che la soluzione firewall e il reparto IT abbiano implementato una forma di sistema di filtro Internet. In questo caso, sarà necessario collaborare con loro per consentire le regole necessarie per gli utenti finali.

#### <a name="add-exceptions-for-cdn-endpoints-for-corporations"></a>Aggiungere eccezioni per gli endpoint della rete CDN per le aziende

  Questa operazione è simile all' [aggiunta di eccezioni per gli utenti finali](#add-exceptions-for-cdn-endpoints), ma è necessario collaborare con il reparto IT della società per configurare gli endpoint della rete CDN Application Insights per il download includendo (o rimuovendo) i servizi in un elenco di blocchi di dominio o per consentire l'inserimento di elenchi.

  > [!WARNING]
  > Se l'utente aziendale usa un [cloud privato](https://azure.microsoft.com/overview/what-is-a-private-cloud/) e non può abilitare alcuna forma di eccezione per fornire agli utenti interni l'accesso pubblico per gli endpoint della rete CDN, sarà necessario usare i [pacchetti NPM Application Insights](https://www.npmjs.com/package/applicationinsights) o ospitare Application Insights SDK nella rete CDN.  

### <a name="additional-troubleshooting-for-blocked-cdn"></a>Risoluzione dei problemi aggiuntiva per la rete CDN bloccata

> [!NOTE]
> Se gli utenti usano un [cloud privato](https://azure.microsoft.com/overview/what-is-a-private-cloud/) e non hanno accesso alla rete Internet pubblica, sarà necessario ospitare l'SDK nella propria rete CDN o usare i pacchetti NPM.

#### <a name="host-the-sdk-on-your-own-cdn"></a>Ospitare l'SDK nella rete CDN

 Anziché gli utenti finali che scaricano Application Insights SDK dalla rete CDN pubblica, è possibile ospitare Application Insights SDK dall'endpoint della rete CDN. Si consiglia di usare una versione specifica (ai. 2. #. # .min.js), in modo che sia più semplice identificare la versione in uso. Aggiornarlo periodicamente alla versione corrente (ai.2.min.js), in modo da poter sfruttare le correzioni di bug e le nuove funzionalità che diventano disponibili.

#### <a name="use-npm-packages-to-embed-the-application-insight-sdk"></a>Usare i pacchetti NPM per incorporare Application Insights SDK

Invece di usare il frammento e gli endpoint della rete CDN pubblica, è possibile usare i [pacchetti NPM](https://www.npmjs.com/package/applicationinsights) per includere l'SDK come parte dei propri file JavaScript. L'SDK diventerà semplicemente un altro pacchetto all'interno degli script.

> [!NOTE]
> Quando si usano i pacchetti NPM, è consigliabile usare anche un modulo [JavaScript bundler](https://www.bing.com/search?q=javascript+bundler) per facilitare la suddivisione del codice e minification.

Come per il frammento di codice, è anche possibile che i propri script, con o senza usare i pacchetti NPM di SDK, possano essere interessati dagli stessi problemi di blocco elencati di seguito, quindi, a seconda dell'applicazione, degli utenti e del Framework, è consigliabile implementare un elemento simile alla logica del frammento per rilevare e segnalare questi problemi.


## <a name="next-steps"></a>Passaggi successivi 
- [Ottieni assistenza aggiuntiva inviando un problema in GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues)
- [Monitorare l'utilizzo delle pagine Web](javascript.md)
