---
title: Controllo di sicurezza di Azure-protezione dei dati
description: Protezione dei dati del controllo di sicurezza
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 5482495f87e87e5d05d8adca6b053810a62dcb4e
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934511"
---
# <a name="security-control-data-protection"></a>Controllo di sicurezza: protezione dei dati

I suggerimenti sulla protezione dei dati sono incentrati sulla risoluzione dei problemi correlati alla crittografia, agli elenchi di controllo di accesso, al controllo di accesso basato sull'identità e alla registrazione di controllo per l'accesso

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: gestire un inventario delle informazioni riservate

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 4.1 | 13.1 | Customer |

Usare i tag per semplificare il monitoraggio delle risorse di Azure che archiviano o elaborano informazioni riservate.

Come creare e usare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: isolare i sistemi che archiviano o elaborano informazioni riservate

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 4.2 | 13.2 | Customer |

Implementare sottoscrizioni e/o gruppi di gestione distinti per lo sviluppo, il test e la produzione. Le risorse devono essere separate da VNet/subnet, contrassegnate in modo appropriato e protette da un NSG o da un firewall di Azure. Le risorse che archiviano o elaborano dati sensibili devono essere sufficientemente isolate. Per le macchine virtuali che archiviano o elaborano dati sensibili, implementare i criteri e le procedure per disabilitarli quando non sono in uso.

Come creare sottoscrizioni di Azure aggiuntive:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare Gruppi di gestione:

https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e usare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Come creare una rete virtuale:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Come creare una NSG con una configurazione di sicurezza:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Come distribuire il firewall di Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Come configurare avvisi o avvisi e negare con il firewall di Azure:

https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 4.3 | 13.3 | Customer |

Distribuire uno strumento automatizzato sui perimetri di rete che monitora il trasferimento non autorizzato di informazioni riservate e blocca tali trasferimenti mentre invia avvisi ai professionisti della sicurezza delle informazioni.

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: crittografare tutte le informazioni riservate in transito

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 4.4 | 14.4 | Condivisione |

Crittografare tutte le informazioni riservate in transito. Assicurarsi che tutti i client che si connettono alle risorse di Azure siano in grado di negoziare TLS 1,2 o versione successiva.

Seguire le raccomandazioni del Centro sicurezza di Azure per la crittografia dei dati inattivi e la crittografia in transito, ove applicabile.

Comprendere la crittografia in transito con Azure:

https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: usare uno strumento di individuazione attivo per identificare i dati sensibili

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 4.5 | 14,5 | Customer |

Quando non è disponibile alcuna funzionalità per il servizio specifico in Azure, usare uno strumento di individuazione attiva di terze parti per identificare tutte le informazioni riservate archiviate, elaborate o trasmesse dai sistemi tecnologici dell'organizzazione, inclusi quelli presenti in sede o provider di servizi remoti e aggiornare l'inventario delle informazioni riservate dell'organizzazione.

Usare Azure Information Protection per identificare le informazioni riservate nei documenti di Office 365.

Usare Information Protection SQL di Azure per semplificare la classificazione e l'assegnazione di etichette alle informazioni archiviate nei database SQL di Azure.

Come implementare l'individuazione dati SQL di Azure:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

Come implementare Azure Information Protection:

https://docs.microsoft.com/azure/information-protection/deployment-roadmap

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: usare RBAC di Azure per controllare l'accesso alle risorse

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 4.6 | 14,6 | Customer |

Usare Azure AD controllo degli accessi in base al ruolo per controllare l'accesso ai dati e alle risorse; in caso contrario, usare metodi di controllo

Informazioni su RBAC di Azure:

https://docs.microsoft.com/azure/role-based-access-control/overview

Come configurare RBAC in Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 4.7 | 14,7 | Customer |

Implementare uno strumento di terze parti, ad esempio una soluzione di prevenzione della perdita dei dati basata su host automatizzata, per applicare i controlli di accesso ai dati anche quando i dati vengono copiati fuori da un sistema.

## <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: crittografare le informazioni riservate inattive

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 4.8 | 14.8 | Customer |

Usare la crittografia inattiva in tutte le risorse di Azure. Microsoft consiglia di consentire ad Azure di gestire le chiavi di crittografia. Tuttavia, è possibile gestire le proprie chiavi in alcune istanze. 

Informazioni sulla crittografia inattiva in Azure:

https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Come configurare le chiavi di crittografia gestite dal cliente:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: registrare e segnalare le modifiche apportate alle risorse di Azure critiche

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 4,9 | 14,9 | Customer |

Usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle risorse di Azure critiche.

Come creare avvisi per gli eventi del log attività di Azure:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

## <a name="next-steps"></a>Passaggi successivi

Vedere il controllo di sicurezza successivo: [gestione delle vulnerabilità](security-control-vulnerability-management.md)
