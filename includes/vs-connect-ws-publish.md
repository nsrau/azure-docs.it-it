-   Fare clic su **Sign In**, quindi immettere le credenziali dell'account Azure.

    Questo metodo è più semplice e rapido, ma non consente di visualizzare il database SQL o Servizi mobili di Azure nella finestra **Esplora server**.

-   Fare clic su **Manage subscriptions** per installare un certificato di gestione che consenta di accedere all'account.

    Nella finestra di dialogo **Manage Azure Subscriptions** fare clic sulla scheda **Certificates**, quindi fare clic su **Import**. Attenersi alle istruzioni per scaricare e importare un file di sottoscrizione (denominato anche file *.publishsettings*) per l'account Azure.

    <div class="dev-callout"><strong>Nota di sicurezza:</strong><br /> <p>scaricare il file di sottoscrizione in una cartella all'esterno delle directory del codice sorgente, ad esempio nella cartella Download, quindi eliminarlo al termine dell'importazione. Un utente malintenzionato che riesce ad accedere al file di sottoscrizione pu&ograve; modificare, creare ed eliminare i servizi di Azure.</p></div>



Per altre informazioni, vedere [Come connettersi ad Azure da Visual Studio][Come connettersi ad Azure da Visual Studio].

  [Come connettersi ad Azure da Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=324796
