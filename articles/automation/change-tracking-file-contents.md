---
title: Gestire Rilevamento modifiche e inventario in Automazione di Azure
description: Questo articolo illustra come usare Rilevamento modifiche e inventario per tenere traccia delle modifiche apportate al software e ai servizi Microsoft nell'ambiente in uso.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 4b8bf6a3f583e4c17f61e0a46911990ac5cc827c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830481"
---
# <a name="manage-change-tracking-and-inventory"></a>Gestire il rilevamento modifiche e l'inventario

Automazione di Azure offre la funzionalità [Rilevamento modifiche e inventario](change-tracking.md) per i computer nell'ambiente in uso. La funzionalità tiene traccia e rende disponibili le modifiche a chiavi del Registro di sistema, file, contenuti e simili. Questo articolo include procedure per l'uso di questa funzionalità.

## <a name="enable-the-full-change-tracking-and-inventory-feature"></a>Abilitare la funzionalità Rilevamento modifiche e inventario completa

Se è stato abilitato [Monitoraggio dell'integrità dei file del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring), è possibile usare la funzionalità Rilevamento modifiche e inventario completa per i computer, come descritto di seguito. Le impostazioni non vengono rimosse da questo processo.

> [!NOTE]
> L'abilitazione della funzionalità Rilevamento modifiche e inventario completa potrebbe causare addebiti aggiuntivi. Vedere [Prezzi di Automazione](https://azure.microsoft.com/pricing/details/automation/).

1. Rimuovere la soluzione di monitoraggio passando all'area di lavoro e trovandola nell'[elenco di soluzioni di monitoraggio installate](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Fare clic sul nome della soluzione per aprire la relativa pagina di riepilogo e quindi fare clic su **Elimina**, come descritto in [Rimuovere una soluzione di monitoraggio](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Per abilitare di nuovo Rilevamento modifiche e inventario, passare all'account di Automazione e selezionare **Rilevamento modifiche** o **Inventario** in **Gestione della configurazione**.
4. Scegliere l'area di lavoro Log Analytics e l'account di Automazione, confermare le impostazioni dell'area di lavoro e fare clic su **Abilita**.

## <a name="enable-machines-for-change-tracking-and-inventory"></a><a name="onboard"></a>Abilitare i computer per Rilevamento modifiche e inventario

Per iniziare a tenere traccia delle modifiche, è necessario abilitare Rilevamento modifiche e Inventario in Automazione di Azure. Questi sono i modi consigliati e supportati per abilitare questa funzionalità per i computer: 

* [Abilitare da una macchina virtuale](automation-onboard-solutions-from-vm.md)
* [Abilitare dall'esplorazione di più computer](automation-onboard-solutions-from-browse.md)
* [Abilitare dall'account di Automazione](automation-onboard-solutions-from-automation-account.md)
* [Abilitare in un runbook di Automazione di Azure](automation-onboard-solutions.md)

## <a name="track-files"></a>Rilevare file

### <a name="configure-file-tracking-on-windows"></a>Configurare il rilevamento dei file in Windows

Seguire questa procedura per configurare il rilevamento dei file in computer Windows:

1. Nell'account di Automazione selezionare **Rilevamento modifiche** in **Gestione della configurazione**. 
2. Fare clic su **Modifica impostazioni** (simbolo dell'ingranaggio).
3. Nella pagina Configurazione dell'area di lavoro selezionare **File Windows**, quindi fare clic su **+ Aggiungi** per aggiungere un nuovo file da rilevare.
4. Nel riquadro Aggiungi file Windows per Rilevamento modifiche immettere le informazioni per il file da rilevare e fare clic su **Salva**. La tabella seguente definisce le proprietà che è possibile usare per le informazioni.

    |Proprietà  |Descrizione  |
    |---------|---------|
    |Attivato     | True se viene applicata l'impostazione e False in caso contrario.        |
    |Nome elemento     | Nome descrittivo del file da rilevare.        |
    |Gruppo     | Nome del gruppo per il raggruppamento logico dei file.        |
    |Immettere il percorso     | Percorso in cui cercare il file, ad esempio **c:\temp\\\*.txt**. È anche possibile usare le variabili di ambiente, ad esempio `%winDir%\System32\\\*.*`.       |
    |Tipo di percorso     | Tipo di percorso. I valori possibili sono File e Directory.        |    
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
    |Collegamenti     | Impostazione che determina la modalità di gestione dei collegamenti simbolici durante l'attraversamento delle directory. I valori possibili sono:<br> Ignora: ignora i collegamenti simbolici e non include i file e le directory a cui viene fatto riferimento.<br>Segui: segue i collegamenti simbolici durante la ricorsione e include anche i file e le directory a cui viene fatto riferimento.<br>Gestisci: segue i collegamenti simbolici e consente la modifica del contenuto restituito. **Nota**: questa opzione non è consigliata perché non supporta il recupero del contenuto del file.    |
    |Carica contenuto file | True per caricare il contenuto del file nelle modifiche rilevate e False in caso contrario. |

5. Assicurarsi di specificare True per **Carica contenuto file**. Questa impostazione abilita il rilevamento del contenuto del file solo per il percorso indicato.

   ![Aggiungere file Linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>Rilevare il contenuto del file

Il rilevamento del contenuto del file consente di visualizzare il contenuto di un file prima e dopo una modifica rilevata. La funzionalità salva il contenuto del file in un account di archiviazione dopo ogni modifica. Ecco alcune regole da seguire per rilevare il contenuto dei file:

* Un account di archiviazione standard con il modello di distribuzione di Gestione risorse è necessario per la memorizzazione del contenuto del file. 

* Non usare account di archiviazione con modello di distribuzione classica e Premium. Vedere [Informazioni sugli account di archiviazione di Azure](../storage/common/storage-create-storage-account.md).

* L'account di archiviazione usato può essere connesso a un solo account di Automazione.

* [Rilevamento modifiche e inventario](change-tracking.md) è abilitato nell'account di Automazione.

### <a name="enable-tracking-for-file-content-changes"></a>Abilitare il rilevamento delle modifiche al contenuto del file

1. Nel portale di Azure aprire l'account di Automazione e quindi selezionare **Rilevamento modifiche** in **Gestione della configurazione**.
2. Fare clic su **Modifica impostazioni** (simbolo dell'ingranaggio).
3. Selezionare **Contenuto file** e fare clic su **Collegamento**. Con questa selezione si apre il riquadro Aggiungi posizione contenuto per Rilevamento modifiche.

   ![Abilitare il percorso del contenuto](./media/change-tracking-file-contents/enable.png)

4. Selezionare la sottoscrizione e l'account di archiviazione da usare per archiviare il contenuto del file. 

5. Se si desidera abilitare il rilevamento di contenuto dei file per tutti i file verificati esistenti, selezionare **On** per **Caricare il contenuto del file per tutte le impostazioni**. È possibile modificare questa impostazione per ogni percorso di file in un secondo momento.

   ![Impostare l'account di archiviazione](./media/change-tracking-file-contents/storage-account.png)

6. Rilevamento modifiche e inventario mostra gli URI dell'account di archiviazione e della firma di accesso condiviso (SAS) quando abilita il rilevamento delle modifiche al contenuto del file. Le firme scadono dopo 365 giorni ed è possibile ricrearle facendo clic su **Rigenera**.

   ![Elencare le chiavi dell'account](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Visualizzare il contenuto di un file rilevato

Dopo che Rilevamento modifiche e inventario ha individuato una modifica a un file rilevato, è possibile visualizzare il contenuto del file nel riquadro Dettagli modifiche.  

![Elencare le modifiche](./media/change-tracking-file-contents/change-list.png)

1. Nel portale di Azure aprire l'account di Automazione e quindi selezionare **Rilevamento modifiche** in **Gestione della configurazione**.

2. Scegliere un file nell'elenco delle modifiche e selezionare **Visualizza modifiche contenuto file** per visualizzare il contenuto del file. Il riquadro Dettagli modifiche mostra le informazioni standard prima e dopo la modifica.

   ![Dettagli modifiche](./media/change-tracking-file-contents/change-details.png)

3. Il contenuto del file viene mostrato in una visualizzazione affiancata. È possibile selezionare **In linea** per ottenere una visualizzazione in linea delle modifiche.

## <a name="track-registry-keys"></a>Rilevare le chiavi del Registro di sistema

Seguire questa procedura per configurare il rilevamento delle chiavi del Registro di sistema in computer Windows:

1. Nell'account di Automazione selezionare **Rilevamento modifiche** in **Gestione della configurazione**. 
2. Fare clic su **Modifica impostazioni** (simbolo dell'ingranaggio).
3. Nella pagina Configurazione dell'area di lavoro selezionare **Registro di sistema di Windows**.
4. Fare clic su **+ Aggiungi** per aggiungere una nuova chiave del Registro di sistema da rilevare.
5. Nel riquadro Aggiungi Registro di sistema di Windows per Rilevamento modifiche immettere le informazioni per la chiave da rilevare e fare clic su **Salva**. La tabella seguente definisce le proprietà che è possibile usare per le informazioni.

    |Proprietà  |Descrizione  |
    |---------|---------|
    |Attivato     | True se viene applicata l'impostazione e False in caso contrario.        |
    |Nome elemento     | Nome descrittivo della chiave del Registro di sistema da rilevare.        |
    |Gruppo     | Nome del gruppo per il raggruppamento logico delle chiavi del Registro di sistema.        |
    |Chiave del Registro di sistema di Windows   | Nome della chiave con percorso, ad esempio **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup**.      |

## <a name="search-logs-for-change-records"></a>Cercare i record delle modifiche nei log

È possibile eseguire varie ricerche di record delle modifiche nei log di Monitoraggio di Azure. Con la pagina Rilevamento modifiche aperta, fare clic su **Log Analytics** per aprire la pagina Log. La tabella seguente contiene esempi di ricerche log per i record di modifica.

|Query  |Descrizione  |
|---------|---------|
|ConfigurationData<br>&#124; dove   ConfigDataType == "Microsoft services" e SvcStartupType == "Auto"<br>&#124; where SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | Mostra i record dell'inventario più recenti per i servizi Microsoft che sono stati impostati su Auto (Automatico) ma sono stati segnalati come Stopped (Interrotto). I risultati sono limitati al record più recente per il nome software e il computer specificati.    |
|ConfigurationChange<br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Removed"<br>&#124; order by TimeGenerated desc|Mostra i record di modifica per il software rimosso.|

## <a name="create-alerts-on-changes"></a>Creare avvisi relativi alle modifiche

L'esempio seguente mostra che il file **C:\windows\system32\drivers\etc\hosts** è stato modificato in un computer. Questo file è importante perché viene usato da Windows per risolvere i nomi host in indirizzi IP. Questa operazione ha la precedenza rispetto a DNS e potrebbe causare problemi di connettività. Può anche causare il reindirizzamento del traffico a siti Web dannosi o altrimenti pericolosi.

![Grafico che mostra la modifica del file hosts](./media/change-tracking-file-contents/changes.png)

Si userà questo esempio per illustrare i passaggi per la creazione di avvisi in una modifica.

1. Nell'account di Automazione selezionare **Rilevamento modifiche** in **Gestione della configurazione**, quindi selezionare **Log Analytics**. 
2. In Ricerca log cercare le modifiche al contenuto nel file **hosts** file con la query `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. Questa query cerca una modifica del contenuto per i file con un percorso completo che contiene la parola "hosts". È anche possibile cercare un file specifico usando il formato completo del percorso anziché una sola parte, ad esempio `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`.

3. Dopo che la query ha restituito i risultati desiderati, fare clic sul pulsante **Nuova regola di avviso** nella ricerca log per aprire la pagina di creazione dell'avviso. È possibile accedere a questa pagina anche tramite **Monitoraggio di Azure** nel portale di Azure. 

4. Controllare di nuovo la query e modificare la logica di avviso. In questo caso, si vuole che l'avviso venga attivato anche se viene rilevata una sola modifica in tutti i computer nell'ambiente.

    ![Modificare la query per tenere traccia delle modifiche apportate al file hosts](./media/change-tracking-file-contents/change-query.png)

5. Dopo che è stata impostata la logica di avviso, assegnare gruppi di azioni in modo da eseguire azioni in risposta all'avviso attivato. In questo caso vengono configurati messaggi di posta elettronica da inviare e un ticket di Gestione dei servizi IT (ITSM) da creare. 

    ![Configurazione del gruppo di azioni per l'avviso in seguito alla modifica](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>Passaggi successivi

* Se è necessario eseguire ricerche nei log archiviati nell'area di lavoro Log Analytics, vedere [Ricerche nei log di Monitoraggio di Azure](../log-analytics/log-analytics-log-searches.md).
* Per risolvere i problemi relativi alla funzionalità, vedere [Risolvere i problemi relativi a Rilevamento modifiche e inventario](troubleshoot/change-tracking.md).