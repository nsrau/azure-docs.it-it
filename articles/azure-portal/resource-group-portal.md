<properties 
	pageTitle="Uso del portale di Azure per gestire le risorse di Azure | Microsoft Azure" 
	description="Usare il portale di Azure e Azure Resource Manager per distribuire e gestire le risorse. Illustra come contrassegnare le risorse e visualizzare i log di controllo." 
	services="azure-resource-manager,azure-portal" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/16/2016" 
	ms.author="tomfitz"/>


# Uso del portale di Azure per distribuire e gestire le risorse di Azure

## Introduzione

Questo argomento illustra come usare il [portale di Azure](https://portal.azure.com) con [Azure Resource Manager](../resource-group-overview.md) per distribuire e gestire le risorse di Azure.

Non tutti i servizi attualmente supportano il portale o Gestione risorse. Per questi servizi, sarà necessario usare il [portale classico](https://manage.windowsazure.com). Per lo stato di ogni servizio, vedere il [Grafico della disponibilità dei portali di Azure](https://azure.microsoft.com/features/azure-portal/availability/).

È anche possibile gestire le risorse usando Azure PowerShell e l'interfaccia della riga di comando di Azure. Per altre informazioni sull'uso di queste interfacce, vedere [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md) e [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Azure Resource Manager](../xplat-cli-azure-resource-manager.md). Per altre informazioni sulla distribuzione di soluzioni con Visual Studio, vedere [Creazione e distribuzione di gruppi di risorse di Azure tramite Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## Creare un gruppo di risorse

Per creare un gruppo di risorse vuoto, selezionare **Nuovo**, **Gestione** e **Gruppo di risorse**.

![creare un gruppo di risorse vuoto](./media/resource-group-portal/create-empty-group.png)

Assegnare un nome e un percorso e, se necessario, selezionare una sottoscrizione.

![impostare i valori del gruppo](./media/resource-group-portal/set-group-properties.png)

## Distribuire le risorse

Dopo aver creato il gruppo di risorse, è possibile distribuirvi le risorse. Per avviare una distribuzione, è sufficiente selezionare **Nuovo** e il tipo di risorsa da distribuire.

![distribuire risorse](./media/resource-group-portal/deploy-resource.png)

Se il tipo di risorsa da distribuire non è visualizzato, si può cercarlo nel Marketplace.

![cercare nel Marketplace](./media/resource-group-portal/search-resource.png)

A seconda del tipo di risorsa selezionato, sarà disponibile una raccolta di proprietà rilevanti da impostare prima della distribuzione. Le opzioni non sono visualizzate in questo articolo, perché variano in base al tipo di risorsa. Per tutti i tipi è necessario selezionare un gruppo di risorse di destinazione. L'immagine seguente mostra come creare una nuova app Web e distribuirla nel gruppo di risorse appena creato.

![creare un gruppo di risorse](./media/resource-group-portal/select-existing-group.png)

In alternativa, è possibile decidere di creare un nuovo gruppo di risorse durante la distribuzione delle risorse. Invece di selezionare uno dei gruppi di risorse esistenti nella sottoscrizione, selezionare **Nuovo** e assegnare un nome al gruppo di risorse.

![creare un nuovo gruppo di risorse](./media/resource-group-portal/select-new-group.png)

Verrà avviata la distribuzione. L'operazione può richiedere alcuni minuti. Al termine della distribuzione, verrà visualizzata una notifica.

![visualizzare notifiche](./media/resource-group-portal/view-notification.png)

Dopo avere distribuito le risorse, è possibile che si decida che è necessario aggiungere altre risorse al gruppo. È possibile aggiungere risorse a un gruppo usando il comando **Aggiungi** nel pannello corrispondente.

![aggiungere una risorsa](./media/resource-group-portal/add-resource.png)

## Esportare il modello

Dopo avere configurato il gruppo di risorse, è possibile che si voglia visualizzare il modello di Azure Resource Manager per il gruppo di risorse. L'esportazione del modello offre due vantaggi:

1. È possibile automatizzare le distribuzioni future della soluzione, perché tutti gli elementi dell'infrastruttura sono definiti nel modello.

2. È possibile acquisire familiarità con la sintassi del modello esaminando il codice JSON (JavaScript Object Notation) che rappresenta la soluzione.

> [AZURE.NOTE] La funzionalità di esportazione del modello è disponibile in anteprima e non tutti i tipi di risorse supportano attualmente l'esportazione di un modello. Quando si prova a esportare un modello, è possibile che venga visualizzato un errore che indica che alcune risorse non sono state esportate. Se necessario, è possibile definire manualmente queste risorse nel modello dopo averlo scaricato.

Per istruzioni dettagliate vedere [Esportare il modello di Azure Resource Manager dalle risorse esistenti](../resource-manager-export-template/).

## Gestire il gruppo di risorse

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

Dopo aver selezionato **Fatto** nella parte superiore del portale la nuova vista farà parte del pannello.

![mostrare un riquadro](./media/resource-group-portal/show-lens.png)

Per accedere rapidamente al gruppo di risorse, è possibile aggiungere il pannello al dashboard.

![aggiungere un gruppo di risorse](./media/resource-group-portal/pin-group.png)

In alternativa, è possibile aggiungere una sezione del pannello al dashboard selezionando i puntini di sospensione (...) sopra la sezione. È inoltre possibile personalizzare le dimensioni della sezione nel pannello o rimuoverla completamente. L'immagine seguente illustra come aggiungere, personalizzare o rimuovere la sezione relativa a CPU e memoria.

![sezione di aggiunta](./media/resource-group-portal/pin-cpu-section.png)

Dopo avere aggiunto la sezione al dashboard, ne verrà visualizzato il riepilogo.

![visualizzare il dashboard](./media/resource-group-portal/view-startboard.png)

Selezionandola si potranno vedere altri dettagli sui dati.

Poiché i gruppi di risorse consentono di gestire il ciclo di vita di tutte le risorse contenute, l'eliminazione di un gruppo comporterà la rimozione di tutte le risorse al suo interno. È anche possibile eliminare singole risorse all'interno di un gruppo. Prestare attenzione quando si elimina un gruppo di risorse, perché è possibile che altri gruppi di risorse includano risorse collegate ad esso. Le risorse collegate non verranno eliminate, ma è possibile che non funzionino come previsto.

![eliminare un gruppo](./media/resource-group-portal/delete-group.png)

## Aggiungere tag alle risorse

È possibile applicare tag ai gruppi di risorse e alle risorse per organizzare logicamente gli asset. Per informazioni sull'utilizzo dei tag tramite il portale, vedere [Uso dei tag per organizzare le risorse di Azure](../resource-group-using-tags.md).

## Distribuire un modello salvato

Se è stato salvato un modello nell'account, sarà possibile visualizzarlo in seguito selezionando **Sfoglia** e **Modelli**.

![esplorare i modelli](./media/resource-group-portal/browse-templates.png)

Verrà visualizzata la raccolta personalizzata di modelli.

![visualizzare la raccolta di modelli](./media/resource-group-portal/show-template-collection.png)


## Distribuire un modello personalizzato

Se si desidera eseguire una distribuzione ma non usare i modelli in Marketplace, è possibile creare un modello personalizzato che definisce l'infrastruttura per la soluzione. Per altre informazioni sui modelli, vedere [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md).

Per distribuire un modello personalizzato tramite il portale, selezionare **Nuovo**, cercare **Distribuzione modello** e quindi selezionarlo nelle opzioni disponibili.

![cercare la distribuzione del modello](./media/resource-group-portal/search-template.png)

Selezionare **Distribuzione modello** nelle risorse disponibili.

![selezionare la distribuzione del modello](./media/resource-group-portal/select-template.png)

Dopo aver avviato la distribuzione del modello, è possibile creare il modello personalizzato e impostare i valori per la distribuzione.

![creare un modello](./media/resource-group-portal/show-custom-template.png)

In alternativa, è possibile selezionare un modello preesistente dai [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/). Questi modelli sono forniti dalla community. I modelli sono relativi a molti scenari comuni ed è possibile che qualcuno abbia aggiunto un modello simile a quello che si sta provando a distribuire. È possibile eseguire ricerche nei modelli per trovare aspetti corrispondenti al proprio scenario.

![selezionare un modello di Guida introduttiva](./media/resource-group-portal/select-quickstart-template.png)

Dopo la selezione, il modello viene caricato nell'editor.

## Visualizzare la sottoscrizione e i costi

È possibile visualizzare informazioni sulla sottoscrizione e un riepilogo dei costi per tutte le risorse. Selezionare **Sottoscrizioni** e quindi la sottoscrizione da visualizzare. Potrebbe essere disponibile una sola sottoscrizione da selezionare.

![subscription](./media/resource-group-portal/select-subscription.png)

Nel pannello della sottoscrizione viene visualizzata la velocità.

![velocità](./media/resource-group-portal/burn-rate.png)

E una suddivisione dei costi in base al tipo di risorsa.

![costo delle risorse](./media/resource-group-portal/cost-by-resource.png)

## Controllo di accesso per i dashboard di Azure

L'accesso alle informazioni visualizzate nella maggior parte dei riquadri del portale è regolato dal [controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-configure.md) di Azure. Per integrare facilmente i dashboard nell'ecosistema, tutti quelli pubblicati vengono implementati come risorse di Azure. Da un punto di vista del controllo di accesso, i dashboard non sono diversi da una macchina virtuale o da un account di archiviazione.

Di seguito è fornito un esempio. Si supponga di avere una sottoscrizione di Azure e che a diversi membri del team siano stati assegnati i ruoli di **proprietario**, **collaboratore** o **lettore** della sottoscrizione. Gli utenti con il ruolo di proprietario o collaboratore possono elencare, visualizzare, creare, modificare o eliminare dashboard nella sottoscrizione. Gli utenti con il ruolo di lettore possono elencare e visualizzare i dashboard, ma non modificarli o eliminarli. Gli utenti con accesso in lettura possono apportare modifiche locali a un dashboard pubblicato, ad esempio per risolvere un problema, ma non pubblicarle nel server. Hanno comunque la possibilità di creare una copia privata del dashboard per se stessi.

Si noti che i singoli riquadri del dashboard applicano requisiti di controllo di accesso personalizzati in base alle risorse per cui visualizzano i dati. Ciò significa che è possibile progettare un dashboard più ampiamente condivisibile, continuando comunque a proteggere i dati nei singoli riquadri.

## Passaggi successivi

- Per visualizzare i log di controllo vedere [Operazioni di controllo con Azure Resource Manager](../resource-group-audit.md).
- Per risolvere gli errori di distribuzione vedere [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse con il portale di Azure](../resource-manager-troubleshoot-deployments-portal.md).

<!---HONumber=AcomDC_0518_2016-->