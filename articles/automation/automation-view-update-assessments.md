---
title: Visualizzare le valutazioni degli aggiornamenti di Automazione di Azure
description: Questo articolo descrive come visualizzare le valutazioni degli aggiornamenti per le distribuzioni di Gestione aggiornamenti.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 321146364897d46a403bdfd6789fcb219179d88c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830600"
---
# <a name="view-update-assessments"></a>Visualizzare la valutazione degli aggiornamenti

Nell'account di Automazione di Azure selezionare **Gestione aggiornamenti** per visualizzare lo stato dei computer.

Questa visualizzazione contiene informazioni sui computer, sugli aggiornamenti mancanti, sulle distribuzioni degli aggiornamenti e sulle distribuzioni degli aggiornamenti pianificate. Nella colonna **CONFORMITÀ** è possibile vedere quando è stata eseguita l'ultima valutazione del computer. Nella colonna **AGGIORNA IDONEITÀ AGENTE** è possibile verificare l'integrità dell'agente di aggiornamento. Se è presente un problema, selezionare il collegamento alla documentazione sulla risoluzione dei problemi, che contiene informazioni utili per risolverlo.

Per eseguire una ricerca log che restituisce informazioni sul computer, l'aggiornamento o la distribuzione, selezionare la voce corrispondente nell'elenco. Si apre il riquadro Ricerca log con una query per la voce selezionata.

![Visualizzazione predefinita di Gestione aggiornamenti](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Visualizzare gli aggiornamenti mancanti

Selezionare **Aggiornamenti mancanti** per visualizzare l'elenco di aggiornamenti mancanti nei computer. Ogni aggiornamento viene inserito nell'elenco e può essere selezionato. Sono disponibili informazioni relative al numero di computer che richiedono l'aggiornamento, i dettagli del sistema operativo e un collegamento per accedere ad altre informazioni. Il riquadro Ricerca log visualizza anche altri dettagli sugli aggiornamenti.

![Aggiornamenti mancanti](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="work-with-update-classifications"></a>Usare classificazioni degli aggiornamenti

Le tabelle che seguono elencano le classificazioni degli aggiornamenti supportati in Gestione aggiornamenti, con una definizione per ogni classificazione.

### <a name="windows"></a>Windows

|Classificazione  |Descrizione  |
|---------|---------|
|Aggiornamenti critici     | Aggiornamenti per problemi specifici che risolvono bug critici non correlati alla sicurezza.        |
|Aggiornamenti per la sicurezza     | Aggiornamenti per problemi specifici del prodotto correlati alla sicurezza.        |
|Aggiornamenti cumulativi     | Set di aggiornamenti rapidi, contenuti nello stesso pacchetto per facilitarne la distribuzione.        |
|Feature Pack     | Nuove funzionalità del prodotto distribuite di fuori di una versione del prodotto.        |
|Service Pack     | Set di aggiornamenti rapidi applicati a un'applicazione.        |
|Aggiornamenti della definizione     | Aggiornamenti di file di definizione di virus o di altro tipo.        |
|Strumenti     | Utilità o funzionalità che consentono di completare una o più attività.        |
|Aggiornamenti     | Aggiornamenti alle applicazioni o ai file attualmente installati.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|Classificazione  |Descrizione  |
|---------|---------|
|Aggiornamenti critici e della sicurezza     | Aggiornamenti per un problema specifico o specifico del prodotto, correlato alla sicurezza.         |
|Altri aggiornamenti     | Tutti gli altri aggiornamenti non critici per loro natura o che non sono aggiornamenti della sicurezza.        |

Per Linux, Gestione aggiornamenti è in grado di distinguere tra gli aggiornamenti critici e quelli della sicurezza nel cloud, visualizzando i dati di valutazione. Questa granularità è possibile grazie all'arricchimento dei dati nel cloud. Per l'applicazione di patch, Gestione aggiornamenti si affida ai dati di classificazione disponibili nel computer. A differenza di altre distribuzioni, CentOS non mette a disposizione queste informazioni nelle versioni RTM del prodotto. Se i computer CentOS sono configurati in modo da restituire dati sulla sicurezza per il comando seguente, Gestione aggiornamenti è in grado di applicare patch in base alle classificazioni:

```bash
sudo yum -q --security check-update
```

Attualmente non è supportato alcun metodo per abilitare la disponibilità dei dati di classificazione nativi in CentOS. In questa fase viene offerto il miglior supporto possibile ai clienti che hanno abilitato autonomamente questa funzionalità.

Per classificare gli aggiornamenti in Red Hat Enterprise versione 6, è necessario installare il plug-in yum-security. In Red Hat Enterprise Linux 7 il plug-in fa già parte di yum e non è necessario eseguire alcuna installazione supplementare. Per altre informazioni, vedere questo [file di caratteristiche del caso](https://access.redhat.com/solutions/10021) su Red Hat.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni generali, vedere [Gestire gli aggiornamenti e le patch per le macchine virtuali di Azure](automation-tutorial-update-management.md).
