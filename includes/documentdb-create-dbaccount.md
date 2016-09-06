1.	Accedere al [portale di Azure](https://portal.azure.com/).
2.	Nell'indice scegliere **Nuovo**, fare clic su **Dati + archiviazione** e quindi su **DocumentDB (NoSQL)**.

	![Screenshot del portale di Azure, in cui sono evidenziati More Services (Altri servizi) e DocumentDB (NoSQL)](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. Nel pannello **Nuovo account** specificare la configurazione per l'account DocumentDB.

	![Schermata del pannello Nuovo DocumentDB](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)  


	- Nella casella **ID** immettere un nome per identificare l'account DocumentDB. Quando l’**ID** viene convalidato, appare un segno di spunta verde nella casella **ID**. Il valore **ID** diventa il nome host nell'URI. L'**ID** può contenere solo lettere minuscole, numeri e il carattere '-' e deve avere una lunghezza compresa tra 3 e 50 caratteri. Tenere presente che al nome di endpoint scelto viene aggiunto *documents.azure.com* e il risultato finale sarà l'endpoint dell'account DocumentDB.

	- Per **Sottoscrizione** selezionare la sottoscrizione di Azure da usare per l'account DocumentDB. Se l'account ha solo una sottoscrizione, tale account viene selezionato per impostazione predefinita.

	- In **Gruppo di risorse** selezionare o creare un gruppo di risorse per l'account DocumentDB. Per impostazione predefinita, viene creato un nuovo gruppo di risorse. Per altre informazioni, vedere [Uso del portale di Azure per gestire le risorse di Azure](../articles/azure-portal/resource-group-portal.md).

	- Usare **Località** per specificare la posizione geografica in cui verrà ospitato l'account DocumentDB.
	
    - Per semplificare l'accesso all'account e alle risorse che verranno create in futuro, selezionare **Aggiungi al dashboard**.

4.	Dopo aver configurato le opzioni del nuovo account DocumentDB, fare clic su **Crea**. Per verificare lo stato della distribuzione, è possibile monitorare l'avanzamento nella Schermata iniziale. ![Schermata del riquadro Creazione sulla Schermata iniziale - creazione di database in linea](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-3.png)

	o dall'Hub di notifica.

	![Creare database in modo rapido - Schermata dell'hub Notifiche, che mostra l'account DocumentDB creato](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-4.png) 

	![Schermata dell'hub Notifiche, che mostra che l'account DocumentDB è stato creato correttamente e distribuito a un gruppo di risorse - Creatore di database online](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-5.png)

5.	Dopo la creazione, l'account DocumentDB è pronto all'uso con le impostazioni predefinite. Si noti che la coerenza predefinita dell'account DocumentDB è impostata su **Sessione**. È possibile modificare la coerenza predefinita facendo clic su **Coerenza predefinita** nel menu della risorsa. Per altre informazioni sui livelli di coerenza offerti da DocumentDB, vedere [Livelli di coerenza in DocumentDB](../articles/azure-portal/resource-group-portal.md).

    ![Schermata del pannello del gruppo di risorse - iniziare lo sviluppo di applicazioni](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-6.png)

    ![Screenshot del pannello del livello di coerenza, coerenza di sessione](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-7.png)  

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps
[documentdb-manage]: ../articles/documentdb/documentdb-manage.md

<!---HONumber=AcomDC_0831_2016-->