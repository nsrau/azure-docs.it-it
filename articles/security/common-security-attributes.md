---
title: Attributi di sicurezza per i servizi di Azure
description: Un elenco di controllo degli attributi di sicurezza comuni per la valutazione di Azure Service Fabric
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 64accb70561d4c0282b3ee45935d955dba1c67c4
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474545"
---
# <a name="security-attributes-for-azure-services"></a>Attributi di sicurezza per i servizi di Azure

Questo articolo consente di raccogliere gli attributi di sicurezza comuni per servizi di Azure selezionati. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="azure-api-managementapi-managementapi-management-security-attributesmd"></a>[Gestione API di Azure](../api-management/api-management-security-attributes.md)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Sì (solo crittografia sul lato servizio) | I dati sensibili, ad esempio i certificati, chiavi e valori di nome segreto sono crittografati con gestite dal servizio, per le chiavi di istanza di servizio. |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Nella crittografia di rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Yes | [Express Route](../expressroute/index.yml) e la crittografia di rete virtuale è disponibile dal [la rete di Azure](../virtual-network/index.yml). |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| No | Tutte le chiavi di crittografia sono per ogni istanza del servizio e servizio gestito. |
| Crittografia a livello di colonna (Azure Data Services)| N/D | |
| Chiamate API crittografate| Yes | Gestione piano chiamate vengono effettuate tramite [Azure Resource Manager](../azure-resource-manager/index.yml) su TLS. È necessario un token JSON Web (JWT) valido.  Le chiamate di piano dati è possibile proteggere con TLS e uno dei meccanismi di autenticazione supportati (ad esempio, il certificato client o JWT).
 |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| No | |
| Supportare l'inserimento di rete virtuale| Yes | |
| Isolamento di rete e supporto firewall| Yes | Uso di gruppi di sicurezza di rete (NSG) e il Gateway applicazione di Azure (o altro dispositivo di software) rispettivamente. |
| Supporto di tunneling forzato| Yes | Sulla rete di Azure è disponibile il tunneling forzato. |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Yes | |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | |
| Authorization| Yes | |


### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Yes | [Azure log attività di monitoraggio](../azure-monitor/platform/activity-logs-overview.md) |
| Controllo e registrazione di piano dati| Yes | [Log di diagnostica Azure Monitor](../azure-monitor/platform/diagnostic-logs-overview.md) ed eventualmente [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Yes | Uso di [DevOps Resource Kit di gestione API di Azure](https://aka.ms/apimdevops) |


## <a name="azure-app-serviceapp-serviceapp-service-security-attributesmd"></a>[Servizio app di Azure](../app-service/app-service-security-attributes.md)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei dati inattivi (ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia) | Yes | Contenuto del file del sito Web verrà archiviato in archiviazione di Azure, che il contenuto quando sono inattivi vengono crittografati automaticamente. Visualizzare [crittografia di archiviazione di Azure per dati inattivi](../storage/common/storage-service-encryption.md).<br><br>I segreti forniti dal cliente vengono crittografati a riposo. I segreti vengono crittografati a riposo mentre archiviati nei database di configurazione del servizio App.<br><br>I dischi collegati localmente, facoltativamente, utilizzabile come risorsa di archiviazione temporanea per siti Web (D:\local e % TMP %). I dischi collegati in locale non vengono crittografati a riposo. |
| Crittografia in transito (ad esempio la crittografia di ExpressRoute, nella crittografia di rete virtuale e la crittografia di rete)| Yes | I clienti possono configurare siti web per richiedere e uso di HTTPS per il traffico in ingresso. Vedere il blog post [come rendere un HTTPS a un servizio App di Azure solo](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| Yes | I clienti possono scegliere di archiviare i segreti dell'applicazione in Azure Key Vault e recuperarli in fase di esecuzione. Visualizzare [fa riferimento a usare Key Vault per servizio App e funzioni di Azure (anteprima)](../app-service/app-service-key-vault-references.md).|
| Crittografia a livello di colonna (Azure Data Services)| N/D | |
| Chiamate API crittografate| Yes | Le chiamate di gestione per configurare il servizio App hanno luogo tramite [Azure Resource Manager](../azure-resource-manager/index.yml) chiamate tramite HTTPS. |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| Yes | Attualmente disponibile in anteprima per il servizio App. Visualizzare [restrizioni di accesso di servizio App di Azure](../app-service/app-service-ip-restrictions.md). |
| Supportare l'inserimento di rete virtuale| Yes | Gli ambienti del servizio App sono implementazioni private del servizio App dedicato per un singolo cliente inserito nella rete virtuale del cliente. Visualizzare [introduzione per gli ambienti del servizio App](../app-service/environment/intro.md). |
| Supporto per l'isolamento della rete e Firewalling| Yes | Per la variazione di multi-tenant pubblica del servizio App, i clienti possono configurare ACL (restrizioni IP) per bloccare il traffico in ingresso consentito di rete.  Visualizzare [restrizioni di accesso di servizio App di Azure](../app-service/app-service-ip-restrictions.md).  Ambienti del servizio App vengono distribuiti direttamente nelle reti virtuali e pertanto possono essere protetti con Nsg. |
| Supporto di tunneling forzato| Yes | Ambienti del servizio App possono essere distribuiti in una rete virtuale del cliente in cui è configurato il tunneling forzato. I clienti devono seguire le istruzioni disponibili nel [configurare l'ambiente del servizio App con tunneling forzato](../app-service/environment/forced-tunnel-support.md). |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Yes | Servizio App si integra con Application Insights per le lingue che supportano Application Insights (completa di .NET Framework, .NET Core, Java e Node. js).  Visualizzare [sulle prestazioni di monitoraggio del servizio App Azure](../azure-monitor/app/azure-web-apps.md). Servizio App invia anche le metriche dell'applicazione in Monitoraggio di Azure. Visualizzare [monitorare le app nel servizio App di Azure](../app-service/web-sites-monitor.md). |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | I clienti possono creare applicazioni nel servizio App che si integrano automaticamente con [Azure Active Directory (Azure AD)](../active-directory/index.yml) , nonché altri provider di identità compatibili OAuth; vedere [autenticazione e autorizzazione nel Servizio App di Azure](../app-service/overview-authentication-authorization.md). Per l'accesso a gestione agli asset di servizio App, tutti gli accessi sono controllato da una combinazione di entità di Azure AD autenticato e i ruoli RBAC di Azure Resource Manager. |
| Authorization| Yes | Per l'accesso a gestione agli asset di servizio App, tutti gli accessi sono controllato da una combinazione di entità di Azure AD autenticato e i ruoli RBAC di Azure Resource Manager.  |


### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Yes | Tutte le operazioni di gestione eseguite sugli oggetti di servizio App hanno luogo tramite [Azure Resource Manager](../azure-resource-manager/index.yml). I log cronologici di queste operazioni sono disponibili nel portale e tramite l'interfaccia CLI; visualizzare [operazioni di provider di risorse di Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftweb) e [az monitor log attività](/cli/azure/monitor/activity-log). |
| Controllo e registrazione di piano dati | No | Il piano dati per il servizio App è una condivisione file remota che contiene un sito web distribuiti dal cliente contenuto.  Non vi è alcun controllo di condivisione file remota. |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Yes | Per le operazioni di gestione, lo stato di una configurazione del servizio App può essere esportato come un modello di Azure Resource Manager e con controllo delle versioni nel corso del tempo. Per le operazioni di runtime, i clienti possono gestire più versioni diverse in tempo reale di un'applicazione usando la funzionalità di slot di distribuzione di servizio App. | 


## <a name="azure-resource-managerazure-resource-managerazure-resource-manager-security-attributesmd"></a>[Azure Resource Manager](../azure-resource-manager/azure-resource-manager-security-attributes.md)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Yes |  |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Nella crittografia di rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Yes | HTTPS/TLS. |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| N/D | Azure Resource Manager non archivia alcun contenuto del cliente, solo i dati di controllo. |
| Crittografia a livello di colonna (Azure Data Services)| Yes | |
| Chiamate API crittografate| Yes | |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| No | |
| Supportare l'inserimento di rete virtuale| Yes | |
| Isolamento di rete e supporto firewall| No |  |
| Supporto di tunneling forzato| No |  |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| No | |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | [Azure Active Directory](/azure/active-directory) basato.|
| Authorization| Yes | |


### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Yes | Log attività di esporre tutte le operazioni (PUT, POST, DELETE) eseguite sulle risorse; di scrittura visualizzare [visualizzare i log attività per controllare le azioni sulle risorse](../azure-resource-manager/resource-group-audit.md). |
| Controllo e registrazione di piano dati| N/D | |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Yes |  |


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Backup di Azure](../backup/backup-security-attributes.md)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Yes | Uso della crittografia del servizio di archiviazione per gli account di archiviazione. |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Nella crittografia di rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| No | Uso di HTTPS. |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| No |  |
| Crittografia a livello di colonna (Azure Data Services)| No |  |
| Chiamate API crittografate| Yes |  |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| No |  |
| Supportare l'inserimento di rete virtuale| No |  |
| Isolamento di rete e supporto firewall| Yes | Il tunneling forzato è supportato per il backup delle macchine virtuali. Il tunneling forzato non è supportato per i carichi di lavoro in esecuzione all'interno di macchine virtuali. |
| Supporto di tunneling forzato| No |  |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Yes | Log Analytics è supportato tramite i log di diagnostica. Per altre informazioni, vedere Monitor Azure Backup protected workloads using Log Analytics (Monitorare i carichi di lavoro protetti di Backup di Azure usando Log Analytics) (https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | L'autenticazione avviene tramite Azure Active Directory. |
| Authorization| Yes | Vengono usati ruoli RBAC creati dai clienti o predefiniti. Vedere il controllo di accesso degli accessi in base a gestire i punti di ripristino Backup di Azure (/ azure/backup/backup-rbac--insieme di credenziali rs) per altre informazioni. |


### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Yes | Tutte le azioni attivate dai clienti dal portale di Azure vengono registrate nei log attività. |
| Controllo e registrazione di piano dati| No | Il piano dati di Backup di Azure non è raggiungibile direttamente.  |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Yes|  |


## <a name="azure-key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Insieme di credenziali chiave Azure](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Yes | Vengono crittografati tutti gli oggetti. |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Nella crittografia di rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Yes | Tutte le comunicazioni vengono effettuate tramite chiamate API crittografate |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| Yes | Il cliente controlla tutte le chiavi nel proprio insieme di credenziali delle chiavi. Quando vengono specificate le chiavi module (HSM) supportata di protezione hardware, un modulo di protezione hardware di FIPS livello 2 consente di proteggere la chiave, un certificato o un segreto. |
| Crittografia a livello di colonna (Azure Data Services)| N/D |  |
| Chiamate API crittografate| Yes | Uso di HTTPS. |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| Yes | Tramite gli endpoint di servizio di rete virtuale (VNet). |
| Supportare l'inserimento di rete virtuale| No |  |
| Isolamento di rete e supporto firewall| Yes | Usando le regole del firewall della rete virtuale. |
| Supporto di tunneling forzato| No |  |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Yes | Viene usato Log Analytics. |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | L'autenticazione avviene tramite Azure Active Directory. |
| Authorization| Yes | Vengono usati i criteri di accesso dell'insieme di credenziali delle chiavi. |


### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Piano di gestione/controllo - Registrazione e controllo| Yes | Viene usato Log Analytics. |
| Controllo e registrazione di piano dati| Yes | Viene usato Log Analytics. |

### <a name="access-controls"></a>Controlli di accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Piano di gestione/controllo - Controlli di accesso | Yes | Controllo degli accessi in base al ruolo di Azure Resource Manager |
| Piano dati - Controlli di accesso (a ogni livello di servizio) | Yes | Criteri di accesso insieme di credenziali delle chiavi |

## <a name="azure-service-bus-messagingservice-bus-messagingservice-bus-messaging-security-attributesmd"></a>[Azure messaggistica del Bus di servizio](../service-bus-messaging/service-bus-messaging-security-attributes.md)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>|  Sì per il server-side encryption inattivi per impostazione predefinita. | Chiavi gestite dal cliente e la chiave BYOK non sono ancora supportati. Crittografia lato client è responsabilità del cliente |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Nella crittografia di rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Yes | Supporta il meccanismo HTTPS/TLS standard. |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| No |   |
| Crittografia a livello di colonna (Azure Data Services)| N/D | |
| Chiamate API crittografate| Yes | Le chiamate API vengono effettuate tramite [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| Sì (solo livello Premium) | Endpoint del servizio rete virtuale sono supportate per [livello Premium del Bus di servizio](../service-bus-messaging/service-bus-premium-messaging.md) solo. |
| Supportare l'inserimento di rete virtuale| No | |
| Isolamento di rete e supporto firewall| Sì (solo livello Premium) |  |
| Supporto di tunneling forzato| No |  |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Yes | Supportato tramite [avvisi e monitoraggio di Azure](../service-bus-messaging/service-bus-metrics-azure-monitor.md). |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | Gestito tramite [Azure Active Directory identità del servizio gestito](../service-bus-messaging/service-bus-managed-service-identity.md); vedere [autenticazione e autorizzazione Service Bus](../service-bus-messaging/service-bus-authentication-and-authorization.md).|
| Authorization| Yes | Supporta l'autorizzazione tramite [RBAC](../service-bus-messaging/service-bus-role-based-access-control.md) (anteprima) e il token di firma di accesso condiviso, vedere [autenticazione e autorizzazione Service Bus](../service-bus-messaging/service-bus-authentication-and-authorization.md). |

### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Yes | I log operazioni sono disponibili; visualizzare [log di diagnostica del Bus di servizio](../service-bus-messaging/service-bus-diagnostic-logs.md).  |
| Controllo e registrazione di piano dati| No |  |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Yes | Supporta il controllo delle versioni del provider di risorse tramite il [API Azure Resource Manager](/rest/api/resources/).|


## <a name="azure-service-bus-relayservice-bus-relayservice-bus-relay-security-attributesmd"></a>[Inoltro del Bus di servizio di Azure](../service-bus-relay/service-bus-relay-security-attributes.md)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>|  N/D | Inoltro è un web socket e non rende persistenti i dati. |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Nella crittografia di rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Yes | Il servizio richiede TLS. |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| No | Utilizza solo certificati TLS Microsoft.  |
| Crittografia a livello di colonna (Azure Data Services)| N/D | |
| Chiamate API crittografate| Yes | HTTPS. |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| No |  |
| Isolamento di rete e supporto firewall| No |  |
| Supporto di tunneling forzato| N/D | Endpoint di inoltro è il tunnel TLS  |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Yes | |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | Via SAS. |
| Authorization|  Yes | Via SAS. |


### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Yes | Attraverso [Azure Resource Manager](../azure-resource-manager/index.yml). |
| Controllo e registrazione di piano dati| Yes | Esito positivo di connessione / errore e gli errori e registrati.  |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Yes | Attraverso [Azure Resource Manager](../azure-resource-manager/index.yml).|


## <a name="azure-service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Yes | Il cliente è proprietario del cluster e del set di scalabilità di macchine virtuali su cui è basato il cluster. Crittografia dischi di Azure può essere abilitata nel set di scalabilità di macchine virtuali. |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Nella crittografia di rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Yes |  |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| Yes | Il cliente è proprietario del cluster e del set di scalabilità di macchine virtuali su cui è basato il cluster. Crittografia dischi di Azure può essere abilitata nel set di scalabilità di macchine virtuali. |
| Crittografia a livello di colonna (Azure Data Services)| N/D |  |
| Chiamate API crittografate| Yes | Le chiamate API di Service Fabric vengono eseguite tramite Azure Resource Manager. È necessario un token JSON Web (JWT) valido. |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| Yes |  |
| Supportare l'inserimento di rete virtuale| Yes |  |
| Isolamento di rete e supporto firewall| Yes | Uso dei gruppi di sicurezza di rete. |
| Supporto di tunneling forzato| Yes | Sulla rete di Azure è disponibile il tunneling forzato. |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Yes | Utilizzo di supporto e il supporto di terze parti di monitoraggio di Azure. |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | L'autenticazione avviene tramite Azure Active Directory. |
| Authorization| Yes | Gestione delle identità e degli accessi per le chiamate tramite provider di risorse di Service Fabric. Per le chiamate inviate direttamente all'endpoint del cluster sono supportati due ruoli: Utente e Amministratore. Il cliente può mappare le API a entrambi i ruoli. |

### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Yes | Tutte le operazioni del piano di controllo vengono eseguite tramite i processi per il controllo e le approvazioni. |
| Controllo e registrazione di piano dati| N/D | Il cliente è proprietario del cluster.  |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Yes | Per la configurazione del servizio viene specificata la versione ed eseguita la distribuzione tramite Azure Deploy. Le versioni del codice (applicazione e runtime) sono definite con Azure Build.
 |

## <a name="azure-sql-databasesql-databasesql-database-security-attributesmd"></a>[Database SQL di Azure](../sql-database/sql-database-security-attributes.md)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Yes | Detto "crittografia in uso", come descritto nell'articolo [Always Encrypted](../sql-database/sql-database-always-encrypted.md). Crittografia lato servizio viene utilizzato [crittografia dati trasparente](../sql-database/transparent-data-encryption-azure-sql.md) (TDE).|
| Crittografia in transito:<ul><li>Crittografia di ExpressRoute</li><li>Nella crittografia di rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Yes | Uso di HTTPS. |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| Yes | Gestione delle chiavi gestite dal servizio sia gestita dal cliente vengono offerti (quest'ultimo attraverso [Azure Key Vault](../key-vault/index.yml). |
| Crittografia a livello di colonna (Azure Data Services)| Yes | Attraverso [Always Encrypted](../sql-database/sql-database-always-encrypted.md). |
| Chiamate API crittografate| Yes | Tramite HTTPS/SSL. |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| Yes | Si applica a [singolo database](../sql-database/sql-database-single-index.yml) solo. |
| Supportare l'inserimento di rete virtuale| Yes | Si applica a [istanza gestita](../sql-database/sql-database-managed-instance.md) solo. |
| Isolamento di rete e supporto firewall| Yes | In entrambi database - e a livello di server; del firewall isolamento di rete [istanza gestita](../sql-database/sql-database-managed-instance.md) solo |
| Supporto di tunneling forzato| Yes | [istanza gestita](../sql-database/sql-database-managed-instance.md) tramite [Azure ExpressRoute](../expressroute/index.yml) VPN |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Yes | La soluzione SIEM di terze parti cybersicurezza di Imperva (SecureSphere) è supportata anche, tramite [hub eventi di Azure](../event-hubs/index.yml) integrazione tramite [SQL audit](../sql-database/sql-database-auditing.md). |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | Azure Active Directory. |
| Authorization| Yes |  |


### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Yes | Sì per solo alcuni eventi. |
| Controllo e registrazione di piano dati | Yes | Tramite [SQL audit](../sql-database/sql-database-auditing.md). |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| No  | | 

### <a name="additional-security-attributes-for-sql-database"></a>Attributi di sicurezza aggiuntive per il Database SQL

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Preventive: valutazione della vulnerabilità | Yes | Visualizzare [valutazione della vulnerabilità SQL service consente di identificare le vulnerabilità dei database](../sql-database/sql-vulnerability-assessment.md). |
| Preventive: individuazione dati e classificazione  | Yes | Visualizzare [Database SQL di Azure e SQL Data Warehouse individuazione dati e classificazione](../sql-database/sql-database-data-discovery-and-classification.md). |
| Il rilevamento: rilevamento delle minacce | Yes | Visualizzare [Advanced Threat Protection per il Database SQL di Azure](../sql-database/sql-database-threat-detection-overview.md). |

## <a name="azure-storagestoragecommonstorage-security-attributesmd"></a>[Archiviazione di Azure](../storage/common/storage-security-attributes.md)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Yes |  |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Nella crittografia di rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Yes | Supporto di meccanismi standard HTTPS/TLS.  Gli utenti possono anche crittografare i dati prima di essere trasmesso al servizio. |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| Yes | Visualizzare [crittografia del servizio di archiviazione di Azure con chiavi gestite dal cliente in Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
| Crittografia a livello di colonna (Azure Data Services)| N/D |  |
| Chiamate API crittografate| Yes |  |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| Yes |  |
| Supportare l'inserimento di rete virtuale| N/D |  |
| Isolamento di rete e supporto firewall| Yes | |
| Supporto di tunneling forzato| N/D |  |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Yes | Metriche di monitoraggio di Azure disponibili a questo punto, i log di anteprima inizia |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | Azure Active Directory, chiave condivisa, il token di accesso condiviso. |
| Authorization| Yes | Autorizzazione di supporto tramite RBAC, POSIX ACL e i token di firma di accesso condiviso |


### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo | Yes | Log attività di Azure Resource Manager |
| Controllo e registrazione di piano dati| Yes | I log di diagnostica del servizio e anteprima di partenza la registrazione di monitoraggio di Azure  |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Yes | Supporta il controllo delle versioni del Provider di risorse tramite le API di Azure Resource Manager |