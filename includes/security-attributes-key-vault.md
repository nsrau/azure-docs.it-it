---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 01/31/2019
ms.author: mbaldwin
ms.openlocfilehash: aba09012bf2e9d2741f598280add8b599a6f6d1a
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55807192"
---
## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Sì | Vengono crittografati tutti gli oggetti. |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Crittografia nella rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Sì | Tutte le comunicazioni vengono effettuate tramite chiamate API crittografate |
| Gestione delle chiavi di crittografia (CMK, BYOK e così via)| Sì | Il cliente controlla tutte le chiavi nel proprio insieme di credenziali delle chiavi. Se vengono specificate chiavi basate sul modulo di protezione hardware (HSM), la chiave, il certificato o il segreto viene protetto da un modulo di protezione hardware FIPS di livello 2. |
| Crittografia a livello di colonna (Servizi dati di Azure)| N/D |  |
| Chiamate API crittografate| Sì | Viene usato HTTPS. |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint di servizio| Sì | Vengono usati endpoint di servizio di rete virtuale. |
| Supporto aggiunta rete virtuale| No  |  |
| Supporto isolamento rete/protezione tramite firewall| Sì | Vengono usate le regole del firewall della rete virtuale. |
| Supporto tunneling forzato | No  |  |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto monitoraggio di Azure (Log Analytics, Application Insights e così via)| Sì | Viene usato Log Analytics. |

## <a name="iam-support"></a>Supporto per la gestione delle identità e degli accessi

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Gestione degli accessi - Autenticazione| Sì | L'autenticazione avviene tramite Azure Active Directory. |
| Gestione degli accessi - Autorizzazione| Sì | Vengono usati i criteri di accesso dell'insieme di credenziali delle chiavi. |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Piano di gestione/controllo - Registrazione e controllo| Sì | Viene usato Log Analytics. |
| Piano dati - Registrazione e controllo| Sì | Viene usato Log Analytics. |

## <a name="access-controls"></a>Controlli di accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Piano di gestione/controllo - Controlli di accesso | Sì | Controllo degli accessi in base al ruolo di Azure Resource Manager |
| Piano dati - Controlli di accesso (a ogni livello di servizio) | Sì | Criteri di accesso insieme di credenziali delle chiavi |
