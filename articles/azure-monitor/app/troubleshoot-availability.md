---
title: Risolvere i problemi dei test di disponibilità di Azure Application Insights | Microsoft Docs
description: Risolvere i problemi di test web in Azure Application Insights. Ottenere avvisi se un sito Web diventa non disponibile o risponde lentamente.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: 87bc87d7d105d581f0143e87044fb0337c0fd7f6
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304935"
---
# <a name="troubleshooting"></a>risoluzione dei problemi

Questo articolo consente di risolvere i problemi comuni che possono verificarsi quando si usa il monitoraggio della disponibilità.

## <a name="ssltls-errors"></a>Errori SSL/TLS

|Sintomo/messaggio di errore| Possibili cause|
|--------|------|
|Impossibile creare un canale sicuro SSL/TLS  | Versione SSL. Sono supportati solo TLS 1.0, 1.1 e 1.2. **SSLv3 non è supportato.**
|Livello di Record TLSv1.2: Avviso (livello: Errore irreversibile, descrizione: Record non valida MAC)| Vedere StackExchange thread per [ulteriori informazioni](https://security.stackexchange.com/questions/39844/getting-ssl-alert-write-fatal-bad-record-mac-during-openssl-handshake).
|URL che non è possibile eseguire è in una rete CDN (Content Delivery Network) | Ciò potrebbe dipendere da un'errata configurazione sulla rete CDN |  

### <a name="possible-workaround"></a>Possibile soluzione alternativa

* Se gli URL che si verificano il problema sono sempre per le risorse dipendenti, è consigliabile disabilitare **Analizza richieste dipendenti** per il test web.

## <a name="test-fails-only-from-certain-locations"></a>Test non riesce solo da determinati percorsi

|Sintomo/messaggio di errore| Possibili cause|
|----|---------|
|Un tentativo di connessione non è riuscito perché la parte connessa non ha risposto correttamente dopo un periodo di tempo  | Agenti di test in determinati percorsi sono bloccati da un firewall.|
|    |Reindirizzamento di determinati indirizzi IP è in corso tramite (bilanciamento del carico, strumenti di gestione traffico geografico, Azure Expressroute.) 
|    |Se si usa Azure ExpressRoute, esistono scenari in cui è possibile eliminare i pacchetti nei casi in cui [Routing asimmetrico si verifica](https://docs.microsoft.com/azure/expressroute/expressroute-asymmetric-routing).|

## <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Negativo intermittente dei test con un errore di violazione del protocollo

|Sintomo/messaggio di errore| Possibili cause|
|----|---------|
protocollo violazione CR deve essere seguito da LF | Ciò si verifica quando vengono rilevate le intestazioni in formato non valido. In particolare, alcune intestazioni non usino CRLF per indicare di fine della riga che viola la specifica HTTP e pertanto supererà la convalida a livello di WebRequest .NET.
 || Ciò può essere causato anche da servizi di bilanciamento del carico o reti CDN.

> [!NOTE]
> l'errore dell'URL potrebbe non verificarsi in browser con una convalida delle intestazioni HTTP meno rigida. Per una spiegazione dettagliata del problema, vedere questo post di blog: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

## <a name="common-troubleshooting-questions"></a>Domande comuni per la risoluzione dei problemi

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Sito ha un aspetto corretto ma vedo errori di test? Il motivo per cui è Application Insights avvisi me?

   * Il test ha **Analizza richieste dipendenti** abilitato? viene eseguito un controllo rigoroso sulle risorse, ad esempio script, immagini e così via. Questi tipi di errori possono non essere evidenti in un browser. Controllare tutte le immagini, gli script, i fogli di stile e qualsiasi altro file caricato dalla pagina. Se uno di essi non riesce, il test è segnalato come non superato, anche se la pagina HTML principale viene caricata senza alcun problema. Per eliminare la sensibilità del test per tali errori delle risorse, è sufficiente deselezionare l'Analizza richieste dipendenti dalla configurazione di test.

   * Per ridurre la probabilità di disturbo da problemi di rete temporanei e così via, verificare che i tentativi di attivazione per gli errori di test configurazione viene controllata. È anche possibile eseguire test da più posizioni e gestire la soglia delle regole di avviso di conseguenza per evitare che problemi specifici della posizione causino avvisi non dovuti.

   * Fare clic su uno qualsiasi dei punti rossi dell'esperienza di disponibilità oppure su qualsiasi errore di disponibilità presente in Esplora ricerche per visualizzare i dettagli del motivo per cui è stato segnalato l'errore. Il risultato del test, insieme ai dati di telemetria lato server correlati (se abilitati), dovrebbe consentire di comprendere perché il test non è riuscito. Le cause più comuni dei problemi temporanei sono problemi di connessione o di rete.

   * Controllare se si è verificato il timeout del test. I test vengono interrotti dopo 2 minuti. Se il ping o il test in più passaggi richiede più di 2 minuti, verrà segnalato come errore. È consigliabile suddividere il test in più test di durata più breve.

   * Valutare se sono stati segnalati errori in tutte le località e solo in alcune. Se gli errori sono stati segnalati solo in alcune località, potrebbero essere causati da errori di rete/CDN. Anche in questo caso, facendo clic sui puntini rossi dovrebbe essere possibile comprendere perché nella località sono stati segnalati errori.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Non hanno ricevuto un messaggio di posta elettronica quando l'avviso attivato o risolti o entrambi?

Controllare la configurazione degli avvisi classici per verificare se l'indirizzo di posta elettronica è inserito direttamente nell'elenco o se per la ricezione delle notifiche è configurata una lista di distribuzione che include tale indirizzo. In quest'ultimo caso, controllare la configurazione della lista di distribuzione per verificare che possa ricevere messaggi di posta elettronica esterni. Controllare anche se l'amministratore di posta elettronica ha eventualmente configurato criteri che possono causare questo problema.

### <a name="i-did-not-receive-the-webhook-notification"></a>Non ricevuto la notifica di webhook?

Verificare che l'applicazione che riceve la notifica webhook sia disponibile e che riesca a elaborare le richieste di un webhook. Per altre informazioni, vedere [qui](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook).

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Negativo intermittente dei test con un errore di violazione del protocollo?

Un errore di tipo "Violazione del protocollo... CR deve essere seguito da LF" indica un problema relativo al server o alle dipendenze. Questa situazione si verifica quando nella risposta vengono impostate intestazioni in formato non valido e può essere causata da servizi di bilanciamento del carico o reti per la distribuzione di contenuti. In particolare, alcune intestazioni non usino CRLF per indicare di fine della riga che viola la specifica HTTP e pertanto non superano la convalida a livello di WebRequest .NET. Controllare la risposta per individuare le intestazioni, che potrebbero essere segnalate violazioni in.

> [!NOTE]
> l'errore dell'URL potrebbe non verificarsi in browser con una convalida delle intestazioni HTTP meno rigida. Per una spiegazione dettagliata del problema, vedere questo post di blog: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Non è possibile visualizzare le relative telemetria lato server per diagnosticare gli errori dei test? *

Se Application Insights è configurato per l'applicazione lato server, il motivo può essere l'esecuzione del [campionamento](../../azure-monitor/app/sampling.md). Selezionare un risultato di disponibilità diverso.

### <a name="can-i-call-code-from-my-web-test"></a>È possibile chiamare codice da un test web?

No. I passaggi del test devono essere nel file con estensione webtest. Inoltre non è possibile chiamare altri test web o utilizzare cicli. Esistono diversi plug-in che potrebbero risultare utili.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>È presente una differenza tra "test web" e "test di disponibilità"?

I due termini vengono usati in modo intercambiabile. Test di disponibilità è un termine più generico che include i singoli test di ping URL oltre ai test Web in più passaggi.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>Mi piacerebbe utilizzare test di disponibilità nel server interno che esegue un firewall.

   Le soluzioni possono essere due:

   * Configurare il firewall per consentire richieste in ingresso dagli [indirizzi IP degli agenti di test Web](../../azure-monitor/app/ip-addresses.md).
   * Scrivere il proprio codice per testare periodicamente il server interno. Eseguire il codice come processo in background in un server di prova protetto da firewall. Il processo di test può inviare i risultati ad Application Insights tramite l'API [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) nel pacchetto SDK di base. In questo modo è necessario testare il server per avere un accesso in uscita per l'endpoint di inserimento di Application Insights, ma questo rappresenta un minore rischio per la sicurezza rispetto all'alternativa di consentire le richieste in ingresso. I risultati non verranno visualizzati nei pannelli dei test Web di disponibilità, ma verranno mostrati come risultati relativi alla disponibilità in Analisi, Ricerca ed Esplorazione metriche.

### <a name="uploading-a-multi-step-web-test-fails"></a>Si verifica un errore di caricamento di un test web in più passi

Di seguito sono riportate alcune delle possibili cause:
   * È previsto un limite di dimensioni pari a 300 KB.
   * I cicli non sono supportati.
   * I riferimenti ad altri test Web non sono supportati.
   * Le origini dati non sono supportate.

### <a name="my-multi-step-test-doesnt-complete"></a>Non viene completato il test in più passi

È previsto un limite di 100 richieste per ogni test. Inoltre, il test viene arrestato se la durata dell'esecuzione è superiore a due minuti.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>Come è possibile eseguire un test con certificati client?

Non supportato attualmente.

## <a name="who-receives-the-classic-alert-notifications"></a>Chi riceve le notifiche di avviso (classiche)?

Questa sezione si applica solo agli avvisi classici e aiuterà a ottimizzare le notifiche di avviso assicurando che siano ricevute solo dai destinatari desiderati. Per capire meglio la differenza tra gli [avvisi classici](../platform/alerts-classic.overview.md) e la nuova esperienza di avviso fare riferimento all'[articolo Panoramica avvisi](../platform/alerts-overview.md). Per controllare la notifica di avviso nella nuova esperienza di avviso usare i [gruppi di azioni](../platform/action-groups.md).

* Consigliamo di usare destinatari specifici per le notifiche di avviso classiche.

* Per gli avvisi in caso di errori dalle posizioni X da Y, l'opzione **in blocco/gruppo** della casella di controllo, se abilitata, invia notifiche agli utenti con ruoli di amministratore o coamministratore.  Essenzialmente _tutti_ gli amministratori della _sottoscrizione_ riceveranno le notifiche.

* Per gli avvisi sulle metriche di disponibilità le **bulk/gruppo** opzione della casella di controllo se abilitata, invia agli utenti con i ruoli proprietario, collaboratore o lettore nella sottoscrizione. In effetti, _tutti_ gli utenti con accesso alla sottoscrizione della risorsa di Application Insights rientrano nell'ambito e riceveranno le notifiche. 

> [!NOTE]
> Se attualmente si usa l'opzione **in blocco/gruppo** della casella di controllo e la si disabilita, sarà impossibile annullare le modifiche.

Usare la nuova esperienza di avviso/avvisi quasi in tempo reale se si desidera inviare notifiche agli utenti in base ai ruoli. Con [gruppi di azioni](../platform/action-groups.md), è possibile configurare notifiche tramite posta elettronica agli utenti con ruolo proprietario, collaboratore o lettore (non combinati insieme come una singola opzione).

## <a name="next-steps"></a>Passaggi successivi

* [Test web in più passi](availability-multistep.md)
* [Test di ping URL](monitor-web-app-availability.md)
