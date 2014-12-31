
1. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili**, sul servizio mobile desiderato e quindi sulla scheda **Dashboard**.

	![Select your mobile service](./media/mobile-services-enable-source-control/mobile-services-selection.png)

2. (Facoltativo) Se le credenziali del controllo codice sorgente di Servizi mobili o di Siti Web per la sottoscrizione di Azure sono state già impostate, è possibile passare direttamente al passaggio 4. In alternativa, fare clic su **Impostare il controllo codice sorgente** in **Riepilogo rapido** e fare clic su **Sì** per confermare.

	![Set up source control](./media/mobile-services-enable-source-control/mobile-setup-source-control.png)


3. Specificare **Nome utente**, **Nuova password**, confermare la password e quindi fare clic sul segno di spunta. 

	![Set source control credentials](./media/mobile-services-enable-source-control/mobile-source-control-credentials.png)

	Nel servizio mobile verrà creato il repository Git. Prendere nota delle credenziali fornite, in quanto saranno necessarie per accedere a questo e ad altri repository di Servizi mobili nella sottoscrizione.

4. Fare clic sulla scheda **Configura** e osservare i nuovi campi **Controllo codice sorgente**.

	![Configure source control](./media/mobile-services-enable-source-control/mobile-source-control-configure.png)

	Viene visualizzato l'URL del repository Git. Questo URL sarà necessario per clonare il repository nel computer locale.

Con il controllo del codice sorgente abilitato nel servizio mobile, è possibile usare Git per clonare il repository nel computer locale.
 

<!--HONumber=35_1-->
