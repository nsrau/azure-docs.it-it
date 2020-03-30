---
title: Azure Security Control - Inventory and Asset Management
description: Inventario del controllo di sicurezza e gestione delle risorse
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 2f24012a69a7673426644a8ded6162cd1021938e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75930069"
---
# <a name="security-control-inventory-and-asset-management"></a>Controllo di sicurezza: gestione dell'inventario e delle risorse

Le raccomandazioni di Inventario e Gestione asset si concentrano sull'affrontare i problemi relativi alla gestione attiva (inventario, traccia e correzione) di tutte le risorse di Azure in modo che solo le risorse autorizzate siano accessibili e le risorse non autorizzate e non gestite identificati e rimossi.

## <a name="61-use-azure-asset-discovery"></a>6.1: Usare Azure Asset Discovery

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.1 | 1.1, 1.2, 1.3, 1.4, 9.1, 12.1 | Customer |

Usare Azure Resource Graph per eseguire query/individuare tutte le risorse (ad esempio elaborazione, archiviazione, rete, porte e protocolli e così via) all'interno delle sottoscrizioni.  Garantire le autorizzazioni appropriate (di lettura) nel tenant ed enumerare tutte le sottoscrizioni di Azure, nonché le risorse all'interno delle sottoscrizioni.

Anche se le risorse di Azure classiche possono essere individuate tramite Resource Graph, è consigliabile creare e usare le risorse di Azure Resource Manager in futuro.

Come creare query con Azure Resource Graph:How to create queries with Azure Resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Come visualizzare le sottoscrizioni di Azure:How to view your Azure Subscriptions:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Informazioni sul controllo degli accessi in base al ruolo di Azure:Understand

https://docs.microsoft.com/azure/role-based-access-control/overview

## <a name="62-maintain-asset-metadata"></a>6.2: Gestire i metadati delle risorse

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.2 | 1.5 | Customer |

Applicare tag alle risorse di Azure che assano metadati per organizzarli logicamente in una tassonomia.

Come creare e utilizzare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminare risorse di Azure non autorizzate

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.3 | 1.6 | Customer |

Utilizzare il tagging, i gruppi di gestione e le sottoscrizioni separate, se appropriato, per organizzare e tenere traccia delle risorse. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate dalla sottoscrizione in modo tempestivo.

Come creare sottoscrizioni di Azure aggiuntive:How to create additional Azure subscriptions:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Come creare i gruppi di gestione:

https://docs.microsoft.com/azure/governance/management-groups/create

Come creare e utilizzare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Gestire un inventario delle risorse di Azure approvate e dei titoli software

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.4 | 2.1 | Customer |

Definire le risorse di Azure approvate e il software approvato per le risorse di calcolo.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorare le risorse di Azure non approvate

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6,5 | 2.3, 2.4 | Customer |

Usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni.

Usare Azure Resource Graph per eseguire query/individuare risorse all'interno delle sottoscrizioni. &nbsp;Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come creare query con Azure Graph:How to create queries with Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorare le applicazioni software non approvate all'interno delle risorse di calcolo

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.6 | 2.3/2.4 | Customer |

Usare l'inventario delle macchine virtuali di Azure per automatizzare la raccolta di informazioni su tutto il software nelle macchine virtuali. Nome software, Versione, Autore e Ora aggiornamento sono disponibili nel portale di Azure.Software Name, Version, Publisher, and Refresh time are available from the Azure portal. To get access to install date and other information, enable guest-level diagnostics and bring the Windows Event Logs into a Log Analytics Workspace.

Come abilitare l'inventario delle macchine virtuali di Azure:How to enable Azure virtual machine Inventory:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Rimuovere le risorse e le applicazioni software di Azure non approvate

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.7 | 2.5 | Customer |

Usare il monitoraggio dell'integrità dei file del Centro sicurezza di Azure (rilevamento delle modifiche) e l'inventario delle macchine virtuali per identificare tutto il software installato nelle macchine virtuali. È possibile implementare un processo personalizzato per la rimozione di software non autorizzato. È inoltre possibile utilizzare una soluzione di terze parti per identificare il software non approvato.

Come utilizzare il monitoraggio dell'integrità dei file:

https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring

Informazioni sul rilevamento delle modifiche di Azure:Understand Azure Change Tracking:

https://docs.microsoft.com/azure/automation/change-tracking

Come abilitare l'inventario delle macchine virtuali di Azure:How to enable Azure virtual machine inventory:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="68-use-only-approved-applications"></a>6.8: Utilizzare solo applicazioni approvate

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.8 | 2.6 | Customer |

Usare i controlli delle applicazioni adattive del Centro sicurezza di Azure per assicurarsi che venga eseguito solo il software autorizzato e che l'esecuzione di tutto il software non autorizzato nelle macchine virtuali di Azure.Use Azure Security Center Adaptive Application Controls to ensure that only authorized software executes and all unauthorized software is blocked from executing on Azure Virtual Machines.

Come usare i controlli delle applicazioni adattive del Centro sicurezza di Azure:How to use Azure Security Center Adaptive Application Controls:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="69-use-only-approved-azure-services"></a>6.9: Usare solo i servizi di Azure approvati

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.9 | 2.6 | Customer |

Usare Criteri di Azure per limitare i servizi di cui è possibile eseguire il provisioning nell'ambiente.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Come negare un tipo di risorsa specifico con Criteri di Azure:How to deny a specific resource type with Azure Policy:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

## <a name="610-implement-approved-application-list"></a>6.10: Implementare l'elenco delle applicazioni approvate

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.1 | 2.7 | Customer |

Usare i controlli delle applicazioni adattivi del Centro sicurezza di Azure per specificare i tipi di file a cui una regola può applicare o meno.

Implementare una soluzione di terze parti se ciò non soddisfa il requisito.

Come usare i controlli delle applicazioni adattive del Centro sicurezza di Azure:How to use Azure Security Center Adaptive Application Controls:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resource Manager tramite script

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.11 | 2.8 | Customer |

Usare l'accesso condizionale di Azure per limitare la &quot;capacità&quot; degli &quot;utenti&quot; di interagire con Azure Resource Manager configurando Blocca accesso per l'app di gestione di Microsoft Azure.Use Azure Conditional Access to limit users' ability to interact with Azure Resource Manager by configuring Block access for the Microsoft Azure Management App.

Come configurare l'accesso condizionale per bloccare l'accesso a Azure Resource Manager:How to configure Conditional Access to block access to Azure Resource Manager:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitare la capacità degli utenti di eseguire script all'interno di risorse di calcolo

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.12 | 2.8 | Customer |

Usare configurazioni specifiche del sistema operativo o risorse di terze parti per limitare la capacità degli utenti di eseguire script all'interno delle risorse di calcolo di Azure.Use operating system specific configurations or third-party resources to limit users' ability to execute scripts within Azure compute resources.

Ad esempio, come controllare l'esecuzione di script di PowerShell in ambienti Windows:For example, how to control PowerShell script execution in Windows Environments:

https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separare fisicamente o logicamente le applicazioni ad alto rischio

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.13 | 2,9 | Customer |

Il software necessario per le operazioni aziendali, ma che può comportare rischi più elevati per l'organizzazione, deve essere isolato all'interno della propria macchina virtuale e/o di rete virtuale e sufficientemente protetto con un firewall di Azure o un gruppo di sicurezza di rete.

Come creare una rete virtuale:How to create a virtual network:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Come creare un gruppo di sicurezza di gruppo con una configurazione di sicurezza:How to create an NSG with a security config:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="next-steps"></a>Passaggi successivi

Vedere il controllo di sicurezza successivo: [Configurazione sicuraSee](security-control-secure-configuration.md) the next security control: Secure Configuration
