---
title: Attributi di sicurezza comuni per Azure Service Fabric
description: Un elenco di controllo degli attributi di sicurezza comuni per la valutazione di Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: f6db40a35c289fa0870ac4c9e5e55b773c84f4f4
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679228"
---
# <a name="common-security-attributes-for-azure-service-fabric"></a>Attributi di sicurezza comuni per Azure Service Fabric

La sicurezza è integrata in ogni aspetto di un servizio di Azure. Questo articolo sono descritti gli attributi di sicurezza comuni integrati in Azure Service Fabric. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Sì | Il cliente è proprietario del cluster e la scalabilità di macchine virtuali impostato su cui viene creato il cluster. Crittografia dischi di Azure può essere abilitata nel set di scalabilità di macchine virtuali. |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Crittografia nella rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Sì |  |
| Gestione delle chiavi di crittografia (CMK, BYOK e così via)| Sì | Il cliente è proprietario del cluster e la scalabilità di macchine virtuali impostato su cui viene creato il cluster. Crittografia dischi di Azure può essere abilitata nel set di scalabilità di macchine virtuali. |
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
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Sì | Utilizzo di supporto e il supporto di terze parti di monitoraggio di Azure. |

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
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Sì | Per la configurazione del servizio viene specificata la versione ed eseguita la distribuzione tramite Azure Deploy. Le versioni del codice (applicazione e runtime) sono definite con Azure Build.