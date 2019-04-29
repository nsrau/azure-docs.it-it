---
title: Importare le macchine virtuali da un altro lab in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come importare le macchine virtuali da un altro lab in lab corrente.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: ca6ed58cfabb5027830828812c4820c1b586875c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61322875"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Importare le macchine virtuali da un altro lab in Azure DevTest Labs
Questo articolo fornisce informazioni su come importare le macchine virtuali da un altro lab in lab.

## <a name="scenarios"></a>Scenari
Ecco alcuni scenari in cui è necessario importare le macchine virtuali da un lab in lab un'altra:

- Una persona del team viene spostata in un altro gruppo all'interno dell'organizzazione e desidera adottare il desktop per gli sviluppatori in DevTest Labs del nuovo team.
- Il gruppo ha raggiunto un [quota a livello di sottoscrizione](../azure-subscription-service-limits.md) e si vuole suddividere i team in alcune sottoscrizioni
- L'azienda si prepari per Express Route (o alcuni altri nuova topologia di rete) e il team desidera spostare le macchine virtuali per usare questa nuova infrastruttura

## <a name="solution-and-constraints"></a>Soluzioni e vincoli
Questa funzionalità consente di importare le macchine virtuali in un laboratorio (origine) in un'altra esercitazione (destinazione). È facoltativamente possibile assegnare un nuovo nome per la macchina virtuale di destinazione del processo. Il processo di importazione include tutte le dipendenze, ad esempio dischi, le pianificazioni, le impostazioni di rete e così via.

Il processo potrebbe richiedere molto tempo ed è interessato dai fattori seguenti:

- Numero o le dimensioni dei dischi collegati al computer di origine (poiché è un'operazione di copia e non un'operazione di spostamento)
- Distanza e la destinazione (ad esempio, area Stati Uniti orientali nell'area Asia sud-orientale).

Una volta completato il processo, la macchina virtuale di origine rimane arresto e il nuovo uno è in esecuzione nell'ambiente di laboratorio di destinazione.

Esistono due vincoli di chiave da tenere presenti quando si intende importare le macchine virtuali da un lab in un'altra lab:

- Importa macchina virtuale tra sottoscrizioni e aree supportate, ma le sottoscrizioni devono essere associate allo stesso tenant di Azure Active Directory.
- Le macchine virtuali non deve essere in uno stato a disposizione degli utenti nel lab di origine.
- Si è il proprietario della macchina virtuale nel lab di origine e del proprietario del lab nell'ambiente di laboratorio di destinazione.
- Attualmente, questa funzionalità è supportata solo tramite Powershell e API REST.

## <a name="use-powershell"></a>Usare PowerShell
Scaricare il file ImportVirtualMachines.ps1 dal [GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). È possibile usare lo script per importare tutte le macchine virtuali del lab di origine o una singola macchina virtuale del lab di destinazione.

### <a name="use-powershell-to-import-a-single-vm"></a>Usare PowerShell per importare una singola macchina virtuale
L'esecuzione di questo script di powershell richiede che identifica la macchina virtuale di origine e il lab di destinazione e, facoltativamente, fornire un nuovo nome da utilizzare per la macchina di destinazione:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Usare PowerShell per importare tutte le macchine virtuali del lab di origine
Se la macchina virtuale di origine non è specificato, lo script importa automaticamente tutte le macchine virtuali in DevTest Labs.  Ad esempio: 

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Usare REST HTTP per importare una macchina virtuale
La chiamata REST è semplice. Fornire informazioni sufficienti per identificare le risorse di origine e destinazione. Tenere presente che l'operazione viene eseguita sulla risorsa di lab di destinazione.

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
