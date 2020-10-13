---
title: Installare & distribuire l'agente di Linux C
description: Informazioni su come installare e distribuire il Defender per l'agente di sicurezza basato su tutto il linguaggio C in Linux
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 8c03f6b882c8b1a64c9f256493c5d586b5fa0f89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940050"
---
# <a name="deploy-defender-for-iot-c-based-security-agent-for-linux"></a>Distribuire Defender per l'agente di sicurezza basato su Internet

Questa guida illustra come installare e distribuire il Defender per l'agente di sicurezza basato su tutto il linguaggio C in Linux.

Questa guida illustra come eseguire queste operazioni:

> [!div class="checklist"]
> * Installazione
> * Verificare la distribuzione
> * Disinstallazione dell'agente
> * Risolvere problemi

## <a name="prerequisites"></a>Prerequisiti

Per altre piattaforme e versioni degli agenti, vedere [scegliere l'agente di sicurezza appropriato](how-to-deploy-agent.md).

1. Per distribuire l'agente protezione, sono necessari i diritti di amministratore locale nel computer in cui si vuole installare (sudo).

1. [Creare un modulo di sicurezza](quickstart-create-security-twin.md) per il dispositivo.

## <a name="installation"></a>Installazione

Per installare e distribuire l'agente sicurezza, utilizzare il flusso di lavoro seguente:

1. Scaricare la versione più recente nel computer da [GitHub](https://aka.ms/iot-security-github-c).

1. Estrarre il contenuto del pacchetto e passare alla cartella _/src/Installation_

1. Aggiungere le autorizzazioni in esecuzione allo **script InstallSecurityAgent** eseguendo il comando seguente:

   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Quindi eseguire:

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```

   Per altre informazioni sui parametri di autenticazione, vedere la pagina [Come configurare l'autenticazione](concept-security-agent-authentication-methods.md).

Questo script esegue la funzione seguente:

1. Installazione dei prerequisiti.

1. Aggiunge un utente del servizio (con accesso interattivo disabilitato).

1. Installazione dell'agente come **Daemon**, supponendo che il dispositivo usi **systemd** per la gestione dei servizi.

1. Configurazione dell'agente con i parametri di autenticazione forniti.

Per altre informazioni, eseguire lo script con il parametro –help:

```./InstallSecurityAgent.sh --help```

### <a name="uninstall-the-agent"></a>Disinstallazione dell'agente

Per disinstallare l'agente, eseguire lo script con il parametro –-uninstall:

```./InstallSecurityAgent.sh -–uninstall```

## <a name="troubleshooting"></a>Risoluzione dei problemi

Controllare lo stato di distribuzione eseguendo:

```systemctl status ASCIoTAgent.service```

## <a name="next-steps"></a>Passaggi successivi

- Leggi la [Panoramica](overview.md) del servizio Defender for Internet
- Scopri di più su Defender per l' [architettura dell'it](architecture.md)
- Abilitare il [servizio](quickstart-onboard-iot-hub.md)
- Leggere le [Domande frequenti](resources-frequently-asked-questions.md)
- Informazioni sugli [avvisi di sicurezza](concept-security-alerts.md)
