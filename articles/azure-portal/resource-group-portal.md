<properties 
	pageTitle="Uso del portale di Azure per gestire le risorse di Azure | Microsoft Azure" 
	description="Usare il portale di Azure e Azure Resource Manager per distribuire e gestire le risorse. Illustra come contrassegnare le risorse e visualizzare i log di controllo." 
	services="azure-resource-manager,azure-portal" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="03/29/2016" 
	ms.author="tomfitz"/>


# Uso del portale di Azure per distribuire e gestire le risorse di Azure

## Introduzione

Questo argomento illustra come usare il [portale di Azure](https://portal.azure.com) con [Azure Resource Manager](../resource-group-overview.md) per distribuire e gestire le risorse di Azure.

Non tutti i servizi attualmente supportano il portale o Gestione risorse. Per questi servizi, sarà necessario usare il [portale classico](https://manage.windowsazure.com). Per lo stato di ogni servizio, vedere il [Grafico della disponibilità dei portali di Azure](https://azure.microsoft.com/features/azure-portal/availability/).

È anche possibile gestire le risorse usando Azure PowerShell e l'interfaccia della riga di comando di Azure. Per altre informazioni sull'uso di queste interfacce, vedere [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md) e [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Azure Resource Manager](../xplat-cli-azure-resource-manager.md). Per altre informazioni sulla distribuzione di soluzioni con Visual Studio, vedere [Creazione e distribuzione di gruppi di risorse di Azure tramite Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## Creare e gestire gruppi di risorse

Per creare un gruppo di risorse vuoto, selezionare **Nuovo**, **Gestione** e **Gruppo di risorse**.

![creare un gruppo di risorse vuoto](./media/resource-group-portal/create-empty-group.png)

Assegnare un nome e un percorso e, se necessario, selezionare una sottoscrizione.

![impostare i valori del gruppo](./media/resource-group-portal/set-group-properties.png)

Dopo aver creato il gruppo di risorse, è possibile distribuirvi le risorse. Per avviare la distribuzione, è sufficiente selezionare **Nuovo** e il tipo di risorsa da distribuire.

![distribuire risorse](./media/resource-group-portal/deploy-resource.png)

Se il tipo di risorsa da distribuire non è visualizzato, si può cercarlo nel Marketplace.

![cercare nel Marketplace](./media/resource-group-portal/search-resource.png)

A seconda del tipo di risorsa selezionato, sarà disponibile una raccolta di proprietà rilevanti da impostare prima della distribuzione. Le opzioni non sono visualizzate in questo articolo, perché variano in base al tipo di risorsa. Per tutti i tipi è necessario selezionare un gruppo di risorse di destinazione. L'immagine seguente mostra come creare una nuova app Web e distribuirla nel gruppo di risorse appena creato.

![creare un gruppo di risorse](./media/resource-group-portal/select-existing-group.png)

In alternativa, è possibile decidere di creare un nuovo gruppo di risorse durante la distribuzione delle risorse. Invece di selezionare uno dei gruppi di risorse esistenti nella sottoscrizione, selezionare **Nuovo** e assegnare un nome al gruppo di risorse.

![creare un nuovo gruppo di risorse](./media/resource-group-portal/select-new-group.png)

Verrà avviata la distribuzione. L'operazione può richiedere alcuni minuti. Al termine della distribuzione, verrà visualizzata una notifica.

![visualizzare notifiche](./media/resource-group-portal/view-notification.png)

### Aggiungere risorse a un gruppo di risorse esistente

È possibile aggiungere risorse a un gruppo usando il comando **Add** nel pannello corrispondente.

![aggiungere una risorsa](./media/resource-group-portal/add-resource.png)

È possibile selezionare la risorsa desiderata dall'elenco disponibile.

### Esplorare i gruppi di risorse

È possibile esplorare tutti i gruppi di risorse facendo clic su **Gruppi di risorse**.

![esplorare i gruppi di risorse](./media/resource-group-portal/browse-groups.png)

Quando si seleziona un gruppo di risorse specifico, viene visualizzato un pannello del gruppo di risorse che fornisce informazioni su tale gruppo, incluso un elenco di tutte le risorse nel gruppo.

![riepilogo del gruppo di risorse](./media/resource-group-portal/group-summary.png)

È possibile aggiungere più tabelle e grafici al pannello del gruppo di risorse selezionando **Aggiungi una sezione** sotto il riepilogo.

![aggiungere una sezione](./media/resource-group-portal/add-section.png)

Viene visualizzata una raccolta di riquadri che consente di selezionare le informazioni da includere nel pannello. I tipi di riquadri visualizzati sono filtrati in base al tipo di risorsa. Se si seleziona una risorsa diversa, i riquadri disponibili cambiano.

![aggiungere una sezione](./media/resource-group-portal/tile-gallery.png)

Trascinare il riquadro necessario negli spazi disponibili.

![trascinare un riquadro](./media/resource-group-portal/drag-tile.png)

Dopo aver selezionato **Fatto** nella parte superiore del portale, la nuova vista farà parte del pannello.

![mostrare un riquadro](./media/resource-group-portal/show-lens.png)

Per accedere rapidamente al gruppo di risorse, è possibile aggiungere il pannello al dashboard.

![aggiungere un gruppo di risorse](./media/resource-group-portal/pin-group.png)

In alternativa, è possibile aggiungere una sezione del pannello al dashboard selezionando i puntini di sospensione (...) sopra la sezione. È inoltre possibile personalizzare le dimensioni della sezione nel pannello o rimuoverla completamente. L'immagine seguente illustra come aggiungere, personalizzare o rimuovere la sezione relativa a CPU e memoria.

![sezione di aggiunta](./media/resource-group-portal/pin-cpu-section.png)

Dopo avere aggiunto la sezione al dashboard, ne verrà visualizzato il riepilogo.

![visualizzare il dashboard](./media/resource-group-portal/view-startboard.png)

Selezionandola si potranno vedere altri dettagli sui dati.

### Eliminare un gruppo di risorse

Poiché i gruppi di risorse consentono di gestire il ciclo di vita di tutte le risorse contenute, l'eliminazione di un gruppo comporterà la rimozione di tutte le risorse al suo interno. È anche possibile eliminare singole risorse all'interno di un gruppo. Prestare attenzione quando si elimina un gruppo di risorse, in quanto potrebbe essere collegato ad altre risorse. È possibile visualizzare le risorse collegate nella mappa e quindi eseguire i passaggi necessari per evitare conseguenze involontarie quando si eliminano i gruppi. Le risorse collegate non verranno eliminate, ma è possibile che non funzionino come previsto.

![eliminare un gruppo](./media/resource-group-portal/delete-group.png)


## Visualizzare le distribuzioni precedenti

Nel pannello del gruppo di risorse è possibile visualizzare la data e lo stato dell'ultima distribuzione per questo gruppo di risorse. Se si seleziona il collegamento, viene visualizzata la cronologia delle distribuzioni per il gruppo.

![ultima distribuzione](./media/resource-group-portal/last-deployment.png)

Se si seleziona una distribuzione dalla cronologia, vengono visualizzate informazioni dettagliate su tale distribuzione.

![riepilogo della distribuzione](./media/resource-group-portal/deployment-summary.png)

È possibile visualizzare le singole operazioni eseguite durante la distribuzione. L'immagine seguente illustra un'operazione che ha avuto esito positivo e una che non è riuscita.

![dettagli dell'operazione](./media/resource-group-portal/operation-details.png)

Per informazioni sulla risoluzione dei problemi di una distribuzione, vedere l'articolo relativo alla [risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse con il portale di Azure](../resource-manager-troubleshoot-deployments-portal.md).

È possibile recuperare il modello usato per la distribuzione selezionando **Esporta modello**.

![esportare il modello](./media/resource-group-portal/export-template.png)

Verrà visualizzato il modello esatto usato per questa distribuzione.

![visualizzare il modello](./media/resource-group-portal/show-template.png)

Non è una rappresentazione completa del gruppo di risorse, perché se sono state aggiunte o eliminate risorse all'esterno di questa distribuzione, le azioni non vengono riflesse nel modello. Il pannello include il modello, un file di parametri da usare con il modello e uno script di PowerShell per distribuire il modello. È possibile scaricare questi 3 file selezionando **Salva su file**.

## Visualizzare i log di controllo

Il log di controllo include non solo le operazioni di distribuzione, ma tutte le operazioni di gestione eseguite sulle risorse nella sottoscrizione. Ad esempio, nei log di controllo è possibile visualizzare quando un utente dell'organizzazione ha arrestato un'app. Per visualizzare i log di controllo, selezionare **Esplora tutto** e **Log di controllo**.

![esplorare i log di controllo](./media/resource-group-portal/browse-audit-logs.png)

Nella sezione delle operazioni è possibile visualizzare le singole operazioni eseguite nella sottoscrizione.

![visualizzare i log di controllo](./media/resource-group-portal/view-audit-log.png)

Selezionando una delle operazioni, è possibile visualizzare maggiori dettagli, tra cui informazioni sull'utente che ha eseguito l'operazione.

Per altre informazioni sulla visualizzazione dei log di controllo, vedere [Operazioni di controllo con Gestione risorse](../resource-group-audit.md).

## Aggiungere tag alle risorse

È possibile applicare tag ai gruppi di risorse e alle risorse per organizzare logicamente gli asset. Per informazioni sull'utilizzo dei tag tramite il portale, vedere [Uso dei tag per organizzare le risorse di Azure](../resource-group-using-tags.md).

## Distribuire un modello personalizzato

Se si desidera eseguire una distribuzione ma non usare i modelli in Marketplace, è possibile creare un modello personalizzato che definisce l'infrastruttura per la soluzione. Per altre informazioni sui modelli, vedere [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md).

Per distribuire un modello personalizzato tramite il portale, selezionare **Nuovo**, cercare **Distribuzione modello** e quindi selezionarlo nelle opzioni disponibili.

![cercare la distribuzione del modello](./media/resource-group-portal/search-template.png)

Selezionare **Distribuzione modello** nelle risorse disponibili.

![selezionare la distribuzione del modello](./media/resource-group-portal/select-template.png)

Dopo aver avviato la distribuzione del modello, è possibile creare il modello personalizzato e impostare i valori per la distribuzione.

![creare un modello](./media/resource-group-portal/show-custom-template.png)

## Visualizzare la sottoscrizione e i costi

È possibile visualizzare informazioni sulla sottoscrizione e un riepilogo dei costi per tutte le risorse. Selezionare **Sottoscrizioni** e quindi la sottoscrizione da visualizzare. Potrebbe essere disponibile una sola sottoscrizione da selezionare.

![subscription](./media/resource-group-portal/select-subscription.png)

Nel pannello della sottoscrizione viene visualizzata la velocità.

![velocità](./media/resource-group-portal/burn-rate.png)

E una suddivisione dei costi in base al tipo di risorsa.

![costo delle risorse](./media/resource-group-portal/cost-by-resource.png)

## Controllo di accesso per i dashboard di Azure

L'accesso alle informazioni visualizzate nella maggior parte dei riquadri del portale è regolato dal [Controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-configure.md) di Azure. Per integrare facilmente i dashboard nell'ecosistema, tutti quelli pubblicati vengono implementati come risorse di Azure. Da un punto di vista del controllo di accesso, i dashboard non sono diversi da una macchina virtuale o da un account di archiviazione.

Di seguito è fornito un esempio. Si supponga di avere una sottoscrizione di Azure e che a diversi membri del team siano stati assegnati i ruoli di **proprietario**, **collaboratore** o **lettore** della sottoscrizione. Gli utenti con il ruolo di proprietario o collaboratore possono elencare, visualizzare, creare, modificare o eliminare dashboard nella sottoscrizione. Gli utenti con il ruolo di lettore possono elencare e visualizzare i dashboard, ma non modificarli o eliminarli. Gli utenti con accesso in lettura possono apportare modifiche locali a un dashboard pubblicato, ad esempio per risolvere un problema, ma non pubblicarle nel server. Hanno comunque la possibilità di creare una copia privata del dashboard per se stessi.

Si noti che i singoli riquadri del dashboard applicano requisiti di controllo di accesso personalizzati in base alle risorse per cui visualizzano i dati. Ciò significa che è possibile progettare un dashboard più ampiamente condivisibile, continuando comunque a proteggere i dati nei singoli riquadri.

## Passaggi successivi

- Per un'introduzione ai concetti di Gestione risorse, vedere [Panoramica di Gestione risorse di Azure](../resource-group-overview.md).
- Per un'introduzione all'uso di Azure PowerShell per la distribuzione delle risorse, vedere [Uso di Azure PowerShell con Gestione risorse di Azure](../powershell-azure-resource-manager.md).
- Per un'introduzione all'uso dell'interfaccia della riga di comando di Azure per la distribuzione delle risorse, vedere [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione risorse di Azure](../xplat-cli-azure-resource-manager.md).

<!---HONumber=AcomDC_0330_2016-->