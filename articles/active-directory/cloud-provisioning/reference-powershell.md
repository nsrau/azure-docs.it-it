---
title: Modulo AADCloudSyncTools di PowerShell per il provisioning di Azure AD Connect Cloud
description: Questo articolo descrive come installare l'agente di provisioning cloud di Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba71d3eedbb75780c20499daa890556574494a35
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348138"
---
# <a name="aadcloudsynctools-powershell-module-for-azure-ad-connect-cloud-provisioning"></a>Modulo AADCloudSyncTools di PowerShell per il provisioning di Azure AD Connect Cloud

Con il rilascio dell'aggiornamento dell'anteprima pubblica 2, Microsoft ha introdotto il modulo AADCloudSyncTools di PowerShell.  Questo modulo fornisce un set di strumenti utili che è possibile usare per gestire le distribuzioni di Azure AD Connect Cloud Sync.

## <a name="pre-requisites"></a>Prerequisiti
Sono necessari i seguenti prerequisiti:
- Questo modulo usa l'autenticazione MSAL, quindi richiede l'installazione del modulo MSAL.PS. Non dipende più da Azure AD o Azure AD Preview.   Per verificare, in una finestra di PowerShell amministratore eseguire `Get-module MSAL.PS` . Se il modulo è installato correttamente, si otterrà una risposta.  È possibile usare `Install-AADCloudSyncToolsPrerequisites` per installare la versione più recente di MSAL.PS
- Il modulo AzureAD di PowerShell.  Alcuni cmdlet si basano sulle parti del modulo AzureAD di PowerShell per eseguire le attività.  Per verificare, in una finestra di PowerShell amministratore eseguire `Get-module AzureAD` . Si dovrebbe ottenere una risposta.  È possibile usare `Install-AADCloudSyncToolsPrerequisites` per installare la versione più recente del modulo AzureAD di PowerShell.
- L'installazione di moduli da PowerShell può imporre l'uso di TLS 1,2.  Per assicurarsi che sia possibile installare i moduli, impostare quanto segue: \
`[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 `

## <a name="install-the-aadcloudsynctools-powershell-module"></a>Installare il modulo AADCloudSyncTools di PowerShell
Per installare e usare il modulo AADCloudSyncTools, seguire questa procedura:

1.  Aprire Windows PowerShell con privilegi amministrativi
2.  Digitare `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` e premere INVIO.
3.  Digitare o copiare e incollare quanto segue: 
    ``` powershell
    Import-module -Name "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"
    ```
3.  Premere INVIO.
4.  Per verificare che il modulo sia stato installato, immettere o copiare e incollare quanto segue:
    ```powershell
    Get-module AADCloudSyncTools
    ```
5.  Verranno ora visualizzate le informazioni sul modulo.
6.  Prossima esecuzione
    ``` powershell
    Install-AADCloudSyncToolsPrerequisites
    ```
7.  Verrà installato il modulo PowerShell Get modules.  Chiudere la finestra di PowerShell.
8.  Aprire Windows PowerShell con privilegi amministrativi
9.  Importare di nuovo il modulo usando il passaggio 3.
10. Eseguire `Install-AADCloudSyncToolsPrerequisites` per installare i moduli MSAL e AzureAD
11. Il modulo di installazione di Rich deve essere installato correttamente ![](media/reference-powershell/install-1.png)

## <a name="aadcloudsynctools--cmdlets"></a>Cmdlet AADCloudSyncTools
### <a name="connect-aadcloudsynctools"></a>Connect-AADCloudSyncTools
Usa il modulo AzureAD per connettersi a Azure AD e al modulo MSAL.PS per richiedere un token per Microsoft Graph


### <a name="export-aadcloudsynctoolslogs"></a>Export-AADCloudSyncToolsLogs
Esporta e comprime tutti i dati di risoluzione dei problemi in un file compresso, come indicato di seguito:
 1. Avvia una traccia dettagliata con Start-AADCloudSyncToolsVerboseLogs.  Questi log di traccia sono disponibili nella cartella C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace.
 2. Raccoglie un registro di traccia per 3 minuti.
   È possibile specificare un'ora diversa con-TracingDurationMins o ignorare la traccia dettagliata con-SkipVerboseTrace
 3. Arresta la traccia dettagliata con Stop-AADCloudSyncToolsVerboseLogs
 4. Raccoglie i registri Visualizzatore eventi per le ultime 24 ore
 5. Comprime tutti i log dell'agente, i log dettagliati e i log del Visualizzatore eventi in un file zip compresso nella cartella documenti dell'utente. 
 </br>È possibile specificare una cartella di output diversa con-OutputPath \<folder path\>

### <a name="get-aadcloudsynctoolsinfo"></a>Get-AADCloudSyncToolsInfo
Mostra i dettagli del tenant Azure AD e lo stato delle variabili interne

### <a name="get-aadcloudsynctoolsjob"></a>Get-AADCloudSyncToolsJob
USA Graph per ottenere le entità servizio AD2AAD e restituisce le informazioni sul processo di sincronizzazione.
Può essere chiamato anche usando l'ID del processo di sincronizzazione specifico come parametro.

### <a name="get-aadcloudsynctoolsjobschedule"></a>Get-AADCloudSyncToolsJobSchedule
USA Graph per ottenere le entità servizio AD2AAD e restituisce la pianificazione del processo di sincronizzazione.
Può essere chiamato anche usando l'ID del processo di sincronizzazione specifico come parametro.

### <a name="get-aadcloudsynctoolsjobschema"></a>Get-AADCloudSyncToolsJobSchema
USA Graph per ottenere le entità servizio AD2AAD e restituisce lo schema del processo di sincronizzazione.

### <a name="get-aadcloudsynctoolsjobscope"></a>Get-AADCloudSyncToolsJobScope
USA Graph per ottenere lo schema del processo di sincronizzazione per l'ID del processo di sincronizzazione fornito e restituisce gli ambiti di tutti i gruppi di filtri.

### <a name="get-aadcloudsynctoolsjobsettings"></a>Get-AADCloudSyncToolsJobSettings
USA Graph per ottenere le entità servizio AD2AAD e restituisce le impostazioni del processo di sincronizzazione.
Può essere chiamato anche usando l'ID del processo di sincronizzazione specifico come parametro.

### <a name="get-aadcloudsynctoolsjobstatus"></a>Get-AADCloudSyncToolsJobStatus
USA Graph per ottenere le entità servizio AD2AAD e restituisce lo stato del processo di sincronizzazione.
Può essere chiamato anche usando l'ID del processo di sincronizzazione specifico come parametro.

### <a name="get-aadcloudsynctoolsserviceprincipal"></a>Get-AADCloudSyncToolsServicePrincipal
USA Graph per ottenere le entità servizio per AD2AAD e/o SyncFabric.
Senza parametri, restituirà solo le entità servizio AD2AAD.

### <a name="install-aadcloudsynctoolsprerequisites"></a>Install-AADCloudSyncToolsPrerequisites
Verifica la presenza dei moduli PowerShellGet v 2.2.4.1 o versioni successive e Azure AD e MSAL.PS e li installa, se mancanti.

### <a name="invoke-aadcloudsynctoolsgraphquery"></a>Invoke-AADCloudSyncToolsGraphQuery
Richiama una richiesta Web per l'URI, il metodo e il corpo specificati come parametri.

### <a name="repair-aadcloudsynctoolsaccount"></a>Repair-AADCloudSyncToolsAccount
USA Azure AD PowerShell per eliminare l'account corrente (se presente) e Reimposta l'autenticazione dell'account di sincronizzazione con un nuovo account di sincronizzazione in Azure AD.

### <a name="restart-aadcloudsynctoolsjob"></a>Restart-AADCloudSyncToolsJob
Riavvia una sincronizzazione completa.

### <a name="resume-aadcloudsynctoolsjob"></a>Resume-AADCloudSyncToolsJob
Continua la sincronizzazione dalla filigrana precedente.

### <a name="start-aadcloudsynctoolsverboselogs"></a>Start-AADCloudSyncToolsVerboseLogs
Modifica il ' AADConnectProvisioningAgent.exe.config' per abilitare la traccia dettagliata e riavvia il servizio AADConnectProvisioningAgent è possibile usare-SkipServiceRestart per impedire il riavvio del servizio, ma le modifiche di configurazione non diventeranno effettive.  Questi log di traccia sono disponibili nella cartella C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace.

### <a name="stop-aadcloudsynctoolsverboselogs"></a>Stop-AADCloudSyncToolsVerboseLogs
Modifica il ' AADConnectProvisioningAgent.exe.config' per disabilitare la traccia dettagliata e riavviare il servizio AADConnectProvisioningAgent. È possibile usare-SkipServiceRestart per impedire il riavvio del servizio, ma le modifiche di configurazione non diventeranno effettive.

### <a name="suspend-aadcloudsynctoolsjob"></a>Suspend-AADCloudSyncToolsJob
Sospende la sincronizzazione.

## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)

