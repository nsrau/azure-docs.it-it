Questa esercitazione è basata sull'[app GetStartedWithMobileServices][app GetStartedWithMobileServices], ovvero un progetto di app di Windows universale disponibile in Visual Studio 2013. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale.

1.  Scaricare la versione JavaScript dell'app di esempio GetStartedWithMobileServices da [Developer Code Samples site].

2.  In Visual Studio 2013, aprire il progetto scaricato, espandere la cartella js in Esplora soluzioni ed esaminare il file default.js.

    Si noti che gli oggetti **TodoItem** aggiunti sono archiviati in un oggetto `WinJS.Binding.List` in memoria.

3.  Premere **F5** per ricompilare il progetto e avviare l'app.

4.  Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

    ![][]

    Si noti che viene mostrato il testo salvato.

5.  Fare clic con il pulsante destro del mouse sul progetto di Windows Phone 8.1, selezionare **Imposta come progetto di avvio** e premere **F5** per eseguire l'app di Windows Phone Store.

    ![][1]

6.  Ripetere i passaggi 3 e 4 per verificare se l'esempio si comport anello stesso modo.

  [app GetStartedWithMobileServices]: http://go.microsoft.com/fwlink/p/?LinkID=510826
  []: ./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup.png
  [1]: ./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup-wp8.png
