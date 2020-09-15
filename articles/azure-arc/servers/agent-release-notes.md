---
title: What ' s New with Azure Arc Enabled Servers (anteprima) Agent
description: Questo articolo contiene le note sulla versione per l'agente Azure Arc Enabled Servers (anteprima). Per molti dei problemi riepilogati sono disponibili collegamenti a ulteriori dettagli.
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: 3b739401603f6dc18b9f48fb3cb6e28023a051ab
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532172"
---
# <a name="whats-new-with-azure-arc-enabled-servers-preview-agent"></a>What ' s New with Azure Arc Enabled Servers (anteprima) Agent

L'agente computer connesso ad Arc di Azure (anteprima) riceve miglioramenti su base continuativa. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

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

Prima di valutare o abilitare i server abilitati per Arc (anteprima) tra più computer ibridi, vedere [Panoramica dell'agente Connected Machine](agent-overview.md) per informazioni su requisiti, i dettagli tecnici sull'agente e metodi di distribuzione.