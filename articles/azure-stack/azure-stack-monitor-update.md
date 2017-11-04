---
title: Monitorare gli aggiornamenti nello Stack di Azure utilizzando l'endpoint con privilegi | Documenti Microsoft
description: Informazioni su come utilizzare l'endpoint con privilegi per monitorare lo stato di aggiornamento per i sistemi Azure Stack integrato.
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
ms.date: 10/18/2017
ms.author: twooley
ms.openlocfilehash: af7387268d60dc639a39da23e040097fd0695a22
ms.sourcegitcommit: d6ad3203ecc54ab267f40649d3903584ac4db60b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2017
---
# <a name="monitor-updates-in-azure-stack-using-the-privileged-endpoint"></a>Monitorare gli aggiornamenti nello Stack di Azure utilizzando l'endpoint con privilegi

*Si applica a: Azure Stack integrate di sistemi*

Per monitorare lo stato di avanzamento di un'operazione di aggiornamento dello Stack di Azure e per riprendere un aggiornamento non riuscito a eseguire dall'ultima operazione ha esito positivo, è possibile utilizzare l'endpoint con privilegi. 

I seguenti nuovi cmdlet di PowerShell per la gestione degli aggiornamenti sono inclusi nell'aggiornamento 1710 per i sistemi Azure Stack integrato.

| Cmdlet  | Descrizione  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | Restituisce lo stato dell'aggiornamento attualmente in esecuzione, completato o non riuscito. Fornisce lo stato di alto livello di operazione di aggiornamento e un documento XML che descrive il passaggio corrente sia stato corrispondente. |
| `Get-AzureStackUpdateVerboseLog` | Restituisce i log dettagliati generati dall'aggiornamento. |
| `Resume-AzureStackUpdate` | Riprende un aggiornamento non riuscito nel punto in cui non è riuscito. In alcuni scenari, potrebbe essere necessario completare i passaggi di attenuazione prima di riprendere l'aggiornamento.         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>Verificare che siano disponibili i cmdlet
Poiché i cmdlet sono nuovi nel pacchetto di aggiornamento 1710 per lo Stack di Azure, il processo di aggiornamento 1710 deve ottenere un determinato punto prima che sia disponibile la funzionalità di monitoraggio. In genere, i cmdlet sono disponibili se lo stato nel portale di amministrazione indica che l'aggiornamento 1710 è il **riavviare gli host di archiviazione** passaggio. In particolare, durante l'aggiornamento di cmdlet **passaggio: esecuzione del passaggio 2.6 - aggiornare PrivilegedEndpoint whitelist**.

È anche possibile determinare se i cmdlet sono disponibili a livello di codice richiedendo l'elenco dei comandi dall'endpoint con privilegi. A tale scopo, eseguire i comandi seguenti dall'host del ciclo di vita dell'hardware o da una Workstation con privilegi di accesso. Assicurarsi inoltre che l'endpoint con privilegi è un host attendibile. Per ulteriori informazioni, vedere il passaggio 1 [accedere all'endpoint con privilegi](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). 

1. Creare una sessione di PowerShell in ognuna delle macchine virtuali ERCS nell'ambiente Azure Stack (*prefisso*-ERCS01, *prefisso*-ERCS02, o *prefisso*-ERCS03). Sostituire *prefisso* con la stringa di prefisso di macchina virtuale specifica per l'ambiente.

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   Quando vengono richieste le credenziali, utilizzare il &lt; *dominio Azure Stack*&gt;\cloudadmin account o un account membro del gruppo CloudAdmins. Per l'account CloudAdmin, immettere la stessa password fornita durante l'installazione per l'account di amministratore di dominio AzureStackAdmin.

2. Ottenere l'elenco completo di comandi che sono disponibili nell'endpoint con privilegi. 

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. Determinare se l'endpoint con privilegi è stato aggiornato.

   ```powershell
   $updateManagementModuleName = "Microsoft.Azurestack.UpdateManagement"
    if (($commands | ? Source -eq $updateManagementModuleName)) {
   Write-Host "Privileged endpoint was updated to support update monitoring tools."
    } else {
   Write-Host "Privileged endpoint has not been updated yet. Please try again later."
    } 
   ```

4. Elenco di comandi specifici per il modulo Microsoft.AzureStack.UpdateManagement.

   ```powershell
   $commands | ? Source -eq $updateManagementModuleName 
   ```
   ad esempio:
   ```powershell
   $commands | ? Source -eq $updateManagementModuleName
   
   CommandType     Name                                               Version    Source                                                  PSComputerName
    -----------     ----                                               -------    ------                                                  --------------
   Function        Get-AzureStackUpdateStatus                         0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Get-AzureStackUpdateVerboseLog                     0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Resume-AzureStackUpdate                            0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   ``` 

## <a name="use-the-update-management-cmdlets"></a>Utilizzare i cmdlet di gestione di aggiornamento

> [!NOTE]
> Eseguire i comandi seguenti dall'host del ciclo di vita dell'hardware o da una Workstation con privilegi di accesso. Assicurarsi inoltre che l'endpoint con privilegi è un host attendibile. Per ulteriori informazioni, vedere il passaggio 1 [accedere all'endpoint con privilegi](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint).

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>Connettersi all'endpoint con privilegi e assegnare la variabile di sessione

Eseguire i comandi seguenti per creare una sessione di PowerShell in ognuna delle macchine virtuali ERCS nell'ambiente Azure Stack (*prefisso*-ERCS01, *prefisso*-ERCS02, o *prefisso*-ERCS03) e assegnare una variabile di sessione.

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 Quando vengono richieste le credenziali, utilizzare il &lt; *dominio Azure Stack*&gt;\cloudadmin account o un account membro del gruppo CloudAdmins. Per l'account CloudAdmin, immettere la stessa password fornita durante l'installazione per l'account di amministratore di dominio AzureStackAdmin.

### <a name="get-high-level-status-of-the-current-update-run"></a>Ottenere lo stato di alto livello dell'esecuzione di aggiornamento corrente 

Per ottenere uno stato di alto livello dell'esecuzione di aggiornamento corrente, eseguire i comandi seguenti: 

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

I valori possibili sono:

- In esecuzione
- Completed
- Operazione non riuscita 
- Canceled

È possibile eseguire questi comandi più volte per visualizzare lo stato più aggiornato. Non è necessario stabilire una connessione per controllare nuovamente.

### <a name="get-the-full-update-run-status-with-details"></a>Ottenere l'aggiornamento completo di stato di esecuzione con dettagli 

È possibile ottenere l'aggiornamento completo eseguire riepilogo come stringa XML. È possibile scrivere la stringa di un file per l'analisi, o convertirlo in un documento XML e utilizzare PowerShell per analizzarlo. Il comando seguente consente di analizzare il codice XML per ottenere un elenco gerarchico dei passaggi attualmente in esecuzione.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

Nell'esempio seguente, il passaggio di primo livello (aggiornamento di Cloud) dispone di un piano figlio per aggiornare e riavviare l'host di archiviazione. Viene illustrato che il piano di riavviare gli host di archiviazione sta aggiornando il servizio di archiviazione Blob su uno degli host.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']") 

    FullStepIndex : 2
    Index         : 2
    Name          : Cloud Update
    Description   : Perform cloud update.
    StartTimeUtc  : 2017-10-13T12:50:39.9020351Z
    Status        : InProgress
    Task          : Task
    
    FullStepIndex  : 2.9
    Index          : 9
    Name           : Restart Storage Hosts
    Description    : Restart Storage Hosts.
    EceErrorAction : Stop
    StartTimeUtc   : 2017-10-13T15:44:06.7431447Z
    Status         : InProgress
    Task           : Task
    
    FullStepIndex : 2.9.2
    Index         : 2
    Name          : PreUpdate ACS Blob Service
    Description   : Check function level, update deployment artifacts, configure Blob service settings
    StartTimeUtc  : 2017-10-13T15:44:26.0708525Z
    Status        : InProgress
    Task          : Task
```

### <a name="get-the-verbose-progress-log"></a>Ottenere il log dettagliato

È possibile scrivere il log in un file per l'analisi. Ciò consente di diagnosticare un errore di aggiornamento.

```powershell
$log = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateVerboseLog }

$log > ".\UpdateVerboseLog.txt" 
```

### <a name="actively-view-the-verbose-logging"></a>Visualizzare attivamente la registrazione dettagliata

Per attivamente vista eseguire, il log dettagliato durante un aggiornamento e di passare alle voci più recenti, eseguire i comandi seguenti per attivare la sessione in modalità interattiva e per la visualizzazione del log:

```powershell
Enter-PSSession -Session $pepSession 

Get-AzureStackUpdateVerboseLog -Wait 
```
Il log Aggiorna ogni 60 secondi, quindi nuovo contenuto, se disponibile, viene scritto nella console. 

Durante i processi in background con esecuzione prolungata, l'output della console potrebbe non essere scritti nella console per un certo tempo. Per annullare l'output interattivo, premere Ctrl + C. 

### <a name="resume-a-failed-update-operation"></a>Ripresa di un'operazione di aggiornamento non riuscito

Se l'aggiornamento non riesce, è possibile riprendere l'aggiornamento eseguito in cui è stata interrotta.

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>Risoluzione dei problemi

L'endpoint con privilegi è disponibile in tutte le macchine virtuali ERCS nell'ambiente dello Stack di Azure. Poiché non viene stabilita la connessione a un endpoint a disponibilità elevata, potrebbero verificarsi interruzioni occasionali, avvisi o messaggi di errore. Questi messaggi possono indicare che la sessione è stata disconnessa o che si è verificato un errore di comunicazione con il servizio ECE. Questo comportamento è previsto. È possibile ripetere l'operazione tra qualche minuto o creare una nuova sessione di endpoint con privilegi in una delle altre macchine virtuali ERCS. 

## <a name="next-steps"></a>Passaggi successivi

- [Gestione degli aggiornamenti in Azure Stack](azure-stack-updates.md) 


