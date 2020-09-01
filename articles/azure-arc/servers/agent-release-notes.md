---
title: What ' s New with Azure Arc Enabled Servers (anteprima) Agent
description: Questo articolo contiene le note sulla versione per l'agente Azure Arc Enabled Servers (anteprima). Per molti dei problemi riepilogati sono disponibili collegamenti a ulteriori dettagli.
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 0f5060189d6f9cac620b49f414e0e27bed3e356e
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236695"
---
# <a name="whats-new-with-azure-arc-enabled-servers-preview-agent"></a>What ' s New with Azure Arc Enabled Servers (anteprima) Agent

L'agente computer connesso ad Arc di Azure (anteprima) riceve miglioramenti su base continuativa. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Versioni più recenti
- Problemi noti
- Correzioni di bug

## <a name="august-2020"></a>Agosto 2020

Versione: 0,11

- Supporto per Ubuntu 20,04.

- Miglioramenti dell'affidabilità per le distribuzioni di estensioni.

### <a name="known-issues"></a>Problemi noti

Se si usa una versione precedente dell'agente Linux e la si configura per l'uso di un server proxy, è necessario riconfigurare l'impostazione del server proxy dopo l'aggiornamento. A tale scopo, eseguire `sudo azcmagent_proxy add http://proxyserver.local:83`.

## <a name="next-steps"></a>Passaggi successivi

Prima di valutare o abilitare i server abilitati per Arc (anteprima) tra più macchine ibride, vedere [Panoramica dell'agente computer connesso](agent-overview.md) per comprendere i requisiti, i dettagli tecnici sull'agente e i metodi di distribuzione.