---
title: le quote di CPU virtuali per Azure | Documenti Microsoft
description: Informazioni sulle quote di CPU virtuali per Azure.
keywords: 
services: virtual-machines-windows
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: drewm
ms.openlocfilehash: b481299b62d452bc306c1f9c1fa2cdccd49b818e
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="virtual-machine-vcpu-quotas"></a>Quote di CPU virtuali delle macchine virtuali

Le quote di CPU virtuali per le macchine virtuali e i set di scalabilità di macchine virtuali vengono disposte in due livelli per ogni sottoscrizione, in ogni area. Al primo livello è il totale internazionali Vcpu e il secondo livello è i vari VM dimensioni famiglia core, ad esempio Vcpu famiglia D Standard. Ogni volta che una nuova macchina virtuale è distribuita la Vcpu per la macchina virtuale appena distribuita non deve superare la quota di CPU virtuali per la famiglia di dimensioni di macchina virtuale specifica o la quota totale CPU virtuali regionali. Se una di queste quote vengono superata, quindi la distribuzione della macchina virtuale non sarà possibile. È inoltre disponibile una quota per il numero complessivo di macchine virtuali nell'area. I dettagli su ciascuna di queste quote possono essere visti nel **utilizzo + quote** sezione del **sottoscrizione** nella pagina di [portale di Azure](https://portal.azure.com), o è possibile eseguire query per i valori utilizzando PowerShell.

 
## <a name="check-usage"></a>Controllare l'utilizzo

È possibile utilizzare il [Get AzureRmVMUsage](/powershell/module/azurerm.compute/get-azurermvmusage) cmdlet per verificare l'utilizzo della quota.

```azurepowershell-interactive
Get-AzureRmVMUsage -Location "East US"
```

L'output sarà simile al seguente:

```
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional vCPUs                         4   260 Count
Virtual Machines                             4 10000 Count
Virtual Machine Scale Sets                   1  2000 Count
Standard B Family vCPUs                      1    10 Count
Standard DSv2 Family vCPUs                   1   100 Count
Standard Dv2 Family vCPUs                    2   100 Count
Basic A Family vCPUs                         0   100 Count
Standard A0-A7 Family vCPUs                  0   250 Count
Standard A8-A11 Family vCPUs                 0   100 Count
Standard D Family vCPUs                      0   100 Count
Standard G Family vCPUs                      0   100 Count
Standard DS Family vCPUs                     0   100 Count
Standard GS Family vCPUs                     0   100 Count
Standard F Family vCPUs                      0   100 Count
Standard FS Family vCPUs                     0   100 Count
Standard NV Family vCPUs                     0    24 Count
Standard NC Family vCPUs                     0    48 Count
Standard H Family vCPUs                      0     8 Count
Standard Av2 Family vCPUs                    0   100 Count
Standard LS Family vCPUs                     0   100 Count
Standard Dv2 Promo Family vCPUs              0   100 Count
Standard DSv2 Promo Family vCPUs             0   100 Count
Standard MS Family vCPUs                     0     0 Count
Standard Dv3 Family vCPUs                    0   100 Count
Standard DSv3 Family vCPUs                   0   100 Count
Standard Ev3 Family vCPUs                    0   100 Count
Standard ESv3 Family vCPUs                   0   100 Count
Standard FSv2 Family vCPUs                   0   100 Count
Standard ND Family vCPUs                     0     0 Count
Standard NCv2 Family vCPUs                   0     0 Count
Standard NCv3 Family vCPUs                   0     0 Count
Standard LSv2 Family vCPUs                   0     0 Count
Standard Storage Managed Disks               2 10000 Count
Premium Storage Managed Disks                1 10000 Count

```


## <a name="reserved-vm-instances"></a>Istanze di macchina virtuale riservate
Istanze riservate di macchina virtuale, nell'ambito di una singola sottoscrizione, verrà aggiunto un nuovo aspetto per le quote di CPU virtuali. Questi valori vengono descritti il numero di istanze della dimensione specificata deve essere distribuita nella sottoscrizione. Funzionano come segnaposto nel sistema di quote per assicurare che la quota è riservato per assicurarsi che le istanze riservate siano distribuibile nella sottoscrizione. Ad esempio, se una sottoscrizione specifica è riservato a Standard_D1 10 istanze limitare gli utilizzi per le istanze riservate Standard_D1 sarà 10. In questo modo Azure garantire che sono disponibili sempre almeno 10 Vcpu della quota di Vcpu totale internazionali da utilizzare per le istanze di Standard_D1 e sono disponibili almeno 10 Vcpu della quota di CPU virtuali famiglia D Standard da utilizzare per le istanze di Standard_D1.

Se un aumento della quota è necessario per l'acquisto di un singolo RI di sottoscrizione, è possibile [richiedere un aumento della quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) nella sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla fatturazione e quote, vedere [sottoscrizione di Azure e limiti dei servizi, quote e vincoli](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).