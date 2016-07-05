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
   ms.date="05/02/2016"
   ms.author="chackdan"/>


# Creare un cluster di Service Fabric dal portale di Azure

Questa pagina illustra come configurare un cluster di Azure Service Fabric. È necessario che nella sottoscrizione sia incluso un numero di core sufficienti per distribuire le VM IaaS che costituiranno il cluster.


## Cercare la risorsa cluster di Service Fabric

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Fare clic su **+ Nuovo** per aggiungere un nuovo modello di risorsa. Cercare il modello in **Marketplace**, nella sezione **Tutto**. È denominato **Cluster di Service Fabric**.

    a. Al primo livello fare clic su **Marketplace**.

    b. Nella sezione **Tutto** immettere "Fabric" e premere INVIO. Il filtro automatico talvolta non funziona, verificare quindi di aver premuto INVIO. ![Schermata della ricerca del modello di cluster di Service Fabric nel portale di Azure.][SearchforServiceFabricClusterTemplate]

3. Selezionare **Cluster di Service Fabric** nell'elenco.

4. Passare al pannello **Cluster di Service Fabric** e fare clic su **Crea**.

5. Verrà visualizzato un pannello **Crea cluster di Service Fabric** in cui sono elencati i quattro passaggi.

## Passaggio 1: Informazioni di base

Nel pannello Informazioni di base è necessario fornire i dettagli di base per il cluster.

1. Immettere il nome del cluster.

2. Scegliere il **Nome utente** e la **Password** per il desktop remoto della macchina virtuale.

3. Assicurarsi di selezionare la **sottoscrizione** in cui si vuole distribuire il cluster, soprattutto se sono presenti più sottoscrizioni.

4. Creare un **nuovo gruppo di risorse** assegnandogli lo stesso nome del cluster, per poterlo trovare più facilmente in seguito, soprattutto quando si cerca di apportare modifiche alla distribuzione e/o eliminare il cluster.

    >[AZURE.NOTE] Anche se è possibile decidere di usare un gruppo di risorse esistente, è meglio crearne uno nuovo, in modo da poter eliminare facilmente i cluster non più necessari.

 	![Schermata della creazione di un nuovo gruppo di risorse.][CreateRG]


5. Selezionare una **località** nell'elenco a discesa. Il valore predefinito è **Stati Uniti occidentali**. Premere OK.

## Passaggio 2: Configurare il cluster

10. Prima di tutto è importante sapere cos'è un **tipo di nodo**. I tipi di nodi possono essere paragonati ai "ruoli" nei servizi cloud, poiché definiscono le dimensioni delle VM, il numero di VM e le relative proprietà. Il cluster può avere più di un tipo di nodo, ma il tipo di nodo primario, ovvero il primo che si definisce nel portale, deve essere costituito da almeno cinque macchine virtuali. In questo tipo di nodo si trovano i servizi di sistema di Service Fabric. Per stabilire la necessità di più tipi di nodo, tenere presente quanto segue.

	* L'applicazione che si vuole distribuire contiene un servizio front-end e un servizio back-end. Si vuole inserire il servizio front-end in macchine virtuali più piccole, ad esempio con dimensioni D2, con porte aperte a Internet e il servizio back-end, a elevato utilizzo di calcolo, in macchine virtuali più grandi, ad esempio con dimensioni D4, D6, D15 e così via, senza connessione Internet.

	* Sebbene sia possibile inserire entrambi i servizi in un unico tipo di nodo, è consigliabile inserirli in un cluster con due tipi di nodo, poiché ognuno di essi può avere proprietà distinte, ad esempio la connettività Internet, le dimensioni delle VM e il numero di VM, che è possibile ridimensionare in modo indipendente.

	* Definire prima il tipo di nodo che conterrà almeno 5 VM. Gli altri tipi di nodo possono contenere anche una sola VM.

13.  Per configurare il tipo di nodo:

	a. Scegliere un nome per il tipo di nodo (da 1 a 12 caratteri, contenenti solo lettere e numeri).

	b. Le dimensioni minime delle macchine virtuali per il tipo di nodo primario sono determinate dal livello di durabilità scelto per il cluster. Il valore predefinito per il livello di durabilità è Minimo. Per altre informazioni su come scegliere l'affidabilità e la durabilità del cluster di Service Fabric, vedere il relativo [articolo](service-fabric-cluster-capacity.md).

	b. Selezionare le dimensioni della macchina virtuale e il piano tariffario. Il valore predefinito è D4 Standard. Se tuttavia si prevede di usare questo cluster solo per testare l'applicazione, è possibile selezionare una VM D2 o di dimensioni inferiori.

	c. Il numero minimo di macchine virtuali per il tipo di nodo primario è determinato dal livello di affidabilità scelto per il cluster. Il valore predefinito per il livello di affidabilità è Medio. Altre informazioni su come [scegliere l'affidabilità e la durabilità del cluster di Service Fabric](service-fabric-cluster-capacity.md).

	c. Scegliere il numero di macchine virtuali per il tipo di nodo. È possibile aumentare o ridurre il numero di macchine virtuali in un tipo di nodo in un secondo momento ma, per il tipo di nodo primario, il minimo è determinato dal livello di affidabilità che si è scelto. Gli altri tipi di nodo possono avere un minimo di 1 VM.


  	![Schermata della creazione di un tipo di nodo.][CreateNodeType]

9. Se si prevede di distribuire le applicazioni direttamente nel cluster, aggiungere le porte che si vuole aprire per le applicazioni in un tipo di nodo **Porte applicazione** o in tipi di nodo creati in precedenza. È possibile aggiungere porte al tipo di nodo anche in un secondo momento modificando il servizio di bilanciamento del carico associato a questo tipo di nodo (è necessario aggiungere un probe, da aggiungere quindi alle regole del servizio di bilanciamento del carico). È più semplice eseguire ora questa operazione, perché l'automazione del portale aggiungerà al servizio di bilanciamento del carico le regole e i probe necessari.

	a. È possibile trovare le porte delle applicazioni nei manifesti del servizio che fanno parte del pacchetto dell'applicazione. Passare a ogni applicazione, aprire i manifesti del servizio e prendere nota di tutti gli endpoint "input" necessari alle applicazioni per comunicare con l'esterno.

	b. Aggiungere tutte le porte, separate da virgole, nel campo degli **endpoint di input dell'applicazione**. Per impostazione predefinita, l'endpoint di connessione client TCP è 19000 e non è necessario specificarlo. Per l'applicazione di esempio WordCount, è necessario aprire la porta 83. Questo valore è disponibile nel file servicemanifest.xml nel pacchetto dell'applicazione (è possibile che siano presenti più file servicemanifest.xml).

    c. Dato che la maggior parte delle applicazioni di esempio usa le porte 80 e 8081, se si prevede di distribuire esempi nel cluster è necessario aggiungerle. ![Porte][Ports]

10. Non è necessario configurare le **proprietà di selezione host**, perché una proprietà di selezione host predefinita di "NodeTypeName" viene aggiunta dal sistema. Se necessario, è possibile aggiungerne altre.

11. Non è necessario configurare le **proprietà di capacità**, ma è consigliabile farlo, perché possono essere usate nelle applicazioni per segnalare il carico al sistema e influire così sulle scelte di selezione host e bilanciamento delle risorse operate dal sistema nel cluster di Service Fabric. Per altre informazioni sul bilanciamento delle risorse in Service Fabric, vedere [questo articolo](service-fabric-cluster-resource-manager-architecture.md).

12. Continuare la procedura descritta per tutti i tipi di nodo.

14. Configurare la **diagnostica** del cluster. Per impostazione predefinita, la diagnostica è abilitata nel cluster come supporto per la risoluzione dei problemi. Per disabilitare la diagnostica, impostare lo **stato** su **No**. **Non** è consigliabile disattivare la diagnostica.

15. Facoltativamente, è possibile configurare le **impostazioni cluster** di Service Fabric. Si tratta di un'opzione avanzata che consente di modificare le impostazioni predefinite del cluster di Service Fabric. È consigliabile non modificare le impostazioni predefinite, a meno che non sia indispensabile per l'applicazione e/o il cluster.

## Passaggio 3: Configurare la sicurezza

Scenari e considerazioni sulla sicurezza sono disponibili in [Proteggere un cluster di Service Fabric](service-fabric-cluster-security.md). Al momento, Service Fabric supporta la protezione dei cluster solo mediante certificato X509; per ulteriori informazioni, fare riferimento a [Proteggere un cluster di Service Fabric in Azure con i certificati](service-fabric-secure-azure-cluster-with-certs.md).

È consigliabile proteggere il cluster, anche se facoltativo. Se si sceglie di non proteggere il cluster, impostare la **modalità di sicurezza** su **Annulla protezione**. Nota: **non** è possibile aggiornare un cluster da non protetto a protetto in un secondo momento.

![Schermata delle configurazioni di sicurezza nel portale di Azure.][SecurityConfigs]


## Passaggio 4: Completare la creazione del cluster

Per completare la creazione del cluster, fare clic su **Riepilogo** per visualizzare le configurazioni fornite oppure scaricare il modello di Azure Resource Manager da utilizzare per distribuire il cluster. Dopo aver specificato le impostazioni obbligatorie, verrà abilitato il pulsante **OK**, che permette di avviare il processo di creazione del cluster.

È possibile visualizzare lo stato di avanzamento del processo di creazione nell'area delle notifiche: fare clic sull'icona a forma di campana accanto alla barra di stato nell'angolo superiore destro della schermata. Se durante la creazione del cluster si è fatto clic su **Aggiungi alla Schermata iniziale**, verrà aggiunta la voce **Distribuzione del cluster di Service Fabric** alla **schermata iniziale**.

![Schermata iniziale con la voce "Distribuzione di un cluster di Service Fabric" visualizzata.][Notifications]

## Visualizzare lo stato del cluster

Al termine della creazione del cluster è possibile esaminare il cluster nel portale.

1. Selezionare **Sfoglia** e fare clic su **Cluster di Service Fabric**.

2. Trovare il cluster e selezionarlo. ![Schermata della ricerca del cluster nel portale.][BrowseCluster]

3. È ora possibile visualizzare i dettagli del cluster nel dashboard, incluso l'indirizzo IP pubblico del cluster. Passando il puntatore sull'**indirizzo IP pubblico del cluster** verranno visualizzati gli Appunti su cui è possibile fare clic per copiare l'indirizzo. ![Schermata dei dettagli del cluster nel dashboard.][ClusterDashboard]

  La sezione relativa al **monitoraggio dei nodi** nel pannello del dashboard del cluster indica il numero di macchine virtuali integre e di quelle non integre. Per altre informazioni sull'integrità del cluster, vedere [Introduzione al monitoraggio dell'integrità di Service Fabric](service-fabric-health-introduction.md).

>[AZURE.NOTE] I cluster di Service Fabric richiedono che un certo numero di nodi sia attivo in ogni momento allo scopo di mantenere la disponibilità e lo stato, ossia per "mantenere il quorum". Di conseguenza, in genere non è sicuro spegnere tutti i computer del cluster se prima non è stato eseguito un [backup completo dello stato](service-fabric-reliable-services-backup-restore.md).

## Connettersi al cluster e distribuire un'applicazione

Completata la configurazione del cluster, è ora possibile connettersi e iniziare la distribuzione delle applicazioni. Iniziare avviando Windows PowerShell in un computer con Service Fabric SDK installato. Per connettersi al cluster, eseguire quindi uno dei seguenti set di comandi di PowerShell, a seconda che si sia creato un cluster sicuro o non sicuro.

### Connettersi a un cluster non sicuro

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 -KeepAliveIntervalInSec 10
```

### Connettersi a un cluster sicuro

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

### Distribuire l'app
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

## Connessione remota a un'istanza di set di scalabilità di macchine virtuali o a un nodo del cluster

Ognuno dei tipi di nodo specificati nel cluster corrisponde a un set di scalabilità di macchine virtuali configurato. Per i dettagli, fare riferimento a [Connessione remota a un'istanza di set di scalabilità di macchine virtuali](service-fabric-cluster-nodetypes.md#remote-connect-to-a-vm-scale-set-instance-or-a-cluster-node).

## Passaggi successivi

Dopo aver creato un cluster, è possibile reperire altre informazioni sulla relativa protezione e sulla distribuzione di app:
- [Gestione delle applicazioni di Service Fabric in Visual Studio](service-fabric-manage-application-in-visual-studio.md)
- [Sicurezza di un cluster di Service Fabric](service-fabric-cluster-security.md)
- [Introduzione al monitoraggio dell'integrità di Service Fabric](service-fabric-health-introduction.md)


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

<!---HONumber=AcomDC_0622_2016-->