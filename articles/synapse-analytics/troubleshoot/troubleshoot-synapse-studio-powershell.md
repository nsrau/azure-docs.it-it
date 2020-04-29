---
title: Risolvere i problemi di PowerShell per la connettività di Azure sinapsi Studio (anteprima)
description: Risolvere i problemi di connettività di Azure sinapsi studio con PowerShell
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: bbc985407a6cb56f4f1b539f514ab092b5f7d0de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431475"
---
# <a name="diagnose-azure-synapse-studio-preview-connectivity-issues-with-powershell-script"></a>Diagnosticare i problemi di connettività di Azure sinapsi Studio (anteprima) con lo script di PowerShell

Azure sinapsi Studio (anteprima) dipende da un set di endpoint dell'API Web per funzionare correttamente. Questa guida consente di identificare le cause dei problemi di connettività quando si è:
- configurazione della rete locale, ad esempio rete dietro un firewall aziendale, per l'accesso ad Azure sinapsi Studio.
- si verificano problemi di connettività con Azure sinapsi Studio.

## <a name="prerequisite"></a>Prerequisito

* PowerShell 5,0 o versione successiva in Windows o
* PowerShell Core 6,0 o versione successiva in Windows o Linux.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

Fare clic con il pulsante destro del mouse sul collegamento seguente e scegliere "Salva destinazione con nome":

- [Test-AzureSynapse. ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

In alternativa, è possibile aprire direttamente il collegamento e salvare il file di script aperto. Non salvare l'indirizzo del collegamento precedente, in quanto potrebbe cambiare in futuro.

In Esplora file fare clic con il pulsante destro del mouse sul file di script scaricato e scegliere "Esegui con PowerShell".

![Eseguire il file di script scaricato con PowerShell](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

Quando richiesto, immettere il nome dell'area di lavoro di Azure sinapsi che sta attualmente riscontrando un problema o che si desidera testare la connettività e premere INVIO.

![Immettere il nome dell'area di lavoro](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

La sessione di diagnostica verrà avviata. Attendere il completamento.

![Attendi il completamento della diagnosi](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

Alla fine, verrà visualizzato un riepilogo della diagnosi. Se il PC non è in grado di connettersi a uno o più endpoint, verrà visualizzato un suggerimento nella sezione "Riepilogo".

![Esaminare il riepilogo di diagnostica](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Inoltre, un file di log di diagnostica per questa sessione verrà generato nella stessa cartella dello script per la risoluzione dei problemi. Il percorso è indicato nella sezione "suggerimenti generali" (`D:\TestAzureSynapse_2020....log`). Se necessario, è possibile inviare questo file al supporto tecnico.

Se si è un amministratore di rete e si sta ottimizzando la configurazione del firewall per Azure sinapsi studio, i dettagli tecnici illustrati sopra la sezione "Riepilogo" possono essere utili.

* Tutti gli elementi di test (richieste) contrassegnati con "superato" indicano che hanno superato i test di connettività, indipendentemente dal codice di stato HTTP.
 Per le richieste non riuscite, il motivo viene visualizzato in giallo, `NamedResolutionFailure` ad `ConnectFailure`esempio o. Questi motivi possono essere utili per determinare se sono presenti configurazioni errate per l'ambiente di rete.


## <a name="next-steps"></a>Passaggi successivi
Se la procedura precedente non consente di risolvere il problema, [creare un ticket di supporto](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).
