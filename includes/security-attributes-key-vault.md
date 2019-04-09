---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 03/15/2019
ms.author: mbaldwin
ms.openlocfilehash: 0a52b7a5234b292fa3f8c3e19fe7f62f989d639c
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007235"
---
## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Sì | Vengono crittografati tutti gli oggetti. |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Crittografia nella rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Sì | Tutte le comunicazioni vengono effettuate tramite chiamate API crittografate |
| Gestione delle chiavi di crittografia (CMK, BYOK e così via)| Sì | Il cliente controlla tutte le chiavi nel proprio insieme di credenziali delle chiavi. Quando vengono specificate le chiavi module (HSM) supportata di protezione hardware, un modulo di protezione hardware di FIPS livello 2 consente di proteggere la chiave, un certificato o un segreto. |
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
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Sì | Viene usato Log Analytics. |

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
