---
title: Visualizzare le valutazioni degli aggiornamenti di Azure Automation Update ManagementView Azure Automation Update Management update assessments
description: In questo articolo viene descritto come visualizzare le valutazioni degli aggiornamenti per le distribuzioni degli aggiornamenti.
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 75762afc9ae69da81e89ce320f454d9764f82914
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617382"
---
# <a name="view-azure-automation-update-management-update-assessments"></a>Visualizzare le valutazioni degli aggiornamenti di Azure Automation Update ManagementView Azure Automation Update Management update assessments

Nell'account di Automazione di Azure selezionare **Gestione aggiornamenti** per visualizzare lo stato dei computer.

Questa visualizzazione contiene informazioni sui computer, sugli aggiornamenti mancanti, sulle distribuzioni degli aggiornamenti e sulle distribuzioni degli aggiornamenti pianificate. Nella colonna **COMPLIANCE** è possibile visualizzare l'ultima volta che la macchina è stata valutata. Nella colonna **UPDATE AGENT READINESS** è possibile visualizzare l'integrità dell'agente di aggiornamento. Se si verifica un problema, selezionare il collegamento per accedere alla documentazione relativa alla risoluzione dei problemi che consente di risolvere il problema.

Per eseguire una ricerca nei log che restituisca informazioni sul computer, sull'aggiornamento o sulla distribuzione, selezionare l'elemento corrispondente nell'elenco. Viene aperto il riquadro Ricerca log con una query per l'elemento selezionato.

![Visualizzazione predefinita di Gestione aggiornamenti](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>Visualizzare gli aggiornamenti mancanti

Selezionare **Aggiornamenti mancanti** per visualizzare l'elenco di aggiornamenti mancanti nei computer. Ogni aggiornamento viene inserito nell'elenco e può essere selezionato. Vengono visualizzate informazioni sul numero di computer che richiedono l'aggiornamento, i dettagli del sistema operativo e un collegamento per ulteriori informazioni. Il riquadro Ricerca log mostra anche ulteriori dettagli sugli aggiornamenti.

![Aggiornamenti mancanti](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>Classificazioni degli aggiornamenti

Nelle tabelle seguenti sono elencate le classificazioni degli aggiornamenti supportate in Gestione aggiornamenti, con una definizione per ogni classificazione.

### <a name="windows"></a>Windows

|Classificazione  |Descrizione  |
|---------|---------|
|Aggiornamenti critici     | Aggiornamenti per problemi specifici che riguardano bug critici e non correlati alla sicurezza.        |
|Aggiornamenti per la sicurezza     | Aggiornamenti per problemi specifici del prodotto e relativi alla sicurezza.        |
|Aggiornamenti cumulativi     | Set di aggiornamenti rapidi che vengono inclusi nel pacchetto per una facile distribuzione.        |
|Feature Pack     | Nuove funzionalità del prodotto distribuite di fuori di una versione del prodotto.        |
|Service Pack     | Set di aggiornamenti rapidi applicati a un'applicazione.        |
|Aggiornamenti della definizione     | Aggiornamenti a virus o altri file di definizione.        |
|Strumenti     | Utilità o funzionalità che consentono di completare una o più attività.        |
|Aggiornamenti     | Aggiornamenti alle applicazioni o ai file attualmente installati.        |

### <a name="linux"></a><a name="linux-2"></a>Linux

|Classificazione  |Descrizione  |
|---------|---------|
|Aggiornamenti critici e della sicurezza     | Aggiornamenti per un problema specifico o specifico del prodotto, correlato alla sicurezza.         |
|Altri aggiornamenti     | Tutti gli altri aggiornamenti che non sono di natura critica o che non sono aggiornamenti della sicurezza.        |

Per Linux, la gestione degli aggiornamenti è in grado di distinguere tra aggiornamenti critici e aggiornamenti della sicurezza nel cloud durante la visualizzazione dei dati di valutazione. Questa granularità è possibile grazie all'arricchimento dei dati nel cloud. Per l'applicazione di patch, Gestione aggiornamenti si basa sui dati di classificazione disponibili nel computer. A differenza di altre distribuzioni, CentOS non dispone di queste informazioni disponibili nelle versioni RTM del prodotto. Se si dispone di macchine CentOS configurate per restituire i dati di sicurezza per il comando seguente, Gestione aggiornamenti può applicare patch in base alle classificazioni:

```bash
sudo yum -q --security check-update
```

Attualmente non è supportato alcun metodo per abilitare la disponibilità dei dati di classificazione nativi in CentOS.There's currently no supported method to enable native classification-data availability on CentOS. Al momento, viene fornito solo il supporto per il massimo sforzo ai clienti che hanno abilitato questa funzionalità autonomamente.

Per classificare gli aggiornamenti in Red Hat Enterprise versione 6, è necessario installare il plug-in yum-security. Su Red Hat Enterprise Linux 7, il plugin è già una parte di yum stesso, non c'è bisogno di installare nulla. Per ulteriori informazioni, vedere il seguente [articolo della Knowledge](https://access.redhat.com/solutions/10021)Base Red Hat .

## <a name="next-steps"></a>Passaggi successivi

Dopo aver visualizzato le valutazioni degli aggiornamenti, è possibile pianificare una distribuzione degli aggiornamenti seguendo la procedura descritta in Gestire gli aggiornamenti e le patch per le macchine virtuali di Azure.After you view any update assessments, you can schedule an update deployment by following the steps at [Manage updates and patches for your Azure VMs](automation-tutorial-update-management.md).
