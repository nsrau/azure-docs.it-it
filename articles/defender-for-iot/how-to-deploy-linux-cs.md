---
title: Installare & distribuire l'agente Linux C#
description: Informazioni su come installare e distribuire il Defender per l'agente sicurezza basato su C# in Linux
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
ms.openlocfilehash: 48737831440a1402b6974955b4da61a4216b011f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939482"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Distribuire il Defender per l'agente di sicurezza basato su C# per le cose per Linux

Questa guida illustra come installare e distribuire il Defender per l'agente sicurezza basato su C# in Linux.

Questa guida illustra come eseguire queste operazioni:

> [!div class="checklist"]
> * Installazione
> * Verificare la distribuzione
> * Disinstallazione dell'agente
> * Risolvere problemi

## <a name="prerequisites"></a>Prerequisiti

Per altre piattaforme e versioni degli agenti, vedere [scegliere l'agente di sicurezza appropriato](how-to-deploy-agent.md).

1. Per distribuire l'agente di sicurezza, sono necessari i diritti di amministratore locale nel computer di installazione.

1. [Creare un modulo di sicurezza](quickstart-create-security-twin.md) per il dispositivo.

## <a name="installation"></a>Installazione

Per distribuire l'agente sicurezza, attenersi alla procedura seguente:

1. Scaricare la versione più recente nel computer da [GitHub](https://aka.ms/iot-security-github-cs).

1. Estrarre il contenuto del pacchetto e passare alla cartella _/Install_

1. Aggiungere le autorizzazioni di esecuzione dello script **InstallSecurityAgent** eseguendo `chmod +x InstallSecurityAgent.sh`

1. Eseguire quindi il comando seguente con **privilegi radice**:

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```

   Per ulteriori informazioni sui parametri di autenticazione, vedere [How to Configure Authentication](concept-security-agent-authentication-methods.md).

Lo script esegue le azioni seguenti:

- Installazione dei prerequisiti.

- Aggiunge un utente del servizio (con accesso interattivo disabilitato).

- Installa l'agente come **daemon** : presuppone che il dispositivo usi **systemd** per il modello di distribuzione classica.

- Configura **sudoers** per consentire all'agente di eseguire determinate attività come radice.

- Configurazione dell'agente con i parametri di autenticazione forniti.

Per altre informazioni, eseguire lo script con il parametro –help: `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Disinstallazione dell'agente

Per disinstallare l'agente, eseguire lo script con il parametro –u: `./InstallSecurityAgent.sh -u`.

> [!NOTE]
> La disinstallazione non rimuove gli eventuali prerequisiti mancanti che sono stati installati.

## <a name="troubleshooting"></a>Risoluzione dei problemi

1. Controllare lo stato di distribuzione eseguendo:

    `systemctl status ASCIoTAgent.service`

1. Abilita registrazione.
   Se l'agente non viene avviato, abilitare la registrazione per ottenere altre informazioni.

   Abilitare la registrazione come segue:

   1. Aprire il file di configurazione per la modifica in un qualsiasi editor di Linux:

        `vi /var/ASCIoTAgent/General.config`

   1. Modificare i valori seguenti:

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" />
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```

       Il valore **logFilePath** è configurabile.

       > [!NOTE]
       > È consigliabile **disattivare** la registrazione dopo aver completato la risoluzione dei problemi. Se si lascia **attivata** la registrazione, aumentano le dimensioni del file dati e l'utilizzo dei dati.

   1. Riavviare l'agente eseguendo:

       `systemctl restart ASCIoTAgent.service`

   1. Visualizzare il file di log per altre informazioni sull'errore.

       La posizione dei file di log è: `/var/ASCIoTAgent/IotAgentLog.log`

       Modificare il percorso del file in base al nome scelto per il valore **logFilePath** nel passaggio 2.

## <a name="next-steps"></a>Passaggi successivi

- Leggi la [Panoramica](overview.md) del servizio Defender for Internet
- Scopri di più su Defender per l' [architettura dell'it](architecture.md)
- Abilitare il [servizio](quickstart-onboard-iot-hub.md)
- Leggere le [Domande frequenti](resources-frequently-asked-questions.md)
- Informazioni sugli [avvisi](concept-security-alerts.md)
