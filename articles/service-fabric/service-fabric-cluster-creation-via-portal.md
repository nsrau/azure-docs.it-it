<properties
   pageTitle="Configurazione di cluster di infrastruttura di servizi dal portale di Azure | Microsoft Azure"
   description="Configurazione di cluster di infrastruttura di servizi dal portale di Azure."
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
   ms.date="11/19/2015"
   ms.author="chackdan"/>

# Configurazione di cluster di infrastruttura di servizi dal portale di Azure

Questa pagina illustra come configurare un cluster di infrastruttura di servizi. Si presuppone che la sottoscrizione preveda un numero di core sufficienti per distribuire le VM IaaS che costituiranno questo cluster.


## Creazione del cluster

1. Accedere al portale di Azure all'indirizzo [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal).

2. Fare clic su **+ Nuovo** per aggiungere un nuovo modello di risorsa. Cercare il modello nel Marketplace sotto Tutto: il nome è **Cluster di infrastruttura di servizi**. Per passare a **Tutto**, fare clic nella categoria di primo livello Marketplace > e quindi cercare "Infrastruttura" sotto "Tutto" e premere INVIO. Poiché a volte il filtro automatico non funziona, assicurarsi di **premere INVIO**. ![Ricerca del modello di cluster di infrastruttura di servizi][SearchforServiceFabricClusterTemplate]

3. Selezionare "Cluster di infrastruttura di servizi" nell'elenco.
4. Passare al pannello Cluster di infrastruttura di servizi, fare clic su **Crea** e immettere i dettagli sul cluster.
5. Creare un **Nuovo gruppo di risorse**, assegnandogli lo stesso nome del cluster, per poterli trovare più facilmente in seguito. È utile soprattutto quando si devono apportare modifiche alla distribuzione e/o eliminare il cluster.

  	Nota: anche se è possibile decidere di usare un gruppo di risorse esistente, è meglio crearne uno nuovo, così sarà possibile eliminare molto facilmente i cluster non necessari.

 	 ![Creazione del gruppo di risorse][CreateRG]


6. Assicurarsi di selezionare la **Sottoscrizione** in cui si vuole distribuire il cluster, soprattutto se sono presenti più sottoscrizioni.

7. Selezionare una **Località** nell'elenco a discesa, se si vuole creare un'altra località. In caso contrario, il valore predefinito sarà Stati Uniti occidentali.

8. Configurare il **Tipo di nodo**. Il tipo di nodo può essere paragonato ai "ruoli" nei servizi cloud, che definiscono le dimensioni delle VM, il numero di VM e le relative proprietà. Il cluster può avere più di un tipo di nodo. L'unico vincolo è che almeno un tipo di nodo (quello principale o il primo definito nel portale) dovrà essere costituito da almeno 5 VM.
	1. Selezionare le dimensioni macchina virtuale e il piano tariffario necessari. Il valore predefinito è D4 Standard. Se si prevede di usare questo cluster solo per testare l'applicazione, è opportuno selezionare D2 o una VM di dimensioni inferiori.	
	2. Scegliere il numero di VM. È possibile aumentare o ridurre il numero di VM in un tipo di nodo in seguito, ma il tipo di nodo principale o il primo tipo di nodo deve avere almeno 5 VM.
	3. Scegliere un nome per il tipo di nodo (da 1 a 12 caratteri di lunghezza, contenenti solo lettere e numeri).	
	4. Scegliere il nome utente e la password per il desktop remoto VM.
	5. **Considerazioni sul tipo di nodo quando sono presenti più tipi di nodo**. Se si prevede di distribuire un cluster con un solo tipo di nodo, andare al passaggio successivo.

		- Per spiegare questo concetto, si consideri ad esempio di voler distribuire un'applicazione contenente un servizio "Front-end" e un servizio "Back-end" e di voler inserire il servizio "Front-end" in VM più piccole (ad esempio con dimensioni A2, D2 e così via) con porte aperte a Internet e il servizio "Back-end", a elevato utilizzo di calcolo, in VM più grandi (ad esempio con dimensioni D4, D6, D12 e così via) senza connessione Internet.
		- Anche se è possibile inserire entrambi i servizi in un tipo di nodo, è consigliabile inserirli in un cluster con due tipi di nodo, perché ogni tipo di nodo può avere proprietà diverse, ad esempio la connettività Internet, le dimensioni delle VM e il numero di VM che è possibile ridimensionare in modo indipendente.
		- Definire prima il tipo di nodo che conterrà almeno 5 VM. Gli altri tipi di nodo possono avere un minimo di 1 VM.
					

  	![Creazione del tipo di nodo][CreateNodeType]

9. **Porte delle applicazioni**: se si prevede di distribuire le applicazioni direttamente nel cluster, aggiungere le porte che si vuole aprire per le applicazioni in questo tipo di nodo (o i tipi di nodi creati). È possibile aggiungere in seguito le porte al tipo di nodo modificando il servizio di bilanciamento del carico associato a questo tipo di nodo. È necessario aggiungere un probe e quindi aggiungerlo alle regole del servizio di bilanciamento del carico. È più semplice eseguire ora questa operazione, perché l'automazione del portale aggiungerà i probe e le regole necessari al servizio di bilanciamento del carico.
	1. È possibile trovare le porte delle applicazioni nei manifesti del servizio che fanno parte del pacchetto dell'applicazione. Passare a ogni applicazione, aprire i manifesti del servizio e prendere nota di tutti gli endpoint "input" necessari alle applicazioni per comunicare con l'esterno.
	2. Aggiungere tutte le porte, separate da virgole, nel campo degli endpoint di input dell'applicazione. Per impostazione predefinita, l'endpoint connessione client TCP è 19000 e non è necessario specificarlo. Per aprire l'applicazione di questo esempio, è necessaria la porta "83". Questo valore è disponibile nel file servicemanifest.xml nel pacchetto dell'applicazione (potrebbero essere presenti più file servicemanifest.xml).

  Poiché la maggior parte delle applicazioni di esempio usa le porte 80 e 8081, aggiungerle se si prevede di distribuire gli esempi nel cluster.

  ![Porte][Ports]



10. **Facoltativo: proprietà di selezione host**: non è necessario aggiungere alcuna configurazione, perché una proprietà di selezione host predefinita di "NodeTypeName" viene aggiunta dal sistema. Se necessario, è possibile aggiungerne altre. 

  
## Configurazioni di sicurezza

A questo punto, l'infrastruttura di servizi supporta solo la protezione dei cluster con un certificato X509. Prima di iniziare questo processo, sarà necessario caricare il certificato nell'insieme di credenziali delle chiavi. Per altri dettagli, vedere [Sicurezza del cluster di infrastruttura di servizi](service-fabric-cluster-security.md).

È consigliabile proteggere il cluster, anche se facoltativo. Se si sceglie di non proteggere il cluster, è necessario non impostare alcuna modalità di sicurezza.

Per considerazioni e procedure di sicurezza dettagliate, vedere [Sicurezza del cluster di infrastruttura di servizi](service-fabric-cluster-security.md).

![Configurazioni di sicurezza][SecurityConfigs]



## Facoltativo: configurazione della diagnostica

Per impostazione predefinita, la diagnostica è abilitata nel cluster come supporto per la risoluzione dei problemi. Per disabilitare la diagnostica:

1. Passare al pannello delle configurazioni della diagnostica.

2. Impostare lo stato come disattivato.

## Facoltativo: impostazioni dell'infrastruttura

Si tratta di un'opzione avanzata, che consente di modificare le impostazioni predefinite per il cluster di infrastruttura di servizi. È consigliabile **non modificare** le impostazioni predefinite, a meno che non sia indispensabile per l'applicazione e/o il cluster.

## Completamento della creazione di un cluster
È possibile fare clic su **Riepilogo** per visualizzare le configurazioni fornite o scaricare il modello ARM che si userà per distribuire il cluster. Dopo avere specificato le impostazioni obbligatorie, verrà abilitato il pulsante Crea e sarà possibile avviare il processo di creazione del cluster.
 

È possibile visualizzare l'avanzamento in NOTIFICHE. Fare clic sull'icona a forma di campana accanto alla barra di stato a destra della schermata. Se durante la creazione del cluster si è fatto clic sull'opzione per l'aggiunta alla schermata iniziale, nella schermata iniziale verrà indicata la distribuzione del cluster di infrastruttura di servizi.

![Notifiche][Notifications]

## Visualizzazione dello stato del cluster

Una volta completata la distribuzione, è possibile esaminare il cluster nel portale.

1. Andare a **Sfoglia** e fare clic sulla risorsa **Cluster di infrastruttura di servizi**.

2. Individuare e fare clic sul cluster.

  ![Ricerca cluster][BrowseCluster]

3. Ora è possibile visualizzare i dettagli del cluster nel dashboard, incluso l'indirizzo IP pubblico del cluster. Si noti che passando il puntatore su **Indirizzo IP pubblico cluster** verranno visualizzati gli Appunti su cui è possibile fare clic per copiarli.

  ![Dashboard del cluster][ClusterDashboard]

  La sezione relativa al monitoraggio dei nodi nel pannello del dashboard del cluster indica il numero di VM integre e di quelle non integre. È possibile trovare altri dettagli sullo stato di integrità nella sezione [Introduzione al modello di integrità dell'infrastruttura di servizi](service-fabric-health-introduction.md) della documentazione.


## Connessione al cluster e distribuzione di un'applicazione

Con la configurazione del cluster, ora è possibile connettersi e iniziare la distribuzione delle applicazioni.

1. In un computer in cui è installato Service Fabric SDK avviare Windows PowerShell.

2. Eseguire uno dei set seguenti di comandi di PowerShell a seconda che si sia creato un cluster sicuro o non sicuro.
 

- Opzione 1: **connessione a un cluster non sicuro**. 

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 -KeepAliveIntervalInSec 10 
```

Eseguire i comandi seguenti per distribuire l'applicazione, sostituendo i percorsi indicati con quelli appropriati per il computer usato.

 - Opzione 2: **connessione a un cluster sicuro**.

Eseguire quanto segue per configurare il certificato sul computer che si userà per eseguire il comando di PowerShell "Connect-serviceFabricCluster".

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

Eseguire il comando di PowerShell seguente per connettersi ora a un cluster sicuro. I dettagli del certificato sono gli stessi specificati nel portale.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
            -KeepAliveIntervalInSec 10 `
            -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
            -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
            -StoreLocation CurrentUser -StoreName My
```
Ad esempio il comando di PowerShell precedente sarà simile al seguente.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint sfcluster4doc.westus.cloudapp.azure.com:19000 `
            -KeepAliveIntervalInSec 10 `
            -X509Credential -ServerCertThumbprint C179E609BBF0B227844342535142306F3913D6ED `
            -FindType FindByThumbprint -FindValue C179E609BBF0B227844342535142306F3913D6ED `
            -StoreLocation CurrentUser -StoreName My
```


Eseguire i comandi seguenti per distribuire l'applicazione, sostituendo i percorsi indicati con quelli appropriati per il computer usato. L'esempio seguente distribuisce l'applicazione di esempio per il conteggio parole.

Copiare il pacchetto nel cluster a cui ci si è connessi nel passaggio precedente.


```powershell
$applicationPath = "C:\VS2015\WordCount\WordCount\pkg\Debug"
```

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $applicationPath -ApplicationPackagePathInImageStore "WordCount" -ImageStoreConnectionString fabric:ImageStore
````
Register your application type with service fabric.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "WordCount" ````

Creare una nuova istanza nel tipo di applicazione appena registrato.

```powershell New-ServiceFabricApplication -ApplicationName fabric:/WordCount -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0.0 ````

Aprire ora il browser preferito e connettersi all'endpoint su cui l'applicazione è in ascolto. Per il conteggio di questa applicazione di esempio, l'URL è il seguente.

http://sfcluster4doc.westus.cloudapp.azure.com:31000



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi
- [Gestione delle applicazioni di Infrastruttura di servizi in Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Sicurezza del cluster di infrastruttura di servizi](service-fabric-cluster-security.md)
- [Introduzione al modello di integrità dell'infrastruttura di servizi](service-fabric-health-introduction.md)

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

<!---HONumber=AcomDC_1203_2015-->