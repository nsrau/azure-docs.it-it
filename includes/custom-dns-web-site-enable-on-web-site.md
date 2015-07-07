Dopo aver propagato i record per il nome di dominio, è necessario associarli all'app Web. Attenersi alla procedura seguente per abilitare i nomi di dominio usando il Web browser.

> [AZURE.NOTE]La propagazione dei record creati nei passaggi precedenti in tutto il sistema DNS può richiedere tempo. Non è possibile aggiungere il nome di dominio dell'app Web finché il record CNAME non è stato propagato. Se si usa un record A, non è possibile aggiungere il nome di dominio del record A all'app Web finché il record CNAME **awverify** creato nel passaggio precedente non sarà stato propagato.
>
> È possibile usare un servizio come <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> per verificare se il record CNAME è disponibile.

1. Accedere al [portale di gestione di Azure](https://portal.azure.com) dal browser.

2. Nella scheda **App Web** fare clic sul nome dell'app Web, selezionare **Impostazioni** e quindi **Domini personalizzati e SSL**.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. Nel pannello **Domini personalizzati e SSL** fare clic su **Porta domini esterni**.

	![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4. Usare le caselle di testo **NOMI DI DOMINIO** per immettere i nomi di dominio da associare a questo sita app Web.

	![](./media/custom-dns-web-site/dncmntask-cname-8.png)

5. Fare clic su **Salva** per salvare la configurazione del nome di dominio.

	Al termine della configurazione, il nome di dominio personalizzato sarà elencato nella sezione **nomi di dominio** dell'app Web.

A questo punto sarà possibile immettere il nome di dominio personalizzato nel browser e verificare che il reindirizzamento all'app Web avvenga correttamente.

<!---HONumber=62-->