<properties
	pageTitle="Introduzione ad Azure Data Catalog | Microsoft Azure"
	description="Esercitazione end-to-end sugli scenari e le funzionalità di Azure Data Catalog."
	documentationCenter=""
	services="data-catalog"
	authors="steelanddata"
	manager=""
	editor=""
	tags=""/>
<tags
	ms.service="data-catalog"
	ms.devlang="NA"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-catalog"
	ms.date="07/06/2016"
	ms.author="spelluru"/>

# Esercitazione: Introduzione ad Azure Data Catalog
Azure Data Catalog è un servizio cloud completamente gestito che funge da sistema di registrazione e di individuazione per asset di dati aziendali. Per una panoramica dettagliata, vedere [Definizione di Azure Data Catalog](data-catalog-what-is-data-catalog.md).

Questa esercitazione consente di iniziare a usare Azure Data Catalog. In questa esercitazione si imparerà a:

| Passaggio | Descrizione |
| :--- | :---------- |
| [Effettuare il provisioning del catalogo dati](#provision-data-catalog) | In questo passaggio si effettueranno il provisioning e l'installazione di Azure Data Catalog. Questo passaggio viene eseguito solo se il catalogo non è stato installato in precedenza. È possibile avere un solo catalogo dati per organizzazione (dominio di Azure Active Directory) anche se sono associate più sottoscrizioni all'account Azure. | 
| [Registrare gli asset di dati](#register-data-assets) | In questo passaggio verranno registrati con il catalogo dati gli asset di dati del database di esempio AdventureWorks2014. La registrazione è il processo di estrazione dei metadati strutturali chiave, quali nomi, tipi e percorsi, dall'origine dati e di copia dei metadati nel catalogo. Le origini dati e gli asset di dati rimangono dove sono, ma i metadati vengono usati dal catalogo per renderli più facilmente individuabili e comprensibili. |
| [Individuare gli asset di dati](#discover-data-assets) | In questo esercizio si userà il portale di Azure Data Catalog per individuare gli asset di dati registrati nel passaggio precedente. Dopo aver registrato un'origine dati con Azure Data Catalog, i relativi metadati vengono indicizzati dal servizio, in modo che gli utenti possano eseguire facilmente ricerche per individuare i dati necessari. |
| [Annotare gli asset di dati](#annotate-data-assets) | In questo passaggio verranno indicate le annotazioni (descrizioni, tag, documentazione, esperti e così via) per gli asset di dati per integrare i metadati estratti dall'origine dati e rendere l'origine dati più comprensibile a più persone. | 
| [Connettersi agli asset di dati](#connect-to-data-assets) | In questo passaggio, gli asset di dati verranno aperti in strumenti client integrati come Excel e SQL Server Data Tools, nonché in uno strumento non integrato, SQL Server Management Studio, usando le informazioni di connessione. |
| [Gestire gli asset di dati](#manage-data-assets) | In questo passaggio si vedrà come configurare la sicurezza per gli asset di dati. Azure Data Catalog non consente agli utenti di accedere ai dati stessi. L'accesso ai dati è controllato dal proprietario dell'origine dati. <br/><br/>Azure Data Catalog consente agli utenti di trovare le origini dati e di visualizzare i **metadati** correlati alle origini registrate nel catalogo. In alcune situazioni, tuttavia, le origini dati devono essere visibili solo a utenti specifici o ai membri di gruppi specifici. Per questi scenari, Azure Data Catalog consente agli utenti di acquisire la proprietà di asset di dati registrati all'interno del catalogo e quindi di controllare la visibilità degli asset di cui sono proprietari. | 
| [Rimuovere gli asset di dati](#remove-data-assets) | In questo passaggio si apprenderà come rimuovere gli asset di dati dal catalogo dati. |  
 
## Prerequisiti per l'esercitazione

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

### Sottoscrizione di Azure
Per configurare Azure Data Catalog, l'utente deve essere **proprietario o comproprietario** di una sottoscrizione di Azure.

Le sottoscrizioni di Azure consentono di organizzare l'accesso alle risorse del servizio cloud, come Catalogo dati di Azure. Consentono inoltre di controllare come l'utilizzo delle risorse viene segnalato, fatturato e pagato. Ogni sottoscrizione può disporre di un’impostazione di fatturazione e pagamento diversa, in modo da poter avere sottoscrizioni e piani diversi per reparto, progetto, ufficio regionale e così via. Ogni servizio cloud appartiene a una sottoscrizione ed è necessario che la sottoscrizione sia disponibile prima di impostare il Catalogo dati di Azure. Per ulteriori informazioni, vedere [Gestire account, sottoscrizioni e ruoli amministrativi](../active-directory/active-directory-how-subscriptions-associated-directory.md).

Se non è disponibile una sottoscrizione, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

### Azure Active Directory
Per configurare Azure Data Catalog è necessario accedere con un **account utente di Azure Active Directory** ed essere proprietari o comproprietari di una sottoscrizione di Azure.

Azure Active Directory (Azure AD) è un servizio che offre semplici e pratiche funzionalità di gestione delle identità e degli accessi, sia nel cloud sia in locale. Gli utenti possono utilizzare un singolo account aziendale o dell'istituto di istruzione per eseguire il Single Sign-On a qualsiasi applicazione Web locale o nel cloud. Il Catalogo dati di Azure utilizza Azure AD per autenticare il Single Sign-On. Per altre informazioni, vedere [Informazioni su Azure Active Directory](../active-directory/active-directory-whatis.md).

### Configurazione dei criteri di Azure Active Directory

In alcuni casi, è possibile che si verifichi una situazione in cui gli utenti possono accedere al portale del Catalogo dati di Azure, ma, quando tentano di accedere allo strumento di registrazione dell'origine dati, viene restituito un messaggio di errore che impedisce l'accesso. Questo errore si verifica solo quando l'utente è nella rete aziendale o si connette dall'esterno della rete aziendale.

Lo strumento di registrazione usa l'**autenticazione basata su form** per convalidare gli accessi degli utenti in Active Directory. Per accedere, l'autenticazione basata su form deve essere abilitata nei **criteri di autenticazione globali** da un amministratore di Active Directory.

I criteri di autenticazione globali consentono di abilitare i metodi di autenticazione separatamente per le connessioni Extranet e Intranet, come illustrato di seguito. Se l'autenticazione basata su form non è abilitata per la rete da cui l'utente si connette, è possibile che si verifichino errori di accesso.

 ![Criteri di autenticazione globali di Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

Per altre informazioni, vedere [Configurare i criteri di autenticazione](https://technet.microsoft.com/library/dn486781.aspx).

## Effettuare il provisioning del catalogo dati
È possibile effettuare il provisioning di un solo catalogo dati per organizzazione (dominio di Azure Active Directory). Se quindi il proprietario o il comproprietario di una sottoscrizione di Azure che appartiene a questo dominio di Active Directory ha già creato un catalogo, non potrà crearne un altro anche se ha più sottoscrizioni di Azure. Per verificare se un utente ha creato un catalogo dati nel dominio di Active Directory, passare a http://azuredatacatalog.com e verificare se è visualizzato il catalogo. Ignorare la procedura seguente e passare alla sezione successiva se è già stato creato un catalogo.

1. Accedere a [https://azure.microsoft.com/services/data-catalog](https://azure.microsoft.com/services/data-catalog).

	![Azure Data Catalog - pagina di destinazione di marketing](media/data-catalog-get-started/data-catalog-marketing-landing-page.png) e fare clic su **Introduzione**.
2. Accedere usando un account utente **proprietario o comproprietario** di una sottoscrizione di Azure. Dopo aver effettuato l'accesso verrà visualizzata la pagina seguente.

	![Azure Data Catalog - effettuare il provisioning del catalogo dati](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)
2. Specificare un **nome** per il catalogo di dati, la **sottoscrizione** da usare e la **località** del catalogo.
3. Espandere **Prezzi** e specificare l'**edizione** di Azure Data Catalog (gratuita e standard). ![Azure Data Catalog - selezionare l'edizione](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)
4. Espandere **Utenti del catalogo**, fare clic su **Aggiungi** per aggiungere utenti per il catalogo dati. L'utente verrà automaticamente aggiunto a questo gruppo. ![Azure Data Catalog - utenti](media/data-catalog-get-started/data-catalog-add-catalog-user.png)
5. Espandere **Amministratori del catalogo** e fare clic su **Aggiungi** per aggiungere altri amministratori per il catalogo dati. L'utente verrà automaticamente aggiunto a questo gruppo. ![Azure Data Catalog - amministratori](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)
6. Fare clic sul pulsante **Crea catalogo** per creare il catalogo dati per l'organizzazione. Verrà visualizzata la home page creata per il catalogo dati. ![Azure Data Catalog - creazione](media/data-catalog-get-started/data-catalog-created.png)

### Trovare il catalogo dati nel portale di Azure
1. In una scheda o una finestra separata del Web browser, passare a [https://portal.azure.com](https://portal.azure.com) ed eseguire l'accesso usando lo stesso account usato per creare il catalogo dati nel passaggio precedente.
2. Fare clic su **Esplora** e quindi su **Data Catalog**.

	![Azure Data Catalog - esplorare il portale di Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)
3. Verrà visualizzato il catalogo dati creato.

	![Azure Data Catalog - visualizzare il catalogo nell'elenco](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)
4.  Fare clic sul catalogo creato per visualizzare il pannello **Data Catalog** nel portale.

	![Azure Data Catalog - pannello nel portale](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)
5. È possibile visualizzare le proprietà del catalogo dati e aggiornarle. Fare ad esempio clic su **Piano tariffario** e modificare l'edizione.

	![Azure Data Catalog - piano tariffario](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

### Database di esempio Adventure Works 
In questa esercitazione verranno registrati gli asset di dati (tabelle) del database di esempio AdventureWorks2014 per il motore di database di SQL Server, ma è possibile usare qualsiasi origine dati supportata se si preferisce lavorare con dati familiari e più attinenti al proprio ruolo. Per un elenco di origini dati supportate, vedere [Origini dati supportate da Azure Data Catalog](data-catalog-dsr.md).

### Installare il database OLTP Adventure Works 2014
Il database Adventure Works supporta gli scenari di elaborazione delle transazioni online standard per un produttore di biciclette fittizio (Adventure Works Cycles) che include i settori Prodotti, Vendite e Acquisti. In questa esercitazione verranno registrate le informazioni sui prodotti in **Azure Data Catalog**.

Ecco come installare il database di esempio Adventure Works:

1. Scaricare [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) in CodePlex.
2. Seguire le istruzioni in questo articolo: [Ripristino di un backup del database (SQL Server Management Studio)](http://msdn.microsoft.com/library/ms177429.aspx) per ripristinare il database nel computer. **Azioni rapide**:
	1. Avviare SQL Server Management Studio e connettersi al motore di database di SQL Server.
	2. Fare clic con il pulsante destro del mouse su **Database**, quindi scegliere **Ripristina database**.
	3. Nella finestra di dialogo **Ripristina database** selezionare l'opzione **Dispositivo** per **Origine** e fare clic su **Sfoglia (...)**.
	4. Nella finestra di dialogo **Seleziona dispositivi di backup**, fare clic su **Aggiungi**.
	5. Passare alla cartella in cui si trova il file **AdventureWorks2014.bak**, selezionare il file e fare clic su **OK** per chiudere la finestra di dialogo **Individua file di backup**.
	6. Fare clic su **OK** per chiudere la finestra di dialogo **Seleziona dispositivi di backup**.
	7. Fare clic su **OK** per chiudere la finestra di dialogo **Ripristina database**.

Ora verranno registrati gli asset di dati dal database di esempio Adventure Works con **Azure Data Catalog**.

## Registrare gli asset di dati

In questo esercizio si userà lo strumento di registrazione per registrare gli asset di dati del database Adventure Works con il catalogo. La registrazione è il processo di estrazione dei metadati strutturali chiave, quali nomi, tipi e percorsi, dall'origine dati e dall’asset che lo contiene e di copia dei metadati nel catalogo. Le origini dati e gli asset di dati rimangono dove sono, ma i metadati vengono usati dal catalogo per renderli più facilmente individuabili e comprensibili.

### Di seguito viene illustrato come registrare un'origine dati

1.	Passare a [https://azuredatacatalog.com](https://azuredatacatlog.com) e fare clic su **Pubblica dati** nella home page.

    ![Azure Data Catalog - pulsante Pubblica dati](media/data-catalog-get-started/data-catalog-publish-data.png)

3.	Fare clic su **Avvia applicazione** per scaricare, installare ed eseguire lo **strumento di registrazione** nel computer.

    ![Azure Data Catalog - pulsante Avvia](media/data-catalog-get-started/data-catalog-launch-application.png)

4. Nella pagina **iniziale** fare clic su **Accedi** e immettere le credenziali.

	![Azure Data Catalog - finestra di dialogo iniziale](media/data-catalog-get-started/data-catalog-welcome-dialog.png)

5. Nella pagina **Microsoft Azure Data Catalog** fare doppio clic su **SQL Server** oppure fare clic su **SQL Server** e quindi su **Avanti**.

    ![Azure Data Catalog - origini dati](media/data-catalog-get-started/data-catalog-data-sources.png)

6.	Immettere le proprietà di connessione di SQL Server per **AdventureWorks2014** (vedere l'esempio seguente) e fare clic su **CONNETTI**.

    ![Azure Data Catalog - stringhe di connessione SQL Server](media/data-catalog-get-started/data-catalog-sql-server-connection.png)

7.	Nella pagina successiva è possibile registrare i metadati dell'asset di dati. In questo esempio si registreranno gli oggetti **Production/Product** dallo spazio dei nomi di produzione di AdventureWorks. Ecco come fare:
    
	1. Nell'albero **Gerarchia server** espandere **AdventureWorks2014** e fare clic su **Production**.
	2. Fare clic tenendo premuto CTRL su **Product**, **ProductCategory**, **ProductDescription** e **ProductPhoto**.
	3. Fare clic sulla **freccia verso destra** (**>**). Tutti gli oggetti Product selezionati verranno spostati nell'elenco **Oggetti da registrare**.
			
    	![Esercitazione di Azure Data Catalog - sfogliare e selezionare gli oggetti](media/data-catalog-get-started/data-catalog-server-hierarchy.png)
	4. Selezionare **Includi anteprima** per includere un'anteprima dei dati. Lo snapshot include fino a 20 record da ogni tabella e viene copiato nel catalogo.
	5. Selezionare **Includi profilo dati** per includere uno snapshot delle statistiche per il profilo dei dati, ad esempio valori minimi, massimi e medi per una colonna, un numero di righe e così via.
	5. In **Aggiungere i tag** immettere **adventure works, cycles**. Verrà aggiunto il tag di ricerca per questi asset di dati. I tag sono un modo eccezionale per consentire agli utenti di trovare un'origine dati registrata.
	6. (Facoltativo) Specificare il nome di un **esperto** per questi dati.
	
    	![Esercitazione di Azure Data Catalog - oggetti da registrare](media/data-catalog-get-started/data-catalog-objects-register.png)
    
	7. Fare clic su **REGISTRA**. Nel Catalogo dati di Azure vengono registrati gli oggetti selezionati. In questo esercizio, vengono registrati gli oggetti selezionati di Adventure Works. Lo strumento di registrazione estrae i metadati dall'asset di dati e li copia nel servizio Azure Data Catalog. I dati rimangono nella posizione in cui risiedono attualmente e sotto il controllo degli amministratori e dei criteri del sistema corrente.
	
		![Azure Data Catalog - oggetti registrati](media/data-catalog-get-started/data-catalog-registered-objects.png)

	8. Per visualizzare gli oggetti origine dati registrati, fare clic su **Visualizza portale**. Nel portale di **Azure Data Catalog**, verificare che tutte le quattro tabelle e il database siano visualizzati nella griglia.
 
    	![Oggetti nel portale di Azure Data Catalog](media/data-catalog-get-started/data-catalog-view-portal.png)
  
	
In questo esercizio sono stati registrati gli oggetti dal database di esempio Adventure Works in modo che possano essere facilmente individuati dagli utenti dell'organizzazione. Nel prossimo esercizio si apprende come individuare gli asset di dati registrati.

## Individuare gli asset di dati
L'individuazione in Azure Data Catalog usa due meccanismi principali: ricerca e filtri.

La **ricerca** è progettata per essere intuitiva e potente: per impostazione predefinita, i termini di ricerca vengono confrontati con qualsiasi proprietà del catalogo, tra cui le annotazioni indicate dall'utente.

I **filtri** sono progettati per completare la ricerca. Gli utenti possono selezionare caratteristiche specifiche, ad esempio esperti, tipo di origine dati, tipo di oggetto e tag, per visualizzare solo gli asset di dati corrispondenti e per limitare anche i risultati della ricerca agli asset corrispondenti.

Usando una combinazione di ricerca e filtri, gli utenti possono spostarsi rapidamente tra le origini dati registrate con Azure Data Catalog per individuare gli asset di dati di proprio interesse.

In questo esercizio si userà il portale di **Azure Data Catalog** per individuare gli asset di dati registrati nell'esercizio precedente. Vedere [Riferimento alla sintassi di ricerca nel catalogo dati](https://msdn.microsoft.com/library/azure/mt267594.aspx) per dettagli sulla sintassi di ricerca.

Ora verranno esaminati alcuni esempi di individuazione degli asset di dati nel catalogo.

### Ricerca di base
La ricerca di base consente di eseguire ricerche nel catalogo con uno o più termini di ricerca. I risultati sono gli asset che corrispondono alle proprietà di uno o più termini specificati.

1. Fare clic sul pulsante **Home** nel portale di Azure Data Catalog. Se è stato chiuso il Web browser, passare a [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com).
2. Nella casella di ricerca nella parte superiore, immettere **cycles** e fare clic sull'icona di **ricerca** oppure premere **INVIO**.
	
	![Azure Data Catalog - ricerca di testo di base](media/data-catalog-get-started/data-catalog-basic-text-search.png)
3. Verificare che tutte le quattro tabelle e il database, AdventureWorks2014, siano visualizzati nei risultati. È possibile passare dalla **visualizzazione griglia** alla **visualizzazione elenco** e viceversa facendo clic sui pulsanti nella parte superiore come illustrato nell'immagine seguente. Si noti che la parola chiave di ricerca è evidenziata nei risultati della ricerca perché l'opzione **Evidenzia** nella parte superiore è **abilitata**. È anche possibile specificare il numero di **risultati per pagina** nei risultati della ricerca.

	![Azure Data Catalog - risultati della ricerca di testo di base](media/data-catalog-get-started/data-catalog-basic-text-search-results.png)
	
	Il pannello **Ricerche** è visualizzato a sinistra e il pannello **Proprietà** a destra. Il pannello di ricerca consente di modificare i criteri di ricerca e filtrare i risultati. Il pannello Proprietà visualizza le proprietà di un oggetto selezionato nella griglia o nell'elenco.

4. Fare clic su **Product** nei risultati di ricerca. Fare clic sulle schede denominate **Anteprima**, **Colonne**, **Profilo dati** e **Documentazione** oppure usare la freccia **SU** per espandere il riquadro in basso al centro.
 
	![Azure Data Catalog - riquadro inferiore](media/data-catalog-get-started/data-catalog-data-asset-preview.png)
	
	Nella scheda **Anteprima** viene visualizzata l'anteprima dei dati nella tabella Product.
5. Fare clic sulla scheda **Colonne** per informazioni dettagliate sulle colonne, ad esempio **nome** e **tipo di dati**, nell'asset di dati.
6. Fare clic sulla scheda **Profilo dati** per visualizzare la profilatura dei dati, ad esempio numero di righe, dimensioni dei dati, valore minimo in una colonna e così via, nell'asset di dati.
6. Filtrare i risultati usando **Filtri** a sinistra. Fare ad esempio clic su **Tabella** per **Tipo oggetto** per visualizzare solo le quattro tabelle, non il database.

	![Azure Data Catalog - filtrare i risultati della ricerca](media/data-catalog-get-started/data-catalog-filter-search-results.png)

### Ambito della proprietà
L'ambito della proprietà consente di individuare gli asset di dati in cui il termine di ricerca corrisponde alla proprietà specificata.

3. Deselezionare il filtro **Tabella** per **Tipo oggetto** in **Filtri**.
4. Nella casella di ricerca, immettere **tags:cycles** e fare clic sull'icona di **ricerca** oppure premere **INVIO**. Vedere [Riferimento alla sintassi di ricerca nel catalogo dati](https://msdn.microsoft.com/library/azure/mt267594.aspx) per tutte le proprietà che possono essere usate per le ricerche nel catalogo dati.
3. Verificare che tutte le quattro tabelle e il database, AdventureWorks2014, siano visualizzati nei risultati.

	![Catalogo dati - risultati della ricerca dell'ambito della proprietà](media/data-catalog-get-started/data-catalog-property-scoping-results.png)

### Salvare la ricerca 
1. Nel riquadro Ricerche a sinistra, nella sezione Ricerca corrente, fare clic su Salva per salvare i criteri di ricerca correnti. Immettere un nome per la ricerca e fare clic su Salva.
	
	![Azure Data Catalog - salvare la ricerca](media/data-catalog-get-started/data-catalog-save-search.png)
2. Verificare che la ricerca salvata venga visualizzata in Ricerche salvate.

	![Azure Data Catalog - ricerche salvate](media/data-catalog-get-started/data-catalog-saved-search.png)
3. Fare clic sulla freccia GIÙ per visualizzare le azioni che possono essere eseguite sulla ricerca salvata, ovvero ridenominazione, eliminazione, impostazione come ricerca predefinita. ![Azure Data Catalog - opzioni delle ricerche salvate](media/data-catalog-get-started/data-catalog-saved-search-options.png)

### Operatori booleani
Gli operatori booleani consentono di ampliare o restringere una ricerca.

2. Nella casella di ricerca, immettere **tags:cycles AND objectType:table** e fare clic sull'icona di **ricerca** oppure premere **INVIO**.
3. Verificare che nei risultati vengano visualizzate solo le tabelle, non il database.

	![Azure Data Catalog - operatore booleano nella ricerca](media/data-catalog-get-started/data-catalog-search-boolean-operator.png)

### Raggruppamento con parentesi
Il raggruppamento con parentesi consente di usare le parentesi per raggruppare parti della query per ottenere l'isolamento logico, soprattutto in combinazione con gli operatori booleani.

1. Nella casella di ricerca, immettere **name:product AND (tags:cycles AND objectType:table)** e fare clic sull'icona di **ricerca** oppure premere **INVIO**.
2. Verificare che nei risultati di ricerca sia ora visualizzata solo la tabella **Product**.

	![Azure Data Catalog - raggruppamento della ricerca](media/data-catalog-get-started/data-catalog-grouping-search.png)

### Operatori di confronto
Gli operatori di confronto consentono di usare confronti diversi dall'uguaglianza per le proprietà che hanno dati di tipo numero e data.

1. Nella casella di ricerca, immettere **lastRegisteredTime:>"06/09/2016"**.
2. Deselezionare il filtro **Tabella** per **Tipo oggetto** a sinistra.
3. Fare clic sull'icona di **ricerca** o premere **INVIO**.
2. Verificare i risultati della ricerca visualizzino le tabelle Product, ProductCategory, ProductDescription e ProductPhoto e il database AdventureWorks2014 registrato.

	![Azure Data Catalog - risultati della ricerca di confronto](media/data-catalog-get-started/data-catalog-comparison-operator-results.png)

Vedere [How to discover data assets](data-catalog-how-to-discover.md) (Come individuare gli asset di dati) per informazioni dettagliate sull'individuazione degli asset di dati e [Riferimento alla sintassi di ricerca nel catalogo dati](https://msdn.microsoft.com/library/azure/mt267594.aspx) per la sintassi di ricerca.

## Annotare gli asset di dati
In questo esercizio verrà usato il portale di **Azure Data Catalog** per annotare con descrizioni, tag, esperti e così via, gli asset di dati registrati in precedenza nel catalogo. Le annotazioni immesse andranno a integrare e migliorare i metadati strutturali estratti dall'origine dati durante la registrazione e sarà ancora più semplice individuare e comprendere gli asset di dati.

### Ecco come è possibile annotare gli asset di dati
In questo passaggio verrà annotato un singolo set di dati, ProductPhoto. Verranno aggiunti un nome descrittivo, una descrizione e così via all'asset di dati ProductPhoto.

1.  Se è stato chiuso il Web browser, passare a [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com) ed eseguire una ricerca con **tags:cycles** per trovare gli asset di dati registrati.
2. Fare clic su **ProductPhoto** nei risultati della ricerca.
3. Immettere **Product images** per il campo **Nome descrittivo** e **Product photos for marketing materials** per il campo **Descrizione**.

	![Azure Data Catalog - descrizione di ProductPhoto](media/data-catalog-get-started/data-catalog-productphoto-description.png)

	La **descrizione** consentirà ad altri utenti di trovare l'asset di dati selezionato e di comprendere perché e come usarlo. È inoltre possibile aggiungere ulteriori tag e visualizzare le colonne. Ora è possibile provare a usare la ricerca e il filtro per individuare gli asset di dati mediante i metadati descrittivi aggiunti al catalogo.

Si noti che è anche possibile eseguire queste operazioni nella pagina:

- Aggiungere esperti per l'asset di dati. Fare clic su **Aggiungi** in **Esperti** nel riquadro di destra.
- Aggiungere tag a livello di set di dati. Fare clic su **Aggiungi** in **Tag** nel riquadro di destra. Un tag può essere un tag utente o un tag di glossario. L'edizione Standard di Data Catalog include un glossario aziendale che consente agli amministratori del catalogo di definire una tassonomia aziendale centrale. Gli utenti del catalogo possono quindi annotare gli asset di dati con i termini di glossario. Per altre informazioni, vedere [Come configurare il glossario aziendale per l'assegnazione di tag regolamentata](data-catalog-how-to-business-glossary.md)
- Aggiungere tag a livello di colonna. Nel riquadro inferiore al centro, fare clic su **Aggiungi** in **Tag** per la colonna da annotare.
- Aggiungere una descrizione a livello di colonna. Nel riquadro inferiore al centro, immettere **Descrizione** per una colonna. È anche possibile visualizzare i metadati di descrizione estratti dall'origine dati.
- In **Richiedi accesso** specificare come richiedere l'accesso all'asset di dati.

	![Azure Data Catalog - aggiungere tag, descrizioni](media/data-catalog-get-started/data-catalog-add-tags-experts-descriptions.png)
  
- Fare clic sulla scheda **Documentazione** nel riquadro inferiore al centro e indicare la documentazione per l'asset di dati. La documentazione di Azure Data Catalog consente di usare il catalogo dati come repository di contenuti per creare un testo descrittivo completo degli asset di dati.

	![Azure Data Catalog - scheda Documentazione](media/data-catalog-get-started/data-catalog-documentation.png)


È anche possibile selezionare più elementi o tutti gli elementi e aggiungere un'annotazione a più/tutti gli asset di dati. È ad esempio possibile selezionare tutti gli asset di dati registrati e specificare un esperto per gli asset.

![Azure Data Catalog - annotare più asset di dati](media/data-catalog-get-started/data-catalog-multi-select-annotate.png)

Azure Data Catalog supporta l'approccio di crowdsourcing alle annotazioni e consente agli utenti del catalogo dati di aggiungere tag (utente o glossario), descrizioni e altri metadati, in modo che qualsiasi utente con una prospettiva su un asset di dati e sul suo uso possa acquisire tale prospettiva e renderla disponibile agli altri utenti.

Vedere [How to annotate data assets](data-catalog-how-to-annotate.md) (Come annotare gli asset di dati) per informazioni dettagliate sulle annotazioni di asset di dati.
 
## Connettersi agli asset di dati
In questo esercizio, gli asset di dati verranno aperti in uno strumento client integrato come Excel e in uno strumento non integrato come SQL Server Management Studio usando le informazioni di connessione.

> [AZURE.NOTE] È importante ricordare che il **Catalogo dati di Azure** non consente agli utenti di accedere all'origine dati effettiva, ma ne semplifica per gli utenti l’individuazione e la comprensione. Quando gli utenti si connettono a un'origine dati, l'applicazione client scelta usa le credenziali di Windows o richiede le credenziali necessarie. Se l'utente non ha già ottenuto l'accesso all'origine dati, dovrà essere concesso l'accesso prima che possa connettersi.

### Per connettersi a un asset di dati da Excel

1. Selezionare **Product** dai risultati della ricerca. Fare clic su **Apri in** sulla barra degli strumenti e selezionare **Excel**.
 
    ![Azure Data Catalog - connettersi all'asset di dati](media/data-catalog-get-started/data-catalog-connect1.png)
5. Fare clic su **Apri** nella finestra popup di download nella parte inferiore. Questa esperienza può variare a seconda del browser.

	![Azure Data Catalog - file di connessione Excel scaricato](media/data-catalog-get-started/data-catalog-download-open.png)
6. Nella finestra **Avviso di sicurezza di Microsoft Excel**, fare clic su **Abilita**.

	![Azure Data Catalog - popup di sicurezza Excel](media/data-catalog-get-started/data-catalog-excel-security-popup.png)
7. Nella finestra di dialogo **Importa dati** conservare le impostazioni predefinite e fare clic su **OK**.

	![Azure Data Catalog - importazione dati di Excel](media/data-catalog-get-started/data-catalog-excel-import-data.png)
8. L'origine dati viene aperta in Excel.

    ![Azure Data Catalog - tabella Product in Excel](media/data-catalog-get-started/data-catalog-connect2.png)

In questo esercizio ci si connetterà agli asset di dati individuati usando **Azure Data Catalog**. Il portale del **Catalogo dati di Azure** consente agli utenti di connettersi direttamente tramite le applicazioni client integrate nel menu **Apri in** e consente agli utenti di connettersi mediante qualsiasi applicazione scelta usando le informazioni sul percorso di connessione incluse nei metadati dell'asset. È ad esempio possibile usare SQL Server Management Studio per connettersi al database AdventureWorks2014 per accedere ai dati degli asset di dati registrati in questa esercitazione.

1. Avviare **SQL Server Management Studio**.
2. Nella finestra di dialogo **Connetti al server** immettere il **nome server** presente nel riquadro **Proprietà** del portale di Azure Data Catalog.
3. Usare l'**autenticazione** e le **credenziali** appropriate per accedere agli asset di dati se si ha già accesso agli asset di dati. Se non ha accesso, usare le informazioni presenti nel campo Richiedi accesso per ottenere l'accesso.

	![Azure Data Catalog - Richiedi accesso](media/data-catalog-get-started/data-catalog-request-access.png)

Fare clic su **Visualizza stringhe di connessione** per visualizzare e copiare negli Appunti le stringhe di connessione ADF.NET, ODBC e OLEDB per usarle nell'applicazione.

## Gestire gli asset di dati
In questo passaggio si vedrà come configurare la sicurezza per gli asset di dati. Azure Data Catalog non consente agli utenti di accedere ai dati stessi. L'accesso ai dati è controllato dal proprietario dell'origine dati.

Azure Data Catalog consente agli utenti di trovare le origini dati e di visualizzare i **metadati** correlati alle origini registrate nel catalogo. In alcune situazioni, tuttavia, le origini dati devono essere visibili solo a utenti specifici o ai membri di gruppi specifici. Per questi scenari, Azure Data Catalog consente agli utenti di acquisire la proprietà di asset di dati registrati all'interno del catalogo e quindi di controllare la visibilità degli asset di cui sono proprietari.

> [AZURE.NOTE] Le funzionalità di gestione descritte in questo esercizio sono disponibili solo nell'**Edizione Standard di Azure Data Catalog** e non nell'**Edizione gratuita**. Nel **Catalogo dati di Azure** è possibile assumere la proprietà di asset di dati, aggiungere co-proprietari ad asset di dati e impostare la visibilità degli asset di dati.

### Ecco come si assume la proprietà di asset di dati si limita la visibilità

1. Se è stato chiuso il Web browser, passare a [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com). Nella casella di ricerca, immettere **tags:cycles** e premere **INVIO**.
3. Selezionare un elemento nell'elenco dei risultati, ad esempio **Product**, facendo clic sulla casella di controllo nell'angolo superiore destro, quindi fare clic su **Diventa proprietario** sulla barra degli strumenti, come illustrato nell'immagine seguente.
4. Nel pannello **Proprietà** a destra, sezione **Gestione**, fare clic su **Diventa proprietario**.

	![Azure Data Catalog - Diventa proprietario](media/data-catalog-get-started/data-catalog-take-ownership.png)
5. Per limitare la visibilità, fare clic su **Proprietario e questi utenti** nella sezione **Visibilità** e fare clic su **Aggiungi**. Immettere l'indirizzo e-mail dell'utente nella casella di testo e premere INVIO.

    ![Azure Data Catalog - limitazione accesso](media/data-catalog-get-started/data-catalog-ownership.png)

## Rimuovere gli asset di dati

In questo esercizio si userà il portale del **Catalogo dati di Azure** per rimuovere i dati di anteprima dagli asset di dati registrati ed eliminare gli asset di dati dal catalogo.

In **Azure Data Catalog** è possibile eliminare un singolo asset o più asset contemporaneamente.

### Per eliminare gli asset di dati

1. Se è stato chiuso il Web browser, passare a [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com).
2. Nella casella di ricerca, immettere **tags:cycles** e premere **INVIO**.
3. Selezionare un elemento nell'elenco dei risultati, ad esempio **ProductDescription**, facendo clic sulla casella di controllo nell'angolo superiore destro, quindi fare clic su **Elimina** sulla barra degli strumenti, come illustrato nell'immagine seguente.

	![Azure Data Catalog - eliminare un elemento della griglia](media/data-catalog-get-started/data-catalog-delete-grid-item.png)
	
	Se si usa la visualizzazione elenco anziché la visualizzazione griglia, la casella di controllo è a sinistra dell'elemento come illustrato nell'immagine seguente.

	![Azure Data Catalog - eliminare un elemento dell'elenco](media/data-catalog-get-started/data-catalog-delete-list-item.png)

	È anche possibile selezionare più asset di dati ed eliminarli come indicato di seguito:

	![Azure Data Catalog - eliminare più asset di dati](media/data-catalog-get-started/data-catalog-delete-assets.png)


> [AZURE.NOTE] Il comportamento predefinito del catalogo consente a tutti gli utenti di registrare qualsiasi origine dati e consente a qualsiasi utente di eliminare qualsiasi asset di dati registrato. Le funzionalità di gestione incluse nell'**Edizione Standard del Catalogo dati di Azure** forniscono opzioni aggiuntive per acquisire la proprietà degli asset, limitando gli utenti che possono individuare ed eliminare gli asset.


## Riepilogo

In questa esercitazione sono state analizzate le funzionalità di base di **Azure Data Catalog**, compresa la registrazione, l'annotazione, l'individuazione e la gestione di asset di dati aziendali. Ora che è stata completata l'esercitazione, è possibile iniziare. È possibile iniziare subito registrando le origini dati usate personalmente o dal team e invitando i colleghi a usare il catalogo.

## Riferimenti

- [How to register data assets](data-catalog-how-to-register.md) (Come registrare gli asset di dati)
- [How to discover data assets](data-catalog-how-to-discover.md) (Come individuare gli asset di dati)
- [How to annotate data assets](data-catalog-how-to-annotate.md) (Come annotare gli asset di dati)
- [How to document data assets](data-catalog-how-to-documentation.md) (Come documentare gli asset di dati)
- [How to connect to data assets](data-catalog-how-to-connect.md) (Come connettersi agli asset di dati)
- [Come gestire gli asset di dati](data-catalog-how-to-manage.md)

<!---HONumber=AcomDC_0713_2016-->