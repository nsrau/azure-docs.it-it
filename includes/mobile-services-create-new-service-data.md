A questo punto verrà creato un nuovo servizio mobile per sostituire
l'elenco in memoria per l'archivio dati. Per creare un nuovo servizio
mobile, attenersi alla procedura seguente.

1.  Accedere al [portale di gestione di Azure][1].
2.  Nella parte inferiore del pannello di navigazione fare clic su
    **+NEW**.
    
    ![plus-new](./media/mobile-services-create-new-service-data/plus-new.png)

3.  Espandere **Compute** e **Mobile Service**, quindi fare clic su
    **Create**.
    
    ![mobile-create](./media/mobile-services-create-new-service-data/mobile-create.png)
    
    Verrà visualizzata la finestra di dialogo **New Mobile Service**.

4.  Nella pagina **Create mobile service** selezionare **Create a free
    20 MB SQL Database**, quindi immettere un nome di sottodominio per
    il nuovo servizio mobile nella casella di testo **URL** e attendere
    la verifica del nome. Quando la verifica del nome viene completata,
    fare clic sulla freccia destra per passare alla pagina successiva.
    
    ![mobile-create-page1](./media/mobile-services-create-new-service-data/mobile-create-page1.png)
    
    Verrà visualizzata la pagina **Specify database settings**.
	<div class="dev-callout"> 
	<b>Nota</b>
	<p>Nel corso di questa esercitazione verranno creati una nuova istanza e un nuovo server di database SQL. È possibile riutilizzare questo nuovo database e amministrarlo allo stesso modo di qualsiasi altra istanza di database SQL. Se nella stessa area del nuovo servizio mobile si dispone già di un database, è possibile scegliere <b>Use existing Database</b> e quindi selezionare questo database. Non è consigliabile utilizzare un database in un</p>

5.  In **Name** digitare il nome del nuovo database, quindi immettere il **Login name**, che corrisponde al nome di accesso dell'amministratore per il nuovo server di database SQL. Immettere e confermare la password, quindi fare clic sul segno di spunta per completare il processo. 

	![mobile-create-page2](./media/mobile-services-create-new-service-data/mobile-create-page2.png)

	<div class="dev-callout"> 
	<b>Note</b>
	<p>Se la password specificata non soddisfa i requisiti minimi oppure si verifica una mancata corrispondenza, verrà visualizzato un avviso.
	È consigliabile prendere nota del nome di
	accesso e della password dell area diversa, a causa dei costi aggiuntivi di larghezza di bandae di latenze più elevate.</p>
	</div>

A questo punto è stato creato un nuovo servizio mobile che può essere
utilizzato dalle app per dispositivi mobili. In seguito, verrà aggiunta
una nuova tabella in cui archiviare i dati dell'app. Questa tabella
verrà utilizzata dall'app al posto della raccolta in memoria.


[1]: https://manage.windowsazure.com/




