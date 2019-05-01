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
ms.openlocfilehash: 94681351758f34cc53c425f3207660bdb09f6494
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717778"
---
# <a name="common-security-attributes-for-azure-services"></a>Attributi di sicurezza comuni per servizi di Azure

La sicurezza è integrata in ogni aspetto di un servizio di Azure. Questo articolo consente di raccogliere gli attributi di sicurezza comuni per servizi di Azure selezionati. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Backup di Azure](../backup/backup-security-attributes.md)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Sì | Uso della crittografia del servizio di archiviazione per gli account di archiviazione. |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Nella crittografia di rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| No  | Uso di HTTPS. |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| No  |  |
| Crittografia a livello di colonna (Azure Data Services)| No  |  |
| Chiamate API crittografate| Sì |  |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| No  |  |
| Supportare l'inserimento di rete virtuale| No  |  |
| Isolamento di rete e supporto firewall| Sì | Il tunneling forzato è supportato per il backup delle macchine virtuali. Il tunneling forzato non è supportato per i carichi di lavoro in esecuzione all'interno di macchine virtuali. |
| Supporto di tunneling forzato| No  |  |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Sì | Log Analytics è supportato tramite i log di diagnostica. Per altre informazioni, vedere Monitor Azure Backup protected workloads using Log Analytics (Monitorare i carichi di lavoro protetti di Backup di Azure usando Log Analytics) (https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | L'autenticazione avviene tramite Azure Active Directory. |
| Authorization| Sì | Vengono usati ruoli RBAC creati dai clienti o predefiniti. Vedere il controllo di accesso degli accessi in base a gestire i punti di ripristino Backup di Azure (/ azure/backup/backup-rbac--insieme di credenziali rs) per altre informazioni. |


### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Sì | Tutte le azioni attivate dai clienti dal portale di Azure vengono registrate nei log attività. |
| Controllo e registrazione di piano dati| No  | Il piano dati di Backup di Azure non è raggiungibile direttamente.  |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Sì|  |

## <a name="azure-key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Insieme di credenziali chiave Azure](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Sì | Vengono crittografati tutti gli oggetti. |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Nella crittografia di rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Sì | Tutte le comunicazioni vengono effettuate tramite chiamate API crittografate |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| Sì | Il cliente controlla tutte le chiavi nel proprio insieme di credenziali delle chiavi. Quando vengono specificate le chiavi module (HSM) supportata di protezione hardware, un modulo di protezione hardware di FIPS livello 2 consente di proteggere la chiave, un certificato o un segreto. |
| Crittografia a livello di colonna (Azure Data Services)| N/D |  |
| Chiamate API crittografate| Sì | Viene usato HTTPS. |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| Sì | Tramite gli endpoint di servizio di rete virtuale (VNet). |
| Supportare l'inserimento di rete virtuale| No  |  |
| Isolamento di rete e supporto firewall| Sì | Usando le regole del firewall della rete virtuale. |
| Supporto di tunneling forzato| No  |  |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Sì | Viene usato Log Analytics. |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | L'autenticazione avviene tramite Azure Active Directory. |
| Authorization| Sì | Vengono usati i criteri di accesso dell'insieme di credenziali delle chiavi. |


### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Piano di gestione/controllo - Registrazione e controllo| Sì | Viene usato Log Analytics. |
| Controllo e registrazione di piano dati| Sì | Viene usato Log Analytics. |

### <a name="access-controls"></a>Controlli di accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Piano di gestione/controllo - Controlli di accesso | Sì | Controllo degli accessi in base al ruolo di Azure Resource Manager |
| Piano dati - Controlli di accesso (a ogni livello di servizio) | Sì | Criteri di accesso insieme di credenziali delle chiavi |

## <a name="azure-service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Sì | Il cliente è proprietario del cluster e del set di scalabilità di macchine virtuali su cui è basato il cluster. Crittografia dischi di Azure può essere abilitata nel set di scalabilità di macchine virtuali. |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Nella crittografia di rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Sì |  |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| Sì | Il cliente è proprietario del cluster e del set di scalabilità di macchine virtuali su cui è basato il cluster. Crittografia dischi di Azure può essere abilitata nel set di scalabilità di macchine virtuali. |
| Crittografia a livello di colonna (Azure Data Services)| N/D |  |
| Chiamate API crittografate| Sì | Le chiamate API di Service Fabric vengono eseguite tramite Azure Resource Manager. È necessario un token JSON Web (JWT) valido. |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| Sì |  |
| Supportare l'inserimento di rete virtuale| Sì |  |
| Isolamento di rete e supporto firewall| Sì | Uso dei gruppi di sicurezza di rete. |
| Supporto di tunneling forzato| Sì | Sulla rete di Azure è disponibile il tunneling forzato. |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Sì | Utilizzo di supporto e il supporto di terze parti di monitoraggio di Azure. |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | L'autenticazione avviene tramite Azure Active Directory. |
| Authorization| Sì | Gestione delle identità e degli accessi per le chiamate tramite provider di risorse di Service Fabric. Per le chiamate inviate direttamente all'endpoint del cluster sono supportati due ruoli: Utente e Amministratore. Il cliente può mappare le API a entrambi i ruoli. |


### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Sì | Tutte le operazioni del piano di controllo vengono eseguite tramite i processi per il controllo e le approvazioni. |
| Controllo e registrazione di piano dati| N/D | Il cliente è proprietario del cluster.  |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Sì | Per la configurazione del servizio viene specificata la versione ed eseguita la distribuzione tramite Azure Deploy. Le versioni del codice (applicazione e runtime) sono definite con Azure Build.
 |

## <a name="azure-storage"></a>Archiviazione di Azure

### <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Sì |  |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Nella crittografia di rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Sì | Supporto di meccanismi standard HTTPS/TLS.  Gli utenti possono anche crittografare i dati prima di essere trasmesso al servizio. |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| Sì | Visualizzare [crittografia del servizio di archiviazione di Azure con chiavi gestite dal cliente in Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
| Crittografia a livello di colonna (Azure Data Services)| N/D |  |
| Chiamate API crittografate| Sì |  |

### <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| Sì |  |
| Supportare l'inserimento di rete virtuale| N/D |  |
| Isolamento di rete e supporto firewall| Sì | |
| Supporto di tunneling forzato| N/D |  |

### <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Sì | Metriche di monitoraggio di Azure disponibili a questo punto, i log di anteprima inizia |

### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Azure Active Directory, chiave condivisa, il token di accesso condiviso. |
| Authorization| Sì | Autorizzazione di supporto tramite RBAC, POSIX ACL e i token di firma di accesso condiviso |


### <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo | Sì | Log attività di Azure Resource Manager |
| Controllo e registrazione di piano dati| Sì | I log di diagnostica del servizio e anteprima di partenza la registrazione di monitoraggio di Azure  |

### <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Sì | Supporta il controllo delle versioni del Provider di risorse tramite le API di Azure Resource Manager |