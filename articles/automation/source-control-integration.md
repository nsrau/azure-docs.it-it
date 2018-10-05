---
title: Integrazione del controllo del codice sorgente in Automazione di Azure
description: Questo articolo descrive l'integrazione del controllo del codice sorgente con GitHub in Automazione di Azure.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c2d13a409d095bca64da781e5c5ca58553f9710c
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47046253"
---
# <a name="source-control-integration-in-azure-automation"></a>Integrazione del controllo del codice sorgente in Automazione di Azure

Il controllo del codice sorgente consente di mantenere i runbook inclusi nell'account di automazione aggiornati con gli script del repository del controllo del codice sorgente Azure DevOps o GitHub. Il controllo del codice sorgente fornisce un ambiente in cui è possibile collaborare con il team, tenere traccia delle modifiche ed eseguire il rollback a versioni precedenti dei runbook. Ad esempio, il controllo del codice sorgente consente di sincronizzare diversi rami nel controllo del codice sorgente con gli account di automazione di sviluppo, test o produzione e alzare di livello il codice testato nell'ambiente di sviluppo per passarlo all'ambiente di produzione.

Automazione di Azure supporta 3 tipi di controllo del codice sorgente:

* GitHub
* Visual Studio Team Services (Git)
* Visual Studio Team Services (Controllo della versione di Team Foundation)

## <a name="pre-requisites"></a>Prerequisiti

* Un repository del controllo del codice sorgente (GitHub o Visual Studio Team Services)
* Un [account RunAs e una connessione](manage-runas-account.md)

> [!NOTE]
> I processi di sincronizzazione del controllo del codice sorgente vengono eseguiti nell'account di automazione dell'utente e vengono fatturati alla stessa tariffa degli altri processi di automazione.

## <a name="configure-source-control"></a>Configurare il controllo del codice sorgente

Nell'account di automazione selezionare **Controllo del codice sorgente (anteprima)** e fare clic su **+ Aggiungi**

![Selezionare il controllo del codice sorgente](./media/source-control-integration/select-source-control.png)

Scegliere **Tipo di controllo del codice sorgente** e fare clic su **Autentica**.

Esaminare la pagina di richiesta autorizzazioni dell'app e fare clic su **Accetta**.

Inserire le informazioni nella pagina **Riepilogo del Controllo del codice sorgente** e fare clic su **Salva**. La tabella seguente fornisce una descrizione dei campi disponibili.

|Proprietà  |Descrizione  |
|---------|---------|
|Nome del controllo del codice sorgente     | Nome descrittivo per il controllo del codice sorgente        |
|Tipo di controllo del codice sorgente     | Specifica del tipo di controllo del codice sorgente. Le opzioni disponibili sono:</br> Github</br>Visual Studio Team Services (Git)</br>Visual Studio Team Services (Controllo della versione di Team Foundation)        |
|Repository     | Nome del repository o del progetto. Viene recuperato dal repository del controllo del codice sorgente. Esempio: $/ContosoFinanceTFVCExample         |
|Ramo     | Ramo da cui eseguire il pull dei file di origine. La selezione della destinazione del ramo non è disponibile per il controllo del codice sorgente di tipo Controllo della versione di Team Foundation.          |
|Percorso della cartella     | Cartella che contiene i runbook da sincronizzare. Esempio: /Runbooks         |
|Sincronizzazione automatica     | Attiva o disattiva la sincronizzazione automatica quando viene eseguito un commit nel repository del controllo del codice sorgente         |
|Pubblica runbook     | Se questa opzione è **attivata**, i runbook vengono pubblicati automaticamente dopo la sincronizzazione dal controllo del codice sorgente.         |
|Descrizione     | Campo di testo in cui fornire altri dettagli        |

![Riepilogo del Controllo del codice sorgente](./media/source-control-integration/source-control-summary.png)

## <a name="syncing"></a>Sincronizzazione

Se è stata impostata la sincronizzazione automatica durante la configurazione dell'integrazione del controllo del codice sorgente, la sincronizzazione iniziale dovrebbe essere avviata automaticamente. Se la sincronizzazione automatica non è stata impostata, selezionare il controllo del codice sorgente nella tabella nella pagina **Controllo del codice sorgente (anteprima)**. Fare clic su **Avvia sincronizzazione** per avviare il processo di sincronizzazione.  

È possibile visualizzare lo stato del processo di sincronizzazione corrente o di quelli precedenti facendo clic sulla scheda **Processi di sincronizzazione**. Nell'elenco a discesa **Controllo del codice sorgente** selezionare un controllo del codice sorgente.

![Stato sincronizzazione](./media/source-control-integration/sync-status.png)

Facendo clic su un processo è possibile visualizzarne l'output. Di seguito è riportato un esempio di output di un processo di sincronizzazione di un controllo del codice sorgente.

```output
========================================================================================================

Azure Automation Source Control Public Preview.
Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

Setting AzureRmEnvironment.

Getting AzureRunAsConnection.

Logging in to Azure...

Source control information for syncing:

[Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

Connecting to VSTS...


Source Control Sync Summary:


2 files synced:
 - ExampleRunbook1.ps1
 - ExampleRunbook2.ps1



========================================================================================================
```

## <a name="disconnecting-source-control"></a>Disconnessione del controllo del codice sorgente

Per disconnettersi dal repository di un controllo del codice sorgente, aprire **Controllo del codice sorgente (anteprima)** in **Impostazioni account** nell'account di automazione.

Selezionare il controllo del codice sorgente da rimuovere. Nella pagina **Riepilogo del Controllo del codice sorgente** fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui tipi di runbook, i relativi vantaggi e le limitazioni, vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md)
