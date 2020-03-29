---
title: Ricevere notifiche di manutenzione tramite l'interfaccia della riga di comandoGet maintenance notifications using the CLI
description: Visualizzare le notifiche di manutenzione per le macchine virtuali in esecuzione in Azure e avviare la manutenzione self-service usando l'interfaccia della riga di comando di Azure.View maintenance notifications for virtual machines running in Azure, and start self-service maintenance, using the Azure CLI.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 4ad57c1c71a51f948bd405a5487a1e27e36bfff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920893"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Gestione delle notifiche di manutenzione pianificata tramite l'interfaccia della riga di comando di AzureHandling planned maintenance notifications using the Azure CLI

**Questo articolo si applica alle macchine virtuali che eseguono sia Linux che Windows.This article applies to virtual machines running both Linux and Windows.**

È possibile usare l'interfaccia della riga di comando per vedere quando le macchine virtuali sono pianificate per [la manutenzione.](maintenance-notifications.md) Le informazioni sulla manutenzione pianificata sono disponibili da [az vm get-instance-view](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view).
 
Le informazioni vengono restituite solo se è presente una manutenzione pianificata. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>Avviare la manutenzione

La chiamata seguente avvierà la `IsCustomerInitiatedMaintenanceAllowed` manutenzione in una macchina virtuale se è impostata su true.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Distribuzioni classiche

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Se si dispone ancora di macchine virtuali legacy distribuite tramite il modello di distribuzione classico, è possibile usare l'interfaccia della riga di comando classica di Azure per eseguire query per le macchine virtuali e avviare la manutenzione.

Verificare che sia attiva la modalità corretta per operare con una VM classica digitando:

```
azure config mode asm
```

Per ottenere lo stato di manutenzione della macchina virtuale denominata *myVM*, digitare:

```
azure vm show myVM 
``` 

Per avviare la manutenzione della macchina virtuale classica denominata *myVM* nel servizio *myService* e nella distribuzione *myDeployment*, digitare:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Passaggi successivi

È anche possibile gestire la manutenzione pianificata usando [Azure PowerShell](maintenance-notifications-powershell.md) o il [portale.](maintenance-notifications-portal.md)
