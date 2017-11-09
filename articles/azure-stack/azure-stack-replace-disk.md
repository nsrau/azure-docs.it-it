---
title: Sostituire un disco fisico nello Stack di Azure | Documenti Microsoft
description: Viene descritto il processo per informazioni su come sostituire un disco fisico nello Stack di Azure.
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 26e5e9f8882cc764922a2cbf0f39e7a3d1b6995b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Sostituire un disco fisico nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

In questo articolo viene descritto il processo generale per sostituire un disco fisico nello Stack di Azure. Se un disco fisico non riesce, è necessario sostituire appena possibile.

È possibile utilizzare questa procedura per sistemi integrati e per le distribuzioni di kit di sviluppo che dispongono di dischi a caldo.

Sostituzione del disco effettivo passaggi variano in base al fornitore dell'hardware (OEM) original equipment manufacturer. Vedere la documentazione del fornitore campo unità sostituibili (FRU) per i passaggi dettagliati che sono specifiche per il sistema. 

## <a name="review-disk-alert-information"></a>Informazioni sugli avvisi di revisione del disco
Quando un disco ha esito negativo, viene visualizzato un avviso che informa che la connettività è stata interrotta a un disco fisico. 

 ![Ha perso la connettività con avviso con disco fisico](media/azure-stack-replace-disk/DiskAlert.png)

Se si apre l'avviso, la descrizione dell'avviso contiene il nodo di unità di scala e la posizione esatta dello slot fisico per il disco che è necessario sostituire. Ulteriormente Stack Azure consente di identificare un disco guasto utilizzando funzionalità indicatore LED.

 ## <a name="replace-the-disk"></a>Sostituire il disco

Seguire le istruzioni FRU del produttore dell'hardware OEM per la sostituzione del disco effettivo.

Per evitare l'utilizzo di un disco non supportato in un sistema integrato, il sistema blocca i dischi che non sono supportati dal fornitore del sistema. Se si tenta di utilizzare un disco non supportato, un nuovo avviso indica che un disco è stato messo in quarantena a causa di un modello non supportato o del firmware.

Dopo aver sostituito il disco, Stack di Azure automaticamente consente di individuare il nuovo disco e avvia il processo di ripristino del disco virtuale.  
 
 ## <a name="check-the-status-of-virtual-disk-repair"></a>Controllare lo stato di ripristino disco virtuale
 
 Dopo aver sostituito il disco, è possibile monitorare lo stato di integrità del disco virtuale e ripristinare lo stato del processo tramite l'endpoint con privilegi. Seguire questi passaggi da qualsiasi computer con connettività di rete all'endpoint con privilegi.

1. Aprire una sessione di Windows PowerShell e connettersi all'endpoint con privilegi.
    ````PowerShell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```` 
  
2. Eseguire il comando seguente per visualizzare lo stato del disco virtuale:
    ````PowerShell
        Get-VirtualDisk -CimSession s-cluster
    ````
   ![Output del comando Get-VirtualDisk PowerShell](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. Eseguire il comando seguente per visualizzare lo stato processo di archiviazione corrente:
    ```PowerShell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ````
      ![Output del comando Get-StorageJob PowerShell](media/azure-stack-replace-disk/GetStorageJobOutput.png)

## <a name="troubleshoot-virtual-disk-repair"></a>Risoluzione dei problemi di riparazione del disco virtuale

Se il disco virtuale Ripristina processo sembra bloccato, eseguire il comando seguente per riavviare il processo:
  ````PowerShell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ```` 
