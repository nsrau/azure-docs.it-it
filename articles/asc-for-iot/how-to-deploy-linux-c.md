---
title: Guida per l'installazione e la distribuzione dell'agente di Linux C del Centro sicurezza di Azure per l'agente Microsoft Docs
description: Informazioni su come installare il Centro sicurezza di Azure per l'agente Internet in Linux a 32 bit e a 64 bit.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 3fd4287c6dd1cc42f419cfa6b252c1d276d1d5a5
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597232"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Distribuire il Centro sicurezza di Azure per l'agente di sicurezza basato su tutto il linguaggio C per Linux

Questa guida illustra come installare e distribuire il Centro sicurezza di Azure per l'agente di sicurezza basato sul linguaggio C per l'it in Linux.

Questa guida illustra come eseguire queste operazioni: 
> [!div class="checklist"]
> * Installa
> * Verifica distribuzione
> * Disinstallare l'agente
> * Risolvere problemi 

## <a name="prerequisites"></a>Prerequisiti

Per altre piattaforme e versioni degli agenti, vedere [scegliere l'agente di sicurezza appropriato](how-to-deploy-agent.md).

1. Per distribuire l'agente protezione, sono necessari i diritti di amministratore locale nel computer in cui si vuole installare (sudo).

1. [Creare un modulo di sicurezza](quickstart-create-security-twin.md) per il dispositivo.

## <a name="installation"></a>Installazione 

Per installare e distribuire l'agente sicurezza, utilizzare il flusso di lavoro seguente:


1. Scaricare la versione più recente nel computer da [GitHub](https://aka.ms/iot-security-github-c).

1. Estrarre il contenuto del pacchetto e passare alla cartella _/Install_.

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

2. Aggiunge un utente del servizio (con accesso interattivo disabilitato).

3. Installazione dell'agente come **Daemon**, supponendo che il dispositivo usi **systemd** per la gestione dei servizi.

4. Configurazione dell'agente con i parametri di autenticazione forniti. 

Per altre informazioni, eseguire lo script con il parametro –help: 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>Disinstallare l'agente

Per disinstallare l'agente, eseguire lo script con il parametro –-uninstall:

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>risoluzione dei problemi
Controllare lo stato di distribuzione eseguendo:

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>Passaggi successivi
- Leggi il Centro sicurezza di Azure per [informazioni generali](overview.md) sul servizio Internet
- Scopri di più sul centro sicurezza di Azure per l' [architettura dell'it](architecture.md)
- Abilitare il [servizio](quickstart-onboard-iot-hub.md)
- Leggere le [Domande frequenti](resources-frequently-asked-questions.md)
- Informazioni sugli [avvisi di sicurezza](concept-security-alerts.md)