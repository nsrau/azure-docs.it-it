Dopo il completamento della propagazione dei record per il nome di dominio, è necessario associarli al sito Web desiderato. Attenersi alla seguente procedura per abilitare i nomi di dominio utilizzando il Web browser.

> [WACOM.NOTE] La propagazione dei record creati nei passaggi precedenti in tutto il sistema DNS può richiedere tempo. Non è possibile aggiungere il nome di dominio nel sito Web di Azure finché CNAME non è stato propagato. Se si sta utilizzando un record A, non è possibile aggiungere il nome di dominio del record A al sito Web di Azure finché i record CNAME **awverify.www** o **www** creati nel passaggio precedente non sono stati propagati.
>
> È possibile utilizzare un servizio come <http://www.digwebinterface.com/> per verificare se il record CNAME è disponibile.

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com) dal browser.

2.  Nella scheda **Web Sites** fare clic sul nome del sito, selezionare **Dashboard** e quindi scegliere **Manage Domains** nella parte inferiore della pagina.

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3.  Utilizzare le caselle di testo **DOMAIN NAMES** per immettere i nomi di dominio da associare a questo sito Web.

    ![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4.  Fare clic sul segno di spunta nell'angolo in basso a destra per salvare la configurazione del nome di dominio.

    Al termine della configurazione, il nome di dominio personalizzato sarà elencato nella sezione **domain names** della pagina **Configure** del sito Web.

A questo punto si dovrebbe essere in grado di immettere il nome di dominio personalizzato nel browser per verificare l'accesso al sito Web di Azure.

