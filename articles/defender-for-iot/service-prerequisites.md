---
title: Componenti & prerequisiti
description: Informazioni dettagliate su tutti gli elementi necessari per iniziare a usare Azure Defender per i prerequisiti del servizio Internet.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.custom: references_regions
ms.openlocfilehash: b0913dc48f807c26396a38e31d293877b4561b7d
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089180"
---
# <a name="azure-defender-for-iot-prerequisites"></a>Prerequisiti di Azure Defender per l'it

Questo articolo fornisce una spiegazione dei diversi componenti di Defender per il servizio Internet delle cose, cosa è necessario iniziare e spiega i concetti di base per comprendere il servizio.

## <a name="minimum-requirements"></a>Requisiti minimi

- Monitoraggio senza agenti per i dispositivi Internet e Internet (basati sulla tecnologia CyberX)
    - Switch di rete che supportano il monitoraggio del traffico tramite la porta SPAN
    - Appliance hardware per il sensore NTA. per altre informazioni, vedere [hardware certificato](https://aka.ms/AzureDefenderforIoTBareMetalAppliance)
    - Ruolo di **collaboratore** della sottoscrizione di Azure (obbligatorio solo durante il caricamento per la definizione dei dispositivi sottoposti a commit)
    - Ruolo di **collaboratore** (livello gratuito o standard) dell'hub Internet (per la gestione connessa al cloud)
- Sicurezza per i dispositivi gestiti per l'IT gestiti tramite l'hub Azure
    - Ruolo **collaboratore** dell'hub Internet (livello standard)
    - Hub tutto: è necessario abilitare **Azure Defender per** l'feature Toggle delle cose
    - Per il supporto dei moduli di sicurezza a livello di dispositivo  
        - Defender per gli agenti di Internet delle cose supporta un elenco in continua crescita di dispositivi e piattaforme, vedere l'elenco di piattaforme [supportate](how-to-deploy-agent.md)


## <a name="supported-service-regions"></a>Aree del servizio supportate

Per altre informazioni, vedere [aree supportate dell'hub](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) Internet. 

Defender for Internet viene indirizzato tutto il traffico da tutte le aree europee all'data center regionale dell'Europa occidentale e a tutte le aree rimanenti per la data center regionale degli Stati Uniti centrali.

## <a name="next-steps"></a>Passaggi successivi

- Leggi la [Panoramica](overview.md) sulla sicurezza di Azure.
- Informazioni su come [abilitare il servizio](quickstart-onboard-iot-hub.md)
- Leggi le [domande frequenti su Defender](resources-frequently-asked-questions.md)
- Scopri come [comprendere Defender per gli avvisi relativi](concept-security-alerts.md) alle cose
