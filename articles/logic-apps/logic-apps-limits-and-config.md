---
title: Limiti e configurazione delle app per la logica | Microsoft Docs
description: Panoramica dei limiti del servizio e dei valori di configurazione disponibili per le app per la logica.
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: LADocs; jehollan
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 5d905d410e70c5b635a3f6221e7e0c0bda7ad140
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---
# <a name="logic-app-limits-and-configuration"></a>Limiti e configurazione delle app per la logica

Di seguito sono riportate informazioni sugli attuali limiti e sui dettagli della configurazione di App per la logica di Azure.

## <a name="limits"></a>Limiti

### <a name="http-request-limits"></a>Limiti delle richieste HTTP

Di seguito sono riportati i limiti per una singola richiesta e/o chiamata al connettore HTTP.

#### <a name="timeout"></a>Timeout

|Nome|Limite|Note|
|----|----|----|
|Timeout richiesta|120 secondi|La compensazione necessaria può essere offerta da un [modello asincrono](../logic-apps/logic-apps-create-api-app.md) o un [ciclo Until](logic-apps-loops-and-scopes.md)|

#### <a name="message-size"></a>Dimensioni dei messaggi

|Nome|Limite|Note|
|----|----|----|
|Dimensioni dei messaggi|100 MB|Alcuni connettori e alcune API potrebbero non supportare 100 MB |
|Limite per la valutazione delle espressioni|131.072 caratteri|`@concat()`, `@base64()` e `string` non possono superare tale limite|

#### <a name="retry-policy"></a>Criteri di ripetizione

|Nome|Limite|Note|
|----|----|----|
|Tentativi|10| Il valore predefinito è 4. Configurabile con il [parametro dei criteri di ripetizione](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Intervallo massimo tra i tentativi|1 ora|Configurabile con il [parametro dei criteri di ripetizione](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Intervallo minimo tra i tentativi|5 secondi|Configurabile con il [parametro dei criteri di ripetizione](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|

### <a name="run-duration-and-retention"></a>Durata dell'esecuzione e conservazione

Di seguito sono riportati i limiti per una singola esecuzione di app per la logica.

|Nome|Limite|Note|
|----|----|----|
|Durata esecuzione|90 giorni||
|Conservazione in risorsa di archiviazione|90 giorni|A partire dalla data di inizio dell'esecuzione|
|Intervallo di ricorrenza minimo|1 secondo|| 15 secondi per le app per la logica con il piano di servizio app
|Intervallo di ricorrenza massimo|500 giorni||

Se si prevede di superare i limiti di durata dell'esecuzione o di conservazione della risorsa di archiviazione nel normale flusso di elaborazione, [contattare il supporto](mailto://logicappsemail@microsoft.com) per ottenere assistenza sui requisiti specifici.


### <a name="looping-and-debatching-limits"></a>Limiti di esecuzione di cicli e debatching

Di seguito sono riportati i limiti per una singola esecuzione di app per la logica.

|Nome|Limite|Note|
|----|----|----|
|Elementi ForEach|100.000|È possibile usare l' [azione di query](../connectors/connectors-native-query.md) per filtrare matrici di dimensioni superiori, se necessario|
|Iterazioni Until|5.000||
|Elementi SplitOn|100.000||
|Parallelismo ForEach|50| Il valore predefinito è 20. È possibile impostare un elemento foreach sequenziale aggiungendo `"operationOptions": "Sequential"` all'azione `foreach` o un livello specifico di parallelismo usando `runtimeConfiguration`|


### <a name="throughput-limits"></a>Limiti di velocità effettiva

Di seguito sono riportati i limiti per una singola istanza di app per la logica. 

|Nome|Limite|Note|
|----|----|----|
|Esecuzioni di azioni per 5 minuti |100.000|Se necessario, è possibile distribuire il carico di lavoro tra più app|
|Chiamate in uscita simultanee di azioni |~2.500|Diminuire il numero di richieste simultanee o ridurre la durata in base alle esigenze|
|Chiamate in ingresso simultanee di endpoint di runtime |~1,000|Diminuire il numero di richieste simultanee o ridurre la durata in base alle esigenze|
|L'endpoint di runtime legge le chiamate per cinque minuti |60.000|Se necessario, è possibile distribuire il carico di lavoro tra più app|
|L'endpoint di runtime richiama le chiamate per cinque minuti |45,000|Se necessario, è possibile distribuire il carico di lavoro tra più app|

Se si prevede di superare questo limite nella normale elaborazione o si vuole eseguire il test di carico che potrebbe superare questo limite per un periodo di tempo, [contattare il supporto](mailto://logicappsemail@microsoft.com) per ottenere assistenza sui requisiti specifici.

### <a name="definition-limits"></a>Limiti delle definizioni

Di seguito sono riportati i limiti per una singola definizione di app per la logica.

|Nome|Limite|Note|
|----|----|----|
|Azioni per flusso di lavoro|500|È possibile aggiungere flussi di lavoro annidati per estendere questo limite, se necessario|
|Livello di annidamento azioni consentito|8|È possibile aggiungere flussi di lavoro annidati per estendere questo limite, se necessario|
|Flussi di lavoro per area per sottoscrizione|1000||
|Trigger per flusso di lavoro|10||
|Limite ambito switch-case|25||
|Numero di variabili per flusso di lavoro|250||
|Numero massimo di caratteri per espressione|8.192||
|Dimensioni massime di `trackedProperties` in caratteri|16.000|
|`action`/`trigger` |80||
|`description` |256||
|`parameters` limit|50||
|`outputs` limit|10||

### <a name="integration-account-limits"></a>Limiti dell'account di integrazione

Di seguito sono riportati i limiti per gli elementi che è possibile aggiungere a un account di integrazione.

|Nome|Limite|Note|
|----|----|----|
|Schema|8 MB|È possibile usare l'[URI del BLOB](logic-apps-enterprise-integration-schemas.md) per caricare file di dimensioni superiori a 2 MB |
|Mappa (file XSLT)|2 MB| |
|L'endpoint di runtime legge le chiamate per cinque minuti |60.000|Se necessario, è possibile distribuire il carico di lavoro tra più account|
|L'endpoint di runtime richiama le chiamate per cinque minuti |45,000|Se necessario, è possibile distribuire il carico di lavoro tra più account|
|L'endpoint di runtime tiene traccia delle chiamate per 5 minuti |45,000|Se necessario, è possibile distribuire il carico di lavoro tra più account|
|L'endpoint di runtime blocca le chiamate simultanee |~1,000|Diminuire il numero di richieste simultanee o ridurre la durata in base alle esigenze|

Di seguito sono riportati i limiti sul numero di elementi che è possibile aggiungere a un account di integrazione.

Piano tariffario Gratuito

|Nome|Limite|Note|
|----|----|----|
|Contratti|10||
|Altri tipi di elementi|25|I tipi includono partner, schemi, certificati e mappe. Ogni tipo può avere fino al numero massimo di elementi.|

Piano tariffario Standard

|Nome|Limite|Note|
|----|----|----|
|Qualsiasi tipo di elemento|500|I tipi includono argomenti, partner, schemi, certificati e mappe. Ogni tipo può avere fino al numero massimo di elementi.|

### <a name="b2b-protocols-as2-x12-edifact-message-size"></a>Dimensioni dei messaggi per i protocolli B2B (AS2, X12, EDIFACT)

Di seguito sono riportati i limiti per i protocolli B2B

|Nome|Limite|Note|
|----|----|----|
|AS2|50 MB|Applicabile alla decodifica e alla codifica|
|X12|50 MB|Applicabile alla decodifica e alla codifica|
|EDIFACT|50 MB|Applicabile alla decodifica e alla codifica|

## <a name="configuration"></a>Configurazione

### <a name="ip-address"></a>Indirizzo IP

#### <a name="logic-app-service"></a>Servizio app per la logica

Le chiamate effettuate direttamente da un'app per la logica (ad esempio, tramite [HTTP](../connectors/connectors-native-http.md) oppure [HTTP e Swagger](../connectors/connectors-native-http-swagger.md)) o altre richieste HTTP provengono dall'indirizzo IP specificato nell'elenco seguente:

|Area dell'app per la logica|IP in uscita|
|-----|----|
|Australia orientale|13.75.149.4, 104.210.91.55, 104.210.90.241|
|Australia sudorientale|13.73.114.207, 13.77.3.139, 13.70.159.205|
|Brasile meridionale|191.235.82.221, 191.235.91.7, 191.234.182.26|
|Canada centrale|52.233.29.92, 52.228.39.241, 52.228.39.244|
|Canada orientale|52.232.128.155, 52.229.120.45, 52.229.126.25|
|India centrale|52.172.154.168, 52.172.186.159, 52.172.185.79|
|Stati Uniti centrali|13.67.236.125, 104.208.25.27, 40.122.170.198|
|Asia orientale|13.75.94.173, 40.83.127.19, 52.175.33.254|
|Stati Uniti Orientali|13.92.98.111, 40.121.91.41, 40.114.82.191|
|Stati Uniti orientali 2|40.84.30.147, 104.208.155.200, 104.208.158.174|
|Giappone orientale|13.71.158.3, 13.73.4.207, 13.71.158.120|
|Giappone occidentale|40.74.140.4, 104.214.137.243, 138.91.26.45|
|Stati Uniti centro-settentrionali|168.62.248.37, 157.55.210.61, 157.55.212.238|
|Europa settentrionale|40.113.12.95, 52.178.165.215, 52.178.166.21|
|Stati Uniti centro-meridionali|104.210.144.48, 13.65.82.17, 13.66.52.232|
|Asia sudorientale|13.76.133.155, 52.163.228.93, 52.163.230.166|
|India meridionale|52.172.50.24, 52.172.55.231, 52.172.52.0|
|Stati Uniti centro-occidentali|52.161.27.190, 52.161.18.218, 52.161.9.108|
|Europa occidentale|40.68.222.65, 40.68.209.23, 13.95.147.65|
|India occidentale|104.211.164.80, 104.211.162.205, 104.211.164.136|
|Stati Uniti occidentali|52.160.92.112, 40.118.244.241, 40.118.241.243|
|Stati Uniti occidentali 2|13.66.210.167, 52.183.30.169, 52.183.29.132|
|Regno Unito meridionale|51.140.74.14, 51.140.73.85, 51.140.78.44|
|Regno Unito occidentale|51.141.54.185, 51.141.45.238, 51.141.47.136|

#### <a name="connectors"></a>Connettori

Le chiamate effettuate da un [connettore](../connectors/apis-list.md) provengono dall'indirizzo IP specificato nell'elenco seguente:

|Area dell'app per la logica|IP in uscita|
|-----|----|
|Australia orientale|40.126.251.213|
|Australia sudorientale|40.127.80.34|
|Brasile meridionale|191.232.38.129|
|Canada centrale|52.233.31.197, 52.228.42.205, 52.228.33.76, 52.228.34.13|
|Canada orientale|52.229.123.98, 52.229.120.178, 52.229.126.202, 52.229.120.52|
|India centrale|104.211.98.164|
|Stati Uniti centrali|40.122.49.51|
|Asia orientale|23.99.116.181|
|Stati Uniti orientali|191.237.41.52|
|Stati Uniti orientali 2|104.208.233.100|
|Giappone orientale|40.115.186.96|
|Giappone occidentale|40.74.130.77|
|Stati Uniti centro-settentrionali|65.52.218.230|
|Europa settentrionale|104.45.93.9|
|Stati Uniti centro-meridionali|104.214.70.191|
|Asia sudorientale|13.76.231.68|
|India meridionale|104.211.227.225|
|Europa occidentale|40.115.50.13|
|India occidentale|104.211.161.203|
|Stati Uniti occidentali|104.40.51.248|
|Regno Unito meridionale|51.140.80.51|
|Regno Unito occidentale|51.141.47.105|


## <a name="next-steps"></a>Passaggi successivi  

- Per iniziare a usare App per la logica, seguire l'esercitazione [Creare una nuova app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).  
- [Visualizzare esempi e scenari comuni](../logic-apps/logic-apps-examples-and-scenarios.md)
- [Le app per la logica consentono di automatizzare i processi aziendali](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Informazioni su come integrare i sistemi correnti con le app per la logica](http://channel9.msdn.com/Events/Build/2016/P462)

