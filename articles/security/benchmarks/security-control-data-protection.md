---
title: Azure Security Control - Data Protection
description: Protezione dei dati del controllo di sicurezza di AzureAzure Security Control Data Protection
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 035894c80e619851264aae91daa2d7852d156964
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408552"
---
# <a name="security-control-data-protection"></a>Controllo di sicurezza: Protezione dei dati

Le raccomandazioni sulla protezione dei dati si concentrano sulla risoluzione dei problemi relativi alla crittografia, agli elenchi di controllo di accesso, al controllo degli accessi basato sull'identità e alla registrazione di controllo per l'accesso ai dati.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Mantenere un inventario di informazioni sensibili

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 4.1 | 13.1 | Customer |

Usare i tag per tenere traccia delle risorse di Azure che archiviano o elaborano informazioni riservate.

- [Come creare e utilizzare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolare i sistemi che memorizzano o elaborano informazioni sensibili

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 4.2 | 13.2, 2.10 | Customer |

Implementare l'isolamento utilizzando sottoscrizioni e gruppi di gestione separati per singoli domini di sicurezza, ad esempio il tipo di ambiente e il livello di riservatezza dei dati. È possibile limitare il livello di accesso alle risorse di Azure richieste dalle applicazioni e dagli ambienti aziendali. È possibile controllare l'accesso alle risorse di Azure tramite il controllo degli accessi in base al ruolo di Azure Active Directory.You can control access to Azure resources via Azure Active Directory role-based access control. 

- [Come creare sottoscrizioni di Azure aggiuntiveHow to create additional Azure subscriptions](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Come creare gruppi di gestioneHow to create Management Groups](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Come creare e utilizzare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorare e bloccare il trasferimento non autorizzato di informazioni sensibili

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 4.3 | 13.3 | Condiviso |

Sfrutta una soluzione di terze parti di Azure Marketplace sui perimetri di rete che monitora il trasferimento non autorizzato di informazioni riservate e blocca tali trasferimenti mentre avvisa i professionisti della sicurezza delle informazioni.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e protegge dalla perdita e dall'esposizione dei dati dei clienti. Per garantire la sicurezza dei dati dei clienti in Azure, Microsoft ha implementato e gestisce una suite di potenti controlli e funzionalità di protezione dei dati.

- [Comprendere la protezione dei dati dei clienti in AzureUnderstand customer data protection in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Crittografare tutte le informazioni sensibili in transito

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 4.4 | 14.4 | Condiviso |

Crittografare tutte le informazioni riservate in transito. Verificare che tutti i client che si connettono alle risorse di Azure siano in grado di negoziare TLS 1.2 o versione successiva.

Seguire i consigli del Centro sicurezza di Azure per la crittografia inattivi e la crittografia in transito, se applicabile.

- [Comprendere la crittografia in transito con AzureUnderstand encryption in transit with Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilizzare uno strumento di individuazione attivo per identificare i dati sensibili

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 4.5 | 14.5 | Condiviso |

Quando non è disponibile alcuna funzionalità per il servizio specifico in Azure, usare uno strumento di individuazione attivo di terze parti per identificare tutte le informazioni riservate archiviate, elaborate o trasmesse dai sistemi tecnologici dell'organizzazione, inclusi quelli in loco o presso un provider di servizi remoto, e aggiornare l'inventario delle informazioni riservate dell'organizzazione.

Usare Azure Information Protection per identificare le informazioni riservate all'interno dei documenti di Office 365.Use Azure Information Protection for identifying sensitive information within Office 365 documents.

Usare Azure SQL Information Protection per facilitare la classificazione e l'etichettatura delle informazioni archiviate nei database SQL di Azure.Use Azure SQL Information Protection to assist in the classification and labeling of information stored in Azure SQL Databases.

- [Come implementare l'individuazione dei dati SQL di AzureHow to implement Azure SQL Data Discovery](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

- [Come implementare Azure Information Protection](https://docs.microsoft.com/azure/information-protection/deployment-roadmap)

- [Comprendere la protezione dei dati dei clienti in AzureUnderstand customer data protection in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Utilizzare il controllo degli accessi in base al ruolo per controllare l'accesso alle risorse

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 4.6 | 14.6 | Customer |

Usare il controllo degli accessi in base al ruolo di Azure AD per controllare l'accesso a dati e risorse, altrimenti usare metodi di controllo di accesso specifici del servizio.

- [Come configurare il controllo degli accessi in base al ruolo in AzureHow to configure RBAC in Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizzare la prevenzione della perdita di dati basata su host per applicare il controllo degli accessi

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 4.7 | 14.7 | Condiviso |

Se necessario per la conformità alle risorse di calcolo, implementare uno strumento di terze parti, ad esempio una soluzione automatizzata di Prevenzione della perdita di dati basata su host, per applicare i controlli di accesso ai dati anche quando i dati vengono copiati da un sistema.

Per la piattaforma sottostante gestita da Microsoft, Microsoft considera tutti i contenuti dei clienti come sensibili e fa di tutto per proteggersi dalla perdita e dall'esposizione dei dati dei clienti. Per garantire la sicurezza dei dati dei clienti in Azure, Microsoft ha implementato e gestisce una suite di potenti controlli e funzionalità di protezione dei dati.

- [Comprendere la protezione dei dati dei clienti in AzureUnderstand customer data protection in Azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Crittografare le informazioni sensibili inattivi

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 4.8 | 14.8 | Customer |

Usare la crittografia inattivi in tutte le risorse di Azure.Use encryption at rest on all Azure resources. Microsoft consiglia di consentire ad Azure di gestire le chiavi di crittografia, tuttavia è possibile gestire le proprie chiavi in alcuni casi. 

- [Comprendere la crittografia inattivi in AzureUnderstand encryption at rest in Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- [Come configurare le chiavi di crittografia gestite dal cliente](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrare e avvisare le modifiche alle risorse critiche di Azure4.9: Log and alert on changes to critical Azure resources

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 4.9 | 14.9 | Customer |

Usare Monitoraggio di Azure con il log attività di Azure per creare avvisi per quando vengono apportate modifiche alle risorse di Azure critiche.

- [Come creare avvisi per gli eventi del log attività di AzureHow to create alerts for Azure Activity Log events](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)


## <a name="next-steps"></a>Passaggi successivi

- Vedere il prossimo controllo di sicurezza: [gestione delle vulnerabilità](security-control-vulnerability-management.md)