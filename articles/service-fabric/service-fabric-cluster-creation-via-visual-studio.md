<properties
   pageTitle="Configurazione di un cluster dell'infrastruttura di servizi tramite Visual Studio | Microsoft Azure"
   description="Descrive come configurare un cluster dell'infrastruttura di servizi usando il modello di Gestione risorse di Azure (ARM) creato da un progetto Gruppo di risorse di Azure in Visual Studio"
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
   ms.date="11/13/2015"
   ms.author="karolz@microsoft.com"/>

# Configurare un cluster dell'infrastruttura di servizi tramite Visual Studio
Questo articolo descrive come configurare un cluster dell'[**infrastruttura di servizi**](http://azure.microsoft.com/documentation/services/service-fabric/) usando **Visual Studio** e un modello di [**Gestione risorse di Azure (ARM)**](https://azure.microsoft.com/documentation/articles/resource-group-overview/). Per creare il modello, verrà usato un **progetto Gruppo di risorse di Azure** di Visual Studio. Dopo aver creato il modello, è possibile distribuirlo direttamente in Azure da Visual Studio, ma è anche possibile usarlo da uno script o come parte della funzionalità **Integrazione continuata**.

## Creare un modello di cluster dell'infrastruttura di servizi con un progetto Gruppo di risorse di Azure
Per iniziare, aprire Visual Studio e creare un progetto Gruppo di risorse di Azure, disponibile nella cartella "Cloud":

![Finestra di dialogo Nuovo progetto con il progetto Gruppo di risorse di Azure selezionato][1]

È possibile creare una nuova soluzione di Visual Studio per questo progetto oppure aggiungerlo a una soluzione esistente.

>[AZURE.NOTE]Se non viene visualizzato il progetto Gruppo di risorse di Azure nel nodo Cloud, Azure SDK non è installato. Avviare Installazione guidata piattaforma Web ([disponibile qui](http://www.microsoft.com/web/downloads/platform.aspx) se non è già stata installata), quindi cercare "Azure SDK per .NET" e installare la versione compatibile con la versione di Visual Studio in uso.

Dopo aver premuto il pulsante OK, Visual Studio chiederà di selezionare il modello di ARM da creare:

![Finestra di dialogo Seleziona modello di Azure con il modello Service Fabric Cluster selezionato][2]

Selezionare il modello "Service Fabric Cluster" e premere nuovamente il pulsante OK. A questo punto verranno creati il progetto e il modello di ARM.

## Preparare il modello per la distribuzione
Prima di distribuire il modello per la creazione del cluster, è necessario fornire valori per i parametri obbligatori del modello. Questi valori di parametro vengono letti dal file `ServiceFabricCluster.param.dev.json` che si trova nella cartella `Templates` del progetto Gruppo di risorse. Aprire il file e specificare i valori seguenti:

|Nome parametro |Descrizione|
|-----------------------  |--------------------------|
|clusterLocation |Nome dell'**area Azure** in cui verrà posizionato il cluster dell'infrastruttura di servizi. Ad esempio "Stati Uniti orientali".|
|clusterName |Nome (DNS) del cluster dell'infrastruttura di servizi che verrà creato dal modello. <br /><br /> Ad esempio, se si imposta questo parametro su 'myBigCluster' e il parametro `clusterLocation` è impostato su Stati Uniti orientali, il nome del cluster sarà `myBigCluster.eastus.cloudapp.azure.com`.|
|certificateThumbprint |Identificazione personale del certificato che garantisce la sicurezza del cluster.|
|sourceVaultValue |*ID risorsa* dell'insieme di credenziali delle chiavi in cui viene memorizzato il certificato che garantisce la sicurezza del cluster.|
|certificateUrlValue |URL del certificato di protezione del cluster.|

Il modello di ARM dell'infrastruttura di servizi di Visual Studio crea un cluster sicuro, protetto da un certificato. Questo certificato è identificato dagli ultimi tre parametri del modello (`certificateThumbprint`, `sourceVaultValue` e `certificateUrlValue`) e deve essere presente in un **insieme di credenziali delle chiavi di Azure**. Per altre informazioni su come creare il certificato di sicurezza del cluster, vedere l'articolo relativo alla [modalità di protezione del cluster dell'infrastruttura di servizi mediante certificati](service-fabric-cluster-security.md).

## Facoltativo: Aggiungere le porte pubbliche dell'applicazione
Un altro aspetto del modello che si potrebbe modificare prima della distribuzione sono le **porte pubbliche dell'applicazione** per il cluster. Per impostazione predefinita, il modello visualizza solo due porte TCP pubbliche (80 e 8081). Se per le applicazioni sono necessarie più porte, occorre modificare la definizione del servizio di bilanciamento del carico di Azure nel modello. La definizione viene archiviata nel file del modello principale (`SecureFabricCluster.json`). Aprire tale file e cercare "loadBalancedAppPort". Si noterà che ogni porta è associata a tre elementi:

1. Un parametro modello che definisce il valore della porta TCP per la porta: ```json
	"loadBalancedAppPort1": {
	    "type": "int",
	    "defaultValue": 80
	}
	```

2. Un *probe* che definisce la frequenza e per quanto tempo il servizio di bilanciamento del carico di Azure tenterà di usare un nodo specifico dell'infrastruttura di servizi prima del failover a un altro nodo. I probe fanno parte della risorsa del servizio di bilanciamento del carico. Ecco la definizione di probe per la prima porta dell'applicazione predefinita: ```json
	{
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[parameters('loadBalancedAppPort1')]",
            "protocol": "tcp"
        }
    }
	```

3. Una *regola del servizio di bilanciamento del carico* che collega la porta al probe e consente il bilanciamento del carico in un set di nodi del cluster dell'infrastruttura di servizi: ```json
	{
	    "name": "AppPortLBRule1",
	    "properties": {
	        "backendAddressPool": {
	            "id": "[variables('lbPoolID0')]"
	        },
	        "backendPort": "[parameters('loadBalancedAppPort1')]",
	        "enableFloatingIP": false,
	        "frontendIPConfiguration": {
	            "id": "[variables('lbIPConfig0')]"
	        },
	        "frontendPort": "[parameters('loadBalancedAppPort1')]",
	        "idleTimeoutInMinutes": 5,
	        "probe": {
	            "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
	        },
	        "protocol": "tcp"
	    }
	}
    ``` Se le applicazioni che si prevede di distribuire al cluster richiedono più porte, sarà necessario aggiungerle creando probe e definizioni delle regole di bilanciamento del carico aggiuntivi. Per altre informazioni sull'uso del servizio di bilanciamento del carico di Azure tramite modelli di ARM, vedere l'articolo [Introduzione alla configurazione del servizio di bilanciamento del carico interno con Gestione risorse di Azure](https://azure.microsoft.com/documentation/articles/load-balancer-internal-arm-powershell/).

## Distribuire il modello tramite Visual Studio
Dopo aver salvato tutti i valori di parametro obbligatori nel file `ServiceFabricCluster.param.dev.json`, è possibile distribuire il modello e creare il cluster dell'infrastruttura di servizi. Fare clic con il pulsante destro del mouse sul progetto Gruppo di risorse in Esplora soluzioni di Visual Studio e scegliere "Distribuisci". Verrà visualizzata la finestra di dialogo "Distribuisci a Gruppo di risorse" di Visual Studio che richiede l'autenticazione ad Azure, se necessaria:

![Finestra di dialogo Distribuisci a Gruppo di risorse][3]

La finestra di dialogo consente di scegliere un gruppo di risorse di ARM esistente per il cluster e offre un'opzione per crearne uno nuovo. In genere è opportuno usare un gruppo di risorse distinto per un cluster dell'infrastruttura di servizi.

Dopo aver premuto il pulsante "Distribuisci", Visual Studio chiederà di confermare i valori di parametro del modello. Premere il pulsante Salva. Esiste un parametro che non dispone di un valore persistente, ossia la password dell'account amministrativo per il cluster. Specificare un valore di password quando viene visualizzata la richiesta di Visual Studio.

>[AZURE.NOTE]Se PowerShell non è mai stato usato per amministrare Azure dal computer in uso, è necessario eseguire alcune attività di manutenzione: 1. Abilitare gli script di PowerShell eseguendo il comando [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx). Per i computer di sviluppo, i criteri "Unrestricted" sono in genere accettabili. 2. Decidere se consentire la raccolta dei dati di diagnostica dai comandi di Azure PowerShell ed eseguire [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) o [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx) in base alle esigenze. Se si usa Azure PowerShell 0.9.8 o versioni meno recenti, questi comandi sono denominati rispettivamente `Enable-AzureDataCollection` e `Discable-AzureDataCollection`. In questo modo, si eviteranno richieste non necessarie durante la distribuzione del modello.

È possibile monitorare lo stato di avanzamento del processo di distribuzione nella finestra di output di Visual Studio. Al termine della distribuzione del modello, è possibile usare il nuovo cluster.

Se sono presenti errori, passare al [portale di Azure](https://portal.azure.com/) e controllare le notifiche. In caso di distribuzione del gruppo di risorse non riuscita, in quest'area saranno presenti informazioni di diagnostica dettagliate.

## Passaggi successivi
- [Informazioni sulla configurazione di un cluster dell'infrastruttura di servizi tramite il portale di Azure](service-fabric-cluster-creation-via-portal.md)
- [Informazioni sulle modalità di gestione e distribuzione delle applicazioni dell'infrastruttura di servizi tramite Visual Studio](service-fabric-manage-applications-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png

<!---HONumber=Nov15_HO4-->