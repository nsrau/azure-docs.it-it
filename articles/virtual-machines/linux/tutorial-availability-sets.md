---
title: "Esercitazione sui set di disponibilità per macchine virtuali Linux in Azure | Microsoft Docs"
description: "Informazioni sui set di disponibilità per macchine virtuali Linux in Azure."
documentationcenter: 
services: virtual-machines-linux
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: d082b37a2e070136178259c54ada8dc141f81e13
ms.contentlocale: it-it
ms.lasthandoff: 05/03/2017


---

# <a name="how-to-use-availability-sets"></a>Come usare i set di disponibilità

In questa esercitazione sarà descritto come aumentare la disponibilità delle macchine virtuali (VM), inserendole in un raggruppamento logico denominato set di disponibilità. Quando si creano macchine virtuali in un set di disponibilità, la piattaforma Azure ne distribuirà il posizionamento nell'infrastruttura sottostante. Se si verifica un errore dell'hardware o viene effettuata la manutenzione pianificata della piattaforma, l'uso dei set di disponibilità garantisce che almeno una macchina virtuale rimanga in esecuzione.

I passaggi descritti in questa esercitazione possono essere eseguiti usando la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli).

## <a name="availability-set-overview"></a>Informazioni generali sui set di disponibilità

È possibile creare macchine virtuali in raggruppamenti logici di hardware nel data center di Azure sottostante. Quando si creano due o più macchine virtuali, le risorse di calcolo e di archiviazione vengono distribuite sull'hardware, cioè tra server, commutatori di rete e risorse di archiviazione. Questa distribuzione assicura la disponibilità dell'app in caso di manutenzione di un componente hardware. I set di disponibilità consentono di definire questo raggruppamento logico.

I set di disponibilità garantiscono un'elevata disponibilità per le macchine virtuali. Verificare anche che le applicazioni siano progettate per tollerare interruzioni o eventi di manutenzione.

## <a name="create-an-availability-set"></a>Creare un set di disponibilità

È possibile creare un set di disponibilità usando il comando [az vm availability-set create](/cli/azure/availability-set#create). In questo esempio sia il numero di domini di aggiornamento che quello di domini di errore viene impostato a *2* per il set di disponibilità denominato *myAvailabilitySet* nel gruppo di risorse *myResourceGroupAvailability*.

```azurecli
az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

## <a name="create-vms-inside-an-availability-set"></a>Creare macchine virtuali in un set di disponibilità

Per garantire la corretta distribuzione delle macchine virtuali in tutto l'hardware, le VM devono essere create all'interno del set di disponibilità. Non è possibile aggiungere una macchina virtuale esistente a un set di disponibilità dopo la sua creazione. 

L'hardware in un percorso è suddiviso in più domini di aggiornamento e domini di errore. I **domini di aggiornamento** sono gruppi di macchine virtuali con il relativo hardware fisico sottostante che è possibile riavviare nello stesso momento. Le macchine virtuali nello stesso **dominio di errore** condividono risorse di archiviazione comuni, nonché un alimentatore e un commutatore di rete comune. 

Quando si crea una macchina virtuale usando il comando [az vm create](/cli/azure/vm#create) si specifica il set di disponibilità usando il parametro `--availability-set` per indicare il nome del set di disponibilità.

```azurecli
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --image Canonical:UbuntuServer:14.04.4-LTS:latest \
     --admin-username azureuser \
     --generate-ssh-keys \
     --no-wait
done 
```

Sono ora presenti 2 macchine virtuali distribuite tra l'hardware sottostante. 

## <a name="check-for-available-vm-sizes"></a>Controllare le dimensioni delle macchine virtuali disponibili 

È possibile aggiungere più macchine virtuali al set di disponibilità in un secondo momento, ma è necessario conoscere le dimensioni delle macchine virtuali disponibili nell'hardware. Usare il comando [az vm availability-set list-sizes](/cli/azure/availability-set#list-sizes) per elencare tutte le dimensioni disponibili nel cluster hardware per il set di disponibilità.

```azurecli
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table  
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto l'uso dei set di disponibilità. Passare all'esercitazione successiva per informazioni sui set di scalabilità di macchine virtuali.

[Creare un set di scalabilità di macchine virtuali](tutorial-create-vmss.md)


