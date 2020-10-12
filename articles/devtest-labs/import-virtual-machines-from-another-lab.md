---
title: Importa le macchine virtuali da un altro Lab in Azure DevTest Labs
description: Questo articolo descrive come importare le macchine virtuali da un altro Lab nel Lab corrente in Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0f664a0ae399575ee936565adaf7364fd1c5ce5c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85475935"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Importa le macchine virtuali da un altro Lab in Azure DevTest Labs
Questo articolo fornisce informazioni su come importare le macchine virtuali da un altro Lab nel Lab.

## <a name="scenarios"></a>Scenari
Di seguito sono riportati alcuni scenari in cui è necessario importare VM da un Lab in un altro Lab:

- Un utente del team sta migrando a un altro gruppo all'interno dell'organizzazione e vuole portare il desktop dello sviluppatore nei laboratori DevTest del nuovo team.
- Il gruppo ha raggiunto una [quota a livello di sottoscrizione](../azure-resource-manager/management/azure-subscription-service-limits.md) e vuole suddividere i team in alcune sottoscrizioni
- La società sta passando a Express Route (o a un'altra nuova topologia di rete) e il team vuole spostare le macchine virtuali per l'uso di questa nuova infrastruttura

## <a name="solution-and-constraints"></a>Soluzioni e vincoli
Questa funzionalità consente di importare le macchine virtuali in un Lab (origine) in un altro Lab (destinazione). Facoltativamente, è possibile assegnare un nuovo nome per la macchina virtuale di destinazione nel processo. Il processo di importazione include tutte le dipendenze, ad esempio i dischi, le pianificazioni, le impostazioni di rete e così via.

Il processo può richiedere del tempo ed è influenzato dai fattori seguenti:

- Numero/dimensioni dei dischi collegati alla macchina di origine (poiché si tratta di un'operazione di copia e non di un'operazione di spostamento)
- Distanza dalla destinazione (ad esempio, l'area Stati Uniti orientali verso l'Asia sudorientale).

Al termine del processo, la macchina virtuale di origine rimane arrestata e la nuova viene eseguita nel Lab di destinazione.

Quando si pianifica l'importazione di macchine virtuali da un Lab in a un altro Lab, è necessario tenere presenti due vincoli principali:

- Sono supportate le importazioni di macchine virtuali tra sottoscrizioni e tra le aree, ma le sottoscrizioni devono essere associate allo stesso tenant Azure Active Directory.
- Le macchine virtuali non devono essere in uno stato attestabile nel Lab di origine.
- L'utente è il proprietario della macchina virtuale nel Lab di origine e il proprietario del Lab nel Lab di destinazione.
- Attualmente questa funzionalità è supportata solo tramite PowerShell e l'API REST.

## <a name="use-powershell"></a>Usare PowerShell
Scaricare ImportVirtualMachines.ps1 file da [GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). È possibile usare lo script per importare una singola macchina virtuale o tutte le macchine virtuali nel Lab di origine nel Lab di destinazione.

### <a name="use-powershell-to-import-a-single-vm"></a>Usare PowerShell per importare una singola macchina virtuale
Per eseguire questo script di PowerShell è necessario identificare la macchina virtuale di origine e il Lab di destinazione e, facoltativamente, specificare un nuovo nome da usare per il computer di destinazione:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Usare PowerShell per importare tutte le macchine virtuali nel Lab di origine
Se la macchina virtuale di origine non è specificata, lo script importa automaticamente tutte le macchine virtuali in DevTest Labs.  Ad esempio:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Usare REST HTTP per importare una macchina virtuale
La chiamata REST è semplice. Si forniscono informazioni sufficienti per identificare le risorse di origine e di destinazione. Tenere presente che l'operazione viene eseguita sulla risorsa lab di destinazione.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Impostare i criteri per un lab](devtest-lab-set-lab-policy.md)
- [Domande frequenti](devtest-lab-faq.md)
