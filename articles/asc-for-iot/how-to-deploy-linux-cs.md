---
title: Installare & distribuire l'agente Linux C#
description: Informazioni su come installare il Centro sicurezza di Azure per l'agente Internet in Linux a 32 bit e a 64 bit.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2019
ms.author: mlottner
ms.openlocfilehash: 40c6ea91fd84a0f088ed770cd7c4c3ea7b8b1c91
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311140"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Distribuire un agente di sicurezza per Linux basato su C# del Centro sicurezza di Azure per IoT

Questa guida illustra come installare e distribuire il Centro sicurezza di Azure per l'agente di sicurezza basato su C# in Linux.

Questa guida illustra come eseguire queste operazioni:

> [!div class="checklist"]
> * Installazione
> * Verificare la distribuzione
> * Disinstallare l'agente
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

### <a name="uninstall-the-agent"></a>Disinstallare l'agente

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

- Leggi il Centro sicurezza di Azure per [informazioni generali](overview.md) sul servizio Internet
- Scopri di più sul centro sicurezza di Azure per l' [architettura dell'it](architecture.md)
- Abilitare il [servizio](quickstart-onboard-iot-hub.md)
- Leggere le [Domande frequenti](resources-frequently-asked-questions.md)
- Informazioni sugli [avvisi](concept-security-alerts.md)
