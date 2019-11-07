---
title: Modulo PowerShell per Azure Lab Services | Microsoft Docs
description: Questo articolo fornisce informazioni su un modulo di PowerShell che consente di gestire gli artefatti in Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2019
ms.author: spelluru
ms.openlocfilehash: cc82355ee43f9fee4f9c2e1bb1bcc0481e4dcea3
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609398"
---
# <a name="azlabservices-powershell-module-preview"></a>Modulo di PowerShell AZ. LabServices (anteprima)
AZ. LabServices è un modulo di PowerShell che semplifica la gestione di Azure Lab Services. Offre funzioni componibili per la creazione, l'esecuzione di query, l'aggiornamento e l'eliminazione di account Lab, Lab, macchine virtuali e immagini. Per ulteriori informazioni su questo modulo, vedere [AZ. LabServices Home page su GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Questo modulo è in **Anteprima**. 

## <a name="example-command"></a>Comando di esempio
Di seguito è riportato un esempio di utilizzo di un comando di PowerShell per arrestare tutte le macchine virtuali in esecuzione in tutti i Lab.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Introduzione
1. Installare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) se non esiste nel computer. 
2. Scaricare [AZ. LabServices. psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) nel computer.
3. Importare il modulo:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Eseguire il comando seguente per elencare tutti i Lab nella sottoscrizione.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Passaggi successivi
Vedere [AZ. LabServices Home page su GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).
