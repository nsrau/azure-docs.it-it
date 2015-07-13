

Prima di poter archiviare dati nel nuovo servizio mobile, è necessario creare una nuova tabella di archiviazione. Nella procedura seguente viene illustrato come utilizzare Visual Studio 2013 per creare una nuova tabella nel servizio mobile. Sarà quindi necessario aggiornare l'app in modo che gli elementi vengano archiviati in Servizi mobili di Azure anziché nella raccolta locale.


1. In Esplora server espandere **Servizi mobili di Azure**, fare clic con il pulsante destro del mouse sul servizio mobile, scegliere **Crea tabella** e quindi digitare `TodoItem` in **Nome tabella**.

	![creazione di una tabella in VS 2013](./media/mobile-services-create-new-table-vs2013/mobile-create-table-vs2013.png)

2. Espandere **Avanzate**, verificare che per impostazione predefinita venga usata l'autorizzazione **Chiunque con la chiave applicazione** per le operazioni sulle tabelle e quindi fare clic su **Crea**.

	Nel server verrà creata la tabella TodoItem, a cui può accedere per modificare i dati chiunque disponga della chiave dell'applicazione, senza prima eseguire l'autenticazione.

	>[AZURE.NOTE]La chiave dell'applicazione viene distribuita con l'applicazione. Poiché la distribuzione non è sicura, la chiave non può essere considerata un token di sicurezza. Per proteggere l'accesso ai dati del servizio mobile, è necessario autenticare gli utenti prima dell'accesso. Per altre informazioni, vedere [Autorizzazioni](http://msdn.microsoft.com/library/windowsazure/jj193161.aspx).
	>
	>Le nuove tabelle create includono le colonne Id, __createdAt, __updatedAt e __version. Quando è abilitato lo schema dinamico, in Servizi mobili vengono generate automaticamente nuove colonne basate sull'oggetto JSON nella richiesta di inserimento o di aggiornamento. Per altre informazioni, vedere [Schema dinamico](http://msdn.microsoft.com/library/windowsazure/jj193175.aspx).

<!---HONumber=July15_HO1-->