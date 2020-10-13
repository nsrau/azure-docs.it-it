---
title: Abilitare Monitoraggio di Azure per le macchine virtuali con i modelli di Resource Manager
description: Questo articolo descrive come abilitare Monitoraggio di Azure per le macchine virtuali per una o più macchine virtuali di Azure o set di scalabilità di macchine virtuali usando Azure PowerShell o Azure Resource Manager modelli.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 89a9a1b762e02237a8ee08dca5d6eedefabaafbb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87328229"
---
# <a name="enable-azure-monitor-for-vms-using-resource-manager-templates"></a>Abilitare Monitoraggio di Azure per le macchine virtuali con i modelli di Resource Manager
Questo articolo descrive come abilitare Monitoraggio di Azure per le macchine virtuali per una macchina virtuale o un set di scalabilità di macchine virtuali usando i modelli Gestione risorse. Questa procedura può essere utilizzata per gli elementi seguenti:

- Macchina virtuale di Azure
- Set di scalabilità di macchine virtuali di Azure
- Macchina virtuale ibrida connessa ad Azure Arc

## <a name="prerequisites"></a>Prerequisiti

- [Creare e configurare un'area di lavoro log Analytics](vminsights-configure-workspace.md). 
- Vedere [sistemi operativi supportati](vminsights-enable-overview.md#supported-operating-systems) per assicurarsi che il sistema operativo della macchina virtuale o del set di scalabilità di macchine virtuali che si sta abilitando sia supportato. 

## <a name="resource-manager-templates"></a>Modelli di Gestione risorse

Sono stati creati esempi Azure Resource Manager modelli per l'onboarding delle macchine virtuali e dei set di scalabilità di macchine virtuali. Questi modelli includono scenari che è possibile usare per abilitare il monitoraggio in una risorsa esistente e creare una nuova risorsa con monitoraggio abilitato.

>[!NOTE]
>Il modello deve essere distribuito nello stesso gruppo di risorse della macchina virtuale o del set di scalabilità di macchine virtuali in fase di abilitazione.


I modelli di Azure Resource Manager sono disponibili in un file di archivio (con estensione zip) che è possibile [scaricare](https://aka.ms/VmInsightsARMTemplates) dal repository GitHub. Contenuto delle cartelle di inclusione di file che rappresentano ogni scenario di distribuzione con un file di modello e di parametri. Prima di eseguirli, modificare il file dei parametri e specificare i valori necessari. 

Il file di download contiene i modelli seguenti per diversi scenari:

- Il modello **ExistingVmOnboarding** Abilita monitoraggio di Azure per le macchine virtuali se la macchina virtuale esiste già.
- Il modello **NewVmOnboarding** crea una macchina virtuale e Abilita monitoraggio di Azure per le macchine virtuali per monitorarla.
- Il modello **ExistingVmssOnboarding** Abilita monitoraggio di Azure per le macchine virtuali se il set di scalabilità di macchine virtuali esiste già.
- Il modello **NewVmssOnboarding** Crea set di scalabilità di macchine virtuali e consente monitoraggio di Azure per le macchine virtuali di monitorarli.
- Il modello **ConfigureWorkspace** configura l'area di lavoro di log Analytics per supportare monitoraggio di Azure per le macchine virtuali abilitando le soluzioni e la raccolta dei contatori delle prestazioni del sistema operativo Linux e Windows.

>[!NOTE]
>Se i set di scalabilità di macchine virtuali sono già presenti e i criteri di aggiornamento sono impostati su **manuale**, per impostazione predefinita monitoraggio di Azure per le macchine virtuali non verrà abilitato per le istanze dopo l'esecuzione del modello di Azure Resource Manager **ExistingVmssOnboarding** . È necessario aggiornare manualmente le istanze.

## <a name="deploy-templates"></a>Distribuire modelli
I modelli possono essere distribuiti usando [qualsiasi metodo di distribuzione per i modelli di gestione risorse](../../azure-resource-manager/templates/deploy-powershell.md) inclusi gli esempi seguenti con PowerShell e l'interfaccia della riga di comando.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```


```azurecli
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```



## <a name="next-steps"></a>Passaggi successivi

Ora che il monitoraggio è abilitato per le macchine virtuali, queste informazioni sono disponibili per l'analisi con Monitoraggio di Azure per le macchine virtuali.

- Per visualizzare le dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md).

- Per identificare i colli di bottiglia e l'utilizzo complessivo delle prestazioni della VM, vedere [visualizzare le prestazioni delle VM di Azure](vminsights-performance.md).
