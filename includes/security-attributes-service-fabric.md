---
author: msmbaldwin
ms.service: service-fabric
ms.topic: include
ms.date: 01/31/2019
ms.author: mbaldwin
ms.openlocfilehash: a20311a0285bf8fda5498241a60b85093039ad19
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513256"
---
## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Sì | Il cliente è proprietario del cluster e del set di scalabilità di macchine virtuali su cui è basato il cluster. Nel set di scalabilità di macchine virtuali può essere abilitato il servizio Crittografia dischi di Azure. |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Crittografia nella rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Sì |  |
| Gestione delle chiavi di crittografia (CMK, BYOK e così via)| Sì | Il cliente è proprietario del cluster e del set di scalabilità di macchine virtuali su cui è basato il cluster. Nel set di scalabilità di macchine virtuali può essere abilitato il servizio Crittografia dischi di Azure. |
| Crittografia a livello di colonna (Servizi dati di Azure)| N/D |  |
| Chiamate API crittografate| Sì | Le chiamate API di Service Fabric vengono eseguite tramite Azure Resource Manager. È necessario un token JSON Web (JWT) valido. |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint di servizio| Sì |  |
| Supporto aggiunta rete virtuale| Sì |  |
| Supporto isolamento rete/protezione tramite firewall| Sì | Uso dei gruppi di sicurezza di rete. |
| Supporto tunneling forzato | Sì | Sulla rete di Azure è disponibile il tunneling forzato. |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto monitoraggio di Azure (Log Analytics, Application Insights e così via)| Sì | Uso delle funzionalità di monitoraggio di Azure e di terze parti. |

## <a name="iam-support"></a>Supporto per la gestione delle identità e degli accessi

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Gestione degli accessi - Autenticazione| Sì | L'autenticazione avviene tramite Azure Active Directory. |
| Gestione degli accessi - Autorizzazione| Sì | Gestione delle identità e degli accessi per le chiamate tramite provider di risorse di Service Fabric. Per le chiamate inviate direttamente all'endpoint del cluster sono supportati due ruoli: Utente e Amministratore. Il cliente può mappare le API a entrambi i ruoli. |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Piano di gestione/controllo - Registrazione e controllo| Sì | Tutte le operazioni del piano di controllo vengono eseguite tramite i processi per il controllo e le approvazioni. |
| Piano dati - Registrazione e controllo| N/D | Il cliente è proprietario del cluster.  |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto gestione della configurazione (controllo delle versioni di configurazione e così via)| Sì | Per la configurazione del servizio viene specificata la versione ed eseguita la distribuzione tramite Azure Deploy. Le versioni del codice (applicazione e runtime) sono definite con Azure Build.
 |