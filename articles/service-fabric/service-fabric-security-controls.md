---
title: Controlli di sicurezza per Azure Service Fabric
description: Informazioni sui controlli di sicurezza per Azure Service Fabric. Include un elenco di controllo dei controlli di sicurezza incorporati.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a8bb49e20ec5812a4882966c6918cf2bd59f36a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75645430"
---
# <a name="security-controls-for-azure-service-fabric"></a>Controlli di sicurezza per Azure Service Fabric

Questo articolo descrive i controlli di sicurezza incorporati in Azure Service Fabric. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Sì |  |
| Supporto di VNet Injection| Sì |  |
| Isolamento rete e supporto del firewall| Sì | Uso dei gruppi di sicurezza di rete. |
| Supporto del tunneling forzato| Sì | Sulla rete di Azure è disponibile il tunneling forzato. |

## <a name="monitoring--logging"></a>Monitoraggio & registrazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Uso del supporto di monitoraggio di Azure e del supporto di terze parti. |
| Registrazione e controllo del piano di gestione e controllo| Sì | Tutte le operazioni del piano di controllo vengono eseguite tramite i processi per il controllo e le approvazioni. |
| Registrazione e controllo del piano dati| N/D | Il cliente è proprietario del cluster.  |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | L'autenticazione avviene tramite Azure Active Directory. |
| Autorizzazione| Sì | Gestione delle identità e degli accessi per le chiamate tramite provider di risorse di Service Fabric. Chiamate dirette a endpoint cluster supporta due ruoli: utente e amministratore. Il cliente può eseguire il mapping delle API a entrambi i ruoli. |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server: chiavi gestite da Microsoft | Sì | Il cliente è proprietario del cluster e del set di scalabilità di macchine virtuali in cui viene compilato il cluster. Crittografia dischi di Azure può essere abilitata nel set di scalabilità di macchine virtuali. |
| Crittografia lato server inattiva: chiavi gestite dal cliente (BYOK) | Sì | Il cliente è proprietario del cluster e del set di scalabilità di macchine virtuali in cui viene compilato il cluster. Crittografia dischi di Azure può essere abilitata nel set di scalabilità di macchine virtuali. |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D |  |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì |  |
| Chiamate API crittografate| Sì | Le chiamate API di Service Fabric vengono eseguite tramite Azure Resource Manager. È necessario un token JSON Web (JWT) valido. |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).