---
title: Guida all'installazione e la distribuzione Linux C# agente del Centro sicurezza di AZURE per l'anteprima di IoT | Microsoft Docs
description: Informazioni su come installare il Centro sicurezza di AZURE per l'agente di IoT in Linux sia a 32 e 64 bit.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: d6b4e6065b0ef198ad583b3760124730e658fe0b
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619909"
---
# <a name="deploy-asc-for-iot-c-based-security-agent-for-linux"></a>Distribuire Centro sicurezza di AZURE per IoT C#-agente di protezione per Linux basato su

> [!IMPORTANT]
> Centro sicurezza di AZURE per IoT è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questa guida illustra come installare e distribuire il Centro sicurezza di AZURE per IoT C#-agente di protezione basato su in Linux.

Questa guida illustra come eseguire queste operazioni: 
> [!div class="checklist"]
> * Installa
> * Verifica distribuzione
> * Disinstallare l'agente
> * Risoluzione dei problemi 

## <a name="prerequisites"></a>Prerequisiti

Per altre piattaforme e versioni dell'agente, vedere [scegliere l'agente di protezione adeguato](how-to-deploy-agent.md).

1. Per distribuire l'agente protezione, sono necessari diritti di amministratore locale nel computer che si desidera installare in. 

1. [Creare un modulo di protezione](quickstart-create-security-twin.md) per il dispositivo.

## <a name="installation"></a>Installazione 

Per distribuire l'agente protezione, eseguire le operazioni seguenti:

1. Scaricare la versione più recente nel computer dal [Github](https://aka.ms/iot-security-github-cs).

1. Estrarre il contenuto del pacchetto e passare al _/Install_ cartella.

1. Aggiungere le autorizzazioni di esecuzione per il **InstallSecurityAgent script** eseguendo `chmod +x InstallSecurityAgent.sh` 

1. Successivamente, eseguire: 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   Visualizzare [come configurare l'autenticazione](concept-security-agent-authentication-methods.md) per altre informazioni sui parametri di autenticazione.

Lo script esegue queste operazioni:

- Installa i prerequisiti.

- Aggiunge un utente del servizio (con accesso interattivo disabilitato).

- Installa l'agente come un **Daemon** -si presuppone che il dispositivo usa **systemd** per la gestione dei servizi.

- Consente di configurare **sudoers** per consentire all'agente eseguire determinate attività come utente root.

- Configura l'agente con i parametri di autenticazione specificato.


Per altre informazioni, eseguire lo script con il parametro – help: `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Disinstallare l'agente

Per disinstallare l'agente, eseguire lo script con il parametro – u: `./InstallSecurityAgent.sh -u`. 

> [!NOTE]
> Disinstallazione non rimuove gli eventuali prerequisiti mancanti che sono stati installati durante l'installazione.

## <a name="troubleshooting"></a>Risoluzione dei problemi  

1. Controllare lo stato della distribuzione eseguendo:

    `systemctl status ASCIoTAgent.service`

2. Abilitare la registrazione.  
   Se l'agente non viene avviato, abilitare la registrazione per ottenere altre informazioni.

   Attivare la registrazione da:

   1. Aprire il file di configurazione per la modifica in qualsiasi editor di Linux:

        `vi /var/ASCIoTAgent/General.config`

   1. Modificare i valori seguenti: 

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" /> 
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```
       Il **logFilePath** il valore è configurabile. 

       > [!NOTE]
       > È consigliabile attivare la registrazione **disattivata** dopo la risoluzione dei problemi è stata completata. Lasciando la registrazione **su** aumenta utilizzo dati e le dimensioni del file di log.

   1. Riavviare l'agente eseguendo:

       `systemctl restart ASCIoTAgent.service`

   1. Visualizzare il file di log per ulteriori informazioni sull'errore.  

       Percorso file di log è: `/var/ASCIoTAgent/IotAgentLog.log`

       Modificare il percorso del file in base al nome scelto per il **logFilePath** nel passaggio 2. 

## <a name="next-steps"></a>Passaggi successivi

- Leggere il Centro sicurezza di AZURE per il servizio IoT [Panoramica](overview.md)
- Altre informazioni sul Centro sicurezza di AZURE per IoT [architettura](architecture.md)
- Abilitare il [servizio](quickstart-onboard-iot-hub.md)
- Lettura di [domande frequenti](resources-frequently-asked-questions.md)
- Comprendere [avvisi](concept-security-alerts.md)