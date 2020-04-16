---
title: Risolvere i problemi di connettività di Azure Synapse Studio (anteprima)Troubleshoot Azure Synapse Studio (preview) connectivity PowerShell
description: Risolvere i problemi di connettività di Azure Synapse Studio usando PowerShellTroubleshoot Azure Synapse Studio connectivity using PowerShell
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: bbc985407a6cb56f4f1b539f514ab092b5f7d0de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431475"
---
# <a name="diagnose-azure-synapse-studio-preview-connectivity-issues-with-powershell-script"></a>Diagnosticare i problemi di connettività di Azure Synapse Studio (anteprima) con lo script di PowerShellDiagnose Azure Synapse Studio (preview) connectivity issues with PowerShell script

Azure Synapse Studio (anteprima) dipende da un set di endpoint API Web per funzionare correttamente. Questa guida ti aiuterà a identificare le cause dei problemi di connettività quando sei:
- configurazione della rete locale (ad esempio rete dietro un firewall aziendale) per l'accesso ad Azure Synapse Studio.
- problemi di connettività con Azure Synapse Studio.

## <a name="prerequisite"></a>Prerequisito

* PowerShell 5.0 o versione successiva in Windows oppure
* PowerShell Core 6.0 or higher version on Windows or Linux.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

Fare clic con il pulsante destro del mouse sul seguente collegamento e scegliere "Salva destinazione con nome":

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

In alternativa, è possibile aprire direttamente il collegamento e salvare il file di script aperto. Non salvare l'indirizzo del link qui sopra, in quanto potrebbe cambiare in futuro.

In Esplora file fare clic con il pulsante destro del mouse sul file di script scaricato e scegliere "Esegui con PowerShell".

![Eseguire il file di script scaricato con PowerShellRun downloaded script file with PowerShell](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

Quando richiesto, immettere il nome dell'area di lavoro Synapse di Azure che si verifica un problema o che si desidera verificare la connettività e premere INVIO.

![Immettere il nome dell'area di lavoro](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

La sessione di diagnostica verrà avviata. Attendere il completamento.

![Attendere il completamento della diagnosi](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

Alla fine, verrà visualizzato un riepilogo di diagnosi. Se il PC non riesce a connettersi a uno o più endpoint, mostrerà alcuni suggerimenti nella sezione "Riepilogo".

![Esaminare il riepilogo diagnosticoReview diagnostic summary](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Inoltre, un file di log di diagnostica per questa sessione verrà generato nella stessa cartella dello script di risoluzione dei problemi. La sua posizione è mostrata nella`D:\TestAzureSynapse_2020....log`sezione "Suggerimenti generali" ( ). Se necessario, è possibile inviare questo file al supporto tecnico.

Se sei un amministratore di rete e stai ottimizzando la configurazione del firewall per Azure Synapse Studio, i dettagli tecnici mostrati sopra la sezione "Riepilogo" possono essere utili.

* Tutti gli elementi di test (richieste) contrassegnati con "Superato" indicano che hanno superato i test di connettività, indipendentemente dal codice di stato HTTP.
 Per le richieste non riuscite, il `NamedResolutionFailure` motivo viene visualizzato in giallo, ad esempio o `ConnectFailure`. Questi motivi possono aiutare a capire se ci sono configurazioni errate con l'ambiente di rete.


## <a name="next-steps"></a>Passaggi successivi
Se i passaggi precedenti non consentono di risolvere il problema Creare un ticket di [supporto.](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md)
