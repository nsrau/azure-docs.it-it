---
author: msmbaldwin
ms.service: backup
ms.topic: include
ms.date: 01/31/2019
ms.author: mbaldwin
ms.openlocfilehash: 5a4cc3ea822c5613fc7a50b8e370d6846b683721
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513296"
---
## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Sì | Uso della crittografia del servizio di archiviazione per gli account di archiviazione. |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Crittografia nella rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| No  | Uso di HTTPS. |
| Gestione delle chiavi di crittografia (CMK, BYOK e così via)| No  |  |
| Crittografia a livello di colonna (Servizi dati di Azure)| No  |  |
| Chiamate API crittografate| Sì |  |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint di servizio| No  |  |
| Supporto aggiunta rete virtuale| No  |  |
| Supporto isolamento rete/protezione tramite firewall| Sì | Il tunneling forzato è supportato per il backup delle macchine virtuali. Il tunneling forzato non è supportato per i carichi di lavoro in esecuzione all'interno di macchine virtuali. |
| Supporto tunneling forzato | No  |  |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto monitoraggio di Azure (Log Analytics, Application Insights e così via)| Sì | Log Analytics è supportato tramite i log di diagnostica. Per altre informazioni, vedere Monitor Azure Backup protected workloads using Log Analytics (Monitorare i carichi di lavoro protetti di Backup di Azure usando Log Analytics) (https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |

## <a name="iam-support"></a>Supporto per la gestione delle identità e degli accessi

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Gestione degli accessi - Autenticazione| Sì | L'autenticazione avviene tramite Azure Active Directory. |
| Gestione degli accessi - Autorizzazione| Sì | Vengono usati ruoli RBAC creati dai clienti o predefiniti. Per altre informazioni, vedere Usare il controllo degli accessi in base al ruolo per gestire i punti di ripristino di Backup di Azure (https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault). |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Piano di gestione/controllo - Registrazione e controllo| Sì | Tutte le azioni attivate dai clienti dal portale di Azure vengono registrate nei log attività. |
| Piano dati - Registrazione e controllo| No  | Il piano dati di Backup di Azure non è raggiungibile direttamente.  |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto gestione della configurazione (controllo delle versioni di configurazione e così via)| Sì|  |