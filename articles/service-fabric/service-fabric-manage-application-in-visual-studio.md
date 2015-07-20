<properties
   pageTitle="Gestione delle applicazioni di Service Fabric in Visual Studio"
   description="È possibile gestire le applicazioni e i servizi di Microsoft Azure Service Fabric tramite Visual Studio."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/14/2015"
   ms.author="jesseb"/>

# Gestione delle applicazioni di Service Fabric in Visual Studio

È possibile gestire le applicazioni e i servizi di Microsoft Azure Service Fabric tramite Visual Studio. Dopo aver [configurato l'ambiente di sviluppo](../service-fabric-setup-your-development-environment), è infatti possibile usare Visual Studio per creare applicazioni di Service Fabric, aggiungere servizi o creare i pacchetti, registrare e distribuire le applicazioni nel cluster di sviluppo locale.

Per gestire l'applicazione di Service Fabric, in Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto dell'applicazione.

![Gestire l'applicazione di Service Fabric facendo clic con il pulsante destro del mouse sul progetto dell'applicazione][manageservicefabric]

## Distribuzione dell'applicazione di Service Fabric

Distribuendo un'applicazione di Service Fabric, si combinano in un'unica operazione i passaggi seguenti:

1. Creazione del pacchetto applicazione
2. Caricamento del pacchetto applicazione in Image Store
3. Registrazione del tipo di applicazione
4. Rimozione delle eventuali istanze dell'applicazione in esecuzione
5. Creazione di una nuova istanza dell'applicazione

In Visual Studio è possibile eseguire la distribuzione facendo clic con il pulsante destro del mouse sull'applicazione di Service Fabric in **Esplora soluzioni** e scegliendo **Distribuisci**. Anche premendo **F5** l'applicazione viene distribuita e il debugger viene collegato a tutte le relative istanze.

È possibile rimuovere la distribuzione usando **Rimuovi distribuzione**. In questo modo verranno annullati i passaggi di distribuzione descritti in precedenza.

1. Rimuovere le eventuali istanze dell'applicazione in esecuzione.
2. Annullare la registrazione del tipo di applicazione.
3. Rimuovere il pacchetto applicazione da Image Store.

## Aggiunta di un servizio a Service Fabric

È possibile aggiungere nuovi servizi di Service Fabric all'applicazione per estenderne la funzionalità. Per essere certi che il servizio venga incluso nel pacchetto applicazione, aggiungere il servizio usando la voce di menu **New Fabric Service**.

![Aggiungere un nuovo servizio di Service Fabric all'applicazione][newservice]

Selezionare un tipo di progetto di Service Fabric da aggiungere all'applicazione e specificare un nome per il servizio. Per decidere più facilmente quale tipo di servizio usare, vedere l'articolo relativo alla [scelta di un framework per il servizio](service-fabric-choose-framework.md).

![Selezionare un tipo di progetto di Service Fabric da aggiungere all'applicazione][addserviceproject]

Il nuovo servizio verrà aggiunto alla soluzione e al pacchetto applicazione esistente. Al manifesto dell'applicazione verranno inoltre aggiunti i riferimenti al servizio e un'istanza di servizio predefinita. Il servizio verrà creato e avviato alla successiva distribuzione dell'applicazione.

![Il nuovo servizio verrà aggiunto al manifesto dell'applicazione][newserviceapplicationmanifest]

## Creazione del pacchetto per l'applicazione di Service Fabric

Per distribuire l'applicazione e i relativi servizi in un cluster, è necessario creare un pacchetto applicazione. Tale pacchetto organizza il manifesto dell'applicazione, i manifesti dei servizi e gli altri file necessari in un layout specifico. Visual Studio imposta e gestisce il pacchetto nella cartella del progetto dell'applicazione, nella cartella "pkg". Quando si fa clic su **Pacchetto**, il pacchetto applicazione viene creato o aggiornato. È possibile procedere in questo modo se si intende distribuire l'applicazione usando script di PowerShell personalizzati.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

- [Modello di applicazione di Service Fabric](../service-fabric-application-model)
- [Distribuzione di un'applicazione di Service Fabric](../service-fabric-deploy-remove-applications)
- [Debug dell'applicazione di Service Fabric](../service-fabric-debugging-your-application)
- [Visualizzazione del cluster con Service Fabric Explorer](../service-fabric-visualizing-your-cluster)

<!--Image references-->
[addserviceproject]: ./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]: ./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]: ./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
 

<!---HONumber=July15_HO2-->