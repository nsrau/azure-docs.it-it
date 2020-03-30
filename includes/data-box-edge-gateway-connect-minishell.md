---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67180609"
---
A seconda del sistema operativo del client, le procedure per la connessione remota al dispositivo sono diverse.

### <a name="remotely-connect-from-a-windows-client"></a>Connessione remota da un client Windows

Prima di iniziare, assicurarsi che sul client Windows sia in esecuzione Windows PowerShell 5.0 o versione successiva.

Seguire questi passaggi per connettersi in remoto da un client Windows.Follow these steps to remotely connect from a Windows client.

1. Eseguire una sessione di Windows PowerShell come amministratore.
2. Assicurarsi che il servizio Gestione remota Windows sia in esecuzione nel client. Al prompt dei comandi digitare:

    `winrm quickconfig`

3. Assegnare una variabile all'indirizzo IP del dispositivo.

    $ip - "> <device_ip"

    Sostituire `<device_ip>` con l'indirizzo IP del dispositivo.

4. Per aggiungere l'indirizzo IP del dispositivo all'elenco degli host attendibili del client, digitare il comando seguente:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Avviare una sessione di Windows PowerShell nel dispositivo:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Fornire la password quando richiesto. Usare la stessa password utilizzata per accedere all'interfaccia utente Web locale. La password predefinita dell'interfaccia utente Web locale è *Password1*. Quando ci si connette correttamente al dispositivo tramite PowerShell remoto, viene visualizzato l'output di esempio seguente:When you successfully connect to the device using remote PowerShell, you see the following sample output:  

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

### <a name="remotely-connect-from-a-linux-client"></a>Connessione remota da un client Linux

Sul client Linux che userai per connetterti:

- [Installare il Core di PowerShell più recente per Linux](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6) da GitHub per ottenere la funzionalità di comunicazione remota SSH. 
- [Installare solo `gss-ntlmssp` il pacchetto dal modulo NTLM](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). Per i client Ubuntu, utilizzare il comando seguente:
    - `sudo apt-get install gss-ntlmssp`

Per altre informazioni, visitare la comunicazione remota di [PowerShell tramite SSH](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6).

Attenersi alla seguente procedura per connettersi in remoto da un client NFS.

1. Per aprire la sessione di PowerShell, digitare:

    `sudo pwsh`
 
2. Per la connessione tramite il client remoto, digitare:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Quando richiesto, fornire la password utilizzata per accedere al dispositivo.
 
> [!NOTE]
> Questa procedura non funziona su Mac OS.
