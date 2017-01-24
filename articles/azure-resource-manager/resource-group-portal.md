---
title: Gestire le risorse di Azure tramite il portale di Azure | Microsoft Docs
description: Utilizzare il portale di Azure e Azure Resource Manager per gestire le risorse. Illustra come usare i dashboard per monitorare le risorse.
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 0725bbf2-5913-4c07-af6e-24e11d957fbc
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: f1955e3e1618c6228bfa5439ca66155148ff1cb3
ms.openlocfilehash: 3eb3ea76fa577dfcc360e209a645b45e0cb4ab34


---
# <a name="manage-azure-resources-through-portal"></a>Gestire le risorse di Azure mediante il portale
> [!div class="op_single_selector"]
> * [Azure PowerShell](powershell-azure-resource-manager.md)
> * [Interfaccia della riga di comando di Azure](xplat-cli-azure-resource-manager.md)
> * [Portale](resource-group-portal.md) 
> * [API REST](resource-manager-rest-api.md)
> 
> 

Questo argomento illustra come usare il [portale di Azure](https://portal.azure.com) con [Azure Resource Manager](resource-group-overview.md) per gestire le risorse di Azure. Per informazioni sulla distribuzione delle risorse tramite il portale, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e il portale di Azure](resource-group-template-deploy-portal.md).

Non tutti i servizi attualmente supportano il portale o Gestione risorse. Per questi servizi sarà necessario usare il [portale classico](https://manage.windowsazure.com). Per lo stato di ogni servizio, vedere il [grafico della disponibilità del portale di Azure](https://azure.microsoft.com/features/azure-portal/availability/).

## <a name="manage-resource-groups"></a>Gestire gruppi di risorse

Un gruppo di risorse è un contenitore con risorse correlate per una soluzione Azure. Il gruppo di risorse può includere tutte le risorse della soluzione o solo le risorse da gestire come gruppo. L'utente decide come allocare le risorse ai gruppi di risorse nel modo più appropriato per l'organizzazione. È in genere consigliabile aggiungere risorse che condividono lo stesso ciclo di vita allo stesso gruppo di risorse per poterle distribuire, aggiornare ed eliminare facilmente come gruppo. 

Il gruppo di risorse archivia i metadati delle risorse. Quando si specifica un percorso per il gruppo di risorse, si specifica il percorso di archiviazione dei metadati. Per motivi di conformità potrebbe essere necessario assicurarsi che i dati siano archiviati in una determinata area.

1. Per visualizzare tutti i gruppi di risorse della sottoscrizione, selezionare **Gruppi di risorse**.
   
    ![esplorare i gruppi di risorse](./media/resource-group-portal/browse-groups.png)
2. Per creare un gruppo di risorse vuoto, selezionare **Aggiungi**.
   
    ![aggiungere un gruppo di risorse](./media/resource-group-portal/add-resource-group.png)
3. Specificare il nome e il percorso del nuovo gruppo di risorse. Selezionare **Create**.
   
    ![creare un gruppo di risorse](./media/resource-group-portal/create-empty-group.png)
4. Potrebbe essere necessario selezionare **Aggiorna** per visualizzare il gruppo di risorse creato di recente.
   
    ![aggiornare un gruppo di risorse](./media/resource-group-portal/refresh-resource-groups.png)
5. Per personalizzare le informazioni visualizzate per i gruppi di risorse, selezionare **Colonne**.
   
    ![personalizzare colonne](./media/resource-group-portal/select-columns.png)
6. Selezionare le colonne da aggiungere, quindi selezionare **Aggiorna**.
   
    ![aggiungere colonne](./media/resource-group-portal/add-columns.png)
7. Per informazioni sulla distribuzione delle risorse nel nuovo gruppo di risorse, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e il portale di Azure](resource-group-template-deploy-portal.md).
8. Per accedere rapidamente al gruppo di risorse, è possibile aggiungere il pannello al dashboard.
   
    ![aggiungere un gruppo di risorse](./media/resource-group-portal/pin-group.png)
9. Il dashboard visualizza il gruppo di risorse e le relative risorse. È possibile selezionare i gruppi di risorse o le relative risorse per passare all'elemento.
   
    ![aggiungere un gruppo di risorse](./media/resource-group-portal/show-resource-group-dashboard.png)

## <a name="tag-resources"></a>Aggiungere tag alle risorse
È possibile applicare tag ai gruppi di risorse e alle risorse per organizzare logicamente gli asset. Per informazioni sull'uso dei tag, vedere [Uso dei tag per organizzare le risorse di Azure](resource-group-using-tags.md).

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="monitor-resources"></a>Monitorare le risorse
Quando si seleziona una risorsa, il pannello della risorsa visualizza grafici e tabelle predefiniti per il monitoraggio di quel tipo di risorsa.

1. Selezionare una risorsa ed esaminare la sezione **Monitoraggio**, che include i grafici relativi al tipo di risorsa. L'immagine seguente illustra i dati di monitoraggio predefiniti per un account di archiviazione.
   
    ![visualizzare il monitoraggio](./media/resource-group-portal/show-monitoring.png)
2. È possibile aggiungere una sezione del pannello al dashboard selezionando i puntini di sospensione (...) sopra la sezione. È inoltre possibile personalizzare le dimensioni della sezione nel pannello o rimuoverla completamente. L'immagine seguente illustra come aggiungere, personalizzare o rimuovere la sezione relativa a CPU e memoria.
   
    ![sezione di aggiunta](./media/resource-group-portal/pin-cpu-section.png)
3. Dopo avere aggiunto la sezione al dashboard, ne verrà visualizzato il riepilogo. Selezionandola si potranno vedere altri dettagli sui dati.
   
    ![visualizzare il dashboard](./media/resource-group-portal/view-startboard.png)
4. Per personalizzare completamente i dati monitorati dal portale, passare al dashboard predefinito e selezionare **Nuovo dashboard**.
   
    ![dashboard](./media/resource-group-portal/dashboard.png)
5. Assegnare un nome al nuovo dashboard e trascinare i riquadri sul dashboard. I riquadri vengono filtrati in base a opzioni diverse.
   
    ![dashboard](./media/resource-group-portal/create-dashboard.png)
   
     Per informazioni sull'uso dei dashboard, vedere [Creating and sharing dashboards in the Azure portal](../azure-portal/azure-portal-dashboards.md)(Creazione e condivisione di dashboard nel portale di Azure).

## <a name="manage-resources"></a>Gestire risorse
Nel pannello di una risorsa sono visibili le opzioni per la gestione delle risorse. Il portale presenta le opzioni di gestione per quel particolare tipo di risorsa. I comandi per la gestione vengono visualizzati nella parte superiore del pannello della risorsa e sul lato sinistro.

![Gestire risorse](./media/resource-group-portal/manage-resources.png)

Con queste opzioni, è possibile eseguire operazioni come l'avvio e l'arresto di una macchina virtuale o la riconfigurazione delle proprietà della macchina virtuale.

## <a name="move-resources"></a>Spostare le risorse
Per spostare una risorsa in un altro gruppo di risorse o in un'altra sottoscrizione, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](resource-group-move-resources.md).

## <a name="lock-resources"></a>Bloccare le risorse
È possibile bloccare una sottoscrizione, una risorsa o un gruppo di risorse per impedire che altri utenti nell'organizzazione modifichino o eliminino accidentalmente risorse strategiche. Per altre informazioni, vedere [Bloccare le risorse con Gestione risorse di Azure](resource-group-lock-resources.md).

[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="view-your-subscription-and-costs"></a>Visualizzare la sottoscrizione e i costi
È possibile visualizzare informazioni sulla sottoscrizione e un riepilogo dei costi per tutte le risorse. Selezionare **Sottoscrizioni** e quindi la sottoscrizione da visualizzare. Potrebbe essere disponibile una sola sottoscrizione da selezionare.

![sottoscrizione](./media/resource-group-portal/select-subscription.png)

Nel pannello della sottoscrizione viene visualizzata la velocità.

![velocità](./media/resource-group-portal/burn-rate.png)

E una suddivisione dei costi in base al tipo di risorsa.

![costo delle risorse](./media/resource-group-portal/cost-by-resource.png)

## <a name="export-template"></a>Esportare il modello
Dopo avere configurato il gruppo di risorse, è possibile che si voglia visualizzare il modello di Azure Resource Manager per il gruppo di risorse. L'esportazione del modello offre due vantaggi:

1. È possibile automatizzare le distribuzioni future della soluzione perché il modello contiene l'infrastruttura completa.
2. È possibile acquisire familiarità con la sintassi del modello esaminando il codice JSON (JavaScript Object Notation) che rappresenta la soluzione.

Per istruzioni dettagliate, vedere [Esportare un modello di Azure Resource Manager da risorse esistenti](resource-manager-export-template.md).

## <a name="delete-resource-group-or-resources"></a>Eliminare risorse o gruppi di risorse
Se si elimina un gruppo di risorse, vengono eliminate tutte le risorse contenute in esso. È anche possibile eliminare singole risorse all'interno di un gruppo. Prestare attenzione quando si elimina un gruppo di risorse perché è possibile che altri gruppi di risorse includano risorse collegate ad esso. Resource Manager non elimina le risorse collegate, ma le risorse potrebbero non funzionare correttamente senza le risorse necessarie.

![eliminare un gruppo](./media/resource-group-portal/delete-group.png)

## <a name="next-steps"></a>Passaggi successivi
* Per visualizzare i log attività, vedere [Operazioni di controllo con Resource Manager](resource-group-audit.md).
* Per risolvere gli errori di distribuzione, vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](resource-manager-troubleshoot-deployments-portal.md).
* Per la distribuzione di risorse tramite il portale, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e il portale di Azure](resource-group-template-deploy-portal.md).
* Per gestire l'accesso alle risorse, vedere [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../active-directory/role-based-access-control-configure.md).
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).




<!--HONumber=Dec16_HO3-->


