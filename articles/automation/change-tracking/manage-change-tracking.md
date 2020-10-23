---
title: Gestire Rilevamento modifiche e inventario in Automazione di Azure
description: Questo articolo illustra come usare Rilevamento modifiche e inventario per tenere traccia delle modifiche apportate al software e ai servizi Microsoft nell'ambiente in uso.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: a599bb6f07683540f5b12c6a69d6565161f89a4f
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210335"
---
# <a name="manage-change-tracking-and-inventory"></a>Gestire il rilevamento modifiche e l'inventario

Quando si aggiunge un nuovo file o una nuova chiave del registro di sistema da rilevare, automazione di Azure lo Abilita per [rilevamento modifiche e l'inventario](overview.md). Questo articolo descrive come configurare il rilevamento, rivedere i risultati del rilevamento e gestire gli avvisi quando vengono rilevate modifiche.

Prima di usare le procedure descritte in questo articolo, assicurarsi di aver abilitato Rilevamento modifiche e l'inventario nelle VM usando una di queste tecniche:

* [Abilitare Rilevamento modifiche e inventario da un account di Automazione](enable-from-automation-account.md)
* [Abilitare Rilevamento modifiche e l'inventario esplorando il portale di Azure](enable-from-portal.md)
* [Abilitare Rilevamento modifiche e inventario da un runbook](enable-from-runbook.md)
* [Abilitare Rilevamento modifiche e inventario da una macchina virtuale di Azure](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Limitare l'ambito della distribuzione

Rilevamento modifiche e inventario usa una configurazione dell'ambito all'interno dell'area di lavoro per individuare i computer che ricevono le modifiche. Per altre informazioni, vedere [Limit rilevamento modifiche and Inventory Deployment scope](manage-scope-configurations.md).

## <a name="track-files"></a>Rilevare file

È possibile utilizzare Rilevamento modifiche e l'inventario per tenere traccia delle modifiche apportate a file e cartelle/directory. Questa sezione descrive come configurare il rilevamento dei file in Windows e in Linux.

### <a name="configure-file-tracking-on-windows"></a>Configurare il rilevamento dei file in Windows

Seguire questa procedura per configurare il rilevamento dei file in computer Windows:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Automazione**. Quando si inizia a digitare, l'elenco Filtra i suggerimenti in base all'input. Selezionare **Account di automazione**.

3. Nell'elenco degli account di automazione selezionare l'account scelto quando è stato abilitato il Rilevamento modifiche e l'inventario.

4. Nell'account di Automazione selezionare **Rilevamento modifiche** in **Gestione della configurazione**.

5. Selezionare **Modifica impostazioni** (simbolo dell'ingranaggio).

6. Nella pagina Configurazione dell'area di lavoro selezionare **File Windows**, quindi fare clic su **+ Aggiungi** per aggiungere un nuovo file da rilevare.

7. Nel riquadro Aggiungi file Windows per Rilevamento modifiche immettere le informazioni per il file o la cartella da rilevare e fare clic su **Salva**. La tabella seguente definisce le proprietà che è possibile usare per le informazioni.

    |Proprietà  |Descrizione  |
    |---------|---------|
    |Attivato     | True se viene applicata l'impostazione e False in caso contrario.        |
    |Nome elemento     | Nome descrittivo del file da rilevare.        |
    |Gruppo     | Nome del gruppo per il raggruppamento logico dei file.        |
    |Immettere il percorso     | Percorso in cui cercare il file, ad esempio **c:\temp\\\*.txt**. È anche possibile usare le variabili di ambiente, ad esempio `%winDir%\System32\\\*.*`.       |
    |Tipo di percorso     | Tipo di percorso. I valori possibili sono file e Folder.        |    
    |Ricorsione     | True se viene usata la ricorsione per la ricerca dell'elemento da rilevare, False in caso contrario.        |    
    |Carica contenuto file | True per caricare il contenuto del file nelle modifiche rilevate e False in caso contrario.|

8. Assicurarsi di specificare True per **Carica contenuto file**. Questa impostazione abilita il rilevamento del contenuto del file solo per il percorso indicato.

### <a name="configure-file-tracking-on-linux"></a>Configurare il rilevamento dei file in Linux

Seguire questa procedura per configurare il rilevamento dei file in computer Linux:

1. Selezionare **Modifica impostazioni** (simbolo dell'ingranaggio).

2. Nella pagina di configurazione dell'area di lavoro selezionare **file Linux**e quindi **+ Aggiungi** per aggiungere un nuovo file da rilevare.

3. Nella pagina **Aggiungi file Linux per rilevamento modifiche** immettere le informazioni per il file o la directory da rilevare e quindi selezionare **Salva**. La tabella seguente definisce le proprietà che è possibile usare per le informazioni.

    |Proprietà  |Descrizione  |
    |---------|---------|
    |Attivato     | True se viene applicata l'impostazione e False in caso contrario.        |
    |Nome elemento     | Nome descrittivo del file da rilevare.        |
    |Gruppo     | Nome del gruppo per il raggruppamento logico dei file.        |
    |Immettere il percorso     | Percorso in cui cercare il file, ad esempio **/etc/*.conf**.       |
    |Tipo di percorso     | Tipo di percorso. I valori possibili sono File e Directory.        |
    |Ricorsione     | True se viene usata la ricorsione per la ricerca dell'elemento da rilevare, False in caso contrario.        |
    |Usa Sudo     | True per utilizzare sudo quando si cerca l'elemento e False in caso contrario.         |
    |Collegamenti     | Impostazione che determina la modalità di gestione dei collegamenti simbolici durante l'attraversamento delle directory. I valori possibili sono:<br> Ignora: ignora i collegamenti simbolici e non include i file e le directory a cui viene fatto riferimento.<br>Segui: segue i collegamenti simbolici durante la ricorsione e include anche i file e le directory a cui viene fatto riferimento.<br>Gestisci: segue i collegamenti simbolici e consente la modifica del contenuto restituito.<br>**Nota:** L'opzione Gestisci non è consigliata perché non supporta il recupero del contenuto del file.    |
    |Carica contenuto file | True per caricare il contenuto del file nelle modifiche rilevate e False in caso contrario. |

4. Assicurarsi di specificare **true** per **caricare il contenuto del file**. Questa impostazione abilita il rilevamento del contenuto del file solo per il percorso indicato.

   ![Aggiungere file Linux](./media/manage-change-tracking/add-linux-file.png)

## <a name="track-file-contents"></a>Rilevare il contenuto del file

Il rilevamento del contenuto del file consente di visualizzare il contenuto di un file prima e dopo una modifica rilevata. La funzionalità Salva il contenuto del file in un [account di archiviazione](../../storage/common/storage-account-overview.md) dopo ogni modifica. Ecco alcune regole da seguire per rilevare il contenuto dei file:

* Un account di archiviazione standard con il modello di distribuzione di Gestione risorse è necessario per la memorizzazione del contenuto del file.
* Non usare account di archiviazione con modello di distribuzione classica e Premium. Vedere [Informazioni sugli account di archiviazione di Azure](../../storage/common/storage-account-create.md).
* È possibile connettere l'account di archiviazione a un solo account di automazione.
* Rilevamento modifiche e l'inventario devono essere abilitati nell'account di automazione.

### <a name="enable-tracking-for-file-content-changes"></a>Abilitare il rilevamento delle modifiche al contenuto del file

Usare la procedura seguente per abilitare il rilevamento delle modifiche apportate al contenuto del file:

1. Selezionare **Modifica impostazioni** (simbolo dell'ingranaggio).

2. Selezionare il **contenuto del file** e quindi selezionare **collegamento**. Questa selezione consente di aprire la pagina **Aggiungi percorso contenuto per rilevamento modifiche** .

   ![Aggiungi percorso contenuto](./media/manage-change-tracking/enable.png)

3. Selezionare la sottoscrizione e l'account di archiviazione da usare per archiviare il contenuto del file.

5. Se si desidera abilitare il rilevamento di contenuto dei file per tutti i file verificati esistenti, selezionare **On** per **Caricare il contenuto del file per tutte le impostazioni**. È possibile modificare questa impostazione per ogni percorso di file in un secondo momento.

   ![Impostare l'account di archiviazione](./media/manage-change-tracking/storage-account.png)

6. Rilevamento modifiche e inventario mostra gli URI dell'account di archiviazione e della firma di accesso condiviso (SAS) quando abilita il rilevamento delle modifiche al contenuto del file. Le firme scadono dopo 365 giorni ed è possibile ricrearle selezionando **Rigenera**.

   ![Elencare le chiavi dell'account](./media/manage-change-tracking/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Visualizzare il contenuto di un file rilevato

Dopo che Rilevamento modifiche e inventario ha individuato una modifica a un file rilevato, è possibile visualizzare il contenuto del file nel riquadro Dettagli modifiche.  

![Elencare le modifiche](./media/manage-change-tracking/change-list.png)

1. Nella pagina **rilevamento** modifiche dell'account di automazione scegliere un file nell'elenco di modifiche e quindi selezionare **Visualizza modifiche al contenuto del file** per visualizzare il contenuto del file. Il riquadro Modifica dettagli Mostra le informazioni standard prima e dopo file per ogni proprietà.

   ![Dettagli modifiche](./media/manage-change-tracking/change-details.png)

2. Il contenuto del file viene mostrato in una visualizzazione affiancata. È possibile selezionare **In linea** per ottenere una visualizzazione in linea delle modifiche.

## <a name="track-registry-keys"></a>Rilevare le chiavi del Registro di sistema

Seguire questa procedura per configurare il rilevamento delle chiavi del Registro di sistema in computer Windows:

1. Nella pagina **rilevamento modifiche** dell'account di automazione selezionare **Modifica impostazioni** (simbolo dell'ingranaggio).

2. Nella pagina Configurazione dell'area di lavoro selezionare **Registro di sistema di Windows**.

3. Selezionare **+ Aggiungi** per aggiungere una nuova chiave del registro di sistema da rilevare.

4. Nella pagina **Aggiungi registro di sistema di Windows per rilevamento modifiche** immettere le informazioni per la chiave da rilevare e quindi selezionare **Salva**. La tabella seguente definisce le proprietà che è possibile usare per le informazioni.

    |Proprietà  |Descrizione  |
    |---------|---------|
    |Attivato     | True se viene applicata l'impostazione e False in caso contrario.        |
    |Nome elemento     | Nome descrittivo della chiave del Registro di sistema da rilevare.        |
    |Gruppo     | Nome del gruppo per il raggruppamento logico delle chiavi del Registro di sistema.        |
    |Chiave del Registro di sistema di Windows   | Nome della chiave con percorso, ad esempio `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup` .      |

## <a name="search-logs-for-change-records"></a>Cercare i record delle modifiche nei log

È possibile eseguire varie ricerche di record delle modifiche nei log di Monitoraggio di Azure. Con la pagina Rilevamento modifiche aperta, fare clic su **Log Analytics** per aprire la pagina Log. La tabella seguente contiene esempi di ricerche log per i record di modifica.

|Query  |Descrizione  |
|---------|---------|
|`ConfigurationData`<br> &#124; `where ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"`<br> &#124; `where SvcState == "Stopped"`<br> &#124; `summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | Mostra i record dell'inventario più recenti per i servizi Microsoft che sono stati impostati su Auto (Automatico) ma sono stati segnalati come Stopped (Interrotto). I risultati sono limitati al record più recente per il nome software e il computer specificati.    |
|`ConfigurationChange`<br> &#124; `where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br> &#124; `order by TimeGenerated desc`|Mostra i record di modifica per il software rimosso.|

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle configurazioni dell'ambito, vedere [limitare rilevamento modifiche e l'ambito di distribuzione dell'inventario](manage-scope-configurations.md).
* Se è necessario eseguire ricerche nei log archiviati nei log di monitoraggio di Azure, vedere [ricerche nei log nei log di monitoraggio di Azure](../../azure-monitor/log-query/log-query-overview.md).
* Se terminato con le distribuzioni, vedere [rimuovere rilevamento modifiche e l'inventario](remove-feature.md).
* Per eliminare le macchine virtuali da Rilevamento modifiche e dall'inventario, vedere [rimuovere VM da rilevamento modifiche e inventario](remove-vms-from-change-tracking.md).
* Per risolvere i problemi relativi alla funzionalità, vedere [Risolvere i problemi relativi a Rilevamento modifiche e inventario](../troubleshoot/change-tracking.md).
