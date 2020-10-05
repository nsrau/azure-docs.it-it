---
title: Azure Defender per IoT
description: Informazioni su Azure Defender per IoT
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 2f41aac085fef62e0356a93a07823f21d7ba3667
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91448345"
---
# <a name="introduction-to-azure-defender-for-iot"></a>Introduzione ad Azure Defender per IoT

È possibile unificare la gestione della sicurezza e abilitare il rilevamento e l'analisi delle minacce end-to-end nei carichi di lavoro cloud ibridi e nella soluzione Azure IoT.

Azure Defender per IoT offre:

- **Individuazione delle risorse e mapping di rete**, inclusi dettagli come il produttore del dispositivo, il tipo di dispositivo e la modalità di comunicazione dei dispositivi in rete
- **Gestione delle vulnerabilità**, incluse informazioni su CVE, porte aperte e connessioni Internet non autorizzate
- **Monitoraggio continuo delle minacce**, con avvisi in tempo reale che indicano qualsiasi attività anomala o non autorizzata, ad esempio attacchi mirati o malware

Per i dettagli completi, vedere la [documentazione dedicata](https://docs.microsoft.com/azure/asc-for-iot/overview).

## <a name="availability"></a>Disponibilità
|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Disponibile a livello generale|
|Prezzi:|È necessario [Azure Defender](security-center-pricing.md)|
|Autorizzazioni e ruoli obbligatori:|Autorizzazioni di scrittura per i gruppi di sicurezza di rete del computer|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)|
|||

## <a name="what-devices-can-azure-defender-for-iot-secure"></a>Quali dispositivi è possibile proteggere con Azure Defender per IoT?
Unendo le funzionalità del Centro sicurezza, di Azure Defender e di CyberX Agentless Technology è possibile proteggere:

- **Dispositivi greenfield** connessi tramite hub IoT. In questo modo le organizzazioni possono accelerare le proprie iniziative IoT proteggendo sia i dispositivi moderni nativi di Internet sia i dispositivi ICS/SCADA tradizionali con un'unica soluzione unificata.
    - È possibile eseguire l'onboarding di nuovi dispositivi e applicare i criteri di sicurezza nei diversi carichi di lavoro (dispositivi foglia, dispositivi Microsoft Edge, hub IoT) per assicurare la conformità agli standard di sicurezza e migliorare il comportamento di sicurezza.

- **Dispositivi brownfield non gestiti** usati in ambienti di tecnologia operativa (OT), ad esempio nel settore produttivo, nei sistemi di gestione degli edifici, nelle scienze biologiche, nei servizi pubblici relativi a energia elettrica e acqua, nel settore petrolifero e del gas e nella logistica. 
    - Per proteggere i dispositivi non gestiti, Azure Defender per IoT usa un sensore locale per eseguire un'analisi del traffico di rete passiva e non invasiva che non ha alcun impatto sulle prestazioni negli ambienti di tecnologia operativa. 
    - Incorpora anche una conoscenza approfondita di protocolli IoT/OT speciali, combinati con funzionalità brevettate di Machine Learning e analisi del comportamento basata su IoT/OT, che eliminano la necessità di configurare regole o firme, ottenendo distribuzioni tipiche in minuti o ore anziché in giorni o settimane. 


## <a name="azure-defender-for-iot-integration-with-azure-sentinel"></a>Integrazione di Azure Defender per IoT con Azure Sentinel
Per abilitare la governance e il monitoraggio unificati della sicurezza IoT/OT, Azure Defender per IoT si integra in modo nativo con [Azure Sentinel](../sentinel/overview.md).

I team responsabili delle operazioni di sicurezza possono:

- Rilevare le minacce IoT/OT e reagire rapidamente tramite playbook SOAR specifici di OT inclusi in Sentinel
- Sfruttare le funzionalità di intelligence sulle minacce specifiche di IoT/OT sempre aggiornate fornite da Section 52, il team Microsoft interno responsabile dell'intelligence sulle minacce IoT/OT
- Integrare Azure Defender per IoT con flussi di lavoro SOC e strumenti di sicurezza di terze parti esistenti come Splunk, IBM QRadar e ServiceNow


## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni su Azure Defender per IoT nel Centro sicurezza di Azure. Per altre informazioni, vedere:

- [Introduzione al Centro sicurezza di Azure per IoT](../asc-for-iot/overview.md)
