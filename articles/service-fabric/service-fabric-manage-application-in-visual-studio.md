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
   ms.date="09/09/2016"
   ms.author="seanmck;mikhegn"/> 

# Usare Visual Studio per semplificare la scrittura e la gestione delle applicazioni dell’infrastruttura di servizi

È possibile gestire le applicazioni e i servizi di Service Fabric di Azure tramite Visual Studio. Dopo aver [configurato l'ambiente di sviluppo](service-fabric-get-started.md), è infatti possibile usare Visual Studio per creare applicazioni di Service Fabric, aggiungere servizi o creare i pacchetti, registrare e distribuire le applicazioni nel cluster di sviluppo locale.

## Distribuire l'applicazione di Service Fabric

Per impostazione predefinita, la distribuzione di un'applicazione combina in un'unica operazione i passaggi seguenti:

1. Creazione del pacchetto applicazione
2. Caricamento del pacchetto applicazione in Image Store
3. Registrazione del tipo di applicazione
4. Rimozione delle eventuali istanze dell'applicazione in esecuzione
5. Creazione di una nuova istanza dell'applicazione

In Visual Studio, anche premendo **F5** l'applicazione viene distribuita e il debugger viene collegato a tutte le relative istanze. È possibile utilizzare **Ctrl + F5** per distribuire un'applicazione senza il debug, o pubblicare in un cluster locale o remoto mediante il profilo di pubblicazione. Per altre informazioni, consultare [Pubblicare un'applicazione in un cluster remoto mediante Visual Studio](service-fabric-publish-app-remote-cluster.md).

### Modalità di debug applicazione

Per impostazione predefinita, Visual Studio rimuove le istanze esistenti del tipo di applicazione quando si interrompe il debug oppure (se l'app è stata distribuita senza collegare il debugger) quando si ridistribuisce l'applicazione. In tal caso vengono rimossi tutti i dati dell'applicazione. Durante il debug in locale, è possibile conservare i dati già creati durante il test di una nuova versione dell'applicazione, mantenere l'applicazione in esecuzione o fare in modo che l'applicazione venga aggiornata durante sessioni di debug successive. Strumenti di Service Fabric per Visual Studio offre una proprietà denominata **Modalità di debug applicazione** che controlla se **F5** disinstalla o mantiene l'applicazione al termine di una sessione di debug oppure fa sì che l'applicazione venga aggiornata in sessioni di debug successive, invece che rimossa e ridistribuita.

#### Per impostare la proprietà Modalità di debug applicazione

1. Dal menu di scelta rapida del progetto dell'applicazione, scegliere **Proprietà** (o premere il pulsante **F4**).
2. Nella finestra **Proprietà** impostare la proprietà **Modalità di debug applicazione**.

    ![Impostare la proprietà Modalità di debug applicazione][debugmodeproperty] 

Di seguito sono indicate le opzioni di **modalità di debug applicazione** disponibili.

1. **Aggiornamento automatico**: l'applicazione resta in esecuzione al termine della sessione di debug. Premendo nuovamente **F5** la distribuzione viene considerata come un aggiornamento e viene usata la modalità automatica non monitorata per aggiornare rapidamente l'applicazione a una versione più recente con l'aggiunta di una stringa della data. Il processo di aggiornamento mantiene tutti i dati immessi in una sessione di debug precedente.

2. **Mantieni applicazione**: l'applicazione viene lasciata in esecuzione nel cluster al termine della sessione di debug. Premendo nuovamente **F5** l'applicazione verrà rimossa e la nuova applicazione creata verrà distribuita nel cluster.

3. **Rimuovi applicazione** fa sì che l'applicazione venga rimossa al termine della sessione di debug.

Nell'opzione **Aggiornamento automatico** i dati vengono conservati applicando le funzionalità di aggiornamento dell'applicazione di Service Fabric, ma sono ottimizzati ai fini delle prestazioni anziché della sicurezza. Per altre informazioni sull'aggiornamento delle applicazioni e su come è possibile eseguire un aggiornamento in un ambiente reale, vedere [Aggiornamento di un'applicazione di Service Fabric](service-fabric-application-upgrade.md).

![Esempio di una nuova versione dell'applicazione con data aggiunta][preservedata] 

>[AZURE.NOTE] Questa proprietà non è presente nelle versioni precedenti alla versione 1.1 di Strumenti di Service Fabric per Visual Studio. Per ottenere lo stesso comportamento nelle versioni precedenti alla versione 1.1, usare la proprietà **Conserva dati all'avvio**. L'opzione "Mantieni applicazione" è stata introdotta nella versione 1.2 di Strumenti di Service Fabric per Visual Studio.

## Aggiungere un servizio all'applicazione di Service Fabric

È possibile aggiungere nuovi servizi all'applicazione per estenderne le funzionalità. Per essere certi che il servizio venga incluso nel pacchetto applicazione, aggiungere il servizio usando la voce di menu **New Fabric Service**.

![Aggiungere un nuovo servizio di Service Fabric all'applicazione][newservice] 

Selezionare un tipo di progetto di Service Fabric da aggiungere all'applicazione e specificare un nome per il servizio. Per decidere più facilmente quale tipo di servizio usare, vedere [Scelta di un framework per il servizio](service-fabric-choose-framework.md).

![Selezionare un tipo di progetto di Service Fabric da aggiungere all'applicazione][addserviceproject] 

Il nuovo servizio verrà aggiunto alla soluzione e al pacchetto applicazione esistente. Al manifesto dell'applicazione verranno inoltre aggiunti i riferimenti al servizio e un'istanza di servizio predefinita. Il servizio verrà creato e avviato alla successiva distribuzione dell'applicazione.

![Il nuovo servizio verrà aggiunto al manifesto dell'applicazione][newserviceapplicationmanifest] 

## Creazione del pacchetto per l'applicazione di Service Fabric

Per distribuire l'applicazione e i relativi servizi in un cluster, è necessario creare un pacchetto dell’applicazione. Tale pacchetto organizza il manifesto dell'applicazione, i manifesti dei servizi e gli altri file necessari in un layout specifico. Visual Studio imposta e gestisce il pacchetto nella cartella del progetto dell'applicazione, nella cartella "pkg". Facendo clic su **Pacchetto** dal menu di scelta rapida **Applicazione** il pacchetto di applicazione viene creato o aggiornato. È possibile procedere in questo modo se si intende distribuire l'applicazione usando script di PowerShell personalizzati.

## Rimuovere applicazioni e tipi di applicazione con Cloud Explorer

In Visual Studio è possibile eseguire operazioni di gestione dei cluster di base con Cloud Explorer, che può essere avviato dal menu **Visualizza**. È ad esempio possibile eliminare applicazioni e annullare il provisioning di tipi di applicazione in cluster locali o remoti.

![Rimuovere un'applicazione](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png) 

>[AZURE.TIP] Per funzionalità di gestione dei cluster più avanzate, vedere [Visualizzare il cluster con Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).


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
[debugmodeproperty]: ./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png

<!---HONumber=AcomDC_0921_2016-->