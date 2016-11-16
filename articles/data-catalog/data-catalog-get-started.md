---
title: Introduzione a Data Catalog | Documentazione Microsoft
description: "Esercitazione end-to-end sugli scenari e le funzionalità di Azure Data Catalog."
documentationcenter: 
services: data-catalog
author: steelanddata
manager: jhubbard
editor: 
tags: 
ms.assetid: 03332872-8d84-44a0-8a78-04fd30e14b18
ms.service: data-catalog
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 09/20/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 7a401cb0cd9c13f2ab1779fbc18bee2a61ba5e77


---
# <a name="get-started-with-azure-data-catalog"></a>Introduzione ad Azure Data Catalog
Azure Data Catalog è un servizio cloud completamente gestito che funge da sistema di registrazione e di individuazione per asset di dati aziendali. Per una panoramica dettagliata, vedere [Definizione di Azure Data Catalog](data-catalog-what-is-data-catalog.md).

Questa esercitazione consente di iniziare a usare Azure Data Catalog. In questa esercitazione verranno eseguite le procedure seguenti:

| Procedura | Descrizione |
|:--- |:--- |
| [Effettuare il provisioning del catalogo dati](#provision-data-catalog) |In questa procedura si effettuerà il provisioning o la configurazione di Azure Data Catalog. Questo passaggio viene eseguito solo se il catalogo non è stato configurato in precedenza. È possibile avere solo un catalogo dati per ogni organizzazione (dominio di Microsoft Azure Active Directory), anche se all'account di Azure sono associate più sottoscrizioni. |
| [Registrare gli asset di dati](#register-data-assets) |In questa procedura si registreranno gli asset di dati del database di esempio AdventureWorks2014 nel catalogo dati. La registrazione è il processo di estrazione dei metadati strutturali chiave, quali nomi, tipi e percorsi, dall'origine dati e di copia dei metadati nel catalogo. Le origini dati e gli asset di dati rimangono dove sono, ma i metadati vengono usati dal catalogo per renderli più facilmente individuabili e comprensibili. |
| [Individuare gli asset di dati](#discover-data-assets) |In questa procedura si userà il portale di Azure Data Catalog per individuare gli asset di dati registrati nel passaggio precedente. Dopo aver registrato un'origine dati con Azure Data Catalog, i relativi metadati vengono indicizzati dal servizio, in modo che gli utenti possano eseguire facilmente ricerche per individuare i dati necessari. |
| [Annotare gli asset di dati](#annotate-data-assets) |In questa procedura si specificheranno annotazioni (informazioni come descrizioni, tag, documentazione o esperti) per gli asset di dati. Queste informazioni integrano i metadati estratti dall'origine dati e rendono l'origine dati più comprensibile a un maggior numero di persone. |
| [Connettersi agli asset di dati](#connect-to-data-assets) |In questa procedura, gli asset di dati verranno aperti in strumenti client integrati (come Excel e SQL Server Data Tools) e in uno strumento non integrato (SQL Server Management Studio). |
| [Gestire gli asset di dati](#manage-data-assets) |In questa procedura verrà configurata la sicurezza per gli asset di dati. Azure Data Catalog non consente agli utenti di accedere ai dati stessi. Il proprietario dell'origine dati controlla l'accesso ai dati. <br/><br/> Azure Data Catalog consente di trovare le origini dati e di visualizzare i **metadati** correlati alle origini registrate nel catalogo. In alcune situazioni, tuttavia, le origini dati devono essere visibili solo a utenti specifici o ai membri di gruppi specifici. Per questi scenari, è possibile usare Azure Data Catalog per acquisire la proprietà di asset di dati registrati all'interno del catalogo e controllare la visibilità degli asset di cui si è proprietari. |
| [Rimuovere gli asset di dati](#remove-data-assets) |In questa procedura verrà illustrato come rimuovere gli asset di dati dal catalogo dati. |

## <a name="tutorial-prerequisites"></a>Prerequisiti per l'esercitazione
### <a name="azure-subscription"></a>Sottoscrizione di Azure
Per configurare Azure Data Catalog l'utente deve essere proprietario o comproprietario di una sottoscrizione di Azure.

Le sottoscrizioni di Azure consentono di organizzare l'accesso alle risorse del servizio cloud, come Catalogo dati di Azure. Consentono inoltre di controllare come l'utilizzo delle risorse viene segnalato, fatturato e pagato. Ogni sottoscrizione può disporre di un’impostazione di fatturazione e pagamento diversa, in modo da poter avere sottoscrizioni e piani diversi per reparto, progetto, ufficio regionale e così via. Ogni servizio cloud appartiene a una sottoscrizione ed è necessario che la sottoscrizione sia disponibile prima di impostare il Catalogo dati di Azure. Per altre informazioni, vedere l'articolo su come [gestire gli account, le sottoscrizioni e i ruoli amministrativi](../active-directory/active-directory-how-subscriptions-associated-directory.md).

Se non è disponibile una sottoscrizione, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/) .

### <a name="azure-active-directory"></a>Azure Active Directory
Per configurare Azure Data Catalog, è necessario accedere con un account utente di Azure Active Directory (Azure AD). L'utente deve essere proprietario o comproprietario di una sottoscrizione di Azure.  

Azure AD è un servizio che offre semplici e pratiche funzionalità di gestione delle identità e degli accessi, sia nel cloud sia in locale. È possibile usare un singolo account aziendale o dell'istituto di istruzione per accedere a qualsiasi applicazione Web cloud o locale. Azure Data Catalog usa Azure AD per autenticare l'accesso. Per altre informazioni, vedere [Informazioni su Azure Active Directory](../active-directory/active-directory-whatis.md).

### <a name="azure-active-directory-policy-configuration"></a>Configurazione dei criteri di Azure Active Directory
In alcune situazioni è possibile accedere al portale di Azure Data Catalog, ma quando si prova ad accedere allo strumento di registrazione dell'origine dati viene visualizzato un messaggio di errore che impedisce l'accesso. È possibile che questo problema si verifichi quando si usa la rete aziendale o quando ci si connette dall'esterno della rete aziendale.

Lo strumento di registrazione usa l' *autenticazione basata su form* per convalidare l'accesso utente in Azure Active Directory. Per completare l'accesso, un amministratore di Azure Active Directory deve abilitare l'autenticazione basata su form nei *criteri di autenticazione globali*.

I criteri di autenticazione globali consentono di abilitare l'autenticazione separatamente per connessioni Intranet ed Extranet, come illustrato nell'immagine seguente. Se l'autenticazione basata su form non è abilitata per la rete da cui ci si connette, è possibile che si verifichino errori di accesso.

 ![Criteri di autenticazione globali di Azure Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

Per altre informazioni, vedere [Configurazione dei criteri di autenticazione](https://technet.microsoft.com/library/dn486781.aspx).

## <a name="provision-data-catalog"></a>Effettuare il provisioning del catalogo dati
È possibile effettuare il provisioning di un solo catalogo dati per organizzazione (dominio di Azure Active Directory). Quindi se il proprietario o il comproprietario di una sottoscrizione di Azure che appartiene a questo dominio di Azure Active Directory ha già creato un catalogo, non potrà crearne un altro anche se ha più sottoscrizioni di Azure. Per testare se un utente ha creato un catalogo dati nel dominio di Azure Active Directory, passare alla [home page di Azure Data Catalog](http://azuredatacatalog.com) e verificare se il catalogo è visualizzato. Se è già stato creato un catalogo, ignorare la procedura seguente e passare alla sezione successiva.    

1. Passare alla [pagina del servizio Data Catalog](https://azure.microsoft.com/services/data-catalog) e fare clic su **Introduzione**.
   
    ![Azure Data Catalog - Pagina di destinazione di marketing](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)
2. Accedere usando un account utente proprietario o comproprietario di una sottoscrizione di Azure. Dopo l'accesso verrà visualizzata la pagina seguente.
   
    ![Azure Data Catalog - Effettuare il provisioning del catalogo dati](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)
3. Specificare un **nome** per il catalogo dati, la **sottoscrizione** da usare e la **località** del catalogo.
4. Espandere **Prezzi** e selezionare un'**edizione** di Azure Data Catalog (Gratuito o Standard).
    ![Azure Data Catalog - Selezionare l'edizione](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)
5. Espandere **Utenti del catalogo** e fare clic su **Aggiungi** per aggiungere utenti per il catalogo dati. L'utente verrà aggiunto automaticamente a questo gruppo.
    ![Azure Data Catalog - Utenti](media/data-catalog-get-started/data-catalog-add-catalog-user.png)
6. Espandere **Amministratori del catalogo** e fare clic su **Aggiungi** per aggiungere altri amministratori per il catalogo dati. L'utente verrà aggiunto automaticamente a questo gruppo.
    ![Azure Data Catalog - Amministratori](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)
7. Fare clic su **Crea catalogo** per creare il catalogo dati per l'organizzazione. Dopo la creazione, verrà visualizzata la home page per il catalogo dati.
    ![Azure Data Catalog - Creazione](media/data-catalog-get-started/data-catalog-created.png)    

### <a name="find-a-data-catalog-in-the-azure-portal"></a>Trovare un catalogo dati nel portale di Azure
1. In una scheda o una finestra separata del Web browser passare al [portale di Azure](https://portal.azure.com) e accedere con lo stesso account usato per creare il catalogo dati nel passaggio precedente.
2. Selezionare **Esplora** e quindi fare clic su **Catalogo dati**.
   
    ![Azure Data Catalog - Esplorare Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png) Viene visualizzato il catalogo dati creato.
   
    ![Azure Data Catalog - Visualizzare il catalogo nell'elenco](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)
3. Fare clic sul catalogo creato. Nel portale verrà visualizzato il pannello **Catalogo dati** .
   
   ![Azure Data Catalog - Pannello nel portale ](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)
4. È possibile visualizzare le proprietà del catalogo dati e aggiornarle. Fare ad esempio clic su **Piano tariffario** e modificare l'edizione.
   
    ![Azure Data Catalog - Piano tariffario](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

### <a name="adventure-works-sample-database"></a>Database di esempio Adventure Works
In questa esercitazione si registreranno gli asset di dati (tabelle) del database di esempio AdventureWorks2014 per il motore di database di SQL Server, ma è possibile usare qualsiasi origine dati supportata se si preferisce lavorare con dati familiari e più attinenti al proprio ruolo. Per un elenco di origini dati supportate, vedere [Origini dati supportate](data-catalog-dsr.md).

### <a name="install-the-adventure-works-2014-oltp-database"></a>Installare il database OLTP Adventure Works 2014
Il database Adventure Works supporta gli scenari di elaborazione delle transazioni online standard per un produttore di biciclette fittizio (Adventure Works Cycles) che include i settori prodotti, vendite e acquisti. In questa esercitazione si registreranno le informazioni sui prodotti in Azure Data Catalog.

Per installare il database di esempio Adventure Works:

1. Scaricare [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) in CodePlex.
2. Per ripristinare il database nel computer, seguire le istruzioni disponibili in [Ripristinare un backup del database tramite SQL Server Management Studio](http://msdn.microsoft.com/library/ms177429.aspx)oppure seguire questa procedura:
   1. Aprire SQL Server Management Studio e connettersi al motore di database di SQL Server.
   2. Fare clic con il pulsante destro del mouse su **Database** e scegliere **Ripristina database**.
   3. In **Ripristina database** selezionare l'opzione **Dispositivo** per **Origine** e fare clic su **Esplora**.
   4. In **Seleziona dispositivi di backup** fare clic su **Aggiungi**.
   5. Passare alla cartella in cui si trova il file **AdventureWorks2014.bak**, selezionare il file e fare clic su **OK** per chiudere la finestra di dialogo **Individua file di backup**.
   6. Fare clic su **OK** per chiudere la finestra di dialogo **Seleziona dispositivi di backup**.    
   7. Fare clic su **OK** per chiudere la finestra di dialogo **Ripristina database**.

È ora possibile registrare gli asset di dati dal database di esempio Adventure Works usando Azure Data Catalog.

## <a name="register-data-assets"></a>Registrare gli asset di dati
In questo esercizio si userà lo strumento di registrazione per registrare gli asset di dati del database Adventure Works nel catalogo. La registrazione è il processo di estrazione dei metadati strutturali chiave, quali nomi, tipi e percorsi, dall'origine dati e dagli asset che contiene e di copia dei metadati nel catalogo. Le origini dati e gli asset di dati rimangono dove sono, ma i metadati vengono usati dal catalogo per renderli più facilmente individuabili e comprensibili.

### <a name="register-a-data-source"></a>Registrazione di un'origine dati
1. Passare alla [home page di Azure Data Catalog](https://azuredatacatalog.com) e fare clic su **Pubblica dati**.
   
   ![Azure Data Catalog - Pulsante Pubblica dati](media/data-catalog-get-started/data-catalog-publish-data.png)
2. Fare clic su **Avvia applicazione** per scaricare, installare ed eseguire lo strumento di registrazione nel computer.
   
   ![Azure Data Catalog - Pulsante Avvia](media/data-catalog-get-started/data-catalog-launch-application.png)
3. Nella **pagina iniziale** fare clic su **Accedi** e immettere le credenziali.     
   
    ![Azure Data Catalog - Pagina Introduzione](media/data-catalog-get-started/data-catalog-welcome-dialog.png)
4. Nella pagina **Microsoft Azure Data Catalog** fare clic su **SQL Server** e **Avanti**.
   
    ![Azure Data Catalog - Origini dati](media/data-catalog-get-started/data-catalog-data-sources.png)
5. Immettere le proprietà della connessione a SQL Server per **AdventureWorks2014** (vedere l'esempio seguente) e fare clic su **CONNETTI**.
   
   ![Azure Data Catalog - Impostazioni di connessione SQL Server](media/data-catalog-get-started/data-catalog-sql-server-connection.png)
6. Registrare i metadati degli asset di dati. In questo esempio si registreranno gli oggetti **Production/Product** dallo spazio dei nomi Production di AdventureWorks:
   
   1. Nell'albero **Gerarchia server** espandere **AdventureWorks2014** e fare clic su**Production**.
   2. Selezionare **Product**, **ProductCategory**, **ProductDescription** e **ProductPhoto** usando CTRL+clic.
   3. Fare clic sulla **freccia verso destra** (**>**). Questa azione sposta tutti gli oggetti selezionati nell'elenco **Oggetti da registrare** .
      
      ![Esercitazione di Azure Data Catalog - Esplorare e selezionare gli oggetti](media/data-catalog-get-started/data-catalog-server-hierarchy.png)
   4. Selezionare **Includi anteprima** per includere un'anteprima sotto forma di snapshot dei dati. Lo snapshot include fino a 20 record da ogni tabella e viene copiato nel catalogo.
   5. Selezionare **Includi profilo dati** per includere uno snapshot delle statistiche per il profilo dei dati, ad esempio valori minimi, massimi e medi per una colonna e il numero di righe.
   6. Nel campo **Aggiungi tag** immettere **adventure works, cycles**. Questa azione aggiunge i tag di ricerca per gli asset di dati. I tag sono un modo eccezionale per consentire agli utenti di trovare un'origine dati registrata.
   7. Specificare il nome di un **esperto** per i dati (facoltativo).
      
      ![Esercitazione di Azure Data Catalog - Oggetti da registrare](media/data-catalog-get-started/data-catalog-objects-register.png)
   8. Fare clic su **REGISTRA**. Nel Catalogo dati di Azure vengono registrati gli oggetti selezionati. In questo esercizio, vengono registrati gli oggetti selezionati di Adventure Works. Lo strumento di registrazione estrae i metadati dall'asset di dati e li copia nel servizio Azure Data Catalog. I dati rimangono nella posizione in cui risiedono attualmente e sotto il controllo degli amministratori e dei criteri del sistema corrente.
      
      ![Azure Data Catalog - Oggetti registrati](media/data-catalog-get-started/data-catalog-registered-objects.png)
   9. Per visualizzare gli oggetti origine dati registrati, fare clic su **Visualizza portale**. Nel portale di Azure Data Catalog verificare che tutte le quattro tabelle e il database siano visualizzati nella griglia.
      
      ![Oggetti nel portale di Azure Data Catalog ](media/data-catalog-get-started/data-catalog-view-portal.png)

In questo esercizio sono stati registrati gli oggetti dal database di esempio Adventure Works in modo che possano essere facilmente individuati dagli utenti dell'organizzazione. Nel prossimo esercizio verrà illustrato come individuare gli asset di dati registrati.

## <a name="discover-data-assets"></a>Individuare gli asset di dati
L'individuazione in Azure Data Catalog usa due meccanismi principali: ricerca e filtri.

La ricerca è stata progettata in modo da essere intuitiva ed efficiente. Per impostazione predefinita, i termini di ricerca vengono confrontati con qualsiasi proprietà del catalogo, tra cui le annotazioni indicate dall'utente.

I filtri sono progettati per completare la ricerca. Gli utenti possono selezionare caratteristiche specifiche, ad esempio esperti, tipo di origine dati, tipo di oggetto e tag, per visualizzare gli asset di dati corrispondenti e per limitare i risultati della ricerca agli asset corrispondenti.

Usando una combinazione di ricerca e filtri, gli utenti possono spostarsi rapidamente tra le origini dati registrate con Azure Data Catalog per individuare gli asset di dati di proprio interesse.

In questo esercizio si userà il portale di Azure Data Catalog per individuare gli asset di dati registrati nell'esercizio precedente. Per informazioni dettagliate sulla sintassi di ricerca, vedere [Riferimento alla sintassi di ricerca in Data Catalog](https://msdn.microsoft.com/library/azure/mt267594.aspx) .

Ecco alcuni esempi di individuazione degli asset di dati nel catalogo.  

### <a name="discover-data-assets-with-basic-search"></a>Individuare gli asset di dati con la ricerca di base
La ricerca di base consente di eseguire ricerche nel catalogo con uno o più termini di ricerca. I risultati sono gli asset che corrispondono alle proprietà di uno o più  termini specificati.

1. Fare clic su **Home** nel portale di Azure Data Catalog. Se il Web browser è stato chiuso, passare alla [home page di Azure Data Catalog](https://www.azuredatacatalog.com).
2. Nella casella di ricerca immettere `cycles` e premere **INVIO**.
   
    ![Azure Data Catalog - Ricerca di testo di base](media/data-catalog-get-started/data-catalog-basic-text-search.png)
3. Verificare che tutte le quattro tabelle e il database, AdventureWorks2014, siano visualizzati nei risultati. È possibile passare dalla **visualizzazione griglia** alla **visualizzazione elenco** e viceversa facendo clic sui pulsanti sulla barra degli strumenti, come illustrato nell'immagine seguente. Si noti che la parola chiave di ricerca è evidenziata nei risultati della ricerca perché l'opzione **Evidenzia** è **ATTIVATA**. È anche possibile specificare il numero di **risultati per pagina** nei risultati della ricerca.
   
    ![Azure Data Catalog - Risultati della ricerca di testo di base](media/data-catalog-get-started/data-catalog-basic-text-search-results.png)
   
    Il pannello **Ricerche** è visualizzato a sinistra e il pannello **Proprietà** a destra. Il pannello **Ricerche** consente di modificare i criteri di ricerca e filtrare i risultati. Il pannello **Proprietà** visualizza le proprietà di un oggetto selezionato nella griglia o nell'elenco.
4. Fare clic su **Product** nei risultati della ricerca. Fare clic sulle schede **Anteprima**, **Colonne**, **Profilo dati** e **Documentazione** oppure usare la freccia per espandere il riquadro in basso.  
   
    ![Azure Data Catalog - Riquadro inferiore](media/data-catalog-get-started/data-catalog-data-asset-preview.png)
   
    Nella scheda **Anteprima** viene visualizzata un'anteprima dei dati nella tabella **Product**.  
5. Fare clic sulla scheda **Colonne** per informazioni dettagliate sulle colonne dell'asset di dati, ad esempio **nome** e **tipo di dati**.
6. Fare clic sulla scheda **Profilo dati** per visualizzare la profilatura dei dati dell'asset di dati, ad esempio numero di righe, dimensioni dei dati o valore minimo in una colonna.
7. Filtrare i risultati usando **Filtri** a sinistra. Fare ad esempio clic su **Tabella** per **Tipo oggetto** per visualizzare solo le quattro tabelle e non il database.
   
    ![Azure Data Catalog - Filtrare i risultati della ricerca](media/data-catalog-get-started/data-catalog-filter-search-results.png)

### <a name="discover-data-assets-with-property-scoping"></a>Individuare gli asset di dati con la ricerca dell'ambito della proprietà
L'ambito della proprietà consente di individuare gli asset di dati in cui il termine di ricerca corrisponde alla proprietà specificata.

1. Deselezionare il filtro **Tabella** in **Tipo oggetto** in **Filtri**.  
2. Nella casella di ricerca immettere `tags:cycles` e premere **INVIO**. Per informazioni su tutte le proprietà che possono essere usate per le ricerche nel catalogo dati, vedere [Riferimento alla sintassi di ricerca in Data Catalog](https://msdn.microsoft.com/library/azure/mt267594.aspx) .
3. Verificare che tutte le quattro tabelle e il database, AdventureWorks2014, siano visualizzati nei risultati.  
   
    ![Data Catalog - Risultati della ricerca dell'ambito della proprietà](media/data-catalog-get-started/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>Salvare la ricerca
1. Nella sezione **Ricerca corrente** del riquadro **Ricerche** immettere un nome per la ricerca e fare clic su **Salva**.
   
    ![Azure Data Catalog - Salvare la ricerca](media/data-catalog-get-started/data-catalog-save-search.png)
2. Verificare che la ricerca salvata venga visualizzata in **Ricerche salvate**.
   
    ![Azure Data Catalog - Ricerche salvate](media/data-catalog-get-started/data-catalog-saved-search.png)
3. Selezionare una delle azioni che possono essere eseguite sulla ricerca salvata, ovvero **Rinomina**, **Elimina** o **Salva come predefinita**.
   
    ![Azure Data Catalog - Opzioni delle ricerche salvate](media/data-catalog-get-started/data-catalog-saved-search-options.png)

### <a name="boolean-operators"></a>Operatori booleani
È possibile ampliare o limitare la ricerca con operatori booleani.

1. Nella casella di ricerca immettere `tags:cycles AND objectType:table`e premere **INVIO**.
2. Verificare che nei risultati vengano visualizzate solo le tabelle, non il database.  
   
    ![Azure Data Catalog - Operatore booleano nella ricerca](media/data-catalog-get-started/data-catalog-search-boolean-operator.png)

### <a name="grouping-with-parentheses"></a>Raggruppamento con parentesi
Il raggruppamento con parentesi consente di raggruppare parti della query per ottenere l'isolamento logico, in particolare con operatori booleani.

1. Nella casella di ricerca immettere `name:product AND (tags:cycles AND objectType:table)` e premere **INVIO**.
2. Verificare che nei risultati della ricerca venga visualizzata solo la tabella **Product** .
   
    ![Azure Data Catalog - Raggruppamento delle ricerche](media/data-catalog-get-started/data-catalog-grouping-search.png)   

### <a name="comparison-operators"></a>Operatori di confronto
Gli operatori di confronto consentono di usare confronti diversi dall'uguaglianza per le proprietà che hanno dati di tipo numero e data.

1. Nella casella di ricerca immettere `lastRegisteredTime:>"06/09/2016"`.
2. Deselezionare il filtro **Tabella** in **Tipo oggetto**.
3. Premere **INVIO**.
4. Verificare che nei risultati della ricerca vengano visualizzati le tabelle **Product**, **ProductCategory**, **ProductDescription** e **ProductPhoto** e il database AdventureWorks2014 registrato.
   
    ![Azure Data Catalog - Risultati della ricerca di confronto](media/data-catalog-get-started/data-catalog-comparison-operator-results.png)

Vedere [Come individuare gli asset di dati](data-catalog-how-to-discover.md) per informazioni dettagliate sull'individuazione degli asset di dati e [Riferimento alla sintassi di ricerca in Data Catalog](https://msdn.microsoft.com/library/azure/mt267594.aspx) per la sintassi di ricerca.

## <a name="annotate-data-assets"></a>Annotare gli asset di dati
In questo esercizio verrà usato il portale di Azure Data Catalog per annotare con descrizioni, tag o esperti, gli asset di dati registrati in precedenza nel catalogo. Le annotazioni integrano e migliorano i metadati strutturali estratti dall'origine dati durante la registrazione e facilitano notevolmente l'individuazione e la comprensione degli asset di dati.

In questo esercizio verrà annotato un singolo asset di dati (ProductPhoto). Verranno aggiunti un nome descrittivo e una descrizione all'asset di dati ProductPhoto.  

1. Passare alla [home page di Azure Data Catalog](https://www.azuredatacatalog.com) ed eseguire una ricerca con `tags:cycles` per trovare gli asset di dati registrati.  
2. Fare clic su **ProductPhoto** nei risultati della ricerca.  
3. Immettere **Product images** nel campo **Nome descrittivo** e **Product photos for marketing materials** nel campo **Descrizione**.
   
    ![Azure Data Catalog - Descrizione di ProductPhoto](media/data-catalog-get-started/data-catalog-productphoto-description.png)
   
    Il contenuto del campo **Descrizione** consente ad altri utenti di individuare l'asset di dati selezionato e di comprendere perché e come usarlo. È anche possibile aggiungere altri tag e visualizzare le colonne. Ora è possibile provare a usare la ricerca e il filtro per individuare gli asset di dati mediante i metadati descrittivi aggiunti al catalogo.

In questa pagina è anche possibile eseguire queste operazioni:

* Aggiungere esperti per l'asset di dati. Fare clic su **Aggiungi** in the **Esperti** .
* Aggiungere tag a livello di set di dati. Fare clic su **Aggiungi** in the **Tag** . Un tag può essere un tag utente o un tag di glossario. L'edizione Standard di Azure Data Catalog include un glossario aziendale che consente agli amministratori del catalogo di definire una tassonomia aziendale centrale. Gli utenti del catalogo possono quindi annotare gli asset di dati con i termini di glossario. Per altre informazioni, vedere [Come configurare il glossario aziendale per l'assegnazione di tag regolamentata](data-catalog-how-to-business-glossary.md)
* Aggiungere tag a livello di colonna. Fare clic su **Aggiungi** under **Tag** per la colonna da annotare.
* Aggiungere una descrizione a livello di colonna. Immettere una **Descrizione** per una colonna. È anche possibile visualizzare i metadati di descrizione estratti dall'origine dati.
* In **Richiedi accesso** specificare in che modo gli utenti devono richiedere l'accesso all'asset di dati.
  
    ![Azure Data Catalog - Aggiungere tag, descrizioni](media/data-catalog-get-started/data-catalog-add-tags-experts-descriptions.png)
* Fare clic sulla scheda **Documentazione** e specificare la documentazione per l'asset di dati. La documentazione di Azure Data Catalog consente di usare il catalogo dati come repository di contenuti per creare un testo descrittivo completo degli asset di dati.
  
    ![Azure Data Catalog - Scheda Documentazione](media/data-catalog-get-started/data-catalog-documentation.png)

È anche possibile aggiungere un'annotazione a più asset di dati. È ad esempio possibile selezionare tutti gli asset di dati registrati e specificare un esperto per gli asset.

![Azure Data Catalog - Annotare più asset di dati](media/data-catalog-get-started/data-catalog-multi-select-annotate.png)

Azure Data Catalog supporta un approccio di tipo crowdsourcing alle annotazioni. Qualsiasi utente di Azure Data Catalog può aggiungere tag (utente o glossario), descrizioni e altri metadati, in modo che qualsiasi utente con una prospettiva su un asset di dati e sul suo uso possa acquisire tale prospettiva e renderla disponibile agli altri utenti.

Per informazioni dettagliate sull'annotazione di asset di dati, vedere [Come annotare gli asset di dati](data-catalog-how-to-annotate.md) .

## <a name="connect-to-data-assets"></a>Connettersi agli asset di dati
In questo esercizio, gli asset di dati verranno aperti in uno strumento client integrato (Excel) e in uno strumento non integrato (SQL Server Management Studio) usando le informazioni di connessione.

> [!NOTE]
> È importante ricordare che Azure Data Catalog non consente di accedere all'origine dati effettiva, ma ne semplifica l'individuazione e la comprensione. Quando ci si connette a un'origine dati, l'applicazione client scelta usa le credenziali di Windows oppure richiede le credenziali in base alla necessità. Se all'utente non è stato concesso in precedenza l'accesso all'origine dati, dovrà essere concesso per consentirne la connessione.
> 
> 

### <a name="connect-to-a-data-asset-from-excel"></a>Connettersi a un asset di dati da Excel
1. Selezionare **Product** nei risultati della ricerca. Fare clic su **Apri in** sulla barra degli strumenti e quindi su **Excel**.
   
    ![Azure Data Catalog - Connettersi all'asset di dati](media/data-catalog-get-started/data-catalog-connect1.png)
2. Fare clic su **Apri** nella finestra popup di download. Questa esperienza può variare in base al browser.
   
    ![Azure Data Catalog - File di connessione Excel scaricato](media/data-catalog-get-started/data-catalog-download-open.png)
3. Nella finestra **Avviso di sicurezza di Microsoft Excel** fare clic su **Abilita**.
   
    ![Azure Data Catalog - Popup di sicurezza Excel](media/data-catalog-get-started/data-catalog-excel-security-popup.png)
4. Mantenere i valori predefiniti nella finestra di dialogo **Importa dati** e fare clic su **OK**.
   
    ![Azure Data Catalog - Importazione dati di Excel](media/data-catalog-get-started/data-catalog-excel-import-data.png)
5. Visualizzare l'origine dati in Excel.
   
    ![Azure Data Catalog - Tabella Product in Excel](media/data-catalog-get-started/data-catalog-connect2.png)

In questo esercizio ci si connetterà agli asset di dati individuati usando Azure Data Catalog. Il portale di Azure Data Catalog consente di connettersi direttamente usando le applicazioni client integrate nel menu **Apri in** . È anche possibile connettersi con qualsiasi applicazione scelta usando le informazioni sulla località della connessione incluse nei metadati dell'asset. È ad esempio possibile usare SQL Server Management Studio per connettersi al database AdventureWorks2014 per accedere ai dati degli asset di dati registrati in questa esercitazione.

1. Aprire **SQL Server Management Studio**.
2. Nella finestra di dialogo **Connetti al server** immettere il nome del server presente nel riquadro **Proprietà** del portale di Azure Data Catalog.
3. Usare l'autenticazione e le credenziali appropriate per accedere agli asset di dati. Se non è possibile accedere, usare le informazioni nel campo **Richiedi accesso** per ottenere l'accesso.
   
    ![Azure Data Catalog - Richiedi accesso](media/data-catalog-get-started/data-catalog-request-access.png)

Fare clic su **Visualizza stringhe di connessione** per visualizzare e copiare negli Appunti le stringhe di connessione ADF.NET, ODBC e OLEDB da usare nell'applicazione.

## <a name="manage-data-assets"></a>Gestire gli asset di dati
In questo passaggio verrà illustrato come configurare la sicurezza per gli asset di dati. Azure Data Catalog non consente agli utenti di accedere ai dati stessi. Il proprietario dell'origine dati controlla l'accesso ai dati.

È possibile usare Azure Data Catalog per trovare le origini dati e di visualizzare i metadati correlati alle origini registrate nel catalogo. In alcune situazioni, tuttavia, le origini dati devono essere visibili solo a utenti specifici o ai membri di gruppi specifici. Per questi scenari, è possibile usare Azure Data Catalog per acquisire la proprietà di asset di dati registrati all'interno del catalogo e controllare quindi la visibilità degli asset di cui si è proprietari.

> [!NOTE]
> Le funzionalità di gestione descritte in questo esercizio sono disponibili solo nell'Edizione Standard di Azure Data Catalog e non nell'Edizione gratuita.
> In Azure Data Catalog è possibile assumere la proprietà di asset di dati, aggiungere comproprietari ad asset di dati e impostare la visibilità degli asset di dati.
> 
> 

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Assumere la proprietà di asset di dati e limitarne la visibilità
1. Passare alla [home page di Azure Data Catalog](https://www.azuredatacatalog.com). Nella casella di testo di **ricerca** immettere `tags:cycles` e premere **INVIO**.
2. Fare clic su un elemento nell'elenco dei risultati e quindi su **Diventa proprietario** sulla barra degli strumenti.
3. Nella sezione **Gestione** del pannello **Proprietà** fare clic su **Diventa proprietario**.
   
    ![Azure Data Catalog - Diventa proprietario](media/data-catalog-get-started/data-catalog-take-ownership.png)
4. Per limitare la visibilità, scegliere **Proprietario e questi utenti** nella sezione **Visibilità** e fare clic su **Aggiungi**. Immettere gli indirizzi di posta elettronica degli utenti nella casella di testo e premere **INVIO**.
   
    ![Azure Data Catalog - Limitazione accesso](media/data-catalog-get-started/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Rimuovere gli asset di dati
In questo esercizio si userà il portale di Azure Data Catalog per rimuovere i dati di anteprima dagli asset di dati registrati ed eliminare gli asset di dati dal catalogo.

In Azure Data Catalog è possibile eliminare un singolo asset o più asset contemporaneamente.

1. Passare alla [home page di Azure Data Catalog](https://www.azuredatacatalog.com).
2. Nella casella di testo di **ricerca** immettere `tags:cycles` e premere **INVIO**.
3. Selezionare un elemento nell'elenco dei risultati e fare clic su **Elimina** sulla barra degli strumenti, come illustrato nell'immagine seguente:
   
    ![Azure Data Catalog - Eliminare un elemento della griglia](media/data-catalog-get-started/data-catalog-delete-grid-item.png)
   
    Se si usa la visualizzazione elenco, la casella di controllo è a sinistra dell'elemento come illustrato nell'immagine seguente:
   
    ![Azure Data Catalog - Eliminare un elemento dell'elenco](media/data-catalog-get-started/data-catalog-delete-list-item.png)
   
    È anche possibile selezionare più asset di dati ed eliminarli come illustrato nell'immagine seguente:
   
    ![Azure Data Catalog - Eliminare più asset di dati](media/data-catalog-get-started/data-catalog-delete-assets.png)

> [!NOTE]
> Il comportamento predefinito del catalogo consente a tutti gli utenti di registrare qualsiasi origine dati e consente a qualsiasi utente di eliminare qualsiasi asset di dati registrato. Le funzionalità di gestione incluse nell'Edizione Standard di Azure Data Catalog forniscono opzioni aggiuntive per acquisire la proprietà degli asset, limitando gli utenti che possono individuare ed eliminare gli asset.
> 
> 

## <a name="summary"></a>Riepilogo
In questa esercitazione sono state analizzate le funzionalità di base di Azure Data Catalog, compresa la registrazione, l'annotazione, l'individuazione e la gestione di asset di dati aziendali. Ora che è stata completata l'esercitazione, è possibile iniziare. È possibile iniziare subito registrando le origini dati usate personalmente o dal team e invitando i colleghi a usare il catalogo.

## <a name="references"></a>Riferimenti
* [How to register data assets (Come registrare gli asset di dati)](data-catalog-how-to-register.md)
* [How to discover data assets (Come individuare gli asset di dati)](data-catalog-how-to-discover.md)
* [How to annotate data assets (Come annotare gli asset di dati)](data-catalog-how-to-annotate.md)
* [How to document data assets (Come documentare gli asset di dati)](data-catalog-how-to-documentation.md)
* [How to connect to data assets (Come connettersi agli asset di dati)](data-catalog-how-to-connect.md)
* [Come gestire gli asset di dati](data-catalog-how-to-manage.md)




<!--HONumber=Nov16_HO2-->


