---
title: Modulo di PowerShell per Azure Lab Services - Documenti Microsoft
description: Questo articolo fornisce informazioni su un modulo di PowerShell che consente di gestire gli elementi in Azure Lab Services.This article provides information about a PowerShell module that helps with managing artifacts in Azure Lab Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73609398"
---
# <a name="azlabservices-powershell-module-preview"></a>Modulo Az.LabServices di PowerShell (anteprima)
Az.LabServices è un modulo di PowerShell che semplifica la gestione dei servizi di Azure Lab.Az.LabServices is a PowerShell module that seimplifies the management of Azure Lab services. Fornisce funzioni componibili per creare, eseguire query, aggiornare ed eliminare account lab, lab, macchine virtuali e immagini. Per ulteriori informazioni su questo modulo, vedere la [home page di Az.LabServices in GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Questo modulo è in **anteprima**. 

## <a name="example-command"></a>Comando di esempio
Ecco un esempio di utilizzo di un comando di PowerShell per arrestare tutte le macchine virtuali in esecuzione in tutti i lab.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Introduzione
1. Installare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) se non esiste nel computer. 
2. Scaricare [Az.LabServices.psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) nel computer.
3. Importare il modulo:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Eseguire il comando seguente per elencare tutti i lab nella sottoscrizione.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Passaggi successivi
Vedere la [home page di Az.LabServices in GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).
