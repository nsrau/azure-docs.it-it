
1. Nelle impostazioni del back-end dell'app per dispositivi mobili fare clic su **Attività iniziali** > piattaforma client. 

2. In **Creare un'API di tabella** selezionare un valore per **Linguaggio back-end**, ovvero **C#** o **Node.js**:

	+ **Back-end Node.js** (tramite il portale): accettare l'acknowledgment e fare clic su **Crea tabella TodoItem**. Verrà creata una nuova tabella *TodoItem* nel database.
	 
		>[AZURE.IMPORTANT]Se si passa un back-end dell'app esistente a Node.js, tutti i contenuti del sito verranno sovrascritti.

	+ **Back-end .NET (C#)**: fare clic su **Download**, estrarre i file di progetto compressi nel computer locale, aprire la soluzione in Visual Studio, compilare il progetto per ripristinare i pacchetti NuGet e quindi distribuire il progetto in Azure. Per informazioni su come distribuire un progetto server back-end .NET in Azure, vedere la sezione [Procedura: Pubblicare il progetto server](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#publish-server-project) nell'argomento relativo all'SDK del back-end .NET.
	 
Il back-end dell'app per dispositivi mobili è ora pronto per l'uso con l'app client.

<!---HONumber=AcomDC_1223_2015-->