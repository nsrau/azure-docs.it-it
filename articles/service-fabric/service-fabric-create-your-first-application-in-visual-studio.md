<properties
   pageTitle="Creare la prima applicazione Infrastruttura di servizi in Visual Studio | Microsoft Azure"
   description="Creare, distribuire ed eseguire il debug di un'applicazione Infrastruttura di servizi con Visual Studio"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="seanmck"/>

# Creare la prima applicazione Infrastruttura di servizi in Visual Studio

Service Fabric SDK include un componente aggiuntivo per Visual Studio che fornisce i modelli e gli strumenti per creare, eseguire il debug e distribuire applicazioni Infrastruttura di servizi. Questo argomento descrive la procedura per creare la prima applicazione in Visual Studio.

## Prerequisiti

Prima di iniziare, assicurarsi di aver [configurato l'ambiente di sviluppo](service-fabric-get-started.md).

## Creare l'applicazione

Un'applicazione Infrastruttura di servizi può contenere uno o più servizi, ognuno dei quali contribuisce alle funzionalità dell'applicazione con un ruolo specifico. La Creazione guidata nuovo progetto consente di creare un progetto di applicazione insieme al primo progetto di servizio. Sarà possibile aggiungere altri servizi in un secondo momento.

1. Avviare Visual Studio come amministratore.

2. Scegliere **File > Nuovo progetto > Cloud > Applicazione Infrastruttura di servizi**.

3. Assegnare un nome all'applicazione e fare clic su OK.

	![Finestra di dialogo Nuovo progetto in Visual Studio][1]

4. Nella finestra di dialogo successiva verrà richiesto di scegliere il primo tipo di servizio da includere nell'applicazione. Ai fini di questa esercitazione, verrà selezionata l'opzione "Servizio con stato". Assegnargli un nome e fare clic su OK.

	![Finestra di dialogo Nuovo servizio in Visual Studio][2]

	>[AZURE.NOTE]Per altre informazioni sulle opzioni, vedere [Scegliere un framework](service-fabric-choose-framework.md).

	Visual studio crea il progetto di applicazione e il progetto di servizio con stato e li visualizza in Esplora soluzioni.

	![Esplora soluzioni dopo la creazione dell'applicazione con servizio con stato][3]

	Il progetto di applicazione non contiene direttamente il codice, ma fa riferimento a un set di progetti di servizio. Include inoltre altri tre tipi di contenuto:

	- **Profili di pubblicazione**: usati per gestire le preferenze relative agli strumenti per diversi ambienti.

	- **Script**: uno script PowerShell per distribuire/aggiornare l'applicazione. Questo script viene eseguito in background da Visual Studio e può essere richiamato direttamente tramite la riga di comando.

	- **Definizione dell'applicazione**: il manifesto dell'applicazione e i file dei parametri dell'applicazione associati definiscono l'applicazione e consentono di configurarla appositamente per un ambiente specifico.

  Per una panoramica dei contenuti del progetto di servizio, vedere la [Guida introduttiva a Reliable Services](service-fabric-reliable-services-quick-start.md).

## Distribuire ed eseguire il debug dell'applicazione

A questo punto, è possibile provare a eseguire l'app creata.

1. Premere F5 in Visual Studio per compilare l'applicazione per il debug.

	>[AZURE.NOTE]Poiché si tratta della prima volta, l'operazione richiederà del tempo perché Visual Studio creerà un cluster locale per lo sviluppo. Un cluster locale esegue lo stesso codice della piattaforma che verrà creato in un cluster con più macchine virtuali, ma in una singola macchina virtuale. Lo stato della creazione del cluster verrà visualizzato nella finestra di output di Visual Studio.

	Quando il cluster è pronto, si riceverà una notifica dall'applicazione di gestione della barra delle applicazioni inclusa nell'SDK.

	![Notifica della barra delle applicazioni per il cluster locale][4]

2. All'avvio dell'applicazione, Visual Studio aprirà automaticamente il visualizzatore eventi di diagnostica in cui è possibile visualizzare l'output di traccia dal servizio.

	![Visualizzatore eventi di diagnostica][5]

	Nel caso del servizio con stato, i messaggi indicheranno semplicemente il valore del contatore che viene incrementato nel metodo `RunAsync` del file MyStatefulService.cs.

3. Espandere uno degli eventi per visualizzare informazioni dettagliate, ad esempio il nodo in cui è in esecuzione il codice. Nel caso di questo esempio, si tratta del nodo 2, ma questo numero può variare a seconda del computer usato.

	![Dettaglio del visualizzatore eventi di diagnostica][6]

	Il cluster locale è costituito da 5 nodi ospitati in una singola macchina virtuale, per simulare un cluster a 5 nodi ognuno dei quali risiede in una macchina virtuale diversa. Verrà quindi portato offline uno dei nodi del cluster locale per simulare la perdita di una macchina virtuale e provare a eseguire il debugger di Visual Studio.

    >[AZURE.NOTE]Gli eventi di diagnostica dell'applicazione inviati dal modello di progetto usano la classe `ServiceEventSource` inclusa. Per altre informazioni, vedere l'argomento che illustra [come monitorare e diagnosticare i servizi in locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally)

4. Nel progetto di servizio individuare la classe che deriva da StatefulService (ad esempio MyStatefulService) e impostare un punto di interruzione sulla prima riga del metodo `RunAsync`.

	![Punto di interruzione nel metodo RunAsync del servizio con stato][7]

5. Fare clic con il pulsante destro del mouse sull'app della barra delle applicazioni Local Cluster Manager e scegliere Manage Local Cluster per avviare Service Fabric Explorer.

  ![Avvio di Service Fabric Explorer da Local Cluster Manager][systray-launch-sfx]

  Service Fabric Explorer offre una rappresentazione visiva del cluster, inclusi il set di applicazioni distribuite al suo interno e il set di nodi fisici che lo costituiscono.

6. Nel riquadro sinistro espandere **Cluster > Nodi** e individuare il nodo in cui è in esecuzione il codice.

7. Fare clic su **Azioni > Disattiva (Riavvia)** per simulare il riavvio di una macchina virtuale.

	![Arresto di un nodo in Service Fabric Explorer][sfx-stop-node]

	Verrà visualizzato il raggiungimento del punto di interruzione in Visual Studio mentre viene effettuato il failover del calcolo eseguito su un nodo in un altro nodo.

8. Tornare al visualizzatore eventi di diagnostica e osservare i messaggi. Si noti che il contatore ha continuato ad aumentare anche se gli eventi provengono effettivamente da un nodo diversi.

  ![Visualizzatore eventi di diagnostica dopo il failover][diagnostic-events-viewer-detail-post-failover]


## Passaggi successivi

- [Informazioni su come creare un cluster in Azure](service-fabric-cluster-creation-via-portal.md)
- [Informazioni su come creare servizi con il modello di programmazione Reliable Services](service-fabric-reliable-services-quick-start.md)
- [Informazioni su come è possibile esporre i servizi in Internet con l'API Web](service-fabric-add-a-web-frontend.md)
- [Provare a creare un servizio usando il modello di programmazione Reliable Actors](service-fabric-reliable-actors-get-started.md)

<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png

<!---HONumber=Nov15_HO4-->