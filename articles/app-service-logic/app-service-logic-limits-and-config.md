<properties
	pageTitle="Limiti e configurazione delle app per la logica | Microsoft Azure"
	description="Panoramica dei limiti del servizio e dei valori di configurazione disponibili per le app per la logica."
	services="logic-apps"
	documentationCenter=".net,nodejs,java"
	authors="jeffhollan"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2016"
	ms.author="jehollan"/>

# Limiti e configurazione delle app per la logica

Di seguito sono riportate informazioni sugli attuali limiti e sui dettagli della configurazione di App per la logica di Azure.

## Limiti

### Limiti delle richieste HTTP

Di seguito sono riportati i limiti per una singola richiesta e/o chiamata al connettore HTTP.

#### Timeout

|Nome|Limite|Note|
|----|----|----|
|Timeout richiesta|1 minuto|La compensazione necessaria può essere offerta da un [modello asincrono](app-service-logic-create-api-app.md) o un [ciclo Until](app-service-logic-loops-and-scopes.md)|

#### Dimensioni dei messaggi

|Nome|Limite|Note|
|----|----|----|
|Dimensioni dei messaggi|50 MB|Alcuni connettori e alcune API potrebbero non supportare 50 MB. Il trigger di richiesta supporta fino a 25 MB|
|Limite per la valutazione delle espressioni|131\.072 caratteri|`@concat()`, `@base64()` e `string` non possono superare tale lunghezza|

#### Criteri di ripetizione

|Nome|Limite|Note|
|----|----|----|
|Tentativi|4|Configurabile con il [parametro dei criteri di ripetizione](https://msdn.microsoft.com/it-IT/library/azure/mt643939.aspx)|
|Intervallo massimo tra i tentativi|1 ora|Configurabile con il [parametro dei criteri di ripetizione](https://msdn.microsoft.com/it-IT/library/azure/mt643939.aspx)|
|Intervallo minimo tra i tentativi|20 minuti|Configurabile con il [parametro dei criteri di ripetizione](https://msdn.microsoft.com/it-IT/library/azure/mt643939.aspx)|

### Durata dell'esecuzione e conservazione

Di seguito sono riportati i limiti per una singola esecuzione di app per la logica.

|Nome|Limite|Note|
|----|----|----|
|Durata esecuzione|90 giorni||
|Conservazione in risorsa di archiviazione|90 giorni|Dalla data di inizio dell'esecuzione|
|Intervallo di ricorrenza minimo|15 secondi||
|Intervallo di ricorrenza massimo|500 giorni||


### Limiti di esecuzione di cicli e debatching

Di seguito sono riportati i limiti per una singola esecuzione di app per la logica.

|Nome|Limite|Note|
|----|----|----|
|Elementi ForEach|5\.000|È possibile usare l'[azione di query](../connectors/connectors-native-query.md) per filtrare matrici di dimensioni superiori, se necessario|
|Iterazioni Until|10\.000||
|Elementi SplitOn|10\.000||
|Parallelismo ForEach|20|È possibile impostare un ForEach sequenziale aggiungendo `"operationOptions": "Sequential"` all'azione `foreach`|


### Limiti di velocità effettiva

Di seguito sono riportati i limiti per una singola istanza di app per la logica.

|Nome|Limite|Note|
|----|----|----|
|Trigger al secondo|100|È possibile distribuire i flussi di lavoro tra più app, se necessario|

### Limiti delle definizioni

Di seguito sono riportati i limiti per una singola definizione di app per la logica.

|Nome|Limite|Note|
|----|----|----|
|Azioni in ForEach|1|È possibile aggiungere flussi di lavoro annidati per estendere questo valore, se necessario|
|Azioni per flusso di lavoro|60|È possibile aggiungere flussi di lavoro annidati per estendere questo valore, se necessario|
|Livello di annidamento azioni consentito|5|È possibile aggiungere flussi di lavoro annidati per estendere questo valore, se necessario|
|Flussi per area per sottoscrizione|1000||
|Trigger per flusso di lavoro|10||
|Numero massimo di caratteri per espressione|8\.192||
|Dimensioni massime di `trackedProperties` in caratteri|16\.000|
|Limite del nome di `action`/`trigger`|80||
|Limite di lunghezza di `description`|256||
|Limite di `parameters`|50||
|Limite di `outputs`|10||

## Configurazione

### Indirizzo IP

Le chiamate effettuate da un [connettore](../connectors/apis-list.md) proverranno dall'indirizzo IP specificato di seguito.

Le chiamate effettuate direttamente da un'app per la logica (tramite [HTTP](../connectors/connectors-native-http.md) oppure [HTTP e Swagger](../connectors/connectors-native-http-swagger.md)) possono provenire da uno qualsiasi degli [intervalli IP del data center di Azure](https://www.microsoft.com/it-IT/download/details.aspx?id=41653).

|Area dell'app per la logica|IP in uscita|
|-----|----|
|Australia orientale|40\.126.251.213|
|Australia sudorientale|40\.127.80.34|
|Brasile meridionale|191\.232.38.129|
|India centrale|104\.211.98.164|
|Stati Uniti centrali|40\.122.49.51|
|Asia orientale|23\.99.116.181|
|Stati Uniti orientali|191\.237.41.52|
|Stati Uniti orientali 2|104\.208.233.100|
|Giappone orientale|40\.115.186.96|
|Giappone occidentale|40\.74.130.77|
|Stati Uniti centro-settentrionali|65\.52.218.230|
|Europa settentrionale|104\.45.93.9|
|Stati Uniti centro-meridionali|104\.214.70.191|
|Asia sudorientale|13\.76.231.68|
|India meridionale|104\.211.227.225|
|Europa occidentale|40\.115.50.13|
|India occidentale|104\.211.161.203|
|Stati Uniti occidentali|104\.40.51.248|


## Passaggi successivi  

- Per iniziare a usare le app per la logica, seguire l'esercitazione [Creare una nuova app per la logica](app-service-logic-create-a-logic-app.md).
- [Visualizzare esempi e scenari comuni](app-service-logic-examples-and-scenarios.md)
- [Le app per la logica consentono di automatizzare i processi aziendali](http://channel9.msdn.com/Events/Build/2016/T694)
- [Informazioni su come integrare i sistemi correnti con le app per la logica](http://channel9.msdn.com/Events/Build/2016/P462)

<!---HONumber=AcomDC_0831_2016-->