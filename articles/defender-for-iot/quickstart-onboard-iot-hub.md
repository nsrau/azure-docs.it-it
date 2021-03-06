---
title: 'Guida introduttiva: Abilitare il servizio'
description: Informazioni su come eseguire l'onboarding e abilitare il servizio di sicurezza Defender per IoT nell'hub IoT di Azure.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: 786fcd1a0c6d7df2c38a086a830a63f7179d7d40
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352508"
---
# <a name="quickstart-onboard-azure-defender-for-iot-service-in-iot-hub"></a>Avvio rapido: Eseguire l'onboarding del servizio Azure Defender per IoT nell'hub IoT

Questo articolo illustra come abilitare il servizio Defender per IoT nell'hub IoT esistente. Se non si ha già un hub IoT, vedere [Creare un hub IoT usando il portale di Azure](../iot-hub/iot-hub-create-through-portal.md) per iniziare.

> [!NOTE]
> Attualmente Defender per IoT supporta solo hub IoT del livello Standard.

## <a name="prerequisites-for-enabling-the-service"></a>Prerequisiti per l'abilitazione del servizio

- Area di lavoro Log Analytics
  - Per impostazione predefinita, nell'area di lavoro Log Analytics vengono archiviati due tipi di informazioni da Defender per IoT: gli **avvisi di sicurezza** e le **raccomandazioni**.
  - È possibile scegliere di archiviare un altro tipo di informazioni, gli **eventi non elaborati**. L'archiviazione degli **eventi non elaborati** in Log Analytics comporta costi aggiuntivi.
- Hub IoT (livello Standard)
- Soddisfare tutti i [prerequisiti del servizio](service-prerequisites.md)

## <a name="enable-defender-for-iot-on-your-iot-hub"></a>Abilitare Defender for IoT nell'hub IoT

Per abilitare la sicurezza nell'hub IoT:

1. Aprire l'**hub IoT** nel portale di Azure.
1. Nel menu **Sicurezza** fare clic su **Secure your IoT solution** (Proteggere la soluzione IoT).

Congratulazioni! L'abilitazione di Defender per IoT nell'hub IoT è stata completata.

### <a name="geolocation-and-ip-address-handling"></a>Georilevazione e gestione degli indirizzi IP

Per proteggere la soluzione IoT, vengono raccolti e archiviati per impostazione predefinita gli indirizzi IP delle connessioni in ingresso e in uscita da e verso i dispositivi IoT, IoT Edge e gli hub IoT. Queste informazioni sono essenziali per rilevare la connettività anomala da origini IP sospette. Ad esempio, quando vengono eseguiti tentativi per stabilire connessioni da un'origine IP di una botnet nota o da un'origine IP esterna alla georilevazione. Il servizio Defender per IoT offre la flessibilità per abilitare e disabilitare in qualsiasi momento la raccolta di dati sugli indirizzi IP.

Per abilitare o disabilitare la raccolta di dati degli indirizzi IP:

1. Aprire l'hub IoT e quindi scegliere **Impostazioni** dal menu **Sicurezza**.
1. Scegliere la schermata **Raccolta dati** e modificare le impostazioni di georilevazione e/o gestione IP nel modo desiderato.

### <a name="log-analytics-creation"></a>Creazione di Log Analytics

Quando il servizio Defender per IoT viene attivato, viene creata un'area di lavoro Log Analytics predefinita in cui archiviare eventi di sicurezza, avvisi e raccomandazioni di base per i dispositivi IoT, IoT Edge e hub IoT. Ogni mese i primi cinque (5) GB di dati inseriti per cliente nel servizio Azure Log Analytics ogni mese sono gratuiti. Ogni GB di dati inseriti nell'area di lavoro Azure Log Analytics viene conservato senza addebiti per i primi 31 giorni. Altre informazioni sui prezzi di [Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Per modificare la configurazione dell'area di lavoro di Log Analytics:

1. Aprire l'hub IoT e quindi scegliere **Impostazioni** dal menu **Sicurezza**.
1. Scegliere la schermata **Raccolta dati** e modificare la configurazione dell'area di lavoro delle impostazioni di Log Analytics nel modo desiderato.

### <a name="customize-your-iot-security-solution"></a>Personalizzare la soluzione di sicurezza per IoT

Per impostazione predefinita, l'attivazione della soluzione Defender per IoT protegge automaticamente tutti gli hub IoT inclusi nella sottoscrizione di Azure.

Per attivare o disattivare il servizio Defender per IoT in un hub IoT specifico:

1. Aprire l'hub IoT e quindi scegliere **Impostazioni** dal menu **Sicurezza**.
1. Scegliere la schermata **Raccolta dati** e modificare le impostazioni di sicurezza di qualsiasi hub IoT nella sottoscrizione di Azure nel modo desiderato.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare la soluzione, passare all'articolo successivo...

> [!div class="nextstepaction"]
> [Configurare la soluzione](quickstart-configure-your-solution.md)