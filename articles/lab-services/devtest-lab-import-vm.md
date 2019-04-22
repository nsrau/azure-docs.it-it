---
title: Importare macchine virtuali da un altro lab in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come importare le macchine virtuali da un altro lab nel lab corrente in Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: 9d5b7f32cb298315a5816562f548bcdafbdeb5cf
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682309"
---
# <a name="import-vms-from-another-lab-in-azure-devtest-labs"></a>Importare macchine virtuali da un altro lab in Azure DevTest Labs
Il servizio Azure DevTest Labs migliora in modo significativo la gestione delle macchine virtuali (VM) per attività di sviluppo e test. Consente di spostare una macchina virtuale da un lab a un altro se il team o i requisiti dell'infrastruttura vengono modificati. Ecco alcuni scenari comuni in cui potrebbe essere necessario eseguire questa operazione: 

- Una persona del team viene spostata in un altro gruppo all'interno dell'azienda e vuole trasferire lo sviluppo delle macchine virtuali nel lab del nuovo team.
- Il gruppo ha raggiunto la quota del livello di sottoscrizione e desidera suddividere i team in più sottoscrizioni.
- L'azienda intende passare a ExpressRoute (o a qualche altra nuova topologia di rete) e il team desidera spostare le macchine virtuali per usare questa nuova infrastruttura.

## <a name="solution-and-constraints"></a>Soluzioni e vincoli
Azure DevTest Labs consente a un proprietario del lab di importare le macchine virtuali da un lab di origine a uno di destinazione. Il proprietario del lab può facoltativamente fornire un nuovo nome per la macchina virtuale di destinazione del processo. Il processo di importazione include tutte le dipendenze quali i dischi, le pianificazioni, le impostazioni di rete e così via. Il processo potrebbe richiedere tempo e la durata dipende dal numero/dimensioni dei dischi collegati al computer di origine e la distanza dalla destinazione (ad esempio, dall'area Stati Uniti orientali all'area Asia sud-orientale). Una volta completato il processo di importazione, la macchina virtuale di origine viene arrestata e la nuova è in esecuzione nel lab di destinazione.

Esistono due vincoli chiave di cui tenere conto quando si pianifica l'importazione delle macchine virtuali in un altro lab:

- L'importazione di macchine virtuali tra sottoscrizioni e tra aree è supportata, ma le sottoscrizioni devono essere associate allo stesso tenant di Azure Active Directory.
- Le macchine virtuali non devono essere in uno stato richiedibile nel lab di origine.

Inoltre, per importare una macchina virtuale da un lab a un altro, è necessario essere il proprietario della macchina virtuale nel lab di origine e proprietario del lab nel lab di destinazione.

## <a name="steps-to-import-a-vm-from-another-lab"></a>Passaggi per importare una macchina virtuale da un altro lab
Attualmente, è possibile importare una macchina virtuale da un lab in un altro solo con Azure PowerShell e l'API REST.

### <a name="use-powershell"></a>Usare PowerShell
Scaricare il file dello script di PowerShell ImportVirtualMachines.ps1 dal [repository Git di Azure DevTest Lab](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines) nell'unità locale. 

#### <a name="import-a-single-vm"></a>Importare una singola macchina virtuale
Eseguire lo script ImportVirtualMachines.ps1 per importare una singola macchina virtuale da un lab di origine in un lab di destinazione. È possibile specificare un nuovo nome per la macchina virtuale copiata usando il parametro DestinationVirtualMachineName. 

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -SourceVirtualMachineName "<Name of the machine. Optional. If not specified, all VMs are copied>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VM is copied>" `
                            -DestinationVirtualMachineName "<New name for the VM. Optional>"
```


#### <a name="importing-all-vms"></a>Importare tutte le macchine virtuali
Quando si esegue lo script ImportVirtualMachines.ps1, se non si specifica una macchina virtuale nel lab di origine, lo script importa tutte le macchine virtuali del lab di origine nel lab di destinazione. 

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VMs are copied>"
```

### <a name="use-rest-api"></a>Usare l'API REST
Richiamare l'API REST per il lab di origine/destinazione e passare le informazioni del lab di origine, della sottoscrizione e della macchina virtuale come parametri, come illustrato nell'esempio seguente: 

```json
POST https://management.azure.com/subscriptions/<ID of the target/destination subscription>/resourceGroups/<Name of the resource group that contains the destination lab>/providers/Microsoft.DevTestLab/labs/<Name of the lab to which the VMs are copied>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<ID of the subscription that contains the source VM>/resourcegroups/<Name of the resource group that contains the source lab>/providers/microsoft.devtestlab/labs/<Name of the lab that contains the source VM>/virtualmachines/MyVm",
   destinationVirtualMachineName: "MyVmNew"
}
```

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sul ridimensionamento di una macchina virtuale, vedere l'argomento relativo al [Ridimensionamento di una VM](devtest-lab-resize-vm.md).
- Per informazioni sulla ridistribuzione di una macchina virtuale, vedere l'argomento relativo alla [Ridistribuzione di una VM](devtest-lab-redeploy-vm.md).


