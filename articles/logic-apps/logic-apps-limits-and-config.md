---
title: Limiti e configurazione delle app per la logica | Documentazione Microsoft
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
ms.date: 11/23/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: b2845e1835c5571909a911b1eb0bfd61ac00da86


---
# <a name="logic-app-limits-and-configuration"></a>Limiti e configurazione delle app per la logica

Di seguito sono riportate informazioni sugli attuali limiti e sui dettagli della configurazione di App per la logica di Azure.

## <a name="limits"></a>Limiti

### <a name="http-request-limits"></a>Limiti delle richieste HTTP

Di seguito sono riportati i limiti per una singola richiesta e/o chiamata al connettore HTTP.

#### <a name="timeout"></a>Timeout

|Nome|Limite|Note|
|----|----|----|
|Timeout richiesta|90 secondi|La compensazione necessaria può essere offerta da un [modello asincrono](../logic-apps/logic-apps-create-api-app.md) o un [ciclo Until](logic-apps-loops-and-scopes.md)|

#### <a name="message-size"></a>Dimensioni dei messaggi

|Nome|Limite|Note|
|----|----|----|
|Dimensioni dei messaggi|50 MB|Alcuni connettori e alcune API potrebbero non supportare 50 MB |
|Limite per la valutazione delle espressioni|131.072 caratteri|`@concat()`, `@base64()` e `string` non possono superare tale lunghezza|

#### <a name="retry-policy"></a>Criteri di ripetizione

|Nome|Limite|Note|
|----|----|----|
|Tentativi|4|Configurabile con il [parametro dei criteri di ripetizione](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Intervallo massimo tra i tentativi|1 ora|Configurabile con il [parametro dei criteri di ripetizione](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Intervallo minimo tra i tentativi|20 secondi|Configurabile con il [parametro dei criteri di ripetizione](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|

### <a name="run-duration-and-retention"></a>Durata dell'esecuzione e conservazione

Di seguito sono riportati i limiti per una singola esecuzione di app per la logica.

|Nome|Limite|Note|
|----|----|----|
|Durata esecuzione|90 giorni||
|Conservazione in risorsa di archiviazione|90 giorni|Dalla data di inizio dell'esecuzione|
|Intervallo di ricorrenza minimo|1 secondo|| 15 secondi per le app per la logica con il piano di servizio app
|Intervallo di ricorrenza massimo|500 giorni||


### <a name="looping-and-debatching-limits"></a>Limiti di esecuzione di cicli e debatching

Di seguito sono riportati i limiti per una singola esecuzione di app per la logica.

|Nome|Limite|Note|
|----|----|----|
|Elementi ForEach|5.000|È possibile usare l' [azione di query](../connectors/connectors-native-query.md) per filtrare matrici di dimensioni superiori, se necessario|
|Iterazioni Until|5.000||
|Elementi SplitOn|5.000||
|Parallelismo ForEach|20|È possibile impostare un ForEach sequenziale aggiungendo `"operationOptions": "Sequential"` all'azione `foreach`|


### <a name="throughput-limits"></a>Limiti di velocità effettiva

Di seguito sono riportati i limiti per una singola istanza di app per la logica. 

|Nome|Limite|Note|
|----|----|----|
|Trigger al secondo|100|È possibile distribuire i flussi di lavoro tra più app, se necessario|

### <a name="definition-limits"></a>Limiti delle definizioni

Di seguito sono riportati i limiti per una singola definizione di app per la logica.

|Nome|Limite|Note|
|----|----|----|
|Azioni per flusso di lavoro|250|È possibile aggiungere flussi di lavoro annidati per estendere questo valore, se necessario|
|Livello di annidamento azioni consentito|5|È possibile aggiungere flussi di lavoro annidati per estendere questo valore, se necessario|
|Flussi per area per sottoscrizione|1000||
|Trigger per flusso di lavoro|10||
|Numero massimo di caratteri per espressione|8.192||
|Dimensioni massime di `trackedProperties` in caratteri|16.000|
|`action`/`trigger` |80||
|`description` |256||
|`parameters` limit|50||
|`outputs` limit|10||

### <a name="integration-account-limits"></a>Limiti dell'account di integrazione

Ecco i limiti per gli elementi aggiunti all'account di integrazione

|Nome|Limite|Note|
|----|----|----|
|Schema|8 MB|È possibile usare l'[URI del BLOB](logic-apps-enterprise-integration-schemas.md) per caricare file di dimensioni superiori a 2 MB |
|Mappa (file XSLT)|2 MB| |

### <a name="b2b-protocols-as2-x12-edifact-message-size"></a>Dimensioni dei messaggi per i protocolli B2B (AS2, X12, EDIFACT)

Ecco i limiti per i protocolli B2B

|Nome|Limite|Note|
|----|----|----|
|AS2|50 MB|Applicabile alla decodifica e alla codifica|
|X12|50 MB|Applicabile alla decodifica e alla codifica|
|EDIFACT|50 MB|Applicabile alla decodifica e alla codifica|

## <a name="configuration"></a>Configurazione

### <a name="ip-address"></a>Indirizzo IP

#### <a name="logic-app-service"></a>Servizio app per la logica

Le chiamate effettuate direttamente da un'app per la logica (tramite [HTTP](../connectors/connectors-native-http.md) oppure [HTTP e Swagger](../connectors/connectors-native-http-swagger.md)) o altre richieste HTTP proverranno dall'indirizzo IP specificato di seguito:

|Area dell'app per la logica|IP in uscita|
|-----|----|
|Australia orientale|13.75.153.66, 104.210.89.222, 104.210.89.244, 13.75.149.4, 104.210.91.55, 104.210.90.241|
|Australia sudorientale|13.73.115.153, 40.115.78.70, 40.115.78.237, 13.73.114.207, 13.77.3.139, 13.70.159.205|
|Brasile meridionale|191.235.86.199, 191.235.95.229, 191.235.94.220, 191.235.82.221, 191.235.91.7, 191.234.182.26|
|India centrale|52.172.157.194, 52.172.184.192, 52.172.191.194, 52.172.154.168, 52.172.186.159, 52.172.185.79|
|Stati Uniti centrali|13.67.236.76, 40.77.111.254, 40.77.31.87, 13.67.236.125, 104.208.25.27, 40.122.170.198|
|Asia orientale|168.63.200.173, 13.75.89.159, 23.97.68.172, 13.75.94.173, 40.83.127.19, 52.175.33.254|
|Stati Uniti orientali|137.135.106.54, 40.117.99.79, 40.117.100.228, 13.92.98.111, 40.121.91.41, 40.114.82.191|
|Stati Uniti orientali 2|40.84.25.234, 40.79.44.7, 40.84.59.136, 40.84.30.147, 104.208.155.200, 104.208.158.174|
|Giappone orientale|13.71.146.140, 13.78.84.187, 13.78.62.130, 13.71.158.3, 13.73.4.207, 13.71.158.120|
|Giappone occidentale|40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.140.4, 104.214.137.243, 138.91.26.45|
|Stati Uniti centro-settentrionali|168.62.249.81, 157.56.12.202, 65.52.211.164, 168.62.248.37, 157.55.210.61, 157.55.212.238|
|Europa settentrionale|13.79.173.49, 52.169.218.253, 52.169.220.174, 40.113.12.95, 52.178.165.215, 52.178.166.21|
|Stati Uniti centro-meridionali|13.65.98.39, 13.84.41.46, 13.84.43.45, 104.210.144.48, 13.65.82.17, 13.66.52.232|
|Asia sudorientale|52.163.93.214, 52.187.65.81, 52.187.65.155, 13.76.133.155, 52.163.228.93, 52.163.230.166|
|India meridionale|52.172.9.47, 52.172.49.43, 52.172.51.140, 52.172.50.24, 52.172.55.231, 52.172.52.0|
|Europa occidentale|13.95.155.53, 52.174.54.218, 52.174.49.6, 40.68.222.65, 40.68.209.23, 13.95.147.65|
|India occidentale|104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.164.80, 104.211.162.205, 104.211.164.136|
|Stati Uniti occidentali|52.160.90.237, 138.91.188.137, 13.91.252.184, 52.160.92.112, 40.118.244.241, 40.118.241.243|

#### <a name="connectors"></a>Connettori

Le chiamate effettuate da un [connettore](../connectors/apis-list.md) proverranno dall'indirizzo IP specificato di seguito:

|Area dell'app per la logica|IP in uscita|
|-----|----|
|Australia orientale|40.126.251.213|
|Australia sudorientale|40.127.80.34|
|Brasile meridionale|191.232.38.129|
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


## <a name="next-steps"></a>Passaggi successivi  

- Per iniziare a usare le app per la logica, seguire l'esercitazione [Creare una nuova app per la logica](../logic-apps/logic-apps-create-a-logic-app.md) .  
- [Visualizzare esempi e scenari comuni](../logic-apps/logic-apps-examples-and-scenarios.md)
- [Le app per la logica consentono di automatizzare i processi aziendali](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Informazioni su come integrare i sistemi correnti con le app per la logica](http://channel9.msdn.com/Events/Build/2016/P462)


<!--HONumber=Jan17_HO3-->


