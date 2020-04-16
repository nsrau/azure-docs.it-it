---
title: Azure Security Control - Inventory and Asset Management
description: Inventario e gestione delle risorse di Azure Security Control
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: aece7278d134145e00b027c184f3817d5566e1e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408370"
---
# <a name="security-control-inventory-and-asset-management"></a>Controllo di sicurezza: gestione dell'inventario e delle risorse

I consigli di inventario e gestione delle risorse si concentrano sull'affrontare i problemi relativi alla gestione attiva (inventario, traccia e correttezza) di tutte le risorse di Azure in modo che solo le risorse autorizzate vengano accessibili e che le risorse non autorizzate e non gestite vengano identificate e rimosse.

## <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilizzare la soluzione di individuazione automatica degli asset

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.1 | 1.1, 1.2, 1.3, 1.4, 9.1, 12.1 | Customer |

Usare Azure Resource Graph per eseguire query/individuare tutte le risorse (ad esempio elaborazione, archiviazione, rete, porte e protocolli e così via) all'interno delle sottoscrizioni.  Garantire le autorizzazioni appropriate (di lettura) nel tenant ed enumerare tutte le sottoscrizioni di Azure, nonché le risorse all'interno delle sottoscrizioni.

Anche se le risorse di Azure classiche possono essere individuate tramite Resource Graph, è consigliabile creare e usare le risorse di Azure Resource Manager in futuro.

- [Come creare query con Azure Resource GraphHow to create queries with Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Come visualizzare le sottoscrizioni di AzureHow to view your Azure Subscriptions](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Informazioni sul controllo degli accessi in base al ruolo di](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="62-maintain-asset-metadata"></a>6.2: Gestire i metadati delle risorse

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.2 | 1.5 | Customer |

Applicare tag alle risorse di Azure che assano metadati per organizzarli logicamente in una tassonomia.

- [Come creare e utilizzare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminare risorse di Azure non autorizzate

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.3 | 1.6 | Customer |

Utilizzare il tagging, i gruppi di gestione e le sottoscrizioni separate, se appropriato, per organizzare e tenere traccia delle risorse. Riconciliare l'inventario a intervalli regolari e assicurarsi che le risorse non autorizzate vengano eliminate dalla sottoscrizione in modo tempestivo.

- [Come creare sottoscrizioni di Azure aggiuntiveHow to create additional Azure subscriptions](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Come creare gruppi di gestioneHow to create Management Groups](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Come creare e utilizzare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definire e gestire un inventario delle risorse di Azure approvate

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.4 | 2.1 | Customer |

Creare un inventario delle risorse di Azure approvate e del software approvato per le risorse di calcolo in base alle esigenze dell'organizzazione.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitorare le risorse di Azure non approvate

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6,5 | 2.3, 2.4 | Customer |

Usare Criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni.

Usare Azure Resource Graph per eseguire query/individuare risorse all'interno delle sottoscrizioni.  Verificare che tutte le risorse di Azure presenti nell'ambiente siano approvate.

- [Come configurare e gestire Criteri di AzureHow to configure and manage Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Come creare query con Azure GraphHow to create queries with Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitorare le applicazioni software non approvate all'interno delle risorse di calcolo

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.6 | 2.3, 2.4 | Customer |

Usare l'inventario delle macchine virtuali di Azure per automatizzare la raccolta di informazioni su tutto il software nelle macchine virtuali. Nome software, Versione, Autore e Ora aggiornamento sono disponibili nel portale di Azure.Software Name, Version, Publisher, and Refresh time are available from the Azure portal. To get access to install date and other information, enable guest-level diagnostics and bring the Windows Event Logs into a Log Analytics Workspace.

- [Come abilitare l'inventario delle macchine virtuali di AzureHow to enable Azure virtual machine Inventory](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Rimuovere le risorse e le applicazioni software di Azure non approvate

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.7 | 2.5 | Customer |

Usare il monitoraggio dell'integrità dei file del Centro sicurezza di Azure (rilevamento delle modifiche) e l'inventario delle macchine virtuali per identificare tutto il software installato nelle macchine virtuali. È possibile implementare un processo personalizzato per la rimozione di software non autorizzato. È inoltre possibile utilizzare una soluzione di terze parti per identificare il software non approvato.

- [Come utilizzare il monitoraggio dell'integrità dei file](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring)

- [Informazioni sul rilevamento delle modifiche di AzureUnderstand Azure Change Tracking](https://docs.microsoft.com/azure/automation/change-tracking)

- [Come abilitare l'inventario delle macchine virtuali di AzureHow to enable Azure virtual machine inventory](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

## <a name="68-use-only-approved-applications"></a>6.8: Utilizzare solo applicazioni approvate

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.8 | 2.6 | Customer |

Usare i controlli delle applicazioni adattive del Centro sicurezza di Azure per assicurarsi che venga eseguito solo il software autorizzato e che l'esecuzione di tutto il software non autorizzato nelle macchine virtuali di Azure.Use Azure Security Center Adaptive Application Controls to ensure that only authorized software executes and all unauthorized software is blocked from executing on Azure Virtual Machines.

- [Come usare i controlli delle applicazioni adattive del Centro sicurezza di AzureHow to use Azure Security Center Adaptive Application Controls](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="69-use-only-approved-azure-services"></a>6.9: Usare solo i servizi di Azure approvati

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.9 | 2.6 | Customer |

Usare Criteri di Azure per limitare i servizi di cui è possibile eseguire il provisioning nell'ambiente.

- [Come configurare e gestire Criteri di AzureHow to configure and manage Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Come negare un tipo di risorsa specifico con Criteri di AzureHow to deny a specific resource type with Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

## <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Mantenere un inventario dei titoli software approvati

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.10 | 2.7 | Customer |

Usare i controlli delle applicazioni adattivi del Centro sicurezza di Azure per specificare i tipi di file a cui una regola può applicare o meno.

Implementare una soluzione di terze parti se ciò non soddisfa il requisito.

- [Come usare i controlli delle applicazioni adattive del Centro sicurezza di AzureHow to use Azure Security Center Adaptive Application Controls](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Limitare la capacità degli utenti di interagire con Azure Resource Manager

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.11 | 2,9 | Customer |

Usare l'accesso condizionale di Azure per limitare la capacità degli utenti di interagire con Gestione risorse di Azure configurando "Blocca accesso" per l'app "Gestione di Microsoft Azure".

- [Come configurare l'accesso condizionale per bloccare l'accesso a Azure Resources ManagerHow to configure Conditional Access to block access to Azure Resources Manager](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Limitare la capacità degli utenti di eseguire script all'interno di risorse di calcolo

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.12 | 2,9 | Customer |

A seconda del tipo di script, è possibile usare configurazioni specifiche del sistema operativo o risorse di terze parti per limitare la capacità degli utenti di eseguire script all'interno delle risorse di calcolo di Azure.Depending on the type of scripts, you may use operating system specific configurations or third-party resources to limit users' ability to execute scripts within Azure compute resources.  È anche possibile sfruttare i controlli delle applicazioni adattive del Centro sicurezza di Azure per garantire che venga eseguito solo il software autorizzato e che a tutto il software non autorizzato venga impedita l'esecuzione in Macchine virtuali di Azure.You can also use Azure Security Center Adaptive Application Controls to ensure that only authorized software executes and all unauthorized software is blocked from executing on Azure Virtual Machines.

- [Come controllare l'esecuzione di script di PowerShell in ambienti WindowsHow to control PowerShell script execution in Windows Environments](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [Come usare i controlli delle applicazioni adattive del Centro sicurezza di AzureHow to use Azure Security Center Adaptive Application Controls](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Separare fisicamente o logicamente le applicazioni ad alto rischio

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 6.13 | 2,9 | Customer |

Il software necessario per le operazioni aziendali, ma che può comportare rischi più elevati per l'organizzazione, deve essere isolato all'interno della propria macchina virtuale e/o di rete virtuale e sufficientemente protetto con un firewall di Azure o un gruppo di sicurezza di rete.

- [Come creare una rete virtualeHow to create a virtual network](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Come creare un gruppo di sicurezza di gruppo con una configurazione di sicurezzaHow to create an NSG with a security config](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)


## <a name="next-steps"></a>Passaggi successivi

- Vedere il prossimo controllo di sicurezza: [configurazione sicuraSee](security-control-secure-configuration.md) the next Security Control: Secure Configuration