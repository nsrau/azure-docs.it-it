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
   ms.date="09/19/2016"
   ms.author="nepeters"/>

# Gestione di contenitori tramite l'interfaccia utente Web

DC/OS offre un ambiente di distribuzione e ridimensionamento dei carichi di lavoro cluster con l'astrazione dell'hardware sottostante. In DC/OS è disponibile anche un framework che gestisce la pianificazione e l'esecuzione dei carichi di lavoro di calcolo.

Sono disponibili framework per molti dei carichi di lavoro più comuni. Questo documento illustra come creare e ridimensionare la distribuzione di contenitori con Marathon. Prima di eseguire questi esempi, è necessario un cluster DC/OS configurato nel servizio contenitore di Azure. È necessaria anche la connettività remota a questo cluster. Per altre informazioni su questi elementi, vedere gli articoli indicati di seguito:

- [Distribuire un cluster del servizio contenitore di Azure](container-service-deployment.md)
- [Connettersi a un cluster del servizio contenitore di Azure](container-service-connect.md)

## Esplorare l'interfaccia utente di DC/OS

Dopo aver stabilito un tunnel SSH (Secure Shell), passare a http://localhost/. Verrà caricata l'interfaccia utente Web di DC/OS che visualizza informazioni sul cluster, ad esempio le risorse usate, gli agenti attivi e i servizi in esecuzione.

![Interfaccia utente di DC/OS](media/dcos/dcos2.png)

## Esplorare l'interfaccia utente di Marathon

Per visualizzare l'interfaccia utente di Marathon, passare a http://localhost/Marathon. In questa schermata è possibile avviare un nuovo contenitore o un'altra applicazione nel cluster DC/OS del servizio contenitore di Azure. È anche possibile visualizzare le informazioni sull'esecuzione di contenitori e applicazioni.

![Interfaccia utente di Marathon](media/dcos/dcos3.png)

## Distribuire un contenitore Docker formattato

Per distribuire un nuovo contenitore con Marathon, fare clic sul pulsante **Create Application** e immettere le informazioni seguenti nel modulo:

Campo | Valore
----------------|-----------
ID | nginx
Image | nginx
Network | Bridged
Host Port | 80
Protocol | TCP

![Interfaccia utente New Application--General](media/dcos/dcos4.png)

![Interfaccia utente New Application--Docker Container](media/dcos/dcos5.png)

![Interfaccia utente New Application--Ports and Service Discovery](media/dcos/dcos6.png)

Per eseguire il mapping statico della porta del contenitore su una porta dell'agente è necessario usare la modalità JSON. A tale scopo impostare la procedura guidata New Application su **JSON Mode** usando l'interruttore. Specificare quindi il codice seguente sotto la sezione `portMappings` della definizione dell'applicazione. Questo esempio associa la porta 80 del contenitore alla porta 80 dell'agente DC/OS. Dopo aver apportato questa modifica è possibile uscire dalla modalità JSON della procedura guidata.

```none
"hostPort": 80,
```

![Interfaccia utente New Application--esempio con porta 80](media/dcos/dcos13.png)

Il cluster CD/OS viene distribuito con un set di agenti privati e pubblici. Per consentire al cluster di accedere alle applicazioni da Internet è necessario distribuire le applicazioni a un agente di pubblico. A tale scopo selezionare la scheda **Optional** della procedura guidata New Application e immettere **slave\_public** per **Accepted Resource Roles**.

![Interfaccia utente New Application--impostazione dell'agente pubblico](media/dcos/dcos14.png)

Tornare alla pagina principale di Marathon, dove ora viene visualizzato lo stato di distribuzione del contenitore.

![Interfaccia utente della pagina principale di Marathon--stato di distribuzione contenitore](media/dcos/dcos7.png)

Quando si passa di nuovo all'interfaccia utente Web di DC/OS (http://localhost/) si noterà che nel cluster DC/OS è in esecuzione un'attività, in questo caso un contenitore in formato Docker.

![Interfaccia utente Web di DC/OS--attività in esecuzione nel cluster](media/dcos/dcos8.png)

Viene anche visualizzato il nodo del cluster in cui viene eseguita l'attività.

![Interfaccia utente Web di DC/OS--nodo del cluster dell'attività](media/dcos/dcos9.png)

## Ridimensionare i contenitori

È possibile usare l'interfaccia utente di Marathon per ridimensionare il numero di istanze di un contenitore. A questo scopo passare alla pagina di **Marathon**, selezionare il contenitore da ridimensionare e fare clic sul pulsante **Scale**. Nella finestra di dialogo **Scale Application** immettere il numero di istanze del contenitore e selezionare **Scale Application**.

![Interfaccia utente di Marathon--finestra di dialogo Scale Application](media/dcos/dcos10.png)

Al termine dell'operazione di ridimensionamento verranno visualizzate più istanze della stessa attività distribuite tra gli agenti di DC/OS.

![Dashboard dell'interfaccia utente Web di DC/OS--attività distribuita tra agenti](media/dcos/dcos11.png)

![Interfaccia utente Web di DC/OS--nodi](media/dcos/dcos12.png)

## Passaggi successivi

- [Usare DC/OS e l'API Marathon](container-service-mesos-marathon-rest.md)

Approfondimento sul servizio contenitore di Azure con Mesos

> [AZURE.VIDEO] azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos]

<!---HONumber=AcomDC_0921_2016-->