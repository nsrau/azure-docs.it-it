---
title: Distribuisci Management Console in Azure Defender per l'it
description: Informazioni su come distribuire la console di gestione in Azure Defender per l'it.
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
ms.date: 10/14/2020
ms.author: rkarlin
ms.openlocfilehash: 6af943951b2dba0c4a11b08ad1bcd961cf1c0fc2
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094416"
---
# <a name="deploy-the-management-console"></a>Distribuire la console di gestione
Questo articolo descrive come distribuire Azure Defender per la console di gestione locale.

## <a name="the-on-premises-management-console"></a>Console di gestione locale

La console di gestione locale offre una visualizzazione consolidata di tutte le risorse di rete e offre una visualizzazione in tempo reale degli elementi principali e degli indicatori di rischio e degli avvisi per tutte le funzionalità. Strettamente integrato con i flussi di lavoro SOC ed eseguire libri, consente di semplificare la definizione delle priorità delle attività di mitigazione e la correlazione tra siti delle minacce.

- Olistica: Riduci la complessità con una singola piattaforma unificata per la gestione delle risorse, la gestione dei rischi e delle vulnerabilità, nonché il monitoraggio delle minacce con risposta agli eventi imprevisti.

- Aggregazione e correlazione: Visualizza, aggrega e analizza i dati e gli avvisi raccolti da tutti i siti.

- Controllare tutti i sensori: configurare e monitorare tutti i sensori da un'unica posizione.

   ![Azure Defender per la visualizzazione di gestione del sito](media/updates/image2.png)

## <a name="deploy-the-on-premises-management-console-appliance"></a>Distribuire il dispositivo della console di gestione locale

Questo processo richiede l'acquisizione dell'hardware e l'installazione del software. La soluzione viene eseguita su appliance certificate. Vedere la [Guida alle specifiche dell'hardware di Azure Defender per](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) le informazioni di riferimento per l'acquisto dell'appliance certificata.

Per informazioni dettagliate sul download dell'immagine ISO e sull'installazione del software per i sensori, vedere la [Guida all'installazione di Azure Defender per](https://aka.ms/AzureDefenderforIoTInstallSensorISO) l'it.

**Per distribuire la console di gestione locale:**

1. Passare a Microsoft Azure Defender per l'it.

2. Selezionare **console di gestione locale**.

   ![Azure Defender per la visualizzazione della console di gestione locale](media/updates/image15.png)

3. Selezionare la versione 3,1 dal menu **Seleziona versione** .

4. Selezionare **Scarica** e salvare il file.

5. Dopo l'installazione del software, eseguire le attività di configurazione di rete. Per informazioni dettagliate, vedere la [Guida all'installazione di rete di Azure Defender per](https://aka.ms/AzureDefenderForIoTNetworkSetup) l'it.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle opzioni di configurazione, passare alla Guida alle procedure per la configurazione del modulo.
> [!div class="nextstepaction"]
> [Guida alle procedure di configurazione del modulo](./how-to-agent-configuration.md)
