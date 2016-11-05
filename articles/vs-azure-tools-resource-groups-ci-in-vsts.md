---
title: Integrazione continua in VS Team Services con i progetti di gruppi di risorse di Azure | Microsoft Docs
description: Descrive come configurare l'integrazione continua in Visual Studio Team Services con i progetti di distribuzione Gruppo di risorse di Azure in Visual Studio.
services: visual-studio-online
documentationcenter: na
author: mlearned
manager: erickson-doug
editor: ''

ms.service: azure-resource-manager
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: mlearned

---
# Integrazione continua in Visual Studio Team Services con i progetti di distribuzione Gruppo di risorse di Azure
Per distribuire un modello di Azure, è necessario eseguire attività per completare le varie fasi, ovvero compilazione, test, copia in Azure (detta anche "staging") e distribuzione del modello. Esistono due modi diversi per eseguire questa operazione in Visual Studio Team Services (VS Team Services). Entrambi i metodi forniscono gli stessi risultati, quindi è opportuno scegliere quello che meglio si adatta al proprio flusso di lavoro.

* Aggiungere un singolo passaggio alla definizione di compilazione che esegue lo script di PowerShell incluso nel progetto di distribuzione Gruppo di risorse di Azure (Deploy-AzureResourceGroup.ps1). Lo script copia gli elementi e quindi distribuisce il modello.
* Aggiungere più istruzioni di compilazione di VS Team Services, ognuna delle quali esegue un'attività della fase.

Questo articolo illustra come usare la prima opzione, ovvero usare una definizione di compilazione per eseguire lo script di PowerShell. Uno dei vantaggi di questa opzione è che lo script usato dagli sviluppatori in Visual Studio è lo stesso usato da VS Team Services. Questa procedura presuppone che sia già disponibile un progetto di distribuzione di Visual Studio archiviato in VS Team Services.

## Copiare gli elementi in Azure
Indipendentemente dallo scenario, se sono disponibili elementi necessari per la distribuzione del modello, occorre concedere a Gestione risorse di Azure l'accesso a tali elementi. Questi elementi possono includere file, ad esempio:

* Modelli annidati
* Script di configurazione e script DSC
* File binari dell'applicazione

### Modelli annidati e script di configurazione
Quando si usano i modelli forniti da Visual Studio, o compilati con frammenti di Visual Studio, lo script di PowerShell non solo prepara gli elementi, ma imposta anche l'URI con parametri per le risorse per le diverse distribuzioni. Lo script copia quindi gli elementi in un contenitore protetto in Azure, crea un token di firma di accesso condiviso per tale contenitore e quindi passa le informazioni alla distribuzione del modello. Per altre informazioni sui modelli annidati, vedere [Creare una distribuzione di modelli](https://msdn.microsoft.com/library/azure/dn790564.aspx).

## Configurare la distribuzione continua in VS Team Services
Per chiamare lo script di PowerShell in VS Team Services, è necessario aggiornare la definizione di compilazione. In breve, ecco i passaggi:

1. Modificare la definizione di compilazione.
2. Configurare l'autorizzazione di Azure in VS Team Services.
3. Aggiungere un'istruzione di compilazione di Azure PowerShell che fa riferimento allo script di PowerShell nel progetto di distribuzione Gruppo di risorse di Azure.
4. Impostare il valore del parametro *-ArtifactsStagingDirectory* per l'interazione con un progetto compilato in VS Team Services.

### Procedura dettagliata
I passaggi seguenti illustrano la procedura necessaria per configurare la distribuzione continua in VS Team Services

1. Modificare la definizione di compilazione di VS Team Services e aggiungere un'istruzione di compilazione di Azure PowerShell. Scegliere la definizione di compilazione nella categoria **Definizioni di compilazione** e quindi scegliere il collegamento **Modifica**.
   
   ![][0]
2. Aggiungere un'istruzione di compilazione di **Azure PowerShell** alla definizione di compilazione e quindi scegliere il pulsante **Aggiungi istruzione di compilazione**.
   
   ![][1]
3. Scegliere la categoria **Distribuzione**, selezionare l'attività **Azure PowerShell** e quindi scegliere il pulsante **Aggiungi**.
   
   ![][2]
4. Scegliere l'istruzione di compilazione di **Azure PowerShell** e quindi compilare i relativi valori.
   
   1. Se è già stato aggiunto un endpoint di servizio di Azure a VS Team Services, scegliere la sottoscrizione nella casella di riepilogo a discesa **Sottoscrizione Azure** e quindi passare alla sezione successiva.
      
      Se non si ha un endpoint di servizio di Azure in VS Team Services, è necessario aggiungerne uno. Questa sottosezione illustra il processo. Se l'account Azure usa un account Microsoft, ad esempio Hotmail, è necessario eseguire i passaggi seguenti per ottenere un'autenticazione dell'entità servizio.
   2. Scegliere il collegamento**Gestisci** accanto alla casella di riepilogo a discesa **Sottoscrizione Azure**.
      
      ![][3]
   3. Scegliere **Azure** nella casella di riepilogo a discesa **Nuovo endpoint servizio**.
      
      ![][4]
   4. Nella finestra di dialogo **Aggiungi sottoscrizione di Azure** selezionare l'opzione **Entità servizio**.
      
      ![][5]
   5. Aggiungere le informazioni sulla sottoscrizione di Azure nella finestra di dialogo **Aggiungi sottoscrizione di Azure**. È necessario specificare gli elementi seguenti:
      
      * ID sottoscrizione
      * Nome sottoscrizione
      * ID entità servizio
      * Chiave entità servizio
      * ID tenant
   6. Aggiungere un nome a propria scelta nella casella **Sottoscrizione**. Questo valore verrà visualizzato in seguito nell'elenco a discesa **Sottoscrizione Azure** in VS Team Services.
   7. Se non si conosce l'ID sottoscrizione di Azure, è possibile usare uno dei seguenti comandi per ottenerlo.
      
      Per gli script di Azure PowerShell usare:
      
      `Get-AzureRmSubscription`
      
      Per l'interfaccia della riga di comando di Azure usare:
      
      `azure account show`
   8. Per ottenere un ID entità servizio, una chiave entità servizio e un ID Tenant, seguire la procedura in [Creare un'applicazione e un'entità servizio di Active Directory tramite il portale](resource-group-create-service-principal-portal.md) o [Autenticazione di un'entità servizio con Gestione risorse di Azure](resource-group-authenticate-service-principal.md).
   9. Aggiungere i valori per ID entità servizio, Chiave entità servizio e ID Tenant nella finestra di dialogo **Aggiungi sottoscrizione di Azure** e quindi scegliere **OK**.
      
      Ora è disponibile un'entità servizio valida da usare per eseguire lo script di Azure PowerShell.
5. Modificare la definizione di compilazione e scegliere l'istruzione di compilazione **Azure PowerShell**. Selezionare la sottoscrizione nella casella di riepilogo a discesa **Sottoscrizione Azure**. Se la sottoscrizione non viene visualizzata, scegliere il pulsante **Aggiorna** accanto al collegamento **Gestisci**.
   
   ![][8]
6. Fornire un percorso per lo script di PowerShell Deploy-AzureResourceGroup.ps1. A tale scopo, scegliere il pulsante con i puntini di sospensione (...) accanto alla casella **Percorso script**, passare allo script di PowerShell Deploy-AzureResourceGroup.ps1 nella cartella **Script** del progetto, selezionarlo e quindi scegliere **OK**.
   
   ![][9]
7. Dopo aver selezionato lo script, aggiornare il percorso dello script in modo che venga eseguito da Build.StagingDirectory, la stessa directory su cui è impostato *ArtifactsLocation*. A questo scopo è possibile aggiungere "$(Build.StagingDirectory)/" all'inizio del percorso dello script.
   
    ![][10]
8. Nella casella **Argomenti script** immettere i parametri seguenti, su una sola riga. Quando si esegue lo script in Visual Studio, è possibile vedere come vengono usati i parametri nella finestra **Output**. Si può usare questa finestra come punto di partenza per impostare i valori dei parametri nell'istruzione di compilazione.
   
   | Parametro | Descrizione |
   | --- | --- |
   | -ResourceGroupLocation |Valore della posizione geografica in cui si trova il gruppo di risorse, ad esempio **eastus** o **'East US'**. Aggiungere virgolette singole se nel nome è presente uno spazio. Per altre informazioni, vedere [Aree di Azure](https://azure.microsoft.com/regions/). |
   | -ResourceGroupName |Nome del gruppo di risorse usato per la distribuzione. |
   | -UploadArtifacts |Questo parametro, se presente, specifica che gli elementi devono essere caricati in Azure dal sistema locale. È sufficiente impostare questa opzione se la distribuzione del modello richiede elementi aggiuntivi che si prevede di preparare usando lo script di PowerShell, ad esempio gli script di configurazione o i modelli annidati. |
   | -StorageAccountName |Nome dell'account di archiviazione usato per la preparazione degli elementi per questa distribuzione. Questo parametro è obbligatorio solo se si copiano elementi in Azure. L'account di archiviazione non verrà creato automaticamente dalla distribuzione, deve esistere già. |
   | -StorageAccountResourceGroupName |Nome del gruppo di risorse associato all'account di archiviazione. Questo parametro è obbligatorio solo se si copiano elementi in Azure. |
   | -TemplateFile |Percorso del file modello nel progetto di distribuzione del gruppo di risorse di Azure. Per maggiore flessibilità, usare un percorso relativo alla posizione dello script di PowerShell per questo parametro, invece di un percorso assoluto. |
   | -TemplateParametersFile |Percorso del file modello nel progetto di distribuzione Gruppo di risorse di Azure. Per maggiore flessibilità, usare un percorso relativo alla posizione dello script di PowerShell per questo parametro, invece di un percorso assoluto. |
   | -ArtifactStagingDirectory |Questo parametro indica allo script di PowerShell la cartella da cui devono essere copiati i file binari del progetto. Il valore sostituisce quello predefinito usato dallo script di PowerShell. Per l'uso di VS Team Services, impostare il valore su: -ArtifactStagingDirectory $(Build.StagingDirectory) |
   
   Ecco un esempio di argomenti dello script, la riga è interrotta per facilitare la lettura:
   
   ```    
   -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
   -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
   –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)'    
   ```
   
   Al termine, la casella **Argomenti script** sarà simile alla seguente.
   
   ![][11]
9. Dopo aver aggiunto tutti gli elementi richiesti per l'istruzione di compilazione di Azure PowerShell, scegliere il pulsante **Accoda compilazione** per compilare il progetto. La schermata **Compilazione** mostra l'output dello script di PowerShell.

## Passaggi successivi
Per altre informazioni su Gestione risorse di Azure e sui gruppi di risorse di Azure, vedere [Panoramica di Gestione risorse di Microsoft Azure](resource-group-overview.md).

[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png

<!---HONumber=AcomDC_0803_2016-->