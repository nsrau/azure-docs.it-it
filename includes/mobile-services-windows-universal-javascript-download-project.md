
Questa esercitazione è basata sull'[app GetStartedWithMobileServices](http://go.microsoft.com/fwlink/p/?LinkID=510826), ovvero un progetto di app Windows universale in Visual Studio 2013. L'interfaccia utente di questa app è identica a quella dell'app generata dalla guida introduttiva di Servizi mobili, ma in questo caso gli elementi aggiunti vengono archiviati nella memoria locale. 

1. Scaricare la versione JavaScript dell'app di esempio GetStartedWithMobileServices dal [sito degli esempi di codice di Developer Network]. 

3. In Visual Studio 2013 aprire il progetto scaricato, espandere la cartella js in Esplora soluzioni ed esaminare il file default.js.

   Si noti che gli oggetti **TodoItem** aggiunti sono archiviati in un oggetto  `WinJS.Binding.List` in memoria.

4. Premere **F5** per ricompilare il progetto e avviare l'app.

5. Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

   	![](./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup.png) 

   	Notice that the saved text is displayed.

6. Fare clic con il pulsante destro del mouse sul progetto di Windows Phone 8.1, selezionare **Imposta come progetto di avvio** e premere **F5** per eseguire l'app di Windows Phone Store.  

	![](./media/mobile-services-windows-universal-dotnet-download-project/mobile-quickstart-startup-wp8.png)

7. Ripetere i passaggi 3 e 4 per verificare se l'esempio si comporta nello stesso modo.
<!--HONumber=42-->
