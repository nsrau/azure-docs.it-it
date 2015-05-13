#Come creare un database MySQL in Azure

In questa guida viene illustrato come usare [ClearDB] per creare un database MySQL da [Azure Store] e come creare un database MySQL come risorsa collegata quando si crea un [sito Web di Azure][waws]. [ClearDB] è un fornitore a tolleranza di errore di database come servizio, che consente di eseguire e gestire database MySQL in data center di Azure e di connettersi a tali database da qualsiasi applicazione.

##Sommario
* [Procedura: Creare un database MySQL da Azure Store](#CreateFromStore)
* [Procedura: Creare un database MySQL come risorsa collegata per un sito Web di Azure](#CreateForWebSite)

> [AZURE.NOTE]Quando si crea un database MySQL come parte del processo di creazione del sito Web, è possibile creare solo un database gratuito. La creazione di un database MySQL da Azure Store consente di creare un database gratuito oppure di scegliere tra le opzioni a pagamento disponibili.

<h2><a id="CreateFromStore"></a>Procedura: Creare un database MySQL da Azure Store</h2>

Per creare un database MySQL da [Azure Store], eseguire la procedura seguente:

1. Accedere al [portale di gestione di Azure][portal].
2. Fare clic su **+NUOVO** nella parte inferiore della pagina, quindi selezionare **MARKETPLACE**.

	![Selezione del componente aggiuntivo nel negozio](./media/create-mysql-db/select-store.png)

3. Selezionare **ClearDB MySQL Database**, quindi fare clic sulla freccia nella parte inferiore della pagina.

	![Selezione del database MySQL di ClearDB](./media/create-mysql-db/select-cleardb-mysql.png)

4. Selezionare un piano, immettere un nome di database, selezionare un'area geografica, quindi fare clic sulla freccia nella parte inferiore della pagina.

	![Acquisto del database MySQL dal negozio](./media/create-mysql-db/purchase-mysql.png)

5. Fare clic sul segno di spunta per completare l'acquisto.

	![Verifica e completamento dell'acquisto](./media/create-mysql-db/complete-mysql-purchase.png)

6. Dopo che è stato creato, il database può essere gestito nella scheda **ADD-ONS** del portale di gestione.

	![Gestione del database MySQL nel portale di Azure](./media/create-mysql-db/manage-mysql-add-on.png)

7. Per ottenere le informazioni di connessione del database, fare clic su **CONNECTION INFO** nella parte inferiore della pagina, come illustrato sopra.

	![Informazioni di connessione per MySQL](./media/create-mysql-db/mysql-conn-info.png)


<h2><a id="CreateForWebSite"></a>Procedura: Creare un database MySQL come risorsa collegata per un sito Web di Azure</h2>

Per creare un database MySQL come risorsa collegata durante la creazione di un [sito Web di Azure][waws], eseguire la procedura seguente:

1. Accedere al [portale di gestione di Azure][portal].
2. Fare clic su **+NUOVO** nella parte inferiore della pagina, quindi selezionare **CALCOLO**, **SITO WEB** e **CREA CON DATABASE**.

	![Creazione di un sito Web con database](./media/create-mysql-db/custom_create.png)

3. Specificare un **URL** per il sito Web, selezionare una **REGIONE** per il sito e scegliere **Crea un nuovo database MySQL** dall'elenco a discesa **DATABASE**. Facoltativamente, è possibile sostituire il nome predefinito per la stringa di connessione. Fare clic sulla freccia nella parte inferiore della pagina.

	![Dettagli del sito Web](./media/create-mysql-db/provide-website-details.png)

4. Specificare un **NOME** per il database, selezionare la **REGIONE** per il database \(deve corrispondere alla regione del sito Web\), accettare le note legali di ClearDB e fare clic sul segno di spunta nella parte inferiore della pagina.

	![Dettagli di MySQL](./media/create-mysql-db/provide-mysql-details.png)

5. Dopo la creazione del sito Web, fare clic sul nome del sito per passare al dashboard del sito.

	![Visualizzazione del dashboard del sito Web](./media/create-mysql-db/go-to-website-dashboard.png)

6. Fare clic su **CONFIGURE**.

	![Visualizzazione della scheda di configurazione](./media/create-mysql-db/go-to-configure-tab.png)

7. Scorrere verso il basso fino alla sezione **connection strings**, quindi fare clic su **Show Connection Strings**.

	![Visualizzazione della stringa di connessione](./media/create-mysql-db/show-conn-string.png)

8. Copiare la stringa di connessione per l'utilizzo nell'applicazione.

	![Stringa di connessione visualizzata](./media/create-mysql-db/shown-conn-string.png)

> [AZURE.NOTE]Le stringhe di connessione sono accessibili all'applicazione del sito Web tramite il nome della stringa di connessione. Nelle applicazioni .NET le stringhe di connessione sono disponibili nell'oggetto **connectionStrings**. In altri linguaggi di programmazione le stringhe di connessione sono accessibili tramite le variabili di ambiente. Per altre informazioni, vedere [Come configurare i siti Web][configure].

[ClearDB]: http://www.cleardb.com/
[waws]: /documentation/services/web-sites/
[Azure Store]: ../articles/store.md
[portal]: http://manage.windowsazure.com
[configure]: ../articles/web-sites-configure.md

<!--HONumber=52-->
