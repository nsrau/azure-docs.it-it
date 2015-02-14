

1. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sull'app.

	![](./media/mobile-services-create-custom-api/mobile-services-selection.png)

2. Fare clic sulla scheda **API** e quindi su **Crea API personalizzata**.

	![](./media/mobile-services-create-custom-api/mobile-custom-api-create.png)

	Verrà visualizzata la finestra di dialogo **Crea nuova API personalizzata**.

3. Immettere _completeall_ in **Nome API**, quindi fare clic sul segno di spunta.

	![](./media/mobile-services-create-custom-api/mobile-custom-api-create-dialog2.png)

	Verrà creata la nuova API.

	> [AZURE.NOTE] Poiché vengono impostate autorizzazioni predefinite, qualsiasi utente dell'app può chiamare l'API personalizzata. Tuttavia, la chiave dell'applicazione non viene distribuita né archiviata in modo sicuro e non può essere considerata una credenziale di sicurezza. Per questo motivo, è consigliabile limitare l'accesso solo agli utenti autenticati per le operazioni che modificano dati o hanno effetto sul servizio mobile.

4. Fare clic sulla nuova voce**completeall** nella tabella dell'API.

	![](./media/mobile-services-create-custom-api/mobile-custom-api-select2.png)

5. Fare clic sulla scheda **Script**, sostituire il codice esistente con quello riportato di seguito, quindi fare clic su **Salva**:

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

   In questo codice viene usato l'[oggetto mssql] per accedere direttamente alla tabella **todoitem** e impostare il contrassegno di completamento su tutti gli elementi. Poiché viene usata la funzione **exports.post**, i client inviano una richiesta POST per eseguire l'operazione. Al client viene restituito un valore intero corrispondente al numero di righe modificate.

> [AZURE.NOTE]
> Gli oggetti <a href="http://msdn.microsoft.com/it-it/library/windowsazure/jj554218.aspx" target="_blank">request</a> e <a href="http://msdn.microsoft.com/it-it/library/windowsazure/dn303373.aspx" target="_blank">response</a> forniti a funzioni API personalizzate vengono implementati dalla <a href="http://go.microsoft.com/fwlink/p/?LinkId=309046" target="_blank">libreria Express.js</a>. Per altre informazioni, vedere <a href="http://msdn.microsoft.com/it-it/library/windowsazure/dn280974.aspx" target="_blank">API personalizzata</a>. 

In seguito, l'app della Guida introduttiva verrà modificata per aggiungere un pulsante New e il codice che chiama in modo asincrono la nuova API personalizzata.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[oggetto mssql]: http://msdn.microsoft.com/it-it/library/windowsazure/jj554212.aspx
<!--HONumber=42-->
