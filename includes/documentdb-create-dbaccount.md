1.	Accedere al [portale di anteprima di Microsoft Azure](https://portal.azure.com/).
2.	Nell'indice scegliere **Nuovo**, selezionare **Dati + archiviazione**, quindi fare clic su **DocumentDB**. 

	![Schermata del portale di anteprima di Azure, in cui sono evidenziati il pulsante Nuovo, Dati + archiviazione nel pannello Crea e DocumentDB nel pannello Dati + archiviazione][1]

	<!-- Alternatively, from the Startboard, you can browse the Azure Marketplace, select **Data + storage**, choose **DocumentDB**, and then click **Create**.  --><!-- ![Screen shot of the Azure Preview portal, showing the Marketplace blade with the DocumentDB tile highlighted, and the DocumentDB blade with the Create button highlighted][2]    -->
   

3. Nel pannello **Nuovo DocumentDB** specificare la configurazione desiderata per l'account DocumentDB.
 
	![Schermata del pannello Nuovo DocumentDB][3]


	- Nella casella **ID** immettere un nome per identificare l'account DocumentDB. Questo valore diventa il nome host nell'URI. L'**ID** può contenere solo lettere minuscole, numeri e il carattere '-' e deve avere una lunghezza compresa tra 3 e 50 caratteri. Tenere presente che al nome di endpoint scelto viene aggiunto *documents.azure.com* e il risultato finale sarà l'endpoint dell'account DocumentDB.

	- La sezione **Livello account** è bloccata perché DocumentDB supporta un solo livello account standard. Per altre informazioni, vedere [DocumentDB - Prezzi](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).

	- In **Gruppo di risorse** selezionare o creare un gruppo di risorse per l'account DocumentDB. Per impostazione predefinita, verrà creato un nuovo gruppo di risorse. È tuttavia possibile selezionare un gruppo di risorse esistente al quale aggiungere l'account DocumentDB. Per altre informazioni, vedere [Uso di Gruppi di risorse per gestire le risorse di Azure](resource-group-portal.md).

	- Per **Sottoscrizione** selezionare la sottoscrizione di Azure da usare per l'account DocumentDB. Se l'account dispone di una sola sottoscrizione, tale account verrà selezionato automaticamente.
 
	- Usare **Località** per specificare la posizione geografica in cui verrà ospitato l'account DocumentDB.

4.	Dopo aver configurato le opzioni del nuovo account DocumentDB, fare clic su **Crea**. La creazione dell'account DocumentDB può richiedere alcuni minuti. Per verificare lo stato, è possibile monitorare l'avanzamento nella Schermata iniziale ![Schermata del riquadro Creazione sulla schermata iniziale][4]
  
	o dall'Hub di notifica.

	![Schermata dell'hub Notifica, che mostra l'account DocumentDB creato][5]

	![Schermata dell'hub Notifica, che mostra l'account DocumentDB creato correttamente e distribuito a un gruppo di risorse][6]

5.	Dopo la creazione, l'account DocumentDB è pronto all'uso con le impostazioni predefinite. Si noti che la coerenza predefinita dell'account DocumentDB è impostata su Sessione. È possibile modificare le impostazioni predefinite relative alla coerenza nel [portale di anteprima di Azure](https://portal.azure.com/#gallery/Microsoft.DocumentDB).

    ![Schermata del pannello Gruppo di risorse][7]

<!--Image references-->
[1]: media/documentdb-create-dbaccount/ca1.png
[2]: media/documentdb-create-dbaccount/ca2.png
[3]: media/documentdb-create-dbaccount/ca3.png
[4]: media/documentdb-create-dbaccount/ca4.png
[5]: media/documentdb-create-dbaccount/ca5.png
[6]: media/documentdb-create-dbaccount/ca6.png
[7]: media/documentdb-create-dbaccount/ca7.png

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps
[documentdb-manage]: ../articles/documentdb/documentdb-manage.md

<!---HONumber=62-->