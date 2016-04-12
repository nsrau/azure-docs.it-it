<properties
   pageTitle="Gestione di contenitori del servizio contenitore di Azure tramite l'interfaccia utente Web | Microsoft Azure"
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

Mesos offre un ambiente di distribuzione e ridimensionamento dei carichi di lavoro cluster con l'astrazione dell'hardware sottostante. Su Mesos è disponibile un framework che gestisce la pianificazione e l'esecuzione dei carichi di lavoro di calcolo.

Sono disponibili framework per molti dei carichi di lavoro più comuni. Questo documento illustra come creare e ridimensionare la distribuzione di contenitori con Marathon. Prima di eseguire questi esempi, è necessario avere a disposizione un cluster Mesos configurato nel servizio contenitore di Azure. È necessaria anche la connettività remota a questo cluster. Per altre informazioni su questi elementi, vedere gli articoli indicati di seguito.

- [Distribuire un cluster del servizio contenitore di Azure](./container-service-deployment.md)
- [Connettersi a un cluster del servizio contenitore di Azure](./container-service-connect.md)

## Esplorare l'interfaccia utente di Mesos

Dopo aver stabilito un tunnel SSH (Secure Shell), passare a http://localhost/Mesos. Verrà caricata l'interfaccia utente Web di Mesos. Nella pagina è possibile raccogliere informazioni sul cluster Mesos, ad esempio gli agenti attivati, lo stato delle attività e la disponibilità delle risorse.

![Interfaccia utente di creazione di una distribuzione - 1](media/ui1.png)

## Esplorare l'interfaccia utente di Marathon

Per visualizzare l'interfaccia utente di Marathon, passare a http://localhost/Marathon. In questa schermata è possibile avviare un nuovo contenitore o un'altra applicazione nel cluster Mesos del servizio contenitore di Azure. È anche possibile visualizzare le informazioni sull'esecuzione di contenitori e applicazioni.

![Interfaccia utente di creazione di una distribuzione - 2](media/ui2.png)

## Distribuire un contenitore Docker formattato

Per usare Marathon per l'avvio di un nuovo contenitore nel cluster Mesos, fare clic sul pulsante **Crea applicazione**. È possibile usare il modulo **Nuova applicazione** per definire i parametri dell'applicazione o del contenitore. In questo esempio verrà distribuito un semplice contenitore Nginx. Immettere le seguenti informazioni. Al termine, fare clic su **Crea**.

Campo | Valore
----------------|-----------
ID | nginx
Image | nginx
Network | Bridged
Container Port | 80
Host Port | 80
Protocol | TCP

![Interfaccia utente di creazione di una distribuzione - 3](media/ui3.png)

Tornare alla pagina principale di Marathon, dove ora viene visualizzato lo stato di distribuzione del contenitore.

![Interfaccia utente di creazione di una distribuzione - 4](media/ui4.png)

Se si passa nuovamente all'app Mesos (http://localhost/Mesos) è possibile notare un'attività, in questo caso un contenitore Docker formattato, in esecuzione nel cluster Mesos. Viene anche visualizzato il nodo del cluster in cui viene eseguita l'attività.

![Interfaccia utente di creazione di una distribuzione - 5](media/ui5.png)

## Ridimensionare i contenitori

È anche possibile usare l'interfaccia utente Web di Marathon per ridimensionare il numero di istanze di un contenitore. A tale scopo, passare alla pagina di Marathon, selezionare il contenitore da ridimensionare e quindi fare clic sul pulsante **Ridimensiona**. Nella finestra di dialogo **Ridimensiona applicazione** immettere il numero di istanze del contenitore desiderato e selezionare **Ridimensiona applicazione**.

![Interfaccia utente di creazione di una distribuzione - 6](media/ui6.png)

Al termine dell'operazione di ridimensionamento verranno visualizzate più istanze della stessa attività distribuite tra gli agenti di Mesos.

![Interfaccia utente di creazione di una distribuzione - 7](media/ui8.png)

<!---HONumber=AcomDC_0406_2016-->