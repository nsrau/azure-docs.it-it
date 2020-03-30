---
title: Controlli di sicurezza per Azure Service FabricSecurity controls for Azure Service Fabric
description: Informazioni sui controlli di sicurezza per Azure Service Fabric.Learn about security controls for Azure Service Fabric. Include un elenco di controllo dei controlli di sicurezza incorporati.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a8bb49e20ec5812a4882966c6918cf2bd59f36a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645430"
---
# <a name="security-controls-for-azure-service-fabric"></a>Controlli di sicurezza per Azure Service FabricSecurity controls for Azure Service Fabric

Questo articolo documenta i controlli di sicurezza incorporati in Azure Service Fabric.This article documents the security controls built into Azure Service Fabric. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto dell'endpoint di servizioService endpoint support| Sì |  |
| Supporto per l'inserimento di vNet| Sì |  |
| Isolamento della rete e supporto firewall| Sì | Uso dei gruppi di sicurezza di rete. |
| Supporto per il tunneling forzato| Sì | Sulla rete di Azure è disponibile il tunneling forzato. |

## <a name="monitoring--logging"></a>Monitoraggio della registrazione dei &

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per il monitoraggio di Azure (analisi dei log, informazioni dettagliate sulle app e così via)Azure monitoring support (Log analytics, App insights, etc.)| Sì | Uso del supporto per il monitoraggio di Azure e del supporto di terze parti. |
| Controllo e gestione del controllo e dell'audit degli aerei| Sì | Tutte le operazioni del piano di controllo vengono eseguite tramite i processi per il controllo e le approvazioni. |
| Registrazione e controllo dei piani dati| N/D | Il cliente è proprietario del cluster.  |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | L'autenticazione avviene tramite Azure Active Directory. |
| Autorizzazione| Sì | Gestione delle identità e degli accessi per le chiamate tramite provider di risorse di Service Fabric. Le chiamate direttamente all'end point del cluster supportano due ruoli: User e Admin. Il cliente può eseguire il mapping delle API a entrambi i ruoli. |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server inattivi: chiavi gestite da MicrosoftServer-side encryption at rest: Microsoft-managed keys | Sì | Il cliente è proprietario del cluster e del set di scalabilità della macchina virtuale in cui viene creato il cluster. La crittografia del disco di Azure può essere abilitata nel set di scalabilità della macchina virtuale. |
| Crittografia lato server inattivi: chiavi gestite dal cliente (BYOK)Server-side encryption at rest: customer-managed keys (BYOK) | Sì | Il cliente è proprietario del cluster e del set di scalabilità della macchina virtuale in cui viene creato il cluster. La crittografia del disco di Azure può essere abilitata nel set di scalabilità della macchina virtuale. |
| Crittografia a livello di colonna (Azure Data Services)Column level encryption (Azure Data Services)| N/D |  |
| Crittografia in transito (ad esempio la crittografia ExpressRoute, nella crittografia della rete virtuale e crittografia VNet-VNet)Encryption in transit (such as ExpressRoute encryption, in VNet encryption, and VNet-VNet encryption)| Sì |  |
| Chiamate API crittografate| Sì | Le chiamate API di Service Fabric vengono eseguite tramite Azure Resource Manager. È necessario un token JSON Web (JWT) valido. |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui controlli di sicurezza incorporati nei servizi di Azure.Learn more about the [built-in security controls across Azure services](../security/fundamentals/security-controls.md).