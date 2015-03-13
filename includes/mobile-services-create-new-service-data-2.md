Prima di poter archiviare i dati dell'app nel nuovo servizio mobile, è necessario creare una nuova tabella nell'istanza di database SQL associata.

1. Nel portale di gestione fare clic su **Servizi mobili** e quindi sul servizio mobile appena creato.

2. Fare clic sulla scheda **Dati** e quindi su **+Crea**.
	
   	![mobile-data-tab-empty](./media/mobile-services-create-new-service-data-2/mobile-data-tab-empty.png)

   	Verrà visualizzata la finestra di dialogo **Crea nuova tabella**.

3. In **Nome tabella** digitare _TodoItem_ e quindi fare clic sul pulsante con il segno di spunta.

 	![mobile-create-todoitem-table](./media/mobile-services-create-new-service-data-2/mobile-create-todoitem-table.png)

  Verrà creata una nuova tabella di archiviazione, **TodoItem**, con le autorizzazioni predefinite impostate. Questo significa che chiunque disponga della chiave dell'applicazione, che viene distribuita con l'app, potrà accedere alla tabella e modificare i dati. 

> [AZURE.NOTE] 
> Nella Guida introduttiva di Servizi mobili viene usato lo stesso nome di tabella. Tuttavia, ogni tabella viene creata in uno schema specifico per un determinato servizio mobile. In questo modo è possibile evitare conflitti di dati quando più servizi mobili usano lo stesso database.

4. Fare clic sulla nuova tabella **TodoItem** e verificare che non siano presenti righe di dati.

5. Fare clic sulla scheda **Colonne**. Verificare che siano state create automaticamente le colonne predefinite seguenti: 
	
	<table border="1" cellpadding="10">
 	<tr>
 	<th>Nome colonna</th>
 	<th>Tipo</th>
 	<th>Indice</th>
 	</tr>
 	<tr>
 	<td>id</td>
 	<td>stringa</td>
 	<td>Indicizzata</td>
 	</tr>
 	<tr>
 	<td>__createdAt</td>
 	<td>data</td>
 	<td>Indicizzata</td>
 	</tr>
 	<tr>
 	<td>__updatedAt</td>
 	<td>data</td>
 	<td><font color="transparent">-</font></td>
 	</tr>
 	<tr>
 	<td>__version</td>
 	<td>timestamp (MSSQL)</td>
 	<td><font color="transparent">-</font></td>
 	</tr> 	
 	</table> 	
		

  	Questo è il requisito minimo per le tabelle in Servizi mobili. 

    > [AZURE.NOTE] Se nel servizio mobile è abilitato lo schema dinamico, vengono automaticamente create nuove colonne quando al servizio mobile vengono inviati oggetti JSON mediante un'operazione di inserimento o aggiornamento.

È ora possibile usare il nuovo servizio mobile come archivio dati per l'app.


<!--HONumber=45--> 
