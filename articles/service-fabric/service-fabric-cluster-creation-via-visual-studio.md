<properties
   pageTitle="Configurazione di un cluster dell'infrastruttura di servizi tramite Visual Studio | Microsoft Azure"
   description="Descrive come configurare un cluster di Service Fabric usando il modello di Gestione risorse di Azure creato da un progetto di Gruppo di risorse di Azure in Visual Studio"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/11/2016"
   ms.author="karolz@microsoft.com"/>

# Configurare un cluster di Service Fabric tramite Visual Studio
Questo articolo descrive come configurare un cluster di Azure Service Fabric usando Visual Studio e un modello di Gestione risorse di Azure (ARM). Per creare il modello, si userà un progetto Gruppo di risorse di Azure di Visual Studio. Dopo aver creato il modello, è possibile distribuirlo direttamente in Azure da Visual Studio oppure usarlo da uno script o nell'ambito della funzionalità di integrazione continuata.

## Creare un modello di cluster di Service Fabric con un progetto Gruppo di risorse di Azure
Per iniziare, aprire Visual Studio e creare un progetto Gruppo di risorse di Azure, disponibile nella cartella **Cloud**:

![Finestra di dialogo Nuovo progetto con il progetto Gruppo di risorse di Azure selezionato][1]

È possibile creare una nuova soluzione di Visual Studio per il progetto oppure aggiungerlo a una soluzione esistente.

>[AZURE.NOTE] Se il progetto Gruppo di risorse di Azure non è visibile nel nodo Cloud, significa che Azure SDK non è installato. Avviare Installazione guidata piattaforma Web ([eseguire ora l'installazione](http://www.microsoft.com/web/downloads/platform.aspx), se necessario), quindi cercare "Azure SDK per .NET" e installare la versione compatibile con la versione di Visual Studio in uso.

Dopo aver premuto il pulsante OK, Visual Studio chiederà di selezionare il modello di Gestione risorse da creare:

![Finestra di dialogo Seleziona modello di Azure con il modello Service Fabric Cluster selezionato][2]

Selezionare il modello "Service Fabric Cluster" e premere nuovamente il pulsante OK. In questo modo, sono stati creati il progetto e il modello di Gestione risorse.

## Preparare il modello per la distribuzione
Prima di distribuire il modello per la creazione del cluster, è necessario specificare i valori per i parametri obbligatori del modello. Questi valori dei parametri vengono letti dal file `ServiceFabricCluster.parameters.json` che si trova nella cartella `Templates` del progetto del gruppo di risorse. Aprire il file e specificare i valori seguenti:

|Nome parametro |Descrizione|
|-----------------------  |--------------------------|
|clusterLocation |Nome dell'**area Azure** in cui verrà posizionato il cluster dell'infrastruttura di servizi. Ad esempio, "Stati Uniti orientali".|
|certificateThumbprint |Identificazione personale del certificato che garantisce la sicurezza del cluster.|
|sourceVaultResourceId |*ID risorsa* dell'insieme di credenziali delle chiavi in cui viene memorizzato il certificato che garantisce la sicurezza del cluster.|
|certificateUrlValue |URL del certificato di protezione del cluster.|

Il modello di Gestione risorse di Service Fabric in Visual Studio crea un cluster sicuro, protetto da un certificato, identificato dagli ultimi tre parametri del modello (`certificateThumbprint`, `sourceVaultValue` e `certificateUrlValue`) e incluso in un **insieme di credenziali delle chiavi di Azure**. Per altre informazioni su come creare il certificato di sicurezza del cluster, vedere l'articolo [Proteggere un cluster di Service Fabric con i certificati](service-fabric-cluster-security.md#secure-a-service-fabric-cluster-by-using-certificates).

## Facoltativo: modificare il nome del cluster
Ogni cluster di Service Fabric ha un nome. Quando viene creato un cluster di Fabric in Azure, il nome del cluster insieme all'area di Azure determina il nome DNS (Domain Name System) del cluster. Ad esempio, se il nome del cluster è `myBigCluster` e il parametro `clusterLocation` è impostato su Stati Uniti orientali, il nome DNS del cluster sarà `myBigCluster.eastus.cloudapp.azure.com`.

Per impostazione predefinita il nome del cluster viene generato automaticamente in modo univoco aggiungendo un suffisso casuale al prefisso "cluster". Questo metodo semplifica l'uso del modello nell'ambito di un sistema ad **integrazione continua**. Se si desidera utilizzare un nome specifico per il cluster con un particolare significato, impostare il valore della variabile `clusterName` nel file di modello di Gestione risorse (`ServiceFabricCluster.json`) sul nome scelto. Si tratta della prima variabile definita nel file.

## Facoltativo: aggiungere le porte pubbliche dell'applicazione
Prima di distribuire il cluster, è possibile anche modificare le porte pubbliche delle applicazioni. Per impostazione predefinita, il modello apre solo due porte TCP pubbliche: 80 e 8081. Se per le applicazioni sono necessarie più porte, modificare la definizione del servizio di bilanciamento del carico di Azure nel modello. La definizione viene archiviata nel file del modello principale (`SecureFabricCluster.json`). Aprire il file e cercare `loadBalancedAppPort`. Si noterà che ogni porta è associata a tre elementi:

1. Una variabile del modello che definisce il valore della porta TCP per la porta:

	```json
	"loadBalancedAppPort1": "80"
	```

2. Un *probe* che definisce la frequenza e per quanto tempo il servizio di bilanciamento del carico di Azure tenterà di usare un nodo specifico dell'infrastruttura di servizi prima del failover a un altro nodo. I probe fanno parte della risorsa del servizio di bilanciamento del carico. Segue la definizione di probe per la prima porta dell'applicazione predefinita:

	```json
	{
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[variables('loadBalancedAppPort1')]",
            "protocol": "Tcp"
        }
    }
	```

3. Una *regola di bilanciamento del carico* che collega la porta e il probe e che consente il bilanciamento del carico in un set di nodi del cluster Service Fabric:

    ```json
	{
	    "name": "AppPortLBRule1",
	    "properties": {
	        "backendAddressPool": {
	            "id": "[variables('lbPoolID0')]"
	        },
	        "backendPort": "[variables('loadBalancedAppPort1')]",
	        "enableFloatingIP": false,
	        "frontendIPConfiguration": {
	            "id": "[variables('lbIPConfig0')]"
	        },
	        "frontendPort": "[variables('loadBalancedAppPort1')]",
	        "idleTimeoutInMinutes": 5,
	        "probe": {
	            "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
	        },
	        "protocol": "Tcp"
	    }
	}
    ```
Se le applicazioni che si intende distribuire nel cluster richiedono più porte, è possibile aggiungerle mediante la creazione di definizioni aggiuntive delle regole di bilanciamento del carico e di probe. Per altre informazioni su come usare il servizio di bilanciamento del carico di Azure con i modelli di Gestione risorse, vedere [Introduzione alla creazione di un servizio di bilanciamento del carico interno tramite un modello](../load-balancer/load-balancer-get-started-ilb-arm-template.md).

## Distribuire il modello tramite Visual Studio
Dopo aver salvato tutti i valori di parametro obbligatori nel file `ServiceFabricCluster.param.dev.json`, è possibile distribuire il modello e creare il cluster di Service Fabric. Fare clic con il pulsante destro del mouse sul progetto Gruppo di risorse in Esplora soluzioni di Visual Studio e scegliere **Distribuisci**. Verrà visualizzata la finestra di dialogo **Distribuisci in gruppo di risorse** che richiede l'autenticazione ad Azure, se necessaria:

![Finestra di dialogo Distribuisci a Gruppo di risorse][3]

La finestra di dialogo consente di scegliere se selezionare per il cluster un gruppo di risorse esistente oppure crearne uno nuovo in Gestione risorse. In genere, è opportuno usare un gruppo di risorse distinto per un cluster di Service Fabric.

Dopo aver premuto il pulsante Distribuisci, Visual Studio chiederà di confermare i valori di parametro del modello. Premere il pulsante **Salva**. Uno dei parametri, tuttavia, non ha un valore persistente: la password dell'account amministrativo per il cluster. Sarà quindi necessario specificare un valore di password ogni volta che Visual Studio lo richiede.

>[AZURE.NOTE] Se PowerShell non è mai stato usato per amministrare Azure dal computer in uso, è necessario eseguire alcune attività di manutenzione.
>1. Abilitare gli script di PowerShell eseguendo il comando [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx). Per i computer di sviluppo, i criteri "Unrestricted" sono in genere accettabili.
>2. Decidere se consentire la raccolta dei dati di diagnostica dai comandi di Azure PowerShell ed eseguire [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) o [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx), in base alle esigenze. In questo modo, si eviteranno richieste non necessarie durante la distribuzione del modello.

È possibile monitorare lo stato di avanzamento del processo di distribuzione nella finestra di output di Visual Studio. Al termine della distribuzione del modello, è possibile usare il nuovo cluster.

Se sono presenti errori, passare al [portale di Azure](https://portal.azure.com/) e aprire il gruppo di risorse in cui è stata eseguita la distribuzione. Fare clic su **Tutte le impostazioni**, quindi fare clic su **Distribuzioni** nel pannello Impostazioni. Nel caso in cui la distribuzione del gruppo di risorse non sia riuscita, in questa sezione saranno presenti informazioni di diagnostica dettagliate.

>[AZURE.NOTE] I cluster di Service Fabric richiedono che un certo numero di nodi sia attivo in ogni momento allo scopo di mantenere la disponibilità e lo stato, ossia per "mantenere il quorum". Di conseguenza, in genere non è sicuro spegnere tutti i computer del cluster se prima non è stato eseguito un [backup completo dello stato](service-fabric-reliable-services-backup-restore.md).

## Passaggi successivi
- [Informazioni sulla configurazione di cluster di Service Fabric dal portale di Azure](service-fabric-cluster-creation-via-portal.md)
- [Informazioni sulla gestione e la distribuzione di applicazioni di Service Fabric mediante Visual Studio](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png

<!---HONumber=AcomDC_0316_2016-->