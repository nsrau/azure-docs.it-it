---
title: Visualizza le valutazioni degli aggiornamenti di Azure Gestione aggiornamenti
description: Questo articolo descrive come visualizzare le valutazioni degli aggiornamenti per le distribuzioni degli aggiornamenti.
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d27df57e9371f16a15d3a18b7722598062377d88
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850517"
---
# <a name="view-azure-update-management-update-assessments"></a>Visualizza le valutazioni degli aggiornamenti di Azure Gestione aggiornamenti

Nell'account di automazione di Azure selezionare **Gestione aggiornamenti** per visualizzare lo stato dei computer.

Questa visualizzazione contiene informazioni sui computer, sugli aggiornamenti mancanti, sulle distribuzioni degli aggiornamenti e sulle distribuzioni degli aggiornamenti pianificate. Nella colonna **conformità** è possibile visualizzare l'ultima volta in cui il computer è stato valutato. Nella colonna **Aggiorna conformità agente** è possibile visualizzare lo stato dell'agente di aggiornamento. Se si verifica un problema, selezionare il collegamento per passare alla documentazione per la risoluzione dei problemi che consente di risolvere il problema.

Per eseguire una ricerca log che restituisce informazioni sul computer, l'aggiornamento o la distribuzione, selezionare l'elemento corrispondente nell'elenco. Si apre il riquadro **Ricerca log** con una query per l'elemento selezionato:

![Visualizzazione predefinita di Gestione aggiornamenti](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Visualizzare gli aggiornamenti mancanti

Selezionare **Aggiornamenti mancanti** per visualizzare l'elenco di aggiornamenti mancanti nei computer. Ogni aggiornamento viene inserito nell'elenco e può essere selezionato. Vengono visualizzate tutte le informazioni sul numero di computer che richiedono l'aggiornamento, i dettagli del sistema operativo e un collegamento per ulteriori informazioni. Il riquadro **Ricerca log** Mostra anche altri dettagli sugli aggiornamenti.

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
|Altri aggiornamenti     | Tutti gli altri aggiornamenti che non sono critici per natura o che non sono aggiornamenti della sicurezza.        |

Per Linux, Gestione aggiornamenti possibile distinguere tra gli aggiornamenti critici e gli aggiornamenti della sicurezza nel cloud durante la visualizzazione dei dati di valutazione. Questa granularità è possibile grazie all'arricchimento dei dati nel cloud. Per l'applicazione di patch, Gestione aggiornamenti si basa sui dati di classificazione disponibili nel computer. A differenza di altre distribuzioni, CentOS non ha queste informazioni disponibili nelle versioni RTM del prodotto. Se i computer CentOS sono configurati per restituire i dati di sicurezza per il comando seguente, Gestione aggiornamenti possibile applicare una patch in base alle classificazioni:

```bash
sudo yum -q --security check-update
```

Attualmente non è disponibile alcun metodo supportato per abilitare la classificazione nativa: disponibilità dei dati in CentOS. A questo punto, è disponibile solo il supporto per i clienti che hanno abilitato questa funzionalità autonomamente.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver visualizzato eventuali valutazioni degli aggiornamenti, è possibile pianificare una distribuzione degli aggiornamenti seguendo i passaggi descritti in [gestire gli aggiornamenti e le patch per le macchine virtuali di Azure](automation-tutorial-update-management.md).
