---
title: Effettuare il provisioning di microservizi e distribuirli in modo prevedibile in Azure
description: "Informazioni su come effettuare il provisioning di un'applicazione costituita da microservizi e come distribuirla nel servizio app di Azure come un'unità singola e in modo prevedibile usando modelli di gruppo di risorse JSON e script di PowerShell."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2016
ms.author: cephalin
ms.openlocfilehash: 3719e037f1564411a8f94d1ca962ba1ef6b5d435
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Effettuare il provisioning di microservizi e distribuirli in modo prevedibile in Azure
Questa esercitazione illustra come effettuare il provisioning di un'applicazione costituita da [microservizi](https://en.wikipedia.org/wiki/Microservices) e come distribuirla nel [servizio app di Azure](/services/app-service/) come un'unità singola e in modo prevedibile usando modelli di gruppo di risorse JSON e script di PowerShell. 

Quando si effettua il provisioning e si distribuiscono applicazioni su vasta scala costituite la microservizi altamente disaccoppiati, ripetibilità e prevedibilità sono fondamentali. [servizio app di Azure](/services/app-service/) è possibile creare microservizi che includono app Web, per dispositivi mobili, per le API e per la logica. [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) consente di gestire tutti i microservizi come unità, insieme alle dipendenze delle risorse, ad esempio le impostazioni di controllo del codice sorgente e del database. A questo punto è possibile distribuire l'applicazione usando modelli JSON e semplici script di PowerShell. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="what-you-will-do"></a>Contenuto dell'esercitazione
In questa esercitazione si distribuirà un'applicazione che include:

* Due app Web (ovvero due microservizi)
* Un database SQL di back-end
* Impostazioni dell'app, stringhe di connessione e controllo del codice sorgente
* Application Insights, avvisi, impostazioni di scalabilità automatica

## <a name="tools-you-will-use"></a>Strumenti da usare
In questa esercitazione si useranno gli strumenti seguenti. Non essendo una discussione completa sugli strumenti, verrà considerato solo lo scenario end-to-end, fornendo una breve introduzione per ogni strumento e dove è possibile trovare altre informazioni specifiche. 

### <a name="azure-resource-manager-templates-json"></a>Modelli di Gestione risorse di Azure (JSON)
Ogni volta che si crea, ad esempio, un'app Web nel servizio app di Azure, Gestione risorse di Azure usa un modello JSON per creare l'intero gruppo di risorse con le risorse del componente. Un modello complesso disponibile in [Azure Marketplace](/marketplace), come l'app [WordPress scalabile](/marketplace/partners/wordpress/scalablewordpress/), può includere il database MySQL, account di archiviazione, il piano di servizio app, la stessa app Web, regole di avviso, impostazioni dell'app, impostazioni di scalabilità automatica e altro. Tutti questi modelli sono disponibili tramite PowerShell. Per informazioni su come scaricare e usare questi modelli, vedere [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

Per altre informazioni sui modelli di Gestione risorse di Azure, vedere [Creazione di modelli di Gestione risorse di Azure](../azure-resource-manager/resource-group-authoring-templates.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Azure SDK 2.6 per Visual Studio
L'SDK più recente include miglioramenti a livello di supporto per i modelli di Gestione risorse di Azure nell'editor JSON. È possibile usarlo per creare rapidamente da zero un modello di gruppo di risorse o aprire un modello JSON esistente (ad esempio, un modello della raccolta scaricato) per modificarlo, popolare il file di parametri e distribuire il gruppo di risorse direttamente da una soluzione del gruppo di risorse di Azure.

Per altre informazioni, vedere [Azure SDK 2.6 per Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 o versione successiva
A partire dalla versione 0.8.0, l'installazione di Azure PowerShell include il modulo Gestione risorse di Azure, oltre al modulo Azure. Il nuovo modulo permette di creare script per la distribuzione di gruppi di risorse.

Per altre informazioni, vedere [Uso di Azure PowerShell con Gestione risorse di Azure](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Esplora risorse di Azure
Questo [strumento in anteprima](https://resources.azure.com) consente di esplorare le definizioni JSON di tutti i gruppi di risorse nella sottoscrizione e delle singole risorse. Con lo strumento è possibile modificare le definizioni JSON di una risorsa, eliminare l'intera gerarchia di risorse e crearne di nuove.  Le informazioni immediatamente disponibili nello strumento sono molto utili per la modifica del modello, perché mostrano quali proprietà è necessario impostare per un tipo di risorsa particolare, i valori corretti e così via. È anche possibile creare un gruppo di risorse personalizzato nel [Portale di Azure](https://portal.azure.com/) e ispezionarne quindi le definizioni JSON nello strumento di esplorazione per creare un modello del gruppo di risorse.

### <a name="deploy-to-azure-button"></a>Pulsante Deploy to Azure per la distribuzione in Azure
Se si usa GitHub per il controllo del codice sorgente, è possibile inserire nel file README.MD un [pulsante Deploy to Azure](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) , che abilita un'interfaccia utente di distribuzione in Azure pronta all'uso. Mentre si può procedere in questo modo per qualsiasi app Web semplice, è possibile estendere questa opzione per abilitare la distribuzione di un intero gruppo di risorse inserendo un file azuredeploy.json nella radice del repository. Questo file JSON, che contiene il modello di gruppo di risorse, verrà usato dal pulsante Deploy to Azure per creare il gruppo di risorse. Per un esempio vedere [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) , l'app di esempio che verrà usata in questa esercitazione.

## <a name="get-the-sample-resource-group-template"></a>Ottenere il modello del gruppo di risorse di esempio
Ecco come scaricarlo.

1. Passare all'esempio di servizio app [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) .
2. Nel file readme.md fare clic su **Deploy to Azure**.
3. Verrà aperto il sito [deploy-to-azure](https://deploy.azure.com) e verrà chiesto di immettere i parametri di distribuzione. Come si noterà, la maggior parte dei campi è già popolata con il nome del repository e alcune stringhe casuali. È possibile modificare tutti i campi, se lo si desidera, ma le uniche informazioni che è necessario immettere sono l'accesso amministrativo e la password di SQL Server, quindi fare clic su **Next**.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Fare quindi clic su **Deploy** per avviare il processo di distribuzione. Dopo il completamento del processo, fare clic sul collegamento http://todoapp*XXXX*.azurewebsites.net per accedere all'applicazione distribuita. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   La visualizzazione dell'interfaccia utente sarà un po' lenta alla prima esplorazione, perché le app sono in fase di avvio, ma l'applicazione è di sicuro completamente funzionante.
5. Tornando alla pagina di distribuzione, fare clic sul collegamento **Gestisci** per visualizzare la nuova applicazione nel portale di Azure.
6. Nell'elenco a discesa **Informazioni di base** fare clic sul collegamento del gruppo di risorse. Notare anche che l'app Web è già connessa al repository GitHub in **Progetto esterno**. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. Nel pannello del gruppo di risorse si noterà che il gruppo include già due app Web e un database SQL.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Tutto ciò che si è appena visto in pochi minuti è un'applicazione di microservizi completamente distribuita, con tutti i componenti, le dipendenze, le impostazioni, il database e la pubblicazione continua configurati da un'orchestrazione automatizzata in Gestione risorse di Azure. Tutte le operazioni sono state eseguite da due elementi:

* Il pulsante Deploy to Azure
* Il file azuredeploy.json nella radice del repository

È possibile distribuire la stessa applicazione decine, centinaia o migliaia di volte e avere ogni volta esattamente la stessa configurazione. La ripetibilità e la prevedibilità di questo approccio consentono di distribuire applicazioni su vasta scala in modo semplice e affidabile.

## <a name="examine-or-edit-azuredeployjson"></a>Esaminare (o modificare) AZUREDEPLOY.JSON
A questo punto si esaminerà come è stato configurato il repository GitHub. Si userà l'editor JSON incluso in Azure .NET SDK, quindi se [Azure .NET SDK 2.6](https://azure.microsoft.com/downloads/)non è già installato, installarlo ora.

1. Clonare il repository [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) con lo strumento git preferito. Nella schermata seguente questa operazione viene eseguita con Team Explorer in Visual Studio 2013.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. Dalla radice del repository aprire azuredeploy.json in Visual Studio. Se il riquadro Struttura JSON non è visibile, è necessario installare Azure .NET SDK.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Non verranno descritti tutti i dettagli del formato JSON, ma nella sezione [Altre risorse](#resources) sono disponibili collegamenti per l'apprendimento del linguaggio del modello di gruppo di risorse. In questa esercitazione verranno illustrate solo le interessanti funzionalità che possono semplificare le attività iniziali per creare un modello personalizzato per la distribuzione di app.

### <a name="parameters"></a>Parametri
Nella sezione dei parametri è possibile osservare che la maggior parte di essi corrisponde all'input richiesto tramite il pulsante **Deploy to Azure** . Il sito aperto tramite il pulsante **Deploy to Azure** popola l'interfaccia utente di input usando i parametri definiti nel file azuredeploy.json. Questi parametri sono usati in tutte le definizioni di risorse, ad esempio nomi delle risorse, valori delle proprietà e così via.

### <a name="resources"></a>Risorse
Come si può vedere, nel nodo delle risorse sono definite 4 risorse di primo livello, quali un'istanza di SQL Server, un piano di servizio app e due app Web. 

#### <a name="app-service-plan"></a>Piano di servizio app
Si inizierà con una semplice risorsa a livello di radice in JSON. In Struttura JSON fare clic sul piano di servizio app denominato **[hostingPlanName]** per evidenziare il codice JSON corrispondente. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Si noti che l'elemento `type` specifica la stringa per un piano di servizio app (che molto tempo fa era definito server farm), altri elementi e proprietà sono compilati con i parametri definiti nel file JSON e questa risorsa non include risorse annidate.

> [!NOTE]
> Notare anche che il valore `apiVersion` indica ad Azure quale versione dell'API REST usare con la definizione delle risorse JSON e può influire sulla formattazione della risorsa all'interno di `{}`. 
> 
> 

#### <a name="sql-server"></a>SQL Server
Fare quindi clic sulla risorsa di SQL Server denominata **SQLServer** in Struttura JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Nel codice JSON evidenziato notare quando indicato di seguito:

* Per usare i parametri, assicurarsi che le risorse create siano denominate e configurate in modo coerente tra di esse.
* La risorsa SQLServer ha due risorse annidate, ognuna con un valore diverso per `type`.
* Le risorse annidate in `“resources”: […]`, dove sono definite le regole di database e del firewall, includono un elemento `dependsOn` che specifica l'ID risorsa della risorsa SQLServer a livello di radice. Questo elemento indica a Gestione risorse di Azure che "prima di creare questa risorsa, deve esistere già l'altra risorsa e se l'altra risorsa è definita nel modello, dev'essere creata prima quella".
  
  > [!NOTE]
  > Per altre informazioni sull'uso della funzione `resourceId()`, vedere [Funzioni del modello di Azure Resource Manager](../azure-resource-manager/resource-group-template-functions-resource.md#resourceid).
  > 
  > 
* L'elemento `dependsOn` indica a Gestione risorse di Azure quali risorse possono essere create in parallelo e quali devono essere create in sequenza. 

#### <a name="web-app"></a>App Web
Si passerà ora alle app Web effettive, che sono più complesse. Fare clic sull'app Web [variables('apiSiteName')] in Struttura JSON per evidenziare il relativo codice JSON. Come si noterà, diventa tutto molto più interessante. A questo scopo, le funzionalità verranno descritte una per una:

##### <a name="root-resource"></a>Risorsa radice
L'app Web dipende da due diverse risorse. Ciò significa che Gestione risorse di Azure creerà l'app Web solo dopo la creazione del piano di servizio app e dell'istanza di SQL Server.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Impostazioni app
Le impostazioni dell'app vengono definite anche come risorsa annidata.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

Nell'elemento `properties` per `config/appsettings` sono disponibili due impostazioni app nel formato `“<name>” : “<value>”`.

* `PROJECT` è un' [impostazione KUDU](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) che indica alla distribuzione di Azure quale progetto usare in una soluzione di Visual Studio con più progetti. Più avanti verrà illustrato come configurare il controllo del codice sorgente, ma poiché il codice di ToDoApp è una soluzione di Visual Studio con più progetti, questa impostazione è necessaria.
* `clientUrl` è semplicemente un'impostazione app usata dal codice dell'applicazione.

##### <a name="connection-strings"></a>Stringhe di connessione
Le stringhe di connessione vengono definite anche come risorsa annidata.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

Nell'elemento `properties` per `config/connectionstrings` ogni stringa di connessione è definita anche come coppia nome/valore con il formato specifico `“<name>” : {“value”: “…”, “type”: “…”}`. Per l'elemento `type` i valori possibili sono `MySql`, `SQLServer`, `SQLAzure` e `Custom`.

> [!TIP]
> Per un elenco definitivo di tipi di stringhe di connessione, eseguire il comando seguente in Azure PowerShell: \[Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>Controllo del codice sorgente
Le impostazioni di controllo del codice sorgente vengono definite anche come risorsa annidata. Gestione risorse di Azure usa questa risorsa per configurare la pubblicazione continua (vedere più avanti l'avvertenza per `IsManualIntegration` ) e anche per l'avvio automatico della distribuzione del codice applicazione durante l'elaborazione del file JSON.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` e `branch` devono essere abbastanza intuitive e puntare al repository Git e al nome del ramo dal quale viene eseguita la pubblicazione. Anche in questo caso, sono definite dai parametri di input. 

Si noti che nell'elemento `dependsOn`, oltre alla risorsa app Web stessa, `sourcecontrols/web` dipende anche da `config/appsettings` e `config/connectionstrings`. Il motivo è che dopo la configurazione di `sourcecontrols/web` , il processo di distribuzione di Azure tenta automaticamente di distribuire, compilare e avviare il codice dell'applicazione. L'inserimento di questa dipendenza consente di assicurare l'accesso dell'applicazione alle impostazioni delle app e alle impostazioni di connessione necessarie prima di eseguire il codice dell'applicazione. 

> [!NOTE]
> Notare anche che `IsManualIntegration` è impostata su `true`. Questa proprietà è necessaria nell'esercitazione, perché non si è effettivamente il proprietario del repository GitHub e quindi non è possibile concedere ad Azure l'autorizzazione per configurare la pubblicazione continua da [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp), (ovvero eseguire il push degli aggiornamenti automatici del repository ad Azure). È possibile usare il valore predefinito `false` per il repository specificato solo se le credenziali GitHub del proprietario sono già state configurate nel [portale di Azure](https://portal.azure.com/) . In altre parole, se in precedenza è stato impostato il controllo del codice sorgente in GitHub o BitBucket per qualsiasi app nel [Portale di Azure](https://portal.azure.com/) usando le proprie credenziali utente, Azure memorizzerà le credenziali e le userà ogni volta che in seguito si distribuirà un'app da GitHub o BitBucket. Tuttavia, se questa operazione non è ancora stata eseguita, la distribuzione del modello JSON non riuscirà quando Gestione risorse di Azure tenta di configurare le impostazioni di controllo del codice sorgente dell'app Web, perché non può connettersi a GitHub o BitBucket con le credenziali del proprietario del repository.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Confrontare il modello JSON con il gruppo di risorse distribuite
Qui è possibile scorrere tutti i pannelli dell'app Web nel [portale di Azure](https://portal.azure.com/), ma esiste un altro strumento altrettanto, se non più, utile. Passare allo strumento di anteprima di [Esplora risorse di Azure](https://resources.azure.com) che fornisce una rappresentazione JSON di tutti i gruppi di risorse nelle proprie sottoscrizioni, così come esistono effettivamente nel back-end di Azure. È anche possibile vedere in che modo la gerarchia JSON del gruppo di risorse in Azure corrisponde alla gerarchia nel file modello usato per crearla.

Ad esempio, se si passa allo strumento [Esplora risorse di Azure](https://resources.azure.com) e si espandono i nodi nello strumento, si vedranno il gruppo di risorse e le risorse a livello di radice raccolte nei rispettivi tipi di risorse.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Se si esegue il drill-down fino a un'app Web, dovrebbero essere visibili i dettagli di configurazione dell'app Web analoghi alla schermata seguente:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Anche in questo caso, le risorse annidate avranno una gerarchia molto simile a quelle nel file modello JSON e saranno visualizzate impostazioni app, stringhe di connessione e così via, adeguatamente riflesse nel riquadro JSON. L'assenza di impostazioni a questo punto può indicare un problema con il file JSON e può contribuire a risolvere i problemi del file modello JSON.

## <a name="deploy-the-resource-group-template-yourself"></a>Distribuire manualmente il modello di gruppo di risorse
Il pulsante **Deploy to Azure** è molto utile, ma consente di distribuire il modello di gruppo di risorse in azuredeploy.json solo se è già stato effettuato il push di azuredeploy.json a GitHub. Azure .NET SDK fornisce anche gli strumenti per la distribuzione di qualsiasi file modello JSON direttamente dal computer locale. A questo scopo, eseguire la procedura seguente.

1. In Visual Studio fare clic su **File** > **Nuovo** > **Progetto**.
2. Fare clic su **Visual C#** > **Cloud** > **Gruppo di risorse di Azure**, quindi su **OK**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. In **Seleziona modello di Azure** selezionare **Modello vuoto** e fare clic su **OK**.
4. Trascinare azuredeploy.json nella cartella **Modello** del nuovo progetto.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. Da Esplora soluzioni aprire il file azuredeploy.json copiato.
6. Ai fini di questa dimostrazione, si aggiungeranno al file JSON alcune risorse standard di Application Insights, facendo clic su **Aggiungi risorsa**. Se si è interessati solo alla distribuzione del file JSON, ignorare i passaggi relativi alla distribuzione.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Selezionare **Application Insights per app Web**, assicurarsi che siano selezionati un piano di servizio app esistente e un'app Web e quindi fare clic su **Aggiungi**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   A questo punto saranno visualizzate diverse nuove risorse che, a seconda della risorsa e delle operazioni che esegue, presentano dipendenze dal piano di servizio app o dall'app Web. Queste risorse non sono abilitate dalla relativa definizione esistente, perciò si procederà alla modifica.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. In Struttura JSON fare clic su **appInsights AutoScale** per evidenziare il relativo codice JSON. Questa è l'impostazione della scalabilità per il piano di servizio app.
9. Nel codice JSON evidenziato individuare le proprietà `location` e `enabled` e impostarle come illustrato di seguito.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. In Struttura JSON fare clic su **CPUHigh appInsights** per evidenziare il relativo codice JSON. Si tratta di un avviso.
11. Individuare le proprietà `location` e `isEnabled` e impostarle come illustrato di seguito. Eseguire la stessa operazione per gli altri tre avvisi (lampadine viola).
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. A questo punto si è pronti per la distribuzione. Fare quindi clic con il pulsante destro del mouse sul progetto e scegliere **Deploy** > **Nuova distribuzione**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Accedere al proprio account Azure, se non si è già connessi.
14. Selezionare un gruppo di risorse esistente nella sottoscrizione o creare un nuovo gruppo, selezionare **azuredeploy.json**, quindi fare clic su **Modifica parametri**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    A questo punto si sarà in grado di modificare tutti i parametri definiti nel file modello in una tabella. I parametri che definiscono i valori predefiniti avranno già i relativi valori predefiniti e i parametri che definiscono un elenco di valori consentiti saranno visualizzati come elenchi a discesa.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Compilare tutti i parametri vuoti e usare l' [indirizzo del repository GitHub per ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) in **repoUrl**. Fare quindi clic su **Salva**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > La scalabilità automatica è una funzionalità offerta nel livello **Standard** o superiore e gli avvisi a livello di piano sono funzionalità offerte nel livello **Basic** o superiore. Sarà necessario impostare il parametro **sku** su **Standard** o **Premium** per visualizzare tutte le nuove risorse di Application Insights evidenziate.
    > 
    > 
16. Fare clic su **Distribuisci**. Se è stata selezionata l'opzione **Salva password**, la password verrà salvata nel file di parametri **come testo normale**. In caso contrario, verrà richiesto di immettere la password del database durante il processo di distribuzione.

La procedura è terminata. A questo punto basta passare al [portale di Azure](https://portal.azure.com/) e allo strumento [Esplora risorse di Azure](https://resources.azure.com) per visualizzare i nuovi avvisi e le impostazioni di scalabilità automatica aggiunte all'applicazione JSON distribuita.

I passaggi in questa sezione hanno eseguito principalmente le operazioni seguenti:

1. Preparazione del file modello
2. Creazione di un file di parametri da usare con il file modello
3. Distribuzione del file modello con il file di parametri

L'ultimo passaggio viene eseguito facilmente da un cmdlet di PowerShell. Per vedere le operazioni eseguite da Visual Studio durante la distribuzione dell'applicazione, aprire Scripts\Deploy-AzureResourceGroup.ps1. In questo file è presente una quantità elevata di codice, ma verrà evidenziato solo tutto il codice pertinente e necessario per la distribuzione del file modello con il file di parametri.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

L'ultimo cmdlet, `New-AzureResourceGroup`, è quello che esegue effettivamente l'azione. Tutto questo serve a dimostrare che con strumenti appropriati, distribuire l'applicazione cloud in modo prevedibile è relativamente semplice. Ogni volta che si esegue il cmdlet sullo stesso modello con lo stesso file di parametri si otterrà lo stesso risultato.

## <a name="summary"></a>Summary
In DevOps la ripetibilità e la prevedibilità sono fondamentali per la riuscita della distribuzione di un'applicazione su vasta scala costituita da microservizi. In questa esercitazione è stata distribuita in Azure un'applicazione con due microservizi come singolo gruppo di risorse usando il modello di Gestione risorse di Azure. È auspicabile che si siano acquisite le conoscenze necessarie per iniziare a convertire l'applicazione in Azure in un modello, in modo da poterne effettuare il provisioning e distribuirla in modo prevedibile. 

<a name="resources"></a>

## <a name="more-resources"></a>Altre risorse
* [Linguaggio del modello di Gestione risorse di Azure](../azure-resource-manager/resource-group-authoring-templates.md)
* [Creazione di modelli di Gestione risorse di Azure](../azure-resource-manager/resource-group-authoring-templates.md)
* [Funzioni del modello di Gestione risorse di Azure](../azure-resource-manager/resource-group-template-functions.md)
* [Distribuire un'applicazione con un modello di Gestione risorse di Azure](../azure-resource-manager/resource-group-template-deploy.md)
* [Uso di Azure PowerShell con Gestione risorse di Azure](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse in Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md)

