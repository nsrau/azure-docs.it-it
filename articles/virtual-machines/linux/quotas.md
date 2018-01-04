---
title: le quote di CPU virtuali per Azure | Documenti Microsoft
description: Informazioni sulle quote di CPU virtuali per Azure.
keywords: 
services: virtual-machines-linux
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: drewm
ms.openlocfilehash: 8b99fd92d9031b7172e698cf5db1f776387bdfb9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="virtual-machine-vcpu-quotas"></a>Quote di CPU virtuali delle macchine virtuali

Le quote di CPU virtuali per le macchine virtuali e i set di scalabilità di macchine virtuali vengono disposte in due livelli per ogni sottoscrizione, in ogni area. Al primo livello è il totale internazionali Vcpu e il secondo livello è i vari VM dimensioni famiglia core, ad esempio Vcpu famiglia D Standard. Ogni volta che una nuova macchina virtuale è distribuita la Vcpu per la macchina virtuale appena distribuita non deve superare la quota di CPU virtuali per la famiglia di dimensioni di macchina virtuale specifica o la quota totale CPU virtuali regionali. Se una di queste quote vengono superata, quindi la distribuzione della macchina virtuale non sarà possibile. È inoltre disponibile una quota per il numero complessivo di macchine virtuali nell'area. I dettagli su ciascuna di queste quote possono essere visti nel **utilizzo + quote** sezione del **sottoscrizione** nella pagina di [portale di Azure](https://portal.azure.com), o è possibile eseguire query per i valori usando l'interfaccia CLI di Azure .


## <a name="check-usage"></a>Controllare l'utilizzo

È possibile controllare l'utilizzo della quota utilizzando [elenco-utilizzo delle vm az](/cli/azure/vm#az_vm_list_usage).

```azurecli-interactive
az vm list-usage --location "East US"
[
  …
  {
    "currentValue": 4,
    "limit": 260,
    "name": {
      "localizedValue": "Total Regional vCPUs",
      "value": "cores"
    }
  },
  {
    "currentValue": 4,
    "limit": 10000,
    "name": {
      "localizedValue": "Virtual Machines",
      "value": "virtualMachines"
    }
  },
  {
    "currentValue": 1,
    "limit": 2000,
    "name": {
      "localizedValue": "Virtual Machine Scale Sets",
      "value": "virtualMachineScaleSets"
    }
  },
  {
    "currentValue": 1,
    "limit": 10,
    "name": {
      "localizedValue": "Standard B Family vCPUs",
      "value": "standardBFamily"
    }
  },
```
## <a name="reserved-vm-instances"></a>Istanze di macchina virtuale riservate
Istanze riservate di macchina virtuale, nell'ambito di una singola sottoscrizione, verrà aggiunto un nuovo aspetto per le quote di CPU virtuali. Questi valori vengono descritti il numero di istanze della dimensione specificata deve essere distribuita nella sottoscrizione. Funzionano come segnaposto nel sistema di quote per assicurare che la quota è riservato per assicurarsi che le istanze riservate siano distribuibile nella sottoscrizione. Ad esempio, se una sottoscrizione specifica è riservato a Standard_D1 10 istanze limitare gli utilizzi per le istanze riservate Standard_D1 sarà 10. In questo modo Azure garantire che sono disponibili sempre almeno 10 Vcpu della quota di Vcpu totale internazionali da utilizzare per le istanze di Standard_D1 e sono disponibili almeno 10 Vcpu della quota di CPU virtuali famiglia D Standard da utilizzare per le istanze di Standard_D1.

Se un aumento della quota è necessario per l'acquisto di un singolo RI di sottoscrizione, è possibile [richiedere un aumento della quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) nella sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla fatturazione e quote, vedere [sottoscrizione di Azure e limiti dei servizi, quote e vincoli](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json).
