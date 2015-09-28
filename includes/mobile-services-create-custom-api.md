

1. Accedere al portale di gestione di Azure, fare clic su **Servizi mobili** e quindi selezionare il servizio mobile.

2. Fare clic sulla scheda **API** e quindi su **Crea**. Verrà visualizzata la finestra di dialogo **Crea nuova API personalizzata**.

3. Digitare _completeall_ in **Nome API** e quindi fare clic sul segno di spunta per creare la nuova API.

	> [AZURE.TIP]Con le autorizzazioni predefinite, chiunque disponga della chiave dell'app può chiamare l'API personalizzata. La chiave dell'applicazione non è tuttavia considerata una credenziale di sicurezza, in quanto non può essere distribuita o archiviata in modo sicuro. Per maggiore sicurezza, è consigliabile limitare l'accesso ai soli utenti autenticati.

4. Fare clic su **completeall** nella tabella dell'API.

5. Fare clic sulla scheda **Script**, sostituire il codice esistente con il seguente e quindi fare clic su **Salva**. Questo codice usa l'oggetto [mssql] per accedere direttamente alla tabella **todoitem** e impostare il contrassegno `complete` su tutti gli elementi. Poiché viene usata la funzione **exports.post**, i client inviano una richiesta POST per eseguire l'operazione. Al client viene restituito un valore intero corrispondente al numero di righe modificate.


		exports.post = function(request, response) {
			var mssql = request.service.mssql;
			var sql = "UPDATE todoitem SET complete = 1 " +
                "WHERE complete = 0; SELECT @@ROWCOUNT as count";
			mssql.query(sql, {
				success: function(results) {
					if(results.length == 1)
						response.send(200, results[0]);
				}
			})
		};


> [AZURE.NOTE]Gli oggetti [request](http://msdn.microsoft.com/library/windowsazure/jj554218.aspx) e [response](http://msdn.microsoft.com/library/windowsazure/dn303373.aspx) forniti a funzioni API personalizzate vengono implementati dalla [libreria Express.js](http://go.microsoft.com/fwlink/p/?LinkId=309046).

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[mssql]: http://msdn.microsoft.com/library/windowsazure/jj554212.aspx

<!---HONumber=Sept15_HO3-->