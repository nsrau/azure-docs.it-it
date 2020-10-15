---
title: What ' s New with Azure Arc Enabled Servers Agent
description: Questo articolo contiene le note sulla versione per l'agente Azure Arc Enabled Server. Per molti dei problemi riepilogati sono disponibili collegamenti a ulteriori dettagli.
ms.topic: conceptual
ms.date: 09/16/2020
ms.openlocfilehash: 1918d03b5bbfaaa64b7d74c18fad4eb9a86800a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90908163"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>What ' s New with Azure Arc Enabled Servers Agent

Azure Arc Enabled Servers Connected computer Agent riceve i miglioramenti su base continuativa. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Versioni più recenti
- Problemi noti
- Correzioni di bug

## <a name="september-2020"></a>Settembre 2020

Versione: 1,0 (disponibilità generale)

### <a name="plan-for-change"></a>Modifica prevista

- Il supporto per gli agenti di anteprima (tutte le versioni precedenti alla 1,0) verrà rimosso in un aggiornamento futuro del servizio.
- È stato rimosso il supporto per l'endpoint di fallback `.azure-automation.net` . Se si dispone di un proxy, è necessario consentire l'endpoint `*.his.arc.azure.com` .
- Se l'agente del computer connesso è installato in una macchina virtuale ospitata in Azure, le estensioni VM non possono essere installate o modificate dalla risorsa server abilitati per Arc. Questo consente di evitare l'esecuzione di operazioni di estensione in conflitto dalla risorsa **Microsoft. Compute** e **Microsoft. HybridCompute** della macchina virtuale. Usare la risorsa **Microsoft. Compute** per il computer per tutte le operazioni di estensione.
- Il nome del processo di configurazione Guest è stato modificato, da *MCD* a *GCAD* in Linux e da *gcservice* a *gcarcservice* in Windows.

### <a name="new-feature"></a>Nuova funzionalità

- Aggiunta `azcmagent logs` dell'opzione per raccogliere informazioni per il supporto.
- Aggiunta dell' `azcmagent license` opzione per visualizzare il contratto di licenza.
- Aggiunta `azcmagent show --json` dell'opzione per lo stato dell'agente di output in formato facilmente analizzabile.
- Aggiunta del flag nell' `azcmagent show` output per indicare se il server si trova in una macchina virtuale ospitata in Azure.
- Aggiunta `azcmagent disconnect --force-local-only` dell'opzione per consentire il ripristino dello stato dell'agente locale quando non è possibile raggiungere il servizio di Azure.
- Aggiunta dell' `azcmagent connect --cloud` opzione per supportare cloud aggiuntivi. In questa versione, solo Azure è supportato dal servizio al momento della versione dell'agente.
- Agent è stato localizzato in lingue supportate da Azure.

### <a name="fixed"></a>Fisso

- Miglioramenti al controllo della connettività.
- Correzione del problema relativo alla perdita delle impostazioni del server proxy durante l'aggiornamento dell'agente in Linux.
- Problemi risolti durante il tentativo di installare Agent nel server che esegue Windows Server 2012 R2.
- Miglioramenti dell'affidabilità dell'installazione delle estensioni

## <a name="august-2020"></a>Agosto 2020

Versione: 0,11

- Questa versione ha annunciato in precedenza il supporto per Ubuntu 20,04. Poiché alcune estensioni di VM di Azure non supportano Ubuntu 20,04, è in corso la rimozione del supporto per questa versione di Ubuntu.

- Miglioramenti dell'affidabilità per le distribuzioni di estensioni.

### <a name="known-issues"></a>Problemi noti

Se si usa una versione precedente dell'agente Linux e la si configura per l'uso di un server proxy, è necessario riconfigurare l'impostazione del server proxy dopo l'aggiornamento. A tale scopo, eseguire `sudo azcmagent_proxy add http://proxyserver.local:83`.

## <a name="next-steps"></a>Passaggi successivi

Prima di valutare o abilitare i server con abilitazione di Arc tra più macchine virtuali ibride, vedere [Panoramica dell'agente Connected Machine](agent-overview.md) per informazioni sui requisiti, i dettagli tecnici sull'agente e i metodi di distribuzione.