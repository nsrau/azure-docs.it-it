<properties
   pageTitle="Gestione di contenitori ACS tramite l'interfaccia utente Web"
   description="Distribuire contenitori in un cluster del servizio contenitore di Azure usando l'interfaccia utente Web di Marathon."
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Contenitori, Micro-servizi, Mesos, Azure"/>
   
<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="nepeters"/>
   
# Gestione di contenitori tramite l'interfaccia utente Web
   
Mesos offre un ambiente di distribuzione e ridimensionamento del carico di lavoro cluster con l'astrazione dell'hardware sottostante. Su Mesos, un framework gestisce la pianificazione e l'esecuzione del carico di lavoro di calcolo. Sono disponibili framework per molti dei carichi di lavoro più comuni. Questo documento fornisce informazioni dettagliate sulla creazione e il ridimensionamento della distribuzione di contenitori con Marathon. Prima di eseguire questi esempi, è necessario avere a disposizione un cluster Mesos configurato in ACS e la connettività remota al cluster. Per altre informazioni su questi elementi, vedere gli articoli indicati di seguito.

- [Distribuire un cluster del servizio contenitore di Azure](./container-service-deployment.md) 
- [Connessione a un cluster ACS](./container-service-connect.md)

## Esplorare l'interfaccia utente di Mesos

Dopo aver stabilito un tunnel SSH, passare a http://localhost/Mesos. Verrà caricata l'interfaccia utente Web di Mesos. Nella pagina è possibile raccogliere informazioni sul cluster Mesos, ad esempio gli agenti attivati, lo stato delle attività e la disponibilità delle risorse.

![Creare una distribuzione](media/ui1.png)

## Esplorare l'interfaccia utente di Marathon

Per visualizzare l'interfaccia utente di Marathon, passare a http://localhost/Marathon. In questa schermata è possibile avviare un nuovo contenitore o un'altra applicazione nel cluster Mesos ACS, nonché visualizzare le informazioni sull'applicazione e i contenitori in esecuzione.

![Creare una distribuzione](media/ui2.png)

## Distribuire un contenitore Docker formattato

Per usare Marathon per l'avvio di un nuovo contenitore nel cluster Mesos, fare clic sul pulsante `Create Application`. Il modulo New Application viene usato per definire i parametri del contenitore o dell'applicazione. In questo esempio verrà distribuito un semplice contenitore nginx. Immettere le seguenti informazioni. Al termine fare clic su Create.
 
Campo | Valore
----------------|-----------
ID | nginx
Image | nginx
Network | Bridged
Container Port | 80
Host Port | 80
Protocol | TCP

![Creare una distribuzione](media/ui3.png)

Tornare alla pagina principale di Marathon, dove ora viene visualizzato lo stato di distribuzione del contenitore.

![Creare una distribuzione](media/ui4.png)

Se si passa nuovamente all'app Mesos (http://localhost/Mesos) è possibile notare un'attività, in questo caso un contenitore Docker formattato, in esecuzione nel cluster Mesos. Viene anche visualizzato il nodo del cluster in cui viene eseguita l'attività.

![Creare una distribuzione](media/ui5.png)

## Ridimensionare i contenitori

L'interfaccia utente Web di Marathon consente anche di ridimensionare il numero di istanze di un contenitore. A tale scopo, passare alla pagina di Marathon, selezionare il contenitore da ridimensionare e quindi fare clic sul pulsante `scale`. Nella finestra Scale Application immettere il numero di istanze del contenitore previste e selezionare `Scale Application`.

![Creare una distribuzione](media/ui6.png)

Al termine dell'operazione di ridimensionamento verranno visualizzate più istanze della stessa attività distribuite tra gli agenti di Mesos.

![Creare una distribuzione](media/ui8.png)

<!---HONumber=AcomDC_0323_2016-->