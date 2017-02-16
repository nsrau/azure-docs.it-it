

Questo articolo descrive come distribuire un set di scalabilità della macchina virtuale di Azure usando una distribuzione del gruppo di risorse di Visual Studio.

[set di scalabilità della macchina virtuale di Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) sono una risorsa di calcolo di Azure per distribuire e gestire una raccolta di macchine virtuali simili con opzioni facilmente integrabili per la scalabilità automatica e il bilanciamento del carico. È possibile eseguire il provisioning e distribuire set di scalabilità della macchina virtuale tramite i [modelli di Gestione risorse di Azure](https://github.com/Azure/azure-quickstart-templates). I modelli di Gestione risorse di Azure possono essere distribuiti tramite l'interfaccia della riga di comando di Azure, PowerShell, REST e direttamente da Visual Studio. Visual Studio offre un set di modelli di esempio che possono essere distribuiti come parte di un progetto di distribuzione del gruppo di risorse di Azure.

Le distribuzioni del gruppo di risorse di Azure sono un modo di raggruppare e pubblicare un set di risorse di Azure correlate con un'unica operazione di distribuzione. Sono disponibili altre informazioni in [Creazione e distribuzione di gruppi di risorse di Azure tramite Visual Studio](../articles/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="pre-requisites"></a>Prerequisiti
Per iniziare a distribuire set di scalabilità della macchina virtuale in Visual Studio è necessario quanto segue:

* Visual Studio 2013 o 2015
* Azure SDK 2.7 o 2.8

Nota: queste istruzioni presuppongono l'uso di Visual Studio 2015 con [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>Creazione di un progetto
1. Creare un nuovo progetto in Visual Studio 2015 scegliendo **File | Nuovo | Progetto**.
   
    ![File Nuovo][file_new]
2. In **Visual C# | Cloud**, scegliere **Azure Resource Manager** per creare un progetto per la distribuzione di un modello di Azure Resource Manager.
   
    ![Crea progetto][create_project]
3. Dall'elenco dei modelli, selezionare il modello di set di scalabilità della macchina virtuale Linux o Windows.
   
   ![Seleziona modello][select_Template]
4. Dopo aver creato il progetto saranno disponibili gli script di distribuzione di PowerShell, un modello di gestione risorse di Azure e un file di parametri per il set di scalabilità della macchina virtuale.
   
    ![Esplora soluzioni][solution_explorer]

## <a name="customize-your-project"></a>Personalizzare il progetto
È ora possibile modificare il modello per personalizzarlo in base alle esigenze dell'applicazione, ad esempio aggiungendo proprietà di estensione della macchina virtuale o modificando le regole di bilanciamento del carico. Per impostazione predefinita, i modelli del set di scalabilità della macchina virtuale sono configurati per distribuire l'estensione AzureDiagnostics, che semplifica l'aggiunta di regole di scalabilità automatica. L'estensione distribuisce anche un servizio di bilanciamento del carico con un indirizzo IP pubblico, configurato con regole NAT in entrata che consentono di connettersi a istanze della macchina virtuale con SSH (Linux) o RDP (Windows): l'intervallo di porte di front-end inizia a 50000, ovvero nel caso di Linux, se si esegue il comando SSH alla porta 50000 dell'indirizzo IP pubblico (o nome di dominio) si verrà instradati alla porta 22 della prima macchina virtuale nel set di scalabilità. La connessione alla porta 50001 verrà instradata alla porta 22 della seconda macchina virtuale e così via.

 Un buon metodo per modificare i modelli con Visual Studio consiste nell'usare la struttura JSON per organizzare i parametri, le variabili e le risorse. Comprendendo lo schema, Visual Studio è in grado di indicare errori nel modello prima che venga distribuito.

![Esplora JSON][json_explorer]

## <a name="deploy-the-project"></a>Distribuire il progetto
1. Distribuire il modello di Gestione risorse di Azure in Azure per creare la risorsa del set di scalabilità della macchina virtuale. Fare clic con il pulsante destro del mouse sul nodo del progetto e scegliere **Distribuisci | Nuova distribuzione**.
   
    ![Modello di distribuzione][5deploy_Template]
2. Selezionare la sottoscrizione nella finestra di dialogo "Distribuisci in gruppo di risorse".
   
    ![Modello di distribuzione][6deploy_Template]
3. Da qui è anche possibile creare un nuovo gruppo di risorse di Azure a cui distribuire il modello.
   
    ![Nuovo gruppo di risorse][new_resource]
4. Selezionare quindi il pulsante **Modifica parametri** per immettere i parametri che verranno trasferiti al modello. Sono necessari determinati valori (ad esempio il nome utente e la password) per il sistema operativo per creare la distribuzione.
   
    ![Modifica parametri][edit_parameters]
5. Fare quindi clic su **Distribuisci**. La finestra **Output** visualizzerà lo stato della distribuzione. Si noti che l'azione esegue lo script **Deploy-AzureResourceGroup.ps1** .
   
   ![Finestra Output][output_window]

## <a name="exploring-your-vm-scale-set"></a>Esplorazione del set di scalabilità della macchina virtuale
Dopo aver completato la distribuzione, è possibile visualizzare il nuovo set di scalabilità della macchina virtuale in **Cloud Explorer** di Visual Studio (aggiornare l'elenco). Cloud Explorer consente di gestire le risorse di Azure in Visual Studio durante lo sviluppo di applicazioni. È anche possibile visualizzare il set di scalabilità della macchina virtuale nel portale di Azure e in Esplora risorse di Azure.

![Cloud Explorer][cloud_explorer]

 Il portale è il modo migliore per gestire la visualizzazione dell'infrastruttura di Azure con un Web browser, mentre Esplora risorse di Azure offre un modo semplice per esplorare le risorse di Azure ed eseguirne il debug, offrendo una "visualizzazione per istanza" e visualizzando anche i comandi PowerShell per le risorse che si stanno analizzando. Mentre i set di scalabilità della macchina virtuale sono visualizzati in anteprima, Esplora risorse mostrerà il livello massimo di dettaglio per i set di scalabilità della macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver distribuito i set di scalabilità della macchina virtuale tramite Visual Studio è possibile personalizzare ulteriormente il progetto in base alle esigenze dell'applicazione. Ad esempio, impostando la scalabilità automatica aggiungendo una risorsa di Insights, aggiungendo l'infrastruttura al modello come macchine virtuali autonome o distribuendo applicazioni usando l'estensione dello script personalizzata. Un'utile fonte di modelli di esempio è disponibile nel repository GitHub dedicato ai [modelli della guida introduttiva di Azure](https://github.com/Azure/azure-quickstart-templates) (cercare "vmss").

[file_new]: ./media/virtual-machines-common-scale-sets-visual-studio/1-FileNew.png
[create_project]: ./media/virtual-machines-common-scale-sets-visual-studio/2-CreateProject.png
[select_Template]: ./media/virtual-machines-common-scale-sets-visual-studio/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machines-common-scale-sets-visual-studio/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machines-common-scale-sets-visual-studio/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machines-common-scale-sets-visual-studio/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machines-common-scale-sets-visual-studio/6-DeployTemplate.png
[new_resource]: ./media/virtual-machines-common-scale-sets-visual-studio/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machines-common-scale-sets-visual-studio/8-EditParameter.png
[output_window]: ./media/virtual-machines-common-scale-sets-visual-studio/9-Output.png
[cloud_explorer]: ./media/virtual-machines-common-scale-sets-visual-studio/12-CloudExplorer.png

<!--HONumber=Jan17_HO3-->


