---
title: Protezione dalle minacce nel Centro sicurezza di Azure
description: Questo argomento descrive le risorse protette dalle funzionalità di protezione dalle minacce del Centro sicurezza di Azure
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: e74dac779fc1eafaf33ffbc63bf997cf26b64954
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836803"
---
# <a name="threat-protection-in-azure-security-center"></a>Protezione dalle minacce nel Centro sicurezza di Azure

Quando il Centro sicurezza rileva una minaccia in qualsiasi area dell'ambiente, viene generato un avviso. Questi avvisi descrivono i dettagli sulle risorse interessate, le procedure di correzione consigliate e, in alcuni casi, un'opzione per attivare un'app per la logica in risposta.

La protezione dalle minacce del Centro sicurezza di Azure offre difese complete per l'ambiente:

* **Protezione dalle minacce per le risorse di calcolo di Azure**: computer Windows, computer Linux, servizio app di Azure e contenitori di Azure

* **Protezione dalle minacce per le risorse dati di Azure**: database SQL e SQL Data Warehouse, Archiviazione di Azure e Azure Cosmos DB

* **Protezione delle minacce per livelli di servizi di Azure**: livello di rete di Azure, livello di gestione di Azure (Azure Resource Manager) (anteprima) e Azure Key Vault (anteprima)

Che un avviso venga generato dal Centro sicurezza o ricevuto dal Centro sicurezza da un prodotto di sicurezza diverso, è possibile esportarlo. Per esportare gli avvisi in Azure Sentinel, in qualsiasi soluzione SIEM di terze parti o in qualsiasi altro strumento esterno, seguire le istruzioni riportate in [Esportazione di avvisi in un sistema SIEM](continuous-export.md). 

> [!NOTE]
> Gli avvisi provenienti da origini diverse potrebbero comparire in tempi diversi. Ad esempio, la visualizzazione degli avvisi che richiedono l'analisi del traffico di rete potrebbe richiedere più tempo rispetto agli avvisi correlati a processi sospetti in esecuzione sulle macchine virtuali.

> [!TIP]
> Per abilitare le funzionalità di protezione dalle minacce del Centro sicurezza, è necessario applicare il piano tariffario standard alla sottoscrizione contenente i carichi di lavoro applicabili.
>
> È possibile abilitare la protezione dalle minacce per gli **account di archiviazione di Azure** a livello di sottoscrizione o di risorsa.
> È possibile abilitare la protezione dalle minacce per i **server SQL del database SQL di Azure** a livello di sottoscrizione o di risorsa.
> È possibile abilitare la protezione dalle minacce per **Database di Azure per MariaDB/MySQL/PostgreSQL** solo a livello di risorsa.



## <a name="threat-protection-for-windows-machines"></a>Protezione dalle minacce per i computer Windows <a name="windows-machines"></a>

Il Centro sicurezza di Azure si integra con i servizi di Azure per monitorare e proteggere i computer basati su Windows. Il Centro sicurezza presenta gli avvisi e i suggerimenti per la correzione provenienti da tutti questi servizi in un formato di facile utilizzo.

* **Microsoft Defender Advanced Threat Protection (ATP)** <a name="windows-atp"></a> - Il Centro sicurezza estende le proprie piattaforme di protezione del carico di lavoro cloud grazie all'integrazione con Microsoft Defender Advanced Threat Protection (ATP). Insieme, le due soluzioni offrono funzionalità di rilevamento e reazione dagli endpoint (EDR) complete.

    > [!IMPORTANT]
    > Il sensore di Microsoft Defender ATP viene abilitato automaticamente nei server Windows che usano il Centro sicurezza.

    Quando Microsoft Defender ATP rileva una minaccia, attiva un avviso. L'avviso viene visualizzato nel dashboard del Centro sicurezza. Dal dashboard è possibile passare alla console di Microsoft Defender ATP ed eseguire un'indagine dettagliata per individuare l'ambito dell'attacco. Per altre informazioni su Microsoft Defender ATP, vedere [Onboard servers to the Microsoft Defender ATP service](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints) (Onboarding dei server nel servizio Microsoft Defender ATP).

* Rilevamento di attacchi non **file** <a name="windows-fileless"></a> -Gli attacchi senza file inseriscono payload dannosi nella memoria per evitare il rilevamento da parte di tecniche di analisi basate su disco. Il payload del pirata informatico viene quindi mantenuto nella memoria dei processi compromessi ed esegue un'ampia gamma di attività dannose.

    Con il rilevamento degli attacchi senza file, tecnologie automatizzate di analisi forense della memoria identificano i toolkit, le tecniche e i comportamenti degli attacchi senza file. Questa soluzione analizza periodicamente il computer in fase di esecuzione ed estrae informazioni dettagliate direttamente dalla memoria dei processi. Informazioni dettagliate specifiche per Linux includono l'identificazione di: 

    - Toolkit ben noti e software di data mining di crittografia 
    - Shellcode, ovvero un piccolo frammento di codice usato in genere come payload nell'exploit di una vulnerabilità software.
    - Eseguibile dannoso inserito nella memoria del processo

    Il rilevamento di attacchi senza file genera avvisi di sicurezza dettagliati contenenti le descrizioni con metadati aggiuntivi del processo, ad esempio l'attività di rete. Questa operazione accelera la valutazione degli avvisi, la correlazione e il tempo di risposta downstream. Questo approccio è complementare alle soluzioni EDR basate su eventi e offre una maggiore copertura del rilevamento.

    Per informazioni dettagliate sugli avvisi di rilevamento degli attacchi senza file, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-windows).

> [!TIP]
> È possibile simulare gli avvisi di Windows scaricando il documento [Azure Security Center Playbook: Security Alerts](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046) (Playbook del Centro sicurezza di Azure: Avvisi di sicurezza).






## <a name="threat-protection-for-linux-machines"></a>Protezione dalle minacce per i computer Linux <a name="linux-machines"></a>

Il Centro sicurezza raccoglie record di controllo dai computer Linux usando **auditd**, uno dei framework di controllo di Linux più diffusi. Auditd risiede nel kernel mainline. 

* **Integrazione degli avvisi auditd Linux e dell'agente di Log Analytics**  <a name="linux-auditd"></a>- Il sistema auditd è un sottosistema a livello di kernel, responsabile del monitoraggio delle chiamate di sistema. Filtra le chiamate in base a un set di regole specificato e scrive messaggi per le chiamate in un socket. Il Centro sicurezza integra le funzionalità del pacchetto auditd nell'agente di Log Analytics. Questa integrazione consente la raccolta degli eventi auditd in tutte le distribuzioni Linux supportate, senza alcun prerequisito.

    I record auditd vengono raccolti, arricchiti e aggregati in eventi usando l'agente di Log Analytics per l'agente Linux. Il Centro sicurezza aggiunge continuamente nuove analisi, che usano i segnali di Linux per rilevare comportamenti dannosi in computer Linux locali e nel cloud. In modo simile alle funzionalità di Windows, queste analisi abbracciano processi sospetti, tentativi di accesso ambigui, caricamento del modulo kernel e altre attività. Queste attività possono indicare che un computer è sotto attacco o è stato violato.  

    Per un elenco degli avvisi di Linux, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-linux).

> [!TIP]
> È possibile simulare gli avvisi di Linux scaricando il documento [Azure Security Center Playbook: Linux Detections](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef) (Playbook del Centro sicurezza di Azure: rilevamenti Linux).





## <a name="threat-protection-for-azure-app-service"></a>Protezione dalle minacce per il servizio app di Azure <a name="app-services"></a>

> [!NOTE]
> Questo servizio non è attualmente disponibile nelle aree di cloud sovrano e Azure per enti pubblici.

Il Centro sicurezza sfrutta la scala del cloud per identificare gli attacchi alle applicazioni in esecuzione sul servizio app. Gli utenti malintenzionati mettono alla prova le applicazioni Web per individuare e sfruttare i punti deboli. Prima di essere instradate verso ambienti specifici, le richieste alle applicazioni in esecuzione in Azure passano attraverso diversi gateway, in cui vengono ispezionate e registrate. Questi dati vengono poi usati per identificare exploit e utenti malintenzionati, oltre che per apprendere nuovi modelli che verranno usati successivamente.

Grazie alla visibilità di cui Azure dispone come provider di servizi cloud, il Centro sicurezza analizza i log interni del servizio app per identificare la metodologia di attacco usata in più obiettivi. La metodologia comprende, ad esempio, analisi su larga scala e attacchi distribuiti. Gli attacchi di questo tipo in genere provengono da un piccolo subset di indirizzi IP e mostrano modelli di ricerca per indicizzazione in endpoint simili su più host. Questi attacchi cercano una pagina o un plug-in vulnerabile e non possono essere identificati dal punto di vista di un singolo host.

Se si sta eseguendo un piano di servizio app basato su Windows, il Centro sicurezza ha anche accesso alle sandbox e alle macchine virtuali sottostanti. Insieme ai dati di log indicati in precedenza, l'infrastruttura può fornire indicazioni su ciò che accade, da un nuovo attacco in circolazione alle compromissioni nei computer dei clienti. Pertanto, anche se il Centro sicurezza viene distribuito dopo che le vulnerabilità di un'app Web sono state già sfruttate, può essere in grado di rilevare gli attacchi in atto.

Per un elenco degli avvisi del servizio app di Azure, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azureappserv).

Per altre informazioni sui piani di servizio app, vedere [Piani del servizio app](https://azure.microsoft.com/pricing/details/app-service/plans/).





## <a name="threat-protection-for-containers"></a>Protezione dalle minacce per i contenitori<a name="azure-containers"></a>

### <a name="availability"></a>Disponibilità

- Stato versione: **disponibilità generale**
- Ruoli necessari: l' **amministratore della sicurezza** può ignorare gli avvisi. Il **ruolo con autorizzazioni di lettura per la sicurezza** può visualizzare i risultati.
- Cloud:<br>
    ✔ Cloud commerciali<br>
    US Gov ✘<br>
    ✘ Cina, altro gov

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>Protezione dalle minacce per il database SQL e SQL Data Warehouse <a name="data-sql"></a>

Advanced Threat Protection per il database SQL di Azure rileva le attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

Si vedranno avvisi su attività di database sospette, potenziali vulnerabilità o attacchi SQL injection, oltre che anomalie negli accessi al database e nei modelli di query.

Advanced Threat Protection per il database SQL di Azure e SQL fa parte del pacchetto unificato Advanced [Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) per funzionalità di sicurezza avanzate di SQL, che include il database SQL di Azure, le istanze gestite di SQL di Azure, i database Azure SQL data warehouse e i server SQL in macchine virtuali di Azure.

Per altre informazioni, vedere:

* [Come abilitare Advanced Threat Protection per il database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Come abilitare Advanced Threat Protection per SQL Server in Macchine virtuali di Azure](security-center-iaas-advanced-data.md)
* [L'elenco di avvisi di protezione dalle minacce per il database SQL e SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Protezione dalle minacce per Archiviazione di Azure <a name="azure-storage"></a>

### <a name="availability"></a>Disponibilità

- Stato versione:
    - [Archiviazione BLOB](https://azure.microsoft.com/services/storage/blobs/) (disponibilità generale)
    - [File di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) (anteprima)
    - [Azure Data Lake storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (anteprima)
- Cloud:<br>
    ✔ Cloud commerciali<br>
    ✔ US Gov<br>
    ✘ Cina, altro gov

### <a name="whats-protected"></a>Cosa è protetto?

La protezione dalle minacce per archiviazione di Azure rileva attività potenzialmente dannose negli account di archiviazione di Azure. I dati possono essere protetti indipendentemente dal fatto che vengano archiviati come contenitori BLOB, condivisioni file o data Lake.

Questo livello di protezione consente di risolvere le minacce *senza* che sia necessario essere un esperto di sicurezza e consente di gestire i sistemi di monitoraggio della sicurezza.

Gli account di archiviazione sono protetti 

### <a name="what-kind-of-alerts-does-threat-protection-for-azure-storage-provide"></a>Che tipo di avvisi offre la protezione dalle minacce per archiviazione di Azure?

Gli avvisi di sicurezza vengono attivati quando sono presenti:

- **Attività sospetta** . ad esempio, l'accesso all'account di archiviazione è stato eseguito correttamente da un indirizzo IP noto come nodo di uscita attivo di Tor
- **Comportamento anomalo** , ad esempio modifiche nel modello di accesso a un account di archiviazione
- **Potenziale malware caricato** : l'analisi della reputazione hash indica che un file caricato contiene malware

Gli avvisi includono i dettagli dell'evento imprevisto che li ha attivati, nonché indicazioni su come individuare e correggere le minacce.

### <a name="what-is-hash-reputation-analysis-for-malware"></a>Che cos'è l'analisi della reputazione hash per malware?

Per determinare se un file caricato è sospetto, la protezione dalle minacce per archiviazione di Azure usa l'analisi della reputazione hash supportata da [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684). Gli strumenti di protezione dalle minacce non analizzano i file caricati, bensì esaminano i log di archiviazione e confrontano gli hash dei file appena caricati con quelli di virus, Trojan, spyware e ransomware noti. 

Quando si sospetta che un file contenga malware, il Centro sicurezza Visualizza un avviso e può facoltativamente inviare tramite posta elettronica al proprietario dell'archiviazione l'approvazione per eliminare il file sospetto. Per configurare questa rimozione automatica dei file che l'analisi della reputazione hash indica che contengono malware, distribuire un' [automazione del flusso di lavoro per attivare gli avvisi che contengono "malware potenziale caricato in un account di archiviazione"](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).



### <a name="next-steps"></a>Passaggi successivi 

Per informazioni dettagliate sui prezzi, inclusa una versione di valutazione gratuita di 30 giorni, vedere la [pagina dei prezzi del Centro sicurezza di Azure](https://azure.microsoft.com/pricing/details/security-center/).

Per altre informazioni, vedere:

* [Come abilitare Advanced Threat Protection per Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [L'elenco di avvisi di protezione dalle minacce per Archiviazione di Azure](alerts-reference.md#alerts-azurestorage)
* [Funzionalità di intelligence per le minacce di Microsoft](https://go.microsoft.com/fwlink/?linkid=2128684)

> [!TIP]
> È possibile simulare gli avvisi di archiviazione seguendo le istruzioni riportate in [questo post di Blog](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).







## <a name="threat-protection-for-azure-cosmos-db"></a>Protezione dalle minacce per Azure Cosmos DB <a name="cosmos-db"></a>

Gli avvisi di Azure Cosmos DB vengono generati da tentativi insoliti e potenzialmente dannosi di accesso o exploit degli account Azure Cosmos DB.

Per altre informazioni, vedere:

* [Advanced Threat Protection per Azure Cosmos DB (anteprima)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [L'elenco di avvisi di protezione dalle minacce per Azure Cosmos DB (anteprima)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Protezione dalle minacce per il livello di rete di Azure <a name="network-layer"></a>

L'analisi a livello di rete del Centro sicurezza si basa su [dati IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), di esempio, intestazioni di pacchetti raccolte dai router core di Azure. In base a questo feed di dati, il Centro sicurezza usa modelli di Machine Learning per identificare e contrassegnare le attività di traffico dannose. Il Centro sicurezza usa anche il database di intelligence sulle minacce di Microsoft per arricchire gli indirizzi IP.

Alcune configurazioni di rete potrebbero impedire al Centro sicurezza di generare avvisi sulle attività di rete sospette. Per consentire al Centro sicurezza di generare avvisi di rete, verificare che:

- La macchina virtuale abbia un indirizzo IP pubblico o si trovi in un servizio di bilanciamento del carico con un indirizzo IP pubblico.

- Il traffico di rete in uscita della macchina virtuale non sia bloccato da una soluzione IDS esterna.

- Alla macchina virtuale fosse assegnato lo stesso indirizzo IP per l'intera ora in cui si sono verificate le comunicazioni sospette. Questo vale anche per le macchine virtuali create come parte di un servizio gestito, ad esempio servizio Azure Kubernetes o Databricks.

Per un elenco degli avvisi a livello di rete di Azure, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azurenetlayer).

Per informazioni dettagliate sul modo in cui il Centro sicurezza può usare segnali correlati alla rete per applicare la protezione dalle minacce, vedere [Heuristic DNS detections in Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/) (Rilevamenti di DNS euristici nel Centro sicurezza).



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Protezione dalle minacce per il livello di gestione di Azure (Azure Resource Manager) (anteprima)<a name ="management-layer"></a>

Il livello di protezione del Centro sicurezza basato su Azure Resource Manager è attualmente in fase di anteprima.

Il Centro sicurezza offre un ulteriore livello di protezione usando gli eventi di Azure Resource Manager, che è considerato il piano di controllo per Azure. Analizzando i record di Azure Resource Manager, il Centro sicurezza rileva le operazioni insolite o potenzialmente dannose nell'ambiente della sottoscrizione di Azure.

Per un elenco degli avvisi di Azure Resource Manager (anteprima), vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azureresourceman).



>[!NOTE]
> Alcune delle analisi precedenti sono basate su Microsoft Cloud App Security. Per usufruire di queste analisi è necessario attivare una licenza di Cloud App Security. Se si ha una licenza di Cloud App Security, questi avvisi sono abilitati per impostazione predefinita. Per disabilitare gli avvisi:
>
> 1. Dal menu del Centro sicurezza selezionare **prezzi & impostazioni**.
> 1. Selezionare la sottoscrizione da modificare.
> 1. Selezionare **Rilevamento delle minacce**.
> 1. Deselezionare **consenti Microsoft cloud app Security per accedere ai dati**e selezionare **Salva**.

>[!NOTE]
>Il Centro sicurezza archivia i dati dei clienti correlati alla sicurezza nella stessa area geografica della risorsa. Se Microsoft non ha ancora distribuito il Centro sicurezza nell'area geografica della risorsa, archivia i dati nel Stati Uniti. Quando è abilitato Cloud App Security, queste informazioni vengono archiviate in base alle regole relative alla posizione geografica di Cloud App Security. Per altre informazioni, vedere [Data storage for non-regional services](https://azuredatacentermap.azurewebsites.net/) (Archiviazione dei dati per i servizi non a livello di area).

1. Impostare l'area di lavoro in cui si sta installando l'agente. Assicurarsi che l'area di lavoro si trovi nella stessa sottoscrizione che si usa nel Centro sicurezza e di disporre delle autorizzazioni di lettura/scrittura nell'area di lavoro.

1. Impostare il piano tariffario standard e selezionare **Salva**.






## <a name="threat-protection-for-azure-key-vault-preview"></a>Protezione dalle minacce per Azure Key Vault (anteprima)<a name="azure-keyvault"></a>

> [!NOTE]
> Questo servizio non è attualmente disponibile nelle aree di cloud sovrano e Azure per enti pubblici.

Azure Key Vault è un servizio cloud che protegge le chiavi di crittografia e i segreti, come certificati, stringhe di connessione e password. 

Il Centro sicurezza di Azure include la protezione avanzata dalle minacce nativa di Azure per Azure Key Vault, offrendo un ulteriore livello di intelligence sulla sicurezza. Il Centro sicurezza rileva tentativi insoliti e potenzialmente dannosi di accesso o exploit degli account Key Vault. Questo livello di protezione consente di affrontare le minacce senza dover essere esperti di sicurezza e senza bisogno di gestire sistemi di monitoraggio della sicurezza di terze parti.  

Quando si verificano attività anomale, il Centro sicurezza mostra avvisi e, facoltativamente, li invia tramite posta elettronica agli amministratori della sottoscrizione. Questi avvisi includono i dettagli delle attività sospette e raccomandazioni su come analizzare e risolvere le minacce. 

Per un elenco degli avvisi di Azure Key Vault, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azurekv).





## <a name="threat-protection-for-other-microsoft-services"></a>Protezione dalle minacce per altri servizi Microsoft <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Protezione dalle minacce per il WAF di Azure <a name="azure-waf"></a>

Il gateway applicazione di Azure offre un web application firewall (WAF) che garantisce alle applicazioni Web una protezione centralizzata da exploit e vulnerabilità comuni.

Le applicazioni Web sono sempre più vittime di attacchi che sfruttano le più comuni e note vulnerabilità. Il WAF del gateway applicazione basa sullo standard CRS (Core Rule Set) 3.0 o 2.2.9 dell'OWASP (Open Web Application Security Project). Il WAF viene aggiornato automaticamente per offrire protezione dalle nuove vulnerabilità. 

Se si ha una licenza per il WAF di Azure, gli avvisi WAF vengono trasmessi al Centro sicurezza senza che sia necessaria alcuna configurazione aggiuntiva. Per altre informazioni sugli avvisi generati da WAF, vedere [Regole e gruppi di regole CRS di Web Application Firewall](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Protezione dalle minacce per Protezione DDoS di Azure <a name="azure-ddos"></a>

Gli attacchi Distributed Denial of Service (DDoS) sono notoriamente facili da eseguire. Sono diventati un grosso problema di sicurezza, soprattutto se si stanno trasferendo le applicazioni nel cloud. 

Un attacco DDoS tenta di esaurire le risorse di un'applicazione, che quindi non risulta più disponibile per gli utenti legittimi. Gli attacchi DDoS possono avere come obiettivo qualsiasi endpoint raggiungibile tramite Internet.

Per difendersi dagli attacchi DDoS, acquistare una licenza per Protezione DDoS di Azure e attenersi alle procedure consigliate per la progettazione delle applicazioni. Protezione DDoS offre diversi livelli di servizio. Per altre informazioni, vedere [Panoramica di Protezione DDoS di Azure Standard](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Per un elenco degli avvisi di Protezione DDoS di Azure, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sugli avvisi di sicurezza generati da queste funzionalità di protezione dalle minacce, vedere gli articoli seguenti:

* [Tabella di riferimento per tutti gli avvisi del Centro sicurezza di Azure](alerts-reference.md)
* [Avvisi di sicurezza nel Centro sicurezza di Azure](security-center-alerts-overview.md)
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md)
* [Esportare avvisi e raccomandazioni sulla sicurezza (anteprima)](continuous-export.md)