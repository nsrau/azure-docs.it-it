---
title: Gestire Rilevamento modifiche e inventario in automazione di Azure
description: Questo articolo illustra come usare Rilevamento modifiche e l'inventario per tenere traccia delle modifiche al software e al servizio Microsoft che si verificano nell'ambiente in uso.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 8ca1bd7a724d3256bc2e171ce39fd6a06e2e5935
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779298"
---
# <a name="manage-change-tracking-and-inventory"></a>Gestire il rilevamento modifiche e l'inventario

Quando si aggiunge un nuovo file o una nuova chiave del registro di sistema da rilevare, automazione di Azure lo Abilita per la funzionalità di [rilevamento modifiche e inventario](change-tracking.md) . Questo articolo include procedure per l'utilizzo di questa funzionalità.

## <a name="enable-the-full-change-tracking-and-inventory-feature"></a>Abilitare la funzionalità di Rilevamento modifiche e inventario completa

Se è stato abilitato il [monitoraggio dell'integrità dei file (FIM) del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring), è possibile usare la funzionalità di rilevamento modifiche e inventario completa, come descritto di seguito. Le impostazioni non vengono rimosse da questo processo.

> [!NOTE]
> L'abilitazione della funzionalità di Rilevamento modifiche e inventario completa potrebbe causare addebiti aggiuntivi. Vedere [prezzi di automazione](https://azure.microsoft.com/pricing/details/automation/).

1. Rimuovere la soluzione di monitoraggio passando all'area di lavoro e individuando l' [elenco delle soluzioni di monitoraggio installate](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Fare clic sul nome della soluzione per aprire la relativa pagina di riepilogo e quindi fare clic su **Elimina**, come descritto in [rimuovere una soluzione di monitoraggio](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Per abilitare di nuovo Rilevamento modifiche e inventario, passare all'account di automazione e selezionare **rilevamento modifiche** in **gestione della configurazione**.
4. Scegliere l'area di lavoro Log Analytics e l'account di automazione, confermare le impostazioni dell'area di lavoro e fare clic su **Abilita**.

## <a name="onboard-machines-to-change-tracking-and-inventory"></a><a name="onboard"></a>Eseguire l'onboarding di computer per Rilevamento modifiche e inventario

Per avviare il rilevamento delle modifiche, è necessario abilitare Rilevamento modifiche e l'inventario in automazione di Azure. Ecco le modalità consigliate e supportate per l'onboarding dei computer a questa funzionalità: 

* [Onboarding da una macchina virtuale](automation-onboard-solutions-from-vm.md)
* [Onboarding dall'esplorazione di più computer](automation-onboard-solutions-from-browse.md)
* [Onboarding dall'account di automazione](automation-onboard-solutions-from-automation-account.md)
* [Onboarding in un Runbook di automazione di Azure](automation-onboard-solutions.md)

## <a name="track-files"></a>Rileva file

### <a name="configure-file-tracking-on-windows"></a>Configurare il rilevamento dei file in Windows

Per configurare il rilevamento dei file nei computer Windows, attenersi alla procedura seguente:

1. Nell'account di automazione selezionare **rilevamento modifiche** in **Gestione configurazione**. 
2. Fare clic su **Modifica impostazioni** (simbolo dell'ingranaggio).
3. Nella pagina Configurazione dell'area di lavoro selezionare **file di Windows**e quindi fare clic su **+ Aggiungi** per aggiungere un nuovo file da rilevare.
4. Nel riquadro Aggiungi file Windows per Rilevamento modifiche immettere le informazioni per il file da rilevare e fare clic su **Salva**. La tabella seguente definisce le proprietà che è possibile usare per le informazioni.

    |Proprietà  |Descrizione  |
    |---------|---------|
    |Attivato     | True se viene applicata l'impostazione e false in caso contrario.        |
    |Nome elemento     | Nome descrittivo del file da rilevare.        |
    |Gruppo     | Nome del gruppo per il raggruppamento logico dei file.        |
    |Immettere il percorso     | Percorso da verificare per il file, ad esempio **\\\*c:\Temp. txt**. È anche possibile usare le variabili di ambiente, `%winDir%\System32\\\*.*`ad esempio.       |
    |Tipo di percorso     | Tipo di percorso. I valori possibili sono file e directory.        |    
    |Ricorsione     | True se viene utilizzata la ricorsione durante la ricerca dell'elemento da rilevare e false in caso contrario.        |    
    |Caricare il contenuto del file | True per caricare il contenuto del file nelle modifiche rilevate e false in caso contrario.|

5. Assicurarsi di specificare true per **caricare il contenuto del file**. Questa impostazione Abilita il rilevamento del contenuto del file per il percorso del file indicato.

### <a name="configure-file-tracking-on-linux"></a>Configurare il rilevamento dei file in Linux

Seguire questa procedura per configurare il rilevamento dei file in computer Linux:

1. Nell'account di automazione selezionare **rilevamento modifiche** in **Gestione configurazione**. 
2. Fare clic su **Modifica impostazioni** (simbolo dell'ingranaggio).
3. Nella pagina Configurazione dell'area di lavoro selezionare **file Linux**e quindi fare clic su **+ Aggiungi** per aggiungere un nuovo file da rilevare.
4. Nel riquadro Aggiungi file Linux per Rilevamento modifiche immettere le informazioni per il file o la directory da rilevare e fare clic su **Salva**. La tabella seguente definisce le proprietà che è possibile usare per le informazioni.

    |Proprietà  |Descrizione  |
    |---------|---------|
    |Attivato     | True se viene applicata l'impostazione e false in caso contrario.        |
    |Nome elemento     | Nome descrittivo del file da rilevare.        |
    |Gruppo     | Nome del gruppo per il raggruppamento logico dei file.        |
    |Immettere il percorso     | Percorso da verificare per il file, ad esempio **/etc/*. conf**.       |
    |Tipo di percorso     | Tipo di percorso. I valori possibili sono file e directory.        |
    |Ricorsione     | True se viene utilizzata la ricorsione durante la ricerca dell'elemento da rilevare e false in caso contrario.        |
    |Usa Sudo     | True per utilizzare sudo quando si verifica l'elemento e false in caso contrario.         |
    |Collegamenti     | Impostazione che determina la modalità di gestione dei collegamenti simbolici durante l'attraversamento delle directory. I valori possibili sono:<br> Ignora: ignora i collegamenti simbolici e non include i file e le directory a cui viene fatto riferimento.<br>Segui: segue i collegamenti simbolici durante la ricorsione e include anche i file e le directory a cui si fa riferimento.<br>Gestisci: segue i collegamenti simbolici e consente la modifica del contenuto restituito. **Nota** : questa opzione non è consigliata perché non supporta il recupero del contenuto del file.    |
    |Caricare il contenuto del file | True per caricare il contenuto del file nelle modifiche rilevate e false in caso contrario. |

5. Assicurarsi di specificare true per **caricare il contenuto del file**. Questa impostazione Abilita il rilevamento del contenuto del file per il percorso del file indicato.

   ![Aggiungi file Linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>Rileva contenuto file

Rilevamento contenuto file consente di visualizzare il contenuto di un file prima e dopo una modifica rilevata. La funzionalità Salva il contenuto del file in un account di archiviazione dopo ogni modifica. Ecco alcune regole da seguire per tenere traccia del contenuto dei file:

* Un account di archiviazione standard con il modello di distribuzione di Gestione risorse è necessario per la memorizzazione del contenuto del file. 

* Non usare account di archiviazione con modello di distribuzione classica e Premium. Vedere [informazioni sugli account di archiviazione di Azure](../storage/common/storage-create-storage-account.md).

* L'account di archiviazione usato può essere connesso a un solo account di automazione.

* [Rilevamento modifiche e l'inventario](change-tracking.md) sono abilitati nell'account di automazione.

### <a name="enable-tracking-for-file-content-changes"></a>Abilita rilevamento per le modifiche al contenuto del file

1. Nella portale di Azure aprire l'account di automazione e quindi selezionare **rilevamento modifiche** in **Gestione configurazione**.
2. Fare clic su **Modifica impostazioni** (simbolo dell'ingranaggio).
3. Selezionare **Contenuto file** e fare clic su **Collegamento**. Questa selezione consente di aprire il riquadro Aggiungi percorso contenuto per Rilevamento modifiche.

   ![Abilita percorso contenuto](./media/change-tracking-file-contents/enable.png)

4. Selezionare la sottoscrizione e l'account di archiviazione da usare per archiviare il contenuto del file. 

5. Se si desidera abilitare il rilevamento di contenuto dei file per tutti i file verificati esistenti, selezionare **On** per **Caricare il contenuto del file per tutte le impostazioni**. È possibile modificare questa impostazione per ogni percorso di file in un secondo momento.

   ![Impostare l'account di archiviazione](./media/change-tracking-file-contents/storage-account.png)

6. Rilevamento modifiche e Inventory Mostra gli URI dell'account di archiviazione e della firma di accesso condiviso (SAS) quando Abilita il rilevamento delle modifiche al contenuto del file. Le firme scadono dopo 365 giorni ed è possibile ricrearle facendo clic su **Rigenera**.

   ![Elencare le chiavi dell'account](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Visualizzare il contenuto di un file rilevato

Una volta rilevata una modifica per un file rilevato, Rilevamento modifiche e l'inventario è possibile visualizzare il contenuto del file nel riquadro Change details.  

![Elencare le modifiche](./media/change-tracking-file-contents/change-list.png)

1. Nella portale di Azure aprire l'account di automazione e quindi selezionare **rilevamento modifiche** in **Gestione configurazione**.

2. Scegliere un file nell'elenco di modifiche e selezionare **Visualizza modifiche al contenuto del file** per visualizzare il contenuto del file. Il riquadro Modifica dettagli Mostra le informazioni standard prima e dopo file.

   ![Dettagli modifiche](./media/change-tracking-file-contents/change-details.png)

3. Il contenuto del file viene visualizzato in una visualizzazione affiancata. È possibile selezionare **inline** per visualizzare una visualizzazione inline delle modifiche.

## <a name="track-registry-keys"></a>Rileva chiavi del registro di sistema

Seguire questa procedura per configurare il rilevamento delle chiavi del Registro di sistema in computer Windows:

1. Nell'account di automazione selezionare **rilevamento modifiche** in **Gestione configurazione**. 
2. Fare clic su **Modifica impostazioni** (simbolo dell'ingranaggio).
3. Nella pagina Configurazione dell'area di lavoro selezionare **Registro di sistema di Windows**.
4. Fare clic su **+ Aggiungi** per aggiungere una nuova chiave del registro di sistema da rilevare.
5. Nel riquadro Aggiungi registro di sistema di Windows per Rilevamento modifiche immettere le informazioni per la chiave da rilevare e quindi fare clic su **Salva**. La tabella seguente definisce le proprietà che è possibile usare per le informazioni.

    |Proprietà  |Descrizione  |
    |---------|---------|
    |Attivato     | True se viene applicata un'impostazione e false in caso contrario.        |
    |Nome elemento     | Nome descrittivo della chiave del registro di sistema da rilevare.        |
    |Gruppo     | Nome del gruppo per il raggruppamento logico delle chiavi del registro di sistema.        |
    |Chiave del Registro di sistema di Windows   | Nome chiave con percorso, ad esempio **HKEY_LOCAL_MACHINE avvio Folders\Common Startup della shell \software\microsoft\windows\currentversion\explorer\user**.      |

## <a name="search-logs-for-change-records"></a>Cerca i record delle modifiche nei log

È possibile eseguire varie ricerche nei log di monitoraggio di Azure per i record delle modifiche. Con la pagina Rilevamento modifiche aperta, fare clic su **log Analytics** per aprire la pagina log. Nella tabella seguente vengono fornite le ricerche log di esempio per i record delle modifiche.

|Query  |Descrizione  |
|---------|---------|
|ConfigurationData<br>&#124; where ConfigDataType = = "Microsoft Services" e SvcStartupType = = "auto"<br>&#124; where SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | Mostra i record di inventario più recenti per i servizi Microsoft che sono stati impostati su auto, ma sono stati segnalati come arrestati. I risultati sono limitati al record più recente per il nome software e il computer specificati.    |
|ConfigurationChange<br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Removed"<br>&#124; order by TimeGenerated desc|Mostra i record delle modifiche per il software rimosso.|

## <a name="create-alerts-on-changes"></a>Crea avvisi per le modifiche

L'esempio seguente mostra che il file **C:\windows\system32\drivers\etc\hosts** è stato modificato in un computer. Questo file è importante perché viene utilizzato da Windows per risolvere i nomi host in indirizzi IP. Questa operazione ha la precedenza rispetto a DNS e potrebbe causare problemi di connettività. Può anche causare il reindirizzamento del traffico a siti Web dannosi o altrimenti pericolosi.

![Grafico che mostra la modifica del file hosts](./media/change-tracking-file-contents/changes.png)

Si userà questo esempio per illustrare i passaggi per la creazione di avvisi in una modifica.

1. Nell'account di automazione selezionare **rilevamento modifiche** in **Gestione configurazione**, quindi selezionare **log Analytics**. 
2. Nella ricerca log cercare le modifiche al contenuto del file **hosts** con la query `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. Questa query cerca una modifica del contenuto per i file con un percorso completo che contiene la parola "hosts". È anche possibile richiedere un file specifico cambiando la parte del percorso nel formato completo, ad esempio usando `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`.

3. Dopo che la query ha restituito i risultati desiderati, fare clic su **nuova regola di avviso** nella ricerca log per aprire la pagina di creazione dell'avviso. È anche possibile passare a questa pagina tramite **monitoraggio di Azure** nella portale di Azure. 

4. Controllare di nuovo la query e modificare la logica di avviso. In questo caso, si vuole che l'avviso venga attivato anche se viene rilevata una sola modifica in tutti i computer nell'ambiente.

    ![Modificare la query per tenere traccia delle modifiche apportate al file host](./media/change-tracking-file-contents/change-query.png)

5. Dopo aver impostato la logica di avviso, assegnare i gruppi di azioni per eseguire azioni in risposta all'avviso attivato. In questo caso, vengono configurati messaggi di posta elettronica da inviare e un ticket di gestione dei servizi IT (ITSM) da creare. 

    ![Configurazione del gruppo di azioni per l'avviso in seguito alla modifica](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni di base su Rilevamento modifiche e inventario, vedere [Panoramica di rilevamento modifiche e inventario](change-tracking.md).
* Per risolvere i problemi relativi alle modifiche per una macchina virtuale di Azure, vedere [risolvere i problemi di rilevamento modifiche e inventario](troubleshoot/change-tracking.md).
* Usare le [ricerche log nei log di monitoraggio di Azure](../log-analytics/log-analytics-log-searches.md) per visualizzare i dati dettagliati del rilevamento delle modifiche.