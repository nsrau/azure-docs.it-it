Questa esercitazione è basata sull'[app GetStartedWithMobileServices][], ovvero un progetto di app di Windows universale disponibile in Visual Studio 2013. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale.

1.  Scaricare la versione in C# dell'app di esempio GetStartedWithMobileServices da [Developer Code Samples site].

2.  In Visual Studio 2013 aprire il progetto scaricato ed esaminare il file MainPage.xaml.cs. rilevato nella cartella di progetto GetStartedWithData.Shared.

    Si noti che gli oggetti **TodoItem** aggiunti sono archiviati in un oggetto **ObservableCollection\<TodoItem\>** in memoria.

3.  Premere **F5** per ricompilare il progetto e avviare l'app.

4.  Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

    ![][]

    Si noti che viene mostrato il testo salvato.

5.  Right-click the Windows Phone 8.1 project, click **Set as Start-up Project**, then press **F5** to start the Windows Phone Store app.

    ![][1]

6.  Repeat steps 3 and 4 to verify that the sample behaves the same way.

  [app GetStartedWithMobileServices]: http://go.microsoft.com/fwlink/p/?LinkID=510826
  []: ./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup.png
  [1]: ./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup-wp8.png
