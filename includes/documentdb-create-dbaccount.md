1.	Accedere al [portale di anteprima di Azure](https://portal.azure.com/).
2.	Nell'indice, scegliere **Nuovo**, selezionare **Dati + archiviazione**, quindi fare clic su **DocumentDB**. 

	![Schermata del portale di anteprima d Azure, che evidenzia il pulsante **Nuovo**, **Dati + archiviazione** nel pannello Crea e **DocumentDB** nel pannello Dati + archiviazione][1]   

	In alternativa, dalla schermata iniziale accedere ad Azure Marketplace, selezionare **Dati + analisi**, scegliere **DocumentDB** e quindi fare clic su **Crea**.  
	
	![Schermata del portale di anteprima di Azure, che mostra il panello Marketplace con il riquadro DocumentDB evidenziato e il pannello DocumentDB con il pulsante Crea evidenziato][2]   
   

3. Nel pannello **Nuovo DocumentDB (anteprima)** specificare la configurazione desiderata per l'account DocumentDB. 
 
	![Schermata del panello Nuovo DocumentDB (anteprima)][3] 


	- Nella casella **ID** immettere un nome per identificare l'account DocumentDB. Questo valore diventa il nome host nell'URI. L'ID può contenere solo lettere minuscole, numeri e il carattere '-' e deve avere una lunghezza compresa tra 3 e 50 caratteri. Tenere presente che al nome di endpoint scelto viene aggiunto documents.azure.com e il risultato finale sarà l'endpoint dell'account DocumentDB.

	- La sezione **Livello di prezzo** è bloccata, perché l'anteprima di DocumentDB supporta un unico livello di prezzo standard. Per altre informazioni, vedere [DocumentDB - Prezzi](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).

	- La sezione **Configurazione facoltativa** consente di specificare la capacità iniziale allocata all'account DocumentDB.  DocumentDB viene offerto in unità di capacità, che consentono di scalare l'account DocumentDB in base alle esigenze. Ogni unità di capacità include velocità effettiva e spazio di archiviazione di database riservato.  Per impostazione predefinita, viene effettuato il provisioning di 1 unità di capacità.  È possibile modificare il numero di unità di capacità disponibili per l'account DocumentDB in qualsiasi momento mediante il [portale di gestione di anteprima](https://portal.azure.com/#gallery/Microsoft.DocumentDB). Per informazioni sulla capacità e sulla velocità effettiva dell'account DocumentDB, vedere l'articolo [Gestire la capacità e le prestazioni di DocumentDB][documentdb-manage].

	- In **Gruppo di risorse** selezionare o creare un gruppo di risorse per l'account DocumentDB.  Per impostazione predefinita, verrà creato un nuovo gruppo di risorse.  È tuttavia possibile selezionare un gruppo di risorse esistente al quale aggiungere l'account DocumentDB. Per ulteriori informazioni, vedere [Uso di Gruppi di risorse per gestire le risorse di Azure. 
](azure-preview-portal-using-resource-groups.md).

	- Per **Sottoscrizione** selezionare la sottoscrizione di Azure che si desidera usare per l'account DocumentDB. Se l'account ha una sola sottoscrizione, verrà selezionato automaticamente.*
 
	- Usare **Posizione** per specificare la posizione geografica in cui verrà ospitato l'account DocumentDB.   

3.	Dopo aver configurato le opzioni del nuovo account DocumentDB, fare clic su **Crea**.  La creazione dell'account DocumentDB può richiedere alcuni minuti.  Per verificare lo stato, è possibile monitorare l'avanzamento nella Schermata iniziale  
	![Schermata del riquadro Creazione sulla schermata iniziale][4]  
  
	o dall'Hub di notifica.  

	![Schermata dell'hub Notifica, che mostra l'account DocumentDB creato ][5]  

	![Schermata dell'hub Notifica, che mostra l'account DocumentDB creato correttamente e distribuito a un gruppo di risorse][6]

4.	Dopo la creazione, l'account DocumentDB è pronto all'uso con le impostazioni predefinite.

	*Tenere presente che la coerenza predefinita dell'account DocumentDB sarà impostata su Sessione.  È possibile modificare le impostazioni di coerenza predefinite tramite il [portale di gestione di anteprima](https://portal.azure.com/#gallery/Microsoft.DocumentDB).*  
	![Schermata del pannello Gruppo di risorse][7]  


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
[documentdb-manage]:../articles/documentdb/documentdb-manage.md

<!--HONumber=49-->