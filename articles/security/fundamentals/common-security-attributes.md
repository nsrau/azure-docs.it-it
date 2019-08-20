---
title: Attributi di sicurezza per i servizi di Azure
description: Elenco di controllo degli attributi di sicurezza per la valutazione dei servizi di Azure
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 43eb7e5c4cab722eb97f9e2fe819c9c79bae45d9
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828777"
---
# <a name="security-attributes-for-azure-services"></a>Attributi di sicurezza per i servizi di Azure

Questo articolo raccoglie gli attributi di sicurezza per i servizi di Azure selezionati. Un attributo di sicurezza è una qualità o una funzionalità di un servizio di Azure. Contribuisce alla capacità del servizio di prevenire, rilevare e rispondere alle vulnerabilità di sicurezza.

Gli attributi di sicurezza sono classificati come segue:
* Prevenzione
* Segmentazione di rete
* Rilevamento
* Supporto per la gestione delle identità e degli accessi
* Audit trail
* Controlli di accesso (se usati)
* Gestione della configurazione (se usata)

In ogni categoria viene visualizzato "Yes" o "No" per indicare se viene utilizzato un attributo. Per alcuni servizi, viene visualizzato "N/A" per un attributo non applicabile. È anche possibile fornire una nota o un collegamento ad altre informazioni su un attributo.

## <a name="api-managementazureapi-managementapi-management-security-attributes"></a>[Gestione API](/azure/api-management/api-management-security-attributes)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia | I dati sensibili, ad esempio i certificati, le chiavi e i valori con nome segreto, vengono crittografati con chiavi di istanza di servizio gestite dal servizio. |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet) | Sì | [Express Route](/azure/expressroute/index) e la crittografia VNet sono fornite da [rete di Azure](/azure/virtual-network/index). |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| No | Tutte le chiavi di crittografia sono per istanza del servizio e sono gestite dal servizio. |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Sì | Le chiamate del piano di gestione vengono effettuate tramite [Azure Resource Manager](/azure/azure-resource-manager/index) su TLS. È necessario un token JSON Web (JWT) valido.  Le chiamate del piano dati possono essere protette con TLS e uno dei meccanismi di autenticazione supportati (ad esempio, il certificato client o JWT).
 |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| No | |
| Supporto di VNet Injection| Sì | |
| Isolamento rete e supporto del firewall| Sì | Usando i gruppi di sicurezza di rete (NSG) e applicazione Azure Gateway (o altro appliance software) rispettivamente. |
| Supporto del tunneling forzato| Sì | Sulla rete di Azure è disponibile il tunneling forzato. |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Yes | |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | |
| Authorization| Sì | |


### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Sì | [Log attività di monitoraggio di Azure](/azure/azure-monitor/platform/activity-logs-overview) |
| Registrazione e controllo del piano dati| Sì | [Log di diagnostica di monitoraggio di Azure](/azure/azure-monitor/platform/diagnostic-logs-overview) e, facoltativamente, [applicazione Azure informazioni dettagliate](/azure/azure-monitor/app/app-insights-overview).|

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Uso del [Resource Kit DevOps di gestione API di Azure](https://aka.ms/apimdevops) |

### <a name="vulnerability-scans-false-positives"></a>Analisi della vulnerabilità falsi positivi

Questa sezione documenta le vulnerabilità comuni che non influiscono sulla gestione API di Azure.

| Vulnerabilità               | Descrizione                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed è una vulnerabilità nell'implementazione dell'estensione TLS SessionTicket presente in alcuni prodotti F5. Consente la perdita ("emorragia") di un massimo di 31 byte di dati dalla memoria non inizializzata. Questo problema si verifica perché lo stack TLS riempie un ID di sessione, passato dal client, con dati per renderlo 32 bit. |


## <a name="app-serviceazureapp-serviceapp-service-security-attributes"></a>[Servizio app](/azure/app-service/app-service-security-attributes)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia | Yes | Il contenuto del file di sito Web viene archiviato in archiviazione di Azure, che crittografa automaticamente il contenuto inattivo. Vedere [crittografia di archiviazione di Azure per dati](/azure/storage/common/storage-service-encryption)inattivi.<br><br>I segreti forniti dal cliente vengono crittografati a riposo. I segreti vengono crittografati a riposo durante l'archiviazione nei database di configurazione del servizio app.<br><br>I dischi collegati localmente possono essere facoltativamente usati come archiviazione temporanea da siti Web (D:\Local e% TMP%). I dischi collegati localmente non sono crittografati. |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet) | Sì | I clienti possono configurare i siti Web in modo da richiedere e usare HTTPS per il traffico in ingresso. Vedere il post [di Blog come creare solo un servizio app Azure HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| Yes | I clienti possono scegliere di archiviare i segreti dell'applicazione in Key Vault e recuperarli in fase di esecuzione. Vedere [usare i riferimenti Key Vault per il servizio app e funzioni di Azure (anteprima)](/azure/app-service/app-service-key-vault-references).|
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Sì | Le chiamate di gestione per configurare il servizio app si verificano tramite [Azure Resource Manager](/azure/azure-resource-manager/index) chiamate su HTTPS. |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Sì | Attualmente disponibile in anteprima per il servizio app. Vedere [restrizioni di accesso al servizio app Azure](/azure/app-service/app-service-ip-restrictions). |
| Supporto di VNet Injection| Sì | Gli ambienti del servizio app sono implementazioni private del servizio App dedicato a un singolo cliente inserito nella rete virtuale di un cliente. Vedere [Introduzione agli ambienti del servizio app](/azure/app-service/environment/intro). |
| Isolamento rete e supporto del firewall| Sì | Per la variante pubblica multi-tenant del servizio app, i clienti possono configurare gli ACL di rete (restrizioni IP) per bloccare il traffico in ingresso consentito.  Vedere [restrizioni di accesso al servizio app Azure](/azure/app-service/app-service-ip-restrictions).  Gli ambienti del servizio app vengono distribuiti direttamente nelle reti virtuali e possono quindi essere protetti con gruppi. |
| Supporto del tunneling forzato| Yes | Gli ambienti del servizio app possono essere distribuiti nella rete virtuale di un cliente in cui è configurato il tunneling forzato. I clienti devono seguire le istruzioni riportate in [configurare il ambiente del servizio app con](/azure/app-service/environment/forced-tunnel-support)il tunneling forzato. |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Yes | Il servizio app si integra con Application Insights per le lingue che supportano Application Insights (Full .NET Framework, .NET Core, Java e node. JS).  Vedere [monitorare le prestazioni del servizio app Azure](/azure/azure-monitor/app/azure-web-apps). Il servizio app invia anche le metriche dell'applicazione in monitoraggio di Azure. Vedere [monitorare le app nel servizio app Azure](/azure/app-service/web-sites-monitor). |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | I clienti possono compilare applicazioni nel servizio app che si integrano automaticamente con [Azure Active Directory (Azure ad)](/azure/active-directory/index) e altri provider di identità compatibili con OAuth; vedere [autenticazione e autorizzazione nel servizio app Azure](/azure/app-service/overview-authentication-authorization). Per la gestione dell'accesso alle risorse del servizio app, tutti gli accessi sono controllati da una combinazione di Azure AD entità autenticata e Azure Resource Manager ruoli RBAC. |
| Authorization| Yes | Per la gestione dell'accesso alle risorse del servizio app, tutti gli accessi sono controllati da una combinazione di Azure AD entità autenticata e Azure Resource Manager ruoli RBAC.  |


### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Sì | Tutte le operazioni di gestione eseguite sugli oggetti del servizio app si verificano tramite [Azure Resource Manager](/azure/azure-resource-manager/index). I log cronologici di queste operazioni sono disponibili sia nel portale che tramite l'interfaccia della riga di comando. vedere [Azure Resource Manager operazioni del provider di risorse](/azure/role-based-access-control/resource-provider-operations#microsoftweb) e [AZ monitor Activity-log](/cli/azure/monitor/activity-log). |
| Registrazione e controllo del piano dati | No | Il piano dati per il servizio app è una condivisione file remota che contiene il contenuto del sito Web distribuito del cliente.  Nessun controllo della condivisione file remota. |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Yes | Per le operazioni di gestione, lo stato di una configurazione del servizio app può essere esportato come modello di Azure Resource Manager e con controllo delle versioni nel tempo. Per le operazioni di runtime, i clienti possono gestire più versioni live di un'applicazione usando la funzionalità slot di distribuzione del servizio app. | 



## <a name="azure-resource-managerazureazure-resource-managerazure-resource-manager-security-attributes"></a>[Azure Resource Manager](/azure/azure-resource-manager/azure-resource-manager-security-attributes)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia | Yes |  |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet) | Sì | HTTPS/TLS. |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| N/D | Azure Resource Manager non archivia alcun contenuto del cliente, ma solo i dati di controllo. |
| Crittografia a livello di colonna (servizi dati di Azure)| Sì | |
| Chiamate API crittografate| Sì | |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| No | |
| Supporto di VNet Injection| Yes | |
| Isolamento rete e supporto del firewall| No |  |
| Supporto del tunneling forzato| No |  |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| No | |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Basato su [Azure Active Directory](/azure/active-directory) .|
| Authorization| Sì | |


### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Yes | I log attività espongono tutte le operazioni di scrittura (PUT, POST, DELETE) eseguite sulle risorse; vedere [visualizzare i log attività per controllare le azioni sulle risorse](/azure/azure-resource-manager/resource-group-audit). |
| Registrazione e controllo del piano dati| N/D | |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Yes |  |


## <a name="azure-backupazurebackupbackup-security-attributes"></a>[Backup di Azure](/azure/backup/backup-security-attributes)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia| Sì | Uso della crittografia del servizio di archiviazione per gli account di archiviazione. |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet) | No | Uso di HTTPS. |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| No |  |
| Crittografia a livello di colonna (servizi dati di Azure)| No |  |
| Chiamate API crittografate| Yes |  |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| No |  |
| Supporto di VNet Injection| No |  |
| Isolamento rete e supporto del firewall| Sì | Il tunneling forzato è supportato per il backup delle macchine virtuali. Il tunneling forzato non è supportato per i carichi di lavoro in esecuzione all'interno di macchine virtuali. |
| Supporto del tunneling forzato| No |  |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Log Analytics è supportato tramite i log di diagnostica. Per altre informazioni, vedere [monitorare i carichi di lavoro protetti di backup di Azure con log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) . |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | L'autenticazione avviene tramite Azure Active Directory. |
| Authorization| Sì | Vengono usati ruoli RBAC creati dai clienti o predefiniti. Per altre informazioni, vedere [usare il controllo degli accessi in base al ruolo per gestire i punti di ripristino di backup di Azure](/azure/backup/backup-rbac-rs-vault) . |


### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Sì | Tutte le azioni attivate dai clienti dal portale di Azure vengono registrate nei log attività. |
| Registrazione e controllo del piano dati| No | Il piano dati di Backup di Azure non è raggiungibile direttamente.  |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Yes|  |

## <a name="cosmos-dbazurecosmos-dbcosmos-db-security-attributes"></a>[Cosmos DB](/azure/cosmos-db/cosmos-db-security-attributes)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia | Sì | Tutti i backup e i database di Cosmos DB vengono crittografati per impostazione predefinita. vedere [crittografia dei dati in Azure Cosmos DB](/azure/cosmos-db/database-encryption-at-rest). La crittografia lato server con chiavi gestite dal cliente non è supportata. |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Tutti i dati Azure Cosmos DB vengono crittografati in transito. |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| No |  |
| Crittografia a livello di colonna (servizi dati di Azure)| Yes | Solo nell'API Tables Premium. Non tutte le API supportano questa funzionalità. Vedere [Introduzione a Azure Cosmos DB: API Tabella](/azure/cosmos-db/table-introduction). |
| Chiamate API crittografate| Yes | Tutte le connessioni a Azure Cosmos DB supportano HTTPS. Azure Cosmos DB supporta anche le connessioni TLS 1,2, ma questo non è ancora applicato. Se i clienti disattivano TLS di livello inferiore al termine, possono garantire la connessione a Cosmos DB.  |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Sì |  |
| Supporto di VNet Injection| Sì | Con l'endpoint del servizio VNet è possibile configurare un account Azure Cosmos DB per consentire l'accesso solo da una subnet specifica di una rete virtuale (VNet). È anche possibile combinare l'accesso VNet con le regole del firewall.  Vedere [accedere Azure Cosmos DB da reti virtuali](/azure/cosmos-db/vnet-service-endpoint). |
| Isolamento rete e supporto del firewall| Sì | Con il supporto del firewall, è possibile configurare l'account Azure Cosmos per consentire l'accesso solo da un set di indirizzi IP approvato, un intervallo di indirizzi IP e/o servizi cloud. Vedere [configurare il firewall IP in Azure Cosmos DB](/azure/cosmos-db/how-to-configure-firewall).|
| Supporto del tunneling forzato| Sì | Può essere configurato sul lato client nel VNet in cui si trovano le macchine virtuali.   |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Vengono registrate tutte le richieste inviate a Azure Cosmos DB. Il [monitoraggio di Azure](/azure/azure-monitor/overview), le metriche di Azure e la registrazione di controllo di Azure sono supportate.  È possibile registrare le informazioni corrispondenti alle richieste del piano dati, le statistiche di runtime delle query, il testo della query, le richieste MongoDB. È anche possibile configurare gli avvisi. |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | Sì a livello di account del database; a livello di piano dati Cosmos DB usa i token delle risorse e l'accesso alla chiave. |
| Authorization| Sì | Supportato nell'account Azure Cosmos con chiavi master (primarie e secondarie) e token di risorsa. È possibile ottenere l'accesso in lettura/scrittura o in sola lettura ai dati con chiavi master. I token delle risorse consentono un accesso limitato al tempo a risorse quali documenti e contenitori. |

### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Yes | Log attività di Azure per le operazioni a livello di account, ad esempio firewall, reti virtuali, accesso alle chiavi e IAM. |
| Registrazione e controllo del piano dati | Yes | Registrazione del monitoraggio della diagnostica per operazioni a livello di contenitore come create container, provisioning della velocità effettiva, indicizzazione dei criteri e operazioni CRUD sui documenti. |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| No  | | 

### <a name="additional-security-attributes-for-cosmos-db"></a>Attributi di sicurezza aggiuntivi per Cosmos DB

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Condivisione risorse tra le origini (CORS) | Sì | Vedere [configurare la condivisione di risorse tra le origini (CORS)](/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing). |


## <a name="event-hubsazureevent-hubsevent-hubs-security-attributes"></a>[Hub eventi](/azure/event-hubs/event-hubs-security-attributes)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia |  Sì | |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet) | Yes | |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| No |  |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Sì |  |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Sì |  |
| Supporto di VNet Injection| No | |
| Isolamento rete e supporto del firewall| Yes |  |
| Supporto del tunneling forzato| No |  |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Yes | |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | |
| Authorization|  Yes | |


### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Sì |  |
| Registrazione e controllo del piano dati| Sì |   |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | |


## <a name="expressrouteazureexpressrouteexpressroute-security-attributes"></a>[ExpressRoute](/azure/expressroute/expressroute-security-attributes)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia |  N/D | ExpressRoute non archivia i dati dei clienti. |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet) | No | |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| N/D |  |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Sì | Tramite [Azure Resource Manager](/azure/azure-resource-manager/index) e HTTPS. |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| N/D |  |
| Supporto di VNet Injection| N/D | |
| Isolamento rete e supporto del firewall| Yes | Ogni cliente è contenuto nel proprio dominio di routing ed è sottopercorso al tunneling per il proprio VNet |
| Supporto del tunneling forzato| N/D | Tramite Border Gateway Protocol (BGP). |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Vedere [monitoraggio, metriche e avvisi di ExpressRoute](/azure/expressroute/expressroute-monitoring-metrics-alerts).|

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Account del servizio per il gateway per Microsoft (GWM) (controller); Accesso just-in-time (JIT) per dev e OP. |
| Authorization|  Sì |Account del servizio per il gateway per Microsoft (GWM) (controller); Accesso just-in-time (JIT) per dev e OP. |


### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note| 
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Sì |  |
| Registrazione e controllo del piano dati| No |   |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Tramite il provider di risorse di rete (NRP). |


## <a name="key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Insieme di credenziali di chiave](../../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia | Yes | Vengono crittografati tutti gli oggetti. |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet) | Yes | Tutte le comunicazioni vengono effettuate tramite chiamate API crittografate |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| Yes | Il cliente controlla tutte le chiavi nel Key Vault. Quando si specificano le chiavi supportate del modulo di protezione hardware (HSM), un HSM di livello 2 di FIPS protegge la chiave, il certificato o il segreto. |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D |  |
| Chiamate API crittografate| Yes | Uso di HTTPS. |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Sì | Uso degli endpoint di servizio della rete virtuale (VNet). |
| Supporto di VNet Injection| No |  |
| Isolamento rete e supporto del firewall| Yes | Uso delle regole del firewall di VNet. |
| Supporto del tunneling forzato| No |  |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Viene usato Log Analytics. |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | L'autenticazione avviene tramite Azure Active Directory. |
| Authorization| Sì | Vengono usati i criteri di accesso dell'insieme di credenziali delle chiavi. |

### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Piano di gestione/controllo - Registrazione e controllo| Yes | Viene usato Log Analytics. |
| Registrazione e controllo del piano dati| Yes | Viene usato Log Analytics. |

### <a name="access-controls"></a>Controlli di accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Piano di gestione/controllo - Controlli di accesso | Sì | Controllo degli accessi in base al ruolo di Azure Resource Manager |
| Piano dati - Controlli di accesso (a ogni livello di servizio) | Yes | Criteri di accesso insieme di credenziali delle chiavi |

## <a name="load-balancerload-balancerload-balancer-security-attributesmd"></a>[Bilanciamento del carico](../../load-balancer/load-balancer-security-attributes.md)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia | N/D | |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| N/D | |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| N/D | |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Sì | Tramite il [Azure Resource Manager](/azure/azure-resource-manager/index). |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| N/D | |
| Supporto di VNet Injection| N/D | . |
| Isolamento rete e supporto del firewall| N/D |  |
| Supporto del tunneling forzato| N/D | |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Vedere [log di monitoraggio di Azure per Load Balancer di base pubblici](/azure/load-balancer/load-balancer-monitor-log). |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| N/D |  |
| Authorization| N/D |  |

### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Sì | Vedere [log di monitoraggio di Azure per Load Balancer di base pubblici](/azure/load-balancer/load-balancer-monitor-log). |
| Registrazione e controllo del piano dati | N/D |  |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| N/D |  | 

## <a name="service-bus-messagingazureservice-bus-messagingservice-bus-messaging-security-attributes"></a>[Messaggistica del bus di servizio](/azure/service-bus-messaging/service-bus-messaging-security-attributes)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia |  Sì per la crittografia lato server per impostazione predefinita. | Chiavi gestite dal cliente e BYOK non sono ancora supportate. La crittografia lato client è responsabilità del client |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet) | Sì | Supporta il meccanismo HTTPS/TLS standard. |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| No |   |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Sì | Le chiamate API vengono eseguite tramite [Azure Resource Manager](/azure/azure-resource-manager/index) e HTTPS. |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Sì (solo livello Premium) | Gli endpoint del servizio VNet sono supportati solo per il [livello Premium del bus di servizio](/azure/service-bus-messaging/service-bus-premium-messaging) . |
| Supporto di VNet Injection| No | |
| Isolamento rete e supporto del firewall| Sì (solo livello Premium) |  |
| Supporto del tunneling forzato| No |  |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Supportato tramite [monitoraggio e avvisi di Azure](/azure/service-bus-messaging/service-bus-metrics-azure-monitor). |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | Gestito tramite [Azure Active Directory identità del servizio gestita](/azure/service-bus-messaging/service-bus-managed-service-identity); vedere [autenticazione e autorizzazione del bus di servizio](/azure/service-bus-messaging/service-bus-authentication-and-authorization).|
| Authorization| Sì | Supporta l'autorizzazione tramite il controllo [RBAC](/azure/service-bus-messaging/service-bus-role-based-access-control) (anteprima) e il token SAS; vedere [autenticazione e autorizzazione del bus di servizio](/azure/service-bus-messaging/service-bus-authentication-and-authorization). |


### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Sì | I log delle operazioni sono disponibili. vedere [log di diagnostica del bus di servizio](/azure/service-bus-messaging/service-bus-diagnostic-logs).  |
| Registrazione e controllo del piano dati| No |  |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Supporta il controllo delle versioni del provider di risorse tramite l' [API Azure Resource Manager](/rest/api/resources/).|


## <a name="service-bus-relayazureservice-bus-relayservice-bus-relay-security-attributes"></a>[Inoltro del bus di servizio](/azure/service-bus-relay/service-bus-relay-security-attributes)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia |  N/D | L'inoltro è un socket Web e non rende persistenti i dati. |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet) | Sì | Il servizio richiede TLS. |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| No | USA solo certificati TLS Microsoft.  |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Sì | HTTPS. |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| No |  |
| Isolamento rete e supporto del firewall| No |  |
| Supporto del tunneling forzato| N/D | L'inoltro è il tunnel TLS  |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Tramite SAS. |
| Authorization|  Sì | Tramite SAS. |

### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Yes | Tramite [Azure Resource Manager](/azure/azure-resource-manager/index). |
| Registrazione e controllo del piano dati| Sì | Connessione riuscita/non riuscita ed errori e registrato.  |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Yes | Tramite [Azure Resource Manager](/azure/azure-resource-manager/index).|

## <a name="service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Service Fabric](../../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia | Sì | Il cliente è proprietario del cluster e del set di scalabilità di macchine virtuali in cui viene compilato il cluster. Crittografia dischi di Azure può essere abilitata nel set di scalabilità di macchine virtuali. |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet) | Sì | Il cliente è proprietario del cluster e del set di scalabilità di macchine virtuali in cui viene compilato il cluster. Crittografia dischi di Azure può essere abilitata nel set di scalabilità di macchine virtuali. |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D |  |
| Chiamate API crittografate| Sì | Le chiamate API di Service Fabric vengono eseguite tramite Azure Resource Manager. È necessario un token JSON Web (JWT) valido. |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Sì |  |
| Supporto di VNet Injection| Sì |  |
| Isolamento rete e supporto del firewall| Sì | Uso dei gruppi di sicurezza di rete. |
| Supporto del tunneling forzato| Sì | Sulla rete di Azure è disponibile il tunneling forzato. |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Yes | Uso del supporto di monitoraggio di Azure e del supporto di terze parti. |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | L'autenticazione avviene tramite Azure Active Directory. |
| Authorization| Yes | Gestione delle identità e degli accessi per le chiamate tramite provider di risorse di Service Fabric. Per le chiamate inviate direttamente all'endpoint del cluster sono supportati due ruoli: Utente e Amministratore. Il cliente può mappare le API a entrambi i ruoli. |

### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Yes | Tutte le operazioni del piano di controllo vengono eseguite tramite i processi per il controllo e le approvazioni. |
| Registrazione e controllo del piano dati| N/D | Il cliente è proprietario del cluster.  |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | |

## <a name="sql-databasesql-databasesql-database-security-attributesmd"></a>[Database SQL](../../sql-database/sql-database-security-attributes.md)

Il database SQL include sia il [database singolo](/azure/sql-database/sql-database-single-index) che l' [istanza gestita](/azure/sql-database/sql-database-managed-instance). Le voci seguenti si applicano a entrambe le offerte tranne se diversamente specificato.

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia | Yes | Denominato "crittografia in uso", come descritto nell'articolo [Always Encrypted](/azure/sql-database/sql-database-always-encrypted). La crittografia lato server utilizza [Transparent Data Encryption](/azure/sql-database/transparent-data-encryption-azure-sql).|
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet) | Yes | Uso di HTTPS. |
| Gestione della chiave di crittografia, ad esempio CMK o BYOK| Sì | Sono disponibili la gestione delle chiavi gestita dal servizio e gestita dal cliente. Quest'ultimo viene offerto tramite [Azure Key Vault](/azure/key-vault/index). |
| Crittografia a livello di colonna fornita da servizi dati di Azure| Sì | Tramite [Always Encrypted](/azure/sql-database/sql-database-always-encrypted). |
| Chiamate API crittografate| Yes | Utilizzando HTTPS/SSL. |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Sì | Si applica solo a [database singolo](/azure/sql-database/sql-database-single-index) . |
| Supporto per l'inserimento di reti virtuali di Azure| Sì | Si applica solo all' [istanza gestita](/azure/sql-database/sql-database-managed-instance) . |
| Isolamento rete e supporto firewall| Sì | Firewall a livello di database e di server. L'isolamento rete è solo per l' [istanza gestita](/azure/sql-database/sql-database-managed-instance) . |
| Supporto del tunneling forzato| Sì | [Istanza gestita](/azure/sql-database/sql-database-managed-instance) tramite una VPN [ExpressRoute](/azure/expressroute/index) . |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure, ad esempio Log Analytics o Application Insights| Sì | SecureSphere, la soluzione SIEM da impervertito, è supportata anche tramite l'integrazione di [Hub eventi di Azure](/azure/event-hubs/index) tramite il [controllo SQL](/azure/sql-database/sql-database-auditing). |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Azure Active Directory (Azure AD) |
| Authorization| Sì | Nessuna |

### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di controllo e del piano di gestione| Yes | Sì solo per alcuni eventi |
| Registrazione e controllo del piano dati | Sì | Tramite il [controllo SQL](/azure/sql-database/sql-database-auditing) |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Configurazione-supporto per la gestione, ad esempio il controllo delle versioni della configurazione| No  | Nessuna |

### <a name="additional-security-attributes-for-sql-database"></a>Attributi di sicurezza aggiuntivi per il database SQL

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Prevenzione: valutazione della vulnerabilità | Sì | Vedere [servizio di valutazione della vulnerabilità SQL consente di identificare le vulnerabilità del database](/azure/sql-database/sql-vulnerability-assessment). |
| Prevenzione: individuazione e classificazione dei dati  | Yes | Vedere [database SQL di Azure e SQL data warehouse individuazione dati & classificazione](/azure/sql-database/sql-database-data-discovery-and-classification). |
| Rilevamento: rilevamento minacce | Sì | Vedere [Advanced Threat Protection per il database SQL di Azure](/azure/sql-database/sql-database-threat-detection-overview). |


## <a name="storageazurestoragecommonstorage-security-attributes"></a>[Archiviazione](/azure/storage/common/storage-security-attributes)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia | Sì |  |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet) | Yes | Supporta meccanismi HTTPS/TLS standard.  Gli utenti possono inoltre crittografare i dati prima che vengano trasmessi al servizio. |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| Sì | Vedere [crittografia del servizio di archiviazione uso delle chiavi gestite dal cliente nel Azure Key Vault](/azure/storage/common/storage-service-encryption-customer-managed-keys?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Crittografia a livello di colonna (servizi dati di Azure)| N/D |  |
| Chiamate API crittografate| Sì |  |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Yes |  |
| Supporto di VNet Injection| N/D |  |
| Isolamento rete e supporto del firewall| Sì | |
| Supporto del tunneling forzato| N/D |  |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Yes | Metriche di monitoraggio di Azure disponibili ora, log che avviano l'anteprima |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Azure Active Directory, chiave condivisa e token di accesso condiviso. |
| Authorization| Sì | Supportare l'autorizzazione tramite RBAC, ACL POSIX e token SAS |

### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo | Yes | Log attività Azure Resource Manager |
| Registrazione e controllo del piano dati| Sì | Log di diagnostica del servizio e registrazione di monitoraggio di Azure, avvio dell'anteprima  |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Supportare il controllo delle versioni del provider di risorse tramite API Azure Resource Manager |

## <a name="virtual-machines-and-virtual-machine-scale-sets"></a>Macchine virtuali e set di scalabilità di macchine virtuali

[Macchine virtuali Linux](/azure/virtual-machines/windows/virtual-machines-windows-security-attributes) | [Macchine virtuali Windows](/azure/virtual-machines/windows/virtual-machines-windows-security-attributes) | [set di scalabilità di macchine virtuali](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-security-attributes)


### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia | Yes | Vedere [come crittografare una macchina virtuale Linux in Azure](/azure/virtual-machines/linux/encrypt-disks) e [crittografare i dischi virtuali in una VM Windows](/azure/virtual-machines/windows/encrypt-disks). |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Macchine virtuali di Azure supporta la crittografia [ExpressRoute](/azure/expressroute) e VNet. Vedere [crittografia in transito nelle VM](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| Sì | Chiavi gestite dal cliente è uno scenario di crittografia di Azure supportato. vedere [Panoramica di crittografia di Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Sì | Tramite HTTPS e SSL. |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Sì | |
| Supporto di VNet Injection| Sì | . |
| Isolamento rete e supporto del firewall| Sì |  |
| Supporto del tunneling forzato| Yes | Vedere [configurare il tunneling forzato usando il modello di distribuzione Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Yes | Vedere [monitorare e aggiornare una macchina virtuale Linux in Azure](/azure/virtual-machines/linux/tutorial-monitoring) e [monitorare e aggiornare una macchina virtuale Windows in Azure](/azure/virtual-machines/windows/tutorial-monitoring). |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì |  |
| Authorization| Yes |  |


### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Yes |  |
| Registrazione e controllo del piano dati | No |  |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Yes |  | 


## <a name="vpn-gatewayazurevpn-gatewayvpn-gateway-security-attributes"></a>[Gateway VPN](/azure/vpn-gateway/vpn-gateway-security-attributes)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia | N/D | I dati dei clienti di transito del gateway VPN non archiviano i dati dei clienti |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Il gateway VPN crittografa i pacchetti del cliente tra i gateway VPN di Azure e i dispositivi VPN locali dei clienti (S2S) o i client VPN (P2S). I gateway VPN supportano anche la crittografia da VNet a VNet. |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| No | Le chiavi precondivise specificate dal cliente sono crittografate a riposo; ma non è ancora stato integrato con CMK. |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Yes | Tramite [Azure Resource Manager](/azure/azure-resource-manager/index) e HTTPS  |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| N/D | |
| Supporto di VNet Injection| N/D | . |
| Isolamento rete e supporto del firewall| Sì | I gateway VPN sono istanze di VM dedicate per ogni rete virtuale dei clienti  |
| Supporto del tunneling forzato| Yes |  |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Vedere [log di diagnostica di monitoraggio di Azure/avvisi](/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log) & /[metriche di monitoraggio di Azure](/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric).  |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) per la gestione del servizio e la configurazione del gateway VPN di Azure. |
| Authorization| Sì | Supportare l'autorizzazione tramite [RBAC](/azure/role-based-access-control/overview). |

### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Sì | Azure Resource Manager log attività. |
| Registrazione e controllo del piano dati | Yes | [Log di diagnostica di monitoraggio di Azure](/azure/azure-resource-manager/resource-group-audit) per la registrazione e il controllo della connettività VPN. |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Per le operazioni di gestione, lo stato di una configurazione del gateway VPN di Azure può essere esportato come modello di Azure Resource Manager e con controllo delle versioni nel tempo. | 

