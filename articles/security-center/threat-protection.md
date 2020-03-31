---
title: Protezione dalle minacce nel Centro sicurezza di Azure
description: Questo argomento descrive le risorse protette dalle funzionalità di protezione dalle minacce del Centro sicurezza di AzureThis topic describes the resources protected by Azure Security Center's threat protection features
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: e348180eb94c1703ceecf2f2b00ab942ba5ff0ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536327"
---
# <a name="threat-protection-in-azure-security-center"></a>Protezione dalle minacce nel Centro sicurezza di Azure

Quando il Centro sicurezza rileva una minaccia in qualsiasi area dell'ambiente, genera un avviso. Questi avvisi descrivono i dettagli delle risorse interessate, i passaggi di correzione suggeriti e in alcuni casi un'opzione per attivare un'app per la logica in risposta.

La protezione dalle minacce del Centro sicurezza di Azure offre difese complete per l'ambiente:Azure Security Center's threat protection provides comprehensive defenses for your environment:

* Protezione dalle minacce per le **risorse di calcolo**di Azure: macchine Windows, macchine Linux, servizio app di Azure e contenitori di Azure

* **Protezione dalle minacce per**le risorse dati di Azure: database SQL e data warehouse SQL, Archiviazione di Azure e database Cosmos di Azure

* Protezione dalle minacce per i livelli del servizio di Azure: livello di rete di Azure, livello di gestione di Azure (Azure Resource Manager) (anteprima) e analto chiave di Azure (anteprima)Threat protection for **Azure service layers:** Azure network layer, Azure management layer (Azure Resource Manager) (Preview) and Azure Key Vault (Preview)

Se un avviso viene generato dal Centro sicurezza o ricevuto dal Centro sicurezza da un prodotto di sicurezza diverso, è possibile esportarlo. Per esportare gli avvisi in Azure Sentinel (o in un SIEM di terze parti) o in qualsiasi altro strumento esterno, seguire le istruzioni in [Esportazione degli avvisi in un SIEM](continuous-export.md). 




## <a name="threat-protection-for-windows-machines"></a>Protezione dalle minacce per i computer Windows<a name="windows-machines"></a>

Il Centro sicurezza di Azure si integra con i servizi di Azure per monitorare e proteggere i computer basati su Windows.Azure Security Center integrates with Azure services to monitor and protect your Windows-based machines. Il Centro sicurezza presenta gli avvisi e i suggerimenti per la correzione di tutti questi servizi in un formato di facile utilizzo.

* **Microsoft Defender ATP** <a name="windows-atp"></a> - Security Center estende le sue piattaforme di protezione del carico di lavoro cloud integrandosi con Microsoft Defender Advanced Threat Protection (ATP). Insieme, forniscono funzionalità complete di rilevamento e risposta degli endpoint (EDR).

    > [!IMPORTANT]
    > Il sensore Microsoft Defender ATP viene abilitato automaticamente nei server Windows che utilizzano il Centro sicurezza.

    Quando Microsoft Defender ATP rileva una minaccia, attiva un avviso. L'avviso viene visualizzato nel dashboard del Centro sicurezza. Dal dashboard, è possibile eseguire il pivot alla console Microsoft Defender ATP ed eseguire un'indagine dettagliata per scoprire l'ambito dell'attacco. Per ulteriori informazioni su Microsoft Defender ATP, vedere [Server di bordo per il servizio Microsoft Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* **Analisi** <a name="windows-dump"></a> del dump di arresto anomalo del sistema: quando il software si arresta in modo anomalo, un dump di arresto anomalo acquisisce una parte di memoria al momento dell'arresto anomalo.

    Un arresto anomalo potrebbe essere stato causato da malware o contenere malware. Per evitare di essere rilevati dai prodotti di sicurezza, varie forme di malware utilizzano un attacco fileless, che evita di scrivere su disco o di crittografare i componenti software scritti su disco. Questo tipo di attacco è difficile da rilevare utilizzando approcci tradizionali basati su disco.

    Tuttavia, utilizzando l'analisi della memoria, è possibile rilevare questo tipo di attacco. Analizzando la memoria nel dump di arresto anomalo del sistema, il Centro sicurezza è in grado di rilevare le tecniche utilizzata dall'attacco. Ad esempio, l'attacco potrebbe tentare di sfruttare le vulnerabilità del software, accedere a dati riservati e persistere di tipo surrettiziamente all'interno di una macchina compromessa. Il Centro sicurezza esegue questa operazione con un impatto minimo sulle prestazioni per gli host.

    Per informazioni dettagliate sugli avvisi di analisi del dump di arresto anomalo del sistema, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-windows).

* **Rilevamento** <a name="windows-fileless"></a> degli attacchi senza file: gli attacchi senza file destinati agli endpoint sono comuni. Per evitare il rilevamento, gli attacchi fileless iniettano payload dannosi in memoria. Payload attaccante persistono all'interno della memoria di processi compromessi, ed eseguire una vasta gamma di attività dannose.

    Con il rilevamento degli attacchi senza file, le tecniche forensi di memoria automatizzate identificano toolkit, tecniche e comportamenti di attacco senza file. Questa soluzione analizza periodicamente il computer in fase di esecuzione ed estrae le informazioni dettagliate direttamente dalla memoria dei processi critici per la sicurezza.

    Trova prove di sfruttamento, iniezione di codice, e l'esecuzione di payload dannosi. Il rilevamento degli attacchi senza file genera avvisi di sicurezza dettagliati per accelerare i tempi di risposta di triage, correlazione e downstream degli avvisi. Questo approccio integra le soluzioni EDR basate su eventi, fornendo una maggiore copertura di rilevamento.

    Per informazioni dettagliate sugli avvisi di rilevamento degli attacchi senza file, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-windows).

> [!TIP]
> È possibile simulare gli avvisi di Windows scaricando [Playbook: Avvisi di sicurezza](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)del Centro sicurezza di Azure.






## <a name="threat-protection-for-linux-machines"></a>Protezione dalle minacce per le macchine Linux<a name="linux-machines"></a>

Security Center raccoglie i record di controllo dalle macchine Linux utilizzando **controllato**, uno dei framework di controllo Linux più comuni. auditd nel kernel mainline. 

* **Avvisi controllati Linux e integrazione** <a name="linux-auditd"></a> di Microsoft Monitoring Agent (MMA): il sistema controllato è costituito da un sottosistema a livello di kernel, responsabile del monitoraggio delle chiamate di sistema. Li filtra in base a un set di regole specificato e scrive i messaggi per loro in un socket. Security Center integra le funzionalità del pacchetto controllato all'interno di Microsoft Monitoring Agent (MMA). Questa integrazione consente la raccolta di eventi controllati in tutte le distribuzioni Linux supportate, senza prerequisiti.  

    i record controllati vengono raccolti, arricchiti e aggregati in eventi utilizzando l'agente MMA Linux. Il Centro sicurezza aggiunge continuamente nuove analisi che usano segnali Linux per rilevare comportamenti dannosi nelle macchine Linux cloud e locali. Analogamente alle funzionalità di Windows, queste analisi si estendono su processi sospetti, tentativi di accesso dubbi, caricamento di moduli del kernel e altre attività. Queste attività possono indicare che una macchina è sotto attacco o è stata violata.  

    Per un elenco degli avvisi Linux, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-linux).

> [!TIP]
> È possibile simulare gli avvisi Linux scaricando Il Playbook del Centro sicurezza di [Azure: Rilevamenti Linux.](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)





## <a name="threat-protection-for-azure-app-service"></a>Protezione dalle minacce per il servizio app di Azure<a name="app-services"></a>

> [!NOTE]
> Questo servizio non è attualmente disponibile nelle aree cloud pubbliche e sovrane di Azure.This service is not currently available in Azure government and sovereign cloud regions.

Il Centro sicurezza usa la scala del cloud per identificare gli attacchi mirati alle applicazioni in esecuzione sul servizio app. Gli aggressori sondano le applicazioni web per trovare e sfruttare i punti deboli. Prima di essere instradate ad ambienti specifici, le richieste alle applicazioni in esecuzione in Azure passano attraverso diversi gateway, dove vengono esaminate e registrate. Questi dati vengono quindi utilizzati per identificare exploit e aggressori, e per imparare nuovi modelli che verranno utilizzati in seguito.

Usando la visibilità di Azure come provider di cloud, il Centro sicurezza analizza i log interni del servizio app per identificare la metodologia di attacco su più destinazioni. Ad esempio, la metodologia include la scansione diffusa e gli attacchi distribuiti. Questo tipo di attacco proviene in genere da un piccolo sottoinsieme di indirizzi IP e mostra modelli di ricerca per indicizzazione in endpoint simili in più host. Gli attacchi sono alla ricerca di una pagina o di un plugin vulnerabile e non possono essere identificati dal punto di vista di un singolo host.

Se si esegue un piano di servizio app basato su Windows, il Centro sicurezza ha anche accesso alle sandbox e alle macchine virtuali sottostanti. Insieme ai dati di registro di cui sopra, l'infrastruttura può raccontare la storia, da un nuovo attacco che circola in natura a compromessi nelle macchine dei clienti. Pertanto, anche se il Centro sicurezza viene distribuito dopo che un'app Web è stata sfruttata, potrebbe essere in grado di rilevare gli attacchi in corso.

Per un elenco degli avvisi del servizio app di Azure, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azureappserv).

Per altre informazioni sui piani del servizio app, vedere [Piani di servizio app](https://azure.microsoft.com/pricing/details/app-service/plans/).





## <a name="threat-protection-for-azure-containers"></a>Protezione dalle minacce per i contenitori di Azure<a name="azure-containers"></a>

> [!NOTE]
> Questo servizio non è attualmente disponibile nelle aree cloud pubbliche e sovrane di Azure.This service is not currently available in Azure government and sovereign cloud regions.

Il Centro sicurezza fornisce la protezione dalle minacce in tempo reale per gli ambienti containerizzati e genera avvisi per attività sospette. È possibile usare queste informazioni per risolvere rapidamente i problemi di sicurezza e migliorare la sicurezza dei contenitori.

Il Centro sicurezza offre una protezione dalle minacce a diversi livelli: 

* **Livello host:** l'agente del Centro sicurezza (disponibile nel livello Standard, vedere [i prezzi](security-center-pricing.md) per i dettagli) monitora Linux per attività sospette. L'agente attiva avvisi per attività sospette provenienti dal nodo o da un contenitore in esecuzione su di esso. Esempi di tali attività includono il rilevamento della shell web e la connessione con indirizzi IP sospetti noti.

    Per informazioni più approfondite sulla sicurezza dell'ambiente containerizzato, l'agente monitora l'analisi specifica del contenitore. Verranno attivati avvisi per eventi quali la creazione di contenitori con privilegi, l'accesso sospetto ai server API e i server Secure Shell (SSH) in esecuzione all'interno di un contenitore Docker.

    >[!IMPORTANT]
    > Se si sceglie di non installare gli agenti negli host, si riceverà solo un sottoinsieme dei vantaggi di protezione dalle minacce e degli avvisi di sicurezza. Continuerai a ricevere avvisi relativi all'analisi della rete e alle comunicazioni con server dannosi.

    Per un elenco degli avvisi a livello di host, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-containerhost).


* A livello di **cluster AKS**, la protezione dalle minacce si basa sull'analisi dei log di controllo di Kubernetes. Per abilitare questo monitoraggio **senza agenti,** aggiungere l'opzione Kubernetes all'abbonamento dalla pagina **Impostazioni & prezzi** (vedere [prezzi](security-center-pricing.md)). Per generare avvisi a questo livello, il Centro sicurezza monitora i servizi gestiti da AKS utilizzando i log recuperati da AKS. Esempi di eventi a questo livello includono dashboard Kubernetes esposti, creazione di ruoli con privilegi elevati e la creazione di supporti sensibili.

    >[!NOTE]
    > Il Centro sicurezza genera avvisi di sicurezza per le azioni e le distribuzioni del servizio Azure Kubernetes che si verificano dopo l'abilitazione dell'opzione Kubernetes nelle impostazioni della sottoscrizione. 

    Per un elenco degli avvisi a livello di cluster AKS, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-akscluster).

Inoltre, il nostro team globale di ricercatori di sicurezza monitorare costantemente il panorama delle minacce. Aggiunge avvisi e vulnerabilità specifici del contenitore man mano che vengono individuati.

> [!TIP]
> È possibile simulare gli avvisi del contenitore seguendo le istruzioni in [questo post](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)di blog .








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>Protezione dalle minacce per il database SQL e il data warehouse SQL<a name="data-sql"></a>

Advanced Threat Protection per il database SQL di Azure rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database.

Verranno visualizzati avvisi quando sono presenti attività di database sospette, potenziali vulnerabilità o attacchi SQL injection e modelli di query e accesso al database anomali.

Advanced Threat Protection for Azure SQL Database and SQL fa parte del pacchetto unificato [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) per funzionalità di sicurezza SQL avanzate, che copre i database SQL di Azure, le istanze gestite del database SQL di Azure, i database del data warehouse SQL di Azure e i server SQL nelle macchine virtuali di Azure.

Per altre informazioni, vedere:

* [Come abilitare Advanced Threat Protection per il database SQL di AzureHow to enable Advanced Threat Protection for Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Come abilitare Advanced Threat Protection per i server SQL nelle macchine virtuali di AzureHow to enable Advanced Threat Protection for SQL servers on Azure Virtual Machines](security-center-iaas-advanced-data.md)
* [Elenco di avvisi di protezione dalle minacce per il database SQL e sql Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)




## <a name="threat-protection-for-azure-storage"></a>Protezione dalle minacce per Archiviazione di Azure<a name="azure-storage"></a>

> [!NOTE]
> Questo servizio è disponibile nei cloud degli enti pubblici degli Stati Uniti, ma non in altre aree cloud sovrane o governative di Azure.This service is available in US government clouds, but no other sovereign or Azure government cloud regions.

Advanced Threat Protection for Storage (attualmente disponibile solo per l'archiviazione BLOB) rileva tentativi insoliti e potenzialmente dannosi di accedere o sfruttare gli account di archiviazione. Questo livello di protezione consente di affrontare le minacce senza richiedere di essere esperti di sicurezza e consente di gestire i sistemi di monitoraggio della sicurezza.

Per altre informazioni, vedere:

* [Come abilitare Advanced Threat Protection per Archiviazione di AzureHow to enable Advanced Threat Protection for Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Elenco di avvisi di protezione dalle minacce per Archiviazione di AzureThe list of threat protection alerts for Azure Storage](alerts-reference.md#alerts-azurestorage)


> [!TIP]
> È possibile simulare gli avvisi di Archiviazione di Azure seguendo le istruzioni in questo post di [blog.](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)




## <a name="threat-protection-for-azure-cosmos-db"></a>Protezione dalle minacce per Azure Cosmos DB<a name="cosmos-db"></a>

Gli avvisi di Azure Cosmos DB vengono generati da tentativi insoliti e potenzialmente dannosi di accedere o sfruttare gli account di Azure Cosmos DB.

Per altre informazioni, vedere:

* [Advanced Threat Protection for Azure Cosmos DB (anteprima)Advanced Threat Protection for Azure Cosmos DB (Preview)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Elenco di avvisi di protezione dalle minacce per Azure Cosmos DB (anteprima)The list of threat protection alerts for Azure Cosmos DB (Preview)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Protezione dalle minacce per il livello di rete di Azure<a name="network-layer"></a>

L'analisi a livello di rete del Centro sicurezza si basa sui dati IPFIX di esempio, ovvero intestazioni di pacchetti raccolte dai router principali di Azure.Security Center network-layer analytics are based on sample [IPFIX data](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), which are packet headers collected by Azure core routers. Basato su questo feed di dati, il Centro sicurezza utilizza modelli di apprendimento automatico per identificare e contrassegnare le attività di traffico dannoso. Il Centro sicurezza usa inoltre il database Microsoft Threat Intelligence per arricchire gli indirizzi IP.

Alcune configurazioni di rete possono impedire al Centro sicurezza di generare avvisi su attività di rete sospette. Affinché il Centro sicurezza generi avvisi di rete, verificare che:For Security Center to generate network alerts, ensure that:

- La macchina virtuale ha un indirizzo IP pubblico (o si trova in un servizio di bilanciamento del carico con un indirizzo IP pubblico).

- Il traffico di rete della macchina virtuale non è bloccato da una soluzione IDS esterna.

- Alla macchina virtuale è stato assegnato lo stesso indirizzo IP per l'intera ora durante la quale si è verificata la comunicazione sospetta. Questo vale anche per le macchine virtuali create come parte di un servizio gestito (ad esempio, AKS, Databricks).

Per un elenco degli avvisi del livello di rete di Azure, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azurenetlayer).

Per informazioni dettagliate su come il Centro sicurezza può utilizzare i segnali relativi alla rete per applicare la protezione dalle minacce, vedere [Rilevamenti DNS euristici nel Centro sicurezza.](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Protezione dalle minacce per il livello di gestione di Azure (Azure Resource Manager) (anteprima)Threat protection for Azure management layer (Azure Resource Manager) (Preview)<a name ="management-layer"></a>

Il livello di protezione del Centro sicurezza basato su Azure Resource Manager è attualmente in anteprima.

Il Centro sicurezza offre un ulteriore livello di protezione usando gli eventi di Azure Resource Manager, che è considerato il piano di controllo per Azure.Security Center offers an additional layer of protection by using Azure Resource Manager events, which is considered to be the control plane for Azure. Analizzando i record di Azure Resource Manager, il Centro sicurezza rileva operazioni insolite o potenzialmente dannose nell'ambiente di sottoscrizione di Azure.By analyzing the Azure Resource Manager records, Security Center detects unusual or potentially harmful operations in the Azure subscription environment.

Per un elenco degli avvisi di Azure Resource Manager (anteprima), vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azureresourceman).



>[!NOTE]
> Molte delle analisi precedenti sono basate su Microsoft Cloud App Security. Per trarre vantaggio da queste analisi, è necessario attivare una licenza di Cloud App Security.To benefit from these analytics, you must activate a Cloud App Security license. Se si dispone di una licenza Cloud App Security, questi avvisi sono abilitati per impostazione predefinita. Per disattivare gli avvisi:
>
> 1. Nel pannello **Centro sicurezza** selezionare Criteri **di sicurezza**. Per la sottoscrizione che si desidera modificare, selezionare **Modifica impostazioni**.
> 2. Selezionare **Rilevamento minacce**.
> 3. In **Abilita integrazioni** **deselezionare Consenti a Microsoft Cloud App Security di accedere ai dati personali**e selezionare **Salva**.

>[!NOTE]
>Il Centro sicurezza archivia i dati dei clienti relativi alla sicurezza nella stessa area geografica della relativa risorsa. Se Microsoft non ha ancora distribuito il Centro sicurezza nell'area geografica della risorsa, archivia i dati negli Stati Uniti. Quando Cloud App Security è abilitato, queste informazioni vengono archiviate in conformità con le regole di posizione geografica di Cloud App Security. Per ulteriori informazioni, vedere [Archiviazione dei dati per servizi non regionali](https://azuredatacentermap.azurewebsites.net/).








## <a name="threat-protection-for-azure-key-vault-preview"></a>Protezione dalle minacce per l'insieme di credenziali delle chiavi di Azure (anteprima)Threat protection for Azure Key Vault (Preview)<a name="azure-keyvault"></a>

> [!NOTE]
> Questo servizio non è attualmente disponibile nelle aree cloud pubbliche e sovrane di Azure.This service is not currently available in Azure government and sovereign cloud regions.

Azure Key Vault è un servizio cloud che protegge le chiavi di crittografia e i segreti, come certificati, stringhe di connessione e password. 

Il Centro sicurezza di Azure include la protezione avanzata dalle minacce di Azure per L'insieme di credenziali delle chiavi di Azure, fornendo un ulteriore livello di intelligence di sicurezza. Il Centro sicurezza rileva tentativi insoliti e potenzialmente dannosi di accedere o sfruttare gli account dell'insieme di credenziali delle chiavi. Questo livello di protezione consente di affrontare le minacce senza essere un esperto di sicurezza e senza la necessità di gestire sistemi di monitoraggio della sicurezza di terze parti.  

Quando si verificano attività anomale, il Centro sicurezza mostra gli avvisi e, facoltativamente, li invia tramite posta elettronica agli amministratori della sottoscrizione. Questi avvisi includono i dettagli delle attività sospette e consigli su come analizzare e correggere le minacce. 

Per un elenco degli avvisi dell'insieme di credenziali delle chiavi di Azure, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azurekv).





## <a name="threat-protection-for-other-microsoft-services"></a>Protezione dalle minacce per altri servizi Microsoft<a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Protezione dalle minacce per Azure WAF<a name="azure-waf"></a>

Il gateway applicazione di Azure offre un web application firewall (WAF) che garantisce alle applicazioni Web una protezione centralizzata da exploit e vulnerabilità comuni.

Le applicazioni Web sono sempre più vittime di attacchi che sfruttano le più comuni e note vulnerabilità. Il WAF del gateway applicazione è basato sul set di regole di base 3.0 o 2.2.9 del progetto Open Web Application Security. Il WAF viene aggiornato automaticamente per proteggersi dalle nuove vulnerabilità. 

Se si dispone di una licenza per Azure WAF, gli avvisi WAF vengono trasmessi al Centro sicurezza senza alcuna configurazione aggiuntiva. Per ulteriori informazioni sugli avvisi generati da WAF, vedere Regole e rule CRS del [firewall dell'applicazione Web](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Protezione dalle minacce per la protezione DDoS di Azure<a name="azure-ddos"></a>

Gli attacchi DDoS (Distributed Denial of Service) sono noti per essere facili da eseguire. Sono diventati un grande problema di sicurezza, in particolare se si spostano le applicazioni nel cloud. 

Un attacco DDoS tenta di esaurire le risorse di un'applicazione, che quindi non risulta più disponibile per gli utenti legittimi. Gli attacchi DDoS possono colpire qualsiasi endpoint raggiungibile tramite Internet.

Per difendersi dagli attacchi DDoS, acquistare una licenza per Azure DDoS Protection e assicurarsi di seguire le procedure consigliate per la progettazione delle applicazioni. La protezione DDoS offre diversi livelli di servizio. Per altre informazioni, vedere Panoramica di Protezione DDoS di Azure.For more information, see [Azure DDoS Protection overview](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Per un elenco degli avvisi di Azure DDoS Protection, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sugli avvisi di sicurezza da queste funzionalità di protezione dalle minacce, vedere gli articoli seguenti:To learn more about the security alerts from these threat protection features, see the following articles:

* [Tabella di riferimento per tutti gli avvisi del Centro sicurezza di AzureReference table for all Azure Security Center alerts](alerts-reference.md)
* [Avvisi di sicurezza nel Centro sicurezza di Azure](security-center-alerts-overview.md)
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md)
* [Esportare avvisi e suggerimenti per la sicurezza (anteprima)Export security alerts and recommendations (Preview)](continuous-export.md)