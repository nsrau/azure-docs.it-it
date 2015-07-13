<properties 
	pageTitle="Uso del portale di anteprima di Azure per gestire le risorse di Azure" 
	description="Informazioni su come raggruppare più risorse in un gruppo logico che diventa il limite del ciclo di vita per le risorse in esso contenute." 
	services="azure-portal" 
	documentationCenter="" 
	authors="" 
	writer="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-portal" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2015" 
	ms.author="tomfitz"/>


# Uso del portale di anteprima di Azure per gestire le risorse di Azure

## Introduzione

In precedenza, per gestire risorse in Microsoft Azure, ovvero entità gestite dall'utente come un server di database, un database o un sito Web, era necessario eseguire operazioni su una risorsa alla volta. La gestione di applicazioni complesse costituite da più risorse era quindi un'attività complicata. Nel portale di anteprima di Azure è possibile creare gruppi di risorse per gestire tutte le risorse di un'applicazione contemporaneamente. Il gruppo di risorse è un nuovo concetto di Azure che funge da limite del ciclo di vita per ogni risorsa contenuta al suo interno.

I gruppi di risorse consentono di gestire tutte le risorse di un'applicazione contemporaneamente. Sono resi disponibili dalla nuova funzionalità di gestione, ovvero Gestione risorse di Azure. Gestione risorse di Azure consente di raggruppare più risorse in un gruppo logico che funge da limite del ciclo di vita per ogni risorsa contenuta al suo interno. In genere, un gruppo conterrà risorse correlate a una specifica applicazione. Può ad esempio contenere una risorsa sito Web che ospita il sito Web pubblico dell'organizzazione, un database SQL in cui sono archiviati i dati relazionali usati dal sito e un account di archiviazione per le risorse non relazionali.

Di seguito è riportata una panoramica sintetica su come usare i gruppi di risorse nel portale di Azure.

## Creazione di gruppi di risorse

Tutte le nuove risorse create nel portale vengono sempre inserite all'interno di un gruppo. È possibile scegliere di creare un nuovo gruppo di risorse oppure di usarne uno esistente nel flusso di creazione. <br><br />

![crea gruppo di risorse](./media/resource-group-portal/1_createWebsite.png)

Le nuove applicazioni costituite da alcune risorse correlate, ad esempio sito Web e database, vengono sempre create in uno specifico gruppo di risorse, in modo che sia possibile gestire il ciclo di vita di tutte le risorse correlate tramite il gruppo. Con l'evoluzione dell'applicazione, è possibile aggiungere o rimuovere risorse dal gruppo.

![crea gruppo di risorse](./media/resource-group-portal/2_createWSandDB.png)

## Esplorazione dei gruppi di risorse

Per esplorare i gruppi di risorse, è possibile fare clic sull'indice a sinistra dello schermo. Un gruppo di risorse è associato a un pannello che fornisce tutte le informazioni correlate. Il pannello del gruppo di risorse offre inoltre una vista unificata delle informazioni di fatturazione e monitoraggio relative a tutte le risorse al suo interno.

Nella sezione riepilogativa viene visualizzata una mappa visiva di tutte le risorse del gruppo, oltre alle risorse di altri gruppi collegati a quest'ultimo. Viene inoltre mostrato lo stato di ogni risorsa. ![riepilogo gruppo di risorse](./media/resource-group-portal/3_1BrowseRGs.png)

La parte della mappa di risorse può essere personalizzata per ingrandirne le dimensioni e visualizzare tutte le risorse contenute all'interno del gruppo e in altri gruppi collegati. Questa parte può essere aggiunta e quindi copiata nella schermata iniziale.

![pin](./media/resource-group-portal/3_2BrowseRGs.png)

Fare clic sulla mappa di risorse per aprire la visualizzazione elenco di tutte le risorse presenti. In questa visualizzazione sono elencate tutte le risorse interne o collegate al gruppo. Fare clic su queste risorse per avviare i relativi pannelli.

![visualizza risorse](./media/resource-group-portal/3_3BrowseRGs.png)

## Aggiunta di risorse ai gruppi

È possibile aggiungere risorse a un gruppo usando il comando **Add** nel pannello corrispondente. Di seguito sono riportati i passaggi del flusso che consentono di aggiungere altre risorse al gruppo.

![aggiungi risorsa](./media/resource-group-portal/4_AddResource.png)

Nota: non è consigliabile inserire il progetto team nello stesso gruppo di risorse di altre risorse di Azure. Se si crea un progetto team in un nuovo account e in un nuovo gruppo e quindi si crea un sito Web, per impostazione predefinita il gruppo del sito sarà l'ultimo usato (gruppo VSO) e alla fine le risorse di runtime/sviluppo si troveranno nello stesso gruppo.

## Eliminazione di gruppi di risorse

Poiché i gruppi di risorse consentono di gestire il ciclo di vita di tutte le risorse contenute, l'eliminazione di un gruppo comporterà la rimozione di tutte le risorse al suo interno. È anche possibile eliminare singole risorse all'interno di un gruppo. Prestare attenzione quando si elimina un gruppo di risorse, in quanto potrebbe essere collegato ad altre risorse. È possibile visualizzare le risorse collegate nella mappa e quindi eseguire i passaggi necessari per evitare conseguenze involontarie quando si eliminano i gruppi.

## Passaggi successivi
Introduzione

- [Panoramica di Gestione risorse di Azure](../resource-group-overview.md)  
- [Uso di Azure PowerShell con Gestione risorse di Azure](../powershell-azure-resource-manager.md)
- [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione delle risorse di Azure](../xplat-cli-azure-resource-manager.md)  
  
Creazione e distribuzione delle applicazioni
  
- [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md)  
- [Distribuire un'applicazione con un modello di Gestione risorse di Azure](resource-group-template-deploy.md)
- [Distribuire un'applicazione complessa in modo prevedibile in Azure](../app-service-web/app-service-deploy-complex-application-predictably.md)
- [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse in Azure](../resource-group-deploy-debug.md)  
- [Funzioni del modello di Gestione risorse di Azure](../resource-group-template-functions.md)  
- [Operazioni avanzate con i modelli](../resource-group-advanced-template.md)  
- [Distribuire le risorse di Azure usando le librerie .NET e un modello](../arm-template-deployment.md)
  
Organizzazione delle risorse
  
- [Uso dei tag per organizzare le risorse di Azure](../resource-group-using-tags.md)  
  
Gestione e controllo dell'accesso
  
- [Gestione e controllo dell'accesso alle risorse](resource-group-rbac.md)  
- [Autenticazione di un'entità servizio con Gestione risorse di Azure](../resource-group-authenticate-service-principal.md)  
- [Creare una nuova entità servizio di Azure usando il portale di Azure classico](../resource-group-create-service-principal-portal.md)  
  


 

<!---HONumber=62-->