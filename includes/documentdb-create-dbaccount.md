1.	Accedere al [portale di Microsoft Azure](https://portal.azure.com/) online.
2.	Nell'indice scegliere **Nuovo**, selezionare **Dati + archiviazione**, quindi fare clic su **DocumentDB**.

	![Schermata del portale di Azure per creare un database, in cui sono evidenziati il pulsante Nuovo, Dati + archiviazione nel pannello Crea e Azure DocumentDB nel pannello Dati + archiviazione](media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. Nel pannello **Nuovo account DocumentDB** specificare la configurazione desiderata per l'account DocumentDB.

	![Schermata del pannello Nuovo DocumentDB](media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)


	- Nella casella **ID** immettere un nome per identificare l'account DocumentDB. Quando l’**ID** viene convalidato, appare un segno di spunta verde nella casella **ID**. Il valore **ID** diventa il nome host nell'URI. L'**ID** può contenere solo lettere minuscole, numeri e il carattere '-' e deve avere una lunghezza compresa tra 3 e 50 caratteri. Tenere presente che al nome di endpoint scelto viene aggiunto *documents.azure.com* e il risultato finale sarà l'endpoint dell'account DocumentDB.

	- Per **Sottoscrizione** selezionare la sottoscrizione di Azure da usare per l'account DocumentDB. Se l'account dispone solo una sottoscrizione, tale account viene selezionato per impostazione predefinita.

	- In **Gruppo di risorse** selezionare o creare un gruppo di risorse per l'account DocumentDB. Per impostazione predefinita, verrà scelto un gruppo di risorse esistente nella sottoscrizione di Azure. È tuttavia possibile scegliere di creare un nuovo gruppo di risorse a cui aggiungere l'account DocumentDB. Per altre informazioni, vedere [Uso del portale di Azure per gestire le risorse di Azure](resource-group-portal.md).

	- Usare **Località** per specificare la posizione geografica in cui verrà ospitato l'account DocumentDB.

4.	Dopo aver configurato le opzioni del nuovo account DocumentDB, fare clic su **Crea**.  Può richiedere alcuni minuti per creare l'account DocumentDB. Per verificare lo stato, è possibile monitorare l'avanzamento nella Schermata iniziale  
	![Schermata del riquadro Creazione sulla Schermata iniziale - creazione di database in linea](media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-3.png)

	o dall'Hub di notifica.

	![Creare database in modo rapido - Schermata dell'hub Notifiche, che mostra l'account DocumentDB creato](media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-4.png)

	![Schermata dell'hub Notifiche, che mostra che l'account DocumentDB è stato creato correttamente e distribuito a un gruppo di risorse - Creatore di database online](media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-5.png)

5.	Dopo aver creato l'account DocumentDB, è pronto per l'utilizzo con le impostazioni predefinite nel portale in linea. Si noti che la coerenza predefinita dell'account DocumentDB è impostata su **Sessione**. È possibile modificare l'impostazione di coerenza predefinita facendo clic sull'**icona della chiave** nella **barra delle informazioni di base** e scegliendo quindi la voce **Coerenza predefinita** nel pannello **Tutte le impostazioni**.

    ![Schermata del pannello del gruppo di risorse - iniziare lo sviluppo di applicazioni](media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-6.png)

    ![Screenshot del pannello del livello di coerenza, coerenza di sessione](media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-7.png)

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps
[documentdb-manage]: ../articles/documentdb/documentdb-manage.md

<!---HONumber=AcomDC_0224_2016-->