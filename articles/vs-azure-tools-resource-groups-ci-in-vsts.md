---
title: Integrazione continua in VS Team Services con i progetti di gruppi di risorse di Azure | Documentazione Microsoft
description: Descrive come configurare l&quot;integrazione continua in Visual Studio Team Services con i progetti di distribuzione Gruppo di risorse di Azure in Visual Studio.
services: visual-studio-online
documentationcenter: na
author: mlearned
manager: erickson-doug
editor: 
ms.assetid: b81c172a-be87-4adc-861e-d20b94be9e38
ms.service: azure-resource-manager
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b178359d2f55e9137b39f42f5562e7bb8a642c57


---
# <a name="continuous-integration-in-visual-studio-team-services-using-azure-resource-group-deployment-projects"></a>Integrazione continua in Visual Studio Team Services con i progetti di distribuzione Gruppo di risorse di Azure
Per distribuire un modello di Azure, è necessario eseguire attività per completare le varie fasi, ovvero compilazione, test, copia in Azure (detta anche "staging") e distribuzione del modello.  Esistono due modi diversi per distribuire i modelli Visual Studio Team Services (VS Team Services). Entrambi i metodi forniscono gli stessi risultati, quindi è opportuno scegliere quello che meglio si adatta al proprio flusso di lavoro.

1. Aggiungere un singolo passaggio alla definizione di compilazione che esegue lo script di PowerShell incluso nel progetto di distribuzione Gruppo di risorse di Azure (Deploy-AzureResourceGroup.ps1). Lo script copia gli elementi e quindi distribuisce il modello.
2. Aggiungere più istruzioni di compilazione di VS Team Services, ognuna delle quali esegue un'attività della fase.

Questo articolo illustra entrambe le opzioni.  La prima opzione ha il vantaggio di usare lo stesso script usato dagli sviluppatori di Visual Studio fornendo coerenza durante tutto il ciclo di vita.  La seconda opzione offre una comoda alternativa allo script predefinito.  Entrambe le procedure presuppongono che sia già disponibile un progetto di distribuzione di Visual Studio archiviato in VS Team Services.

## <a name="copy-artifacts-to-azure"></a>Copiare gli elementi in Azure
Indipendentemente dallo scenario, se sono disponibili elementi necessari per la distribuzione del modello, occorre concedere ad Azure Resource Manager l'accesso a tali elementi. Questi elementi possono includere file, ad esempio:

* Modelli annidati
* Script di configurazione e script DSC
* File binari dell'applicazione

### <a name="nested-templates-and-configuration-scripts"></a>Modelli annidati e script di configurazione
Quando si usano i modelli forniti da Visual Studio, o compilati con frammenti di Visual Studio, lo script di PowerShell non solo prepara gli elementi, ma imposta anche l'URI con parametri per le risorse per le diverse distribuzioni. Lo script copia quindi gli elementi in un contenitore protetto in Azure, crea un token di firma di accesso condiviso per tale contenitore e quindi passa le informazioni alla distribuzione del modello. Per altre informazioni sui modelli annidati, vedere [Creare una distribuzione di modelli](https://msdn.microsoft.com/library/azure/dn790564.aspx) .  Quando si usano le attività in VS Team Services, è necessario selezionare le attività appropriate per la distribuzione del modello e, se necessario, passare i valori dei parametri dal passaggio di gestione temporanea alla distribuzione del modello.

## <a name="set-up-continuous-deployment-in-vs-team-services"></a>Configurare la distribuzione continua in VS Team Services
Per chiamare lo script di PowerShell in VS Team Services, è necessario aggiornare la definizione di compilazione. In breve, ecco i passaggi: 

1. Modificare la definizione di compilazione.
2. Configurare l'autorizzazione di Azure in VS Team Services.
3. Aggiungere un'istruzione di compilazione di Azure PowerShell che fa riferimento allo script di PowerShell nel progetto di distribuzione Gruppo di risorse di Azure.
4. Impostare il valore del parametro *-ArtifactsStagingDirectory* per l'interazione con un progetto compilato in VS Team Services.

### <a name="detailed-walkthrough-for-option-1"></a>Procedura dettagliata per l'opzione 1
I passaggi seguenti illustrano la procedura necessaria per configurare la distribuzione continua in VS Team Services usando una singola attività che esegue lo script PowerShell nel progetto. 

1. Modificare la definizione di compilazione di VS Team Services e aggiungere un'istruzione di compilazione di Azure PowerShell. Scegliere la definizione di compilazione nella categoria **Definizioni di compilazione** e quindi scegliere il collegamento **Modifica**.
   
   ![Modificare la definizione di compilazione][0]
2. Aggiungere un'istruzione di compilazione di **Azure PowerShell** alla definizione di compilazione e quindi scegliere il pulsante **Aggiungi istruzione di compilazione...**. .
   
   ![Aggiungere un'istruzione di compilazione][1]
3. Scegliere la categoria **Deploy task** (Distribuisci attività), selezionare l'attività **Azure PowerShell** e quindi scegliere il pulsante **Aggiungi**.
   
   ![Aggiungere attività][2]
4. Scegliere l'istruzione di compilazione di **Azure PowerShell** e quindi compilare i relativi valori.
   
   1. Se è già stato aggiunto un endpoint di servizio di Azure a VS Team Services, scegliere la sottoscrizione nella casella di riepilogo a discesa **Sottoscrizione Azure** e quindi passare alla sezione successiva. 
      
      Se non si ha un endpoint di servizio di Azure in VS Team Services, è necessario aggiungerne uno. Questa sottosezione illustra il processo. Se l'account Azure usa un account Microsoft, ad esempio Hotmail, è necessario eseguire i passaggi seguenti per ottenere un'autenticazione dell'entità servizio.
   2. Scegliere il collegamento **Gestisci** accanto all'elenco a discesa **Sottoscrizione Azure**.
      
      ![Gestire le sottoscrizioni di Azure][3]
   3. Scegliere **Azure** nell'elenco a discesa **Nuovo endpoint servizio**.
      
      ![Nuovo endpoint di servizio][4]
   4. Nella finestra di dialogo **Aggiungi sottoscrizione di Azure** selezionare l'opzione **Entità servizio**.
      
      ![Oggetto entità servizio][5]
   5. Aggiungere le informazioni sulla sottoscrizione di Azure nella finestra di dialogo **Aggiungi sottoscrizione di Azure** . È necessario specificare gli elementi seguenti:
      
      * ID sottoscrizione
      * Nome sottoscrizione
      * ID entità servizio
      * Chiave entità servizio
      * ID tenant
   6. Aggiungere un nome a propria scelta nella casella **Sottoscrizione** . Questo valore verrà visualizzato in seguito nell'elenco a discesa **Sottoscrizione Azure** in VS Team Services. 
   7. Se non si conosce l'ID sottoscrizione di Azure, è possibile usare uno dei seguenti comandi per ottenerlo.
      
      Per gli script di Azure PowerShell usare:
      
      `Get-AzureRmSubscription`
      
      Per l'interfaccia della riga di comando di Azure usare:
      
      `azure account show`
   8. Per ottenere un ID entità servizio, una chiave entità servizio e un ID tenant, seguire la procedura in [Usare il portale per creare un'applicazione Active Directory e un'entità servizio](resource-group-create-service-principal-portal.md) o [Authenticating a service principal with Azure Resource Manager](resource-group-authenticate-service-principal.md) (Autenticazione di un'entità servizio con Azure Resource Manager).
   9. Aggiungere i valori di ID entità servizio, chiave entità servizio e ID tenant alla finestra di dialogo **Aggiungi sottoscrizione di Azure**, quindi scegliere il pulsante **OK**.
      
      Ora è disponibile un'entità servizio valida da usare per eseguire lo script di Azure PowerShell.
5. Modificare la definizione di compilazione e scegliere l'istruzione di compilazione **Azure PowerShell** . Selezionare la sottoscrizione nella casella di riepilogo a discesa **Sottoscrizione Azure** . Se la sottoscrizione non viene visualizzata, scegliere il pulsante **Aggiorna** accanto al collegamento **Gestisci**. 
   
   ![Configurare l'attività di compilazione di Azure PowerShell][8]
6. Fornire un percorso per lo script di PowerShell Deploy-AzureResourceGroup.ps1. A tale scopo, scegliere il pulsante con i puntini di sospensione (...) accanto alla casella **Percorso script**, passare allo script di PowerShell Deploy-AzureResourceGroup.ps1 nella cartella **Script** del progetto, selezionarlo e quindi scegliere **OK**.    
   
   ![Selezionare il percorso dello script][9]
7. Dopo aver selezionato lo script, aggiornare il percorso dello script in modo che venga eseguito da Build.StagingDirectory, la stessa directory su cui è impostato *ArtifactsLocation* . A questo scopo è possibile aggiungere "$(Build.StagingDirectory)/" all'inizio del percorso dello script.
   
    ![Modificare il percorso dello script][10]
8. Nella casella **Argomenti script** immettere i parametri seguenti, su una sola riga. Quando si esegue lo script in Visual Studio, è possibile vedere come vengono usati i parametri nella finestra **Output** . Si può usare questa finestra come punto di partenza per impostare i valori dei parametri nell'istruzione di compilazione.
   
   | Parametro | Descrizione |
   | --- | --- |
   | -ResourceGroupLocation |Valore dell'area geografica in cui si trova il gruppo di risorse, ad esempio **eastus** o **'East US'**. Aggiungere virgolette singole se nel nome è presente uno spazio. Per altre informazioni, vedere [Aree di Azure](https://azure.microsoft.com/en-us/regions/). |
   | -ResourceGroupName |Nome del gruppo di risorse usato per la distribuzione. |
   | -UploadArtifacts |Questo parametro, se presente, specifica che gli elementi devono essere caricati in Azure dal sistema locale. È sufficiente impostare questa opzione se la distribuzione del modello richiede elementi aggiuntivi che si prevede di preparare usando lo script di PowerShell, ad esempio gli script di configurazione o i modelli annidati. |
   | -StorageAccountName |Nome dell'account di archiviazione usato per la preparazione degli elementi per questa distribuzione.  Questo parametro viene usato solo se si sta eseguendo la gestione temporanea di elementi per la distribuzione. Se questo parametro viene specificato, viene creato un nuovo account di archiviazione, se lo script non ne ha creato uno durante una distribuzione precedente.  Se il parametro viene specificato, l'account di archiviazione deve esistere già. |
   | -StorageAccountResourceGroupName |Nome del gruppo di risorse associato all'account di archiviazione. Questo parametro è obbligatorio solo se si specifica un valore per il parametro StorageAccountName. |
   | -TemplateFile |Percorso del file modello nel progetto di distribuzione del gruppo di risorse di Azure. Per maggiore flessibilità, usare un percorso relativo alla posizione dello script di PowerShell per questo parametro, invece di un percorso assoluto. |
   | -TemplateParametersFile |Percorso del file modello nel progetto di distribuzione Gruppo di risorse di Azure. Per maggiore flessibilità, usare un percorso relativo alla posizione dello script di PowerShell per questo parametro, invece di un percorso assoluto. |
   | -ArtifactStagingDirectory |Questo parametro indica allo script di PowerShell la cartella da cui devono essere copiati i file binari del progetto. Il valore sostituisce quello predefinito usato dallo script di PowerShell. Per l'uso di VS Team Services, impostare il valore su: -ArtifactStagingDirectory $(Build.StagingDirectory) |
   
   Ecco un esempio di argomenti dello script, la riga è interrotta per facilitare la lettura:
   
   ```    
   -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
   -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
   –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)'    
   ```
   
   Al termine, la casella **Argomenti script** sarà simile all'elenco seguente:
   
   ![Argomenti script][11]
9. Dopo aver aggiunto tutti gli elementi richiesti per l'istruzione di compilazione di Azure PowerShell, scegliere il pulsante **Accoda compilazione** per compilare il progetto. La schermata **Compilazione** mostra l'output dello script di PowerShell.

### <a name="detailed-walkthrough-for-option-2"></a>Procedura dettagliata per l'opzione 2
I passaggi seguenti illustrano la procedura necessaria per configurare la distribuzione continua in VS Team Services usando le attività predefinite.

1. Modificare la definizione di compilazione di Visual Studio Team Services per aggiungere due nuove istruzioni di compilazione. Scegliere la definizione di compilazione nella categoria **Definizioni di compilazione** e quindi scegliere il collegamento **Modifica**.
   
   ![Modificare la definizione di compilazione][12]
2. Aggiungere le nuove istruzioni di compilazione alla definizione di compilazione usando **Aggiungi istruzione di compilazione...** .
   
   ![Aggiungere un'istruzione di compilazione][13]
3. Scegliere la categoria **Deploy task** (Distribuisci attività), selezionare l'attività **Copia dei file di Azure** e quindi scegliere il pulsante **Aggiungi**.
   
   ![Aggiungere l'attività Copia dei file di Azure][14]
4. Scegliere l'attività ** 	Distribuzione gruppo di risorse di Azure**, quindi scegliere il pulsante **Aggiungi** e quindi **Chiudi** per il **catalogo delle attività**.
   
   ![Aggiungere attività Distribuzione gruppo di risorse di Azure][15]
5. Scegliere l'attività **Copia dei file di Azure** e compilare i relativi valori.
   
   Se è già stato aggiunto un endpoint di servizio di Azure a VS Team Services, scegliere la sottoscrizione nell'elenco a discesa **Sottoscrizione Azure**.  Se non si dispone di una sottoscrizione vedere la procedura dettagliata per l'[opzione 1](#detailed-walkthrough-for-option-1) per istruzioni sull'impostazione di una sottoscrizione in Visual STUDIO Team Services.
   
   * Origine: immettere **$(Build.StagingDirectory)**
   * Tipo di connessione ad Azure: selezionare **Azure Resource Manager**
   * Sottoscrizione di Azure Resource Manager: selezionare la sottoscrizione per l'account di archiviazione nell'elenco a discesa **Sottoscrizione di Azure**. Se la sottoscrizione non viene visualizzata, scegliere il pulsante **Aggiorna** accanto al collegamento **Gestisci**.
   * Tipo di destinazione: selezionare **Blob di Azure**
   * Account di archiviazione di Resource Manager: selezionare l'account di archiviazione da usare per la gestione temporanea degli elementi
   * Nome contenitore: immettere il nome del contenitore da usare per la gestione temporanea, che può essere qualsiasi nome di contenitore valido, ma usare un contenitore dedicato a questa definizione di compilazione
   
   Per i valori di output:
   
   * URI contenitore di archiviazione: immettere **artifactsLocation**
   * Token di firma di accesso condiviso contenitore di archiviazione: immettere **artifactsLocationSasToken**
   
   ![Configurare l'attività Copia dei file di Azure][16]
6. Scegliere l'istruzione di compilazione di **Distribuzione gruppo di risorse di Azure** e quindi compilare i relativi valori.
   
   * Tipo di connessione ad Azure: selezionare **Azure Resource Manager**
   * Sottoscrizione di Azure Resource Manager: selezionare la sottoscrizione per la distribuzione nell'elenco a discesa **Sottoscrizione di Azure**. In genere, questa sarà la stessa sottoscrizione usata nel passaggio precedente.
   * Azione - selezionare **Creare o aggiornare un gruppo di risorse**
   * Gruppo di risorse: selezionare un gruppo di risorse o immettere il nome del nuovo gruppo di risorse per la distribuzione
   * Posizione: selezionare la posizione per il gruppo di risorse
   * Modello: immettere il percorso e il nome del modello da distribuire anteponendo **$(Build.StagingDirectory)**, ad esempio: **$(Build.StagingDirectory/DSC-CI/azuredeploy.json)**
   * Parametri modello: immettere il percorso e il nome dei parametri da usare, anteponendo **$(Build.StagingDirectory)**, ad esempio: **$(Build.StagingDirectory/DSC-CI/azuredeploy.parameters.json)**
   * Esegui override dei parametri del modello: immettere o copiare e incollare il codice seguente:
     
     ```    
     -_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken (ConvertTo-SecureString -String "$(artifactsLocationSasToken)" -AsPlainText -Force)
     ```
     ![Configurare l'attività Distribuzione gruppo di risorse di Azure][17]
7. Dopo avere aggiunto tutti gli elementi richiesti, salvare la definizione di compilazione e scegliere **Accoda nuova compilazione** nella parte superiore.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui gruppi di risorse di Azure e su Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](azure-resource-manager/resource-group-overview.md).

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
[12]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough13.png
[13]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough14.png
[14]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough15.png
[15]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough16.png
[16]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough17.png
[17]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough18.png



<!--HONumber=Dec16_HO2-->


