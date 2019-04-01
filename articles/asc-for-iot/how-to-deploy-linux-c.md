---
title: Come installare e distribuire l'agente Linux C del Centro sicurezza di Azure per l'agente di IoT Preview | Microsoft Docs
description: Informazioni su come installare il Centro sicurezza di Azure per l'agente di IoT in Linux sia a 32 e 64 bit.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: mlottner
ms.openlocfilehash: 147813ae096114b4dfc1a20d2e0a70639aa82445
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58754444"
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

1. [Creare un modulo di protezione](quickstart-create-security-twin.md) per il dispositivo.

## <a name="installation"></a>Installazione 

Per installare e distribuire l'agente protezione, eseguire le operazioni seguenti:


1. Scaricare la versione più recente nel computer dal [Github](https://aka.ms/iot-security-github-c).

1. Estrarre il contenuto del pacchetto e passare al _/Install_ cartella.

1. Aggiungere le autorizzazioni di esecuzione per il **InstallSecurityAgent script** eseguendo le operazioni seguenti:
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Successivamente, eseguire: 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   Visualizzare [come configurare l'autenticazione](concept-security-agent-authentication-methods.md) per altre informazioni sui parametri di autenticazione.

Lo script esegue queste operazioni:

1. Installa i prerequisiti.

2. Aggiunge un utente del servizio (con accesso interattivo disabilitato).

3. Installa l'agente come un **Daemon** -presuppone che il dispositivo usa **systemd** per la gestione dei servizi.

4. Configura l'agente con i parametri di autenticazione specificati. 

Per altre informazioni, eseguire lo script con il parametro – help: 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>Disinstallare l'agente

Per disinstallare l'agente, eseguire lo script con--Disinstalla parametro:

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>risoluzione dei problemi
Controllare lo stato della distribuzione eseguendo:

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>Passaggi successivi
- Leggere il Centro sicurezza di AZURE per il servizio IoT [Panoramica](overview.md)
- Altre informazioni sul Centro sicurezza di AZURE per IoT [architettura](architecture.md)
- Abilitare il [servizio](quickstart-onboard-iot-hub.md)
- Lettura di [domande frequenti](resources-frequently-asked-questions.md)
- Comprendere [avvisi di sicurezza](concept-security-alerts.md)