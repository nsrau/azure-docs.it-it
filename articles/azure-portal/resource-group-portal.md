<properties 
	pageTitle="Uso del portale di Azure per gestire le risorse di Azure | Microsoft Azure" 
	description="Utilizzare il portale di Azure e Azure Resource Manager per gestire le risorse. Illustra come usare i dashboard e i riquadri per monitorare le risorse." 
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
	ms.date="06/10/2016" 
	ms.author="tomfitz"/>


# Gestire le risorse di Azure mediante il portale

> [AZURE.SELECTOR]
- [Portale](azure-portal/resource-group-portal.md)
- [Interfaccia della riga di comando di Azure](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group/)
- [Nodo](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)
- [Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)
- [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

Questo argomento illustra come usare il [portale di Azure](https://portal.azure.com) con [Azure Resource Manager](../resource-group-overview.md) per gestire le risorse di Azure. Per informazioni sulla distribuzione delle risorse mediante il portale, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e il portale di Azure](../resource-group-template-deploy-portal.md).

Non tutti i servizi attualmente supportano il portale o Gestione risorse. Per questi servizi, sarà necessario usare il [portale classico](https://manage.windowsazure.com). Per lo stato di ogni servizio, vedere il [Grafico della disponibilità dei portali di Azure](https://azure.microsoft.com/features/azure-portal/availability/).

<a id="access-control-for-azure-dashboards" />
## Personalizzare i dashboard per monitorare le risorse

Il portale fornisce un dashboard che è possibile usare per il monitoraggio e la gestione delle risorse. Il dashboard è completamente personalizzabile ed è possibile creare più dashboard per fornire facilmente visualizzazioni diverse della sottoscrizione.

![dashboard](./media/resource-group-portal/dashboard.png)

> [AZURE.TIP] Il modo migliore per acquisire familiarità con i dashboard è guardare il video sulla [creazione di dashboard personalizzati nel portale di Microsoft Azure](https://channel9.msdn.com/Blogs/trevor-cloud/azure-portal-dashboards).

### Condivisione dei dashboard di Azure e controllo di accesso
Dopo aver configurato un dashboard, è possibile pubblicarlo e condividerlo con altri utenti nell'organizzazione. Il [Controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-configure.md) di Azure governa l'accesso alle informazioni visualizzate nei riquadri nel portale. Tutti i dashboard pubblicati sono implementati come risorse di Azure. Dal punto di vista del controllo di accesso, i dashboard non sono diversi da una macchina virtuale o da un account di archiviazione.

Di seguito è fornito un esempio. Si supponga di avere una sottoscrizione di Azure e che a diversi membri del team siano stati assegnati i ruoli di **proprietario**, **collaboratore** o **lettore** della sottoscrizione. Gli utenti con il ruolo di proprietario o collaboratore possono elencare, visualizzare, creare, modificare o eliminare dashboard nella sottoscrizione. Gli utenti con il ruolo di lettore possono elencare e visualizzare i dashboard, ma non modificarli o eliminarli. Gli utenti con accesso in lettura possono apportare modifiche locali a un dashboard pubblicato, ad esempio per risolvere un problema, ma non pubblicarle nel server. Hanno comunque la possibilità di creare una copia privata del dashboard per se stessi.

I singoli riquadri del dashboard applicano requisiti di controllo di accesso personalizzati in base alle risorse che visualizzano. Perciò è possibile progettare un dashboard condiviso più ampiamente, continuando comunque a proteggere i dati nei singoli riquadri.

## Gestire gruppi di risorse

1. Per visualizzare tutti i gruppi di risorse della sottoscrizione, selezionare **Gruppi di risorse**.

    ![esplorare i gruppi di risorse](./media/resource-group-portal/browse-groups.png)

2. Selezionare il gruppo di risorse specifico che si desidera gestire. Viene visualizzato un pannello del gruppo di risorse che fornisce informazioni su tale gruppo, incluso un elenco di tutte le risorse nel gruppo.

    ![riepilogo del gruppo di risorse](./media/resource-group-portal/group-summary.png)

    Selezionando una risorsa vengono visualizzate informazioni dettagliate su tale risorsa.

3. Nel pannello di una risorsa è possibile aggiungere più tabelle e grafici selezionando **Aggiungi una sezione** sotto il riepilogo.

    ![aggiungere una sezione](./media/resource-group-portal/add-section.png)

4. Viene visualizzata una raccolta di riquadri che consente di selezionare le informazioni da includere nel pannello. I tipi di riquadri visualizzati sono filtrati in base al tipo di risorsa. Se si seleziona una risorsa diversa, i riquadri disponibili cambiano.

    ![aggiungere una sezione](./media/resource-group-portal/tile-gallery.png)

5. Trascinare il riquadro necessario negli spazi disponibili.

    ![trascinare un riquadro](./media/resource-group-portal/drag-tile.png)

6. Dopo aver selezionato **Fatto** nella parte superiore del portale la nuova vista farà parte del pannello.

    ![mostrare un riquadro](./media/resource-group-portal/show-lens.png)

7. Per accedere rapidamente al gruppo di risorse, è possibile aggiungere il pannello al dashboard.

    ![aggiungere un gruppo di risorse](./media/resource-group-portal/pin-group.png)

    In alternativa, è possibile aggiungere una sezione del pannello al dashboard selezionando i puntini di sospensione (...) sopra la sezione. È inoltre possibile personalizzare le dimensioni della sezione nel pannello o rimuoverla completamente. L'immagine seguente illustra come aggiungere, personalizzare o rimuovere la sezione relativa a CPU e memoria.

    ![sezione di aggiunta](./media/resource-group-portal/pin-cpu-section.png)

8. Dopo avere aggiunto la sezione al dashboard, ne verrà visualizzato il riepilogo.

    ![visualizzare il dashboard](./media/resource-group-portal/view-startboard.png)

Selezionandola si potranno vedere altri dettagli sui dati.

## Aggiungere tag alle risorse

È possibile applicare tag ai gruppi di risorse e alle risorse per organizzare logicamente gli asset. Per informazioni sull'utilizzo dei tag tramite il portale, vedere [Uso dei tag per organizzare le risorse di Azure](../resource-group-using-tags.md).

## Visualizzare la sottoscrizione e i costi

È possibile visualizzare informazioni sulla sottoscrizione e un riepilogo dei costi per tutte le risorse. Selezionare **Sottoscrizioni** e quindi la sottoscrizione da visualizzare. Potrebbe essere disponibile una sola sottoscrizione da selezionare.

![subscription](./media/resource-group-portal/select-subscription.png)

Nel pannello della sottoscrizione viene visualizzata la velocità.

![velocità](./media/resource-group-portal/burn-rate.png)

E una suddivisione dei costi in base al tipo di risorsa.

![costo delle risorse](./media/resource-group-portal/cost-by-resource.png)

## Esportare il modello

Dopo avere configurato il gruppo di risorse, è possibile che si voglia visualizzare il modello di Azure Resource Manager per il gruppo di risorse. L'esportazione del modello offre due vantaggi:

1. È possibile automatizzare le distribuzioni future della soluzione, perché tutti gli elementi dell'infrastruttura sono definiti nel modello.

2. È possibile acquisire familiarità con la sintassi del modello esaminando il codice JSON (JavaScript Object Notation) che rappresenta la soluzione.

Per istruzioni dettagliate, vedere [Esportare un modello di Azure Resource Manager da risorse esistenti](../resource-manager-export-template.md).

## Eliminare risorse o gruppi di risorse

Se si elimina un gruppo di risorse, vengono eliminate tutte le risorse contenute in esso. È anche possibile eliminare singole risorse all'interno di un gruppo. Prestare attenzione quando si elimina un gruppo di risorse, perché è possibile che altri gruppi di risorse includano risorse collegate ad esso. Le risorse collegate non verranno eliminate, ma è possibile che non funzionino come previsto.

![eliminare un gruppo](./media/resource-group-portal/delete-group.png)


## Passaggi successivi

- Per visualizzare i log di controllo, vedere [Operazioni di controllo con Resource Manager](../resource-group-audit.md).
- Per risolvere gli errori di distribuzione, vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](../resource-manager-troubleshoot-deployments-portal.md).
- Per la distribuzione di risorse mediante il portale, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e il portale di Azure](../resource-group-template-deploy-portal.md).

<!---HONumber=AcomDC_0706_2016-->