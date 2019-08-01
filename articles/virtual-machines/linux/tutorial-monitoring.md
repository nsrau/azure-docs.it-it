---
title: Esercitazione - Monitorare e aggiornare le macchine virtuali Linux in Azure | Microsoft Docs
description: In questa esercitazione si apprenderà come monitorare la diagnostica di avvio e le metriche delle prestazioni e gestire gli aggiornamenti dei pacchetti in una macchina virtuale Linux
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/26/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 1c83cd869142967b358aa5d234d7d487b3c54b4c
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607997"
---
# <a name="tutorial-monitor-and-update-a-linux-virtual-machine-in-azure"></a>Esercitazione: Monitorare e aggiornare una macchina virtuale Linux in Azure

Per assicurarsi che le macchine virtuali in Azure vengano eseguite correttamente, è possibile esaminare la diagnostica di avvio e le metriche delle prestazioni e gestire gli aggiornamenti dei pacchetti. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Abilitare la diagnostica di avvio in una macchina virtuale
> * Visualizzare la diagnostica di avvio
> * Visualizzare le metriche host
> * Abilitare l'estensione della diagnostica nella macchina virtuale
> * Visualizzare le metriche della macchina virtuale
> * Creare avvisi basati sulle metriche di diagnostica
> * Gestire gli aggiornamenti dei pacchetti
> * Monitorare le modifiche e l'inventario
> * Configurare il monitoraggio avanzato

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.30 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="create-vm"></a>Creare una macchina virtuale

Per visualizzare la diagnostica e le metriche in azione, è necessaria una macchina virtuale. Creare prima un gruppo di risorse con [az group create](/cli/azure/group#az-group-create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupMonitor* nella località *eastus*.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Creare quindi una macchina virtuale con il comando [az vm create](/cli/azure/vm#az-vm-create). L'esempio seguente crea una macchina virtuale denominata *myVM* e genera chiavi SSH *~/.ssh/* , se non esistono ancora:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Abilitare la diagnostica di avvio

All'avvio delle macchine virtuali Linux, l'estensione diagnostica di avvio acquisisce l'output e lo memorizza nell'Archiviazione di Azure. Questi dati possono essere usati per risolvere i problemi di avvio della macchina virtuale. La diagnostica di avvio non viene abilitata automaticamente quando si crea una macchina virtuale Linux tramite l'interfaccia della riga di comando di Azure.

Prima di abilitare la diagnostica di avvio, è necessario creare un account di archiviazione per l'archiviazione dei log di avvio. Gli account di archiviazione devono avere un nome univoco globale, che abbia tra i 3 e i 24 caratteri e devono contenere solo numeri e lettere minuscole. Creare un account di archiviazione con il comando [az storage account create](/cli/azure/storage/account#az-storage-account-create). In questo esempio, viene usata una stringa casuale per creare un nome univoco per l'account di archiviazione.

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

Quando si abilita la diagnostica di avvio, è necessario l'URI per il contenitore di archiviazione BLOB. Il comando seguente esegue una query sull'account di archiviazione per restituire questo URI. Il valore URI viene archiviato nei nomi di variabile *bloburi* e verrà usato nel passaggio successivo.

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Abilitare ora la diagnostica di avvio con [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable). Il valore `--storage` è l'URI del BLOB raccolto nel passaggio precedente.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>Visualizzare la diagnostica di avvio

Quando la diagnostica di avvio è abilitata, ogni volta che si arresta e si avvia la macchina virtuale, in un file log vengono scritte le informazioni sul processo di avvio. Per questo esempio, deallocare prima la macchina virtuale con il comando [az vm deallocate](/cli/azure/vm#az-vm-deallocate) come illustrato di seguito:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Ora avviare la macchina virtuale con il comando [az vm start]( /cli/azure/vm#az-vm-stop) come illustrato di seguito:

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

È possibile ottenere i dati della diagnostica di avvio per *myVM* con il comando [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log) come illustrato di seguito:

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>Visualizzare le metriche host

Una macchina virtuale Linux è un host dedicato in Azure con cui interagisce. Le metriche per l'host vengono raccolte automaticamente e possono essere visualizzate nel portale di Azure come indicato di seguito:

1. Nel portale di Azure selezionare **Gruppi di risorse**, scegliere **myResourceGroupMonitor**, quindi selezionare **myVM** nell'elenco delle risorse.
1. Per visualizzare le prestazioni della macchina virtuale host, selezionare **Metriche** nella finestra della macchina virtuale, quindi selezionare una qualsiasi metrica *[Host]* in **Metriche disponibili**.

    ![Visualizzare le metriche host](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Installare l'estensione di diagnostica

Sono disponibili le metriche di base host, ma per visualizzare metriche specifiche per la VM e più granulari è necessario installare l'estensione Diagnostica di Azure nella VM. L'estensione Diagnostica di Azure consente un monitoraggio aggiuntivo e il recupero dei dati di diagnostica dalla macchina virtuale. È possibile visualizzare queste metriche delle prestazioni e creare avvisi in base al funzionamento della macchina virtuale. L'estensione Diagnostica viene installata tramite il portale di Azure come indicato di seguito:

1. Nel portale di Azure scegliere **Gruppi di risorse**, selezionare **myResourceGroupMonitor**, quindi selezionare **myVM** nell'elenco delle risorse.
1. Selezionare **Impostazioni di diagnostica**. Nel menu a discesa *Selezionare un account di archiviazione*, se non è già selezionato, scegliere l'account *mydiagdata [1234]* creato nella sezione precedente.
1. Fare clic sul pulsante **Abilita monitoraggio a livello di guest**.

    ![Visualizzare le metriche di diagnostica](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Visualizzare le metriche della macchina virtuale

È possibile visualizzare le metriche della macchina virtuale nello stesso modo in cui sono state visualizzate le metriche della macchina virtuale host:

1. Nel portale di Azure scegliere **Gruppi di risorse**, selezionare **myResourceGroupMonitor**, quindi selezionare **myVM** nell'elenco delle risorse.
1. Per visualizzare le prestazioni della macchina virtuale, selezionare **Metriche** nella finestra della macchina virtuale, quindi selezionare una qualsiasi metrica di diagnostica *[Guest]* in **Metriche disponibili**.

    ![Visualizzare le metriche della macchina virtuale](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Creare avvisi

È possibile creare avvisi basati sulle metriche di prestazioni specifiche. Gli avvisi possono essere usati, ad esempio, per inviare una notifica quando l'uso medio della CPU supera una determinata soglia o lo spazio su disco disponibile è inferiore a una determinata quantità. Gli avvisi vengono visualizzati nel portale di Azure o possono essere inviati tramite posta elettronica. In risposta agli avvisi generati, è anche possibile attivare i runbook di Automazione di Azure o App per la logica di Azure.

L'esempio seguente crea un avviso per l'uso medio della CPU.

1. Nel portale di Azure selezionare **Gruppi di risorse**, selezionare **myResourceGroupMonitor**, quindi selezionare **myVM** nell'elenco delle risorse.
2. Selezionare **Avvisi (versione classica)** , quindi scegliere **Aggiungi avviso per la metrica (versione classica)** per la parte superiore della finestra degli avvisi.
3. Inserire un **Nome** per l'avviso, ad esempio *myAlertRule*
4. Per attivare un avviso quando la percentuale di CPU supera 1.0 per cinque minuti, lasciare tutte le altre impostazioni predefinite selezionate.
5. Facoltativamente, è possibile selezionare la casella per *Invia messaggio di posta elettronica a proprietari, collaboratori e lettori* per inviare una notifica tramite posta elettronica. L'azione predefinita è di presentare una notifica nel portale.
6. Selezionare il pulsante **OK**.

## <a name="manage-software-updates"></a>Gestire gli aggiornamenti software

La gestione degli aggiornamenti consente di gestire gli aggiornamenti e le patch per le macchine virtuali Linux di Azure.
Direttamente dalla macchina virtuale è possibile valutare in modo rapido lo stato degli aggiornamenti disponibili, pianificare l'installazione degli aggiornamenti richiesti ed esaminare i risultati della distribuzione per verificare che gli aggiornamenti siano stati applicati correttamente alla macchina virtuale.

Per informazioni sui prezzi, vedere [Prezzi di Automazione per la gestione degli aggiornamenti](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>Abilitare Gestione aggiornamenti

Abilitare Gestione aggiornamenti per la macchina virtuale:

1. Sul lato sinistro della schermata selezionare **Macchine virtuali**.
2. Selezionare una macchina virtuale dall'elenco.
3. Nella schermata della macchina virtuale nella sezione **Operazioni** selezionare **Gestione aggiornamenti**. Viene visualizzata la schermata **Abilita Gestione aggiornamenti**.

Viene eseguita una convalida per determinare se Gestione aggiornamenti è abilitata per la macchina virtuale.
La convalida include controlli per un'area di lavoro Log Analytics e un account di Automazione collegato e verifica se la soluzione è presente nell'area di lavoro.

L'area di lavoro di [Log Analytics](../../log-analytics/log-analytics-overview.md) consente di raccogliere i dati generati da funzionalità e servizi, ad esempio Gestione aggiornamenti.
L'area di lavoro offre un'unica posizione per esaminare e analizzare i dati di più origini.
Per eseguire altre azioni nelle macchine virtuali che richiedono gli aggiornamenti, Automazione di Azure consente di eseguire runbook nelle macchine virtuali, ad esempio il download e l'applicazione degli aggiornamenti.

Il processo di convalida controlla anche se nella macchina virtuale sono presenti l'agente Log Analytics e un ruolo di lavoro ibrido per runbook di Automazione. L'agente consente di comunicare con la macchina virtuale e ottenere informazioni sullo stato dell'aggiornamento.

Scegliere l'area di lavoro di Log Analytics e l'account di automazione da usare e selezionare **Abilita** per abilitare la soluzione. Per l'abilitazione della soluzione sono necessari fino a 15 minuti.

Se risultano mancanti durante l'onboarding, i prerequisiti seguenti vengono aggiunti automaticamente:

* Area di lavoro di [Log Analytics](../../log-analytics/log-analytics-overview.md)
* [Account di Automazione](../../automation/automation-offering-get-started.md)
* [Ruolo di lavoro ibrido per runbook](../../automation/automation-hybrid-runbook-worker.md) abilitato nella macchina virtuale

Viene visualizzata la schermata **Gestione aggiornamenti**. Configurare la località, l'area di lavoro di Log Analytics e l'account di automazione da usare e selezionare **Abilita**. Se i campi sono inattivi, significa che un'altra soluzione di automazione è abilitata per la VM e devono essere usati la stessa area di lavoro e lo stesso account di Automazione.

![Abilitare la soluzione Gestione aggiornamenti](./media/tutorial-monitoring/manage-updates-update-enable.png)

L'abilitazione della soluzione può richiedere fino a 15 minuti. Durante questo intervallo di tempo, non chiudere la finestra del browser. Dopo l'abilitazione della soluzione, le informazioni sugli aggiornamenti mancanti nella macchina virtuale passano ai log di Monitoraggio di Azure. Affinché i dati diventino disponibili per l'analisi, sarà necessario attendere da 30 minuti a 6 ore.

### <a name="view-update-assessment"></a>Visualizzare la valutazione degli aggiornamenti

Dopo aver abilitato **Gestione aggiornamenti**, viene visualizzata la schermata **Gestione aggiornamenti**. Dopo la valutazione degli aggiornamenti, viene visualizzato un elenco di aggiornamenti mancanti nella scheda **Aggiornamenti mancanti**.

 ![Visualizzare lo stato degli aggiornamenti](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>Pianificare la distribuzione degli aggiornamenti

Per installare gli aggiornamenti, pianificare una distribuzione che rispetti la pianificazione dei rilasci e l'intervallo di servizio. È possibile scegliere i tipi di aggiornamento da includere nella distribuzione. È possibile ad esempio includere gli aggiornamenti critici o della sicurezza ed escludere gli aggiornamenti cumulativi.

Pianificare una nuova distribuzione di aggiornamenti per la macchina virtuale facendo clic su **Pianifica la distribuzione di aggiornamenti** nella parte superiore della schermata **Gestione aggiornamenti**. Nella schermata **Nuova distribuzione di aggiornamenti** specificare le informazioni seguenti:

Per creare una nuova distribuzione di aggiornamenti, selezionare **Pianifica la distribuzione di aggiornamenti**. Si apre la pagina **Nuova distribuzione di aggiornamenti**. Specificare i valori per le proprietà descritte nella tabella seguente e quindi fare clic su **Crea**:

| Proprietà | DESCRIZIONE |
| --- | --- |
| NOME |Nome univoco che identifica la distribuzione degli aggiornamenti. |
|Sistema operativo| Linux o Windows|
| Gruppi da aggiornare |Per i computer di Azure, definire una query basata su una combinazione di sottoscrizione, gruppi di risorse, posizioni e tag per creare un gruppo dinamico di macchine virtuali di Azure da includere nella distribuzione. </br></br>Per i computer non di Azure, selezionare una ricerca esistente salvata per selezionare un gruppo di computer non di Azure da includere nella distribuzione. </br></br>Per altre informazioni, vedere [Gruppi dinamici](../../automation/automation-update-management.md#using-dynamic-groups)|
| Computer da aggiornare |Selezionare una ricerca salvata o un gruppo importato, oppure scegliere Computer dall'elenco a discesa e selezionare i singoli computer. Se si sceglie**Computer**, l'idoneità del computer è indicata nella colonna **AGGIORNA IDONEITÀ AGENTE**.</br> Per altre informazioni sui diversi metodi di creazione di gruppi di computer nei log di Monitoraggio di Azure, vedere [Gruppi di computer nei log di Monitoraggio di Azure](../../azure-monitor/platform/computer-groups.md) |
|Classificazioni degli aggiornamenti|Selezionare tutte le classificazioni degli aggiornamenti necessarie|
|Includi/Escludi aggiornamenti|Apre la pagina **Includi/Escludi**. Gli aggiornamenti da includere o escludere si trovano in schede separate. Per altre informazioni sulla modalità di gestione dell'inclusione, vedere il [comportamento dell'inclusione](../../automation/automation-update-management.md#inclusion-behavior) |
|Impostazioni di pianificazione|Selezionare l'ora di inizio e selezionare Una sola volta o Ricorrente per la ricorrenza|
| Pre-script e post-script|Selezionare gli script da eseguire prima e dopo la distribuzione|
| Finestra di manutenzione |Numero di minuti impostato per gli aggiornamenti. Il valore non può essere inferiore a 30 minuti e superiore a 6 ore |
| Controllo riavvio| Determina come vengono gestiti i riavvii. Le opzioni disponibili sono:</br>Riavvia se necessario (opzione predefinita)</br>Riavvia sempre</br>Non riavviare mai</br>Riavvia solamente: gli aggiornamenti non verranno installati|

Le distribuzioni di aggiornamenti possono essere create anche a livello di codice. Per informazioni su come creare una distribuzione di aggiornamenti con l'API REST, vedere [Software Update Configurations - Create](/rest/api/automation/softwareupdateconfigurations/create) (Configurazioni degli aggiornamenti software - Creazione). È anche disponibile un runbook di esempio che può essere usato per creare una distribuzione di aggiornamenti settimanale. Per altre informazioni su questo runbook, vedere [Create a weekly update deployment for one or more VMs in a resource group](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1) (Creare una distribuzione di aggiornamenti settimanale per una o più macchine virtuali in un gruppo di risorse).

Dopo avere configurato la pianificazione, fare clic sul pulsante **Crea**. Viene nuovamente visualizzato il dashboard di stato.
Si noti che la tabella **Pianificata** mostra la pianificazione della distribuzione creata.

### <a name="view-results-of-an-update-deployment"></a>Visualizzare i risultati di una distribuzione di aggiornamenti

Dopo avere avviato la distribuzione pianificata, è possibile visualizzare lo stato della distribuzione nella scheda **Distribuzioni di aggiornamenti** nella schermata **Gestione aggiornamenti**.
Se la distribuzione è in corso, viene visualizzato lo stato **In corso**. Quando la distribuzione viene completata correttamente, lo stato diventa **Completato**.
Se si verifica un errore in uno o più aggiornamenti della distribuzione, lo stato sarà **Parzialmente non riuscito**.
Selezionare la distribuzione di aggiornamenti completata per visualizzare il dashboard della distribuzione.

![Dashboard di stato di una distribuzione di aggiornamenti specifica](./media/tutorial-monitoring/manage-updates-view-results.png)

Il riquadro **Risultati aggiornamento** include un riepilogo del numero totale di aggiornamenti e dei risultati della distribuzione nella macchina virtuale.
La tabella di destra visualizza una descrizione dettagliata di ogni aggiornamento e i risultati dell'installazione che possono corrispondere a uno dei valori seguenti:

* **Tentativo non eseguito**: l'aggiornamento non è stato installato a causa di tempo disponibile non sufficiente basato sulla durata della finestra di manutenzione specificata.
* **Completato**: l'aggiornamento è stato completato
* **Non riuscito**: l'aggiornamento non è riuscito

Selezionare **Tutti i log** per visualizzare tutte le voci di log create dalla distribuzione.

Selezionare il riquadro **Output** per visualizzare il flusso del processo del runbook responsabile della gestione della distribuzione di aggiornamenti nella macchina virtuale di destinazione.

Per visualizzare informazioni dettagliate sugli errori della distribuzione, selezionare **Errori**.

## <a name="monitor-changes-and-inventory"></a>Monitorare le modifiche e l'inventario

È possibile raccogliere e visualizzare l'inventario per il software, i file, i daemon Linux, i servizi di Windows e le chiavi del Registro di sistema di Windows presenti nei computer. Tenendo traccia delle configurazioni dei computer, è possibile individuare i problemi operativi dell'ambiente e conoscere meglio lo stato dei computer.

### <a name="enable-change-and-inventory-management"></a>Abilitare la gestione delle modifiche e dell'inventario

Abilitare la gestione delle modifiche e dell'inventario per la macchina virtuale:

1. Sul lato sinistro della schermata selezionare **Macchine virtuali**.
2. Selezionare una macchina virtuale dall'elenco.
3. Nella sezione **Operazioni** della schermata della macchina virtuale selezionare **Inventario** o **Rilevamento modifiche**. Verrà visualizzata la schermata **Enable Change Tracking and Inventory** (Abilita rilevamento modifiche e inventario).

Configurare la località, l'area di lavoro di Log Analytics e l'account di automazione da usare e selezionare **Abilita**. Se i campi sono inattivi, significa che un'altra soluzione di automazione è abilitata per la VM e devono essere usati la stessa area di lavoro e lo stesso account di Automazione. Sebbene le soluzioni siano presentate separatamente sul menu, rappresentano la stessa soluzione. L'abilitazione di una di queste le abilita entrambe per la macchina virtuale.

![Abilitare il rilevamento delle modifiche e dell'inventario](./media/tutorial-monitoring/manage-inventory-enable.png)

Dopo l'abilitazione della soluzione, la raccolta dell'inventario sulla macchina virtuale può richiedere alcuni minuti prima che i dati vengono visualizzati.

### <a name="track-changes"></a>Rilevare le modifiche

Nella macchina virtuale selezionare **Rilevamento modifiche** in **OPERAZIONI**. Selezionare **Modifica impostazioni**. Verrà visualizzata la pagina **Rilevamento modifiche**. Selezionare il tipo di impostazione che si vuole rilevare e quindi fare clic su **+ Aggiungi** per configurare le impostazioni. L'opzione disponibile per Linux è **File Linux**

Per informazioni dettagliate sul rilevamento delle modifiche, vedere [Risolvere i problemi delle modifiche in una macchina virtuale](../../automation/automation-tutorial-troubleshoot-changes.md)

### <a name="view-inventory"></a>Visualizzare l'inventario

Nella macchina virtuale selezionare **Inventario** in **OPERAZIONI**. Nella scheda **Software** è presente una tabella con l'elenco del software trovato. I dettagli generali di ogni record software sono visibili nella tabella. Questi dettagli includono il nome del software, la versione, l'autore, l'ora dell'ultimo aggiornamento.

![Visualizzare l'inventario](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Monitorare i log attività e le modifiche

Dalla pagina **Rilevamento modifiche** della VM, selezionare **Gestisci connessione al log attività**. Si aprirà la pagina **Log attività di Azure**. Selezionare **Connetti** per connettere Rilevamento modifiche al log attività di Azure per la VM.

Con questa impostazione abilitata, passare alla pagina **Panoramica** della VM e selezionare **Arresta** per arrestare la macchina virtuale. Quando richiesto, selezionare **Sì** per arrestare la VM. Quando la VM è deallocata, selezionare **Avvia** per riavviarla.

Con l'arresto e l'avvio di una macchina virtuale viene registrato un evento nel log attività. Tornare alla pagina **Rilevamento modifiche**. Selezionare la scheda **Eventi** nella parte inferiore della pagina. Dopo poco, gli eventi verranno visualizzati nel grafico e nella tabella. È possibile selezionare ogni evento per visualizzare le relative informazioni dettagliate.

![Visualizzare le modifiche nel log attività](./media/tutorial-monitoring/manage-activitylog-view-results.png)

Il grafico mostra le modifiche nel tempo. Dopo aver aggiunto una connessione al log attività, il grafico a linee nella parte superiore visualizza gli eventi del log attività di Azure. Ogni riga dei grafici a barre rappresenta un tipo di modifica tracciabile diverso. Questi tipi sono i daemon, i file e il software Linux. La scheda delle modifiche visualizza i dettagli delle modifiche in ordine decrescente in base alla data della modifica, a partire dalla più recente.

## <a name="advanced-monitoring"></a>Monitoraggio avanzato

Per il monitoraggio più avanzato della macchina virtuale è possibile usare una soluzione come [Monitoraggio di Azure per le macchine virtuali](../../azure-monitor/insights/vminsights-overview.md), che monitora le macchine virtuali di Azure su larga scala analizzando le prestazioni e l'integrità delle macchine virtuali Windows e Linux, inclusi i relativi processi e le dipendenze interconnesse ad altre risorse e processi esterni. La gestione della configurazione delle proprie macchine virtuali di Azure viene fornita con la soluzione Rilevamento modifiche e inventario di [Automazione di Azure](../../automation/automation-intro.md) per identificare facilmente le modifiche nell'ambiente in uso. La gestione della conformità di aggiornamento viene fornita con la soluzione Gestione aggiornamenti di Automazione di Azure.   

Dall'area di lavoro Log Analytics a cui è connessa la macchina virtuale, è anche possibile recuperare, consolidare e analizzare i dati raccolti con il [linguaggio di query avanzato](../../azure-monitor/log-query/log-query-overview.md). 

![Area di lavoro Log Analytics](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati configurati, esaminati e gestiti gli aggiornamenti per una macchina virtuale. Si è appreso come:

> [!div class="checklist"]
> * Abilitare la diagnostica di avvio in una macchina virtuale
> * Visualizzare la diagnostica di avvio
> * Visualizzare le metriche host
> * Abilitare l'estensione della diagnostica nella macchina virtuale
> * Visualizzare le metriche della macchina virtuale
> * Creare avvisi basati sulle metriche di diagnostica
> * Gestire gli aggiornamenti dei pacchetti
> * Monitorare le modifiche e l'inventario
> * Configurare il monitoraggio avanzato

Passare all'esercitazione successiva per informazioni sul Centro sicurezza di Azure.

> [!div class="nextstepaction"]
> [Gestire la sicurezza delle VM](../../security/fundamentals/overview.md)
