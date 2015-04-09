Dopo aver propagato i record per il nome di dominio, è necessario associarli all'app Web. Attenersi alla procedura seguente per abilitare i nomi di dominio utilizzando il Web browser.

> [AZURE.NOTE] La propagazione dei record creati nei passaggi precedenti in tutto il sistema DNS può richiedere tempo. Non è possibile aggiungere il nome di dominio dell'app Web finché il record CNAME non è stato propagato. Se si utilizza un record A, non è possibile aggiungere il nome di dominio del record A all'app Web finché il record **awverify** CNAME record creato nel passaggio precedente non è stato propagato.
> 
> È possibile utilizzare un servizio come <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> per verificare che CNAME sia disponibile.

1. Nel browser, aprire il [Portale di Azure](https://portal.azure.com).

2. Nella scheda **App Web**, fare clic sul nome dell'app Web, selezionare **Impostazioni**, quindi selezionare **Domini personalizzati e SSL**.

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

6. Utilizzare le caselle di testo **NOMI DI DOMINIO** per immettere i nomi di dominio da associare a questo sito Web. 

	![](./media/custom-dns-web-site/dncmntask-cname-7.png)

6. Fare clic sul segno di spunta nell'angolo in basso a destra per salvare la configurazione del nome di dominio.

	Al termine della configurazione, il nome di dominio personalizzato sarà presente nella sezione dei **nomi di dominio** del pannello **Domini personalizzati e SSL** dell'app Web.

A questo punto sarà possibile immettere il nome di dominio personalizzato nel browser e verificare che il reindirizzamento all'app Web avvenga correttamente. 

<!--HONumber=49-->