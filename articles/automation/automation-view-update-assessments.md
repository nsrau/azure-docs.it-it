---
title: Visualizza le valutazioni degli aggiornamenti di Azure Gestione aggiornamenti
description: Questo articolo descrive come visualizzare le valutazioni degli aggiornamenti per le distribuzioni degli aggiornamenti
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e88622ede6437086b86a33081d6ec9b9ea50ef65
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377720"
---
# <a name="view-azure-update-management-update-assessments"></a>Visualizza le valutazioni degli aggiornamenti di Azure Gestione aggiornamenti

Selezionare **Gestione aggiornamenti** nell'account di Automazione per visualizzare lo stato dei computer.

Questa visualizzazione contiene informazioni sui computer, sugli aggiornamenti mancanti, sulle distribuzioni degli aggiornamenti e sulle distribuzioni degli aggiornamenti pianificate. Nella colonna **CONFORMITÀ** è possibile vedere quando è stata eseguita l'ultima valutazione del computer. Nella colonna **UPDATE AGENT READINESS** (Idoneità agente di aggiornamento) è possibile verificare l'integrità dell'agente di aggiornamento. Se si verifica un problema, selezionare il collegamento alla documentazione sulla risoluzione dei problemi, che contiene informazioni utili sulle operazioni da eseguire per risolverlo.

Per eseguire una ricerca log che restituisce informazioni sul computer, l'aggiornamento o la distribuzione, selezionare l'elemento nell'elenco. Si apre il riquadro **Ricerca log** con una query per l'elemento selezionato:

![Visualizzazione predefinita di Gestione aggiornamenti](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Visualizzare gli aggiornamenti mancanti

Selezionare **Aggiornamenti mancanti** per visualizzare l'elenco di aggiornamenti mancanti nei computer. Ogni aggiornamento viene inserito nell'elenco e può essere selezionato. Sono disponibili informazioni relative al numero di computer che richiedono l'aggiornamento e al sistema operativo, oltre a un collegamento per accedere ad altre informazioni. Il riquadro **Ricerca log** visualizza altri dettagli sugli aggiornamenti.

![Aggiornamenti mancanti](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Classificazioni degli aggiornamenti

Nelle tabelle che seguono sono riportate le classificazioni degli aggiornamenti in Gestione aggiornamenti, con una definizione per ogni classificazione.

### <a name="windows"></a>Windows

|Classificazione  |Description  |
|---------|---------|
|Aggiornamenti critici     | Un aggiornamento per un problema specifico che risolve un bug critico non correlato alla sicurezza.        |
|Aggiornamenti della sicurezza     | Un aggiornamento per un problema specifico del prodotto correlato alla sicurezza.        |
|Aggiornamenti cumulativi     | Un set cumulativo di aggiornamenti rapidi, contenuti nello stesso pacchetto per facilitarne la distribuzione.        |
|Feature Pack     | Nuove funzionalità del prodotto distribuite di fuori di una versione del prodotto.        |
|Service Pack     | Un set cumulativo di aggiornamenti rapidi applicati a un'applicazione.        |
|Aggiornamenti della definizione     | Un aggiornamento per un virus o altri file di definizione.        |
|Strumenti     | Utilità o funzionalità che consente di completare una o più attività.        |
|Aggiornamenti     | Un aggiornamento di un'applicazione o un file attualmente installati.        |

### <a name="linux-2"></a>Linux

|Classificazione  |Description  |
|---------|---------|
|Aggiornamenti critici e della sicurezza     | Aggiornamenti per un problema specifico o specifico del prodotto, correlato alla sicurezza.         |
|Altri aggiornamenti     | Tutti gli altri aggiornamenti non critici per loro natura o che non sono aggiornamenti della sicurezza.        |

Per Linux, Gestione aggiornamenti è in grado di distinguere tra gli aggiornamenti critici e quelli della sicurezza nel cloud, visualizzando i dati di valutazione dovuti all'arricchimento dei dati nel cloud. Per l'applicazione di patch, Gestione aggiornamenti si affida ai dati di classificazione disponibili nel computer. A differenza di altre distribuzioni, CentOS non mette a disposizione queste informazioni per impostazione predefinita. Se i computer CentOS sono configurati in modo da restituire dati sulla sicurezza per il comando seguente, Gestione aggiornamenti sarà in grado di applicare patch in base alle classificazioni.

```bash
sudo yum -q --security check-update
```

Attualmente non è supportato alcun metodo per abilitare i dati di classificazione nativi in CentOS. In questa fase viene offerto il miglior supporto possibile ai clienti che abilitano autonomamente questa funzionalità.

## <a name="next-steps"></a>Fasi successive

Dopo aver visualizzato eventuali valutazioni degli aggiornamenti, è possibile pianificare una distribuzione degli aggiornamenti attenendosi alla procedura descritta in [gestire gli aggiornamenti e le patch per le macchine virtuali di Azure](automation-tutorial-update-management.md).