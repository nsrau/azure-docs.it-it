---
title: Azure Defender per IoT
description: Scopri di più su Azure Defender
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 307916680d9a05a5083aea057b2ef4b855a4ff57
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301806"
---
# <a name="introduction-to-azure-defender-for-iot"></a>Introduzione ad Azure Defender per l'it

Unifica la gestione della sicurezza e Abilita l'analisi e il rilevamento delle minacce end-to-end nei carichi di lavoro cloud ibridi e nella tua soluzione Azure.

Azure Defender per l'it, fornisce:

- **Individuazione di asset e mapping di rete**, inclusi dettagli come il produttore del dispositivo, il tipo di dispositivo e la modalità di comunicazione dei dispositivi sulla rete
- **Gestione delle vulnerabilità**, incluse informazioni su cves, porte aperte e connessioni Internet non autorizzate
- **Monitoraggio continuo delle minacce**, con avvisi in tempo reale che indicano un'attività anomala o non autorizzata, ad esempio attacchi mirati o malware

Tutti i dettagli sono disponibili nella [documentazione dedicata](https://docs.microsoft.com/azure/asc-for-iot/overview).

## <a name="availability"></a>Disponibilità
|Aspetto|Dettagli|
|----|:----|
|Stato versione:|Disponibile a livello generale|
|Prezzi|Richiede [Azure Defender](security-center-pricing.md)|
|Ruoli e autorizzazioni necessari:|Autorizzazioni di scrittura per il gruppi del computer|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) Nazionale/sovrano (US Gov, Cina gov, altri gov)|
|||

## <a name="what-devices-can-azure-defender-for-iot-secure"></a>Quali dispositivi è possibile proteggere con Azure Defender?
Combinando le funzionalità del Centro sicurezza, Azure Defender e la tecnologia senza agente CyberX, è possibile proteggere:

- **Dispositivi Greenfield** connessi tramite l'hub Internet. In questo modo, le organizzazioni possono accelerare le proprie iniziative, proteggendo i dispositivi moderni e nativi Internet e i dispositivi ICS/SCADA tradizionali con una singola soluzione unificata.
    - Carica i nuovi dispositivi e applica i criteri di sicurezza nei tuoi carichi di lavoro (dispositivi foglia, dispositivi Microsoft Edge, hub Internet) per garantire la conformità agli standard di sicurezza e al miglioramento del comportamento di sicurezza.

- **Dispositivi Brownfield non gestiti** usati in ambienti con tecnologia operativa (OT), ad esempio produzione, sistemi di gestione della compilazione (BMS), scienze biologiche, utilità di energia e acqua, petrolio & gas e logistica. 
    - Per proteggere i dispositivi non gestiti, Azure Defender for Internet usa un sensore locale per l'analisi del traffico di rete passiva e non invasiva (NTA) che ha un effetto negativo sulle prestazioni negli ambienti OT. 
    - Incorpora anche una conoscenza approfondita dei protocolli di gestione delle risorse e delle cose specializzate, combinati con analisi del comportamento e apprendimento automatico, che consentono di evitare la necessità di configurare regole o firme, ottenendo distribuzioni tipiche in pochi minuti o ore anziché in giorni o settimane. 


## <a name="azure-defender-for-iot-integration-with-azure-sentinel"></a>Azure Defender per l'integrazione con Azure Sentinel
Per abilitare il monitoraggio e la governance della sicurezza unificata di IT/OT, Azure Defender per l'IT si integra in modo nativo con [Azure Sentinel](../sentinel/overview.md).

I team di secops possono:

- Rileva e Rispondi rapidamente a minacce per le cose o le minacce tramite i Playbook specifici per OT inclusi in Sentinel
- Sfrutta i vantaggi dell'Intelligence per le minacce, che è stata aggiornata continuamente, fornita dalla sezione 52, il team di intelligence per le minacce all'interno di Microsoft
- Integra Azure Defender per le cose con i flussi di lavoro SOC esistenti e con strumenti di sicurezza di terze parti come Splunk, IBM QRadar e ServiceNow


## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come Azure Defender per il Centro sicurezza di Azure. Per altre informazioni, vedere:

- [Introduzione al Centro sicurezza di Azure per IoT](../asc-for-iot/overview.md)
