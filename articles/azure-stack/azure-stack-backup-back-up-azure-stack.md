---
title: Eseguire il backup dello Stack di Azure | Documenti Microsoft
description: Eseguire un backup su richiesta nello Stack di Azure con backup sul posto.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: df1f4c6fadd08b17a1a1eb8bbe41ab71ae4729fc
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="back-up-azure-stack"></a>Eseguire il backup di Azure Stack

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Eseguire un backup su richiesta nello Stack di Azure con backup sul posto. Se è necessario abilitare il servizio di Backup di infrastruttura, vedere [abilitare il Backup per lo Stack di Azure dal portale di amministrazione](azure-stack-backup-enable-backup-console.md).

## <a name="start-azure-stack-backup"></a>Avviare il Backup di Azure Stack

Aprire Windows PowerShell con un prompt dei comandi con privilegi elevati ed eseguire i comandi seguenti:

   ```powershell
   Start-AzSBackup -Location $location
   ```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Confermare di backup completata nel portale di amministrazione

1. Aprire il portale di amministrazione di Azure Stack in [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).
2. Selezionare **più servizi** > **backup infrastruttura**. Scegliere **configurazione** nel **backup infrastruttura** blade.
3. Trovare il **nome** e **Data completamento** del backup nella **backup disponibili** elenco.
4. Verificare il **stato** è **Succeeded**.

È inoltre possibile verificare il backup è stata completata dal portale di amministrazione. Passare a`\MASBackup\<datetime>\<backupid>\BackupInfo.xml`

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sul flusso di lavoro per il ripristino da un evento di perdita di dati. Vedere [recupero dalla perdita di dati irreversibile](azure-stack-backup-recover-data.md).