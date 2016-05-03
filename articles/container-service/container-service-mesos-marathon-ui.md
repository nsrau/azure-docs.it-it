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

DC/OS offre un ambiente di distribuzione e ridimensionamento dei carichi di lavoro cluster con l'astrazione dell'hardware sottostante. In DC/OS è disponibile anche un framework che gestisce la pianificazione e l'esecuzione dei carichi di lavoro di calcolo.

Sono disponibili framework per molti dei carichi di lavoro più comuni. Questo documento illustra come creare e ridimensionare la distribuzione di contenitori con Marathon. Prima di eseguire questi esempi, è necessario un cluster DC/OS configurato nel servizio contenitore di Azure. È necessaria anche la connettività remota a questo cluster. Per altre informazioni su questi elementi, vedere gli articoli indicati di seguito.

- [Distribuire un cluster del servizio contenitore di Azure](./container-service-deployment.md)
- [Connettersi a un cluster del servizio contenitore di Azure](./container-service-connect.md)

## Esplorare l'interfaccia utente di DC/OS

Dopo aver stabilito un tunnel SSH (Secure Shell), passare a http://localhost/. Verrà caricata l'interfaccia utente Web di DC/OS che mostra informazioni sul cluster, ad esempio le risorse usate, gli agenti attivi e i servizi in esecuzione.

![](media/dcos/dcos2.png)

## Esplorare l'interfaccia utente di Marathon

Per visualizzare l'interfaccia utente di Marathon, passare a http://localhost/Marathon. In questa schermata è possibile avviare un nuovo contenitore o un'altra applicazione nel cluster DC/OS del servizio contenitore di Azure. È anche possibile visualizzare le informazioni sull'esecuzione di contenitori e applicazioni.

![](media/dcos/dcos3.png)

## Distribuire un contenitore Docker formattato

Per distribuire un nuovo contenitore con Marathon, fare clic sul pulsante **Crea applicazione** e immettere le informazioni seguenti nel modulo. Al termine, fare clic su **Crea applicazione**.

Campo | Valore
----------------|-----------
ID | nginx
Image | nginx
Network | Bridged
Host Port | 80
Protocol | TCP

![](media/dcos/dcos4.png)

![](media/dcos/dcos5.png)

![](media/dcos/dcos6.png)

Se si vuole eseguire il mapping in modo statico della porta del contenitore a una porta dell'agente, è necessario usare "JSON Mode". A tale scopo, nella procedura guidata New Application passare a JSON Mode usando l'interruttore e immettere le informazioni seguenti nella sezione 'portMappings' della definizione dell'applicazione. Questo esempio associa la porta 80 del contenitore alla porta 80 dell'agente di DC/OS. Una volta apportata questa modifica, si può passare di nuovo da JSON Mode e alla procedura guidata.

```none
“hostPort”: 80,
```

![](media/dcos/dcos13.png)

Il cluster CD/OS viene distribuito con un set di agenti privati e pubblici. Per accedere alle applicazioni da Internet, è necessario distribuirle a un agente pubblico. A tale scopo, selezionare la scheda 'optional' della procedura guidata New Application e immettere 'slave\_public' per 'Accepted Resource Roles'.

![](media/dcos/dcos14.png)

Tornare alla pagina principale di Marathon, dove ora viene visualizzato lo stato di distribuzione del contenitore.

![](media/dcos/dcos7.png)

Se si passa di nuovo all'app DC/OS (http://localhost/)) si noterà che un'attività, in questo caso un contenitore in formato Docker, è in esecuzione nel cluster DC/OS.

![](media/dcos/dcos8.png)

Viene anche visualizzato il nodo del cluster in cui viene eseguita l'attività.

![](media/dcos/dcos9.png)

## Ridimensionare i contenitori

L'interfaccia utente di Marathon consente di ridimensionare il numero di istanze di un contenitore. A questo scopo, passare alla pagina di Marathon, selezionare il contenitore da ridimensionare e quindi fare clic sul pulsante **Scale**. Nella finestra di dialogo **Scale Application** immettere il numero desiderato di istanze del contenitore e selezionare **Scale Application**.

![](media/dcos/dcos10.png)

Al termine dell'operazione di ridimensionamento verranno visualizzate più istanze della stessa attività distribuite tra gli agenti di DC/OS.

![](media/dcos/dcos11.png)

![](media/dcos/dcos12.png)

## Passaggi successivi

[Utilizzare l'API Marathon e DC/OS](./container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0427_2016-->