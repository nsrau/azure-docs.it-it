La procedura seguente consente di registrare l'app con Windows Store, configurare il servizio mobile in modo da abilitare le notifiche push e aggiungere codice all'app per la registrazione di un canale di dispositivi con l'hub di notifica. Visual Studio 2013 si connette a Azure e a Windows Store usando le credenziali fornite dall'utente. 

1. In Visual Studio 2013 aprire Esplora soluzioni, fare clic con il pulsante destro del mouse sul progetto di app di Windows Store, scegliere **Aggiungi** quindi fare clic su **Notifica Push**. 

	![Add Push Notification wizard in Visual Studio 2013](./media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013.png)

	Verrà avviata la procedura guidata Aggiungi notifica Push.

2. Fare clic su **Avanti**, accedere al proprio account Windows Store, quindi specificare un nome in **Riserva un nuovo nome** e fare clic su **Riserva**.

	Viene creata una nuova registrazione per l'app.

3. Fare clic sulla nuova registrazione nell'elenco **Nome applicazione**, quindi fare clic su **Avanti**.

4. Nella pagina **Seleziona servizio** selezionare il nome del servizio mobile, quindi fare clic su **Avanti** e su **Fine**. 

	L'hub di notifica usato dal servizio mobile viene aggiornato con la registrazione di Servizi notifica Push Windows (WNS). È ora possibile usare Hub di notifica di Azure per inviare notifiche da Servizi mobili all'app mediante WNS. 

	>[AZURE.NOTE]Questa esercitazione illustra l'invio di notifiche da un back-end del servizio mobile. È possibile usare la stessa registrazione dell'hub di notifica per inviare notifiche da ogni servizio back-end. Per altre informazioni, vedere [Panoramica degli hub notifiche](http://msdn.microsoft.com/library/azure/jj927170.aspx).

5. Al completamento della procedura guidata, viene aperta una nuova pagina ****in Visual Studio con l'indicazione che la configurazione push è quasi completata. In questa pagina vengono fornite informazioni dettagliate su un metodo alternativo per configurare il progetto di servizio mobile per l'invio di notifiche, diverso da quello illustrato in questa esercitazione. 

	Il codice aggiunto alla soluzione di app di Windows universale mediante la procedura guidata Aggiungi notifica Push è specifico della piattaforma. Più avanti in questa sezione si condividerà il codice client di Servizi mobili in modo da eliminare questa ridondanza e rendere l'app universale più facilmente gestibile. 

<!-- URLs. -->
[Introduzione a Servizi mobili]: /develop/mobile/tutorials/get-started/
[Introduzione ai dati]: /develop/mobile/tutorials/get-started-with-data-dotnet/
[Importare il file di impostazioni di pubblicazione in Visual Studio 2013]: /documentation/articles/mobile-services-windows-how-to-import-publishsettings/

<!--HONumber=47-->
