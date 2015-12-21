<properties
    pageTitle="Pubblicare un'applicazione in un cluster remoto con Visual Studio | Microsoft Azure"
    description="Informazioni sui passaggi necessari per pubblicare un'applicazione in un cluster remoto dell'infrastruttura di servizi usando Visual Studio."
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="12/06/2015"
    ms.author="cawa" />

# Pubblicare un'applicazione in un cluster remoto tramite Visual Studio

L'estensione Service Fabric per Visual Studio offre un modo semplice, ripetibile e configurabile tramite script per pubblicare un Service Fabric Cluster.

## Comprendere gli elementi necessari per la pubblicazione

### Deploy-FabricApplication.ps1

Script di PowerShell che usa un percorso del profilo di pubblicazione come parametro per la pubblicazione di applicazioni dell'infrastruttura di servizi. Lo script fa parte dell'applicazione ed è possibile modificarlo in base alle esigenze dell'applicazione.

### Profili di pubblicazione

Una cartella nel progetto dell'applicazione di Service Fabric denominata **PublishProfiles** contiene i file XML che archiviano informazioni fondamentali per la pubblicazione di un'applicazione, ad esempio: parametri di connessione di Service Fabric Cluster, percorso di un file dei parametri dell'applicazione e impostazioni di aggiornamento.

Per impostazione predefinita, l'applicazione include due profili di pubblicazione: Local.xml e Cloud.xml. È possibile aggiungere altri profili copiando e incollando uno dei file predefiniti.

### File di parametri dell'applicazione

Una cartella nel progetto dell'applicazione di Service Fabric denominata **ApplicationParameters** contiene i file XML per i valori dei parametri del manifesto dell'applicazione specificati dall'utente. I file manifesto dell'applicazione possono essere parametrizzati in modo che si possano usare diversi valori per le impostazioni di distribuzione. Per altre informazioni sulla parametrizzazione dell'applicazione, vedere l'articolo relativo alla [gestione di più ambienti in Service Fabric](service-fabric-manage-multiple-environment-app-configuration.md).

>[AZURE.NOTE]Per i servizi Actor, è necessario compilare il progetto prima di generare i parametri nel file manifesto.

## Pubblicare un'applicazione usando la finestra di dialogo Pubblica applicazione di Service Fabric

I passaggi seguenti illustrano come pubblicare un'applicazione usando la finestra di dialogo **Pubblica applicazione di Service Fabric** fornita dagli strumenti di Visual Studio per Service Fabric.

1. Nel menu di scelta rapida del progetto dell'applicazione di Service Fabric scegliere **Pubblica** per visualizzare la finestra di dialogo **Pubblica applicazione di Service Fabric**.

    ![][0]

    Il file selezionato nell'elenco a discesa **Profilo di destinazione** si trova nella stessa posizione di tutte le impostazioni, ad eccezione di **Versioni manifesto**. È possibile riutilizzare un profilo esistente o crearne uno nuovo selezionando **<Gestione profili...>** nell'elenco a discesa **Profilo di destinazione**. Dopo aver scelto un profilo di pubblicazione, il relativo contenuto viene visualizzato nei campi corrispondenti della finestra di dialogo. Per salvare le modifiche in qualsiasi momento, scegliere il collegamento **Salva profilo**.

2. Nella sezione **Endpoint di connessione** è possibile specificare un endpoint di pubblicazione del Service Fabric Cluster locale o remoto. Per aggiungere o modificare l'endpoint di connessione, scegliere il pulsante **Seleziona...**. Nella finestra di dialogo **Seleziona Service Fabric Cluster** vengono visualizzati gli endpoint di connessione disponibili del Service Fabric Cluster in cui è possibile eseguire la pubblicazione in base alle sottoscrizioni di Azure. Se non è già stato effettuato l'accesso a Visual Studio, verrà richiesto di farlo.

    La finestra di dialogo di selezione cluster permette di scegliere tra le sottoscrizioni e i cluster disponibili.

    ![][1]

    >[AZURE.NOTE]Se si vuole pubblicare in un endpoint arbitrario (ad esempio un party cluster), vedere la sezione **Pubblicazione in un endpoint di cluster arbitrario** riportata di seguito.

    Dopo aver scelto un endpoint, Visual Studio convalida la connessione al cluster dell'infrastruttura di servizi selezionato. Se il cluster non è sicuro, Visual Studio non può connettersi immediatamente al cluster. Tuttavia, se il cluster è sicuro, è necessario installare un certificato nel computer locale prima di procedere. Per altre informazioni, vedere l'articolo relativo alla [configurazione di connessioni protette](service-fabric-visualstudio-configure-secure-connections.md). Al termine, scegliere il pulsante **OK**. Il cluster selezionato viene visualizzato nella finestra di dialogo **Pubblica applicazione di Service Fabric**.

3. L'elenco a discesa **File dei parametri dell'applicazione** consente di passare a un file dei parametri dell'applicazione. Un file dei parametri dell'applicazione contiene valori specificati dall'utente per i parametri nel file manifesto dell'applicazione. Per aggiungere o modificare un parametro, scegliere il pulsante **Modifica**. Immettere o modificare il valore del parametro nella griglia **Parametri**. Al termine, scegliere il pulsante **Salva**.

    ![][2]

4. La casella di testo **Aggiorna applicazione** consente di specificare se l'operazione di pubblicazione è un aggiornamento. Le operazioni di pubblicazione di aggiornamento differiscono dalle normali operazioni di pubblicazione. Per un elenco delle differenze, vedere [Aggiornamento di un'applicazione di Service Fabric](service-fabric-application-upgrade.md). Per configurare le impostazioni di aggiornamento, scegliere il collegamento **Configura impostazioni di aggiornamento**. Viene visualizzato l'editor del parametro di aggiornamento. Per altre informazioni sui parametri di aggiornamento, vedere l'articolo relativo alla [configurazione dell'aggiornamento di un'applicazione di Service Fabric](service-fabric-visualstudio-configure-upgrade.md).

5. Scegliere il pulsante **Versioni manifesto** per visualizzare la finestra di dialogo **Modifica versioni**. Affinché l'aggiornamento sia effettivo, è necessario aggiornare le versioni del servizio e dell'applicazione. Per informazioni sull'impatto delle versioni del manifesto del servizio e dell'applicazione su un processo di aggiornamento, vedere l'[esercitazione sull'aggiornamento di un'applicazione di Service Fabric](service-fabric-application-upgrade-tutorial.md).

    ![][3]

    Se le versioni del servizio e dell'applicazione usano il controllo delle versioni semantico, ad esempio 1.0.0, o valori numerici in formato 1.0.0.0, selezionare l'opzione **Aggiorna automaticamente le versioni del servizio e dell'applicazione**. Se si seleziona questa opzione, i numeri di versione del servizio e dell'applicazione vengono aggiornati automaticamente ogni volta che viene aggiornata la versione di un codice, una configurazione o un pacchetto dati. Se si preferisce modificare le versioni manualmente, deselezionare la casella di controllo per disabilitare questa funzionalità.

    >[AZURE.NOTE]Per visualizzare tutte le voci del pacchetto di un progetto Actor, compilare innanzitutto il progetto in modo da generare le voci nei file manifesto del servizio.

6. Dopo aver specificato tutte le impostazioni necessarie, scegliere il pulsante **Pubblica** per pubblicare l'applicazione nel Service Fabric Cluster selezionato. Le impostazioni specificate vengono applicate al processo di pubblicazione.

## Pubblicazione in un endpoint di cluster arbitrario (inclusi i party cluster)

L'esperienza di pubblicazione di Visual Studio è ottimizzata per la pubblicazione in cluster remoti associati a una delle sottoscrizioni di Azure. Tuttavia, è possibile eseguire la pubblicazione in un endpoint arbitrario (ad esempio, party cluster di Service Fabric) modificando direttamente il profilo di pubblicazione XML. Come indicato in precedenza, per impostazione predefinita vengono forniti due profili di pubblicazione, **Local.xml** e **Cloud.xml**, ma è possibile creare profili aggiuntivi per ambienti diversi. Ad esempio, è possibile creare un profilo per la pubblicazione nei party cluster denominato **Party.xml**.

Se ci si connette a un cluster non protetto, è necessario unicamente l'endpoint di connessione del cluster, ad esempio `partycluster1.eastus.cloudapp.azure.com:19000`. In tal caso, l'endpoint di connessione nel profilo di pubblicazione sarà simile al seguente:

```XML
<ClusterConnectionParameters ConnectionEndpoint="partycluster1.eastus.cloudapp.azure.com:19000" />
```

  Se ci si connette a un cluster protetto, è necessario anche fornire i dettagli del certificato client dell'archivio locale da usare per l'autenticazione. Per altre informazioni, vedere [Configurare connessioni protette a un Service Fabric Cluster](service-fabric-visualstudio-configure-secure-connections.md).

  Dopo aver configurato il profilo di pubblicazione, è possibile farvi riferimento nella finestra di dialogo di pubblicazione, come illustrato di seguito.

  ![Nuovo profilo di pubblicazione nella finestra di dialogo di pubblicazione][4]

  Si noti che, in questo caso, il nuovo profilo di pubblicazione punta a uno dei file dei parametri dell'applicazione predefiniti. Questo va bene se si vuole pubblicare la stessa configurazione dell'applicazione in un certo numero di ambienti. Al contrario, nei casi in cui sono richieste diverse configurazioni per ogni ambiente da pubblicare, è opportuno creare un file dei parametri dell'applicazione corrispondente.

## Passaggi successivi

Per informazioni su come automatizzare il processo di pubblicazione in un ambiente di integrazione continua, vedere l'articolo relativo alla [configurazione dell'integrazione continua in Service Fabric](service-fabric-set-up-continuous-integration.md).


[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png
[4]: ./media/service-fabric-publish-app-remote-cluster/publish-to-party-cluster.png

<!---HONumber=AcomDC_1210_2015-->