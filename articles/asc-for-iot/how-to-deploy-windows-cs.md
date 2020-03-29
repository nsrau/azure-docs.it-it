---
title: Installazione di Windows del Centro sicurezza di Azure per l'agente IoT Documenti Microsoft
description: Informazioni su come installare il Centro sicurezza di Azure per l'agente IoT in dispositivi Windows a 32 o 64 bit.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: acc99f260931de7fd8c7566a3ff6daf43f34c5ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68597221"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Distribuire un agente di sicurezza per Windows basato su C# del Centro sicurezza di Azure per IoT

Questa guida illustra come installare l'agente di sicurezza basato su Azure Security Center per L'IoT in Windows.This guide explains how to install the Azure Security Center for IoT C's based security agent on Windows.

Questa guida illustra come eseguire queste operazioni: 
> [!div class="checklist"]
> * Installazione di
> * Verificare la distribuzione
> * Disinstallare l'agente
> * Risolvere i problemi 

## <a name="prerequisites"></a>Prerequisiti

Per altre piattaforme e ruoli di agente, vedere [Scegliere l'agente](how-to-deploy-agent.md)di sicurezza corretto.

1. Diritti di amministratore locale sul computer in cui si desidera eseguire l'installazione. 

1. [Creare un modulo di sicurezza](quickstart-create-security-twin.md) per il dispositivo.

## <a name="installation"></a>Installazione 

Per installare l'agente di sicurezza, utilizzare il flusso di lavoro seguente:

1. Installare nel dispositivo il Centro sicurezza di Azure per l'agente Windows C. Scaricare la versione più recente nel computer dal repository di Azure Security Center for IoT [GitHub](https://github.com/Azure/Azure-IoT-Security-Agent-CS).

1. Estrarre il contenuto del pacchetto e passare alla cartella /Install.

1. Aprire Windows PowerShell come amministratore. 
1. Aggiungere le autorizzazioni in esecuzione allo script InstallSecurityAgent eseguendo:<br>
    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```
    
    quindi eseguire:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Ad esempio:
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    Per ulteriori informazioni sui parametri di autenticazione, vedere [Come configurare l'autenticazione](concept-security-agent-authentication-methods.md).

Questo script esegue le azioni seguenti:This script does the following actions:

- Installazione dei prerequisiti.

- Aggiunge un utente del servizio (con accesso interattivo disabilitato).

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

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se l'agente non viene avviato, abilitare la registrazione (che è *disattivata* per impostazione predefinita) per ottenere altre informazioni.

Per abilitare la registrazione:

1. Aprire il file di configurazione (General.config) per la modifica utilizzando un editor di file standard.

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

    **Powershell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   o

    **Cmd**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. Esaminare il file di log per altre informazioni sull'errore.

   Posizione dei file di log: `%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>Passaggi successivi
- Leggi la [panoramica](overview.md) del Centro sicurezza di Azure per il servizio IoT
- Altre informazioni sul Centro sicurezza di Azure per l'architettura IoTLearn more about Azure Security Center for IoT [Architecture](architecture.md)
- Abilitare il [servizio](quickstart-onboard-iot-hub.md)
- Leggere le [Domande frequenti](resources-frequently-asked-questions.md)
- Informazioni sugli [avvisi](concept-security-alerts.md)