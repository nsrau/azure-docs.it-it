---
title: Utilizzo di un endpoint con privilegi nello Stack di Azure | Documenti Microsoft
description: Viene illustrato come utilizzare l'endpoint con privilegi (PEP) nello Stack di Azure (per un operatore di Stack di Azure).
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
ms.date: 01/25/2018
ms.author: mabrigg
ms.openlocfilehash: 7f95014ac9186815d8ea0c7d271e5c6e19252d73
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>Utilizzo di un endpoint con privilegi nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Come operatore di Stack di Azure, utilizzare il portale dell'amministratore, PowerShell o le API di gestione risorse di Azure per le attività di gestione quotidiane di più. Tuttavia, per alcuni meno operazioni comuni, è necessario utilizzare il *endpoint con privilegi* (PEP). Il PEP è una console di PowerShell remota configurata in precedenza che offre di sufficiente capacità che consentono di eseguire un'attività obbligatoria. L'endpoint utilizza [PowerShell JEA (Just Enough Administration)](https://docs.microsoft.com/en-us/powershell/jea/overview) per esporre solo un set limitato di cmdlet. Per accedere il PEP e richiamare un insieme limitato di cmdlet, viene utilizzato un account con privilegi limitati. Nessun account di amministratore è necessario. Per una maggiore sicurezza, l'esecuzione degli script non è consentita.

È possibile utilizzare il PEP per eseguire le attività seguenti:

- Per eseguire attività di basso livello, ad esempio [la raccolta dei log di diagnostica](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool).
- Per eseguire molte attività di integrazione di Data Center di post-distribuzione per i sistemi integrati, ad esempio l'aggiunta di server d'inoltro sistema DNS (Domain Name) dopo la distribuzione, configurazione di integrazione di Microsoft Graph, integrazione di Active Directory Federation Services (ADFS), rotazione di certificato e così via.
- Per lavorare con il supporto per ottenere l'accesso temporaneo di alto livello per la risoluzione dei problemi approfondita di un sistema integrato.

Il PEP registra ogni azione (e l'output corrispondente) eseguite nella sessione di PowerShell. Consente di trasparenza totale e il controllo completo delle operazioni. È possibile mantenere questi file di log per i controlli futuri.

> [!NOTE]
> Nel Azure Stack Development Kit (ASDK), è possibile eseguire alcuni comandi disponibili nel PEP direttamente da una sessione di PowerShell nell'host di kit di sviluppo. Tuttavia, è consigliabile testare alcune operazioni utilizzando il PEP, ad esempio la raccolta di log, perché questo è l'unico metodo disponibile per eseguire determinate operazioni in un ambiente di sistemi integrati.

## <a name="access-the-privileged-endpoint"></a>Accedere all'endpoint con privilegi

Il PEP sono accessibili tramite una sessione remota di PowerShell nella macchina virtuale che ospita il PEP. In ASDK, questa macchina virtuale è denominata **AzS ERCS01**. Se si utilizza un sistema integrato, sono disponibili tre istanze del PEP, ognuno in esecuzione all'interno di una macchina virtuale (*prefisso*-ERCS01, *prefisso*-ERCS02, o *prefisso*- ERCS03) in host diversi per garantire la resilienza. 

Prima di iniziare questa procedura per un sistema integrato, verificare che sia possibile accedere il PEP indirizzo IP, o tramite DNS. Dopo la distribuzione iniziale dello Stack di Azure, è possibile accedere solo tramite indirizzo IP di PEP perché integrazione DNS non è stato ancora impostato. Il fornitore dell'hardware OEM fornirà un file JSON denominato **AzureStackStampDeploymentInfo** contenente gli indirizzi IP PEP.

È consigliabile connettersi al PEP solo dall'host del ciclo di vita dell'hardware o da un computer dedicato, protetto, ad esempio un [Workstation di accesso con privilegi](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations).

1. La Workstation di accesso con privilegi di accesso.

    - In un sistema integrato, eseguire il comando seguente per aggiungere il PEP come host attendibile in un host di ciclo di vita di hardware o la Workstation di accesso con privilegi.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Se si esegue il ADSK, accedere all'host del kit di sviluppo.

2. In componenti hardware del ciclo di vita host o la Workstation di accesso con privilegi, aprire una sessione di Windows PowerShell con privilegi elevata. Eseguire i comandi seguenti per stabilire una sessione remota nella macchina virtuale che ospita il PEP:
 
    - In un sistema integrato:
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      Il `ComputerName` parametro può essere l'indirizzo IP o il nome DNS di una macchina virtuale che ospita il PEP. 
    - Se si esegue il ADSK:
     
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName azs-ercs01`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   Quando richiesto, utilizzare le seguenti credenziali:

      - **Nome utente**: specificare l'account CloudAdmin, nel formato  **&lt; *dominio Azure Stack*&gt;\cloudadmin**. (Per ASDK, il nome utente è **azurestack\cloudadmin**.)
      - **Password**: immettere la stessa password fornita durante l'installazione per l'account di amministratore di dominio AzureStackAdmin.
    
3.  Dopo la connessione, la richiesta verrà visualizzata l'indicazione **[*nome indirizzo IP o VM ERCS*]: PS >** o **[azs ercs01]: PS >**, a seconda dell'ambiente. A questo punto, eseguire `Get-Command` per visualizzare l'elenco dei cmdlet disponibili.

    ![Il cmdlet Get-Command output con un elenco di comandi disponibili](media/azure-stack-privileged-endpoint/getcommandoutput.png)

    Molti di questi cmdlet sono destinati solo per ambienti con sistemi integrata (ad esempio i cmdlet relativi all'integrazione con Data Center). In ASDK, sono stati convalidati i cmdlet seguenti:

    - Clear-Host
    - Close-PrivilegedEndpoint
    - Exit-PSSession
    - Get-AzureStackLog
    - Get-AzureStackStampInformation
    - Get-Command
    - Get-FormatData
    - Get-Help
    - Get-ThirdPartyNotices
    - Measure-Object
    - New-CloudAdminUser
    - Out-Default
    - Remove-CloudAdminUser
    - Select-Object
    - Set-CloudAdminUserPassword
    - Test-AzureStack
    - Stop-AzureStack
    - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>Suggerimenti per l'utilizzo di un endpoint con privilegi 

Come indicato in precedenza, il PEP è un [JEA PowerShell](https://docs.microsoft.com/en-us/powershell/jea/overview) endpoint. Fornendo un livello di protezione avanzata, un endpoint JEA consente di ridurre alcune funzionalità di base PowerShell, ad esempio di script o scheda completamento. Se si tenta di qualsiasi tipo di operazione script, l'operazione non riesce con l'errore **ScriptsNotAllowed**. Si tratta di un comportamento previsto.

In tal caso, ad esempio, per ottenere l'elenco di parametri per un cmdlet specifico, eseguire il comando seguente:

```PowerShell
    Get-Command <cmdlet_name> -Syntax
```

In alternativa, è possibile utilizzare il [Import-PSSession](https://docs.microsoft.com/en-us/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) per importare tutti i cmdlet PEP nella sessione corrente nel computer locale. In questo modo, tutti i cmdlet e funzioni del PEP sono ora disponibili nel computer locale, con completamento tramite tasto tab e, più in generale, di scripting. 

Per importare la sessione PEP sul computer locale, effettuare i passaggi seguenti:

1. La Workstation di accesso con privilegi di accesso.

    - In un sistema integrato, eseguire il comando seguente per aggiungere il PEP come host attendibile in un host di ciclo di vita di hardware o la Workstation di accesso con privilegi.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Se si esegue il ADSK, accedere all'host del kit di sviluppo.

2. In componenti hardware del ciclo di vita host o la Workstation di accesso con privilegi, aprire una sessione di Windows PowerShell con privilegi elevata. Eseguire i comandi seguenti per stabilire una sessione remota nella macchina virtuale che ospita il PEP:
 
    - In un sistema integrato:
      ````PowerShell
        $cred = Get-Credential

        $session = New-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      Il `ComputerName` parametro può essere l'indirizzo IP o il nome DNS di una macchina virtuale che ospita il PEP. 
    - Se si esegue il ADSK:
     
      ````PowerShell
       $cred = Get-Credential

       $session = New-PSSession -ComputerName azs-ercs01`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   Quando richiesto, utilizzare le seguenti credenziali:

      - **Nome utente**: specificare l'account CloudAdmin, nel formato  **&lt; *dominio Azure Stack*&gt;\cloudadmin**. (Per ASDK, il nome utente è **azurestack\cloudadmin**.)
      - **Password**: immettere la stessa password fornita durante l'installazione per l'account di amministratore di dominio AzureStackAdmin.

3. Importare la sessione PEP nel computer locale
    ````PowerShell 
        Import-PSSession $session
    ````
4. A questo punto, è possibile usare il tasto TAB ed eseguire script come di consueto nella sessione di PowerShell locale con tutte le funzioni e cmdlet di PEP, senza ridurre le condizioni di sicurezza dello Stack di Azure. Buon lavoro.


## <a name="close-the-privileged-endpoint-session"></a>Chiudere la sessione di endpoint con privilegi

 Come accennato in precedenza, il PEP registra ogni azione (e l'output corrispondente) eseguite nella sessione di PowerShell. È necessario chiudere la sessione utilizzando il `Close-PrivilegedEndpoint` cmdlet. Questo cmdlet consente di chiudere l'endpoint correttamente e trasferisce i file di log in una condivisione di file esterno per la conservazione.

Per chiudere la sessione di endpoint:

1. Creare una condivisione di file esterno che è accessibile dal PEP. In un ambiente di kit di sviluppo, è possibile creare solo una condivisione di file nell'host di kit di sviluppo.
2. Eseguire il `Close-PrivilegedEndpoint` cmdlet. 
3. Richiesto per un percorso in cui archiviare il file di registro di sistema. Specificare la condivisione di file creato in precedenza, in formato &#92; &#92; *servername*&#92; *ShareName*. Se non si specifica un percorso, il cmdlet non riesce e la sessione rimane aperta. 

    ![Output del cmdlet Chiudi PrivilegedEndpoint che mostra in cui specificare il percorso di destinazione di trascrizione](media/azure-stack-privileged-endpoint/closeendpoint.png)

Dopo che i file di registro di sistema vengono trasferiti alla condivisione di file, è eliminati automaticamente dal PEP. Se si chiude la sessione PEP utilizzando i cmdlet `Exit-PSSession` o `Exit`, o è sufficiente chiudere la console di PowerShell, i registri di sistema non trasferire in una condivisione file. Rimangono nel PEP. Alla successiva esecuzione `Close-PrivilegedEndpoint` e includono una condivisione file, verranno inoltre trasferire i log di trascrizione dal precedente sessioni.

## <a name="next-steps"></a>Passaggi successivi
[Strumenti di diagnostica Azure Stack](azure-stack-diagnostics.md)