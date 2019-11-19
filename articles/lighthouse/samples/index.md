---
title: Esempi e modelli di Azure Lighthouse
description: Questi esempi e modelli di Azure Resource Manager mostrano come eseguire l'onboarding di clienti per la gestione risorse delegate di Azure e supportare scenari di Azure Lighthouse.
ms.topic: sample
ms.date: 10/17/2019
ms.openlocfilehash: 20c1fc62c83202f97ed9cff68fd285280d94a321
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132155"
---
# <a name="azure-lighthouse-samples"></a>Esempi di Azure Lighthouse

La tabella seguente include collegamenti ai modelli di Azure Resource Manager per Azure Lighthouse. Questi e altri file sono anche disponibili nel [repository di esempi di Azure Lighthouse](https://github.com/Azure/Azure-Lighthouse-samples/).

## <a name="onboarding-customers-to-azure-delegated-resource-management"></a>Eseguire l'onboarding dei clienti nella gestione risorse delegate di Azure

Sono disponibili diversi modelli in base a specifici scenari di onboarding. Scegliere l'opzione più indicata e assicurarsi di modificare il file dei parametri in base all'ambiente. Per altre informazioni su come usare questi file nella distribuzione, vedere [Eseguire l'onboarding di un cliente nella gestione risorse delegate](../how-to/onboard-customer.md).

| **Modello** | **Descrizione** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Esegue l'onboarding della sottoscrizione di un cliente nella gestione risorse delegate di Azure. Per ogni sottoscrizione è necessario eseguire una distribuzione diversa. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Esegue l'onboarding di uno o più gruppi di risorse del cliente nella gestione risorse delegate di Azure. Usare **rgDelegatedResourceManagement** per un singolo gruppo di risorse oppure **multipleRgDelegatedResourceManagement** per eseguire l'onboarding di più gruppi di risorse nella stessa sottoscrizione. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Se è stata [pubblicata un'offerta di servizi gestiti in Azure Marketplace](../how-to/publish-managed-services-offers.md), facoltativamente è possibile usare questo modello per eseguire l'onboarding delle risorse per i clienti che l'hanno accettata. I valori del marketplace nel file di parametri devono corrispondere ai valori usati quando è stata pubblicata l'offerta. |

In genere per ogni sottoscrizione di cui si esegue l'onboarding è necessaria una distribuzione distinta, ma è anche possibile distribuire i modelli in più sottoscrizioni.

| **Modello** | **Descrizione** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Distribuisce modelli di Azure Resource Manager tra più sottoscrizioni. |

## <a name="azure-policy"></a>Criteri di Azure

Questi esempi mostrano come usare Criteri di Azure con le sottoscrizioni di cui è stato eseguito l'onboarding per la gestione risorse delegate.

| **Modello** | **Descrizione** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) | Assegna un criterio che aggiunge o rimuove un tag (usando l'effetto modify) in una sottoscrizione delegata. Per altre informazioni, vedere [Distribuire un criterio che è possibile correggere all'interno di una sottoscrizione delegata](../how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-audit-delegation) | Assegna un criterio che controlla le assegnazioni di delega. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) | Assegna un criterio che abilita la diagnostica per le risorse di Azure Key Vault in una sottoscrizione delegata (usando l'effetto deployIfNotExists). Per altre informazioni, vedere [Distribuire un criterio che è possibile correggere all'interno di una sottoscrizione delegata](../how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-sub-monitoring) | Assegna diversi criteri per abilitare la diagnostica in una sottoscrizione delegata e connette tutte le VM Windows e Linux all'area di lavoro Log Analytics creata dal criterio. Per altre informazioni, vedere [Distribuire un criterio che è possibile correggere all'interno di una sottoscrizione delegata](../how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-initiative) | Applica un'iniziativa (più definizioni di criteri correlate) a una sottoscrizione delegata. |

## <a name="azure-monitor"></a>Monitoraggio di Azure

Questi esempi illustrano come usare Monitoraggio di Azure per creare avvisi per le sottoscrizioni di cui è stato eseguito l'onboarding per la gestione risorse delegate di Azure.

| **Modello** | **Descrizione** |
|---------|---------|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | Questo modello crea un avviso di Azure e si connette a un gruppo di azioni esistente.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Crea più avvisi dei log basati su query Kusto.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | Distribuisce un avviso in un tenant quando un utente delega una sottoscrizione a un tenant per la gestione.|

## <a name="additional-cross-tenant-scenarios"></a>Altri scenari tra tenant

Questi esempi illustrano varie attività che è possibile eseguire in scenari di gestione tra tenant.

| **Modello** | **Descrizione** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | Distribuisce account di archiviazione in due gruppi di risorse diversi.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Crea servizi di gestione di Azure, li collega e distribuisce soluzioni aggiuntive. Per una distribuzione end-to-end, usare il modello **rgWithAzureMgmt.json**. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | Abilita e configura Centro sicurezza di Azure all'interno della sottoscrizione di Azure di destinazione. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-sentinel) | Distribuisce e abilita Azure Sentinel in un'area di lavoro Log Analytics esistente in una sottoscrizione delegata. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-log-analytics-vm-extensions) | Questi modelli consentono di distribuire le estensioni VM di Log Analytics nelle VM Windows e Linux, connettendole all'area di lavoro Log Analytics designata |

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulla [gestione risorse delegate di Azure](../concepts/azure-delegated-resource-management.md).
- Esplorare il [Repository di esempi di Azure Lighthouse](https://github.com/Azure/Azure-Lighthouse-samples/).
