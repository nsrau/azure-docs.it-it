Per creare un nuovo servizio mobile, attenersi alla procedura seguente.

1.  Accedere al [portale di gestione][portale di gestione].

2.  Nella parte inferiore del pannello di navigazione fare clic su **+NEW**.

    ![](./media/mobile-services-dotnet-backend-create-new-service/plus-new.png)

3.  Espandere **Compute** e **Mobile Service**, quindi fare clic su **Create**.

    ![][1]

    Verrà visualizzata la finestra di dialogo **New Mobile Service**.

4.  Nella pagina **Create a mobile service** selezionare **Create a free 20 MB SQL Database**, selezionare il runtime **.NET**, quindi immettere il nome di un sottodominio per il nuovo servizio mobile nella casella di testo **URL** e attendere la verifica del nome. Quando la verifica del nome viene completata, fare clic sulla freccia destra per passare alla pagina successiva.

    ![][2]

    Verrà visualizzata la pagina **Specify database settings**.

    <div class="dev-callout"> 
<b>Nota</b> 
	<p>Nel corso di questa esercitazione verranno creati una nuova istanza e un nuovo server di database SQL. &Egrave; possibile riutilizzare questo nuovo database e amministrarlo allo stesso modo di qualsiasi altra istanza di database SQL. Se nella stessa area del nuovo servizio mobile si dispone gi&agrave; di un database, &egrave; possibile scegliere <strong>Use existing Database</strong> e quindi selezionare questo database. Non &egrave; consigliabile utilizzare un database in un'area diversa, a causa dei costi aggiuntivi di larghezza di banda e di latenze pi&ugrave; elevate.</p></div>

5.  In **Name** digitare il nome del nuovo database, quindi immettere il **Login name**, che corrisponde al nome di accesso dell'amministratore per il nuovo server di database SQL. Immettere e confermare la password, quindi fare clic sul segno di spunta per completare il processo.

    ![][3]

    <div class="dev-callout"> 
<b>Nota</b> 
	<p>Se la password specificata non soddisfa i requisiti minimi oppure si verifica una mancata corrispondenza, verr&agrave; visualizzato un avviso. <br/>&Egrave; consigliabile prendere nota del nome di accesso e della password dell'amministratore specificati. Queste informazioni saranno infatti necessarie per riutilizzare l'istanza o il server di database SQL in futuro.</p> 
</div>

A questo punto è stato creato un nuovo servizio mobile che può essere utilizzato dalle app per dispositivi mobili.



  [portale di gestione]: https://manage.windowsazure.com/
   
  [1]: ./media/mobile-services-dotnet-backend-create-new-service/mobile-create.png
  [2]: ./media/mobile-services-dotnet-backend-create-new-service/mobile-create-page1.png
  [3]: ./media/mobile-services-dotnet-backend-create-new-service/mobile-create-page2.png
