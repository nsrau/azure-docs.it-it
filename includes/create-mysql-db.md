# Come creare un database MySQL in Azure

In questa guida viene illustrato come usare [ClearDB][ClearDB] per creare un database MySQL da [Azure Store][Azure Store] e come creare un database MySQL come risorsa collegata quando si crea un [sito Web di Azure][sito Web di Azure]. [ClearDB][ClearDB] è un fornitore a tolleranza di errore di database come servizio, che consente di eseguire e gestire database MySQL in data center di Azure e di connettersi a tali database da qualsiasi applicazione.

## Sommario

-   [Procedura: Creare un database MySQL da Azure Store][Procedura: Creare un database MySQL da Azure Store]
-   [Procedura: Creare un database MySQL come risorsa collegata per un sito Web di Azure][Procedura: Creare un database MySQL come risorsa collegata per un sito Web di Azure]

<div class="dev-callout"> 
<b>Nota</b> 
<p>Quando si crea un database MySQL come parte del processo di creazione del sito Web, &egrave; possibile creare solo un database gratuito. La creazione di un database MySQL da Azure Store consente di creare un database gratuito oppure di scegliere tra le opzioni a pagamento disponibili.</p> 
</div>

## <span id="CreateFromStore"></span></a>Procedura: Creare un database MySQL da Azure Store

Per creare un database MySQL da [Azure Store][Azure Store], eseguire le operazioni seguenti:

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic su **+NUOVO** nella parte inferiore della pagina, quindi selezionare **NEGOZIO**.

    ![Selezionare il componente aggiuntivo nel negozio][Selezionare il componente aggiuntivo nel negozio]

3.  Selezionare **ClearDB MySQL Database**, quindi fare clic sulla freccia nella parte inferiore della pagina.

    ![Selezionare Il database MySQL di ClearDB][Selezionare Il database MySQL di ClearDB]

4.  Selezionare un piano, immettere un nome di database, selezionare un'area geografica, quindi fare clic sulla freccia nella parte inferiore della pagina.

    ![Acquistare il database MySQL dal negozio][Acquistare il database MySQL dal negozio]

5.  Fare clic sul segno di spunta per completare l'acquisto.

    ![Verificare e completare l'acquisto][Verificare e completare l'acquisto]

6.  Dopo che è stato creato, il database può essere gestito nella scheda **COMPONENTI** del portale di gestione.

    ![Gestire il database MySQL nel portale di Azure][Gestire il database MySQL nel portale di Azure]

7.  Per ottenere le informazioni di connessione del database, fare clic su **INFORMAZIONI DI CONNESSIONE** nella parte inferiore della pagina, come illustrato sopra.

    ![Informazioni di connessione per MySQL][Informazioni di connessione per MySQL]

## <span id="CreateForWebSite"></span></a>Procedura: Creare un database MySQL come risorsa collegata per un sito Web di Azure

Per creare un database MySQL come risorsa collegata durante la creazione di un [sito Web di Azure][sito Web di Azure], eseguire le operazioni seguenti:

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic su **+NUOVO** nella parte inferiore della pagina, quindi selezionare **CALCOLO**, **SITO WEB** e **CREA CON DATABASE**.

    ![Creare un sito Web con database][Creare un sito Web con database]

3.  Specificare un **URL** per il sito Web, selezionare una **REGIONE** per il sito e scegliere **Crea un nuovo database MySQL** dall'elenco a discesa **DATABASE**. Facoltativamente, è possibile sostituire il nome predefinito per la stringa di connessione. Fare clic sulla freccia nella parte inferiore della pagina.

    ![Dettagli del sito Web][Dettagli del sito Web]

4.  Specificare un **NOME** per il database, selezionare la **REGIONE** per il database (deve corrispondere alla regione del sito Web), accettare le note legali di ClearDB e fare clic sul segno di spunta nella parte inferiore della pagina.

    ![Dettagli di MySQL][Dettagli di MySQL]

5.  Dopo la creazione del sito Web, fare clic sul nome del sito per passare al dashboard del sito.

    ![Visualizzazione del dashboard del sito Web][Visualizzazione del dashboard del sito Web]

6.  Fare clic su **CONFIGURA**.

    ![Visualizzare la scheda di configurazione][Visualizzare la scheda di configurazione]

7.  Scorrere verso il basso fino alla sezione **stringhe di connessione**, quindi fare clic su **Mostra stringhe di connessione** .

    ![Visualizzazione della stringa di connessione][Visualizzazione della stringa di connessione]

8.  Copiare la stringa di connessione per l'uso nell'applicazione.

    ![Stringa di connessione visualizzata][Stringa di connessione visualizzata]

> [WACOM.NOTE] Le stringhe di connessione sono accessibili all'applicazione del sito Web tramite il nome della stringa di connessione. Nelle applicazioni .NET le stringhe di connessione sono disponibili nell'oggetto **connectionStrings**. In altri linguaggi di programmazione le stringhe di connessione sono accessibili tramite le variabili di ambiente. Per altre informazioni, vedere [Come configurare i siti Web][Come configurare i siti Web].

  [ClearDB]: http://www.cleardb.com/
  [Azure Store]: /it-it/gallery/store/
  [sito Web di Azure]: /it-it/documentation/services/web-sites/
  [Procedura: Creare un database MySQL da Azure Store]: #CreateFromStore
  [Procedura: Creare un database MySQL come risorsa collegata per un sito Web di Azure]: #CreateForWebSite
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [Selezionare il componente aggiuntivo nel negozio]: ./media/create-mysql-db/select-store.png
  [Selezionare Il database MySQL di ClearDB]: ./media/create-mysql-db/select-cleardb-mysql.png
  [Acquistare il database MySQL dal negozio]: ./media/create-mysql-db/purchase-mysql.png
  [Verificare e completare l'acquisto]: ./media/create-mysql-db/complete-mysql-purchase.png
  [Gestire il database MySQL nel portale di Azure]: ./media/create-mysql-db/manage-mysql-add-on.png
  [Informazioni di connessione per MySQL]: ./media/create-mysql-db/mysql-conn-info.png
  [Creare un sito Web con database]: ./media/create-mysql-db/custom_create.png
  [Dettagli del sito Web]: ./media/create-mysql-db/provide-website-details.png
  [Dettagli di MySQL]: ./media/create-mysql-db/provide-mysql-details.png
  [Visualizzazione del dashboard del sito Web]: ./media/create-mysql-db/go-to-website-dashboard.png
  [Visualizzare la scheda di configurazione]: ./media/create-mysql-db/go-to-configure-tab.png
  [Visualizzazione della stringa di connessione]: ./media/create-mysql-db/show-conn-string.png
  [Stringa di connessione visualizzata]: ./media/create-mysql-db/shown-conn-string.png
  [Come configurare i siti Web]: ../web-sites-configure/
