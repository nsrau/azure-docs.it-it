---
title: Installazione di Windows del Centro sicurezza di Azure per l'agente di IoT anteprima | Microsoft Docs
description: Informazioni su come installare il Centro sicurezza di Azure per l'agente di IoT in dispositivi Windows a 32 o 64 bit.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: 601ee706adedf522890acc3f3996a7d36b349348
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357779"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Distribuire un centro di sicurezza di Azure per IoT C#-agente di protezione per Windows basato su

> [!IMPORTANT]
> Centro sicurezza di Azure per IoT è attualmente in versione di anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questa guida illustra come installare il Centro sicurezza di Azure (ASC) per IoT C#-basato su agente protezione in Windows.

Questa guida illustra come eseguire queste operazioni: 
> [!div class="checklist"]
> * Installa
> * Verificare la distribuzione
> * Disinstallare l'agente
> * Risolvere problemi 

## <a name="prerequisites"></a>Prerequisiti

Per gli altri tipi di piattaforme e agenti, vedere [Scegliere l'agente di sicurezza appropriato](how-to-deploy-agent.md).

1. Diritti di amministratore locale nel computer che si desidera installare in. 

1. [Creare un modulo di sicurezza](quickstart-create-security-twin.md) per il dispositivo.

## <a name="installation"></a>Installazione 

Per installare l'agente di sicurezza, eseguire queste operazioni:

1. Per installare il Centro sicurezza di AZURE per Windows IoT C# agent nel dispositivo, scaricare la versione più recente nel computer dal Centro sicurezza di AZURE per IoT [repository GitHub](https://github.com/Azure/Azure-IoT-Security-Agent-CS).

2. Estrarre il contenuto del pacchetto e passare alla cartella /Install.

3. Aprire Windows PowerShell come amministratore. 
    1. Aggiungere le autorizzazioni di esecuzione dello script InstallSecurityAgent eseguendo ```Unblock-File .\InstallSecurityAgent.ps1```
    
        quindi eseguire:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Ad esempio: 
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    Per altre informazioni sui parametri di autenticazione, vedere la pagina [Come configurare l'autenticazione](concept-security-agent-authentication-methods.md).

Lo script esegue queste operazioni:

- Installazione dei prerequisiti.

- Aggiunta di un utente del servizio (con accesso interattivo disattivato).

- Installazione dell'agente come **Servizio di sistema**.

- Configurazione dell'agente con i parametri di autenticazione forniti.


Per altre informazioni, usare il comando Get-Help in PowerShell <br>Esempio di Get-Help:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Verificare lo stato di distribuzione

- Controllare lo stato di distribuzione dell'agente eseguendo:<br>
    ```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Disinstallare l'agente

Per disinstallare l'agente:

1. Eseguire lo script di PowerShell seguente con il parametro **-mode** impostato su **Disinstalla**.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>risoluzione dei problemi

Se l'agente non viene avviato, abilitare la registrazione (che è *disattivata* per impostazione predefinita) per ottenere altre informazioni.

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
    > È consigliabile **disattivare** la registrazione dopo aver completato la risoluzione dei problemi. Se si lascia **attivata** la registrazione, aumentano le dimensioni del file dati e l'utilizzo dei dati. 

1. Riavviare l'agente eseguendo la riga di comando o il comando PowerShell seguenti:

    **PowerShell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   oppure

    **CMD**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. Esaminare il file di log per altre informazioni sull'errore.

   Posizione dei file di log: `%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>Passaggi successivi
- Leggere la [Panoramica](overview.md) sul servizio del Centro sicurezza di Azure per IoT
- Vedere altre informazioni sull'[Architettura](architecture.md) del Centro sicurezza di Azure per IoT
- Abilitare il [servizio](quickstart-onboard-iot-hub.md)
- Leggere le [Domande frequenti](resources-frequently-asked-questions.md)
- Informazioni sugli [avvisi](concept-security-alerts.md)