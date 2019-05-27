---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66161158"
---
A seconda del sistema operativo del client, le procedure per connettersi in remoto il dispositivo sono diversi.

### <a name="remotely-connect-from-a-windows-client"></a>Connettersi in remoto da un client Windows

Prima di iniziare, assicurarsi che il client di Windows sia in esecuzione Windows PowerShell 5.0 o versioni successive.

Seguire questi passaggi per connettersi in remoto da un client Windows.

1. Eseguire una sessione di Windows PowerShell come amministratore.
2. Assicurarsi che il servizio Gestione remota Windows sia in esecuzione nel client. Al prompt dei comandi digitare:

    `winrm quickconfig`

3. Assegnare una variabile per l'indirizzo IP del dispositivo.

    $ip = "< ip_dispositivo >"

    Sostituire `<device_ip>` con l'indirizzo IP del dispositivo.

4. Per aggiungere l'indirizzo IP del dispositivo all'elenco di host attendibili del client, digitare il comando seguente:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Avviare una sessione di Windows PowerShell del dispositivo:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Fornire la password quando richiesto. Usare la stessa password che consente di accedere all'interfaccia utente Web locale. È la password predefinita dell'interfaccia utente web locale *Password1*. Quando ci si connette correttamente al dispositivo usando PowerShell remoto, è visualizzato l'output di esempio seguente:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>Connettersi in remoto da un client Linux

Nel client Linux che si userà per connettersi:

- [Installare il più recente di PowerShell Core per Linux](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6) da GitHub per ottenere le funzionalità di comunicazione remota SSH. 
- [Installare solo le `gss-ntlmssp` pacchetto dal modulo NTLM](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). Per i client di Ubuntu, usare il comando seguente:
    - `sudo apt-get install gss-ntlmssp`

Per altre informazioni, visitare [comunicazione remota di PowerShell su SSH](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6).

Seguire questi passaggi per connettersi in remoto da un client NFS.

1. Per aprire una sessione di PowerShell, digitare:

    `sudo pwsh`
 
2. Per la connessione usando il client remoto, digitare:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Quando richiesto, specificare la password utilizzata per accedere al dispositivo.
 
> [!NOTE]
> Questa procedura non funziona su Mac OS.
