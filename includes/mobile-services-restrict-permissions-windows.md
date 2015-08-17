
1. In Esplora Server in Visual Studio espandere il nodo **Azure**, **Servizi mobili** e il proprio servizio mobile.

2. Fare clic con il pulsante destro del mouse sulla tabella **TodoItem**, fare clic su **Modifica autorizzazioni**, impostare tutte le autorizzazioni su **Solo gli utenti autenticati** e quindi fare clic su **Applica**. In questo modo, tutte le operazioni eseguite sulla tabella _TodoItem_ richiedono un utente autenticato.

3. Fare clic con il pulsante destro del mouse sul progetto dell'app client, fare clic su **Debug** e quindi su **Avvia nuova istanza**. Verificare che dopo l'avvio dell'app venga generata un'eccezione non gestita con codice di stato 401 (Non autorizzato).

	L'eccezione non gestita viene generata perché l'app tenta di accedere a Servizi mobili come utente non autenticato, mentre la tabella *TodoItem* richiede ora l'autenticazione.

A questo punto, si aggiornerà l'app in modo che autentichi gli utenti prima di richiedere risorse al servizio mobile.

<!---HONumber=August15_HO6-->