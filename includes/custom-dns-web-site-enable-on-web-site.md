Dopo aver propagato i record per il nome di dominio, è necessario associarli all'app Web. Attenersi alla procedura seguente per abilitare i nomi di dominio usando il Web browser.

> [AZURE.NOTE] La propagazione dei record TXT creati nei passaggi precedenti in tutto il sistema DNS può richiedere tempo. Non è possibile aggiungere il nome di dominio dell'app Web finché il record TXT non è stato propagato. Se si usa un record A, non è possibile aggiungere il nome di dominio del record A all'app Web finché il record TXT creato nel passaggio precedente non sarà stato propagato.
>
> È possibile usare un servizio come <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> per verificare se il record TXT è disponibile.

1. Accedere al [portale di Azure](https://portal.azure.com) dal browser.

2. Nella scheda **App Web** fare clic sul nome dell'app Web e selezionare **Domini personalizzati**

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)  

3. Nel pannello **Domini personalizzati** fare clic su **Aggiungi il nome host**.
	
4. Usare le caselle di testo **Nome host** per immettere i nomi di dominio da associare a questa app Web.

	![](./media/custom-dns-web-site/add-custom-domain.png)

6.  Fare clic su **Convalida**.

7.  Dopo aver fatto clic su **Convalida**, Azure avvierà il flusso di lavoro Verifica del dominio, che controllerà la proprietà del dominio oltre alla disponibilità del nome host e segnalerà la riuscita o un errore dettagliato con le linee guida consigliate per correggere l'errore.

A questo punto sarà possibile immettere il nome di dominio personalizzato nel browser e verificare che il reindirizzamento all'app Web avvenga correttamente.

<!---HONumber=AcomDC_0824_2016-->