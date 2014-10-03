Al termine della propagazione dei record per il nome di dominio, è necessario associarli al proprio sito Web. Attenersi alla seguente procedura per abilitare i nomi di dominio utilizzando il Web browser.

> [WACOM.NOTE] La propagazione dei record creati nei passaggi precedenti in tutto il sistema DNS può richiedere tempo. Non è possibile aggiungere il nome di dominio nel sito Web di Azure finché il record CNAME non è stato propagato. Se si usa un record A, non è possibile aggiungere il nome di dominio del record A al sito Web di Azure finché il record CNAME **awverify** creato nel passaggio precedente non è stato propagato.
>
> È possibile utilizzare un servizio come <http://www.digwebinterface.com/> per verificare se il record CNAME è disponibile.

1.  Accedere al [portale di gestione di Azure][] dal browser.

2.  Nella scheda **Siti Web** fare clic sul nome del sito, selezionare **Dashboard** e quindi scegliere **Gestisci domini** nella parte inferiore della pagina.

    ![][]

3.  Usare le caselle di testo **NOMI DI DOMINIO** per immettere i nomi di dominio da associare a questo sito Web.

    ![][1]

4.  Fare clic sul segno di spunta nell'angolo in basso a destra per salvare la configurazione del nome di dominio.

    Al termine della configurazione, il nome di dominio personalizzato sarà elencato nella sezione **nomi di dominio** della pagina **Configura** del sito Web.

A questo punto sarà possibile immettere il nome di dominio personalizzato nel browser e verificare che il reindirizzamento al Web di Azure avvenga correttamente.

  [portale di gestione di Azure]: https://manage.windowsazure.com
  []: ./media/custom-dns-web-site/dncmntask-cname-6.png
  [1]: ./media/custom-dns-web-site/dncmntask-cname-7.png
