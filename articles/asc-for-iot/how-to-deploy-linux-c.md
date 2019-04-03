---
title: Come installare e distribuire l'agente Linux C del Centro sicurezza di Azure per l'agente di IoT Preview | Microsoft Docs
description: Informazioni su come installare il Centro sicurezza di Azure per l'agente di IoT in Linux sia a 32 e 64 bit.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: mlottner
ms.openlocfilehash: 0089fd1af6576f9bcdebe4b7f270a573205dea82
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58861939"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Distribuire il Centro sicurezza di Azure per l'agente protezione basata su IoT C per Linux

> [!IMPORTANT]
> Il Centro sicurezza di Azure per IoT è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questa guida illustra come installare e distribuire Azure Security Center (ASC) per l'agente protezione basata su IoT C in Linux.

Questa guida illustra come eseguire queste operazioni: 
> [!div class="checklist"]
> * Installa
> * Verificare la distribuzione
> * Disinstallare l'agente
> * Risolvere problemi 

## <a name="prerequisites"></a>Prerequisiti

Per altre piattaforme e versioni dell'agente, vedere [scegliere l'agente di protezione adeguato](how-to-deploy-agent.md).

1. Per distribuire l'agente protezione, sono necessari diritti di amministratore locale nel computer che si desidera installare in (sudo).

1. [Creare un modulo di sicurezza](quickstart-create-security-twin.md) per il dispositivo.

## <a name="installation"></a>Installazione 

Per installare e distribuire l'agente di sicurezza, eseguire queste operazioni:


1. Scaricare la versione più recente per il proprio computer da [GitHub](https://aka.ms/iot-security-github-c).

1. Estrarre il contenuto del pacchetto e passare alla cartella _/Install_.

1. Aggiungere le autorizzazioni di esecuzione dello script **InstallSecurityAgent** eseguendo:
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Quindi eseguire: 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   Per altre informazioni sui parametri di autenticazione, vedere la pagina [Come configurare l'autenticazione](concept-security-agent-authentication-methods.md).

Lo script esegue queste operazioni:

1. Installazione dei prerequisiti.

2. Aggiunta di un utente del servizio (con accesso interattivo disattivato).

3. Installazione dell'agente come **Daemon**, supponendo che il dispositivo usi **systemd** per la gestione dei servizi.

4. Configurazione dell'agente con i parametri di autenticazione forniti. 

Per altre informazioni, eseguire lo script con il parametro –help: 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>Disinstallare l'agente

Per disinstallare l'agente, eseguire lo script con--Disinstalla parametro:

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>risoluzione dei problemi
Controllare lo stato di distribuzione eseguendo:

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>Passaggi successivi
- Leggere la [Panoramica](overview.md) sul servizio del Centro sicurezza di Azure per IoT
- Vedere altre informazioni sull'[Architettura](architecture.md) del Centro sicurezza di Azure per IoT
- Abilitare il [servizio](quickstart-onboard-iot-hub.md)
- Leggere le [Domande frequenti](resources-frequently-asked-questions.md)
- Informazioni sugli [avvisi di sicurezza](concept-security-alerts.md)