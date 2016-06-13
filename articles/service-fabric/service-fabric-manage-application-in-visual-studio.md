<properties
   pageTitle="Gestire le applicazioni in Visual Studio | Microsoft Azure"
   description="Usare Visual Studio per creare, sviluppare, creare pacchetti, distribuire ed effettuare il debug di applicazioni dell’infrastruttura di servizi e di servizi."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/02/2016"
   ms.author="seanmck"/>

# Usare Visual Studio per semplificare la scrittura e la gestione delle applicazioni dell’infrastruttura di servizi

È possibile gestire le applicazioni e i servizi di Service Fabric di Azure tramite Visual Studio. Dopo aver [configurato l'ambiente di sviluppo](service-fabric-get-started.md), è infatti possibile usare Visual Studio per creare applicazioni di Service Fabric, aggiungere servizi o creare i pacchetti, registrare e distribuire le applicazioni nel cluster di sviluppo locale.

Per gestire l'applicazione, in Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto dell'applicazione.

![Gestire l'applicazione di Service Fabric facendo clic con il pulsante destro del mouse sul progetto dell'applicazione][manageservicefabric]

## Distribuire l'applicazione di Service Fabric

Distribuendo un'applicazione, si combinano in un'unica operazione i passaggi seguenti:

1. Creazione del pacchetto applicazione
2. Caricamento del pacchetto applicazione in Image Store
3. Registrazione del tipo di applicazione
4. Rimozione delle eventuali istanze dell'applicazione in esecuzione
5. Creazione di una nuova istanza dell'applicazione

In Visual Studio, anche premendo **F5** l'applicazione viene distribuita e il debugger viene collegato a tutte le relative istanze. È possibile utilizzare **Ctrl + F5** per distribuire un'applicazione senza il debug, o pubblicare in un cluster locale o remoto mediante il profilo di pubblicazione. Per ulteriori informazioni, consultare [Pubblicare un'applicazione in un cluster remoto mediante Visual Studio](service-fabric-publish-app-remote-cluster.md)

### Mantenere i dati tra le esecuzioni dei test

Spesso, si verificano i servizi in locale aggiungendo input di dati di test, modificando alcuni blocchi di codice e quindi nuovamente eseguendo il debug in locale. Gli strumenti di Service Fabric di Visual Studio forniscono una proprietà utile denominata **Conservare i dati all'avvio** per mantenere i dati immessi nella sessione precedente e avere la possibilità di usarli nuovamente.

#### Per abilitare la proprietà di conservazione dei dati all’avvio.

1. Dal menu di scelta rapida del progetto dell'applicazione, scegliere **Proprietà** (o premere il pulsante **F4**).
1. Nella finestra **Proprietà**, impostare la proprietà **Mantieni i dati all'avvio** su **Sì**.

	![Impostare la proprietà di conservazione dei dati all’avvio][preservedata]

Quando si esegue nuovamente l'applicazione, lo script di distribuzione considera ora la distribuzione come un aggiornamento utilizzando la modalità automatica non monitorata per l'aggiornamento rapido dell'applicazione a una versione più recente con una stringa di dati aggiunta. Il processo di aggiornamento mantiene tutti i dati immessi in una sessione di debug precedente.

![Esempio di una nuova versione dell'applicazione con data aggiunta][preservedate]

I dati vengono conservati sfruttando la funzionalità di aggiornamento della piattaforma di Service Fabric. Per altre informazioni sull'aggiornamento di un'applicazione, consultare [Aggiornamento dell'applicazione di Service Fabric](service-fabric-application-upgrade.md).

## Aggiungere un servizio all'applicazione di Service Fabric

È possibile aggiungere nuovi servizi di Service Fabric all'applicazione per estenderne la funzionalità. Per essere certi che il servizio venga incluso nel pacchetto applicazione, aggiungere il servizio usando la voce di menu **New Fabric Service**.

![Aggiungere un nuovo servizio di Service Fabric all'applicazione][newservice]

Selezionare un tipo di progetto di Service Fabric da aggiungere all'applicazione e specificare un nome per il servizio. Per decidere più facilmente quale tipo di servizio usare, vedere [Scelta di un framework per il servizio](service-fabric-choose-framework.md).

![Selezionare un tipo di progetto di Service Fabric da aggiungere all'applicazione][addserviceproject]

Il nuovo servizio verrà aggiunto alla soluzione e al pacchetto applicazione esistente. Al manifesto dell'applicazione verranno inoltre aggiunti i riferimenti al servizio e un'istanza di servizio predefinita. Il servizio verrà creato e avviato alla successiva distribuzione dell'applicazione.

![Il nuovo servizio verrà aggiunto al manifesto dell'applicazione][newserviceapplicationmanifest]

## Creazione del pacchetto per l'applicazione di Service Fabric

Per distribuire l'applicazione e i relativi servizi in un cluster, è necessario creare un pacchetto dell’applicazione. Tale pacchetto organizza il manifesto dell'applicazione, i manifesti dei servizi e gli altri file necessari in un layout specifico. Visual Studio imposta e gestisce il pacchetto nella cartella del progetto dell'applicazione, nella cartella "pkg". Facendo clic su **Pacchetto ** dal menu di scelta rapida **Applicazione** il pacchetto di applicazione viene creato o aggiornato. È possibile procedere in questo modo se si intende distribuire l'applicazione usando script di PowerShell personalizzati.

## Rimuovere un'applicazione

È possibile annullare il provisioning di un tipo di applicazione dal cluster locale mediante l’explorer di Service Fabric. L’explorer cluster è accessibile dall’endpoint Gateway HTTP del cluster (in genere 19080 o 19007), ad esempio http://localhost:19080/Explorer. In questo modo verranno annullati i passaggi di distribuzione descritti in precedenza:

1. Rimuovere le eventuali istanze dell'applicazione in esecuzione.
2. Annullare la registrazione del tipo di applicazione.

![Rimuovere un'applicazione](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

- [Modello di applicazione di Service Fabric](service-fabric-application-model.md)
- [Distribuzione di un'applicazione di Service Fabric](service-fabric-deploy-remove-applications.md)
- [Gestione dei parametri dell'applicazione per più ambienti](service-fabric-manage-multiple-environment-app-configuration.md)
- [Debug dell'applicazione di Service Fabric](service-fabric-debugging-your-application.md)
- [Visualizzazione del cluster con l’explorer di Service Fabric](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]: ./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]: ./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]: ./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[preservedata]: ./media/service-fabric-manage-application-in-visual-studio/preservedata.png
[preservedate]: ./media/service-fabric-manage-application-in-visual-studio/preservedate.png

<!---HONumber=AcomDC_0601_2016-->