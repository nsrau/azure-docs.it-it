---
title: Sostituire un disco fisico in Azure Stack | Microsoft Docs
description: Descrive il processo per informazioni su come sostituire un disco fisico in Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2019
ms.author: mabrigg
ms.openlocfilehash: b1fb1166e05eba209eddf72d97d20011c9ee4b78
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103004"
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Sostituire un disco fisico in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Questo articolo descrive il processo generale per sostituire un disco fisico in Azure Stack. Se un disco fisico ha esito negativo, è necessario sostituirlo appena possibile.

È possibile utilizzare questa procedura per i sistemi integrati e per le distribuzioni kit di sviluppo che dispongono di dischi a caldo.

Sostituzione del disco effettivi passaggi variano in base al fornitore dell'hardware OEM (OEM). Vedere la documentazione del fornitore campo unità sostituibile sul (FRU) per informazioni dettagliate specifiche per il sistema.

## <a name="review-disk-alert-information"></a>Informazioni sugli avvisi di revisione del disco
Quando un disco non riesce, si riceve un avviso che indica che la connettività è stata interrotta a un disco fisico.

 ![Visualizzazione degli avvisi connettività persa al disco fisico](media/azure-stack-replace-disk/DiskAlert.png)

Se si apre l'avviso, la descrizione dell'avviso contiene il nodo di unità di scala e la posizione esatta dello slot fisico per il disco che è necessario sostituire. Azure Stack ulteriormente aiuta a identificare il disco guasto usando le funzionalità di indicatore LED.

 ## <a name="replace-the-disk"></a>Sostituire il disco

Seguire le istruzioni FRU del produttore dell'hardware OEM per la sostituzione effettiva del disco.

> [!note]
> Sostituire i dischi per un nodo di unità di scala alla volta. Attendere che i processi di ripristino disco virtuale da completare prima di procedere con il successivo nodo di unità di scala

Per evitare l'uso di un disco non supportato in un sistema integrato, il sistema consente di bloccare i dischi che non sono supportati dal fornitore del sistema. Se si prova a usare un disco non supportato, un nuovo avviso indica che un disco è stato messo in quarantena a causa di un modello non supportato o firmware.

Dopo aver sostituito il disco, Azure Stack automaticamente consente di individuare il nuovo disco e viene avviato il processo di ripristino del disco virtuale.
 
 ## <a name="check-the-status-of-virtual-disk-repair"></a>Controllare lo stato di ripristino disco virtuale
 
 Dopo aver sostituito il disco, è possibile monitorare lo stato di integrità del disco virtuale e ripristinare lo stato del processo utilizzando l'endpoint con privilegi. Seguire questi passaggi da qualsiasi computer dotato di connettività di rete all'endpoint con privilegi.

1. Aprire una sessione di Windows PowerShell e connettersi all'endpoint con privilegi.
    ```PowerShell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ``` 
  
2. Eseguire il comando seguente per visualizzare lo stato del disco virtuale:
    ```PowerShell
        Get-VirtualDisk -CimSession s-cluster
    ```
   ![Output di PowerShell del comando Get-VirtualDisk](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. Eseguire il comando seguente per visualizzare lo stato del processo corrente archiviazione:
    ```PowerShell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ```
      ![Output di PowerShell del comando Get-StorageJob](media/azure-stack-replace-disk/GetStorageJobOutput.png)

## <a name="troubleshoot-virtual-disk-repair"></a>Risolvere i problemi di riparazione del disco virtuale

Se il disco virtuale Ripristina processo sembra bloccato, eseguire il comando seguente per riavviare il processo:
  ```PowerShell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ``` 
