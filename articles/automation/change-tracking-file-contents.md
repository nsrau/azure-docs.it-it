---
title: Gestire Rilevamento modifiche e inventario in Automazione di Azure
description: Questo articolo illustra come usare Rilevamento modifiche e inventario per tenere traccia delle modifiche apportate al software e ai servizi Microsoft nell'ambiente in uso.
services: automation
ms.subservice: change-inventory-management
ms.date: 06/15/2020
ms.topic: conceptual
ms.openlocfilehash: eab509e389c074232526aa93fcebb72f3bc986c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185603"
---
# <a name="manage-change-tracking-and-inventory"></a>Gestire il rilevamento modifiche e l'inventario

Quando si aggiunge un nuovo file o una nuova chiave del registro di sistema da rilevare, automazione di Azure lo Abilita per [rilevamento modifiche e l'inventario](change-tracking.md). Questo articolo descrive come configurare il rilevamento, rivedere i risultati del rilevamento e gestire gli avvisi quando vengono rilevate modifiche.

Prima di usare le procedure descritte in questo articolo, assicurarsi di aver abilitato Rilevamento modifiche e l'inventario nelle VM usando una di queste tecniche:

* [Abilitare Rilevamento modifiche e inventario da un account di Automazione](automation-enable-changes-from-auto-acct.md)
* [Abilitare Rilevamento modifiche e l'inventario esplorando il portale di Azure](automation-enable-changes-from-browse.md)
* [Abilitare Rilevamento modifiche e inventario da un runbook](automation-enable-changes-from-runbook.md)
* [Abilitare Rilevamento modifiche e inventario da una macchina virtuale di Azure](automation-enable-changes-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Limitare l'ambito della distribuzione

Rilevamento modifiche e inventario usa una configurazione dell'ambito all'interno dell'area di lavoro per individuare i computer che ricevono le modifiche. Per altre informazioni, vedere [Limit rilevamento modifiche and Inventory Deployment scope](automation-scope-configurations-change-tracking.md).

## <a name="track-files"></a>Rilevare file

È possibile utilizzare Rilevamento modifiche e l'inventario per tenere traccia delle modifiche apportate a file e cartelle/directory. Questa sezione descrive come configurare il rilevamento dei file in Windows e in Linux.

### <a name="configure-file-tracking-on-windows"></a>Configurare il rilevamento dei file in Windows

Seguire questa procedura per configurare il rilevamento dei file in computer Windows:

1. Nell'account di Automazione selezionare **Rilevamento modifiche** in **Gestione della configurazione**. 
2. Fare clic su **Modifica impostazioni** (simbolo dell'ingranaggio).
3. Nella pagina Configurazione dell'area di lavoro selezionare **File Windows**, quindi fare clic su **+ Aggiungi** per aggiungere un nuovo file da rilevare.
4. Nel riquadro Aggiungi file Windows per Rilevamento modifiche immettere le informazioni per il file o la cartella da rilevare e fare clic su **Salva**. La tabella seguente definisce le proprietà che è possibile usare per le informazioni.

    |Proprietà  |Descrizione  |
    |---------|---------|
    |Attivato     | True se viene applicata l'impostazione e False in caso contrario.        |
    |Nome elemento     | Nome descrittivo del file da rilevare.        |
    |Gruppo     | Nome del gruppo per il raggruppamento logico dei file.        |
    |Immettere il percorso     | Percorso in cui cercare il file, ad esempio **c:\temp\\\*.txt**. È anche possibile usare le variabili di ambiente, ad esempio `%winDir%\System32\\\*.*`.       |
    |Tipo di percorso     | Tipo di percorso. I valori possibili sono file e Folder.        |    
    |Ricorsione     | True se viene usata la ricorsione per la ricerca dell'elemento da rilevare, False in caso contrario.        |    
    |Carica contenuto file | True per caricare il contenuto del file nelle modifiche rilevate e False in caso contrario.|

5. Assicurarsi di specificare True per **Carica contenuto file**. Questa impostazione abilita il rilevamento del contenuto del file solo per il percorso indicato.

### <a name="configure-file-tracking-on-linux"></a>Configurare il rilevamento dei file in Linux

Seguire questa procedura per configurare il rilevamento dei file in computer Linux:

1. Nell'account di Automazione selezionare **Rilevamento modifiche** in **Gestione della configurazione**. 
2. Fare clic su **Modifica impostazioni** (simbolo dell'ingranaggio).
3. Nella pagina Configurazione dell'area di lavoro selezionare **File Linux**, quindi fare clic su **+ Aggiungi** per aggiungere un nuovo file da rilevare.
4. Nel riquadro Aggiungi file Linux per Rilevamento modifiche immettere le informazioni per il file o la directory da rilevare e fare clic su **Salva**. La tabella seguente definisce le proprietà che è possibile usare per le informazioni.

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

5. Assicurarsi di specificare True per **Carica contenuto file**. Questa impostazione abilita il rilevamento del contenuto del file solo per il percorso indicato.

   ![Aggiungere file Linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>Rilevare il contenuto del file

Il rilevamento del contenuto del file consente di visualizzare il contenuto di un file prima e dopo una modifica rilevata. La funzionalità Salva il contenuto del file in un [account di archiviazione](../storage/common/storage-account-overview.md) dopo ogni modifica. Ecco alcune regole da seguire per rilevare il contenuto dei file:

* Un account di archiviazione standard con il modello di distribuzione di Gestione risorse è necessario per la memorizzazione del contenuto del file. 
* Non usare account di archiviazione con modello di distribuzione classica e Premium. Vedere [Informazioni sugli account di archiviazione di Azure](../storage/common/storage-account-create.md).
* È possibile connettere l'account di archiviazione a un solo account di automazione.
* [Rilevamento modifiche e l'inventario](change-tracking.md) devono essere abilitati nell'account di automazione.

### <a name="enable-tracking-for-file-content-changes"></a>Abilitare il rilevamento delle modifiche al contenuto del file

Usare la procedura seguente per abilitare il rilevamento delle modifiche apportate al contenuto del file:

1. Nel portale di Azure aprire l'account di Automazione e quindi selezionare **Rilevamento modifiche** in **Gestione della configurazione**.
2. Fare clic su **Modifica impostazioni** (simbolo dell'ingranaggio).
3. Selezionare **Contenuto file** e fare clic su **Collegamento**. Con questa selezione si apre il riquadro Aggiungi posizione contenuto per Rilevamento modifiche.

   ![Aggiungi percorso contenuto](./media/change-tracking-file-contents/enable.png)

4. Selezionare la sottoscrizione e l'account di archiviazione da usare per archiviare il contenuto del file. 

5. Se si desidera abilitare il rilevamento di contenuto dei file per tutti i file verificati esistenti, selezionare **On** per **Caricare il contenuto del file per tutte le impostazioni**. È possibile modificare questa impostazione per ogni percorso di file in un secondo momento.

   ![Impostare l'account di archiviazione](./media/change-tracking-file-contents/storage-account.png)

6. Rilevamento modifiche e inventario mostra gli URI dell'account di archiviazione e della firma di accesso condiviso (SAS) quando abilita il rilevamento delle modifiche al contenuto del file. Le firme scadono dopo 365 giorni ed è possibile ricrearle facendo clic su **Rigenera**.

   ![Elencare le chiavi dell'account](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Visualizzare il contenuto di un file rilevato

Dopo che Rilevamento modifiche e inventario ha individuato una modifica a un file rilevato, è possibile visualizzare il contenuto del file nel riquadro Dettagli modifiche.  

![Elencare le modifiche](./media/change-tracking-file-contents/change-list.png)

1. Nel portale di Azure aprire l'account di Automazione e quindi selezionare **Rilevamento modifiche** in **Gestione della configurazione**.

2. Scegliere un file nell'elenco delle modifiche e selezionare **Visualizza modifiche contenuto file** per visualizzare il contenuto del file. Il riquadro Modifica dettagli Mostra le informazioni standard prima e dopo file per ogni proprietà.

   ![Dettagli modifiche](./media/change-tracking-file-contents/change-details.png)

3. Il contenuto del file viene mostrato in una visualizzazione affiancata. È possibile selezionare **In linea** per ottenere una visualizzazione in linea delle modifiche.

## <a name="track-registry-keys"></a>Rilevare le chiavi del Registro di sistema

Seguire questa procedura per configurare il rilevamento delle chiavi del Registro di sistema in computer Windows:

1. Nel portale di Azure aprire l'account di Automazione e quindi selezionare **Rilevamento modifiche** in **Gestione della configurazione**. 
2. Fare clic su **Modifica impostazioni** (simbolo dell'ingranaggio).
3. Nella pagina Configurazione dell'area di lavoro selezionare **Registro di sistema di Windows**.
4. Fare clic su **+ Aggiungi** per aggiungere una nuova chiave del Registro di sistema da rilevare.
5. Nel riquadro Aggiungi Registro di sistema di Windows per Rilevamento modifiche immettere le informazioni per la chiave da rilevare e fare clic su **Salva**. La tabella seguente definisce le proprietà che è possibile usare per le informazioni.

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
|`ConfigurationData`<br>&#124; `where ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"`<br>&#124; `where SvcState == "Stopped"`<br>&#124; `summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | Mostra i record dell'inventario più recenti per i servizi Microsoft che sono stati impostati su Auto (Automatico) ma sono stati segnalati come Stopped (Interrotto). I risultati sono limitati al record più recente per il nome software e il computer specificati.    |
|`ConfigurationChange`<br>&#124; `where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br>&#124; `order by TimeGenerated desc`|Mostra i record di modifica per il software rimosso.|

## <a name="create-alerts-on-changes"></a>Creare avvisi relativi alle modifiche

L'esempio seguente mostra che il file **c:\windows\system32\drivers\etc\hosts** è stato modificato in un computer. Questo file è importante perché viene usato da Windows per risolvere i nomi host in indirizzi IP. Questa operazione ha la precedenza rispetto a DNS e potrebbe causare problemi di connettività. Può anche causare il reindirizzamento del traffico a siti Web dannosi o altrimenti pericolosi.

![Grafico che mostra la modifica del file hosts](./media/change-tracking-file-contents/changes.png)

Si userà questo esempio per illustrare i passaggi per la creazione di avvisi in una modifica.

1. Nell'account di Automazione selezionare **Rilevamento modifiche** in **Gestione della configurazione**, quindi selezionare **Log Analytics**. 
2. In Ricerca log cercare le modifiche al contenuto nel file **hosts** file con la query `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. Questa query cerca le modifiche al contenuto per i file con nomi di percorso completi che contengono la parola `hosts` . È anche possibile cercare un file specifico usando il formato completo del percorso anziché una sola parte, ad esempio `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`.

3. Una volta restituiti i risultati della query, fare clic su **nuova regola di avviso** nella ricerca log per aprire la pagina di creazione dell'avviso. È possibile accedere a questa pagina anche tramite **Monitoraggio di Azure** nel portale di Azure. 

4. Controllare di nuovo la query e modificare la logica di avviso. In questo caso, si vuole che l'avviso venga attivato anche se viene rilevata una sola modifica in tutti i computer nell'ambiente.

    ![Modificare la query per tenere traccia delle modifiche apportate al file hosts](./media/change-tracking-file-contents/change-query.png)

5. Dopo aver impostato la logica di avviso, assegnare i gruppi di azioni per eseguire azioni in risposta all'attivazione dell'avviso. In questo caso vengono configurati messaggi di posta elettronica da inviare e un ticket di Gestione dei servizi IT (ITSM) da creare. 

    ![Configurazione del gruppo di azioni per l'avviso in seguito alla modifica](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle configurazioni dell'ambito, vedere [limitare rilevamento modifiche e l'ambito di distribuzione dell'inventario](automation-scope-configurations-change-tracking.md).
* Se è necessario eseguire ricerche nei log archiviati nell'area di lavoro Log Analytics, vedere [Ricerche nei log di Monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md).
* Se terminato con le distribuzioni, vedere [scollegare l'area di lavoro dall'account di automazione per rilevamento modifiche e l'inventario](automation-unlink-workspace-change-tracking.md).
* Per eliminare le macchine virtuali da Rilevamento modifiche e dall'inventario, vedere [rimuovere VM da rilevamento modifiche e inventario](automation-remove-vms-from-change-tracking.md).
* Per risolvere i problemi relativi alla funzionalità, vedere [Risolvere i problemi relativi a Rilevamento modifiche e inventario](troubleshoot/change-tracking.md).
