

Per creare un nuovo servizio mobile, attenersi alla procedura seguente.

1.	Accedere al [portale di gestione]. Nella parte inferiore del riquadro di spostamento fare clic su **+NUOVO**. Espandere **Calcolo** e **Servizio mobile**, quindi fare clic su **Crea**.

	![](./media/mobile-services-create-new-service/mobile-create.png)

	Verrà visualizzata la finestra di dialogo **Crea servizio mobile**.

2.	Nella pagina **Crea servizio mobile** selezionare **Crea un database SQL 20 MB gratuito**, selezionare il runtime **JavaScript**, quindi immettere il nome di un sottodominio per il nuovo servizio mobile nella casella di testo **URL**. Fare clic sulla freccia destra per passare alla pagina successiva.

	![](./media/mobile-services-create-new-service/mobile-create-page1.png)

	Verrà visualizzata la pagina **Specifica impostazioni di database**.
	>[AZURE.NOTE]Nel corso di questa esercitazione verranno creati una nuova istanza e un nuovo server di database SQL. È possibile riusare questo nuovo database e amministrarlo allo stesso modo di qualsiasi altra istanza di database SQL. Se nella stessa area del nuovo servizio mobile si dispone già di un database, è possibile scegliere **Utilizza database esistente** e quindi selezionare questo database. Non è consigliabile usare un database in un'area diversa, a causa dei costi aggiuntivi di larghezza di banda e di latenze più elevate.

3.	In **Nome**, digitare il nome del nuovo database, quindi immettere il **Nome di accesso**, che corrisponde al nome di accesso dell'amministratore per il nuovo server di database SQL. Immettere e confermare la password, quindi fare clic sul segno di spunta per completare il processo.
	![](./media/mobile-services-create-new-service/mobile-create-page2.png)

A questo punto è stato creato un nuovo servizio mobile che può essere usato dalle app per dispositivi mobili.



<!-- URLs. -->
[Portale di gestione]: https://manage.windowsazure.com/

<!--HONumber=52-->
