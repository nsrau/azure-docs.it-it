---
title: Limiti e configurazione - App per la logica di Azure | Microsoft Docs
description: Limiti di servizio e valori di configurazione per App per la logica di Azure
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
ms.date: 09/25/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 22d0ee242d18d73d1d5825567fd61638fd22cc68
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="logic-apps-limits-and-configuration"></a>Limiti e configurazione per App per la logica

Questo argomento descrive gli attuali limiti e dettagli di configurazione per App per la logica di Azure.

## <a name="limits"></a>Limiti

### <a name="http-request-limits"></a>Limiti delle richieste HTTP

Questi limiti si applicano a una singola richiesta HTTP o a una chiamata di un connettore.

#### <a name="timeout"></a>Timeout

| NOME | Limite | Note | 
| ---- | ----- | ----- | 
| Timeout richiesta | 120 secondi | La compensazione necessaria può essere offerta da un [modello asincrono](../logic-apps/logic-apps-create-api-app.md) o un [ciclo Until](logic-apps-loops-and-scopes.md) |
|||| 

#### <a name="message-size"></a>Dimensioni dei messaggi

| NOME | Limite | Note | 
| ---- | ----- | ----- | 
| Dimensioni dei messaggi | 100 MB | Alcuni connettori e API potrebbero non supportare 100 MB. | 
| Limite per la valutazione delle espressioni | 131.072 caratteri | `@concat()`, `@base64()` e `string` non possono superare questo limite. | 
|||| 

#### <a name="retry-policy"></a>Criteri di ripetizione

| NOME | Limite | Note | 
| ---- | ----- | ----- | 
| Tentativi | 90 | Il valore predefinito è 4. Configurabile con il [parametro dei criteri per i tentativi](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Intervallo massimo tra i tentativi | 1 giorno | Configurabile con il [parametro dei criteri per i tentativi](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Intervallo minimo tra i tentativi | 5 secondi | Configurabile con il [parametro dei criteri per i tentativi](../logic-apps/logic-apps-workflow-actions-triggers.md). |
|||| 

### <a name="run-duration-and-retention"></a>Durata dell'esecuzione e conservazione

Questi limiti si applicano a una singola esecuzione di un'app per la logica.

| NOME | Limite | 
| ---- | ----- | 
| Durata esecuzione | 90 giorni | 
| Conservazione in risorsa di archiviazione | 90 giorni dalla data di inizio dell'esecuzione | 
| Intervallo di ricorrenza minimo | 1 secondo </br>Per le app per la logica con un piano di servizio app: 15 secondi | 
| Intervallo di ricorrenza massimo | 500 giorni | 
||| 

Per superare i limiti di durata dell'esecuzione o di conservazione della risorsa di archiviazione nel normale flusso di elaborazione, [contattare il supporto](mailto://logicappsemail@microsoft.com) per ottenere assistenza sui requisiti specifici.

### <a name="looping-and-debatching-limits"></a>Limiti di esecuzione di cicli e debatching

Questi limiti si applicano a una singola esecuzione di un'app per la logica.

| NOME | Limite | Note | 
| ---- | ----- | ----- | 
| Elementi ForEach | 100.000 | È possibile usare l'[azione di query](../connectors/connectors-native-query.md) per filtrare matrici di dimensioni maggiori in base alle esigenze. | 
| Iterazioni Until | 5.000 | | 
| Elementi SplitOn | 100.000 | | 
| Parallelismo ForEach | 50 | Il valore predefinito è 20. <p>Per impostare un livello specifico di parallelismo in un ciclo ForEach, impostare la proprietà `runtimeConfiguration` nell'azione `foreach`. <p>Per eseguire in modo sequenziale un ciclo ForEach, impostare la proprietà `operationOptions` su "Sequential" nell'azione `foreach`. | 
|||| 

### <a name="throughput-limits"></a>Limiti di velocità effettiva

Questi limiti si applicano a una singola istanza di app per la logica.

| NOME | Limite | Note | 
| ---- | ----- | ----- | 
| Esecuzioni di azioni per 5 minuti | 100.000 | È possibile distribuire il carico di lavoro tra più app nel modo necessario. | 
| Chiamate in uscita simultanee di azioni | ~2.500 | Diminuire il numero di richieste simultanee o ridurre la durata in base alle esigenze. | 
| Endpoint di runtime: chiamate in ingresso simultanee | ~1,000 | Diminuire il numero di richieste simultanee o ridurre la durata in base alle esigenze. | 
| Endpoint di runtime: lettura delle chiamate per 5 minuti | 60.000 | È possibile distribuire il carico di lavoro tra più app nel modo necessario. | 
| Endpoint di runtime: richiamata delle chiamate per 5 minuti | 45,000 | È possibile distribuire il carico di lavoro tra più app nel modo necessario. | 
|||| 

Per superare questi limiti nell'elaborazione normale o per eseguire test di carico che possono superare questi limiti, [contattare il supporto](mailto://logicappsemail@microsoft.com) per ottenere assistenza sui requisiti specifici.

### <a name="logic-app-definition-limits"></a>Limiti delle definizioni delle app per la logica

Questi limiti si applicano a una singola definizione di un'app per la logica.

| NOME | Limite | Note | 
| ---- | ----- | ----- | 
| Azioni per flusso di lavoro | 500 | Per estendere questo limite, è possibile aggiungere flussi di lavoro annidati in base alle esigenze. |
| Livello di annidamento azioni consentito | 8 | Per estendere questo limite, è possibile aggiungere flussi di lavoro annidati in base alle esigenze. | 
| Flussi di lavoro per area per sottoscrizione | 1000 | | 
| Trigger per flusso di lavoro | 10 | | 
| Limite ambito switch-case | 25 | | 
| Numero di variabili per flusso di lavoro | 250 | | 
| Numero massimo di caratteri per espressione | 8.192 | | 
| Dimensioni massime di `trackedProperties` in caratteri | 16.000 | 
| `action`/`trigger` | 80 | | 
| `description`  | 256 | | 
| `parameters` limit | 50 | | 
| `outputs` limit | 10 | | 
|||| 

<a name="custom-connector-limits"></a>

### <a name="custom-connector-limits"></a>Limiti per i connettori personalizzati

Questi limiti si applicano ai connettori personalizzati che è possibile creare da API Web.

| NOME | Limite | 
| ---- | ----- | 
| Numero di connettori personalizzati che è possibile creare | 1.000 per ogni sottoscrizione di Azure | 
| Numero di richieste al minuto per ogni connessione creata da un connettore personalizzato | 500 richieste per ogni connessione creata dal connettore |
||| 

### <a name="integration-account-limits"></a>Limiti dell'account di integrazione

Questi limiti si applicano agli elementi che è possibile aggiungere a un account di integrazione.

| NOME | Limite | Note | 
| ---- | ----- | ----- | 
| SCHEMA | 8 MB | È possibile usare l'[URI del BLOB](../logic-apps/logic-apps-enterprise-integration-schemas.md) per caricare file di dimensioni maggiori di 2 MB. | 
| Mappa (file XSLT) | 2 MB | | 
| Endpoint di runtime: lettura delle chiamate per 5 minuti | 60.000 | È possibile distribuire il carico di lavoro tra più account nel modo necessario. | 
| Endpoint di runtime: richiamata delle chiamate per 5 minuti | 45,000 | È possibile distribuire il carico di lavoro tra più account nel modo necessario. | 
| Endpoint di runtime: verifica delle chiamate per 5 minuti | 45,000 | È possibile distribuire il carico di lavoro tra più account nel modo necessario. | 
| Endpoint di runtime: blocco delle chiamate simultanee | ~1,000 | Diminuire il numero di richieste simultanee o ridurre la durata in base alle esigenze. | 
|||| 

Questi limiti si applicano al numero di elementi che è possibile aggiungere a un account di integrazione.

#### <a name="free-pricing-tier"></a>Piano tariffario Gratuito

| NOME | Limite | Note | 
| ---- | ----- | ----- | 
| Contratti | 10 | | 
| Altri tipi di elementi | 25 |I tipi includono partner, schemi, certificati e mappe. Ogni tipo può avere fino al numero massimo di elementi. | 
|||| 

#### <a name="standard-pricing-tier"></a>Piano tariffario Standard

| NOME | Limite | Note | 
| ---- | ----- | ----- | 
| Qualsiasi tipo di elemento | 500 | I tipi di elemento includono contratti, partner, schemi, certificati e mappe. Ogni tipo può avere fino al numero massimo di elementi. | 
|||| 

### <a name="b2b-protocols-as2-x12-edifact-message-size"></a>Dimensioni dei messaggi per i protocolli B2B (AS2, X12, EDIFACT)

Questi limiti si applicano ai protocolli B2B.

| NOME | Limite | Note | 
| ---- | ----- | ----- | 
| AS2 | 50 MB | Applicabile alla decodifica e alla codifica | 
| X12 | 50 MB | Applicabile alla decodifica e alla codifica | 
| EDIFACT | 50 MB | Applicabile alla decodifica e alla codifica | 
|||| 

<a name="configuration"></a>

## <a name="configuration-ip-addresses"></a>Configurazione: indirizzi IP

### <a name="logic-apps-service"></a>Servizio App per la logica

Le chiamate effettuate direttamente da un'app per la logica, ovvero tramite [HTTP](../connectors/connectors-native-http.md) o [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) o altre richieste HTTP, provengono dagli indirizzi IP inclusi in questo elenco.

|Area di App per la logica|IP in uscita|
|-----------------|-----------|
|Australia orientale|13.75.149.4, 104.210.91.55, 104.210.90.241|
|Australia sudorientale|13.73.114.207, 13.77.3.139, 13.70.159.205|
|Brasile meridionale|191.235.82.221, 191.235.91.7, 191.234.182.26|
|Canada centrale|52.233.29.92, 52.228.39.241, 52.228.39.244|
|Canada orientale|52.232.128.155, 52.229.120.45, 52.229.126.25|
|India centrale|52.172.154.168, 52.172.186.159, 52.172.185.79|
|Stati Uniti centrali|13.67.236.125, 104.208.25.27, 40.122.170.198|
|Asia orientale|13.75.94.173, 40.83.127.19, 52.175.33.254|
|Stati Uniti orientali|13.92.98.111, 40.121.91.41, 40.114.82.191|
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
| | |

### <a name="connectors"></a>Connettori

Le chiamate effettuate dai [connettori](../connectors/apis-list.md) provengono dagli indirizzi IP inclusi in questo elenco.

|Area di App per la logica|IP in uscita|
|-----------------|-----------|
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
| | | 

## <a name="next-steps"></a>Passaggi successivi  

* [Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* [Common examples and scenarios](../logic-apps/logic-apps-examples-and-scenarios.md) (Esempi e scenari comuni)
* [Video: Automate business processes with Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694) (Automatizzare processi aziendali con App per la logica) 
* [Video: Integrate your systems with Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462) (Integrare i sistemi con App per la logica di Azure)
