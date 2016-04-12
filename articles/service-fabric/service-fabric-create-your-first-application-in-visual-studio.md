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
   ms.date="03/27/2016"
   ms.author="seanmck"/>

# Creare la prima applicazione di Azure Service Fabric in Visual Studio

Service Fabric SDK include un componente aggiuntivo per Visual Studio che fornisce i modelli e gli strumenti per creare, eseguire il debug e distribuire applicazioni di Service Fabric. Questo argomento descrive la procedura per creare la prima applicazione in Visual Studio.

## Prerequisiti

Prima di iniziare, assicurarsi di avere [configurato l'ambiente di sviluppo](service-fabric-get-started.md).

## Procedura dettagliata video

Il video seguente illustra i passaggi di questa esercitazione:

>[AZURE.VIDEO creating-your-first-service-fabric-application-in-visual-studio]

## Creazione dell'applicazione

Un'applicazione Infrastruttura di servizi può contenere uno o più servizi, ognuno dei quali contribuisce alle funzionalità dell'applicazione con un ruolo specifico. Con la Creazione guidata nuovo progetto si può creare un progetto di applicazione insieme al primo progetto di servizio. Sarà possibile aggiungere altri servizi in un secondo momento.

1. Avviare Visual Studio come amministratori.

2. Scegliere **File > Nuovo progetto > Cloud > Applicazione Infrastruttura di servizi**.

3. Assegnare un nome all'applicazione e fare clic su **OK**.

	![Finestra di dialogo Nuovo progetto in Visual Studio][1]

4. Nella pagina successiva verrà richiesto di scegliere il primo tipo di servizio da includere nell'applicazione. Ai fini di questa esercitazione, si selezionerà **Stateful**. Assegnare un nome al servizio e fare clic su **OK**.

	![Finestra di dialogo Nuovo servizio in Visual Studio][2]

	>[AZURE.NOTE] Per altre informazioni sulle opzioni, vedere [Scegliere un framework](service-fabric-choose-framework.md).

	Visual studio crea il progetto di applicazione e il progetto di servizio con stato e li visualizza in Esplora soluzioni.

	![Esplora soluzioni dopo la creazione dell'applicazione con servizio con stato][3]

	Il progetto di applicazione non contiene codice direttamente, ma fa riferimento a un set di progetti di servizio. Include inoltre altri tre tipi di contenuto:

	- **Profili di pubblicazione**: usati per gestire le preferenze relative agli strumenti per diversi ambienti.

	- **Script**: includono uno script di PowerShell per distribuire o aggiornare l'applicazione. Questo script viene eseguito in background da Visual Studio e può essere richiamato direttamente tramite la riga di comando.

	- **Definizione di applicazione**: include il manifesto dell'applicazione in *ApplicationPackageRoot* e i file di parametri dell'applicazione associati in *ApplicationParameters* che definiscono l'applicazione e consentono di configurarla appositamente per un ambiente specifico.

    Per una panoramica del contenuto del progetto di servizio, vedere la [Guida introduttiva a Reliable Services](service-fabric-reliable-services-quick-start.md).

## Distribuire ed eseguire il debug dell'applicazione

A questo punto, è possibile provare a eseguire l'app creata.

1. Premere F5 in Visual Studio per compilare l'applicazione per il debug.

	>[AZURE.NOTE] La prima volta questa operazione richiederà alcuni minuti, perché Visual Studio crea un cluster locale per lo sviluppo. Un cluster locale esegue lo stesso codice della piattaforma che verrà creato in un cluster con più macchine virtuali, ma in una singola macchina virtuale. Lo stato della creazione del cluster sarà visualizzato nella finestra di output di Visual Studio.

	Quando il cluster è pronto, l'applicazione di gestione dell'area di notifica del cluster locale inclusa nell'SDK visualizzerà un messaggio.

	![Notifica della barra delle applicazioni per il cluster locale][4]

2. All'avvio dell'applicazione, Visual Studio aprirà automaticamente il visualizzatore eventi di diagnostica in cui è possibile visualizzare l'output di traccia dal servizio.

	![Visualizzatore eventi di diagnostica][5]

	Nel caso del servizio con stato, i messaggi indicheranno semplicemente il valore del contatore che viene incrementato nel metodo `RunAsync` del file MyStatefulService.cs.

3. Espandere uno degli eventi per visualizzare altri dettagli, incluso il nodo in cui viene eseguito il codice. In questo caso, è il \_Node\_2, anche se nel computer locale potrebbe essere diverso.

	![Dettaglio del visualizzatore eventi di diagnostica][6]

	Il cluster locale include cinque nodi ospitati in un singolo computer. Simula un cluster di cinque nodi, ognuno dei quali risiede in un computer distinto. Verrà quindi portato offline uno dei nodi del cluster locale per simulare la perdita di una macchina virtuale e provare a eseguire il debugger di Visual Studio.

    >[AZURE.NOTE] Gli eventi di diagnostica dell'applicazione generati dal modello di progetto usano la classe `ServiceEventSource` inclusa. Per altre informazioni, vedere [Monitorare e diagnosticare servizi in una configurazione di sviluppo con computer locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

4. Nel progetto di servizio trovare la classe che deriva da StatefulService, ad esempio MyStatefulService, e impostare un punto di interruzione sulla prima riga del metodo `RunAsync`.

	![Punto di interruzione nel metodo RunAsync del servizio con stato][7]

5. Fare clic con il pulsante destro del mouse sull'app dell'area di notifica Local Cluster Manager e scegliere **Manage Local Cluster** per avviare Service Fabric Explorer.

    ![Avvio di Service Fabric Explorer da Local Cluster Manager][systray-launch-sfx]

    Service Fabric Explorer offre una rappresentazione visiva del cluster, inclusi il set di applicazioni distribuite al suo interno e il set di nodi fisici che lo costituiscono. Per altre informazioni su Service Fabric Explorer, vedere [Visualizzazione del cluster con Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

6. Nel riquadro sinistro espandere **Cluster > Nodes** e trovare il nodo in cui è in esecuzione il codice.

7. Fare clic su **Actions > Deactivate (Restart)** per simulare il riavvio di una macchina virtuale. È anche possibile farlo in un menu di scelta rapida nella vista elenco del nodo nel riquadro a sinistra selezionando i tre punti.

	![Arresto di un nodo in Service Fabric Explorer][sfx-stop-node]

	Verrà visualizzato il raggiungimento del punto di interruzione in Visual Studio mentre viene effettuato il failover del calcolo eseguito su un nodo in un altro nodo.

8. Tornare al visualizzatore eventi di diagnostica e osservare i messaggi. Si noti che il contatore ha continuato ad aumentare anche se gli eventi provengono in effetti da un nodo diverso.

    ![Visualizzatore eventi di diagnostica dopo il failover][diagnostic-events-viewer-detail-post-failover]

### + Cleaning up

  Prima di concludere, è importante ricordare che il cluster locale è molto reale. Anche dopo l'arresto del debugger e la chiusura di Visual Studio, le applicazioni rimarranno in esecuzione in background. A seconda della natura delle app, questa attività in background può richiedere risorse significative del computer. A questo scopo, sono disponibili diverse opzioni di gestione:

  1. Per rimuovere una singola applicazione e tutti i relativi dati, usare l'azione **Delete application** in Service Fabric Explorer con il menu **ACTIONS** o il menu di scelta rapida nella vista elenco dell'applicazione nel riquadro a sinistra.
  
    ![Eliminare un'applicazione in Service Fabric Explorer][sfe-delete-application]
    
  2. Dopo l'eliminazione dell'applicazione dal cluster, è quindi possibile scegliere **Unprovision Type** per l'applicazione, che rimuove il pacchetto dell'applicazione, inclusi il codice e la configurazione, dall'archivio immagini del cluster.
  3. Per arrestare il cluster mantenendo i dati dell'applicazione e le tracce, fare clic su **Stop Local Cluster** nell'app dell'area di notifica.

  4. Per eliminare completamente il cluster, fare clic su **Remove Local Cluster** nell'app dell'area di notifica. Questa opzione comporterà un'altra distribuzione lenta la prossima volta che si preme F5 in Visual Studio. Usarla solo se non si prevede di usare il cluster locale per un certo periodo o se è necessario recuperare risorse.



## Passaggi successivi

<!--
Temporarily removing this link because we have removed the ASP.NET template.

 - [See how you can expose your services to the Internet with a web service front end](service-fabric-add-a-web-frontend.md)
-->
- [Informazioni su come creare un cluster in Azure](service-fabric-cluster-creation-via-portal.md)
- [Altre informazioni su Reliable Services](service-fabric-reliable-services-quick-start.md)
- [Provare a creare un servizio con il modello di programmazione Reliable Actors](service-fabric-reliable-actors-get-started.md)

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
[sfe-delete-application]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-delete-application.png

<!---HONumber=AcomDC_0406_2016-->