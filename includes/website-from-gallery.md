La raccolta rende disponibile un'ampia varietà di applicazioni Web ampiamente diffuse, sviluppate da Microsoft, da terze parti e tramite iniziative software open source. Le applicazioni Web create dalla raccolta non richiedono l'installazione di software, oltre al browser utilizzato per connettersi al portale di gestione di Azure.

In questa esercitazione si apprenderà come:

* Creare un nuovo sito tramite la raccolta.

* Distribuire il sito tramite il portale Azure.

Verrà creato un blog WordPress basato su un modello predefinito. Nella figura seguente è illustrata l'applicazione completata:

![Wordpress blog](./media/website-from-gallery/wordpressgallery-09.png)
<div class="dev-callout"><strong>Nota</strong>
<p>Per completare l'esercitazione, è necessario un account Azure. È possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni, vedere <a href="http://www.windowsazure.com/it-it/develop/php/tutorials/create-a-windows-azure-account/" target="_blank">Creazione di un account Azure</a>.</p>
</div>

## Creazione di un sito Web nel portale

1.  Eseguire l'accesso al [portale di gestione di Azure][1].

2.  Fare clic sull'icona **New** nella parte inferiore sinistra del dashboard.
    
    ![Creazione di un nuovo
    sito](./media/website-from-gallery/wordpressgallery-01.png)

3.  Fare clic sull'icona **Web Site** e quindi su **From Gallery**.
    
    ![Creazione da
    raccolta](./media/website-from-gallery/wordpressgallery-02.png)

4.  Individuare e selezionare l'icona WordPress nell'elenco, quindi fare clic su **Next**.
    
    ![WordPress
    nell'elenco](./media/website-from-gallery/wordpressgallery-03.png)

5.  Nella pagina **Configure Your App** immettere o selezionare i valori per tutti i campi:

* Immettere un nome di URL a scelta.
* Lasciare l'opzione **Create a new MySQL database** selezionata nel campo **Database**.
* Selezionare l'area più vicina.
  
  ![configurazione
  dell'app](./media/website-from-gallery/wordpressgallery-04.png)

1.  Quindi fare clic su **Next**.

2.  Nella pagina **Create New Database** è possibile specificare un nome per il nuovo database MySQL oppure utilizzare quello predefinito. Selezionare l'area più vicina come posizione di hosting. Selezionare la casella nella parte inferiore dello schermo per accettare le condizioni per l'utilizzo di ClearDB per il database MySQL ospitato. Quindi fare clic sul segno di spunta per completare la creazione del sito.
    
    ![creazione del
    database](./media/website-from-gallery/wordpressgallery-05.png)

Dopo aver fatto clic su **Complete** in Azure verranno avviate le operazioni di creazione e distribuzione. Durante la creazione e la distribuzione del sito Web, nella parte inferiore della pagina Web Sites verrà visualizzato lo stato di queste operazioni. Al termine di tutte le operazioni, quando il sito sarà stato correttamente distribuito, verrà visualizzato un messaggio di stato finale.

## Avvio e gestione del sito WordPress

1.  Fare clic sul nuovo sito nella pagina **Web Sites** per aprire il dashboard corrispondente.
    
    ![avvio del
    dashboard](./media/website-from-gallery/wordpressgallery-06.png)

2.  Nella pagina di gestione **Dashboard** scorrere verso il basso e fare clic sul collegamento a sinistra, in **Site Url**, per aprire la pagina iniziale del sito.
    
    ![URL sito](./media/website-from-gallery/wordpressgallery-07.png)

3.  Immettere le informazioni di configurazione appropriate richieste da WordPress e fare clic su **Install WordPress** per finalizzare la configurazione e aprire la pagina di accesso del sito Web.
    
    ![accesso a
    WordPress](./media/website-from-gallery/wordpressgallery-08.png)

4.  Accedere al nuovo sito Web WordPress immettendo il nome utente e la password specificati nella **pagina iniziale.**

5.  Il nuovo sito WordPress avrà un aspetto simile al seguente.
    
    ![sito
    WordPress](./media/website-from-gallery/wordpressgallery-09.png)



[1]: http://manage.windowsazure.com