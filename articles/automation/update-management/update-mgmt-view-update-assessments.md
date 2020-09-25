---
title: Visualizzare le valutazioni degli aggiornamenti di Automazione di Azure
description: Questo articolo descrive come visualizzare le valutazioni degli aggiornamenti per le distribuzioni di Gestione aggiornamenti.
services: automation
ms.subservice: update-management
ms.date: 09/17/2020
ms.topic: conceptual
ms.openlocfilehash: 2e32fc7c1872bf18b7f1c995f281a9b09ec45dc8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91264472"
---
# <a name="view-update-assessments-in-update-management"></a>Visualizza valutazioni aggiornamenti in Gestione aggiornamenti

In Gestione aggiornamenti, è possibile visualizzare le informazioni relative ai computer, gli aggiornamenti mancanti, le distribuzioni degli aggiornamenti e le distribuzioni degli aggiornamenti pianificati. È possibile visualizzare le informazioni di valutazione nell'ambito della macchina virtuale di Azure selezionata, dal server abilitato per l'arco selezionato o dall'account di automazione in tutti i computer e i server configurati.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com)

## <a name="view-update-assessment"></a>Visualizzare la valutazione degli aggiornamenti

Per visualizzare la valutazione degli aggiornamenti da una macchina virtuale di Azure, passare a **macchine virtuali** e selezionare la macchina virtuale nell'elenco. Nel menu a sinistra selezionare **guest + host Updates**e quindi selezionare **Vai a gestione aggiornamenti** nella pagina **guest + host Updates** .

In Gestione aggiornamenti, è possibile visualizzare le informazioni relative al computer, gli aggiornamenti mancanti, le distribuzioni degli aggiornamenti e le distribuzioni degli aggiornamenti pianificati.

[![Vista Gestione aggiornamenti assessment per la macchina virtuale di Azure](./media/update-mgmt-view-update-assessments/update-assessment-azure-vm.png)](./media/update-mgmt-view-update-assessments/update-assessment-azure-vm-expanded.png#lightbox)

Per visualizzare la valutazione degli aggiornamenti da un server abilitato per Arc, passare a **Server-Azure Arc** e selezionare il server dall'elenco. Nel menu a sinistra selezionare **Guest e host Updates**. Nella pagina **aggiornamenti guest + host** selezionare **Vai a gestione aggiornamenti**.

In Gestione aggiornamenti, è possibile visualizzare le informazioni sul computer abilitato per Arc, gli aggiornamenti mancanti, le distribuzioni di aggiornamenti e le distribuzioni di aggiornamenti pianificati.

[![Visualizzazione della valutazione gestione aggiornamenti per i server abilitati per Arc](./media/update-mgmt-view-update-assessments/update-assessment-arc-server.png)](./media/update-mgmt-view-update-assessments/update-assessment-arc-server-expanded.png#lightbox)

Per visualizzare la valutazione degli aggiornamenti in tutti i computer, inclusi i server abilitati per Arc dall'account di automazione, passare a **account di automazione** e selezionare l'account di automazione con gestione aggiornamenti abilitato dall'elenco. Nell'account di automazione selezionare **Gestione aggiornamenti** dal menu a sinistra.

Gli aggiornamenti per l'ambiente sono elencati nella pagina **Gestione aggiornamenti** . Se vengono identificati come mancanti, un elenco di tali aggiornamenti viene visualizzato nella scheda **aggiornamenti mancanti** .

[![Visualizzazione predefinita gestione aggiornamenti](./media/update-mgmt-overview/update-management-view.png)](./media/update-mgmt-overview/update-management-view-expanded.png#lightbox)

Nella colonna **conformità** è possibile visualizzare l'ultima volta in cui il computer è stato valutato. Nella colonna **Aggiorna conformità agente** è possibile visualizzare lo stato dell'agente di aggiornamento. Se è presente un problema, selezionare il collegamento alla documentazione sulla risoluzione dei problemi, che contiene informazioni utili per risolverlo.

In **Collegamento alle informazioni** selezionare il collegamento per un aggiornamento per aprire l'articolo del supporto tecnico con informazioni importanti sull'aggiornamento.

[![Visualizza stato aggiornamento](./media/update-mgmt-view-update-assessments/missing-updates.png)](./media/update-mgmt-view-update-assessments/missing-updates-expanded.png#lightbox)

Fare clic in altro punto dell'area dell'aggiornamento per aprire il riquadro Ricerca log. La query per la ricerca log è predefinita per tale specifico aggiornamento. È possibile modificare questa query o creare una query personalizzata per visualizzare informazioni dettagliate.

[![Visualizzare i risultati delle query di log](./media/update-mgmt-view-update-assessments/logsearch-results.png)](./media/update-mgmt-view-update-assessments/logsearch-results-expanded.png#lightbox)

## <a name="view-missing-updates"></a>Visualizzare gli aggiornamenti mancanti

Selezionare **Aggiornamenti mancanti** per visualizzare l'elenco di aggiornamenti mancanti nei computer. Ogni aggiornamento viene inserito nell'elenco e può essere selezionato. Sono disponibili informazioni relative al numero di computer che richiedono l'aggiornamento, i dettagli del sistema operativo e un collegamento per accedere ad altre informazioni. Il riquadro Ricerca log visualizza anche altri dettagli sugli aggiornamenti.

![Aggiornamenti mancanti](./media/update-mgmt-view-update-assessments/automation-view-update-assessments-missing-updates.png)

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

### <a name="linux"></a>Linux

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

La fase successiva del processo consiste nel [distribuire gli aggiornamenti](update-mgmt-deploy-updates.md) a computer non conformi ed esaminare i risultati della distribuzione.
