Se si desidera un dominio, è possibile acquistarlo direttamente nel [portale di gestione di Azure](https://portal.azure.com). Seguire questa procedura per acquistare i nomi di dominio e assegnarli all'app Web.

1. Accedere al [portale di gestione di Azure](https://portal.azure.com) dal browser.

2. Nella scheda **App Web** fare clic sul nome dell'app Web, selezionare **Impostazioni** e quindi **Domini personalizzati e SSL**.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. Nel pannello **Domini personalizzati e SSL** fare clic su **Acquista domini**.

	![](./media/custom-dns-web-site/dncmntask-cname-buydomains-1.png)

4. Nella casella di testo del pannello **Acquista domini** immettere il nome del dominio che si vuole acquistare. I domini disponibili suggeriti verranno visualizzati sotto la casella di testo. Selezionare il dominio che si vuole acquistare.

  ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-2.png)

5. Scegliere **Informazioni di contatto** e compilare il modulo di informazioni di contatto del dominio.

  ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-3.png)

6. Fare clic su **Seleziona** nel pannello **Acquista domini**, a questo punto verranno visualizzate le informazioni di acquisto nel pannello **Conferma acquisto**. Se si accettano le condizioni legali e si fa clic su **Acquista**, l'ordine viene inviato ed è possibile monitorare il processo di acquisto in **Notifica**.

  ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-4.png)

  ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-5.png)

7. Se un dominio è stato ordinato correttamente, è possibile gestirlo e assegnarlo all'app Web. Fare clic sui tre puntini (**...**) a destra del dominio. Questa operazione consente di **annullare l'acquisto** o **gestire il dominio**. Fare clic su **Gestisci dominio** in modo da associare il **sottodominio** all'app Web nel pannello **Gestisci dominio**.

	![](./media/custom-dns-web-site/dncmntask-cname-buydomains-6.png)

	Al termine della configurazione, il nome di dominio personalizzato sarà elencato nella sezione **Associazioni nome host** dell'app Web.

A questo punto sarà possibile immettere il nome di dominio personalizzato nel browser e verificare che il reindirizzamento all'app Web avvenga correttamente.

<!---HONumber=July15_HO4-->