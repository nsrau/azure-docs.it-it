Azure Marketplace rende disponibile un'ampia varietà di applicazioni Web ampiamente diffuse, sviluppate da Microsoft, da terze parti e tramite iniziative software open source. Le app Web create da Azure Marketplace non richiedono l'installazione di software, oltre al browser utilizzato per connettersi al [Portale di anteprima di Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

In questa esercitazione si apprenderà come:

- Come creare una nuova app Web tramite Azure Marketplace.

- Come distribuire l'app Web tramite il Portale di Azure.
 
Verrà creato un blog WordPress basato su un modello predefinito. Nella figura seguente è illustrata l'applicazione completata:


![Wordpress blog][13]

>[AZURE.NOTE]Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Creare un'app Web nel portale

1. Accedere al portale di anteprima di Azure.

2. Aprire Azure Marketplace facendo clic sull’icona **Marketplace**:

    ![Icona Marketplace][marketplace]

    Oppure facendo clic sull’icona **Nuovo** in alto a destra del dashboard e selezionando **Marketplace** in fondo all’elenco.
	
    ![Creazione di un nuovo sito][5]
	
3. Selezionare **Web + Mobile**. Cercare **WordPress** e scegliere il **WordPress** icona.

	![WordPress nell'elenco][7]
	
5. Dopo aver letto la descrizione dell'app WordPress, selezionare **Crea**.

6. Fare clic su **App Web** e fornire i valori richiesti per la configurazione dell'app web.
	
    ![configurazione dell'app][8]

7. Fare clic su **Database** e fornire i valori richiesti per la configurazione del database MySQL.

    ![configurare il database][database]

8. Fornire un nome per un nuovo gruppo di risorse.

    ![Impostazione di un gruppo di risorse][groupname]

8. Se necessario, fare clic su **SOTTOSCRIZIONE**, e specificare la sottoscrizione da utilizzare.

7. Al termine della definizione di applicazione web, fare clic su **Crea**, e attendere che la nuova applicazione web viene creato.

   Quando l'applicazione è stata creata, verrà visualizzato il gruppo di risorse contenente il database e applicazioni web.

   ![Mostra il gruppo][resourcegroup]

## Avviare e gestire l'app Web WordPress
	
1. Fare clic su della nuova applicazione web per visualizzare i dettagli sull'app.

    ![avvio del dashboard][10]

2. Nella pagina **Essentials**, fare clic su **Sfoglia** o sul collegamento sotto **Url** per aprire la pagina di benvenuto dell'app Web.

    ![URL sito][browse]

3. Se non è stato installato WordPress, immettere le informazioni di configurazione appropriate richieste da WordPress e fare clic su **Installa WordPress** per finalizzare la configurazione e aprire la pagina di accesso all'app Web.

4. Fare clic su **Accesso** e immettere le credenziali.

5. La nuova app Web WordPress avrà un aspetto simile al seguente.

	![sito WordPress][13]






[5]: ./media/website-from-gallery/start-marketplace.png
[6]: ./media/website-from-gallery/wordpressgallery-02.png
[7]: ./media/website-from-gallery/search-web-app.png
[8]: ./media/website-from-gallery/set-web-app.png
[9]: ./media/website-from-gallery/wordpressgallery-05.png
[10]: ./media/website-from-gallery/select-web.png
[13]: ./media/website-from-gallery/wordpressgallery-09.png
[webapps]: ./media/website-from-gallery/selectwebapps.png
[database]: ./media/website-from-gallery/set-db.png
[resourcegroup]: ./media/website-from-gallery/show-rg.png
[browse]: ./media/website-from-gallery/browse-web.png
[marketplace]: ./media/website-from-gallery/marketplace-icon.png
[groupname]: ./media/website-from-gallery/set-rg.png

<!---HONumber=August15_HO6-->