---
title: Importare macchine virtuali da un altro lab in Azure DevTest Labs
description: Questo articolo descrive come importare macchine virtuali da un altro lab nel lab corrente in Lab DevTest di Azure.This article describes how to import virtual machines from another lab into the current lab in Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 299d5c8758a13edded63b99abb2f12ddf9fa14be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759517"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Importare macchine virtuali da un altro lab in Azure DevTest Labs
In questo articolo vengono fornite informazioni su come importare macchine virtuali da un altro lab nel lab.

## <a name="scenarios"></a>Scenari
Ecco alcuni scenari in cui è necessario importare macchine virtuali da un lab a un altro lab:Here are some scenarios where you need to import VMs from one lab into another lab:

- Un individuo del team sta passando a un altro gruppo all'interno dell'azienda e desidera portare il desktop dello sviluppatore ai laboratori DevTest del nuovo team.
- Il gruppo ha raggiunto una quota a livello di [sottoscrizione](../azure-resource-manager/management/azure-subscription-service-limits.md) e vuole dividere i team in alcuni abbonamenti
- L'azienda sta passando a Express Route (o qualche altra nuova topologia di rete) e il team desidera spostare le macchine virtuali per utilizzare questa nuova infrastruttura

## <a name="solution-and-constraints"></a>Soluzioni e vincoli
Questa funzionalità consente di importare macchine virtuali in un lab (origine) in un altro lab (destinazione). Facoltativamente, è possibile assegnare un nuovo nome per la macchina virtuale di destinazione nel processo. Il processo di importazione include tutte le dipendenze come dischi, pianificazioni, impostazioni di rete e così via.

Il processo richiede un certo tempo ed è influenzato dai seguenti fattori:

- Numero/dimensione dei dischi collegati al computer di origine (poiché si tratta di un'operazione di copia e non di un'operazione di spostamento)
- Distanza dalla destinazione (ad esempio, regione degli Stati Uniti orientali al sud-est asiatico).

Al termine del processo, la macchina virtuale di origine rimane arrestata e quella nuova viene eseguita nel lab di destinazione.

Esistono due vincoli chiave da tenere presenti quando si pianifica l'importazione di macchine virtuali da un lab a un altro lab:There are two key constraints to be aware of when planning to import VMs from one lab in to another lab:

- Le importazioni di macchine virtuali tra sottoscrizioni e aree sono supportate, ma le sottoscrizioni devono essere associate allo stesso tenant di Azure Active Directory.Virtual Machine imports across subscriptions and across regions are supported, but the subscriptions must be associated to the same Azure Active Directory tenant.
- Le macchine virtuali non devono essere in uno stato richiedente nel lab di origine.
- Si è il proprietario della macchina virtuale nel lab di origine e il proprietario del lab nel lab di destinazione.
- Attualmente, questa funzionalità è supportata solo tramite Powershell e l'API REST.

## <a name="use-powershell"></a>Usare PowerShell
Scaricare il file ImportVirtualMachines.ps1 da [GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). È possibile usare lo script per importare una singola macchina virtuale o tutte le macchine virtuali nel lab di origine nel lab di destinazione.

### <a name="use-powershell-to-import-a-single-vm"></a>Usare PowerShell per importare una singola macchina virtualeUse PowerShell to import a single VM
L'esecuzione di questo script di PowerShell richiede l'identificazione della macchina virtuale di origine e il lab di destinazione e, facoltativamente, la fornitura di un nuovo nome da usare per il computer di destinazione:Executing this powershell script requires identifying the source VM and the destination lab, and optionally for menta to a new name to use for the destination machine:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Usare PowerShell per importare tutte le macchine virtuali nel lab di origineUse PowerShell to import all VMs in the source lab
Se la macchina virtuale di origine non è specificata, lo script importa automaticamente tutte le macchine virtuali nei laboratori DevTest.  Ad esempio:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Usare REST HTTP per importare una macchina virtualeUse HTTP REST to import a VM
La chiamata REST è semplice. È possibile fornire informazioni sufficienti per identificare le risorse di origine e di destinazione. Tenere presente che l'operazione viene eseguita nella risorsa lab di destinazione.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Impostare i criteri per un lab](devtest-lab-get-started-with-lab-policies.md)
- [Domande frequenti](devtest-lab-faq.md)
