<properties 
	pageTitle="Uso del portale di Azure per gestire le risorse di Azure | Microsoft Azure" 
	description="Informazioni su come raggruppare più risorse in un gruppo logico che diventa il limite del ciclo di vita per le risorse in esso contenute." 
	services="azure-resource-manager,azure-portal" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="12/29/2015" 
	ms.author="tomfitz"/>


# Uso del portale di Azure per gestire le risorse di Azure

## Introduzione

In precedenza, per gestire una risorsa in Microsoft Azure, ad esempio un server di database, un database o un'app Web, era necessario eseguire operazioni su una risorsa alla volta. Se si disponeva di un'applicazione complessa costituita da più risorse, era necessario coordinare manualmente le modifiche all'infrastruttura dell'applicazione. Nel portale di Azure è possibile utilizzare Gestione risorse di Azure per creare gruppi di risorse per distribuire e gestire tutte le risorse di un'applicazione contemporaneamente.

In genere, un gruppo di risorse contiene risorse correlate a una specifica applicazione. Può ad esempio contenere un'app Web che ospita il sito Web pubblico dell'organizzazione, un database SQL in cui sono archiviati i dati relazionali usati dal sito e un account di archiviazione per le risorse non relazionali. Ogni risorsa in un gruppo di risorse deve condividere lo stesso ciclo di vita. Per altre informazioni su Gestione risorse, vedere la [panoramica di Gestione risorse](../resource-group-overview.md).

Questo argomento fornisce una panoramica su come usare i gruppi di risorse nel portale di Azure. Non tutti i servizi attualmente supportano il portale o Gestione risorse. Per questi servizi, sarà necessario usare il [portale classico](https://manage.windowsazure.com). Per lo stato di ogni servizio, vedere il [grafico di disponibilità del portale di Azure](https://azure.microsoft.com/features/azure-portal/availability/).

## Creare risorse e un gruppo di risorse

Se è necessario creare un gruppo di risorse vuoto, è possibile selezionare **Nuovo**, **Gestione** e **Gruppo di risorse**.

![creare un gruppo di risorse vuoto](./media/resource-group-portal/create-empty-group.png)

Assegnare un nome e un percorso e, se necessario, selezionare una sottoscrizione.

![impostare i valori del gruppo](./media/resource-group-portal/set-group-properties.png)

Tuttavia, non è necessario creare in modo esplicito un gruppo di risorse vuoto. Quando si crea una nuova risorsa, è possibile scegliere di creare un nuovo gruppo di risorse oppure di usarne uno esistente. L'immagine seguente illustra come creare una nuova app Web con l'opzione di selezione di un gruppo di risorse esistente o di creazione di un nuovo gruppo.

![creare un gruppo di risorse](./media/resource-group-portal/select-existing-group.png)

## Esplorare i gruppi di risorse

È possibile esplorare tutti i gruppi di risorse selezionando **Esplora tutto** e **Gruppi di risorse**.

![esplorare i gruppi di risorse](./media/resource-group-portal/browse-groups.png)

Quando si seleziona un gruppo di risorse specifico, viene visualizzato un pannello del gruppo di risorse che fornisce informazioni su tale gruppo, incluso un elenco di tutte le risorse nel gruppo.

![riepilogo del gruppo di risorse](./media/resource-group-portal/group-summary.png)

Il pannello del gruppo di risorse offre inoltre una vista unificata delle informazioni di fatturazione e monitoraggio relative a tutte le risorse del gruppo.

![monitoraggio e fatturazione](./media/resource-group-portal/monitoring-billing.png)

## Personalizzazione dell'interfaccia

Per accedere rapidamente al riepilogo del gruppo di risorse, è possibile aggiungere il pannello alla schermata iniziale.

![aggiungere un gruppo di risorse](./media/resource-group-portal/pin-group.png)

In alternativa, è possibile aggiungere una sezione del pannello alla schermata iniziale selezionando i puntini di sospensione (...) sopra la sezione. È inoltre possibile personalizzare le dimensioni della sezione nel pannello o rimuoverla completamente. L'immagine seguente illustra come aggiungere, personalizzare o rimuovere la sezione degli eventi.

![sezione di aggiunta](./media/resource-group-portal/pin-section.png)

Dopo aver aggiunto la sezione degli eventi alla schermata iniziale, è possibile visualizzare un riepilogo degli eventi nella schermata iniziale.

![schermata iniziale degli eventi](./media/resource-group-portal/events-startboard.png)

Se si seleziona tale sezione, è possibile visualizzare altri dettagli sugli eventi.

## Visualizzazione di distribuzioni precedenti

Nel pannello del gruppo di risorse è possibile visualizzare la data e lo stato dell'ultima distribuzione per questo gruppo di risorse. Se si seleziona il collegamento, viene visualizzata la cronologia delle distribuzioni per il gruppo.

![ultima distribuzione](./media/resource-group-portal/last-deployment.png)

Se si seleziona una distribuzione dalla cronologia, vengono visualizzate informazioni dettagliate su tale distribuzione.

![riepilogo della distribuzione](./media/resource-group-portal/deployment-summary.png)

È possibile visualizzare le singole operazioni eseguite durante la distribuzione. L'immagine seguente illustra un'operazione che ha avuto esito positivo e una che non è riuscita.

![dettagli dell'operazione](./media/resource-group-portal/operation-details.png)

Se si seleziona una delle operazioni, vengono visualizzate altre informazioni dettagliate sull'operazione. Ciò può rivelarsi particolarmente utile quando un'operazione ha esito negativo, come illustrato di seguito. Può consentire di risolvere un problema dovuto all'esito negativo di una distribuzione. Nell'immagine seguente è possibile notare che il sito Web non è stato distribuito perché il nome non era univoco.

![messaggio dell'operazione](./media/resource-group-portal/operation-message.png)

## Visualizzazione dei log di controllo

Il log di controllo include non solo le operazioni di distribuzione, ma tutte le operazioni di gestione eseguite sulle risorse nella sottoscrizione. Ad esempio, nei log di controllo è possibile visualizzare quando un utente dell'organizzazione ha arrestato un'app. Per visualizzare i log di controllo, selezionare **Esplora tutto** e **Log di controllo**.

![esplorare i log di controllo](./media/resource-group-portal/browse-audit-logs.png)

Nella sezione delle operazioni è possibile visualizzare le singole operazioni eseguite nella sottoscrizione.

![visualizzare i log di controllo](./media/resource-group-portal/view-audit-log.png)

Selezionando una delle operazioni, è possibile visualizzare maggiori dettagli, tra cui informazioni sull'utente che ha eseguito l'operazione.

Se si seleziona l'opzione **Filtro**, è possibile filtrare gli elementi da visualizzare nel log di controllo.

![filtrare i log](./media/resource-group-portal/filter-logs.png)

È possibile selezionare il tipo di operazioni da visualizzare, quelle appartenenti a un gruppo di risorse o a una risorsa, quelle eseguite in un intervallo di tempo specificato, quelle avviate da un chiamante specifico oppure i livelli dell'operazione.

![opzioni di filtro](./media/resource-group-portal/filter-options.png)

## Aggiunta di risorse ai gruppi

È possibile aggiungere risorse a un gruppo usando il comando **Add** nel pannello corrispondente.

![aggiungere una risorsa](./media/resource-group-portal/add-resource.png)

È possibile selezionare la risorsa desiderata dall'elenco disponibile.

## Eliminazione di gruppi di risorse

Poiché i gruppi di risorse consentono di gestire il ciclo di vita di tutte le risorse contenute, l'eliminazione di un gruppo comporterà la rimozione di tutte le risorse al suo interno. È anche possibile eliminare singole risorse all'interno di un gruppo. Prestare attenzione quando si elimina un gruppo di risorse, in quanto potrebbe essere collegato ad altre risorse. È possibile visualizzare le risorse collegate nella mappa e quindi eseguire i passaggi necessari per evitare conseguenze involontarie quando si eliminano i gruppi. Le risorse collegate non verranno eliminate, ma è possibile che non funzionino come previsto.

![eliminare un gruppo](./media/resource-group-portal/delete-group.png)

## Aggiunta di tag alle risorse

È possibile applicare tag ai gruppi di risorse e alle risorse per organizzare logicamente gli asset. Per informazioni sull'utilizzo dei tag tramite il portale, vedere [Uso dei tag per organizzare le risorse di Azure](../resource-group-using-tags.md).

## Distribuzione di un modello personalizzato

Se si desidera eseguire una distribuzione ma non usare i modelli in Marketplace, è possibile creare un modello personalizzato che definisce l'infrastruttura per la soluzione. Per altre informazioni sui modelli, vedere [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md).

Per distribuire un modello personalizzato tramite il portale, selezionare **Nuovo**, **Marketplace** e **Tutto**.

![individuare la distribuzione del modello](./media/resource-group-portal/launch-template.png)

Cercare la **distribuzione del modello** e selezionarla nell'elenco restituito.

![cercare la distribuzione del modello](./media/resource-group-portal/search-template.png)

Dopo aver avviato la distribuzione del modello, è possibile creare il modello personalizzato e impostare i valori per la distribuzione.

![creare un modello](./media/resource-group-portal/show-custom-template.png)

## Passaggi successivi
Introduzione

- Per un'introduzione ai concetti di Gestione risorse, vedere [Panoramica di Gestione risorse di Azure](../resource-group-overview.md).  
- Per un'introduzione all'uso di Azure PowerShell per la distribuzione delle risorse, vedere [Uso di Azure PowerShell con Gestione risorse di Azure](../powershell-azure-resource-manager.md).
- Per un'introduzione all'uso dell'interfaccia della riga di comando di Azure per la distribuzione delle risorse, vedere [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione risorse di Azure](../xplat-cli-azure-resource-manager.md). 
  


 

<!---HONumber=AcomDC_0211_2016-->