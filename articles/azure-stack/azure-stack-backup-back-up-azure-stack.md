---
title: Eseguire il backup dello Stack di Azure | Documenti Microsoft
description: Eseguire un backup su richiesta nello Stack di Azure con backup sul posto.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/08/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: c2a6727692a7a74b3e5fe32de8800722a9ed91b5
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
---
# <a name="back-up-azure-stack"></a>Eseguire il backup di Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Eseguire un backup su richiesta nello Stack di Azure con backup sul posto. Se è necessario abilitare il servizio di Backup di infrastruttura, vedere [abilitare il Backup per lo Stack di Azure dal portale di amministrazione](azure-stack-backup-enable-backup-console.md).

> [!Note]  
>  Per istruzioni sulla configurazione dell'ambiente di PowerShell, vedere [installazione di PowerShell per Azure Stack ](azure-stack-powershell-install.md).

## <a name="start-azure-stack-backup"></a>Avviare il backup di Azure Stack

Aprire Windows PowerShell con un prompt dei comandi con privilegi elevati nell'ambiente di gestione (operatore) ed eseguire i comandi seguenti:

```powershell
    Start-AzSBackup -Location $location.Name
```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Confermare di backup completata nel portale di amministrazione

1. Aprire il portale di amministrazione dello Stack di Azure all'indirizzo [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).
2. Selezionare **più servizi** > **backup infrastruttura**. Scegliere **configurazione** nel **backup infrastruttura** blade.
3. Trovare il **nome** e **Data completamento** del backup nella **backup disponibili** elenco.
4. Verificare il **stato** è **Succeeded**.

<!-- You can also confirm the backup completed from the administration portal. Navigate to `\MASBackup\<datetime>\<backupid>\BackupInfo.xml`

In ‘Confirm backup completed’ section, the path at the end doesn’t make sense (ie relative to what, datetime format, etc?)
\MASBackup\<datetime>\<backupid>\BackupInfo.xml -->


## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sul flusso di lavoro per il ripristino da un evento di perdita di dati. Vedere [recupero dalla perdita di dati irreversibile](azure-stack-backup-recover-data.md).
