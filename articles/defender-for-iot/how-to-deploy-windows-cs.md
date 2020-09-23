---
title: Installare l'agente C# nel dispositivo Windows
description: Informazioni su come installare Defender per l'agente Internet in dispositivi Windows a 32 bit o a 64 bit.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 04b33c7e63efbd6ffabf978708e1b8ed81f1fc42
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940035"
---
# <a name="deploy-an-defender-for-iot-c-based-security-agent-for-windows"></a>Distribuire una protezione per l'agente di sicurezza basato su C# per Windows

Questa guida illustra come installare il Defender per l'agente sicurezza basato su C# in Windows.

Questa guida illustra come eseguire queste operazioni:

> [!div class="checklist"]
> * Installazione
> * Verificare la distribuzione
> * Disinstallazione dell'agente
> * Risolvere problemi

## <a name="prerequisites"></a>Prerequisiti

Per altre piattaforme e versioni degli agenti, vedere [scegliere l'agente di sicurezza appropriato](how-to-deploy-agent.md).

1. Diritti di amministratore locale nel computer in cui si vuole eseguire l'installazione.

1. [Creare un modulo di sicurezza](quickstart-create-security-twin.md) per il dispositivo.

## <a name="installation"></a>Installazione

Per installare l'agente sicurezza, utilizzare il flusso di lavoro seguente:

1. Installare il Defender per l'agente Windows C# nel dispositivo. Scaricare la versione più recente nel computer dal [repository GitHub](https://github.com/Azure/Azure-IoT-Security-Agent-CS)Defender for Internet.

1. Estrarre il contenuto del pacchetto e passare alla cartella /Install.

1. Aprire Windows PowerShell come amministratore.
1. Aggiungere le autorizzazioni in esecuzione allo script InstallSecurityAgent eseguendo:

    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```

    eseguire quindi:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```

    Ad esempio:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```

    Per ulteriori informazioni sui parametri di autenticazione, vedere [How to Configure Authentication](concept-security-agent-authentication-methods.md).

Questo script esegue le azioni seguenti:

* Installazione dei prerequisiti.
* Aggiunge un utente del servizio (con accesso interattivo disabilitato).
* Installazione dell'agente come **Servizio di sistema**.
* Configurazione dell'agente con i parametri di autenticazione forniti.

Per ulteriori informazioni, utilizzare il comando Get-Help in PowerShell.

Esempio di Get-Help:    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Verificare lo stato di distribuzione

Controllare lo stato di distribuzione dell'agente eseguendo:

```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Disinstallazione dell'agente

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

1. Esaminare il file di log per altre informazioni sull'errore. Il file di log sarà presente nella directory di lavoro in cui viene eseguito lo script. 

   Posizione dei file di log: `.\IoTAgentLog.log`

## <a name="next-steps"></a>Passaggi successivi

* Leggi la [Panoramica](overview.md) del servizio Defender for Internet
* Scopri di più su Defender per l' [architettura dell'it](architecture.md)
* Abilitare il [servizio](quickstart-onboard-iot-hub.md)
* Leggere le [Domande frequenti](resources-frequently-asked-questions.md)
* Informazioni sugli [avvisi](concept-security-alerts.md)
