---
title: Gestire un pool di capacità QoS manuale per Azure NetApp Files | Microsoft Docs
description: Viene descritto come gestire un pool di capacità che utilizza il tipo QoS manuale, inclusa la configurazione di un pool di capacità QoS manuale e la modifica di un pool di capacità per l'utilizzo di QoS manuale.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: d2abb66684738e97ade2a2e67309a4e7b53c4734
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91442092"
---
# <a name="manage-a-manual-qos-capacity-pool"></a>Gestire un pool di capacità QoS manuale

Questo articolo descrive come gestire un pool di capacità che usa il tipo QoS manuale.  

Per informazioni sulle considerazioni sui tipi QoS, vedere la [gerarchia di archiviazione di Azure NetApp files](azure-netapp-files-understand-storage-hierarchy.md) e [Azure NetApp files considerazioni sulle prestazioni](azure-netapp-files-performance-considerations.md) .  

## <a name="register-the-feature"></a>Registrare la funzionalità
La funzionalità di tipo QoS manuale è attualmente in anteprima. Se si utilizza questa funzionalità per la prima volta, è necessario prima registrare la funzionalità.
  
1.  Registrare la funzionalità:

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFFlexPool
    ```

2. Verificare lo stato della registrazione della funzionalità: 

    > [!NOTE]
    > Il **RegistrationState** potrebbe trovarsi nello `Registering` stato per un massimo di 60 minuti prima di modificare in `Registered` . Prima di continuare, attendere che lo stato sia **registrato** .

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFFlexPool
    ```
È anche possibile usare i [comandi dell'interfaccia](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest&preserve-view=true) `az feature register` della riga di comando di Azure e `az feature show` registrare la funzionalità e visualizzare lo stato della registrazione. 

## <a name="set-up-a-new-manual-qos-capacity-pool"></a>Configurare un nuovo pool di capacità QoS manuale 

Per creare un nuovo pool di capacità usando il tipo QoS manuale:

1. Seguire i passaggi in [configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md).  

2. Nella finestra nuovo pool di capacità selezionare il tipo di **QoS manuale** .  

## <a name="change-a-capacity-pool-to-use-manual-qos"></a><a name="change-to-qos"></a>Modificare un pool di capacità per usare QoS manuale

È possibile modificare un pool di capacità che attualmente usa il tipo QoS automatico per usare il tipo QoS manuale.  

> [!IMPORTANT]
> L'impostazione del tipo di capacità su QoS manuale è una modifica permanente. Non è possibile convertire uno strumento di capacità del tipo QoS manuale in un pool di capacità QoS automatica. 

1. Dal pannello di gestione per l'account NetApp fare clic su **pool di capacità** per visualizzare i pool di capacità esistenti.   
 
2.  Fare clic sul pool di capacità a cui si desidera passare utilizzando QoS manuale.

3.  Fare clic su **modifica tipo QoS**. Impostare quindi **nuovo tipo di QoS** su **manuale**. Fare clic su **OK**. 

![Modificare il tipo QoS](../media/azure-netapp-files/change-qos-type.png)


## <a name="monitor-the-throughput-of-a-manual-qos-capacity-pool"></a>Monitorare la velocità effettiva di un pool di capacità QoS manuale  

Sono disponibili metriche che consentono di monitorare la velocità effettiva di lettura e scrittura di un volume.  Vedere [metriche per Azure NetApp files](azure-netapp-files-metrics.md).  

## <a name="modify-the-allotted-throughput-of-a-manual-qos-volume"></a>Modificare la velocità effettiva assegnata di un volume QoS manuale 

Se un volume è incluso in un pool di capacità QoS manuale, è possibile modificare la velocità effettiva del volume assegnata in base alle esigenze.

1. Nella pagina **volumi** selezionare il volume di cui si desidera modificare la velocità effettiva.   

2. Fare clic su **modifica velocità effettiva**. Specificare la **velocità effettiva (MIB/S)** desiderata. Fare clic su **OK**. 

    ![Modificare la velocità effettiva QoS](../media/azure-netapp-files/change-qos-throughput.png)

## <a name="next-steps"></a>Passaggi successivi  

* [Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)
* [Metriche per Azure NetApp Files](azure-netapp-files-metrics.md)
* [Considerazioni sulle prestazioni per Azure NetApp Files](azure-netapp-files-performance-considerations.md)
* [Risolvere i problemi del pool di capacità](troubleshoot-capacity-pools.md)
* [Gerarchia di archiviazione di Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Livelli di servizio per Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Modello di costi per Azure NetApp Files](azure-netapp-files-cost-model.md)
* [Limiti delle risorse per Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Creare un volume NFS](azure-netapp-files-create-volumes.md)
* [Creare un volume SMB](azure-netapp-files-create-volumes-smb.md)
* [Creare un volume con doppio protocollo](create-volumes-dual-protocol.md)


