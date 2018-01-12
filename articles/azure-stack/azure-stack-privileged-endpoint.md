---
title: Utilizzo di un endpoint con privilegi nello Stack di Azure | Documenti Microsoft
description: Viene illustrato come utilizzare l'endpoint con privilegi nello Stack di Azure (per un operatore di Stack di Azure).
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 80c3f248edb40b66e3177c512f3caf77295c6c5d
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>Utilizzo di un endpoint con privilegi nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Come operatore di Stack di Azure, utilizzare il portale dell'amministratore, PowerShell o le API di gestione risorse di Azure per le attività di gestione quotidiane di più. Tuttavia, per alcuni meno operazioni comuni, è necessario utilizzare il *endpoint con privilegi* (PEP). Questo endpoint è una console di PowerShell remota configurata in precedenza che offre di sufficiente capacità che consentono di eseguire un'attività obbligatoria. L'endpoint utilizza PowerShell JEA (Just Enough Administration) per esporre solo un set limitato di cmdlet. Per accedere all'endpoint con privilegi e richiamare un insieme limitato di cmdlet, viene utilizzato un account con privilegi limitati. Nessun account di amministratore è necessario. Per una maggiore sicurezza, l'esecuzione degli script non è consentita.

È possibile utilizzare l'endpoint con privilegi per eseguire le attività seguenti:

- Per eseguire attività di basso livello, ad esempio [la raccolta dei log di diagnostica](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool).
- Per eseguire molte attività di integrazione di Data Center di post-distribuzione per i sistemi integrati, ad esempio l'aggiunta di server d'inoltro sistema DNS (Domain Name) dopo la distribuzione, configurazione di integrazione di grafico, integrazione di Active Directory Federation Services (ADFS), certificati rotazione e così via.
- Per lavorare con il supporto per ottenere l'accesso temporaneo di alto livello per la risoluzione dei problemi approfondita di un sistema integrato. 

L'endpoint con privilegi registra ogni azione (e l'output corrispondente) eseguite nella sessione di PowerShell. Consente di trasparenza totale e il controllo completo delle operazioni. È possibile mantenere questi file di log future a scopo di controllo.

> [!NOTE]
> In Azure Stack Development Kit (ASDK), è possibile eseguire alcuni comandi disponibili nell'endpoint con privilegi direttamente da una sessione di PowerShell nell'host di kit di sviluppo. Tuttavia, è consigliabile testare alcune operazioni utilizzando l'endpoint con privilegi, ad esempio la raccolta di log, perché questo è l'unico metodo disponibile per eseguire determinate operazioni in un ambiente integrato di sistemi.

## <a name="access-the-privileged-endpoint"></a>Accedere all'endpoint con privilegi

L'endpoint con privilegi sono accessibili tramite una sessione remota di PowerShell nella macchina virtuale che ospita l'endpoint con privilegi. In ASDK, questa macchina virtuale è denominata AzS ERCS01. Se si utilizza un sistema integrato, sono disponibili tre istanze dell'endpoint con privilegi, ognuno in esecuzione all'interno di una macchina virtuale (*prefisso*-ERCS01, *prefisso*-ERCS02, o *prefisso*-ERCS03) in host diversi per garantire la resilienza. 

Prima di iniziare questa procedura per un sistema integrato, verificare che è possibile accedere a un endpoint con privilegi utilizzando un indirizzo IP o tramite DNS. Dopo la distribuzione iniziale dello Stack di Azure, è possibile accedere all'endpoint con privilegi solo tramite indirizzo IP poiché l'integrazione DNS non è stato ancora impostato. Il fornitore dell'hardware OEM fornirà un file JSON denominato "AzureStackStampDeploymentInfo" che contiene gli indirizzi IP dell'endpoint con privilegi.

È consigliabile connettersi all'endpoint con privilegi solo dall'host del ciclo di vita dell'hardware o da un computer dedicato, bloccato, ad esempio un [Workstation di accesso con privilegi](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

1. Effettuare una delle operazioni seguenti a seconda dell'ambiente:

    - In un sistema integrato, eseguire il comando seguente per aggiungere l'endpoint con privilegi come host attendibile in un host di ciclo di vita di hardware o la Workstation di accesso con privilegi.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Se si esegue il ADSK, accedere all'host del kit di sviluppo.

2. In componenti hardware del ciclo di vita host o la Workstation di accesso con privilegi, aprire una sessione di Windows PowerShell con privilegi elevata. Eseguire i comandi seguenti per stabilire una sessione remota nella macchina virtuale che ospita l'endpoint con privilegi:
 
    - In un sistema integrato:
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      Il `ComputerName` parametro può essere l'indirizzo IP o il nome DNS di una macchina virtuale che ospita un endpoint con privilegi. 
    - Se si esegue il ADSK:
     
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName azs-ercs01`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   Quando richiesto, utilizzare le seguenti credenziali:

      - **Nome utente**: specificare l'account CloudAdmin, nel formato  **&lt;* dominio Azure Stack*&gt;\cloudadmin**. (Per ASDK, il nome utente è **azurestack\cloudadmin**.)
      - **Password**: immettere la stessa password fornita durante l'installazione per l'account di amministratore di dominio AzureStackAdmin.
    
3.  Dopo la connessione, la richiesta verrà visualizzata l'indicazione  **[*nome indirizzo IP o VM ERCS*]: PS > * * o **[azs ercs01]: PS >**, a seconda dell'ambiente. A questo punto, eseguire `Get-Command` per visualizzare l'elenco dei cmdlet disponibili.

    ![Il cmdlet Get-Command output con un elenco di comandi disponibili](media/azure-stack-privileged-endpoint/getcommandoutput.png)

    Molti di questi cmdlet sono destinati solo per ambienti con sistemi integrata (ad esempio i cmdlet relativi all'integrazione con Data Center). In ASDK, sono stati convalidati i cmdlet seguenti:

    - Clear-Host
    - Chiudi PrivilegedEndpoint
    - Exit-PSSession
    - Get-AzureStackLog
    - Get-AzureStackStampInformation
    - Get-Command
    - Get-FormatData
    - Get-Help
    - Get-ThirdPartyNotices
    - Measure-Object
    - Nuovo CloudAdminUser
    - Out-Default
    - Remove-CloudAdminUser
    - Select-Object
    - Set-CloudAdminUserPassword
    - Test AzureStack
    - Stop-AzureStack
    - Get-ClusterLog

4.  Poiché lo scripting non è consentito, è possibile utilizzare completamento tramite tasto tab per i valori dei parametri. Per ottenere l'elenco di parametri per un cmdlet specifico, eseguire il comando seguente:

    ````PowerShell
    Get-Command <cmdlet_name> -Syntax
    ```` 
    Se si tenta di qualsiasi tipo di operazione script, l'operazione non riesce con l'errore **ScriptsNotAllowed**. Si tratta di un comportamento previsto.

## <a name="close-the-privileged-endpoint-session"></a>Chiudere la sessione di endpoint con privilegi

 Come accennato in precedenza, l'endpoint con privilegi registra ogni azione (e l'output corrispondente) eseguite nella sessione di PowerShell. È necessario chiudere la sessione utilizzando il `Close-PrivilegedEndpoint` cmdlet. Questo cmdlet consente di chiudere l'endpoint correttamente e trasferisce i file di log in una condivisione di file esterno per la conservazione.

Per chiudere la sessione di endpoint:

1. Creare una condivisione di file esterno che è accessibile dall'endpoint con privilegi. In un ambiente di kit di sviluppo, è possibile creare solo una condivisione di file nell'host di kit di sviluppo.
2. Eseguire il `Close-PrivilegedEndpoint` cmdlet. 
3. Richiesto per un percorso in cui archiviare il file di registro di sistema. Specificare la condivisione di file creato in precedenza, in formato &#92; &#92; *servername*&#92; *ShareName*. Se non si specifica un percorso, il cmdlet non riesce e la sessione rimane aperta. 

    ![Output del cmdlet Chiudi PrivilegedEndpoint che mostra in cui specificare il percorso di destinazione di trascrizione](media/azure-stack-privileged-endpoint/closeendpoint.png)

Dopo che i file di registro di sistema vengono trasferiti alla condivisione di file, è eliminati automaticamente dall'endpoint con privilegi. Se si chiude la sessione di endpoint con privilegi utilizzando i cmdlet `Exit-PSSession` o `Exit`, o è sufficiente chiudere la console di PowerShell, i registri di sistema non trasferire in una condivisione file. Rimangono nell'endpoint con privilegi. Alla successiva esecuzione `Close-PrivilegedEndpoint` e includono una condivisione file, verranno inoltre trasferire i log di trascrizione dal precedente sessioni.

## <a name="next-steps"></a>Passaggi successivi
[Strumenti di diagnostica Azure Stack](azure-stack-diagnostics.md)







