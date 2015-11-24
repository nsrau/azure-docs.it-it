
1. Nelle impostazioni del back-end dell'app per dispositivi mobili scorrere verso il basso fino alla sezione **Dispositivo mobile**, fare clic su **Attività iniziali** > piattaforma client. 

2. In **Creare un'API di tabella** selezionare un valore per **Linguaggio back-end**, ovvero **C#** o **Node.js**:

	+ **Back-end .NET (C#)**: fare clic su **Download**, estrarre i file di progetto compressi nel computer locale, aprire la soluzione in Visual Studio, compilare il progetto per ripristinare i pacchetti NuGet e quindi distribuire il progetto in Azure. Per informazioni su come distribuire un progetto server back-end .NET in Azure, vedere la sezione *Distribuire il progetto nell'app Web* di [Creare un'app Web ASP.NET nel servizio app di Azure](../articles/app-service-web/web-sites-dotnet-get-started.md#deploy-the-project-to-the-web-app). Nel servizio app un back-end dell'app per dispositivi mobili equivale a un'app Web.
	 
	+ **Back-end Node.js**: accettare l'acknowledgment e fare clic su **Crea tabella TodoItem**. Verrà creata una nuova tabella *TodoItem* nel database.
	 
		>[AZURE.IMPORTANT]Se si passa un back-end dell'app esistente a Node.js, tutti i contenuti del sito verranno sovrascritti.

Il back-end dell'app per dispositivi mobili è ora pronto per l'uso con l'app client.

<!---HONumber=Nov15_HO4-->