---
title: Uso dell'endpoint con privilegi in Azure Stack | Microsoft Docs
description: Viene illustrato come usare l'endpoint con privilegi (PEP) in Azure Stack (per un operatore di Azure Stack).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: mabrigg
ms.reviewer: fiseraci
ms.openlocfilehash: d91ad42d4d84802c4579a4d228a35030970da8e4
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44377242"
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>Uso dell'endpoint con privilegi in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Come operatore di Azure Stack, è necessario usare il portale dell'amministratore, PowerShell o API di Azure Resource Manager per più quotidiane di gestione. Tuttavia, per alcuni meno operazioni comuni, è necessario usare il *privileged endpoint* (PEP). Il PEP è una console di PowerShell remota preconfigurata che consente di sufficienti funzionalità che consentono di eseguire un'attività obbligatoria. L'endpoint Usa [PowerShell JEA (Just Enough Administration)](https://docs.microsoft.com/powershell/jea/overview) esporre solo un set limitato di cmdlet. Per accedere la PEP e richiamare il set limitato di cmdlet, viene usato un account con privilegi limitati. Nessun account di amministratore è necessario. Per una maggiore sicurezza, la creazione di script non è consentito.

È possibile usare il PEP per eseguire le attività seguenti:

- Per eseguire attività di basso livello, ad esempio [raccogliere i log di diagnostica](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool).
- Per eseguire molte attività di integrazione di Data Center di post-distribuzione per i sistemi integrati, ad esempio l'aggiunta di server d'inoltro Domain Name System (DNS) dopo la distribuzione, configurazione di integrazione di Microsoft Graph, integrazione di Active Directory Federation Services (ADFS), rotazione del certificato e così via.
- Per lavorare con il supporto per ottenere l'accesso temporaneo di alto livello per la risoluzione dei problemi approfondita di un sistema integrato.

Il PEP registra ogni azione (e l'output corrispondente) che eseguono nella sessione di PowerShell. In questo modo completa trasparenza e il controllo completo delle operazioni. È possibile mantenere questi file di log per i controlli di future.

> [!NOTE]
> Nel Azure Stack Development Kit (ASDK), è possibile eseguire alcuni comandi disponibili nella finestra di PEP direttamente da una sessione di PowerShell nell'host di kit di sviluppo. Tuttavia, è possibile testare alcune operazioni con PEP, quali la raccolta dei log, perché questo è l'unico metodo disponibile per eseguire determinate operazioni in un ambiente di sistemi integrati.

## <a name="access-the-privileged-endpoint"></a>Accedere all'endpoint con privilegi

Il PEP sono accessibili tramite una sessione remota di PowerShell nella macchina virtuale che ospita il PEP. In ASDK, questa macchina virtuale è denominata **AzS-ERCS01**. Se si usa un sistema integrato, sono disponibili tre istanze di PEP, ogni esecuzione all'interno di una macchina virtuale (*Prefix*-ERCS01, *prefisso*-ERCS02, o *prefisso*- ERCS03) in host diversi per garantire la resilienza. 

Prima di iniziare questa procedura per un sistema integrato, assicurarsi che sia possibile accedere la PEP indirizzo IP, o tramite DNS. Dopo la distribuzione iniziale di Azure Stack, è possibile accedere solo tramite indirizzo IP di PEP integrazione DNS non è stato ancora configurato. Il fornitore dell'hardware OEM fornirà all'utente un file JSON denominato **AzureStackStampDeploymentInfo** contenente gli indirizzi IP PEP.


> [!NOTE]
> Per motivi di sicurezza, si richiedono la connessione a di PEP solo da in esecuzione una macchina virtuale con protezione avanzata nella parte superiore dell'host del ciclo di vita dell'hardware o da un computer dedicato, protetto, ad esempio un [Privileged Access Workstation](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations). La configurazione dell'host del ciclo di vita dell'hardware originale non deve essere modificata dalla relativa configurazione originale, quali l'installazione di nuovo software, né deve essere utilizzato per connettersi a di PEP.

1. Stabilire la relazione di trust.

    - In un sistema integrato, eseguire il comando seguente da una sessione di Windows PowerShell con privilegi elevata per aggiungere il PEP come host attendibile nella macchina virtuale con protezione avanzata in esecuzione in host del ciclo di vita dell'hardware o la Workstation amministrativa con privilegi.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Se si esegue il ADSK, accedere all'host del kit di sviluppo.

2. Nella macchina virtuale con protezione avanzata in esecuzione in host del ciclo di vita dell'hardware o Privileged Access Workstation, aprire una sessione di Windows PowerShell. Eseguire i comandi seguenti per stabilire una sessione remota nella macchina virtuale che ospita il PEP:
 
    - In un sistema integrato:
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName <IP_address_of_ERCS> `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      Il `ComputerName` parametro può essere l'indirizzo IP o il nome DNS di una delle macchine virtuali che ospita il PEP. 
    - Se si esegue la ADSK:
     
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName azs-ercs01 `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   Quando richiesto, usare le credenziali seguenti:

      - **Nome utente**: specificare l'account CloudAdmin, nel formato  **&lt; *dominio di Azure Stack*&gt;\cloudadmin**. (ASDK, il nome utente rappresentato **azurestack\cloudadmin**.)
      - **Password**: immettere la stessa password che è stata specificata durante l'installazione per l'account di amministratore di dominio AzureStackAdmin.

    > [!NOTE]
    > Se non si riesce a connettersi all'endpoint ERCS, provare a passaggi uno e due con l'indirizzo IP di una VM ERCS a cui si è ancora provato già per la connessione.

3.  Dopo la connessione, il prompt dei comandi verrà modificato in **[*assegnare un nome indirizzo IP o VM ERCS*]: PS >** o a **[azs-ercs01]: PS >**, a seconda dell'ambiente. A questo punto, eseguire `Get-Command` per visualizzare l'elenco dei cmdlet disponibili.

    Molti di questi cmdlet sono destinati esclusivamente agli ambienti di sistema integrata (ad esempio, i cmdlet relativi all'integrazione di Data Center). In ASDK, sono stati convalidati i cmdlet seguenti:

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
    - New-CloudAdminUser
    - Out-Default
    - Remove-CloudAdminUser
    - Select-Object
    - Set-CloudAdminUserPassword
    - Test-AzureStack
    - Stop-AzureStack
    - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>Suggerimenti per l'uso dell'endpoint con privilegi 

Come indicato in precedenza, il PEP è un [PowerShell JEA](https://docs.microsoft.com/powershell/jea/overview) endpoint. Garantendo un livello di protezione avanzata, un endpoint JEA consente di ridurre alcune funzionalità di base PowerShell, ad esempio di script o della scheda di completamento. Se si tenta di qualsiasi tipo di operazione script, l'operazione ha esito negativo con l'errore **ScriptsNotAllowed**. Si tratta di un comportamento previsto.

Quindi, ad esempio, per ottenere l'elenco dei parametri per un cmdlet specifico, si esegue il comando seguente:

```PowerShell
    Get-Command <cmdlet_name> -Syntax
```

In alternativa, è possibile usare la [Import-PSSession](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) cmdlet per importare tutti i cmdlet PEP nella sessione corrente nel computer locale. In questo modo, tutti i cmdlet e funzioni di PEP sono ora disponibili nel computer locale, insieme a completamento tramite tasto tab e, più in generale, la creazione di script. 

Per importare la sessione PEP nel computer locale, procedere come segue:

1. Stabilire la relazione di trust.

    -In un sistema integrato, eseguire il comando seguente da una sessione di Windows PowerShell con privilegi elevata per aggiungere il PEP come host attendibile nella macchina virtuale con protezione avanzata in esecuzione in host del ciclo di vita dell'hardware o la Workstation amministrativa con privilegi.

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - Se si esegue il ADSK, accedere all'host del kit di sviluppo.

2. Nella macchina virtuale con protezione avanzata in esecuzione in host del ciclo di vita dell'hardware o Privileged Access Workstation, aprire una sessione di Windows PowerShell. Eseguire i comandi seguenti per stabilire una sessione remota nella macchina virtuale che ospita il PEP:
 
    - In un sistema integrato:
      ````PowerShell
        $cred = Get-Credential

        $session = New-PSSession -ComputerName <IP_address_of_ERCS> `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      Il `ComputerName` parametro può essere l'indirizzo IP o il nome DNS di una delle macchine virtuali che ospita il PEP. 
    - Se si esegue la ADSK:
     
      ````PowerShell
       $cred = Get-Credential

       $session = New-PSSession -ComputerName azs-ercs01 `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   Quando richiesto, usare le credenziali seguenti:

      - **Nome utente**: specificare l'account CloudAdmin, nel formato  **&lt; *dominio di Azure Stack*&gt;\cloudadmin**. (ASDK, il nome utente rappresentato **azurestack\cloudadmin**.)
      - **Password**: immettere la stessa password che è stata specificata durante l'installazione per l'account di amministratore di dominio AzureStackAdmin.

3. Importare la sessione PEP nel computer locale
    ````PowerShell 
        Import-PSSession $session
    ````
4. A questo punto, è possibile utilizzare completamento tramite tasto tab ed eseguire operazioni di scripting come di consueto nella sessione di PowerShell locale con tutte le funzioni e cmdlet di PEP, senza ridurre le condizioni di sicurezza di Azure Stack. Buon lavoro.


## <a name="close-the-privileged-endpoint-session"></a>Chiudere la sessione di endpoint con privilegi

 Come accennato in precedenza, il PEP registra ogni azione (e l'output corrispondente) che eseguono nella sessione di PowerShell. È necessario chiudere la sessione utilizzando il `Close-PrivilegedEndpoint` cmdlet. Questo cmdlet correttamente chiude l'endpoint e trasferisce i file di log in una condivisione di file esterno per il mantenimento dati.

Per chiudere la sessione di endpoint:

1. Creare una condivisione di file esterno che sia accessibile da di PEP. In un ambiente di kit di sviluppo, è possibile creare solo una condivisione file nell'host di kit di sviluppo.
2. Eseguire il `Close-PrivilegedEndpoint` cmdlet. 
3. Viene chiesto di un percorso in cui archiviare il file di log di trascrizione. Specificare la condivisione file creata in precedenza, nel formato &#92; &#92; *servername*&#92;*sharename*. Se non si specifica un percorso, il cmdlet non riesce e la sessione rimane aperta. 

    ![Output del cmdlet Chiudi PrivilegedEndpoint che mostra dove si specifica il percorso di destinazione di trascrizione](media/azure-stack-privileged-endpoint/closeendpoint.png)

Dopo che i file di log trascrizione correttamente vengono trasferiti alla condivisione file, che esegue automaticamente eliminati di PEP. 

> [!NOTE]
> Se si chiude la sessione PEP usando i cmdlet `Exit-PSSession` o `Exit`, o è sufficiente chiudere la console di PowerShell, i log di trascrizione non è possibile trasferire in una condivisione file. Ovvero rimangono di PEP. Alla successiva esecuzione `Close-PrivilegedEndpoint` e includono una condivisione file, i log di trascrizione dal precedente sessioni anche verranno trasferiti. Non utilizzare `Exit-PSSession` oppure `Exit` per chiudere la sessione PEP; usare `Close-PrivilegedEndpoint` invece.


## <a name="next-steps"></a>Passaggi successivi
[Strumenti di diagnostica Azure Stack](azure-stack-diagnostics.md)
