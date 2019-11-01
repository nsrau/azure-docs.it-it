---
title: Abilitare il servizio Centro sicurezza di Azure per IoT nell'hub IoT | Microsoft Docs
description: Informazioni su come abilitare il servizio Centro sicurezza di Azure per IoT nell'hub IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2019
ms.author: mlottner
ms.openlocfilehash: 67361c402bdbc82d5df01709dc962b59671cb2aa
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991406"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Guida introduttiva: Eseguire l'onboarding del servizio Centro sicurezza di Azure per IoT nell'hub IoT

Questo articolo illustra come abilitare il servizio Centro sicurezza di Azure per IoT nell'hub IoT esistente. Se non si ha già un hub IoT, vedere [Creare un hub IoT usando il portale di Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) per iniziare. 

> [!NOTE]
> Attualmente il Centro sicurezza di Azure per IoT supporta solo gli hub IoT di livello Standard.


## <a name="prerequisites-for-enabling-the-service"></a>Prerequisiti per l'abilitazione del servizio

- Area di lavoro Log Analytics
  - Per impostazione predefinita, nell'area di lavoro Log Analytics vengono archiviati due tipi di informazioni dal Centro sicurezza di Azure per IoT: gli **avvisi di sicurezza** e le **raccomandazioni**. 
  - È possibile scegliere di archiviare un altro tipo di informazioni, gli **eventi non elaborati**. L'archiviazione degli **eventi non elaborati** in Log Analytics comporta costi aggiuntivi. 
- Hub IoT (livello Standard)
- Soddisfare tutti i [prerequisiti del servizio](service-prerequisites.md) 

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>Abilitare il Centro sicurezza di Azure per IoT nell'hub IoT 

Per abilitare la sicurezza nell'hub IoT: 

1. Aprire l'**hub IoT** nel portale di Azure. 
1. Nel menu **Sicurezza** fare clic su **Secure your IoT solution** (Proteggere la soluzione IoT).    


Congratulazioni! È stata completata l'abilitazione del Centro sicurezza di Azure per IoT nell'hub IoT. 

### <a name="geolocation-and-ip-address-handling"></a>Georilevazione e gestione degli indirizzi IP

Per proteggere la soluzione IoT, vengono raccolti e archiviati per impostazione predefinita gli indirizzi IP delle connessioni in ingresso e in uscita da e verso i dispositivi IoT, IoT Edge e gli hub IoT. Queste informazioni sono essenziali per rilevare la connettività anomala da origini IP sospette. Ad esempio, quando vengono eseguiti tentativi per stabilire connessioni da un'origine IP di una botnet nota o da un'origine IP esterna alla georilevazione. Il servizio Centro sicurezza di Azure per IoT offre la flessibilità necessaria per abilitare e disabilitare la raccolta di dati di indirizzi IP in qualsiasi momento. 

Per abilitare o disabilitare la raccolta di dati degli indirizzi IP: 

1. Aprire l'hub IoT e quindi selezionare **Panoramica** dal menu **Sicurezza**. 
2. Scegliere la schermata **Impostazioni** e modificare le impostazioni di georilevazione e/o gestione IP nel modo desiderato.

### <a name="log-analytics-creation"></a>Creazione di Log Analytics

Quando viene attivato il Centro sicurezza di Azure per IoT, viene creata un'area di lavoro Azure Log Analytics predefinita per archiviare gli eventi di sicurezza non elaborati, gli avvisi e le raccomandazioni per i dispositivi IoT, IoT Edge e l'hub IoT. Ogni mese i primi cinque (5) GB di dati inseriti per cliente nel servizio Azure Log Analytics ogni mese sono gratuiti. Ogni GB di dati inseriti nell'area di lavoro Azure Log Analytics viene conservato senza addebiti per i primi 31 giorni. Altre informazioni sui prezzi di [Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Per modificare la configurazione dell'area di lavoro di Log Analytics:

1. Aprire l'hub IoT e quindi selezionare **Panoramica** dal menu **Sicurezza**. 
2. Scegliere la schermata **Impostazioni** e modificare la configurazione dell'area di lavoro delle impostazioni di Log Analytics nel modo desiderato.

### <a name="customize-your-iot-security-solution"></a>Personalizzare la soluzione di sicurezza per IoT
Per impostazione predefinita, l'attivazione della soluzione Centro sicurezza di Azure per IoT protegge automaticamente tutti gli hub IoT nella sottoscrizione di Azure. 

Per attivare o disattivare il servizio Centro sicurezza di Azure per IoT in un hub IoT specifico: 

1. Aprire l'hub IoT e quindi selezionare **Panoramica** dal menu **Sicurezza**. 
2. Scegliere la schermata **Impostazioni** e modificare le impostazioni di sicurezza di tutti gli hub IoT nella sottoscrizione di Azure nel modo desiderato.


## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare la soluzione, passare all'articolo successivo...

> [!div class="nextstepaction"]
> [Configurare la soluzione](quickstart-configure-your-solution.md)


