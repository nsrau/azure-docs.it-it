1.  Accedere al [portale di gestione di Azure][1], fare clic su **Mobile
    Services** e quindi sull'app.
    
    ![](./media/mobile-services-create-custom-api/mobile-services-selection.png)

2.  Fare clic sulla scheda **API** e quindi su **Create a custom API**.
    
    ![](./media/mobile-services-create-custom-api/mobile-custom-api-create.png)
    
    Verrà visualizzata la finestra di dialogo **Create a new custom
    API**.

3.  Immettere *completeall* in **API name**, quindi fare clic sul segno
    di spunta.
    
    ![](./media/mobile-services-create-custom-api/mobile-custom-api-create-dialog2.png)
    
    Verrà creata la nuova API.
    
    > [WACOM.NOTE] Poiché vengono impostate autorizzazioni
    > predefinite, qualsiasi utente dell'app può chiamare l'API
    > personalizzata. Tuttavia, la chiave dell'applicazione non viene
    > distribuita né archiviata in modo sicuro e non può essere
    > considerata una credenziale di sicurezza. Per questo motivo, è
    > consigliabile limitare l'accesso solo agli utenti autenticati per
    > le operazioni che modificano dati o hanno effetto sul servizio
    > mobile.

4.  Fare clic sulla nuova voce **completeall** nella tabella dell'API.
    
    ![](./media/mobile-services-create-custom-api/mobile-custom-api-select2.png)

5.  Fare clic sulla scheda **Script**, sostituire il codice esistente con quello riportato di seguito, quindi fare clic su **Save**:
    
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
    
    In questo codice viene utilizzato l'[oggetto mssql][2] per accedere
    direttamente alla tabella **todoitem** e impostare il contrassegno
    di completamento su tutti gli elementi. Poiché viene utilizzata la
    funzione **exports.post**, i client inviano una richiesta POST per
    eseguire l'operazione. Al client viene restituito un valore intero
    corrispondente al numero di righe modificate.

> [WACOM.NOTE] Gli oggetti [request][3] e [response][4] forniti a funzioni API personalizzate vengono
> implementati dalla [libreria Express.js][5]. Per ulteriori informazioni, vedere [API
> personalizzata][6].

In seguito, l'app della Guida introduttiva verrà modificata per
aggiungere un pulsante New e il codice che chiama in modo asincrono la
nuova API personalizzata.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->



[1]: https://manage.windowsazure.com/
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554212.aspx
[3]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554218.aspx
[4]: http://msdn.microsoft.com/en-us/library/windowsazure/dn303373.aspx
[5]: http://go.microsoft.com/fwlink/p/?LinkId=309046
[6]: http://msdn.microsoft.com/en-us/library/windowsazure/dn280974.aspx
