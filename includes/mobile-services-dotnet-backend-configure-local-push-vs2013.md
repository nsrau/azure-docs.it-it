
In alternativa, è possibile testare le notifiche push con il servizio mobile in esecuzione nel computer locale o nella macchina virtuale prima di pubblicarlo in Azure. Per eseguire questa operazione, è necessario impostare le informazioni relative all'hub di notifica usato dall'app nel file web.config. Queste informazioni vengono usate solo quando il servizio viene eseguito in locale per connettersi all'hub di notifica, ma vengono ignorate quando il servizio è pubblicato in Azure.

1. Aprire il file readme.html nella cartella di progetto dell'app Windows o WindowsPhone. 

	Se non è ancora aperta, viene visualizzata la pagina **Push setup is almost complete**. La sezione **Step 3: Modify Web Config** contiene le informazioni per la connessione all'hub di notifica.

2. Nel progetto di servizio mobile in Visual Studio aprire il file Web.config per il servizio e, in **connectionStrings**, aggiungere la stringa di connessione di **MS\_NotificationHubConnectionString** dalla pagina **Push setup is almost complete**.

3. In **appSettings** sostituire il valore dell'impostazione dell'app **MS\_NotificationHubName** con il nome dell'hub di notifica specificato nella pagina **Push setup is almost complete**.

4. Fare clic con il pulsante destro del mouse sul progetto relativo al servizio mobile, quindi selezionare **Debug** e **Avvia nuova istanza**. Prendere nota dell'URL radice del servizio nella pagina di avvio visualizzata nel browser.

	Si tratta dell'URL dell'host locale per il progetto di back-end .NET. Questo URL viene utilizzato per testare l'app con il proprio servizio mobile in esecuzione sul computer locale.

A questo punto, il progetto di servizio mobile è configurato per connettersi all'hub di notifica in Azure quando viene eseguito in locale. Si noti che è importante che il nome e la stringa di connessione dell'hub di notifica corrispondano a quelli del portale, perché le impostazioni di progetto nel file Web.config vengono sovrascritte dalle impostazioni del portale quando il servizio è eseguito in Azure.

<!---HONumber=August15_HO6-->