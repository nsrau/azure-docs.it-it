<properties
    pageTitle="Pubblicare un'applicazione in un cluster remoto con Visual Studio | Microsoft Azure"
    description="Informazioni sui passaggi necessari per pubblicare un'applicazione in un cluster remoto dell'infrastruttura di servizi usando Visual Studio."
    services="service-fabric"
    documentationCenter="na"
    authors="cawa"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="10/28/2015"
    ms.author="cawa" />

# Pubblicare un'applicazione in un cluster remoto tramite Visual Studio

Gli strumenti di Visual Studio per l'infrastruttura di servizi forniscono un modo semplice, ripetibile e configurabile tramite script per pubblicare un'applicazione in un cluster dell'infrastruttura di servizi. Questa operazione viene eseguita usando uno script di distribuzione di PowerShell con profili di pubblicazione. I profili di pubblicazione sono file in formato XML che archiviano informazioni di pubblicazione fondamentali. La finestra di dialogo **Pubblica applicazione dell'infrastruttura di servizi** è stata aggiunta per consentire agli utenti di pubblicare facilmente un'applicazione in un cluster locale o remoto. Eventuali modifiche alle impostazioni apportate tramite la finestra di dialogo Pubblica vengono acquisite nei profili di pubblicazione. In questo modo è possibile usare le impostazioni di pubblicazione per la modifica manuale in un processo di automazione in un secondo momento.

## Elementi necessari per pubblicare un'applicazione in un cluster dell'infrastruttura di servizi

### Deploy-FabricApplication.ps1

Script di PowerShell che usa un percorso del profilo di pubblicazione come parametro per la pubblicazione di applicazioni dell'infrastruttura di servizi.

### Profili di pubblicazione

Una cartella nel progetto dell'applicazione dell'infrastruttura di servizi denominata **PublishProfiles** contiene i file **Cloud.XML** e **Local.XML**. Si tratta di "profili di pubblicazione" che archiviano informazioni fondamentali per la pubblicazione di un'applicazione, ad esempio: - parametri di connessione del cluster dell'infrastruttura di servizi - percorso di un file dei parametri dell'applicazione - impostazioni di aggiornamento.

### File dei parametri dell'applicazione

Una cartella nel progetto dell'applicazione dell'infrastruttura di pubblicazione denominata **ApplicationParameters** contiene i file XML per i valori dei parametri del manifesto dell'applicazione specificati dall'utente. I file manifesto dell'applicazione possono essere parametrizzati in modo che si possano usare diversi valori per le impostazioni di distribuzione.

Ad esempio, è possibile modificare il numero di partizione in modo che possa adattarsi ai diversi ambienti delle distribuzioni. Quando si crea un progetto, le impostazioni nel manifesto dell'applicazione, ad esempio **TargetReplicaSetSize** e **PartitionCount**, vengono convertite in parametri. Il valore predefinito di questi parametri è specificato nella sezione **Parameters** del file manifesto dell'applicazione, ApplicationManifest.XML, che si trova nel progetto dell'applicazione dell'infrastruttura di servizi. Tutti i valori aggiunti al file dei parametri dell'applicazione sostituiranno i valori predefiniti nel file manifesto dell'applicazione.

>[AZURE.NOTE]Per i servizi Actor, è necessario compilare il progetto prima di generare i parametri nel file manifesto.

Di seguito è illustrato un file manifesto dell'applicazione di esempio.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application2Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Actor1ActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="Actor1ActorService_MinReplicaSetSize" DefaultValue="2" />
      <Parameter Name="Actor1ActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Actor1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Actor1ActorService">
         <StatefulService ServiceTypeName="Actor1ActorServiceType" TargetReplicaSetSize="[Actor1ActorService_TargetReplicaSetSize]" MinReplicaSetSize="[Actor1ActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[Actor1ActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## Pubblicare un'applicazione in un cluster dell'infrastruttura di servizi usando la finestra di dialogo Pubblica applicazione dell'infrastruttura di servizi

I passaggi seguenti illustrano come pubblicare un'applicazione usando la finestra di dialogo **Pubblica applicazione dell'infrastruttura di servizi** fornita dagli strumenti di Visual Studio per l'infrastruttura di servizi.

1. Nel menu di scelta rapida del progetto dell'applicazione dell'infrastruttura di servizi scegliere **Pubblica** per visualizzare la finestra di dialogo **Pubblica applicazione dell'infrastruttura di servizi**.

    ![][0]

    Il file selezionato nell'elenco a discesa **Profilo di destinazione** si trova nella stessa posizione di tutte le impostazioni, ad eccezione di **Versioni manifesto**. È possibile riutilizzare un profilo esistente o crearne uno nuovo selezionando **<Gestisci profili>** nell'elenco a discesa **Profilo di destinazione**. Dopo aver scelto un profilo di pubblicazione, il relativo contenuto viene visualizzato nei campi corrispondenti della finestra di dialogo. Per salvare le modifiche in qualsiasi momento, scegliere il collegamento **Salva profilo**.

1. Nella sezione **Endpoint di connessione** è possibile specificare un endpoint di pubblicazione del cluster dell'infrastruttura di servizi locale o remoto. Per aggiungere o modificare l'endpoint di connessione, scegliere il pulsante **Seleziona**. Nella finestra di dialogo **Seleziona cluster dell'infrastruttura di servizi** vengono visualizzati gli endpoint di connessione disponibili del cluster dell'infrastruttura di servizi in cui è possibile eseguire la pubblicazione. Verrà chiesto di eseguire l'accesso a una sottoscrizione di Azure, se non ancora effettuato. Scegliere un endpoint di connessione.

    ![][1]

    Dopo aver scelto un endpoint, Visual Studio convalida la connessione al cluster dell'infrastruttura di servizi selezionato. Se il cluster non è sicuro, Visual Studio non può connettersi immediatamente al cluster. Tuttavia, se il cluster è sicuro, è necessario installare un certificato nel computer locale prima di procedere. Per altre informazioni, vedere l'articolo relativo alla [configurazione di connessioni sicure](service-fabric-visualstudio-configure-secure-connections.md). Al termine, fare clic sul pulsante **OK**. Il cluster selezionato viene visualizzato nella finestra di dialogo **Pubblica applicazione dell'infrastruttura di servizi**.

1. L'elenco a discesa **File dei parametri dell'applicazione** consente di passare a un file dei parametri dell'applicazione. Un file dei parametri dell'applicazione contiene valori specificati dall'utente per i parametri nel file manifesto dell'applicazione. Per aggiungere o modificare un parametro, scegliere il pulsante **Modifica**. Immettere o modificare il valore del parametro nella griglia **Parametri**. Al termine, scegliere il pulsante **Salva**.

    ![][2]

1. La casella di testo **Aggiorna applicazione** consente di specificare se l'operazione di pubblicazione è un aggiornamento. Le operazioni di pubblicazione di aggiornamento differiscono dalle normali operazioni di pubblicazione. Per un elenco delle differenze, vedere l'articolo relativo all'[aggiornamento di un'applicazione dell'infrastruttura di servizi](service-fabric-application-upgrade.md). Per configurare le impostazioni di aggiornamento, scegliere il collegamento **Configura impostazioni di aggiornamento**. Viene visualizzato l'editor del parametro di aggiornamento. Per altre informazioni sui parametri di aggiornamento, vedere l'articolo relativo alla [configurazione dell'aggiornamento di un'applicazione dell'infrastruttura di servizi](service-fabric-visualstudio-configure-upgrade.md).

1. Scegliere il pulsante **Versioni manifesto** per visualizzare la finestra di dialogo **Modifica versioni**. Affinché l'aggiornamento sia effettivo, è necessario aggiornare le versioni del servizio e dell'applicazione. Per informazioni sull'impatto delle versioni del manifesto del servizio e dell'applicazione su un processo di aggiornamento, vedere l'[esercitazione sull'aggiornamento di un'applicazione dell'infrastruttura di servizi](service-fabric-application-upgrade-tutorial.md).

    ![][3]

    Se le versioni del servizio e dell'applicazione usano il controllo delle versioni semantico, ad esempio 1.0.0, o valori numerici in formato 1.0.0.0, selezionare l'opzione **Aggiorna automaticamente le versioni del servizio e dell'applicazione**. Se si seleziona questa opzione, i numeri di versione del servizio e dell'applicazione vengono aggiornati automaticamente ogni volta che viene aggiornata la versione di un codice, una configurazione o un pacchetto dati. Se si preferisce modificare le versioni manualmente, deselezionare la casella di controllo per disabilitare questa funzionalità.

    >[AZURE.NOTE]Per visualizzare tutte le voci del pacchetto di un progetto Actor, compilare innanzitutto il progetto in modo da generare le voci nei file manifesto del servizio.

1. Dopo aver specificato tutte le impostazioni necessarie, scegliere il pulsante **Pubblica** per pubblicare l'applicazione nel cluster dell'infrastruttura di servizi selezionato. Le impostazioni specificate vengono applicate al processo di pubblicazione.

## Passaggi successivi

Per informazioni su come automatizzare il processo di pubblicazione in un ambiente di integrazione continua, vedere l'articolo relativo alla [configurazione dell'integrazione continua nell'infrastruttura di servizi](service-fabric-set-up-continuous-integration.md).


[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png

<!---HONumber=Nov15_HO4-->