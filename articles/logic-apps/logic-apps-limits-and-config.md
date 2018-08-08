---
title: Limiti e configurazione - App per la logica di Azure | Microsoft Docs
description: Limiti di servizio e valori di configurazione per App per la logica di Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/31/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 644d382b87b0cc7c60cc8917edbaeff34b222718
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390739"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Informazioni su limiti e configurazione per App per la logica di Azure

Questo articolo include informazioni dettagliate sui limiti e sulla configurazione per la creazione e l'esecuzione di flussi di lavoro automatici con App per la logica di Azure. Per Microsoft Flow, vedere [Limiti e configurazione in Microsoft Flow](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Limiti delle definizioni

Ecco i limiti per una singola definizione di app per la logica:

| Nome | Limite | Note | 
| ---- | ----- | ----- | 
| Azioni per flusso di lavoro | 500 | Per estendere questo limite, è possibile aggiungere flussi di lavoro annidati in base alle esigenze. |
| Livello di annidamento consentito per le azioni | 8 | Per estendere questo limite, è possibile aggiungere flussi di lavoro annidati in base alle esigenze. | 
| Flussi di lavoro per area per sottoscrizione | 1.000 | | 
| Trigger per flusso di lavoro | 10 | Quando si usa la visualizzazione codice e non la finestra di progettazione | 
| Limite ambito switch-case | 25 | | 
| Variabili per flusso di lavoro | 250 | | 
| Caratteri per espressione | 8.192 | | 
| Dimensioni massime per `trackedProperties` | 16.000 caratteri | 
| Nome per `action` o `trigger` | 80 caratteri | | 
| Lunghezza di `description` | 256 caratteri | | 
| Massimo per `parameters` | 50 | | 
| Massimo per `outputs` | 10 | | 
||||  

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Durata dell'esecuzione e limiti di conservazione

Ecco i limiti per una singola esecuzione di app per la logica:

| Nome | Limite | Note | 
|------|-------|-------| 
| Durata esecuzione | 90 giorni | Per modificare questo limite, vedere [Modificare la durata dell'esecuzione e la conservazione nella risorsa di archiviazione](#change-duration). | 
| Conservazione in risorsa di archiviazione | 90 giorni dalla data di inizio dell'esecuzione | Per modificare questo limite e impostarlo su un valore compreso tra 7 e 90 giorni, vedere [Modificare la durata dell'esecuzione e la conservazione nella risorsa di archiviazione](#change-retention). | 
| Intervallo di ricorrenza minimo | 1 secondo | | 
| Intervallo di ricorrenza massimo | 500 giorni | | 
|||| 

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Modificare la durata dell'esecuzione e la conservazione nella risorsa di archiviazione

Per modificare il limite predefinito e impostarlo su un valore compreso tra 7 giorni e 90 giorni, seguire questa procedura. Se è necessario superare il limite massimo, [contattare il team di App per la logica](mailto://logicappsemail@microsoft.com) per informazioni sui requisiti.

1. Nel menu dell'app per la logica nel portale di Azure scegliere **Impostazioni del flusso di lavoro**. 

2. In **Opzioni di runtime** scegliere **Personalizzata** nell'elenco **Conservazione cronologia di esecuzione in giorni**. 

3. Immettere un valore o trascinare il dispositivo di scorrimento in base al numero di giorni desiderato.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Limiti di concorrenza, cicli e debatching

Ecco i limiti per una singola esecuzione di app per la logica:

| Nome | Limite | Note | 
| ---- | ----- | ----- | 
| Concorrenza di trigger | 50 | Il limite predefinito è 20. Questo limite descrive il numero massimo di istanze di app per la logica che è possibile eseguire contemporaneamente o in parallelo. <p><p>Per modificare il limite predefinito e impostarlo su un valore compreso tra 1 e 50, estremi inclusi, vedere [Modifica della concorrenza dei trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) o [Attivare le istanze in sequenza](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). | 
| Numero massimo di esecuzioni in attesa | 100 | Il limite predefinito è 10. Questo limite descrive il numero massimo di istanze di app per la logica in attesa di esecuzione quando l'app per la logica esegue già il numero massimo di istanze simultanee. <p><p>Per modificare il limite predefinito e impostarlo su un valore compreso tra 0 e 100, estremi inclusi, vedere [Modifica del limite delle esecuzioni in attesa](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). | 
| Elementi ForEach | 100.000 | Questo limite descrive il numero massimo di elementi della matrice che un ciclo "for each" può elaborare. <p><p>Per filtrare matrici di dimensioni superiori, è possibile usare l'[azione di query](../connectors/connectors-native-query.md). | 
| Iterazioni foreach | 50 | Il limite predefinito è 20. Questo limite descrive il numero massimo di iterazioni "for each" che è possibile eseguire contemporaneamente o in parallelo. <p><p>Per modificare il limite predefinito e impostarlo su un valore compreso tra 1 e 50, estremi inclusi, vedere [Modifica della concorrenza "for each"](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) o [Esecuzione dei cicli "for each" in modo sequenziale](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). | 
| Elementi SplitOn | 100.000 | | 
| Iterazioni Until | 5.000 | | 
|||| 

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Limiti di velocità effettiva

Ecco i limiti per una singola esecuzione di app per la logica:

| Nome | Limite | Note | 
| ---- | ----- | ----- | 
| Azione: esecuzioni per 5 minuti | 300.000 | Il limite predefinito è 100.000. Per modificare il limite predefinito, vedere [Run your logic app in "high throughput" mode](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode) (Eseguire l'app per la logica in modalità di velocità effettiva elevata), attualmente in anteprima. In alternativa, è possibile distribuire il carico di lavoro tra più app per la logica in base alle esigenze. | 
| Azione: chiamate in uscita simultanee | ~2.500 | È possibile diminuire il numero di richieste simultanee o ridurre la durata in base alle esigenze. | 
| Endpoint di runtime: chiamate in ingresso simultanee | ~1,000 | È possibile diminuire il numero di richieste simultanee o ridurre la durata in base alle esigenze. | 
| Endpoint di runtime: lettura delle chiamate per 5 minuti  | 60.000 | È possibile distribuire un carico di lavoro tra più app in base alle esigenze. | 
| Endpoint di runtime: richiamata delle chiamate per 5 minuti | 45,000 | È possibile distribuire un carico di lavoro tra più app in base alle esigenze. | 
| Velocità effettiva del contenuto per 5 minuti | 600 MB | È possibile distribuire un carico di lavoro tra più app in base alle esigenze. | 
|||| 

Per superare questi limiti nell'elaborazione normale o per eseguire test di carico che possono superare questi limiti, [contattare il team di App per la logica](mailto://logicappsemail@microsoft.com) per ottenere assistenza sui requisiti specifici.

<a name="request-limits"></a>

## <a name="http-request-limits"></a>Limiti delle richieste HTTP

Ecco i limiti per una singola richiesta HTTP o a una chiamata sincrona di un connettore:

#### <a name="timeout"></a>Timeout

Alcune operazioni dei connettori effettuano chiamate asincrone o sono in ascolto di richieste di webhook e di conseguenza il timeout per queste operazioni può essere più prolungato rispetto a questi limiti. Per altre informazioni, vedere i dettagli tecnici per il connettore specifico e anche [Trigger e azioni dei flussi di lavoro](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Nome | Limite | Note | 
| ---- | ----- | ----- | 
| Richiesta in uscita | 120 secondi | Per operazioni di esecuzione più lunghe, usare un [modello di polling asincrono](../logic-apps/logic-apps-create-api-app.md#async-pattern) o un [ciclo until](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). | 
| Risposta sincrona | 120 secondi | Perché la richiesta originale ottenga la risposta, tutti i passaggi nella risposta devono terminare entro il limite, a meno che non venga chiamata un'altra app per la logica come flusso di lavoro annidato. Per altre informazioni, vedere [Chiamare, attivare o annidare app per la logica](../logic-apps/logic-apps-http-endpoint.md). | 
|||| 

#### <a name="message-size"></a>Dimensioni dei messaggi

| Nome | Limite | Note | 
| ---- | ----- | ----- | 
| Dimensioni dei messaggi | 100 MB | Per ignorare questo limite, vedere [Gestire messaggi di grandi dimensioni con la divisione in blocchi](../logic-apps/logic-apps-handle-large-messages.md). Tuttavia, alcuni connettori e API potrebbero non supportare la divisione in blocchi o addirittura il limite predefinito. | 
| Dimensione dei messaggi con la divisione in blocchi | 1 GB | Questo limite si applica alle azioni che supportano in modo nativo la divisione in blocchi o che consentono di abilitare la divisione in blocchi nella configurazione di runtime. Per altre informazioni, vedere [Gestire messaggi di grandi dimensioni con la divisione in blocchi](../logic-apps/logic-apps-handle-large-messages.md). | 
| Limite per la valutazione delle espressioni | 131.072 caratteri | Le espressioni `@concat()`, `@base64()` e `@string()` non possono superare questo limite. | 
|||| 

#### <a name="retry-policy"></a>Criteri di ripetizione

| Nome | Limite | Note | 
| ---- | ----- | ----- | 
| Tentativi | 90 | Il valore predefinito è 4. Per modificare il valore predefinito, usare il [parametro dei criteri di ripetizione](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Intervallo massimo tra i tentativi | 1 giorno | Per modificare il valore predefinito, usare il [parametro dei criteri di ripetizione](../logic-apps/logic-apps-workflow-actions-triggers.md). | 
| Intervallo minimo tra i tentativi | 5 secondi | Per modificare il valore predefinito, usare il [parametro dei criteri di ripetizione](../logic-apps/logic-apps-workflow-actions-triggers.md). |
|||| 

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Limiti per i connettori personalizzati

Limiti per i connettori personalizzati che è possibile creare da API Web.

| Nome | Limite | 
| ---- | ----- | 
| Numero di connettori personalizzati | 1.000 per ogni sottoscrizione di Azure | 
| Numero di richieste al minuto per ogni connessione creata da un connettore personalizzato | 500 richieste per connessione |
|||| 

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limiti dell'account di integrazione

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Limite di elementi per account di integrazione

Limiti per il numero di elementi per ogni account di integrazione. Per altre informazioni, vedere [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps/). 

*Livello gratuito*

Usare il livello gratuito solo per scenari esplorativi, non per scenari di produzione. Questo livello limita la velocità effettiva e l'utilizzo e non dispone di alcun contratto di servizio (SLA).

| Elemento | Limite | Note | 
|----------|-------|-------| 
| Partner commerciali EDI | 25 | | 
| Contratti commerciali EDI | 10 | | 
| Mappe | 25 | | 
| Schemi | 25 | 
| Assembly | 10 | | 
| Configurazioni batch | 5 | 
| Certificati | 25 | | 
|||| 

*Livello Basic*

| Elemento | Limite | Note | 
|----------|-------|-------| 
| Partner commerciali EDI | 2 | | 
| Contratti commerciali EDI | 1 | | 
| Mappe | 500 | | 
| Schemi | 500 | 
| Assembly | 25 | | 
| Configurazioni batch | 1 | | 
| Certificati | 2 | | 
|||| 

*Livello Standard*

| Elemento | Limite | Note | 
|----------|-------|-------| 
| Partner commerciali EDI | 500 | | 
| Contratti commerciali EDI | 500 | | 
| Mappe | 500 | | 
| Schemi | 500 | 
| Assembly | 50 | | 
| Configurazioni batch | 5 |  
| Certificati | 50 | | 
|||| 

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Limiti di capacità degli elementi

| Nome | Limite | Note | 
| ---- | ----- | ----- | 
| Schema | 8 MB | Per caricare file di dimensioni maggiori di 2 MB, usare l'[URI del BLOB](../logic-apps/logic-apps-enterprise-integration-schemas.md). | 
| Mappa (file XSLT) | 2 MB | | 
| Endpoint di runtime: lettura delle chiamate per 5 minuti | 60.000 | È possibile distribuire il carico di lavoro tra più di un account in base alle esigenze. | 
| Endpoint di runtime: richiamata delle chiamate per 5 minuti | 45,000 | È possibile distribuire il carico di lavoro tra più di un account in base alle esigenze. | 
| Endpoint di runtime: verifica delle chiamate per 5 minuti | 45,000 | È possibile distribuire il carico di lavoro tra più di un account in base alle esigenze. | 
| Endpoint di runtime: blocco delle chiamate simultanee | ~1,000 | È possibile diminuire il numero di richieste simultanee o ridurre la durata in base alle esigenze. | 
||||  

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Dimensioni dei messaggi per i protocolli B2B (AS2, X12, EDIFACT)

Ecco i limiti che si applicano ai protocolli B2B:

| Nome | Limite | Note | 
| ---- | ----- | ----- | 
| AS2 | 50 MB | Applicabile alla decodifica e alla codifica | 
| X12 | 50 MB | Applicabile alla decodifica e alla codifica | 
| EDIFACT | 50 MB | Applicabile alla decodifica e alla codifica | 
|||| 

<a name="configuration"></a>

## <a name="configuration-ip-addresses"></a>Configurazione: indirizzi IP

### <a name="azure-logic-apps-service"></a>Servizio App per la logica

Tutte le app per la logica in un'area usano gli stessi intervalli di indirizzi IP. Per supportare le chiamate che le app per la logica effettuano direttamente con [HTTP](../connectors/connectors-native-http.md), [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) e altre richieste HTTP, impostare le configurazioni del firewall in modo da includere questi indirizzi IP in entrata e in uscita in base a dove sono presenti le app per la logica:

| Area di App per la logica | IP in uscita |
|-------------------|-------------|
| Australia orientale | 13.75.149.4, 104.210.90.241, 104.210.91.55 |
| Australia sud-orientale | 13.70.159.205, 13.73.114.207, 13.77.3.139 |
| Brasile meridionale | 191.234.182.26, 191.235.82.221, 191.235.91.7 |
| Canada centrale | 52.233.29.92, 52.228.39.241, 52.228.39.244 |
| Canada orientale | 52.229.120.45, 52.229.126.25, 52.232.128.155 |
| India centrale | 52.172.154.168, 52.172.185.79, 52.172.186.159 |
| Stati Uniti centrali | 13.67.236.125, 40.122.170.198, 104.208.25.27 |
| Asia orientale | 13.75.94.173, 40.83.127.19, 52.175.33.254 |
| Stati Uniti orientali | 13.92.98.111, 40.114.82.191, 40.121.91.41 |
| Stati Uniti orientali 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174 |
| Giappone orientale | 13.71.158.3, 13.71.158.120, 13.73.4.207 |
| Giappone occidentale | 40.74.140.4, 104.214.137.243, 138.91.26.45 |
| Stati Uniti centro-settentrionali | 157.55.210.61, 157.55.212.238, 168.62.248.37 |
| Europa settentrionale | 40.113.12.95, 52.178.165.215, 52.178.166.21 |
| Stati Uniti centro-meridionali | 13.65.82.17, 13.66.52.232, 104.210.144.48 |
| India meridionale | 52.172.50.24, 52.172.52.0, 52.172.55.231 |
| Asia sud-orientale | 13.76.133.155, 52.163.228.93, 52.163.230.166 |
| Stati Uniti centro-occidentali | 52.161.18.218, 52.161.9.108, 52.161.27.190 |
| Europa occidentale | 13.95.147.65, 40.68.209.23, 40.68.222.65 |
| India occidentale | 104.211.162.205, 104.211.164.80, 104.211.164.136 |
| Stati Uniti occidentali | 40.83.164.80, 40.118.244.241, 40.118.241.243, 52.160.92.112, 104.42.38.32, 104.42.49.145, 157.56.162.53, 157.56.167.147 |
| Stati Uniti occidentali 2 | 13.66.210.167, 52.183.29.132, 52.183.30.169 |
| Regno Unito meridionale | 51.140.73.85, 51.140.74.14, 51.140.78.44 |
| Regno Unito occidentale | 51.141.45.238, 51.141.47.136, 51.141.54.185 |
| | |

| Area di App per la logica | IP In ingresso |
|-------------------|------------|
| Australia orientale | 3.75.153.66, 104.210.89.222, 104.210.89.244 |
| Australia sud-orientale | 13.73.115.153, 40.115.78.70, 40.115.78.237 |
| Brasile meridionale | 191.235.86.199, 191.235.94.220, 191.235.95.229 |
| Canada centrale | 13.88.249.209, 52.233.29.79, 52.233.30.218 |
| Canada orientale | 52.229.125.57, 52.232.129.143, 52.232.133.109 |
| India centrale | 52.172.157.194, 52.172.184.192, 52.172.191.194 |
| Stati Uniti centrali | 13.67.236.76, 40.77.31.87, 40.77.111.254 |
| Asia orientale | 13.75.89.159, 23.97.68.172, 168.63.200.173 |
| Stati Uniti orientali | 40.117.99.79, 40.117.100.228, 137.135.106.54 |
| Stati Uniti orientali 2 | 40.79.44.7, 40.84.25.234, 40.84.59.136 |
| Giappone orientale | 13.71.146.140, 13.78.84.187, 13.78.62.130 |
| Giappone occidentale | 40.74.140.173, 40.74.81.13, 40.74.85.215 |
| Stati Uniti centro-settentrionali | 168.62.249.81, 157.56.12.202, 65.52.211.164 |
| Europa settentrionale | 13.79.173.49, 52.169.218.253, 52.169.220.174 |
| Stati Uniti centro-meridionali | 13.65.98.39, 13.84.41.46, 13.84.43.45 |
| India meridionale | 52.172.9.47, 52.172.49.43, 52.172.51.140 |
| Asia sud-orientale | 52.163.93.214, 52.187.65.81, 52.187.65.155 |
| Stati Uniti centro-occidentali | 52.161.8.128, 52.161.19.82, 52.161.26.172 |
| Europa occidentale | 13.95.155.53, 52.174.54.218, 52.174.49.6 |
| India occidentale | 104.211.164.25, 104.211.164.112, 104.211.165.81 |
| Stati Uniti occidentali | 13.91.252.184, 52.160.90.237, 138.91.188.137, 157.56.160.212 |
| Stati Uniti occidentali 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| Regno Unito meridionale | 51.140.78.71, 51.140.79.109, 51.140.84.39 |
| Regno Unito occidentale | 51.141.48.98, 51.141.51.145, 51.141.53.164 |
| | |

### <a name="connectors"></a>Connettori

Per supportare le chiamate effettuate dai [connettori](../connectors/apis-list.md), impostare le configurazioni del firewall in modo che includano questi indirizzi IP in uscita, in base alle aree in cui sono presenti le app per la logica.

> [!IMPORTANT]
>
> Se si dispone di configurazioni esistenti, aggiornarle **il possibile prima del 1 settembre 2018** in modo che includano e mettano in corrispondenza gli indirizzi IP in questo elenco per le aree in cui sono presenti le app per la logica. 

| Area di App per la logica | IP in uscita | 
|-------------------|-------------|  
| Australia orientale | 13.70.72.192 - 13.70.72.207, 13.72.243.10, 40.126.251.213 | 
| Australia sud-orientale | 13.70.136.174, 13.77.50.240 - 13.77.50.255, 40.127.80.34 | 
| Brasile meridionale | 104.41.59.51, 191.232.38.129, 191.233.203.192 - 191.233.203.207 | 
| Canada centrale | 13.71.170.208 - 13.71.170.223, 13.71.170.224 - 13.71.170.239, 52.228.33.76, 52.228.34.13, 52.228.42.205, 52.233.26.83, 52.233.31.197, 52.237.24.126 | 
| Canada orientale | 40.69.106.240 - 40.69.106.255, 52.229.120.52, 52.229.120.131, 52.229.120.178, 52.229.123.98, 52.229.126.202, 52.242.35.152 | 
| India centrale | 52.172.211.12, 104.211.81.192 - 104.211.81.207, 104.211.98.164 | 
| Stati Uniti centrali | 13.89.171.80 - 13.89.171.95, 40.122.49.51, 52.173.245.164 | 
| Asia orientale | 13.75.36.64 - 13.75.36.79, 23.99.116.181, 52.175.23.169 | 
| Stati Uniti orientali | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 191.237.41.52 | 
| Stati Uniti orientali 2 | 40.70.146.208 - 40.70.146.223, 52.232.188.154, 104.208.233.100 | 
| Giappone orientale | 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.115.186.96 | 
| Giappone occidentale | 40.74.100.224 - 40.74.100.239, 40.74.130.77, 104.215.61.248 | 
| Stati Uniti centro-settentrionali | 52.162.107.160 - 52.162.107.175, 52.162.242.161, 65.52.218.230 | 
| Europa settentrionale | 13.69.227.208 - 13.69.227.223, 52.178.150.68, 104.45.93.9 | 
| Stati Uniti centro-meridionali | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191 | 
| India meridionale | 13.71.125.22, 40.78.194.240 - 40.78.194.255, 104.211.227.225 | 
| Asia sud-orientale | 13.67.8.240 - 13.67.8.255, 13.76.231.68, 52.187.68.19 | 
| Stati Uniti centro-occidentali | 13.71.195.32 - 13.71.195.47, 52.161.24.128, 52.161.26.212, 52.161.27.108, 52.161.29.35, 52.161.30.5, 52.161.102.22 | 
| Europa occidentale | 13.69.64.208 - 13.69.64.223, 40.115.50.13, 52.174.88.118 | 
| India occidentale | 104.211.146.224 - 104.211.146.239, 104.211.161.203, 104.211.189.218 | 
| Stati Uniti occidentali | 40.112.243.160 - 40.112.243.175, 104.40.51.248, 104.42.122.49 | 
| Stati Uniti occidentali 2 | 13.66.140.128 - 13.66.140.143, 13.66.218.78, 13.66.219.14, 13.66.220.135, 13.66.221.19, 13.66.225.219, 52.183.78.157 | 
| Regno Unito meridionale | 51.140.80.51, 51.140.148.0 - 51.140.148.15 | 
| Regno Unito occidentale | 51.140.211.0 - 51.140.211.15, 51.141.47.105 | 
| | | 

## <a name="next-steps"></a>Passaggi successivi  

* Informazioni su come [creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* Informazioni su [esempi e scenari comuni](../logic-apps/logic-apps-examples-and-scenarios.md)
