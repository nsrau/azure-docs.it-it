---
title: Modalità e requisiti di connettività
description: Illustra le opzioni di connettività dei servizi dati abilitati per Azure Arc per dal proprio ambiente ad Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: c7bff21a17af3c908caeed6a1e60de8e2fe4efc9
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287589"
---
# <a name="connectivity-modes-and-requirements"></a>Modalità di connettività e requisiti

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes"></a>Modalità di connettività

Sono disponibili più opzioni per il grado di connettività dall'ambiente dei servizi dati abilitati per Azure Arc ad Azure. Poiché i requisiti variano in base ai criteri aziendali, al regolamento governativo o alla disponibilità della connettività di rete ad Azure, è possibile scegliere tra le modalità di connettività seguenti.

Azure Arc Enabled Data Services offre la possibilità di connettersi ad Azure con due diverse *modalità di connettività* : 

- Connessione diretta 
- Connesso indirettamente

La modalità di connettività offre la flessibilità necessaria per scegliere la quantità di dati inviata ad Azure e il modo in cui gli utenti interagiscono con il controller di dati Arc. A seconda della modalità di connettività scelta, è possibile che alcune funzionalità di Azure Arc Enabled Data Services siano disponibili o meno.

In particolare, se i servizi dati abilitati per Azure Arc sono connessi direttamente ad Azure, gli utenti possono usare [Azure Resource Manager API](/rest/api/resources/), l'interfaccia della riga di comando di Azure e il portale di Azure per usare i servizi dati di Azure Arc. L'esperienza in modalità di connessione diretta è molto simile a quella usata per qualsiasi altro servizio di Azure con provisioning/deprovisioning, ridimensionamento, configurazione e così via nel portale di Azure.  Se i servizi dati abilitati per Azure Arc sono connessi indirettamente ad Azure, il portale di Azure è una vista di sola lettura. È possibile visualizzare l'inventario delle istanze gestite di SQL e delle istanze di iperscala Postgres distribuite e i relativi dettagli, ma non è possibile intervenire su di essi nell'portale di Azure.  In modalità indirettamente connessa, tutte le azioni devono essere eseguite localmente usando Azure Data Studio, gli [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] strumenti nativi di o Kubernetes come kubectl.

Inoltre, è possibile usare il controllo di accesso Azure Active Directory e Azure Role-Based solo in modalità con connessione diretta solo perché esiste una dipendenza da una connessione continua e diretta ad Azure per fornire questa funzionalità.

Alcuni servizi collegati ad Azure sono disponibili solo quando è possibile raggiungerli direttamente, ad esempio Azure Defender Security Services, container Insights e backup di Azure nell'archivio BLOB.

Attualmente, nell'anteprima solo la modalità connessa indirettamente è supportata. 

||**Connesso indirettamente**|**Connessione diretta**|**Mai connesso**|
|---|---|---|---|
|**Descrizione**|La modalità connessa indirettamente offre la maggior parte dei servizi di gestione localmente nell'ambiente senza connessione diretta ad Azure.  È necessario inviare una quantità minima di dati ad Azure _solo_ a scopo di inventario e fatturazione. Viene esportato in un file e caricato in Azure almeno una volta al mese.  Non è necessaria alcuna connessione diretta o continua ad Azure.  Alcune funzionalità e servizi che richiedono una connessione ad Azure non saranno disponibili.|La modalità connessa direttamente offre tutti i servizi disponibili quando è possibile stabilire una connessione diretta con Azure. Le connessioni vengono sempre avviate _dall'_ ambiente in Azure e usano porte e protocolli standard, ad esempio https/443.|Non è possibile inviare dati da o verso Azure in alcun modo.|
|**Disponibilità corrente**| disponibilità in anteprima.|Pianificato per l'anteprima in futuro.|Non è attualmente supportato.|
|**Casi d'uso tipici**|Data Center locali che non consentono la connettività all'interno o all'esterno dell'area dati del data center a causa di criteri di conformità aziendali o normativi o di problemi di attacchi esterni o di exfiltration di dati.  Esempi tipici: istituti finanziari, assistenza sanitaria, enti pubblici. <br/><br/>Percorsi del sito perimetrale in cui il sito Edge non dispone in genere di connettività a Internet.  Esempi tipici: applicazioni per campi petroliferi, gas o militari.  <br/><br/>Località del sito Edge con connettività intermittente con lunghi periodi di interruzioni.  Esempi tipici: stadi, Cruise Ships. | Organizzazioni che usano cloud pubblici.  Esempi tipici: Azure, AWS o Google Cloud.<br/><br/>Località del sito Edge in cui la connettività Internet è in genere presente e consentita.  Esempi tipici: negozi al dettaglio, produzione.<br/><br/>Data center aziendali con criteri più permissivi per la connettività a/dall'area dati del Data Center a Internet.  Esempi tipici: le aziende non regolamentate, le aziende di piccole e medie dimensioni|Ambienti realmente "gapped" in cui nessun dato in nessuna circostanza può provenire o andare dall'ambiente dei dati. Esempi tipici: principali strutture per enti pubblici.|
|**Modalità di invio dei dati ad Azure**|Sono disponibili tre opzioni per il modo in cui è possibile inviare i dati di fatturazione e inventario ad Azure:<br><br> 1) i dati vengono esportati dall'area dati tramite un processo automatizzato dotato di connettività all'area dati protetta e ad Azure.<br><br>2) i dati vengono esportati dall'area dati tramite un processo automatizzato all'interno dell'area dati, copiati automaticamente in un'area meno sicura e un processo automatizzato nell'area meno sicura carica i dati in Azure.<br><br>3) i dati vengono esportati manualmente da un utente all'interno dell'area protetta, portati manualmente dall'area protetta e caricati manualmente in Azure. <br><br>Le prime due opzioni sono un processo continuo automatico che può essere pianificato per l'esecuzione frequente, in modo da garantire un ritardo minimo nel trasferimento dei dati in Azure soggetto solo alla connettività disponibile ad Azure.|I dati vengono inviati in modo automatico e continuo ad Azure.|I dati non vengono mai inviati ad Azure.|

## <a name="feature-availability-by-connectivity-mode"></a>Disponibilità delle funzionalità in base alla modalità di connettività

|**Funzionalità**|**Connesso indirettamente**|**Connessione diretta**|
|---|---|---|
|**Disponibilità elevata automatica**|Supportato|Supportato|
|**Provisioning self-service**|Supportato<br/>La creazione può essere eseguita tramite [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] gli strumenti nativi di Azure Data Studio, o Kubernetes (Helm, kubectl, OC e così via) o con Azure Arc abilitato Kubernetes GitOps provisioning.|Supportato<br/>Oltre alle opzioni di creazione in modalità di connessione indirettamente, è anche possibile creare tramite la portale di Azure, Azure Resource Manager API, l'interfaccia della riga di comando di Azure o i modelli ARM. **Disponibilità in sospeso della modalità di connessione diretta**
|**Scalabilità elastica**|Supportato|Supportato<br/>**Disponibilità in sospeso della modalità di connessione diretta**|
|**Fatturazione**|Supportato<br/>I dati di fatturazione vengono periodicamente esportati e inviati ad Azure.|Supportato<br/>I dati di fatturazione vengono inviati in modo automatico e continuo ad Azure e si riflettono quasi in tempo reale. **Disponibilità in sospeso della modalità di connessione diretta**|
|**Gestione dell'inventario**|Supportato<br/>I dati di inventario vengono periodicamente esportati e inviati ad Azure.<br/><br/>Usa strumenti client come Azure Data Studio, l'interfaccia della `kubectl` riga di comando di Azure o per visualizzare e gestire l'inventario localmente.|Supportato<br/>I dati di inventario vengono inviati in modo automatico e continuo ad Azure e si riflettono quasi in tempo reale. Di conseguenza, è possibile gestire l'inventario direttamente dalla portale di Azure. **Disponibilità in sospeso della modalità di connessione diretta**|
|**Aggiornamenti automatici e applicazione di patch**|Supportato<br/>Il controller di dati deve avere accesso diretto a Microsoft Container Registry (di Microsoft) o alle immagini del contenitore che devono essere recuperate da e inviate a un registro contenitori locale e privato a cui il controller dati può accedere.|Supportato<br/>**Disponibilità in sospeso della modalità di connessione diretta**|
|**Backup e ripristino automatici**|Supportato<br/>Backup locale automatico e ripristino.|Supportato<br/>Oltre al backup e ripristino locale automatizzato, è possibile _inviare i_ backup a backup di Azure per la conservazione a lungo termine e fuori sede. **Disponibilità in sospeso della modalità di connessione diretta**|
|**Monitoring**|Supportato<br/>Monitoraggio locale con i dashboard Grafana e Kibana.|Supportato<br/>Oltre ai dashboard di monitoraggio locali, è possibile inviare _facoltativamente_ i dati di monitoraggio e i log a monitoraggio di Azure per monitorare su larga scala più siti in un'unica posizione. **Disponibilità in sospeso della modalità di connessione diretta**|
|**autenticazione**|Usare il nome utente/password locale per l'autenticazione del controller di dati e del dashboard. Usare gli account di accesso di SQL e Postgres o Active Directory per la connettività alle istanze del database.  Usare i provider di autenticazione K8s per l'autenticazione per l'API Kubernetes.|In aggiunta a o al posto dei metodi di autenticazione per la modalità connessa indirettamente, è possibile usare _facoltativamente_ Azure Active Directory. **Disponibilità in sospeso della modalità di connessione diretta**|
|**Controllo degli accessi in base al ruolo**|Usare Kubernetes RBAC nell'API Kubernetes. Usare SQL e Postgres RBAC per le istanze di database.|Facoltativamente, è possibile integrare con Azure Active Directory e il controllo degli accessi in base al ruolo **Disponibilità in sospeso della modalità di connessione diretta**|
|**Azure Defender**|Non supportato|Pianificato per il futuro|

## <a name="connectivity-requirements"></a>Requisiti di connettività

**Alcune funzionalità richiedono una connessione ad Azure.**

**Tutte le comunicazioni con Azure vengono sempre avviate dall'ambiente.** Questo vale anche per le operazioni, che vengono avviate da un utente nel portale di Azure.  In tal caso, un'attività viene accodata in Azure.  Un agente dell'ambiente avvia la comunicazione con Azure per vedere quali attività sono presenti nella coda, esegue le attività e segnala lo stato/completamento/errore in Azure.

|**Tipo di dati**|**Direzione**|**Obbligatorio/facoltativo**|**Costi aggiuntivi**|**Modalità obbligatoria**|**Note**|
|---|---|---|---|---|---|
|**Immagini contenitore**|Microsoft Container Registry-> cliente|Obbligatoria|No|Indiretta o diretta|Le immagini del contenitore sono il metodo per distribuire il software.  In un ambiente che è in grado di connettersi a Microsoft Container Registry (di Microsoft) tramite Internet, le immagini del contenitore possono essere estratte direttamente da.  Nel caso in cui l'ambiente di distribuzione non disponga di connettività diretta, è possibile eseguire il pull delle immagini da e inserirle in un registro contenitori privato nell'ambiente di distribuzione.  Al momento della creazione, è possibile configurare il processo di creazione per effettuare il pull dal registro contenitori privato anziché da. Questo vale anche per gli aggiornamenti automatici.|
|**Inventario delle risorse**|Ambiente del cliente-> Azure|Obbligatoria|No|Indiretta o diretta|Un inventario dei controller dati, delle istanze di database (PostgreSQL e SQL) viene mantenuto in Azure a scopo di fatturazione e anche per la creazione di un inventario di tutti i controller di dati e delle istanze di database in un'unica posizione, particolarmente utile se si dispone di più di un ambiente con i servizi dati di Azure Arc.  Quando viene eseguito il provisioning delle istanze, il deprovisioning, la scalabilità orizzontale e orizzontale, l'inventario viene aggiornato in Azure.|
|**Dati di telemetria di fatturazione**|Ambiente del cliente-> Azure|Obbligatoria|No|Indiretta o diretta|L'utilizzo delle istanze di database deve essere inviato ad Azure per finalità di fatturazione.  Non è previsto alcun costo per i servizi dati abilitati per Azure Arc durante il periodo di anteprima.|
|**Monitoraggio di dati e log**|Ambiente del cliente-> Azure|Facoltativo|Probabilmente a seconda del volume dei dati (vedere [prezzi di monitoraggio di Azure](https://azure.microsoft.com/en-us/pricing/details/monitor/))|Indiretta o diretta|È possibile inviare i log e i dati di monitoraggio raccolti localmente a monitoraggio di Azure per aggregare i dati in più ambienti in un'unica posizione e anche per usare i servizi di monitoraggio di Azure, come gli avvisi, usando i dati in Azure Machine Learning e così via.|
|**Controllo degli accessi in base al ruolo di Azure (RBAC di Azure)**|Ambiente del cliente: ambiente del cliente > Azure >|Facoltativo|No|Solo diretta|Se si vuole usare il controllo degli accessi in base al ruolo di Azure, è necessario stabilire la connettività sempre con Azure.  Se non si vuole usare il controllo degli accessi in base al ruolo di Azure, è possibile usare local Kubernetes RBAC.  **Disponibilità in sospeso della modalità di connessione diretta**|
|**Azure Active Directory (AD)**|Ambiente del cliente: ambiente del cliente > Azure >|Facoltativo|Forse, ma è possibile che si stia già pagando Azure AD|Solo diretta|Se si vuole usare Azure AD per l'autenticazione, è necessario stabilire la connettività con Azure in qualsiasi momento. Se non si vuole usare Azure AD per l'autenticazione, è possibile Active Directory Federation Services (ADFS) per Active Directory. **Disponibilità in sospeso della modalità di connessione diretta**|
|**Backup e ripristino**|Ambiente del cliente-> ambiente del cliente|Obbligatoria|No|Diretta o indiretta|Il servizio di backup e ripristino può essere configurato in modo da puntare alle classi di archiviazione locali. |
|**Backup di Azure: conservazione a lungo termine**| Ambiente del cliente-> Azure | Facoltativo| Sì per archiviazione di Azure | Solo diretta |È possibile inviare i backup che vengono eseguiti localmente in backup di Azure per la conservazione a lungo termine e fuori sede dei backup e riportarli nell'ambiente locale per il ripristino. **Disponibilità in sospeso della modalità di connessione diretta**|
|**Servizi di sicurezza di Azure Defender**|Ambiente del cliente: ambiente del cliente > Azure >|Facoltativo|Sì|Solo diretta|**Disponibilità in sospeso della modalità di connessione diretta**|
|**Provisioning e modifiche di configurazione da portale di Azure**|Ambiente del cliente: ambiente del cliente > Azure >|Facoltativo|No|Solo diretta|È possibile eseguire il provisioning e le modifiche alla configurazione localmente utilizzando Azure Data Studio o [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] .  In modalità con connessione diretta sarà anche possibile effettuare il provisioning e apportare modifiche alla configurazione dalla portale di Azure. **Disponibilità in sospeso della modalità di connessione diretta**|


## <a name="details-on-internet-addresses-ports-encryption-and-proxy-server-support"></a>Informazioni dettagliate su indirizzi Internet, porte, crittografia e supporto dei server proxy

Attualmente, nella fase di anteprima è supportata solo la modalità indirettamente connessa. In questa modalità sono necessarie solo tre connessioni per i servizi disponibili su Internet. Le connessioni includono:

- [Microsoft Container Registry](#microsoft-container-registry-mcr)
- [API Azure Resource Manager](#azure-resource-manager-apis)
- [API di monitoraggio di Azure](#azure-monitor-apis)

Tutte le connessioni HTTPS ad Azure e a Microsoft Container Registry vengono crittografate con SSL/TLS utilizzando certificati ufficialmente firmati e verificabili.

Nelle sezioni seguenti vengono forniti i dettagli di queste connessioni. 

### <a name="microsoft-container-registry-mcr"></a>Microsoft Container Registry

Il Container Registry Microsoft ospita le immagini del contenitore di servizi dati abilitati per Azure Arc.  È possibile effettuare il pull di queste immagini da e inviarle a un registro contenitori privato e configurare il processo di distribuzione del controller dei dati per estrarre le immagini del contenitore dal registro contenitori privato.

#### <a name="connection-source"></a>Origine della connessione

Kubernetes kubelet in ognuno dei nodi Kubernetes che effettua il pull delle immagini del contenitore.

#### <a name="connection-target"></a>Destinazione della connessione

`mcr.microsoft.com`

#### <a name="protocol"></a>Protocollo

HTTPS

#### <a name="port"></a>Porta

443

#### <a name="can-use-proxy"></a>Può usare il proxy

Sì

#### <a name="authentication"></a>Authentication

nessuno

### <a name="azure-resource-manager-apis"></a>API Azure Resource Manager
Azure Data Studio [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] e l'interfaccia della riga di comando di Azure si connettono alle api Azure Resource Manager per inviare e recuperare dati da e verso Azure per alcune funzionalità.

#### <a name="connection-source"></a>Origine della connessione

Un computer che esegue Azure Data Studio, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] o l'interfaccia della riga di comando di Azure che si connette ad Azure.

#### <a name="connection-target"></a>Destinazione della connessione

- `login.microsoftonline.com`
- `management.azure.com`
- `san-af-eastus-prod.azurewebsites.net`
- `san-af-eastus2-prod.azurewebsites.net`
- `san-af-australiaeast-prod.azurewebsites.net`
- `san-af-centralus-prod.azurewebsites.net`
- `san-af-westus2-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-southeastasia-prod.azurewebsites.net`
- `san-af-koreacentral-prod.azurewebsites.net`
- `san-af-northeurope-prod.azurewebsites.net`
- `san-af-westeurope-prod.azurewebsites.net`
- `san-af-uksouth-prod.azurewebsites.net`
- `san-af-francecentral-prod.azurewebsites.net`

#### <a name="protocol"></a>Protocollo

HTTPS

#### <a name="port"></a>Porta

443

#### <a name="can-use-proxy"></a>Può usare il proxy

Sì

#### <a name="authentication"></a>Authentication 

Azure Active Directory

### <a name="azure-monitor-apis"></a>API di monitoraggio di Azure

Azure Data Studio [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] e l'interfaccia della riga di comando di Azure si connettono alle api Azure Resource Manager per inviare e recuperare dati da e verso Azure per alcune funzionalità.

#### <a name="connection-source"></a>Origine della connessione

Un computer [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] che esegue o l'interfaccia della riga di comando di Azure che carica le metriche di monitoraggio o i log in monitoraggio di Azure.

#### <a name="connection-target"></a>Destinazione della connessione

- `login.microsoftonline.com`
- `management.azure.com`
- `*.ods.opinsights.azure.com`
- `*.oms.opinsights.azure.com`
- `*.monitoring.azure.com`

#### <a name="protocol"></a>Protocollo

HTTPS

#### <a name="port"></a>Porta

443

#### <a name="can-use-proxy"></a>Può usare il proxy

Sì

#### <a name="authentication"></a>Authentication 

Azure Active Directory

> [!NOTE]
> Per il momento, tutte le connessioni HTTPS/443 del browser ai dashboard Grafana e Kibana e da [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] all'API del controller dati vengono crittografate con SSL con certificati autofirmati.  In futuro sarà disponibile una funzionalità che consente di fornire i propri certificati per la crittografia delle connessioni SSL.

La connettività da Azure Data Studio e [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] al server API Kubernetes usa l'autenticazione Kubernetes e la crittografia stabilita.  Ogni utente che usa Azure Data Studio e [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] deve avere una connessione autenticata all'API Kubernetes per eseguire molte delle azioni correlate ai servizi dati abilitati per Azure Arc.

