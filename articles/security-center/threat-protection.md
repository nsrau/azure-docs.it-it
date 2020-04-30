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
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 79638f584f1c65b33f23a68f01dbe82878460cc2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82234078"
---
# <a name="threat-protection-in-azure-security-center"></a>Protezione dalle minacce nel Centro sicurezza di Azure

Quando il Centro sicurezza rileva una minaccia in qualsiasi area dell'ambiente, viene generato un avviso. Questi avvisi descrivono i dettagli delle risorse interessate, le procedure consigliate per la correzione e, in alcuni casi, un'opzione per attivare un'app per la logica in risposta.

La protezione dalle minacce del Centro sicurezza di Azure offre difese complete per l'ambiente:

* **Protezione dalle minacce per le risorse di calcolo di Azure**: computer Windows, computer Linux, servizio app Azure e contenitori di Azure

* **Protezione dalle minacce per le risorse di dati di Azure**: database SQL e SQL data warehouse, archiviazione di azure e Azure Cosmos DB

* **Protezione dalle minacce per i livelli dei servizi di**Azure: livello di rete di Azure, livello di gestione di azure (Azure Resource Manager) (anteprima) ed Azure Key Vault (anteprima)

Se un avviso viene generato dal centro sicurezza o ricevuto dal centro sicurezza da un prodotto di sicurezza diverso, è possibile esportarlo. Per esportare gli avvisi in Sentinel di Azure (o in SIEM di terze parti) o in qualsiasi altro strumento esterno, seguire le istruzioni in [esportazione degli avvisi in un Siem](continuous-export.md). 

> [!TIP]
> Per abilitare le funzionalità di protezione dalle minacce del Centro sicurezza, è necessario applicare il piano tariffario standard alla sottoscrizione contenente i carichi di lavoro applicabili.
>
> La protezione dalle minacce del Centro sicurezza per gli account di archiviazione e i server PaaS SQL/MySQL/PG sono attualmente gli unici carichi di lavoro che possono essere abilitati a livello di risorsa per proteggere i singoli carichi di lavoro di archiviazione/SQL.



## <a name="threat-protection-for-windows-machines"></a>Protezione dalle minacce per i computer Windows<a name="windows-machines"></a>

Il Centro sicurezza di Azure si integra con i servizi di Azure per monitorare e proteggere i computer basati su Windows. Il Centro sicurezza presenta gli avvisi e i suggerimenti per la correzione di tutti questi servizi in un formato di facile utilizzo.

* **Microsoft Defender ATP** <a name="windows-atp"></a> : il Centro sicurezza estende le proprie piattaforme di protezione del carico di lavoro cloud grazie all'integrazione con Microsoft Defender Advanced Threat Protection (ATP). Insieme, offrono funzionalità complete di rilevamento e risposta dell'endpoint (EDR).

    > [!IMPORTANT]
    > Il sensore Microsoft Defender ATP viene abilitato automaticamente nei server Windows che usano il Centro sicurezza.

    Quando Microsoft Defender ATP rileva una minaccia, viene attivato un avviso. L'avviso viene visualizzato nel dashboard del Centro sicurezza. Dal dashboard è possibile passare alla console di Microsoft Defender ATP ed eseguire un'indagine dettagliata per individuare l'ambito dell'attacco. Per ulteriori informazioni su Microsoft Defender ATP, vedere [onboarding servers to the Microsoft Defender ATP Service](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* **Analisi** <a name="windows-dump"></a> dei dump di arresto anomalo del sistema: in caso di arresto anomalo del software, un dump di arresto anomalo acquisisce una parte della memoria al momento dell'arresto

    Un arresto anomalo potrebbe essere stato causato da malware o contenere malware. Per evitare di essere rilevati dai prodotti per la sicurezza, diverse forme di malware utilizzano un attacco senza file, che evita la scrittura su disco o la crittografia dei componenti software scritti su disco. Questo tipo di attacco è difficile da rilevare usando approcci tradizionali basati su disco.

    Tuttavia, tramite l'analisi della memoria, è possibile rilevare questo tipo di attacco. Analizzando la memoria nel dump di arresto anomalo del sistema, il Centro sicurezza può rilevare le tecniche usate dagli attacchi. Ad esempio, l'attacco potrebbe tentare di sfruttare le vulnerabilità del software, accedere ai dati riservati e mantenersi nascosto in un computer compromesso. Il Centro sicurezza funziona con un effetto minimo sulle prestazioni degli host.

    Per informazioni dettagliate sugli avvisi di analisi dei dump di arresto anomalo del sistema, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-windows).

* **Rilevamento** <a name="windows-fileless"></a> di attacchi non file: gli attacchi di file che hanno come destinazione gli endpoint sono comuni. Per evitare il rilevamento, gli attacchi con file inseriscono payload dannosi nella memoria. I payload degli utenti malintenzionati vengono mantenuti nella memoria dei processi compromessi ed eseguono un'ampia gamma di attività dannose.

    Con il rilevamento di attacchi senza file, le tecniche di analisi della memoria automatizzata identificano i toolkit, le tecniche e i comportamenti di attacco senza file. Questa soluzione analizza periodicamente il computer in fase di esecuzione ed estrae informazioni dettagliate direttamente dalla memoria dei processi critici per la sicurezza.

    Rileva l'evidenza di exploit, inserimento di codice ed esecuzione di payload dannosi. Il rilevamento di attacchi non file genera avvisi di sicurezza dettagliati per accelerare la valutazione degli avvisi, la correlazione e il tempo di risposta downstream. Questo approccio integra le soluzioni EDR basate su eventi, garantendo una maggiore copertura del rilevamento.

    Per informazioni dettagliate sugli avvisi di rilevamento degli attacchi senza file, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-windows).

> [!TIP]
> È possibile simulare gli avvisi di Windows scaricando il [PlayBook del Centro sicurezza di Azure: avvisi di sicurezza](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).






## <a name="threat-protection-for-linux-machines"></a>Protezione dalle minacce per i computer Linux<a name="linux-machines"></a>

Il Centro sicurezza raccoglie i record di controllo dai computer Linux usando **auditd**, uno dei framework di controllo di Linux più comuni. il controllo risiede nel kernel principale. 

* **Avvisi controllati da Linux e integrazione** <a name="linux-auditd"></a> log Analytics agente: il sistema controllato è costituito da un sottosistema a livello di kernel, responsabile del monitoraggio delle chiamate di sistema. I filtri vengono filtrati in base a un set di regole specificato e i messaggi vengono scritti in un socket. Il Centro sicurezza integra le funzionalità del pacchetto controllato all'interno dell'agente di Log Analytics. Questa integrazione consente la raccolta di eventi controllati in tutte le distribuzioni di Linux supportate, senza prerequisiti.

    i record controllati vengono raccolti, arricchiti e aggregati in eventi usando l'agente Log Analytics per l'agente Linux. Il Centro sicurezza aggiunge continuamente nuove analisi che usano i segnali di Linux per rilevare comportamenti dannosi nei computer Linux locali e nel cloud. Analogamente alle funzionalità di Windows, queste analisi si estendono su processi sospetti, tentativi di accesso dubbi, caricamento del modulo kernel e altre attività. Queste attività possono indicare che un computer è sotto attacco o è stato violato.  

    Per un elenco degli avvisi di Linux, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-linux).

> [!TIP]
> È possibile simulare gli avvisi Linux scaricando il [PlayBook del Centro sicurezza di Azure: rilevamento di Linux](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).





## <a name="threat-protection-for-azure-app-service"></a>Protezione dalle minacce per il servizio app Azure<a name="app-services"></a>

> [!NOTE]
> Questo servizio non è attualmente disponibile in Azure per enti pubblici e aree cloud sovrane.

Il Centro sicurezza usa la scalabilità del cloud per identificare gli attacchi destinati alle applicazioni in esecuzione sul servizio app. Gli utenti malintenzionati Probe applicazioni Web per individuare e sfruttare i punti deboli. Prima di essere indirizzati ad ambienti specifici, le richieste alle applicazioni in esecuzione in Azure passano attraverso diversi gateway, dove vengono ispezionate e registrate. Questi dati vengono quindi usati per identificare gli exploit e gli utenti malintenzionati e per apprendere nuovi modelli che verranno usati in un secondo momento.

Grazie alla visibilità di Azure come provider di servizi cloud, il Centro sicurezza analizza i log interni del servizio app per identificare la metodologia di attacco in più destinazioni. La metodologia, ad esempio, include analisi generalizzate e attacchi distribuiti. Questo tipo di attacco in genere deriva da un piccolo subset di indirizzi IP e Mostra i modelli di ricerca per indicizzazione in endpoint simili su più host. Gli attacchi stanno cercando una pagina o un plug-in vulnerabile e non possono essere identificati dal punto di vista di un singolo host.

Se si sta eseguendo un piano di servizio App basato su Windows, il Centro sicurezza ha anche accesso alle macchine virtuali e alle VM sottostanti. Insieme ai dati di log indicati in precedenza, l'infrastruttura può raccontare la storia, da un nuovo attacco che circola in modo naturale a compromessi nei computer dei clienti. Pertanto, anche se il Centro sicurezza viene distribuito dopo che un'app Web è stata sfruttata, potrebbe essere in grado di rilevare attacchi continui.

Per un elenco degli avvisi del servizio app Azure, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azureappserv).

Per altre informazioni sui piani di servizio app, vedere [piani di servizio app](https://azure.microsoft.com/pricing/details/app-service/plans/).





## <a name="threat-protection-for-azure-containers"></a>Protezione dalle minacce per i contenitori di Azure<a name="azure-containers"></a>

> [!NOTE]
> Questo servizio non è attualmente disponibile in Azure per enti pubblici e aree cloud sovrane.

Il Centro sicurezza offre protezione dalle minacce in tempo reale per gli ambienti in contenitori e genera avvisi per le attività sospette. È possibile usare queste informazioni per risolvere rapidamente i problemi di sicurezza e migliorare la sicurezza dei contenitori.

Il Centro sicurezza offre protezione dalle minacce a diversi livelli: 

* **Livello host** : l'agente del Centro sicurezza (disponibile nel livello standard, vedere [prezzi](security-center-pricing.md) per i dettagli) monitora Linux per le attività sospette. L'agente attiva avvisi per le attività sospette provenienti dal nodo o da un contenitore in esecuzione su di esso. Esempi di attività di questo tipo includono rilevamento della shell Web e connessione con indirizzi IP sospetti noti.

    Per informazioni più dettagliate sulla sicurezza dell'ambiente in contenitori, l'agente monitora le analisi specifiche del contenitore. Attiverà gli avvisi per eventi quali la creazione di contenitori con privilegi, l'accesso sospetto ai server API e i server Secure Shell (SSH) in esecuzione all'interno di un contenitore docker.

    >[!IMPORTANT]
    > Se si sceglie di non installare gli agenti negli host, si riceverà solo un subset dei vantaggi della protezione dalle minacce e degli avvisi di sicurezza. Verranno comunque ricevuti gli avvisi relativi all'analisi di rete e alle comunicazioni con server dannosi.

    Per un elenco degli avvisi a livello di host, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-containerhost).


* A **livello di cluster AKS**, la protezione dalle minacce si basa sull'analisi dei log di controllo di Kubernetes. Per abilitare questo monitoraggio senza **agente** , aggiungere l'opzione Kubernetes alla sottoscrizione dalla pagina **Impostazioni & prezzi** (vedere i [prezzi](security-center-pricing.md)). Per generare avvisi a questo livello, il Centro sicurezza monitora i servizi gestiti da AKS usando i log recuperati da AKS. Esempi di eventi a questo livello includono dashboard Kubernetes esposti, creazione di ruoli con privilegi elevati e creazione di montaggi sensibili.

    >[!NOTE]
    > Il Centro sicurezza genera avvisi di sicurezza per le azioni e le distribuzioni del servizio Kubernetes di Azure che si verificano dopo che l'opzione Kubernetes è stata abilitata nelle impostazioni della sottoscrizione. 

    Per un elenco degli avvisi a livello di cluster AKS, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-akscluster).

Inoltre, il team globale dei ricercatori di sicurezza monitora costantemente il panorama delle minacce. Aggiungono avvisi e vulnerabilità specifici del contenitore Man mano che vengono individuati.

> [!TIP]
> È possibile simulare gli avvisi del contenitore seguendo le istruzioni riportate in [questo post di Blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>Protezione dalle minacce per il database SQL e SQL Data Warehouse<a name="data-sql"></a>

Advanced Threat Protection per il database SQL di Azure rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database.

Verranno visualizzati gli avvisi in caso di attività di database sospette, potenziali vulnerabilità o attacchi intrusivi nel codice SQL e modelli di query e di accesso al database anomali.

Advanced Threat Protection per il database SQL di Azure e SQL fa parte del pacchetto unificato Advanced [Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) per le funzionalità avanzate di sicurezza di SQL, per i database SQL di Azure, le istanze gestite del database SQL di Azure, i database di Azure SQL data warehouse e i server SQL in macchine virtuali di Azure.

Per altre informazioni, vedi:

* [Come abilitare Advanced Threat Protection per il database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Come abilitare Advanced Threat Protection per SQL Server in macchine virtuali di Azure](security-center-iaas-advanced-data.md)
* [Elenco di avvisi di protezione dalle minacce per il database SQL e SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Protezione dalle minacce per archiviazione di Azure<a name="azure-storage"></a>

Advanced Threat Protection per l'archiviazione rileva tentativi insoliti e potenzialmente dannosi di accedere o sfruttare gli account di archiviazione. Questo livello di protezione consente di risolvere le minacce senza che sia necessario essere un esperto di sicurezza e consente di gestire i sistemi di monitoraggio della sicurezza.

Advanced Threat Protection per archiviazione di Azure è attualmente disponibile solo per l' [archiviazione BLOB](https://azure.microsoft.com/services/storage/blobs/). 

Questo servizio è disponibile in tutti i cloud pubblici e negli Stati Uniti, ma senza altre aree cloud sovrane o Azure per enti pubblici.

Per informazioni dettagliate sui prezzi, inclusa una versione di valutazione gratuita di 30 giorni, vedere la [pagina dei prezzi del Centro sicurezza di Azure](https://azure.microsoft.com/pricing/details/security-center/).

Per altre informazioni, vedi:

* [Come abilitare Advanced Threat Protection per archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Elenco degli avvisi di protezione dalle minacce per archiviazione di Azure](alerts-reference.md#alerts-azurestorage)

> [!TIP]
> È possibile simulare gli avvisi di archiviazione di Azure seguendo le istruzioni riportate in [questo post di Blog](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).




## <a name="threat-protection-for-azure-cosmos-db"></a>Protezione dalle minacce per Azure Cosmos DB<a name="cosmos-db"></a>

Gli avvisi Azure Cosmos DB vengono generati da tentativi insoliti e potenzialmente dannosi di accesso o di exploit Azure Cosmos DB account.

Per altre informazioni, vedi:

* [Advanced Threat Protection per Azure Cosmos DB (anteprima)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Elenco di avvisi di protezione dalle minacce per Azure Cosmos DB (anteprima)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Protezione dalle minacce per il livello rete di Azure<a name="network-layer"></a>

L'analisi a livello di rete del Centro sicurezza si basa su [dati IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)di esempio, ovvero le intestazioni dei pacchetti raccolte dai router core di Azure. In base a questo feed di dati, il Centro sicurezza usa i modelli di apprendimento automatico per identificare e contrassegnare le attività di traffico dannoso. Il Centro sicurezza usa anche il database di intelligence per le minacce di Microsoft per arricchire gli indirizzi IP.

Alcune configurazioni di rete possono impedire al centro sicurezza di generare avvisi sull'attività di rete sospetta. Per consentire al centro sicurezza di generare avvisi di rete, verificare quanto segue:

- La macchina virtuale ha un indirizzo IP pubblico o si trova in un servizio di bilanciamento del carico con un indirizzo IP pubblico.

- Il traffico in uscita di rete della macchina virtuale non è bloccato da una soluzione di ID esterno.

- Alla macchina virtuale è stato assegnato lo stesso indirizzo IP per l'intera ora durante la quale si è verificata la comunicazione sospetta. Questo vale anche per le macchine virtuali create come parte di un servizio gestito (ad esempio, AKS, databricks).

Per un elenco degli avvisi di livello rete di Azure, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azurenetlayer).

Per informazioni dettagliate sul modo in cui il Centro sicurezza può usare i segnali relativi alla rete per applicare la protezione dalle minacce, vedere [rilevamento di DNS euristici nel centro sicurezza](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Protezione dalle minacce per il livello di gestione di Azure (Azure Resource Manager) (anteprima)<a name ="management-layer"></a>

Il livello di protezione del Centro sicurezza basato su Azure Resource Manager è attualmente in fase di anteprima.

Il Centro sicurezza offre un ulteriore livello di protezione usando gli eventi Azure Resource Manager, che è considerato il piano di controllo per Azure. Analizzando i record di Azure Resource Manager, il Centro sicurezza rileva operazioni insolite o potenzialmente dannose nell'ambiente della sottoscrizione di Azure.

Per un elenco degli avvisi di Azure Resource Manager (anteprima), vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azureresourceman).



>[!NOTE]
> Alcune delle analisi precedenti sono basate su Microsoft Cloud App Security. Per trarre vantaggio da queste analisi, è necessario attivare una licenza Cloud App Security. Se si dispone di una licenza di Cloud App Security, questi avvisi sono abilitati per impostazione predefinita. Per disabilitare gli avvisi:
>
> 1. Nel pannello **Centro sicurezza** selezionare criteri di **sicurezza**. Per la sottoscrizione che si vuole modificare, selezionare **Modifica impostazioni**.
> 2. Selezionare **rilevamento minacce**.
> 3. In **Abilita integrazioni**deselezionare **Consenti Microsoft cloud app Security per accedere ai dati**e selezionare **Salva**.

>[!NOTE]
>Il Centro sicurezza archivia i dati dei clienti correlati alla sicurezza nella stessa area geografica della risorsa. Se Microsoft non ha ancora distribuito il Centro sicurezza nell'area geografica della risorsa, archivia i dati nel Stati Uniti. Quando Cloud App Security è abilitata, queste informazioni vengono archiviate in base alle regole di posizione geografica di Cloud App Security. Per altre informazioni, vedere [archiviazione dei dati per servizi non a livello di](https://azuredatacentermap.azurewebsites.net/)area.








## <a name="threat-protection-for-azure-key-vault-preview"></a>Protezione dalle minacce per Azure Key Vault (anteprima)<a name="azure-keyvault"></a>

> [!NOTE]
> Questo servizio non è attualmente disponibile in Azure per enti pubblici e aree cloud sovrane.

Azure Key Vault è un servizio cloud che protegge le chiavi di crittografia e i segreti, come certificati, stringhe di connessione e password. 

Il Centro sicurezza di Azure include la protezione avanzata dalle minacce di Azure nativa per Azure Key Vault, offrendo un ulteriore livello di intelligence per la sicurezza. Il Centro sicurezza rileva tentativi insoliti e potenzialmente dannosi di accesso o exploit Key Vault account. Questo livello di protezione consente di risolvere le minacce senza essere un esperto di sicurezza e senza la necessità di gestire sistemi di monitoraggio della sicurezza di terze parti.  

Quando si verificano attività anomale, il Centro sicurezza Mostra gli avvisi e, facoltativamente, li invia tramite posta elettronica agli amministratori della sottoscrizione. Questi avvisi includono i dettagli dell'attività sospetta e consigli su come analizzare e correggere le minacce. 

Per un elenco degli avvisi di Azure Key Vault, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azurekv).





## <a name="threat-protection-for-other-microsoft-services"></a>Protezione dalle minacce per altri servizi Microsoft<a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Protezione dalle minacce per Azure WAF<a name="azure-waf"></a>

Il gateway applicazione di Azure offre un web application firewall (WAF) che garantisce alle applicazioni Web una protezione centralizzata da exploit e vulnerabilità comuni.

Le applicazioni Web sono sempre più vittime di attacchi che sfruttano le più comuni e note vulnerabilità. Il WAF del gateway applicazione è basato sul set di regole di base 3,0 o 2.2.9 dal progetto di sicurezza dell'applicazione Web aperta. Il WAF viene aggiornato automaticamente per proteggersi da nuove vulnerabilità. 

Se si dispone di una licenza per Azure WAF, gli avvisi WAF vengono trasmessi al centro sicurezza senza che sia necessaria alcuna configurazione aggiuntiva. Per ulteriori informazioni sugli avvisi generati da WAF, vedere [regole e gruppi di regole CRS del Web Application Firewall](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Protezione dalle minacce per la protezione DDoS di Azure<a name="azure-ddos"></a>

Gli attacchi Distributed Denial of Service (DDoS) sono noti per essere facili da eseguire. Si tratta di un problema di sicurezza eccezionale, in particolare se si trasferiscono le applicazioni nel cloud. 

Un attacco DDoS tenta di esaurire le risorse di un'applicazione, che quindi non risulta più disponibile per gli utenti legittimi. Gli attacchi DDoS possono avere come destinazione qualsiasi endpoint che può essere raggiunto tramite Internet.

Per difendersi dagli attacchi DDoS, acquistare una licenza per la protezione DDoS di Azure e assicurarsi di seguire le procedure consigliate per la progettazione delle applicazioni. La protezione DDoS offre diversi livelli di servizio. Per altre informazioni, vedere [Panoramica di protezione DDoS di Azure](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Per un elenco degli avvisi di protezione DDoS di Azure, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sugli avvisi di sicurezza da queste funzionalità di protezione dalle minacce, vedere gli articoli seguenti:

* [Tabella di riferimento per tutti gli avvisi del Centro sicurezza di Azure](alerts-reference.md)
* [Avvisi di sicurezza nel Centro sicurezza di Azure](security-center-alerts-overview.md)
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md)
* [Esportare avvisi e raccomandazioni sulla sicurezza (anteprima)](continuous-export.md)