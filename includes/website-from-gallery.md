La raccolta rende disponibile un'ampia varietà di applicazioni Web ampiamente diffuse, sviluppate da Microsoft, da terze parti e tramite iniziative software open source. Le applicazioni Web create dalla raccolta non richiedono l'installazione di software, oltre al browser utilizzato per connettersi al portale di gestione di Azure. 

In questa esercitazione si apprenderà come:

- Creare un nuovo sito tramite la raccolta.

- Distribuire il sito tramite il portale Azure.
 
Verrà creato un blog WordPress basato su un modello predefinito. Nella figura seguente è illustrata l'applicazione completata:


![Wordpress blog][13]

> [AZURE.IMPORTANT] Per completare l'esercitazione è necessario un account Azure. È possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni, vedere [Creare un account Azure](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/"%20target="_blank").

## Creazione di un sito Web nel portale

1. Accedere al [portale di gestione di Azure](http://manage.windowsazure.com).

2. Fare clic sull'icona **Nuovo** nella parte inferiore sinistra del dashboard.
	
	![Create New][5]

3. Fare clic sull'icona **Sito Web** e quindi su **Da raccolta**.
	
	![Create From Gallery][6]

4. Individuare e selezionare l'icona WordPress nell'elenco, quindi fare clic su **Avanti**.
	
	![WordPress from list][7]

5. Nella pagina **Configura applicazione** immettere o selezionare i valori per tutti i campi:
	
- Immettere un nome di URL a scelta.	
- Lasciare l'opzione **Crea un nuovo database MySQL** selezionata nel campo **Database**.
- Selezionare l'area più vicina.

	![configure your app][8]

6. Quindi fare clic su **Avanti**.

7. Nella pagina **Crea nuovo database** è possibile specificare un nome per il nuovo database MySQL oppure usare quello predefinito. Selezionare l'area più vicina come posizione di hosting. Selezionare la casella nella parte inferiore dello schermo per accettare le condizioni per l'utilizzo di ClearDB per il database MySQL ospitato. Quindi fare clic sul segno di spunta per completare la creazione del sito. 
	
	![create database][9]

Dopo aver fatto clic su **Completa**, in Azure verranno avviate le operazioni di creazione e distribuzione. Durante la creazione e la distribuzione del sito Web, nella parte inferiore della pagina Siti Web verrà visualizzato lo stato di queste operazioni. Al termine di tutte le operazioni, quando il sito sarà stato correttamente distribuito, verrà visualizzato un messaggio di stato finale.

## Avvio e gestione del sito WordPress

1. Fare clic sul nuovo sito nella pagina **Siti Web** per aprire il dashboard corrispondente.

	![launch dashboard][10]

2. Nella pagina di gestione **Dashboard** scorrere verso il basso e fare clic sul collegamento a sinistra, in **URL sito**, per aprire la pagina iniziale del sito.

	![site URL][11] 

3. Immettere le informazioni di configurazione appropriate richieste da WordPress e fare clic su **Installa WordPress** per finalizzare la configurazione e aprire la pagina di accesso del sito Web.

	![login to WordPress][12]

4. Accedere al nuovo sito Web WordPress immettendo il nome utente e la password specificati nella **pagina iniziale**.

5. Il nuovo sito WordPress avrà un aspetto simile al seguente.  

	![your WordPress site][13]






[5]: ./media/website-from-gallery/wordpressgallery-01.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/wordpressgallery-03.png
[8]: ./media/website-from-gallery/wordpressgallery-04.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/wordpressgallery-06.png
[11]: ./media/website-from-gallery/wordpressgallery-07.png
[12]: ./media/website-from-gallery/wordpressgallery-08.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png





<!--HONumber=42-->
