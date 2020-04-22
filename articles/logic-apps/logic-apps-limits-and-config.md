---
title: Limiti e configurazione
description: Limiti del servizio, ad esempio durata, velocità effettiva e capacità, oltre a valori di configurazione, ad esempio indirizzi IP da consentire, per le app per la logica di AzureService limits, such as duration, throughput, and capacity, plus configuration values, such as IP addresses to allow, for Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: c650cfcbfeddaa83d8bf3127024ac77b93456a57
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683156"
---
# <a name="limits-and-configuration-information-for-azure-logic-apps"></a>Informazioni su limiti e configurazione per App per la logica di Azure

Questo articolo include informazioni dettagliate sui limiti e sulla configurazione per la creazione e l'esecuzione di flussi di lavoro automatici con App per la logica di Azure. Per Power Automate, vedere [Limiti e configurazione in Power Automate](https://docs.microsoft.com/flow/limits-and-config).

<a name="definition-limits"></a>

## <a name="definition-limits"></a>Limiti delle definizioni

Ecco i limiti per una singola definizione di app per la logica:

| Nome | Limite | Note |
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

| Nome | Limite multi-tenant | Limite dell'ambiente del servizio di integrazione | Note |
|------|--------------------|---------------------------------------|-------|
| Durata esecuzione | 90 giorni | 366 giorni | La durata dell'esecuzione viene calcolata utilizzando l'ora di inizio di un'esecuzione e il limite specificato *in fase* di inizio dall'impostazione del flusso di lavoro, Esegui conservazione cronologia [**in giorni**](#change-duration). <p><p>Per modificare il limite predefinito, ovvero 90 giorni, vedere [Modifica durata esecuzione](#change-duration). |
| Eseguire la conservazione nell'archiviazioneRun retention in storage | 90 giorni | 366 giorni | La conservazione delle esecuzione viene calcolata utilizzando l'ora di inizio di un'esecuzione e il limite specificato *all'ora corrente* dall'impostazione del flusso di [**lavoro, Esegui conservazione cronologia in giorni**](#change-retention). Indipendentemente dal completamento o dal timeout di un'esecuzione, il calcolo della conservazione utilizza sempre l'ora di inizio dell'esecuzione. Quando la durata di un'esecuzione supera il limite di conservazione *corrente,* viene rimossa dalla cronologia di esecuzione. <p><p>Se si modifica questa impostazione, il limite corrente viene sempre utilizzato per il calcolo della conservazione, indipendentemente dal limite precedente. Ad esempio, se riduci il limite di conservazione da 90 a 30 giorni, un'esecuzione vecchia di 60 giorni viene rimossa dalla cronologia delle esecuzioni. Se aumenti il periodo di conservazione da 30 a 60 giorni, un'esecuzione vecchia di 20 giorni rimane nella cronologia delle esecuzioni per altri 40 giorni. <p><p>Per modificare il limite predefinito, ovvero 90 giorni, vedere [Conservazione dell'esecuzione delle modifiche nell'archiviazione](#change-retention). |
| Intervallo di ricorrenza minimo | 1 secondo | 1 secondo ||
| Intervallo di ricorrenza massimo | 500 giorni | 500 giorni ||
|||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-run-retention-in-storage"></a>Modificare la durata dell'esecuzione e la conservazione nell'archiviazione

Per modificare il limite predefinito per la durata dell'esecuzione e la conservazione nell'archiviazione, attenersi alla seguente procedura. Per aumentare il limite massimo, [contattare il team app](mailto://logicappsemail@microsoft.com) per la logica per assistenza in base alle proprie esigenze.

> [!NOTE]
> Per le app per la logica in Azure multi-tenant, il limite predefinito di 90 giorni corrisponde al limite massimo. È possibile solo diminuire questo valore.
> Per le app per la logica in un ambiente del servizio di integrazione, è possibile ridurre o aumentare il limite predefinito di 90 giorni.

1. Accedere al [portale di Azure](https://portal.azure.com). Nella casella di ricerca del portale individuare e selezionare **App per**la logica .

1. Selezionare e quindi aprire l'app per la logica in Progettazione app per la logica.

1. Nel menu dell'app per la logica selezionare **Impostazioni flusso di lavoro**.

1. In **Opzioni di runtime**selezionare **Personalizzato**nell'elenco Conservazione cronologia di esecuzione **in giorni.**

1. Trascinare il dispositivo di scorrimento per modificare il numero di giorni desiderato.

1. Al termine, sulla barra degli strumenti **Impostazioni flusso** di lavoro selezionare **Salva**.

<a name="looping-debatching-limits"></a>

## <a name="concurrency-looping-and-debatching-limits"></a>Limiti di concorrenza, cicli e debatching

Ecco i limiti per una singola esecuzione di app per la logica:

| Nome | Limite | Note |
| ---- | ----- | ----- |
| Concorrenza di trigger | - Illimitato quando il controllo della concorrenza è disattivato <p><p>- 25 è il limite predefinito quando il controllo della concorrenza è attivato, che non è possibile annullare dopo aver abilitato la concorrenza.- 25 is the default limit when the concurrency control is turned on, which you can't unundo after you enable concurrency. È possibile modificare il valore predefinito impostandolo su un valore compreso tra 1 e 50, estremi inclusi. | Questo limite descrive il numero più alto di istanze di app per la logica che è possibile eseguire contemporaneamente o in parallelo. <p><p>**Nota:** quando la concorrenza è attivata, il limite SplitOn viene ridotto a 100 elementi per la [distribuzione di matrici.](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) <p><p>Per modificare il limite predefinito e impostarlo su un valore compreso tra 1 e 50 inclusi, vedere [Modificare il limite della concorrenza dei trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) o [Attivare le istanze in sequenza](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Numero massimo di esecuzioni in attesa | - Senza concorrenza, il numero minimo di esecuzioni in attesa è 1, mentre il numero massimo è 50.- Without concurrency, the minimum number of waiting runs is 1, while the maximum number is 50. <p><p>- Con la concorrenza, il numero minimo di esecuzioni in attesa è 10 più il numero di esecuzioni simultanee (concorrenza trigger). È possibile modificare il numero massimo impostando un valore fino a 100 (incluso). | Questo limite descrive il numero più alto di istanze di app per la logica in attesa di esecuzione quando l'app per la logica esegue già il numero massimo di istanze simultanee. <p><p>Per modificare il limite predefinito, vedere [Modificare il limite delle esecuzioni in attesa](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). |
| Elementi della matrice foreach | 100,000 | Questo limite descrive il numero più alto di elementi della matrice che un ciclo "for each" può elaborare. <p><p>Per filtrare matrici di dimensioni superiori, è possibile usare l'[azione di query](logic-apps-perform-data-operations.md#filter-array-action). |
| Concorrenza foreach | 20 è il limite predefinito quando il controllo della concorrenza è disattivato. È possibile modificare il valore predefinito impostandolo su un valore compreso tra 1 e 50, estremi inclusi. | Questo limite indica il numero più alto di iterazioni "for each" che è possibile eseguire contemporaneamente o in parallelo. <p><p>Per modificare il limite predefinito e impostarlo su un valore compreso tra 1 e 50 inclusi, vedere [Modificare il limite della concorrenza "for each"](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) o [Eseguire i cicli "for each" in modo sequenziale](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
| Elementi SplitOn | - 100.000 senza concorrenza del trigger- 100,000 without trigger concurrency <p><p>- 100 con concorrenza del trigger- 100 with trigger concurrency | Per i trigger che restituiscono una matrice, è possibile specificare un'espressione che usa una proprietà 'SplitOn' che [suddivide o esegue il debatch degli elementi della matrice in più istanze del flusso di lavoro](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) per l'elaborazione, anziché usare un ciclo "Foreach". Questa espressione fa riferimento alla matrice da usare per la creazione e l'esecuzione di un'istanza del flusso di lavoro per ogni elemento della matrice. <p><p>**Nota:** quando la concorrenza è attivata, il limite SplitOn viene ridotto a 100 elementi. |
| Iterazioni Until | - Predefinito: 60 <p><p>- Massimo: 5.000 | |
||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>Limiti di velocità effettiva

Ecco i limiti per una singola definizione di app per la logica:

### <a name="multi-tenant-logic-apps-service"></a>Servizio App per la logica multi-tenant

| Nome | Limite | Note |
| ---- | ----- | ----- |
| Azione: esecuzioni per 5 minuti | 100.000 è il limite predefinito, ma 300.000 è il limite massimo. | Per modificare il limite predefinito, vedere [Run your logic app in "high throughput" mode](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode) (Eseguire l'app per la logica in modalità di velocità effettiva elevata), attualmente in anteprima. In alternativa, è possibile distribuire il carico di lavoro tra più app per la logica in base alle esigenze. |
| Azione: chiamate in uscita simultanee | ~2.500 | È possibile diminuire il numero di richieste simultanee o ridurre la durata in base alle esigenze. |
| Endpoint di runtime: chiamate in ingresso simultanee | ~1,000 | È possibile diminuire il numero di richieste simultanee o ridurre la durata in base alle esigenze. |
| Endpoint di runtime: lettura delle chiamate per 5 minuti  | 60.000 | È possibile distribuire un carico di lavoro tra più app in base alle esigenze. |
| Endpoint di runtime: richiamata delle chiamate per 5 minuti | 45,000 | È possibile distribuire un carico di lavoro tra più app in base alle esigenze. |
| Velocità effettiva del contenuto per 5 minuti | 600 MB | È possibile distribuire un carico di lavoro tra più app in base alle esigenze. |
||||

### <a name="integration-service-environment-ise"></a>Ambiente del servizio di integrazione (ISE)Integration service environment (ISE)

Di seguito sono riportati i limiti di velocità effettiva per lo [SKU Premium ISE:](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)

| Nome | Limite | Note |
|------|-------|-------|
| Limite di esecuzione unità di base | Limitazione del sistema quando la capacità dell'infrastruttura raggiunge l'80% | Fornisce 4.000 esecuzioni di azioni al minuto, ovvero 160 milioni di esecuzioni di azioni al mese | |
| Limite di esecuzione dell'unità di scalaScale unit execution limit | Limitazione del sistema quando la capacità dell'infrastruttura raggiunge l'80% | Ogni unità di scala può fornire 2.000 esecuzioni di azioni aggiuntive al minuto, ovvero 80 milioni di dollari in più di esecuzioni di azioni al mese | |
| Numero massimo di unità di scala che è possibile aggiungere | 10 | |
||||

Per superare questi limiti nell'elaborazione normale o per eseguire test di carico che possono superare questi limiti, [contattare il team di App per la logica](mailto://logicappsemail@microsoft.com) per ottenere assistenza sui requisiti specifici.

> [!NOTE]
> Lo [SKU ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) per sviluppatori non ha limiti di pubblicazione, funzionalità per la scalabilità verticale e nessun contratto di servizio (SLA). Usare questo SKU solo per sperimentare, sviluppo e test, non per test di produzione o prestazioni.

<a name="gateway-limits"></a>

## <a name="gateway-limits"></a>Limiti del gateway

Le app per la logica di Azure supportano le operazioni di scrittura, inclusi gli inserimenti e gli aggiornamenti, tramite il gateway. Tuttavia, queste operazioni hanno [limiti sulla loro dimensione del payload](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations).

<a name="request-limits"></a>

## <a name="http-limits"></a>Limiti HTTP

Di seguito sono riportati i limiti per una singola chiamata HTTP in uscita o in arrivo:

#### <a name="timeout"></a>Timeout

Alcune operazioni dei connettori effettuano chiamate asincrone o sono in ascolto di richieste di webhook e di conseguenza il timeout per queste operazioni può essere più prolungato rispetto a questi limiti. Per altre informazioni, vedere i dettagli tecnici per il connettore specifico e anche [Trigger e azioni dei flussi di lavoro](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action).

| Nome | Limite multi-tenant | Limite dell'ambiente del servizio di integrazione | Note |
|------|--------------------|---------------------------------------|-------|
| Richiesta in uscita | 120 secondi <br>(2 minuti) | 240 secondi <br>(4 minuti) | Esempi di richieste in uscita includono le chiamate effettuate da trigger HTTP. <p><p>**Suggerimento**: per operazioni a esecuzione prolungata, utilizzare un modello di [polling asincrono](../logic-apps/logic-apps-create-api-app.md#async-pattern) o un [ciclo di finca](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). |
| Richiesta in entrata | 120 secondi <br>(2 minuti) | 240 secondi <br>(4 minuti) | Esempi di richieste in ingresso includono le chiamate ricevute dai trigger di richiesta e i trigger di webhook. <p><p>**Nota:** affinché il chiamante originale otturi la risposta, tutti i passaggi della risposta devono terminare entro il limite, a meno che non venga chiamata un'altra app per la logica come flusso di lavoro annidato. Per altre informazioni, vedere [Chiamare, attivare o annidare app per la logica](../logic-apps/logic-apps-http-endpoint.md). |
|||||

<a name="message-size-limits"></a>

#### <a name="message-size"></a>Dimensioni dei messaggi

| Nome | Limite multi-tenant | Limite dell'ambiente del servizio di integrazione | Note |
|------|--------------------|---------------------------------------|-------|
| Dimensioni dei messaggi | 100 MB | 200 MB | I connettori con etichetta ISE utilizzano il limite ISE, non i limiti dei connettori non ISE. <p><p>Per ignorare questo limite, vedere [Gestire messaggi di grandi dimensioni con la divisione in blocchi](../logic-apps/logic-apps-handle-large-messages.md). Tuttavia, alcuni connettori e API potrebbero non supportare la divisione in blocchi o addirittura il limite predefinito. |
| Dimensione dei messaggi con la divisione in blocchi | 1 GB | 5 GB | Questo limite si applica alle azioni che supportano in modo nativo la suddivisione in blocchi o consentono di abilitare la suddivisione in blocchi nella configurazione di runtime. <p><p>Per l'ambiente del servizio di integrazione, il motore App per la logica supporta questo limite, ma i connettori hanno i propri limiti di suddivisione in blocchi fino al limite del motore, ad esempio, vedere le [informazioni di riferimento sull'API del connettore di archiviazione BLOB di Azure.](https://docs.microsoft.com/connectors/azureblob/) Per ulteriori informazioni sulla suddivisione in blocchi, vedere [Gestire messaggi di grandi dimensioni con la suddivisione in blocchi](../logic-apps/logic-apps-handle-large-messages.md). |
|||||

#### <a name="character-limits"></a>Limiti dei caratteri

| Nome | Note |
|------|-------|
| Limite per la valutazione delle espressioni | 131.072 caratteri | Le espressioni `@concat()`, `@base64()` e `@string()` non possono superare questo limite. |
| Limite di caratteri DELL'URL della richiesta | 16.384 caratteri |
|||

<a name="retry-policy-limits"></a>

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

| Nome | Limite multi-tenant | Limite dell'ambiente del servizio di integrazione | Note |
|------|--------------------|---------------------------------------|-------|
| Numero di connettori personalizzati | 1.000 per ogni sottoscrizione di Azure | 1.000 per ogni sottoscrizione di Azure ||
| Numero di richieste al minuto per un connettore personalizzato | 500 richieste al minuto per connessione | 2.000 richieste al minuto per *connettore personalizzato* ||
|||

<a name="managed-identity"></a>

## <a name="managed-identities"></a>Identità gestite

| Nome | Limite |
|------|-------|
| Identità gestite per app per la logica | L'identità assegnata dal sistema o 1 l'identità assegnata dall'utente |
| Numero di app per la logica con un'identità gestita in una sottoscrizione di Azure per ogni areaNumber of logic apps that have a managed identity in an Azure subscription per region | 250 |
|||

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>Limiti dell'account di integrazione

Ogni sottoscrizione di Azure ha questi limiti di account di integrazione:Each Azure subscription has these integration account limits:

* Un account di integrazione [di livello gratuito](../logic-apps/logic-apps-pricing.md#integration-accounts) per ogni area di AzureOne Free tier integration account per Azure region

* 1.000 account di integrazione totali, inclusi gli account di integrazione in qualsiasi [ambiente del servizio di integrazione (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) negli [SKU Developer e Premium.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)

* Ogni ISE, sia [Developer che Premium,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)è limitata a 5 account di integrazione totale:

  | ISE SKU | Limiti dell'account di integrazione |
  |---------|----------------------------|
  | **Premium** | 5 totale - Solo conti [standard,](../logic-apps/logic-apps-pricing.md#integration-accounts) incluso un account Standard gratuito. Non sono ammessi account gratuiti o di base. |
  | **Developer** | 5 totale - [Gratuito](../logic-apps/logic-apps-pricing.md#integration-accounts) (limitato a 1 account) e [Standard](../logic-apps/logic-apps-pricing.md#integration-accounts) combinato, o tutti gli account Standard. Non sono ammessi account Basic. Usare lo [SKU per sviluppatori](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) per sperimentare, eseguire lo sviluppo e il test, ma non per i test di produzione o delle prestazioni. |
  |||

I costi aggiuntivi si applicano agli account di integrazione aggiunti oltre agli account di integrazione inclusi in ISE. Per informazioni sul funzionamento dei prezzi e della fatturazione per ISE, vedere il modello di determinazione dei prezzi app per la [logica.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Per i prezzi, vedere [Prezzi di App per la logica.](https://azure.microsoft.com/pricing/details/logic-apps/)

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>Limite di elementi per account di integrazione

Ecco i limiti del numero di elementi per ogni livello di account di integrazione.
Per i prezzi, vedere [Prezzi di App per la logica.](https://azure.microsoft.com/pricing/details/logic-apps/) Per informazioni sul funzionamento dei prezzi e della fatturazione per gli account di integrazione, vedere il modello di determinazione dei prezzi di App per la [logica.](../logic-apps/logic-apps-pricing.md#integration-accounts)

> [!NOTE]
> Usare il livello Gratuito solo per scenari esplorativi, non per scenari di produzione. Questo livello limita la velocità effettiva e l'utilizzo e non dispone di alcun contratto di servizio (SLA).

| Elemento | Gratuito | Basic | Standard |
|----------|------|-------|----------|
| Contratti commerciali EDI | 10 | 1 | 1\.000 |
| Partner commerciali EDI | 25 | 2 | 1\.000 |
| Mappe | 25 | 500 | 1\.000 |
| Schemi | 25 | 500 | 1\.000 |
| Assembly | 10 | 25 | 1\.000 |
| Certificati | 25 | 2 | 1\.000 |
| Configurazioni batch | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>Limiti di capacità degli elementi

| Elemento | Limite | Note |
| -------- | ----- | ----- |
| Assembly | 8 MB | Per caricare file di dimensioni superiori a 2 MB, usare un [account di archiviazione di Azure e un contenitore BLOB](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Mappa (file XSLT) | 8 MB | Per caricare file di dimensioni superiori a 2 MB, usare l'[API REST di App per la logica di Azure Maps](https://docs.microsoft.com/rest/api/logic/maps/createorupdate). <p><p>**Nota:** la quantità di dati o record che una mappa può elaborare correttamente si basa sui limiti di timeout delle dimensioni dei messaggi e delle azioni nelle app per la logica di Azure.Note : The amount of data or records that a map can successfully process is based on the message size and action timeout limits in Azure Logic Apps. Ad esempio, se si utilizza un'azione HTTP, basata sulla dimensione dei messaggi HTTP e sui limiti di [timeout,](#request-limits)un mapping può elaborare i dati fino al limite di dimensione dei messaggi HTTP se l'operazione viene completata entro il limite di timeout HTTP. |
| SCHEMA | 8 MB | Per caricare file di dimensioni superiori a 2 MB, usare un [account di archiviazione di Azure e un contenitore BLOB](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>Limiti di velocità effettiva

| Endpoint di runtime | Gratuito | Basic | Standard | Note |
|------------------|------|-------|----------|-------|
| leggere le chiamate per 5 minuti | 3,000 | 30.000 | 60.000 | È possibile distribuire il carico di lavoro tra più di un account in base alle esigenze. |
| richiamare le chiamate per 5 minuti | 3,000 | 30.000 | 45,000 | È possibile distribuire il carico di lavoro tra più di un account in base alle esigenze. |
| verifica delle chiamate per 5 minuti | 3,000 | 30.000 | 45,000 | È possibile distribuire il carico di lavoro tra più di un account in base alle esigenze. |
| blocco di chiamate simultanee | ~1,000 | ~1,000 | ~1,000 | Lo stesso vale per tutti gli SKU. È possibile diminuire il numero di richieste simultanee o ridurre la durata in base alle esigenze. |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>Dimensioni dei messaggi per i protocolli B2B (AS2, X12, EDIFACT)

Di seguito sono riportati i limiti di dimensione dei messaggi applicabili ai protocolli B2B:

| Nome | Limite multi-tenant | Limite dell'ambiente del servizio di integrazione | Note |
|------|--------------------|---------------------------------------|-------|
| AS2 | v2 - 100 MB<br>v1 - 50 MB | v2 - 200 MB <br>v1 - 50 MB | Applicabile alla decodifica e alla codifica |
| X12 | 50 MB | 50 MB | Applicabile alla decodifica e alla codifica |
| EDIFACT | 50 MB | 50 MB | Applicabile alla decodifica e alla codifica |
||||

<a name="disable-delete"></a>

## <a name="disabling-or-deleting-logic-apps"></a>Disabilitazione o eliminazione di app per la logica

Quando si disabilita un'app per la logica, non viene eseguita alcuna nuova istanza di esecuzione. Tutte le esecuzioni in corso e in sospeso continuano fino al completamento, operazione che potrebbe richiedere tempo.

Quando si elimina un'app per la logica, non viene eseguita alcuna nuova istanza di esecuzione. Tutte le esecuzioni in corso e in sospeso vengono annullate. Se si dispone di migliaia di esecuzioni, l'annullamento potrebbe richiedere molto tempo.

<a name="configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>Configurazione firewall: indirizzi IP e tag di servizio

Gli indirizzi IP utilizzati da App per la logica di Azure per le chiamate in ingresso e in uscita dipendono dall'area in cui è presente l'app per la logica. *Tutte le* app per la logica nella stessa area usano gli stessi intervalli di indirizzi IP. Alcune chiamate [di Power Automate,](https://docs.microsoft.com/power-automate/getting-started) ad esempio le richieste **HTTP** e HTTP **e OpenAPI,** passano direttamente attraverso il servizio App per la logica di Azure e provengono dagli indirizzi IP elencati di seguito. Per ulteriori informazioni sugli indirizzi IP utilizzati da Power Automate, vedere [Limiti e configurazione in Power Automate](https://docs.microsoft.com/flow/limits-and-config#ip-address-configuration).

> [!TIP]
> Per ridurre la complessità quando si creano regole di sicurezza, è possibile utilizzare facoltativamente [i tag](../virtual-network/service-tags-overview.md)di servizio anziché specificare gli indirizzi IP delle app per la logica per ogni area, descritti più avanti in questa sezione. Questi tag funzionano tra le aree in cui è disponibile il servizio App per la logica:These tags work across the regions where the Logic Apps service is available:
>
> * **LogicAppsManagement**: Rappresenta i prefissi degli indirizzi IP in ingresso per il servizio App per la logica.
> * **LogicApps**: Rappresenta i prefissi degli indirizzi IP in uscita per il servizio App per la logica.

* Per [Azure China 21Vianet](https://docs.microsoft.com/azure/china/), gli indirizzi IP fissi o riservati non sono disponibili per [i connettori personalizzati](../logic-apps/custom-connector-overview.md) e i [connettori gestiti,](../connectors/apis-list.md#managed-api-connectors)ad esempio Archiviazione di Azure, SQL Server, Office 365 Outlook e così via.

* Per supportare le chiamate effettuate direttamente dalle app per la logica con [HTTP,](../connectors/connectors-native-http.md) [HTTP e](../connectors/connectors-native-http-swagger.md)altre richieste HTTP, impostare il firewall con tutti gli indirizzi IP [in ingresso](#inbound) *e* [in uscita](#outbound) utilizzati dal servizio App per la logica, in base alle aree in cui sono presenti le app per la logica. Questi indirizzi vengono visualizzati sotto le intestazioni **In ingresso** e **In uscita** in questa sezione e vengono ordinati in base all'area.

* Per supportare le chiamate effettuate dai [connettori gestiti,](../connectors/apis-list.md#managed-api-connectors) configurare il firewall con *tutti* gli indirizzi IP [in uscita](#outbound) usati da questi connettori, in base alle aree in cui sono presenti le app per la logica. Questi indirizzi vengono visualizzati sotto l'intestazione **In uscita** in questa sezione e vengono ordinati in base all'area.

* Per abilitare la comunicazione per le app per la logica in esecuzione in un ambiente del servizio di integrazione (ISE), assicurarsi di [aprire queste porte.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise)

* Se le app per la logica hanno problemi di accesso agli account di archiviazione di Azure che usano [firewall e regole del firewall,](../storage/common/storage-network-security.md)sono disponibili varie opzioni per [abilitare l'accesso.](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)

  Ad esempio, le app per la logica non possono accedere direttamente agli account di archiviazione che usano regole del firewall ed esistono nella stessa area. Tuttavia, se si consentono [gli indirizzi IP in uscita per i connettori gestiti nell'area,](../logic-apps/logic-apps-limits-and-config.md#outbound)le app per la logica possono accedere agli account di archiviazione che si trovano in un'area diversa, tranne quando si usano i connettori Archiviazione tabelle di Azure o Archiviazione coda di Azure.However, if you permit the outbound IP addresses for managed connectors in your region , your logic apps can access storage accounts that are in a different region except when you use the Azure Table Storage or Azure Queue Storage connectors. Per accedere all'archiviazione tabelle o all'archiviazione delle code, è possibile usare il trigger HTTP e le azioni. Per altre opzioni, vedere [Accedere agli account di archiviazione protetti da firewall](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>Indirizzi IP in ingresso

Questa sezione elenca solo gli indirizzi IP in ingresso per il servizio App per la logica di Azure.This section lists the inbound IP addresses for the Azure Logic Apps service only. Se si dispone di Azure per enti pubblici, vedere Azure per enti [pubblici - Indirizzi IP in ingresso](#azure-government-inbound).

> [!TIP]
> Per ridurre la complessità quando si creano regole di sicurezza, è possibile utilizzare facoltativamente il [tag del servizio,](../virtual-network/service-tags-overview.md) **LogicAppsManagement**, anziché specificare i prefissi degli indirizzi IP delle app per la logica in ingresso per ogni area. Questo tag funziona tra le aree in cui è disponibile il servizio App per la logica.

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant-azure---inbound-ip-addresses"></a>Multi-tenant Azure - Indirizzi IP in ingressoMulti-tenant Azure - Inbound IP addresses

| Area multi-tenant | IP |
|---------------------|----|
| Australia orientale | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161 |
| Australia sud-orientale | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brasile meridionale | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198 |
| Canada centrale | 13.88.249.209, 52.233.30.218, 52.233.29.79, 40.85.241.105 |
| Canada orientale | 52.232.129.143, 52.229.125.57, 52.232.133.109, 40.86.202.42 |
| India centrale | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| Stati Uniti centrali | 13.67.236.76, 40.77.111.254, 40.77.31.87, 104.43.243.39 |
| Asia orientale | 168.63.200.173, 13.75.89.159, 23.97.68.172, 40.83.98.194 |
| Stati Uniti orientali | 137.135.106.54, 40.117.99.79, 40.117.100.228, 137.116.126.165 |
| Stati Uniti orientali 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136, 40.70.27.253 |
| Francia centrale | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203 |
| Francia meridionale | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Giappone orientale | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164 |
| Giappone occidentale | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Corea centrale | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Corea meridionale | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| Stati Uniti centro-settentrionali | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| Europa settentrionale | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39 |
| Sudafrica settentrionale | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| Sudafrica Ovest | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| Stati Uniti centro-meridionali | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| India meridionale | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Asia sud-orientale | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Emirati Arabi Uniti centrali | 20.45.75.193, 20.45.64.29, 20.45.64.87, 20.45.71.213 |
| Regno Unito meridionale | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81 |
| Regno Unito occidentale | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| Stati Uniti centro-occidentali | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| Europa occidentale | 13.95.155.53, 52.174.54.218, 52.174.49.6, 52.174.49.6 |
| India occidentale | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| Stati Uniti occidentali | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| Stati Uniti occidentali 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68 |
|||

<a name="azure-government-inbound"></a>

#### <a name="azure-government---inbound-ip-addresses"></a>Azure per enti pubblici - Indirizzi IP in ingressoAzure Government - Inbound IP addresses

| Area di Azure per enti pubbliciAzure Government region | IP |
|-------------------------|----|
| US Gov Arizona | 52.244.67.164, 52.244.67.64, 52.244.66.82 |
| US Gov Texas | 52.238.119.104, 52.238.112.96, 52.238.119.145 |
| US Gov Virginia | 52.227.159.157, 52.227.152.90, 23.97.4.36 |
| US DoD (area centrale) | 52.182.49.204, 52.182.52.106 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses"></a>Indirizzi IP in uscita

Questa sezione elenca gli indirizzi IP in uscita per il servizio App per la logica di Azure e i connettori gestiti. Se si dispone di Azure per enti pubblici, vedere Azure per enti [pubblici - Indirizzi IP in uscita](#azure-government-outbound).

> [!TIP]
> Per ridurre la complessità quando si creano regole di sicurezza, è possibile utilizzare facoltativamente il tag di [servizio](../virtual-network/service-tags-overview.md), **LogicApps**, anziché specificare i prefissi degli indirizzi IP delle app per la logica in uscita per ogni area. Questo tag funziona tra le aree in cui è disponibile il servizio App per la logica. Per i connettori gestiti, è necessario continuare a utilizzare gli indirizzi IP.

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant-azure---outbound-ip-addresses"></a>Multi-tenant Azure - Indirizzi IP in uscitaMulti-tenant Azure - Outbound IP addresses

| Area multi-tenant | IP App per la logica | IP connettori gestiti |
|---------------------|---------------|-----------------------|
| Australia orientale | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139 | 13.70.72.192 - 13.70.72.207, 13.72.243.10, 40.126.251.213, 52.237.214.72 |
| Australia sud-orientale | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 | 13.70.136.174, 13.77.50.240 - 13.77.50.255, 40.127.80.34, 52.255.48.202 |
| Brasile meridionale | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131 | 104.41.59.51, 191.232.38.129, 191.233.203.192 - 191.233.203.207, 191.232.191.157 |
| Canada centrale | 52.233.29.92, 52.228.39.241, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150 | 13.71.170.208 - 13.71.170.223, 52.228.33.76, 52.228.34.13, 52.228.42.205, 52.233.31.197, 52.237.24.126, 52.237.32.212 |
| Canada orientale | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 | 40.69.106.240 - 40.69.106.255, 52.229.120.52, 52.229.120.178, 52.229.123.98, 52.229.126.202, 52.242.35.152, 52.242.30.112 |
| India centrale | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 | 52.172.211.12, 104.211.81.192 - 104.211.81.207, 104.211.98.164, 52.172.212.129 |
| Stati Uniti centrali | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16 | 13.89.171.80 - 13.89.171.95, 40.122.49.51, 52.173.245.164, 52.173.241.27 |
| Asia orientale | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 | 13.75.36.64 - 13.75.36.79, 23.99.116.181, 52.175.23.169, 13.75.110.131 |
| Stati Uniti orientali | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208 | 40.71.11.80 - 40.71.11.95, 40.71.249.205, 40.114.40.132, 40.71.249.139 |
| Stati Uniti orientali 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236 | 40.70.146.208 - 40.70.146.223, 52.232.188.154, 104.208.233.100, 104.209.247.23, 52.225.129.144 |
| Francia centrale | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104 | 40.79.130.208 - 40.79.130.223, 40.89.135.2, 40.89.186.239 |
| Francia meridionale | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 | 40.79.178.240 - 40.79.178.255, 52.136.133.184, 52.136.142.154 |
| Giappone orientale | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232 | 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.115.186.96, 13.73.21.230 |
| Giappone occidentale | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 | 40.74.100.224 - 40.74.100.239, 40.74.130.77, 104.215.61.248, 104.215.27.24 |
| Corea centrale | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 | 52.231.18.208 - 52.231.18.223, 52.141.36.214, 52.141.1.104 |
| Corea meridionale | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 | 52.231.147.0 - 52.231.147.15, 52.231.163.10, 52.231.201.173 |
| Stati Uniti centro-settentrionali | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 | 52.162.107.160 - 52.162.107.175, 52.162.242.161, 65.52.218.230, 52.162.126.4 |
| Europa settentrionale | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181 | 13.69.227.208 - 13.69.227.223, 52.178.150.68, 104.45.93.9, 94.245.91.93, 52.169.28.181 |
| Sudafrica settentrionale | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191, 102.133.168.167 |
| Sudafrica Ovest | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191, 102.133.72.85 |
| Stati Uniti centro-meridionali | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225 | 13.65.86.57, 104.214.19.48 - 104.214.19.63, 104.214.70.191, 52.171.130.92 |
| India meridionale | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 | 13.71.125.22, 40.78.194.240 - 40.78.194.255, 104.211.227.225, 13.71.127.26 |
| Asia sud-orientale | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128 | 13.67.8.240 - 13.67.8.255, 13.76.231.68, 52.187.68.19, 52.187.115.69 |
| Emirati Arabi Uniti centrali | 20.45.75.200, 20.45.72.72, 20.45.75.236, 20.45.79.239, 20.45.67.170, 20.45.72.54, 20.45.67.134, 20.45.67.135 | 20.45.67.28, 20.45.67.45, 20.37.74.192 - 20.37.74.207, 40.120.8.0 - 40.120.8.31 |
| Regno Unito meridionale | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24 | 51.140.80.51, 51.140.148.0 - 51.140.148.15, 51.140.61.124, 51.140.74.150 |
| Regno Unito occidentale | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 | 51.140.211.0 - 51.140.211.15, 51.141.47.105, 51.141.124.13, 51.141.52.185 |
| Stati Uniti centro-occidentali | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75 | 13.71.195.32 - 13.71.195.47, 52.161.102.22, 13.78.132.82, 52.161.101.204 |
| Europa occidentale | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126 | 13.69.64.208 - 13.69.64.223, 40.115.50.13, 52.174.88.118, 40.91.208.65, 52.166.78.89 |
| India occidentale | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 | 104.211.146.224 - 104.211.146.239, 104.211.161.203, 104.211.189.218, 104.211.189.124 |
| Stati Uniti occidentali | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32 | 40.112.243.160 - 40.112.243.175, 104.40.51.248, 104.42.122.49, 40.112.195.87, 13.93.148.62 |
| Stati Uniti occidentali 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 | 13.66.140.128 - 13.66.140.143, 52.183.78.157, 52.191.164.250 |
||||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Azure per enti pubblici - Indirizzi IP in uscitaAzure Government - Outbound IP addresses

| Region | IP App per la logica | IP connettori gestiti |
|--------|---------------|-----------------------|
| US Gov Arizona | 52.244.67.143, 52.244.65.66, 52.244.65.190 | 52.127.2.160 - 52.127.2.175, 52.244.69.0, 52.244.64.91 |
| US Gov Texas | 52.238.114.217, 52.238.115.245, 52.238.117.119 | 52.127.34.160 - 52.127.34.175, 40.112.40.25, 52.238.161.225 |
| US Gov Virginia | 13.72.54.205, 52.227.138.30, 52.227.152.44 | 52.127.42.128 - 52.127.42.143, 52.227.143.61, 52.227.162.91 |
| US DoD (area centrale) | 52.182.48.215, 52.182.92.143 | 52.127.58.160 - 52.127.58.175, 52.182.54.8, 52.182.48.136 |
||||

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)  
* Informazioni su [esempi e scenari comuni](../logic-apps/logic-apps-examples-and-scenarios.md)
