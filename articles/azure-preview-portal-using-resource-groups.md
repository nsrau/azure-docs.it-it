<properties 
	pageTitle="Uso dei gruppi di risorse per la gestione delle risorse di Azure" 
	description="Informazioni su come raggruppare più risorse in un gruppo logico che diventa il limite del ciclo di vita per le risorse in esso contenute." 
	services="multiple" 
	documentationCenter="" 
	authors="" 
	writer="tomfitz" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/09/2015" 
	ms.author="tomfitz"/>


# Uso dei gruppi di risorse per la gestione delle risorse di Azure

### Introduzione

In precedenza, per gestire risorse in Microsoft Azure, ovvero entità gestite dall'utente come un server di database, un database o un sito Web, era necessario eseguire operazioni su una risorsa alla volta. La gestione di applicazioni complesse costituite da più risorse era quindi un'attività complicata. Nel portale di anteprima di Microsoft Azure è invece possibile creare gruppi di risorse per gestire tutte le risorse di un'applicazione contemporaneamente. Il gruppo di risorse è un nuovo concetto di Azure che funge da limite del ciclo di vita per ogni risorsa contenuta al suo interno. 
<br><br />

I gruppi di risorse consentono di gestire tutte le risorse di un'applicazione contemporaneamente. Sono resi disponibili dalla nuova funzionalità di gestione, ovvero Gestione risorse di Azure. Gestione risorse di Azure consente di raggruppare più risorse in un gruppo logico che funge da limite del ciclo di vita per ogni risorsa contenuta al suo interno. In genere, un gruppo conterrà risorse correlate a una specifica applicazione. Può ad esempio contenere una risorsa sito Web che ospita il sito Web pubblico dell'organizzazione, un database SQL in cui sono archiviati i dati relazionali usati dal sito e un account di archiviazione per le risorse non relazionali. 

Di seguito è riportata una panoramica sintetica su come usare i gruppi di risorse nel portale Microsoft Azure Preview. 
<br><br />

### Creazione di gruppi di risorse

Tutte le nuove risorse create nel portale di anteprima vengono sempre inserite all'interno di un gruppo. È possibile scegliere di creare un nuovo gruppo di risorse oppure di usarne uno esistente nel flusso di creazione. <br><br />

![](http://i.imgur.com/USKkQdW.png)

<br><br />
Le nuove applicazioni costituite da alcune risorse correlate, ad esempio sito Web e database, vengono sempre create in uno specifico gruppo di risorse, in modo che sia possibile gestire il ciclo di vita di tutte le risorse correlate tramite il gruppo. Con l'evoluzione dell'applicazione, è possibile aggiungere o rimuovere risorse dal gruppo. 

![](http://i.imgur.com/Me0jbio.png)


<br><br />

### Esplorazione dei gruppi di risorse

Per esplorare i gruppi di risorse, è possibile fare clic sull'indice a sinistra dello schermo. Un gruppo di risorse è associato a un pannello che fornisce tutte le informazioni correlate. Il pannello del gruppo di risorse offre inoltre una vista unificata delle informazioni di fatturazione e monitoraggio relative a tutte le risorse al suo interno.

Nella sezione riepilogativa viene visualizzata una mappa visiva di tutte le risorse del gruppo, oltre alle risorse di altri gruppi collegati a quest'ultimo. Viene inoltre mostrato lo stato di ogni risorsa. 
![](http://i.imgur.com/PhJeLZQ.png)

<br><br />

La parte della mappa di risorse può essere personalizzata per ingrandirne le dimensioni e visualizzare tutte le risorse contenute all'interno del gruppo e in altri gruppi collegati. Questa parte può essere aggiunta e quindi copiata nella schermata iniziale.

![](http://i.imgur.com/5Wqv2XR.png)

<br><br />

  Fare clic sulla mappa di risorse per aprire la visualizzazione elenco di tutte le risorse presenti. In questa visualizzazione sono elencate tutte le risorse interne o collegate al gruppo. Fare clic su queste risorse per avviare i relativi blade. 

![](http://i.imgur.com/COPjNng.png)




<br><br />

### Aggiunta di risorse ai gruppi

È possibile aggiungere risorse a un gruppo usando il comando "Add" nel pannello corrispondente. Di seguito sono riportati i passaggi del flusso che consentono di aggiungere altre risorse al gruppo.

![](http://i.imgur.com/G79kayH.png)

Nota: non è consigliabile inserire il progetto team nello stesso gruppo di risorse di altre risorse di Azure. Se si crea un progetto team in un nuovo account e in un nuovo gruppo, quindi si crea un sito Web, per impostazione predefinita il gruppo del sito sarà l'ultimo usato (gruppo VSO) e alla fine le risorse di runtime/sviluppo si troveranno nello stesso gruppo. 



<br><br />

### Eliminazione di gruppi di risorse

Poiché i gruppi di risorse consentono di gestire il ciclo di vita di tutte le risorse contenute, l'eliminazione di un gruppo comporterà la rimozione di tutte le risorse al suo interno. È anche possibile eliminare singole risorse all'interno di un gruppo. Prestare attenzione quando si elimina un gruppo di risorse, in quanto potrebbe essere collegato ad altre risorse. È possibile visualizzare le risorse collegate nella mappa e quindi eseguire i passaggi necessari per evitare conseguenze involontarie quando si eliminano i gruppi. 

![](http://i.imgur.com/ZTXoISb.png)

<!--HONumber=35.2-->

<!--HONumber=46--> 
