---
title: Azure Security Control - Data Protection
description: Protezione dei dati di controllo di sicurezza
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 5482495f87e87e5d05d8adca6b053810a62dcb4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934511"
---
# <a name="security-control-data-protection"></a>Controllo di sicurezza: Protezione dei dati

Le raccomandazioni sulla protezione dei dati si concentrano sulla risoluzione dei problemi relativi alla crittografia, agli elenchi di controllo di accesso, al controllo degli accessi basato sull'identità e alla registrazione di controllo per l'accesso ai dati.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Mantenere un inventario di informazioni sensibili

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 4.1 | 13.1 | Customer |

Usare i tag per tenere traccia delle risorse di Azure che archiviano o elaborano informazioni riservate.

Come creare e utilizzare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolare i sistemi che memorizzano o elaborano informazioni sensibili

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 4.2 | 13.2 | Customer |

Implementare sottoscrizioni e/o gruppi di gestione separati per lo sviluppo, il test e la produzione. Le risorse devono essere separate da VNet/Subnet, contrassegnate in modo appropriato e protette da un gruppo di sicurezza di rete o da un firewall di Azure.Resources should be separated by VNet/Subnet, tagged appropriately, and secured by an NSG or Azure Firewall. Le risorse che archiviano o elaborano dati sensibili devono essere sufficientemente isolate. Per le macchine virtuali che archiviano o elaborano dati sensibili, implementare criteri e procedure per disattivarli quando non sono in uso.

Come creare sottoscrizioni di Azure aggiuntive:How to create additional Azure subscriptions:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare i gruppi di gestione:

https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e utilizzare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Come creare una rete virtuale:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Come creare un gruppo di sicurezza di base con una configurazione di sicurezza:How to create an NSG with a Security Config:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Come distribuire Firewall di Azure:How to deploy Azure Firewall:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Come configurare avvisi o avvisi e rifiuto con Firewall di Azure:How to configure alert or alert and deny with Azure Firewall:

https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitorare e bloccare il trasferimento non autorizzato di informazioni sensibili

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 4.3 | 13.3 | Customer |

Distribuisci uno strumento automatizzato sui perimetri di rete che monitora il trasferimento non autorizzato di informazioni sensibili e blocca tali trasferimenti mentre avvisa i professionisti della sicurezza delle informazioni.

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Crittografare tutte le informazioni sensibili in transito

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 4.4 | 14.4 | Condiviso |

Crittografare tutte le informazioni riservate in transito. Verificare che tutti i client che si connettono alle risorse di Azure siano in grado di negoziare TLS 1.2 o versione successiva.

Seguire i consigli del Centro sicurezza di Azure per la crittografia inattivi e la crittografia in transito, se applicabile.

Comprendere la crittografia in transito con Azure:Understand encryption in transit with Azure:

https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilizzare uno strumento di individuazione attivo per identificare i dati sensibili

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 4.5 | 14.5 | Customer |

Quando non è disponibile alcuna funzionalità per il servizio specifico in Azure, usare uno strumento di individuazione attivo di terze parti per identificare tutte le informazioni riservate archiviate, elaborate o trasmesse dai sistemi tecnologici dell'organizzazione, inclusi quelli in sede o in un provider di servizi remoto e aggiornare l'inventario delle informazioni riservate dell'organizzazione.

Usare Azure Information Protection per identificare le informazioni riservate all'interno dei documenti di Office 365.Use Azure Information Protection for identifying sensitive information within Office 365 documents.

Usare Azure SQL Information Protection per facilitare la classificazione e l'etichettatura delle informazioni archiviate nei database SQL di Azure.Use Azure SQL Information Protection to assist in the classification and labeling of information stored in Azure SQL Databases.

Come implementare l'individuazione dei dati SQL di Azure:How to implement Azure SQL Data Discovery:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

Come implementare Azure Information Protection:How to implement Azure Information Protection:

https://docs.microsoft.com/azure/information-protection/deployment-roadmap

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse4.6: Usare il controllo degli accessi in base al ruolo di

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 4.6 | 14.6 | Customer |

Usare il controllo degli accessi in base al ruolo di Azure AD per controllare l'accesso a dati e risorse, altrimenti usare metodi di controllo di accesso specifici del servizio.

Informazioni sul controllo degli accessi in base al ruolo di Azure:Understand

https://docs.microsoft.com/azure/role-based-access-control/overview

Come configurare il controllo degli accessi in base al ruolo in Azure:How to configure RBAC in Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizzare la prevenzione della perdita di dati basata su host per applicare il controllo degli accessi

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 4.7 | 14.7 | Customer |

Implementare uno strumento di terze parti, ad esempio una soluzione automatica di Prevenzione della perdita di dati basata su host, per applicare i controlli di accesso ai dati anche quando i dati vengono copiati all'esiura di un sistema.

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Crittografare le informazioni sensibili inattivi

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 4.8 | 14.8 | Customer |

Usare la crittografia inattivi in tutte le risorse di Azure.Use encryption at rest on all Azure resources. Microsoft consiglia di consentire ad Azure di gestire le chiavi di crittografia, tuttavia è possibile gestire le proprie chiavi in alcuni casi. 

Comprendere la crittografia inattivi in Azure:Understand encryption at rest in Azure:

https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Come configurare le chiavi di crittografia gestite dal cliente:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registrare e avvisare le modifiche alle risorse critiche di Azure4.9: Log and alert on changes to critical Azure resources

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 4.9 | 14.9 | Customer |

Usare Monitoraggio di Azure con il log attività di Azure per creare avvisi per quando vengono apportate modifiche alle risorse di Azure critiche.

Come creare avvisi per gli eventi del log attività di Azure:How to create alerts for Azure Activity Log events:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

## <a name="next-steps"></a>Passaggi successivi

Vedere il controllo di sicurezza successivo: [Gestione delle vulnerabilità](security-control-vulnerability-management.md)
