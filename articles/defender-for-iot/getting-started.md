---
title: Opzioni di distribuzione
description: Inizia a comprendere il flusso di lavoro di base di Defender per le funzionalità e il servizio di Internet.
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
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: e3de253ee6f45f9296d6b09189fe4bc488be36ad
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090064"
---
# <a name="getting-started-with-azure-defender-for-iot"></a>Introduzione ad Azure Defender per l'it

Questo articolo descrive i processi di distribuzione e di onboarding necessari per ottenere Azure Defender per l'esecuzione di tutto. Sono inoltre necessari passaggi aggiuntivi. Si consiglia di comprendere questi passaggi e acquisire familiarità con le informazioni contenute nei documenti associati.

Una volta completati tutti i passaggi, Azure Defender per i sensori Internet monitorerà la rete. A seconda di come si configura la soluzione, i rilevamenti possono essere inviati anche alla console di gestione locale o all'hub Internet delle cose.

Completare la procedura seguente per fare in modo che Azure Defender sia attivo e in esecuzione.

## <a name="1-set-up-azure"></a>1. configurare Azure

- Configurare un account Azure. Per altre informazioni, vedere [creare un account Azure](https://docs.microsoft.com/learn/modules/create-an-azure-account/).

- Firewall o proxy: se si dispone di un firewall o di un dispositivo di rete simile configurato per consentire connessioni specifiche, verificare che *. azure-devices.net:443 sia aperto al firewall o al proxy. Se i caratteri jolly non sono supportati o si desidera un maggiore controllo, il nome di dominio completo dell'hub Internet deve essere aperto nel FW o nel proxy. Per altre informazioni, vedere [endpoint dell'hub di riferimento-](/azure/iot-hub/iot-hub-devguide-endpoints)Internet.

## <a name="2-deploy-hardware-software-and-onboard-to-sensor"></a>2. distribuire l'hardware, il software e l'onboarding nel sensore

- Acquistare l'hardware del sensore e installare il software. Seguire i passaggi descritti in questo articolo e per altre informazioni, vedere questo articolo e la Guida all' [installazione](https://aka.ms/AzureDefenderforIoTInstallSensorISO)di [Defender per l'hardware](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) .

  - Dopo aver installato il sensore, registrare in modo sicuro le credenziali di accesso del sensore. Sono necessarie le credenziali per caricare il file di attivazione nel sensore.

  - Se si utilizzano sensori gestiti localmente, registrare in modo sicuro l'indirizzo IP del sensore o il nome del sensore definito nell'installazione. È possibile usarlo durante la creazione di un nome di sensore durante la registrazione del sensore nel portale Defender for Internet. È possibile usarli in un secondo momento per garantire una maggiore semplicità di rilevamento e nomi coerenti tra il nome di registrazione nel portale di Azure Defender per le cose e l'indirizzo IP del sensore distribuito visualizzato nella console del sensore.

- Registrare il sensore con il portale Defender for Internet e scaricare un file di attivazione del sensore.

- Caricare il file di attivazione nel sensore.

## <a name="3-perform-network-setup-for-sensor-monitoring-and-management"></a>3. eseguire la configurazione di rete per il monitoraggio e la gestione dei sensori

- Connettere il sensore alla rete. Descritto nella [Guida all'installazione di rete](https://aka.ms/AzureDefenderForIoTNetworkSetup).

## <a name="4-start-discovering-your-network"></a>4. iniziare a individuare la rete

- Modificare le impostazioni di sistema nella console del sensore.

- Connettere i sensori a una console di gestione locale.

Per altre informazioni, vedere il manuale dell'utente di [Azure Defender per il sensore](https://aka.ms/AzureDefenderforIoTUserGuide) Internet e il [manuale dell'utente della console di gestione locale di Defender for](https://aka.ms/DefenderForIoTManagementConsole)Internet.

## <a name="5-populate-azure-sentinel-with-alert-information"></a>5. popolare Azure Sentinel con le informazioni sugli avvisi

- Per inviare le informazioni sugli avvisi ad Azure Sentinel, configurare Sentinel di Azure: [connettere i dati da Defender per le cose ad Azure Sentinel](how-to-configure-with-sentinel.md).
 

## <a name="next-steps"></a>Passaggi successivi

- Abilita [Defender per](quickstart-onboard-iot-hub.md) le cose
- Configurare la [soluzione](quickstart-configure-your-solution.md)
- [Creare moduli di sicurezza](quickstart-create-security-twin.md)
- Configurare gli [avvisi personalizzati](quickstart-create-custom-alerts.md)
- [Distribuire un agente di sicurezza](how-to-deploy-agent.md)
