---
title: Limiti e configurazione - App per la logica di Azure | Microsoft Docs
description: Limiti di servizio e valori di configurazione per App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 07/19/2019
ms.openlocfilehash: b649ea1c69f82840a663cf32391e4c74ac6cc33e
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68319500"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Informazioni su limiti e configurazione per App per la logica di Azure

Questo articolo include informazioni dettagliate sui limiti e sulla configurazione per la creazione e l'esecuzione di flussi di lavoro automatici con App per la logica di Azure. Per Microsoft Flow, vedere [Limiti e configurazione in Microsoft Flow](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Limiti delle definizioni

Ecco i limiti per una singola definizione di app per la logica:

| NOME | Limite | Note |
| ---- | ----- | ----- |
| Azioni per flusso di lavoro | 500 | Per estendere questo limite, è possibile aggiungere flussi di lavoro annidati in base alle esigenze. |
| Livello di annidamento consentito per le azioni | 8 | Per estendere questo limite, è possibile aggiungere flussi di lavoro annidati in base alle esigenze. |
| Flussi di lavoro per area per sottoscrizione | 1\.000 | |
| Trigger per flusso di lavoro | 10 | Quando si usa la visualizzazione codice e non la finestra di progettazione |
| Limite ambito switch-case | 25 | |
| Variabili per flusso di lavoro | 250 | |
| Caratteri per espressione | 8,192 | |
| Dimensioni massime per `trackedProperties` | 16.000 caratteri |
| Nome per `action` o `trigger` | 80 caratteri | |
| Lunghezza di `description` | 256 caratteri | |
| Massimo per `parameters` | 50 | |
| Massimo per `outputs` | 10 | |
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-limits"></a>Durata dell'esecuzione e limiti di conservazione

Ecco i limiti per una singola esecuzione di app per la logica:

| Name | Limite multi-tenant | Limite dell'ambiente del servizio di integrazione | Note |
|------|--------------------|---------------------------------------|-------|
| Durata esecuzione | 90 giorni | 365 giorni | Per modificare il limite predefinito, vedere la pagina relativa alla [durata dell'esecuzione delle modifiche](#change-duration). |
| Conservazione in risorsa di archiviazione | 90 giorni dalla data di inizio dell'esecuzione | 365 giorni | Per modificare il limite predefinito, vedere [modificare la conservazione dell'archiviazione](#change-retention). |
| Intervallo di ricorrenza minimo | 1 secondo | 1 secondo ||
| Intervallo di ricorrenza massimo | 500 giorni | 500 giorni ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-storage-retention"></a>Modificare la durata dell'esecuzione e la conservazione nella risorsa di archiviazione

Per modificare il limite predefinito per la durata dell'esecuzione e la conservazione dell'archiviazione, attenersi alla seguente procedura. Se è necessario superare il limite massimo, [contattare il team di App per la logica](mailto://logicappsemail@microsoft.com) per informazioni sui requisiti.

1. Nel menu dell'app per la logica nel portale di Azure scegliere **Impostazioni del flusso di lavoro**.

2. In **Opzioni di runtime** scegliere **Personalizzata** nell'elenco **Conservazione cronologia di esecuzione in giorni**.

3. Immettere un valore o trascinare il dispositivo di scorrimento in base al numero di giorni desiderato.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Limiti di concorrenza, cicli e debatching

Ecco i limiti per una singola esecuzione di app per la logica:

| Name | Limite | Note |
| ---- | ----- | ----- |
| Concorrenza di trigger | * Senza limiti quando il controllo della concorrenza è disattivato <p><p>* 25 è il limite predefinito quando il controllo della concorrenza è attivato e non può essere annullato dopo l'attivazione del controllo. È possibile modificare il valore predefinito impostandolo su un valore compreso tra 1 e 50, estremi inclusi. | Questo limite descrive il numero più alto di istanze di app per la logica che è possibile eseguire contemporaneamente o in parallelo. <p><p>Per modificare il limite predefinito e impostarlo su un valore compreso tra 1 e 50 inclusi, vedere [Modificare il limite della concorrenza dei trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) o [Attivare le istanze in sequenza](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Numero massimo di esecuzioni in attesa | Quando il controllo della concorrenza è attivato, il numero minimo di esecuzioni in attesa è 10 più il numero di esecuzioni simultanee (trigger di concorrenza). È possibile modificare il numero massimo impostando un valore fino a 100 (incluso). | Questo limite descrive il numero più alto di istanze di app per la logica in attesa di esecuzione quando l'app per la logica esegue già il numero massimo di istanze simultanee. <p><p>Per modificare il limite predefinito, vedere [Modificare il limite delle esecuzioni in attesa](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). |
| Elementi della matrice foreach | 100,000 | Questo limite descrive il numero più alto di elementi della matrice che un ciclo "for each" può elaborare. <p><p>Per filtrare matrici di dimensioni superiori, è possibile usare l'[azione di query](../connectors/connectors-native-query.md). |
| Concorrenza foreach | 20 è il limite predefinito quando il controllo della concorrenza è disattivato. È possibile modificare il valore predefinito impostandolo su un valore compreso tra 1 e 50, estremi inclusi. | Questo limite indica il numero più alto di iterazioni "for each" che è possibile eseguire contemporaneamente o in parallelo. <p><p>Per modificare il limite predefinito e impostarlo su un valore compreso tra 1 e 50 inclusi, vedere [Modificare il limite della concorrenza "for each"](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) o [Eseguire i cicli "for each" in modo sequenziale](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| Elementi SplitOn | 100,000 | Per i trigger che restituiscono una matrice, è possibile specificare un'espressione che usa una proprietà 'SplitOn' che [suddivide o esegue il debatch degli elementi della matrice in più istanze del flusso di lavoro](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) per l'elaborazione, anziché usare un ciclo "Foreach". Questa espressione fa riferimento alla matrice da usare per la creazione e l'esecuzione di un'istanza del flusso di lavoro per ogni elemento della matrice. |
| Iterazioni Until | 5\.000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Limiti di velocità effettiva

Ecco i limiti per una singola definizione di app per la logica:

### <a name="multi-tenant-logic-apps-service"></a>Servizio app per la logica multi-tenant

| Name | Limite | Note |
| ---- | ----- | ----- |
| Azione: esecuzioni per 5 minuti | 100.000 è il limite predefinito, ma 300.000 è il limite massimo. | Per modificare il limite predefinito, vedere [Run your logic app in "high throughput" mode](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode) (Eseguire l'app per la logica in modalità di velocità effettiva elevata), attualmente in anteprima. In alternativa, è possibile distribuire il carico di lavoro tra più app per la logica in base alle esigenze. |
| Azione: chiamate in uscita simultanee | ~2.500 | È possibile diminuire il numero di richieste simultanee o ridurre la durata in base alle esigenze. |
| Endpoint di runtime: chiamate in ingresso simultanee | ~1,000 | È possibile diminuire il numero di richieste simultanee o ridurre la durata in base alle esigenze. |
| Endpoint di runtime: leggere le chiamate per 5 minuti  | 60.000 | È possibile distribuire un carico di lavoro tra più app in base alle esigenze. |
| Endpoint di runtime: richiamare le chiamate per 5 minuti | 45,000 | È possibile distribuire un carico di lavoro tra più app in base alle esigenze. |
| Velocità effettiva del contenuto per 5 minuti | 600 MB | È possibile distribuire un carico di lavoro tra più app in base alle esigenze. |
||||

### <a name="integration-service-environment-ise"></a>Ambiente Integration Services (ISE)

| Name | Limite | Note |
|------|-------|-------|
| Limite esecuzione unità di base | Limitazione del sistema quando la capacità dell'infrastruttura raggiunge il 80% | In sono disponibili ~ 4.000 esecuzioni di azioni al minuto, ovvero ~ 160 milioni esecuzioni di azioni al mese | |
| Limite di esecuzione delle unità di scala | Limitazione del sistema quando la capacità dell'infrastruttura raggiunge il 80% | Ogni unità di scala può fornire circa 2.000 esecuzioni di azioni aggiuntive al minuto, ovvero ~ 80 milioni altre esecuzioni di azioni al mese | |
| Unità di scala massime che è possibile aggiungere | 10 | |
||||

Per superare questi limiti nell'elaborazione normale o per eseguire test di carico che possono superare questi limiti, [contattare il team di App per la logica](mailto://logicappsemail@microsoft.com) per ottenere assistenza sui requisiti specifici.

<a name="request-limits"></a>

## <a name="http-limits"></a>Limiti HTTP

Ecco i limiti per una singola richiesta HTTP o a una chiamata sincrona di un connettore:

#### <a name="timeout"></a>Timeout

Alcune operazioni dei connettori effettuano chiamate asincrone o sono in ascolto di richieste di webhook e di conseguenza il timeout per queste operazioni può essere più prolungato rispetto a questi limiti. Per altre informazioni, vedere i dettagli tecnici per il connettore specifico e anche [Trigger e azioni dei flussi di lavoro](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Name | Limite multi-tenant | Limite dell'ambiente del servizio di integrazione | Note |
|------|--------------------|---------------------------------------|-------|
| Richiesta in uscita | 120 secondi | 240 secondi | Per operazioni di esecuzione più lunghe, usare un [modello di polling asincrono](../logic-apps/logic-apps-create-api-app.md#async-pattern) o un [ciclo until](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). |
| Risposta sincrona | 120 secondi | 240 secondi | Perché la richiesta originale ottenga la risposta, tutti i passaggi nella risposta devono terminare entro il limite, a meno che non venga chiamata un'altra app per la logica come flusso di lavoro annidato. Per altre informazioni, vedere [Chiamare, attivare o annidare app per la logica](../logic-apps/logic-apps-http-endpoint.md). |
|||||

#### <a name="message-size"></a>Dimensioni dei messaggi

| Name | Limite multi-tenant | Limite dell'ambiente del servizio di integrazione | Note |
|------|--------------------|---------------------------------------|-------|
| Dimensioni dei messaggi | 100 MB | 200 MB | Per ignorare questo limite, vedere [Gestire messaggi di grandi dimensioni con la divisione in blocchi](../logic-apps/logic-apps-handle-large-messages.md). Tuttavia, alcuni connettori e API potrebbero non supportare la divisione in blocchi o addirittura il limite predefinito. |
| Dimensione dei messaggi con la divisione in blocchi | 1 GB | 5 GB | Questo limite si applica alle azioni che supportano in modo nativo la divisione in blocchi o che consentono di abilitare la divisione in blocchi nella configurazione di runtime. <p>Per l'ambiente del servizio di integrazione, il motore delle app per la logica supporta questo limite, ma i connettori hanno limiti di suddivisione in blocchi fino al limite del motore. ad esempio, vedere [connettore di archiviazione BLOB di Azure](/connectors/azureblob/). Per ulteriori informazioni sulla suddivisione in blocchi, vedere [gestire messaggi di grandi dimensioni con la suddivisione in blocchi](../logic-apps/logic-apps-handle-large-messages.md). |
| Limite per la valutazione delle espressioni | 131.072 caratteri | 131.072 caratteri | Le espressioni `@concat()`, `@base64()` e `@string()` non possono superare questo limite. |
|||||

#### <a name="retry-policy"></a>Criteri di ripetizione

| Name | Limite | Note |
| ---- | ----- | ----- |
| Tentativi | 90 | Il valore predefinito è 4. Per modificare il valore predefinito, usare il [parametro dei criteri di ripetizione](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Intervallo massimo tra i tentativi | 1 giorno | Per modificare il valore predefinito, usare il [parametro dei criteri di ripetizione](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Intervallo minimo tra i tentativi | 5 secondi | Per modificare il valore predefinito, usare il [parametro dei criteri di ripetizione](../logic-apps/logic-apps-workflow-actions-triggers.md). |
||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>Limiti per i connettori personalizzati

Limiti per i connettori personalizzati che è possibile creare da API Web.

| Name | Limite multi-tenant | Limite dell'ambiente del servizio di integrazione | Note |
|------|--------------------|---------------------------------------|-------|
| Numero di connettori personalizzati | 1\.000 per ogni sottoscrizione di Azure | 1\.000 per ogni sottoscrizione di Azure ||
| Numero di richieste al minuto per un connettore personalizzato | 500 richieste al minuto per connessione | 2\.000 richieste al minuto per *connettore personalizzato* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Identità gestite

| NOME | Limite |
| ---- | ----- |
| Numero di app per la logica con identità gestite assegnate dal sistema per ogni sottoscrizione di Azure | 100 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limiti dell'account di integrazione

Ogni sottoscrizione di Azure ha questi limiti di account di integrazione:

* Un singolo account di integrazione del [livello gratuito](../logic-apps/logic-apps-pricing.md#integration-accounts)

* 1\.000 account di integrazione totali, inclusi gli account di integrazione in tutti gli [ambienti di Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) negli [SKU Developer e Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

* Ogni ISE, che sia [Developer o Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), è limitato a 5 account di integrazione totali:

  | SKU ISE | Limite |
  |---------|-------|
  | **Premium** | 5 totale-solo standard, nessuna [versione](../logic-apps/logic-apps-pricing.md#integration-accounts) gratuita o di base |
  | **Developer** | 5 totale-gratuito (limitato a 1), standard o entrambi, ma senza base |
  |||

Ulteriori costi si applicano agli account di integrazione aggiunti oltre gli account di integrazione inclusi in ISE. Per informazioni sul funzionamento dei prezzi e della fatturazione per ISEs, vedere il [modello di prezzi di app](../logic-apps/logic-apps-pricing.md#fixed-pricing)per la logica. Per informazioni sui prezzi, vedere [prezzi di app](https://azure.microsoft.com/pricing/details/logic-apps/)per la logica.

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Limite di elementi per account di integrazione

Ecco i limiti per il numero di elementi per ogni livello dell'account di integrazione. Per informazioni sui prezzi, vedere [prezzi di app](https://azure.microsoft.com/pricing/details/logic-apps/)per la logica. Per informazioni sul funzionamento dei prezzi e della fatturazione per gli account di integrazione, vedere il [modello di prezzi di app](../logic-apps/logic-apps-pricing.md#integration-accounts)per la logica.

> [!NOTE]
> Usare il livello gratuito solo per gli scenari di esplorazione, non per gli scenari di produzione. Questo livello limita la velocità effettiva e l'utilizzo e non dispone di alcun contratto di servizio (SLA).

| Elemento | Gratuito | Basic | Standard |
|----------|------|-------|----------|
| Contratti commerciali EDI | 10 | 1 | 1\.000 |
| Partner commerciali EDI | 25 | 2 | 1\.000 |
| Mappe | 25 | 500 | 1\.000 |
| Schemi | 25 | 500 | 1\.000 |
| Assembly | 10 | 25 | 1\.000 |
| Certificati | 25 | 2 | 500 |
| Configurazioni batch | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Limiti di capacità degli elementi

| Elemento | Limite | Note |
| -------- | ----- | ----- |
| Assembly | 8 MB | Per caricare file di dimensioni superiori a 2 MB, usare un [account di archiviazione di Azure e un contenitore BLOB](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Mappa (file XSLT) | 8 MB | Per caricare file di dimensioni superiori a 2 MB, usare l'[API REST di App per la logica di Azure Maps](https://docs.microsoft.com/rest/api/logic/maps/createorupdate). |
| SCHEMA | 8 MB | Per caricare file di dimensioni superiori a 2 MB, usare un [account di archiviazione di Azure e un contenitore BLOB](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

| Endpoint di runtime | Limite | Note |
|------------------|-------|-------|
| leggere le chiamate per 5 minuti | 60.000 | È possibile distribuire il carico di lavoro tra più di un account in base alle esigenze. |
| richiamare le chiamate per 5 minuti | 45,000 | È possibile distribuire il carico di lavoro tra più di un account in base alle esigenze. |
| verifica delle chiamate per 5 minuti | 45,000 | È possibile distribuire il carico di lavoro tra più di un account in base alle esigenze. |
| blocco di chiamate simultanee | ~1,000 | È possibile diminuire il numero di richieste simultanee o ridurre la durata in base alle esigenze. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Dimensioni dei messaggi per i protocolli B2B (AS2, X12, EDIFACT)

Di seguito sono riportati i limiti delle dimensioni dei messaggi applicabili ai protocolli B2B:

| Name | Limite multi-tenant | Limite dell'ambiente del servizio di integrazione | Note |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2 - 100 MB<br>V1-50 MB | v2 - 200 MB <br>V1-50 MB | Applicabile alla decodifica e alla codifica |
| X12 | 50 MB | 50 MB | Applicabile alla decodifica e alla codifica |
| EDIFACT | 50 MB | 50 MB | Applicabile alla decodifica e alla codifica |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Disabilitazione o eliminazione di app per la logica

Quando si disabilita un'app per la logica, non viene eseguita alcuna nuova istanza di esecuzione. Tutte le esecuzioni in corso e in sospeso continuano fino al completamento dell'operazione, il che potrebbe richiedere del tempo.

Quando si elimina un'app per la logica, non viene eseguita alcuna nuova istanza di esecuzione. Tutte le esecuzioni in corso e in sospeso vengono annullate. Se si dispone di migliaia di esecuzioni, l'annullamento potrebbe richiedere molto tempo.

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses"></a>Configurazione del firewall: Indirizzi IP

Tutte le app per la logica nella stessa area usano gli stessi intervalli di indirizzi IP. Per supportare le chiamate che le app per la logica effettuano direttamente con [HTTP](../connectors/connectors-native-http.md), [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) e altre richieste HTTP, impostare le configurazioni dei firewall in modo che includano  *tutti* gli indirizzi IP [in ingresso](#inbound) *e* [in uscita](#outbound) usati dal servizio App per la logica in base all'area in cui sono presenti le app per la logica. Questi indirizzi vengono visualizzati sotto le intestazioni **In ingresso** e **In uscita** in questa sezione e vengono ordinati in base all'area.

Per supportare le chiamate effettuate dai [connettori gestiti da Microsoft](../connectors/apis-list.md), impostare le configurazioni del firewall in modo che includano *tutti* gli indirizzi IP [in uscita](#outbound) usati da questi connettori, in base alle aree in cui sono presenti le app per la logica. Questi indirizzi vengono visualizzati sotto l'intestazione **In uscita** in questa sezione e vengono ordinati in base all'area.

Per [Azure per enti pubblici](../azure-government/documentation-government-overview.md) e [Azure Cina 21Vianet](https://docs.microsoft.com/azure/china/), gli indirizzi IP riservati per i connettori non sono attualmente disponibili.

> [!IMPORTANT]
>
> Se si dispone di configurazioni esistenti, aggiornarle **il prima possibile entro il 1° settembre 2018** in modo che includano e mettano in corrispondenza gli indirizzi IP in questo elenco per le aree in cui sono presenti le app per la logica.

App per la logica non supporta la connessione diretta agli account di archiviazione di Azure attraverso firewall. Per accedere a questi account di archiviazione, usare una delle opzioni descritte qui:

* Creare un [ambiente del servizio di integrazione](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), che può connettersi alle risorse presenti in una rete virtuale di Azure.

* Se si usa già Gestione API, è possibile usare questo servizio in questo scenario. Per altre informazioni, vedere [Architettura di integrazione aziendale semplice](https://aka.ms/aisarch).

<a name="inbound"></a>

### <a name="inbound-ip-addresses---logic-apps-service-only"></a>Indirizzi IP in ingresso - solo per il servizio App per la logica

| Region | IP |
|--------|----|
| Australia orientale | 13.75.153.66, 52.187.231.161, 104.210.89.222, 104.210.89.244 |
| Australia sud-orientale | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brasile meridionale | 191.234.166.198, 191.235.86.199, 191.235.94.220, 191.235.95.229 |
| Canada centrale | 13.88.249.209, 40.85.241.105, 52.233.29.79, 52.233.30.218 |
| Canada orientale | 40.86.202.42, 52.229.125.57, 52.232.129.143, 52.232.133.109 |
| India centrale | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| Stati Uniti centrali | 13.67.236.76, 40.77.31.87, 40.77.111.254, 104.43.243.39 |
| Asia orientale | 13.75.89.159, 23.97.68.172, 40.83.98.194, 168.63.200.173 |
| East US | 40.117.99.79, 40.117.100.228, 137.116.126.165, 137.135.106.54 |
| Stati Uniti orientali 2 | 40.70.27.253, 40.79.44.7, 40.84.25.234, 40.84.59.136 |
| Giappone orientale | 13.71.146.140, 13.78.43.164, 13.78.62.130, 13.78.84.187 |
| Giappone occidentale | 40.74.68.85, 40.74.81.13, 40.74.85.215, 40.74.140.173 |
| Stati Uniti centro-settentrionali | 65.52.9.64, 65.52.211.164, 168.62.249.81, 157.56.12.202 |
| Europa settentrionale | 13.79.173.49, 40.112.90.39, 52.169.218.253, 52.169.220.174 |
| Stati Uniti centro-meridionali | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| India meridionale | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Asia sud-orientale | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Stati Uniti centro-occidentali | 13.78.137.247, 52.161.8.128, 52.161.19.82, 52.161.26.172 |
| Europa occidentale | 13.95.155.53, 51.144.176.185, 52.174.49.6, 52.174.54.218 |
| India occidentale | 104.211.157.237, 104.211.164.25, 104.211.164.112, 104.211.165.81 |
| Stati Uniti occidentali | 13.91.252.184, 52.160.90.237, 138.91.188.137, 157.56.160.212 |
| Stati Uniti occidentali 2 | 13.66.128.68, 13.66.224.169, 52.183.30.10, 52.183.39.67 |
| Regno Unito meridionale | 51.140.78.71, 51.140.79.109, 51.140.84.39, 51.140.155.81 |
| Regno Unito occidentale | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| | |

<a name="outbound"></a>

### <a name="outbound-ip-addresses---logic-apps-service--managed-connectors"></a>Indirizzi IP in uscita - servizio App per la logica e connettori gestiti

| Region | IP App per la logica | IP connettori gestiti |
|--------|---------------|-----------------------|
| Australia orientale | 13.75.149.4, 52.187.226.96, 52.187.226.139, 52.187.227.245, 52.187.229.130, 52.187.231.184, 104.210.90.241, 104.210.91.55 | 13.70.72.192 - 13.70.72.207, 13.72.243.10 |
| Australia sud-orientale | 13.70.159.205, 13.73.114.207, 13.77.3.139, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75, 52.189.222.77 | 13.77.50.240 - 13.77.50.255, 13.70.136.174 |
| Brasile meridionale | 191.234.161.28, 191.234.161.168, 191.234.162.131, 191.234.162.178, 191.234.182.26, 191.235.82.221, 191.235.91.7, 191.237.255.116 | 191.233.203.192 - 191.233.203.207, 104.41.59.51 | 
| Canada centrale | 13.71.184.150, 13.71.186.1, 40.85.250.135, 40.85.250.212, 40.85.252.47, 52.233.29.92, 52.228.39.241, 52.228.39.244 | 13.71.170.208 - 13.71.170.223, 13.71.170.224 - 13.71.170.239, 52.237.24.126 |
| Canada orientale | 40.86.203.228, 40.86.216.241, 40.86.217.241, 40.86.226.149, 40.86.228.93, 52.229.120.45, 52.229.126.25, 52.232.128.155 | 40.69.106.240 - 40.69.106.255, 52.242.35.152 |
| India centrale | 52.172.154.168, 52.172.185.79, 52.172.186.159, 104.211.74.145, 104.211.90.162, 104.211.90.169, 104.211.101.108, 104.211.102.62 | 104.211.81.192 - 104.211.81.207, 52.172.211.12 |
| Stati Uniti centrali | 13.67.236.125, 23.100.82.16, 23.100.86.139, 23.100.87.24, 23.100.87.56, 40.113.218.230, 40.122.170.198, 104.208.25.27 | 13.89.171.80 - 13.89.171.95, 52.173.245.164 |
| Asia orientale | 13.75.94.173, 40.83.73.39, 40.83.75.165, 40.83.77.208, 40.83.100.69, 40.83.127.19, 52.175.33.254, 65.52.175.34 | 13.75.36.64 - 13.75.36.79, 52.175.23.169 |
| East US | 13.92.98.111, 23.100.29.190, 23.101.132.208, 23.101.136.201, 23.101.139.153, 40.114.82.191, 40.121.91.41, 104.45.153.81 | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 191.237.41.52 |
| Stati Uniti orientali 2 | 40.70.26.154, 40.70.27.236, 40.70.29.214, 40.70.131.151, 40.84.30.147, 104.208.140.40, 104.208.155.200, 104.208.158.174 | 40.70.146.208 - 40.70.146.223, 52.232.188.154 |
| Giappone orientale | 13.71.158.3, 13.71.158.120, 13.73.4.207, 13.78.18.168, 13.78.20.232, 13.78.21.155, 13.78.35.229, 13.78.42.223 | 13.78.108.0 - 13.78.108.15, 13.71.153.19 |
| Giappone occidentale | 40.74.64.207, 40.74.68.85, 40.74.74.21, 40.74.76.213, 40.74.77.205, 40.74.140.4, 104.214.137.243, 138.91.26.45 | 40.74.100.224 - 40.74.100.239, 104.215.61.248 |
| Stati Uniti centro-settentrionali | 52.162.208.216, 52.162.213.231, 65.52.8.225, 65.52.9.96, 65.52.10.183, 157.55.210.61, 157.55.212.238, 168.62.248.37 | 52.162.107.160 - 52.162.107.175, 52.162.242.161 |
| Europa settentrionale | 40.112.92.104, 40.112.95.216, 40.113.1.181, 40.113.3.202, 40.113.4.18, 40.113.12.95, 52.178.165.215, 52.178.166.21 | 13.69.227.208 - 13.69.227.223, 52.178.150.68 |
| Stati Uniti centro-meridionali | 13.65.82.17, 13.66.52.232, 23.100.124.84, 23.100.127.172, 23.101.183.225, 70.37.54.122, 70.37.50.6, 104.210.144.48 | 104.214.19.48 - 104.214.19.63, 13.65.86.57 |
| India meridionale | 52.172.50.24, 52.172.52.0, 52.172.55.231, 104.211.227.229, 104.211.229.115, 104.211.230.126, 104.211.230.129, 104.211.231.39 | 40.78.194.240 - 40.78.194.255, 13.71.125.22 |
| Asia sud-orientale | 13.67.91.135, 13.67.107.128, 13.67.110.109, 13.76.4.194, 13.76.5.96, 13.76.133.155, 52.163.228.93, 52.163.230.166 | 13.67.8.240 - 13.67.8.255, 52.187.68.19 |
| Stati Uniti centro-occidentali | 13.78.129.20, 13.78.137.179, 13.78.141.75, 13.78.148.140, 13.78.151.161, 52.161.18.218, 52.161.9.108, 52.161.27.190 | 13.71.195.32 - 13.71.195.47, 52.161.102.22 |
| Europa occidentale | 13.95.147.65, 23.97.210.126, 23.97.211.179, 23.97.218.130, 40.68.209.23, 40.68.222.65, 51.144.182.201, 104.45.9.52 | 13.69.64.208 - 13.69.64.223, 52.174.88.118 |
| India occidentale | 104.211.154.7, 104.211.154.59, 104.211.156.153, 104.211.158.123, 104.211.158.127, 104.211.162.205, 104.211.164.80, 104.211.164.136 | 104.211.146.224 - 104.211.146.239, 104.211.189.218 |
| Stati Uniti occidentali | 40.83.164.80, 40.118.244.241, 40.118.241.243, 52.160.92.112, 104.42.38.32, 104.42.49.145, 157.56.162.53, 157.56.167.147 | 40.112.243.160 - 40.112.243.175, 104.42.122.49 |
| Stati Uniti occidentali 2 | 13.66.201.169, 13.66.210.167, 13.66.246.219, 13.77.149.159, 52.175.198.132, 52.183.29.132, 52.183.30.169 | 13.66.140.128 - 13.66.140.143, 52.183.78.157 |
| Regno Unito meridionale | 51.140.28.225, 51.140.73.85, 51.140.74.14, 51.140.78.44, 51.140.137.190, 51.140.142.28, 51.140.153.135, 51.140.158.24 | 51.140.148.0 - 51.140.148.15, 51.140.80.51 |
| Regno Unito occidentale | 51.141.45.238, 51.141.47.136, 51.141.54.185, 51.141.112.112, 51.141.113.36, 51.141.114.77, 51.141.118.119, 51.141.119.63 | 51.140.211.0 - 51.140.211.15, 51.141.47.105 |
||||

## <a name="next-steps"></a>Passaggi successivi  

* Informazioni su come [creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* Informazioni su [esempi e scenari comuni](../logic-apps/logic-apps-examples-and-scenarios.md)
