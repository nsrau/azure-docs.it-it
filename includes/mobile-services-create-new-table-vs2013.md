

Prima di poter archiviare dati nel nuovo servizio mobile, è necessario creare una nuova tabella di archiviazione. Nella procedura seguente viene illustrato come usare Visual Studio 2013 per creare una nuova tabella nel servizio mobile. Sarà quindi necessario aggiornare l'app in modo che gli elementi vengano archiviati in Servizi mobili di Azure anziché nella raccolta locale.


1. In Esplora server espandere **Servizi mobili di Azure**, fare clic con il pulsante destro del mouse sul servizio mobile, scegliere **Crea tabella**, quindi digitare "TodoItem" in **Nome tabella**.

	![create table in VS 2013](./media/mobile-services-create-new-table-vs2013/mobile-create-table-vs2013.png)

2. Espandere**Avanzate**, verificare che per impostazione predefinita venga usata l'autorizzazione **Chiunque con la chiave applicazione** per le operazioni sulle tabelle, quindi fare clic su **Crea**. 

	![create table in VS 2013 part 2](./media/mobile-services-create-new-table-vs2013/mobile-create-table-vs2013-2.png)

	Nel server verrà creata la tabella TodoItem, a cui potrà accedere e di cui potrà modificare i dati chiunque disponga della chiave dell'applicazione, senza prima eseguire l'autenticazione. 

	>[WACOM.NOTE]La chiave dell'applicazione viene distribuita con l'applicazione. Poiché la distribuzione non è sicura, la chiave non può essere considerata un token di sicurezza. Per proteggere l'accesso ai dati del servizio mobile, è necessario autenticare gli utenti prima dell'accesso. Per altre informazioni, vedere [Autorizzazioni](http://msdn.microsoft.com/it-it/library/windowsazure/jj193161.aspx).



