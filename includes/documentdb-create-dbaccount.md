1.	Accedere al [portale di anteprima di Azure](https://portal.azure.com/).
2.	Nell'indice, scegliere **Nuovo**, selezionare **Dati + archiviazione**, quindi fare clic su **DocumentDB**. 

	![Screen shot of the Azure Preview portal, highlighting the **New** button, **Data + storage** in the Create blade, and **DocumentDB** in the Data + storage blade][1] 

	In alternativa, dalla schermata iniziale accedere ad Azure Marketplace, selezionare **Dati + analisi**, scegliere **DocumentDB** e quindi fare clic su **Crea**. 
	
	![Screen shot of the Azure Preview portal, showing the Marketplace blade with the DocumentDB tile highlighted, and the DocumentDB blade with the Create button highlighted][2] 
 

3. Nel pannello **Nuovo DocumentDB (anteprima)** specificare la configurazione desiderata per l'account DocumentDB. 
 
	![Screen shot of the New DocumentDB (Preview) blade][3] 


	- Nella casella **ID** immettere un nome per identificare l'account DocumentDB. Questo valore diventa il nome host nell'URI. L'ID può contenere solo lettere minuscole, numeri e il carattere '-' e deve avere una lunghezza compresa tra 3 e 50 caratteri. Tenere presente che al nome di endpoint scelto viene aggiunto documents.azure.com e il risultato finale sarà l'endpoint dell'account DocumentDB.

	- La sezione **Livello di prezzo** è bloccata, perché l'anteprima di DocumentDB supporta un unico livello di prezzo standard. Per altre informazioni, vedere [DocumentDB - Prezzi](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).

	- La sezione **Configurazione facoltativa** consente di specificare la capacità iniziale allocata all'account DocumentDB. DocumentDB viene offerto in unità di capacità, che consentono di scalare l'account DocumentDB in base alle esigenze. Ogni unità di capacità include velocità effettiva e spazio di archiviazione di database riservato. Per impostazione predefinita, viene effettuato il provisioning di 1 unità di capacità. È possibile modificare il numero di unità di capacità disponibili per l'account DocumentDB in qualsiasi momento mediante il [portale di gestione di anteprima](https://portal.azure.com/#gallery/Microsoft.DocumentDB). Per informazioni sulla capacità e sulla velocità effettiva dell'account DocumentDB, vedere l'articolo [Gestire la capacità e le prestazioni di DocumentDB][documentdb-manage].

	- In **Gruppo di risorse** selezionare o creare un gruppo di risorse per l'account DocumentDB. Per impostazione predefinita, verrà creato un nuovo gruppo di risorse. È tuttavia possibile selezionare un gruppo di risorse esistente al quale aggiungere l'account DocumentDB. Per altre informazioni, vedere [Uso dei gruppi di risorse per la gestione delle risorse di Azure](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups/).

	- Per **Sottoscrizione** selezionare la sottoscrizione di Azure che si desidera usare per l'account DocumentDB. Se l'account dispone di una sola sottoscrizione, tale account verrà selezionato automaticamente.*
 
	- Usare **Posizione** per specificare la posizione geografica in cui verrà ospitato l'account DocumentDB. 

3.	Dopo aver configurato le opzioni del nuovo account DocumentDB, fare clic su **Crea**. La creazione dell'account DocumentDB può richiedere alcuni minuti. Per verificare lo stato, è possibile monitorare l'avanzamento nella Schermata iniziale 
	![Screen shot of the Creating tile on the Startboard][4] 
 
	o dall'Hub di notifica. 

	![Screen shot of the Notifications hub, showing that the DocumentDB account is being created][5] 

	![Screen shot of the Notifications hub, showing that the DocumentDB account was created successfully and deplyed to a resource group][6]

4.	Dopo la creazione, l'account DocumentDB è pronto all'uso con le impostazioni predefinite.

	*Si noti che la coerenza predefinita dell'account DocumentDB sarà impostata su Sessione. È possibile modificare l'impostazione di coerenza predefinita tramite l'[anteprima del portale di gestione](https://portal.azure.com/#gallery/Microsoft.DocumentDB)*.* 
	![Screen shot of the Resource Group blade][7] 


<!--Image references-->
[1]: ./media/documentdb-create-dbaccount/ca1.png
[2]: ./media/documentdb-create-dbaccount/ca2.png
[3]: ./media/documentdb-create-dbaccount/ca3.png
[4]: ./media/documentdb-create-dbaccount/ca4.png
[5]: ./media/documentdb-create-dbaccount/ca5.png
[6]: ./media/documentdb-create-dbaccount/ca6.png
[7]: ./media/documentdb-create-dbaccount/ca7.png

[Procedura: Creare un account DocumentDB]: #Howto
[Passaggi successivi]: #NextSteps
[documentdb-manage]:../documentdb-manage/
<!--HONumber=47-->
