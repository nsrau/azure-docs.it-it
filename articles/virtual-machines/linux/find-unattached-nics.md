---
title: Trovare ed eliminare le schede di rete di Azure scollegate
description: Come trovare ed eliminare le schede di interfaccia di rete di Azure che non sono collegate alle macchine virtuali con l'interfaccia della riga di comando di Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 5b28226cfe6bc51a2619c4dd63e666ddd51dad2a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87268510"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Come trovare ed eliminare le schede di interfaccia di rete scollegate per le macchine virtuali di Azure
Quando si elimina una macchina virtuale in Azure, le schede di interfaccia di rete non vengono eliminate per impostazione predefinita. Se si creano ed eliminano più macchine virtuali, le schede di interfaccia di rete inutilizzate continuano a usare i lease dell'indirizzo IP interno. Le altre schede di interfaccia di rete create nella macchina virtuale potrebbero non essere in grado di ottenere un lease IP nello spazio di indirizzi della subnet. Questo articolo illustra come trovare ed eliminare tutte le schede di interfaccia di rete scollegate.

## <a name="find-and-delete-unattached-nics"></a>Trovare ed eliminare schede di interfaccia di rete scollegate

La proprietà *virtualMachine* di una scheda di interfaccia di rete archivia l'ID e il gruppo di risorse della macchina virtuale a cui è collegata la scheda di interfaccia di rete. Lo script seguente esegue un ciclo in tutte le schede di interfaccia di rete di una sottoscrizione verificando se la proprietà *virtualMachine* è Null. Se la proprietà è Null, la scheda di interfaccia di rete non è collegata a una macchina virtuale.

Per visualizzare tutte le schede di interfaccia di rete scollegate, è consigliabile eseguire prima lo script con la variabile *deleteUnattachedNics* impostata su *0*. Per eliminare tutte le schede di interfaccia di rete scollegate dopo aver verificato l'output di tipo elenco, eseguire lo script con la variabile *deleteUnattachedNics* impostata su *1*.

```azurecli
# Set deleteUnattachedNics=1 if you want to delete unattached NICs
# Set deleteUnattachedNics=0 if you want to see the Id(s) of the unattached NICs
deleteUnattachedNics=0

unattachedNicsIds=$(az network nic list --query '[?virtualMachine==`null`].[id]' -o tsv)
for id in ${unattachedNicsIds[@]}
do
   if (( $deleteUnattachedNics == 1 ))
   then

       echo "Deleting unattached NIC with Id: "$id
       az network nic delete --ids $id
       echo "Deleted unattached NIC with Id: "$id
   else
       echo $id
   fi
done
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come creare e gestire reti virtuali in Azure, vedere [Create and manage VM networks](tutorial-virtual-network.md) (Creare e gestire reti di macchine virtuali).
