---
title: Integrazione del controllo del codice sorgente in Automazione di Azure
description: Questo articolo descrive l'integrazione del controllo del codice sorgente con GitHub in Automazione di Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 49a28901e2ea471f97270c0407e2f6c0a4a533fd
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58169154"
---
# <a name="source-control-integration-in-azure-automation"></a>Integrazione del controllo del codice sorgente in Automazione di Azure

Il controllo del codice sorgente consente di mantenere i runbook inclusi nell'account di automazione aggiornati con gli script del repository del controllo del codice sorgente Azure DevOps o GitHub. Il controllo del codice sorgente fornisce un ambiente in cui è possibile collaborare con il team, tenere traccia delle modifiche ed eseguire il rollback a versioni precedenti dei runbook. Il controllo del codice sorgente, ad esempio, consente di sincronizzare diversi rami nel controllo del codice sorgente per gli account di automazione di sviluppo, test o produzione. Questo rende facile alzare di livello il codice che è stato testato nell'ambiente di sviluppo a produzione di account di automazione.

Automazione di Azure supporta 3 tipi di controllo del codice sorgente:

* GitHub
* Azure DevOps (Git)
* Azure DevOps (TFVC)

## <a name="pre-requisites"></a>Prerequisiti

* Un repository di controllo del codice sorgente (GitHub o Azure DevOps)
* Le [autorizzazioni](#personal-access-token-permissions) corrette per il repository del controllo del codice sorgente
* Un [account RunAs e una connessione](manage-runas-account.md)

> [!NOTE]
> I processi di sincronizzazione del controllo del codice sorgente vengono eseguiti nell'account di automazione dell'utente e vengono fatturati alla stessa tariffa degli altri processi di automazione.

## <a name="configure-source-control"></a>Configurare il controllo del codice sorgente

Nell'account di automazione selezionare **Controllo del codice sorgente (anteprima)** e fare clic su **+ Aggiungi**

![Selezionare il controllo del codice sorgente](./media/source-control-integration/select-source-control.png)

Scegliere **Tipo di controllo del codice sorgente** e fare clic su **Autentica**.

Esaminare la pagina di richiesta autorizzazioni dell'app e fare clic su **Accetta**.

Inserire le informazioni nella pagina **Riepilogo del Controllo del codice sorgente** e fare clic su **Salva**. La tabella seguente fornisce una descrizione dei campi disponibili.

|Proprietà  |DESCRIZIONE  |
|---------|---------|
|Nome del controllo del codice sorgente     | Nome descrittivo per il controllo del codice sorgente        |
|Tipo di controllo del codice sorgente     | Specifica del tipo di controllo del codice sorgente. Le opzioni disponibili sono:</br> GitHub</br>Azure DevOps (Git)</br> Azure DevOps (TFVC)        |
|Repository     | Nome del repository o del progetto. Questo valore viene sottoposto a pull dal repository del controllo del codice sorgente. Esempio: $/ContosoFinanceTFVCExample         |
|Ramo     | Ramo da cui eseguire il pull dei file di origine. La selezione della destinazione del ramo non è disponibile per il controllo del codice sorgente di tipo Controllo della versione di Team Foundation.          |
|Percorso della cartella     | Cartella che contiene i runbook da sincronizzare. Esempio: /Runbooks         |
|Sincronizzazione automatica     | Attiva o disattiva la sincronizzazione automatica quando viene eseguito un commit nel repository del controllo del codice sorgente         |
|Pubblica runbook     | Se questa opzione è **attivata**, i runbook vengono pubblicati automaticamente dopo la sincronizzazione dal controllo del codice sorgente.         |
|DESCRIZIONE     | Campo di testo in cui fornire altri dettagli        |

![Riepilogo del Controllo del codice sorgente](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Assicurarsi di essere connessi con l'account corretto durante la configurazione di controllo del codice sorgente. Se c'è qualche dubbio, aprire una nuova scheda nel browser e disconnettersi da visualstudio.com o github.com e provare di nuovo a connettersi al controllo del codice sorgente.

## <a name="syncing"></a>Sincronizzazione

Configurando la sincronizzazione automatica durante la configurazione dell'integrazione del controllo del codice sorgente, la sincronizzazione iniziale viene avviata automaticamente. Se la sincronizzazione automatica non è stata impostata, selezionare il controllo del codice sorgente nella tabella nella pagina **Controllo del codice sorgente (anteprima)**. Fare clic su **Avvia sincronizzazione** per avviare il processo di sincronizzazione.

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

## <a name="personal-access-token-permissions"></a>Autorizzazioni dei token di accesso personale

Controllo del codice sorgente richiede alcune autorizzazioni minime per i token di accesso personale. Le tabelle seguenti contengono le autorizzazioni minime necessarie per GitHub e Azure DevOps.

### <a name="github"></a>GitHub

|Scope  |DESCRIZIONE  |
|---------|---------|
|**repo**     |         |
|repo:status     | Accedere allo stato del commit         |
|repo_deployment      | Accedere allo stato della distribuzione         |
|public_repo     | Accedere ai repository pubblici         |
|**admin:repo_hook**     |         |
|write:repo_hook     | Scrivere gli hook del repository         |
|read:repo_hook|Leggere gli hook del repository|

### <a name="azure-devops"></a>Azure DevOps

|Scope  |
|---------|
|Codice (lettura)     |
|Progetto e team (lettura)|
|Identità (lettura)      |
|Profilo utente (lettura)     |
|Elementi di lavoro (lettura)    |
|Connessioni al servizio (lettura, query e gestione)<sup>1</sup>    |

<sup>1</sup>l'autorizzazione delle connessioni al servizio è necessaria solo se è abilitata la sincronizzazione automatica.

## <a name="disconnecting-source-control"></a>Disconnessione del controllo del codice sorgente

Per disconnettersi dal repository di un controllo del codice sorgente, aprire **Controllo del codice sorgente (anteprima)** in **Impostazioni account** nell'account di automazione.

Selezionare il controllo del codice sorgente da rimuovere. Nella pagina **Riepilogo del Controllo del codice sorgente** fare clic su **Elimina**.

## <a name="encoding"></a>Codifica

Se più persone che sta modificando i runbook nel repository di controllo del codice sorgente con diversi editor vi è possibilità verifichino i problemi di codifica. Ciò può inserire caratteri non validi nel runbook. Per altre informazioni, vedere [cause comuni di problemi di codifica](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui tipi di runbook, i relativi vantaggi e le limitazioni, vedere [Tipi di runbook di Automazione di Azure](automation-runbook-types.md)

