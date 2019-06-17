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
ms.openlocfilehash: 7c1718298c3f7c3fea28fa0b18569085f071696f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66003065"
---
# <a name="security-attributes-for-azure-service-fabric"></a>Attributi di sicurezza per Azure Service Fabric

Questo articolo sono descritti gli attributi di sicurezza integrati di Azure Service Fabric. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia di dati inattivi:<ul><li>Modello di crittografia lato server</li><li>Crittografia lato server con chiavi gestite dal cliente</li><li>Altre funzionalità di crittografia (ad esempio lato client, Always Encrypted e così via)</ul>| Yes | Il cliente è proprietario del cluster e la scalabilità di macchine virtuali impostato su cui viene creato il cluster. Crittografia dischi di Azure può essere abilitata nel set di scalabilità di macchine virtuali. |
| Crittografia in transito:<ul><li>Crittografia ExpressRoute</li><li>Nella crittografia di rete virtuale</li><li>Crittografia da rete virtuale a rete virtuale</ul>| Yes |  |
| Gestione della crittografia chiave (CMK, modalità BYOK e così via)| Yes | Il cliente è proprietario del cluster e la scalabilità di macchine virtuali impostato su cui viene creato il cluster. Crittografia dischi di Azure può essere abilitata nel set di scalabilità di macchine virtuali. |
| Crittografia a livello di colonna (Azure Data Services)| N/D |  |
| Chiamate API crittografate| Yes | Le chiamate API di Service Fabric vengono eseguite tramite Azure Resource Manager. È necessario un token JSON Web (JWT) valido. |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint del servizio| Yes |  |
| Supportare l'inserimento di rete virtuale| Yes |  |
| Isolamento di rete e supporto firewall| Yes | Uso dei gruppi di sicurezza di rete. |
| Supporto di tunneling forzato| Yes | Sulla rete di Azure è disponibile il tunneling forzato. |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Monitoraggio di supporto (analitica di Log, Application insights e così via) di Azure| Yes | Utilizzo di supporto e il supporto di terze parti di monitoraggio di Azure. |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | L'autenticazione avviene tramite Azure Active Directory. |
| Authorization| Yes | Gestione delle identità e degli accessi per le chiamate tramite provider di risorse di Service Fabric. Per le chiamate inviate direttamente all'endpoint del cluster sono supportati due ruoli: Utente e Amministratore. Il cliente può mappare le API a entrambi i ruoli. |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| La registrazione di piano di controllo e la gestione e controllo| Yes | Tutte le operazioni del piano di controllo vengono eseguite tramite i processi per il controllo e le approvazioni. |
| Controllo e registrazione di piano dati| N/D | Il cliente è proprietario del cluster.  |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di Gestione configurazione (controllo delle versioni di configurazione e così via).| Yes | |