---
title: Controllo di sicurezza di Azure-protezione dei dati
description: Protezione dei dati del controllo di sicurezza di Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8e2144a61d83f6c7dece8f34232031192b51cde8
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412698"
---
# <a name="security-control-data-protection"></a>Controllo di sicurezza: protezione dei dati

I suggerimenti sulla protezione dei dati sono incentrati sulla risoluzione dei problemi correlati alla crittografia, agli elenchi di controllo di accesso, al controllo di accesso basato sull'identità e alla registrazione di controllo per l'accesso

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: gestire un inventario delle informazioni riservate

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 4.1 | 13.1 | Customer |

Usare i tag per semplificare il monitoraggio delle risorse di Azure che archiviano o elaborano informazioni riservate.

- [Come creare e usare i tag](../../azure-resource-manager/management/tag-resources.md)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: isolare i sistemi che archiviano o elaborano informazioni riservate

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 4.2 | 13,2, 2,10 | Customer |

Implementare l'isolamento utilizzando sottoscrizioni e gruppi di gestione distinti per singoli domini di sicurezza, ad esempio il tipo di ambiente e il livello di sensibilità dei dati. È possibile limitare il livello di accesso alle risorse di Azure richieste dalle applicazioni e dagli ambienti aziendali. È possibile controllare l'accesso alle risorse di Azure tramite il controllo degli accessi in base al ruolo di Azure (RBAC di Azure). 

- [Come creare sottoscrizioni di Azure aggiuntive](../../cost-management-billing/manage/create-subscription.md)

- [Come creare gruppi di gestione](../../governance/management-groups/create-management-group-portal.md)

- [Come creare e usare i tag](../../azure-resource-manager/management/tag-resources.md)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: monitorare e bloccare il trasferimento non autorizzato di informazioni riservate

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 4.3 | 13,3 | Condiviso |

Sfrutta una soluzione di terze parti da Azure Marketplace sui perimetri di rete che monitora il trasferimento non autorizzato di informazioni riservate e blocca tali trasferimenti mentre invia avvisi ai professionisti della sicurezza delle informazioni.

Per la piattaforma sottostante gestita da Microsoft, tutti i contenuti dei clienti vengono considerati sensibili e protetti dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../fundamentals/protection-customer-data.md)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: crittografare tutte le informazioni riservate in transito

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 4.4 | 14,4 | Condiviso |

Crittografare tutte le informazioni riservate in transito. Assicurarsi che tutti i client che si connettono alle risorse di Azure siano in grado di negoziare TLS 1,2 o versione successiva.

Seguire le raccomandazioni del Centro sicurezza di Azure per la crittografia dei dati inattivi e la crittografia in transito, ove applicabile.

- [Informazioni sulla crittografia in transito con Azure](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: usare uno strumento di individuazione attivo per identificare i dati sensibili

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 4.5 | 14,5 | Condiviso |

Quando non è disponibile alcuna funzionalità per il servizio specifico in Azure, usare uno strumento di individuazione attiva di terze parti per identificare tutte le informazioni riservate archiviate, elaborate o trasmesse dai sistemi tecnologici dell'organizzazione, incluse quelle situate in loco o a un provider di servizi remoti, e aggiornare l'inventario delle informazioni riservate dell'organizzazione.

Utilizzare Azure Information Protection per identificare le informazioni riservate all'interno di Microsoft 365 documenti.

Usare Information Protection SQL di Azure per semplificare la classificazione e l'assegnazione di etichette alle informazioni archiviate nel database SQL di Azure.

- [Come implementare l'individuazione dati SQL di Azure](../../azure-sql/database/data-discovery-and-classification-overview.md)

- [Come implementare Azure Information Protection](/azure/information-protection/deployment-roadmap)

- [Informazioni sulla protezione dei dati dei clienti in Azure](../fundamentals/protection-customer-data.md)

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 4,6 | 14,6 | Customer |

Usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso ai dati e alle risorse; in caso contrario, usare i metodi di controllo di accesso specifici del servizio.

- [Come configurare RBAC di Azure](../../role-based-access-control/role-assignments-portal.md)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: usare la prevenzione della perdita dei dati basata su host per applicare il controllo di accesso

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 4.7 | 14,7 | Condiviso |

Se necessario per la conformità alle risorse di calcolo, implementare uno strumento di terze parti, ad esempio una soluzione di prevenzione della perdita dei dati basata su host automatizzata, per applicare i controlli di accesso ai dati anche quando i dati vengono copiati fuori da un sistema.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e si impegna per difendersi dalla perdita di dati e dall'esposizione dei clienti. Per garantire che i dati dei clienti in Azure rimangano protetti, Microsoft ha implementato e applica un gruppo di controlli e funzionalità affidabili per la protezione dei dati.

- [Informazioni sulla protezione dei dati dei clienti in Azure](../fundamentals/protection-customer-data.md)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: crittografare le informazioni riservate inattive

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 4.8 | 14.8 | Customer |

Usare la crittografia inattiva in tutte le risorse di Azure. Microsoft consiglia di consentire ad Azure di gestire le chiavi di crittografia. Tuttavia, è possibile gestire le proprie chiavi in alcune istanze. 

- [Informazioni sulla crittografia inattiva in Azure](../fundamentals/encryption-atrest.md)

- [Come configurare le chiavi di crittografia gestite dal cliente](../../storage/common/customer-managed-keys-configure-key-vault.md)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 4.9 | 14,9 | Customer |

Usare monitoraggio di Azure con il log attività di Azure per creare avvisi per le modifiche apportate alle risorse di Azure critiche.

- [Come creare avvisi per gli eventi del log attività di Azure](../../azure-monitor/platform/alerts-activity-log.md)


## <a name="next-steps"></a>Passaggi successivi

- Vedere il controllo di sicurezza successivo:  [gestione delle vulnerabilità](security-control-vulnerability-management.md)