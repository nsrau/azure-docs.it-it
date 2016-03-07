<properties
   pageTitle="Creare un cluster di Service Fabric dal portale di Azure | Microsoft Azure"
   description="Creare un cluster di Service Fabric dal portale di Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/12/2016"
   ms.author="chackdan"/>


# Creare un cluster di Service Fabric dal portale di Azure

Questa pagina illustra come configurare un cluster di Azure Service Fabric. È necessario che nella sottoscrizione sia incluso un numero di core sufficienti per distribuire le VM IaaS che costituiranno il cluster.


## Creare il cluster

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Fare clic su **+ Nuovo** per aggiungere un nuovo modello di risorsa. Cercare il modello in **Marketplace**, nella sezione **Tutto**. È denominato **Cluster di Service Fabric**.

    a. Al primo livello fare clic su **Marketplace**.

    b. Nella sezione **Tutto** fare clic su **Fabric** e premere Invio. Il filtro automatico talvolta non funziona; verificare quindi di aver premuto Invio. ![Schermata della ricerca del modello di cluster di Service Fabric nel portale di Azure.][SearchforServiceFabricClusterTemplate]

3. Selezionare **Cluster di Service Fabric** nell'elenco.

4. Passare al pannello **Cluster di Service Fabric**, fare clic su **Crea** e immettere i dettagli del cluster.

5. In **Crea un nuovo gruppo di risorse** assegnare al gruppo di risorse lo stesso nome del cluster. In questo modo sarà possibile trovarlo più facilmente in un secondo momento, soprattutto se si tenterà di apportare modifiche alla distribuzione o di eliminare il cluster.

    >[AZURE.NOTE] Anche se è possibile decidere di usare un gruppo di risorse esistente, è meglio crearne uno nuovo, in modo da poter eliminare facilmente i cluster non più necessari.

 	![Schermata della creazione di un nuovo gruppo di risorse.][CreateRG]

6. Assicurarsi di selezionare la **sottoscrizione** in cui si vuole distribuire il cluster, soprattutto se sono presenti più sottoscrizioni.

7. Selezionare una **località** nell'elenco a discesa. Il valore predefinito è **Stati Uniti occidentali**.

8. Configurare il **Tipo di nodo**. I tipi di nodi possono essere paragonati ai "ruoli" nei servizi cloud, poiché definiscono le dimensioni delle VM, il numero di VM e le relative proprietà. Il cluster può avere più di un tipo di nodo, ma il tipo di nodo primario (il primo che si definisce nel portale) deve essere costituito da almeno cinque VM. Per configurare il tipo di nodo:

	a. Selezionare le dimensioni della VM e il piano tariffario necessari. Il valore predefinito è D4 Standard. Se tuttavia si prevede di usare questo cluster solo per testare l'applicazione, è possibile selezionare una VM D2 o di dimensioni inferiori.

	b. Scegliere il numero di VM. È possibile aumentare o ridurre il numero di VM in un tipo di nodo anche in un secondo momento, ma il primo tipo di nodo deve essere sempre costituito da almeno cinque VM.

	c. Scegliere un nome per il tipo di nodo (da 1 a 12 caratteri, contenenti solo lettere e numeri).

	d. Scegliere i valori di **Nome utente** e **Password** per il desktop remoto della VM.

	e. Se nel cluster sono necessari più tipi di nodo, tenere presenti le seguenti problematiche (se si prevede di distribuire un cluster con un solo tipo di nodo, andare al passaggio 9).

	* Si supponga di voler distribuire un'applicazione contenente un servizio front-end e un servizio back-end e di voler inserire il servizio front-end in VM più piccole (ad esempio, di dimensioni A2, D2 e così via), con porte aperte a Internet, e di voler invece inserire il servizio back-end, a elevato utilizzo di calcolo, in VM più grandi (ad esempio, di dimensioni D4, D6, D12 e così via), senza connessione a Internet.

	* Sebbene sia possibile inserire entrambi i servizi in un unico tipo di nodo, è consigliabile inserirli in un cluster con due tipi di nodo, poiché ognuno di essi può avere proprietà distinte, ad esempio la connettività Internet, le dimensioni delle VM e il numero di VM, che è possibile ridimensionare in modo indipendente.

	* Definire prima il tipo di nodo che conterrà almeno 5 VM. Gli altri tipi di nodo possono contenere anche una sola VM.

  	![Schermata della creazione di un tipo di nodo.][CreateNodeType]

9. Se si prevede di distribuire le applicazioni direttamente nel cluster, aggiungere le porte che si vuole aprire per le applicazioni nel tipo di nodo **Porte applicazione** (o in tipi di nodo creati in precedenza). È possibile aggiungere porte al tipo di nodo anche in un secondo momento modificando il servizio di bilanciamento del carico associato a questo tipo di nodo (è necessario aggiungere un probe, da aggiungere quindi alle regole del servizio di bilanciamento del carico). È più semplice eseguire ora questa operazione, perché l'automazione del portale aggiungerà al servizio di bilanciamento del carico le regole e i probe necessari.

	a. È possibile trovare le porte delle applicazioni nei manifesti del servizio che fanno parte del pacchetto dell'applicazione. Passare a ogni applicazione, aprire i manifesti del servizio e prendere nota di tutti gli endpoint "input" necessari alle applicazioni per comunicare con l'esterno.

	b. Aggiungere tutte le porte, separate da virgole, nel campo **Endpoint di input dell'applicazione**. Per impostazione predefinita, l'endpoint di connessione client TCP è 19000 e non è necessario specificarlo. Per l'applicazione di esempio WordCount, è necessario aprire la porta 83. Questo valore è disponibile nel file servicemanifest.xml nel pacchetto dell'applicazione (è possibile che siano presenti più file servicemanifest.xml).

    c. Poiché la maggior parte delle applicazioni di esempio usa le porte 80 e 8081, aggiungerle se si prevede di distribuire gli esempi nel cluster. ![Porte][Ports]

10. Non è necessario configurare le proprietà relative alla posizione, perché una proprietà relativa alla posizione predefinita "NodeTypeName" viene aggiunta automaticamente dal sistema. Se necessario, è possibile aggiungerne altre.

## Configurare la sicurezza

A questo punto, Service Fabric supporta la sicurezza dei cluster solo tramite un certificato X.509. Prima di iniziare questo processo, quindi, è necessario caricare il certificato nell'insieme di credenziali delle chiavi. Per altre informazioni su come eseguire questa operazione, vedere l'articolo [Proteggere un cluster di Service Fabric](service-fabric-cluster-security.md).

È consigliabile proteggere il cluster, anche se facoltativo. Se si sceglie di non proteggere il cluster, impostare il campo **Modalità di sicurezza** su **Nessuna**.

Altre istruzioni e considerazioni di sicurezza sono disponibili nell'articolo [Proteggere un cluster di Service Fabric](service-fabric-cluster-security.md).

![Schermata delle configurazioni di sicurezza nel portale di Azure.][SecurityConfigs]

## Facoltativo: configurare la diagnostica

Per impostazione predefinita, la diagnostica è abilitata nel cluster come supporto per la risoluzione dei problemi. Per disabilitare la diagnostica:

1. Passare al pannello **Configurazioni di diagnostica**.

2. Impostare il campo **Stato** su **Disattivato**.

## Facoltativo: configurare le impostazioni del cluster di Service Fabric

Si tratta di un'opzione avanzata che consente di modificare le impostazioni predefinite del cluster di Service Fabric. È consigliabile non modificare le impostazioni predefinite, a meno che non sia indispensabile per l'applicazione e/o il cluster.

## Completare la creazione del cluster

Per completare la creazione del cluster, fare clic su **Riepilogo** per visualizzare le configurazioni fornite oppure scaricare il modello di Gestione risorse di Azure da usare per distribuire il cluster. Dopo avere specificato le impostazioni obbligatorie, verrà abilitato il pulsante **Crea** e sarà possibile avviare il processo di creazione del cluster.

È possibile visualizzare lo stato di avanzamento del processo di creazione nell'area delle notifiche: fare clic sull'icona a forma di campana accanto alla barra di stato nell'angolo superiore destro della schermata. Se durante la creazione del cluster è stata selezionata l'opzione **Aggiungere a Schermata iniziale**, la voce **Distribuzione di un cluster di Service Fabric** verrà aggiunta alla schermata iniziale.

![Schermata iniziale con la voce "Distribuzione di un cluster di Service Fabric" visualizzata.][Notifications]

## Visualizzare lo stato del cluster

Al termine del processo di distribuzione è possibile esaminare il cluster nel portale.

1. Selezionare **Sfoglia** e fare clic sulla risorsa **Cluster di Service Fabric**.

2. Trovare e fare clic sul cluster. ![Schermata della ricerca del cluster nel portale.][BrowseCluster]

3. È ora possibile visualizzare i dettagli del cluster nel dashboard, incluso l'indirizzo IP pubblico del cluster. Passando il puntatore su **Indirizzo IP pubblico del cluster** verranno visualizzati gli Appunti su cui è possibile fare clic per copiare l'indirizzo. ![Schermata dei dettagli del cluster nel dashboard.][ClusterDashboard]

  La sezione relativa al monitoraggio dei nodi nel pannello del dashboard del cluster indica il numero di VM integre e non integre. È possibile trovare altri dettagli sull'integrità del cluster nell'articolo [Introduzione al monitoraggio dell'integrità di Service Fabric](service-fabric-health-introduction.md).

>[AZURE.NOTE] I cluster di Service Fabric richiedono che un certo numero di nodi sia attivo in ogni momento allo scopo di mantenere la disponibilità e lo stato, ossia per "mantenere il quorum". Di conseguenza, in genere non è consigliabile spegnere tutti i computer nel cluster a meno che non sia stato prima eseguito un [backup completo dello stato](service-fabric-reliable-services-backup-restore.md).

## Connettersi al cluster e distribuire un'applicazione

Completata la configurazione del cluster, è ora possibile connettersi e iniziare la distribuzione delle applicazioni. Iniziare avviando Windows PowerShell in un computer con Service Fabric SDK installato. Per connettersi al cluster, eseguire quindi uno dei seguenti set di comandi di PowerShell, a seconda che si sia creato un cluster sicuro o non sicuro.

- Opzione 1: Connettersi a un cluster non sicuro.

    ```powershell
    Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 -KeepAliveIntervalInSec 10
    ```

- Opzione 2: Connettersi a un cluster sicuro.

    1. Eseguire quanto segue per configurare il certificato sul computer che si userà per eseguire il comando di PowerShell "Connect-serviceFabricCluster".

        ```powershell
        Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
                -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
                -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
        ```

    2. Eseguire il comando di PowerShell seguente per connettersi a un cluster sicuro. I dettagli del certificato sono gli stessi specificati nel portale.

        ```powershell
        Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
                  -KeepAliveIntervalInSec 10 `
                  -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
                  -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
                  -StoreLocation CurrentUser -StoreName My
        ```

        Ad esempio, il comando di PowerShell precedente sarà simile a quello riportato di seguito.

        ```powershell
        Connect-serviceFabricCluster -ConnectionEndpoint sfcluster4doc.westus.cloudapp.azure.com:19000 `
                  -KeepAliveIntervalInSec 10 `
                  -X509Credential -ServerCertThumbprint C179E609BBF0B227844342535142306F3913D6ED `
                  -FindType FindByThumbprint -FindValue C179E609BBF0B227844342535142306F3913D6ED `
                  -StoreLocation CurrentUser -StoreName My
        ```

La connessione è stata stabilita. Eseguire quindi i comandi seguenti per distribuire l'applicazione, sostituendo i percorsi indicati con quelli appropriati per il computer in uso. L'esempio seguente distribuisce l'applicazione di esempio per il conteggio delle parole.

1. Copiare il pacchetto nel cluster a cui ci si è connessi nel passaggio precedente.

    ```powershell
    $applicationPath = "C:\VS2015\WordCount\WordCount\pkg\Debug"
    ```

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $applicationPath -ApplicationPackagePathInImageStore "WordCount" -ImageStoreConnectionString fabric:ImageStore
    ```
2. Registrare il tipo di applicazione con Service Fabric.

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore "WordCount"
    ```

3. Creare una nuova istanza nel tipo di applicazione appena registrato.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/WordCount -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0.0
    ```

4. Aprire ora il browser preferito e connettersi all'endpoint su cui è in ascolto l'applicazione. Per l'applicazione di esempio WordCount, l'URL sarà il seguente:

    http://sfcluster4doc.westus.cloudapp.azure.com:31000

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## RDP in un'istanza di un set di scalabilità di macchine virtuali o in un nodo del cluster 

Ognuno dei tipi di nodo specificati nel cluster corrisponde a un set di scalabilità di macchine virtuali configurato. Per i dettagli fare riferimento alla pagina relativa a [come usare RDP in un'istanza di un set di scalabilità di macchine virtuali](service-fabric-cluster-nodetypes.md).

## Passaggi successivi

- [Gestione delle applicazioni di Service Fabric in Visual Studio](service-fabric-manage-application-in-visual-studio.md)
- [Sicurezza di un cluster di Service Fabric](service-fabric-cluster-security.md)
- [Introduzione al monitoraggio dell'integrità di Service Fabric](service-fabric-health-introduction.md)
- [Come usare RDP nell'istanza di un set di scalabilità di macchine virtuali](service-fabric-cluster-nodetypes.md)

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[Ports]: ./media/service-fabric-cluster-creation-via-portal/ports.png
[SFConfigurations]: ./media/service-fabric-cluster-creation-via-portal/SFConfigurations.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[BrowseCluster]: ./media/service-fabric-cluster-creation-via-portal/browse.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[SecureConnection]: ./media/service-fabric-cluster-creation-via-portal/SecureConnection.png

<!---HONumber=AcomDC_0224_2016-->