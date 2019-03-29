---
title: Installazione di Windows del Centro sicurezza di AZURE per l'agente di IoT anteprima | Microsoft Docs
description: Informazioni su come installare Centro sicurezza di AZURE per l'agente di IoT in dispositivi Windows a 32 o 64 bit.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: 5c3293746fcc52570e708fd4bfab446981d49c24
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621831"
---
# <a name="deploy-an-asc-for-iot-c-based-security-agent-for-windows"></a>Distribuire un Centro sicurezza di AZURE per IoT C#-agente di protezione per Windows basato su

> [!IMPORTANT]
> Centro sicurezza di AZURE per IoT è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questa guida illustra come installare il Centro sicurezza di AZURE per IoT C#-basato su agente protezione in Windows.

Questa guida illustra come eseguire queste operazioni: 
> [!div class="checklist"]
> * Installa
> * Verifica distribuzione
> * Disinstallare l'agente
> * Risoluzione dei problemi 

## <a name="prerequisites"></a>Prerequisiti

Per altre piattaforme e tipologie: blocchi dell'agente, vedere [scegliere l'agente di protezione adeguato](how-to-deploy-agent.md).

1. Diritti di amministratore locale nel computer che si desidera installare in. 

1. [Creare un modulo di protezione](quickstart-create-security-twin.md) per il dispositivo.

## <a name="installation"></a>Installazione 

Per installare l'agente protezione, eseguire le operazioni seguenti:

1. Per installare il Centro sicurezza di AZURE per Windows IoT C# agent nel dispositivo, scaricare la versione più recente nel computer dal Centro sicurezza di AZURE per IoT [repository GitHub](https://github.com/Azure/Azure-IoT-Security-Agent-CS).

2. Estrarre il contenuto del pacchetto e passare alla cartella /Install.

3. Aprire Windows PowerShell come amministratore. 
    1. Aggiungere le autorizzazioni di esecuzione dello script InstallSecurityAgent eseguendo ```Unblock-File .\InstallSecurityAgent.ps1```
    
        ed eseguire:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Ad esempio:
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    Visualizzare [come configurare l'autenticazione](concept-security-agent-authentication-methods.md) per altre informazioni sui parametri di autenticazione.

Lo script esegue queste operazioni:

- Installa i prerequisiti.

- Aggiunge un utente del servizio (con accesso interattivo disabilitato).

- Installa l'agente come un **servizio di sistema**.

- Configura l'agente con i parametri di autenticazione specificato.


Per altre informazioni, usare il comando Get-Help in PowerShell <br>Esempio di Get-Help:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Verificare lo stato di distribuzione

- Controllare lo stato di distribuzione dell'agente eseguendo:<br>
    ```sc.exe query "ASC IoT Agent" ```

### <a name="uninstall-the-agent"></a>Disinstallare l'agente

Per disinstallare l'agente:

1. Eseguire lo script di PowerShell seguente con il **-modalità** parametro impostato su **Disinstalla**.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se l'agente non viene avviato, abilitare la registrazione (la registrazione viene *disattivata* per impostazione predefinita) per ottenere altre informazioni.

Per abilitare la registrazione:

1. Aprire il file di configurazione (General.config) per la modifica mediante un editor di file standard.

1. Modificare i valori seguenti:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > È consigliabile attivare la registrazione **disattivata** dopo la risoluzione dei problemi è stata completata. Lasciando la registrazione **su** aumenta utilizzo dati e le dimensioni del file di log. 

1. Riavviare l'agente tramite l'esecuzione di PowerShell o della riga di comando seguenti:

    **PowerShell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   o

    **CMD**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. Esaminare il file di log per ulteriori informazioni sull'errore.

   Percorso file di log: `%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>Passaggi successivi
- Leggere il Centro sicurezza di AZURE per il servizio IoT [Panoramica](overview.md)
- Altre informazioni sul Centro sicurezza di AZURE per IoT [architettura](architecture.md)
- Abilitare il [servizio](quickstart-onboard-iot-hub.md)
- Lettura di [domande frequenti](resources-frequently-asked-questions.md)
- Comprendere [avvisi](concept-security-alerts.md)