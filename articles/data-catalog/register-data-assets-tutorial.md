---
title: Registrare gli asset di dati in Azure Data Catalog
description: Come registrare gli asset di dati in Azure Data Catalog
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: tutorial
ms.date: 08/01/2019
ms.openlocfilehash: 6dcf29c1bb98d15daf652671f31ed1647d66a81b
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68735197"
---
# <a name="tutorial-register-data-assets-in-azure-data-catalog"></a>Esercitazione: Registrare gli asset di dati in Azure Data Catalog

Questa esercitazione descrive come usare lo strumento di registrazione per registrare gli asset di dati dell'esempio di database SQL di Azure con il catalogo. La registrazione è il processo di estrazione dei metadati strutturali chiave, quali nomi, tipi e percorsi, dall'origine dati e dagli asset che contiene e di copia dei metadati nel catalogo. Le origini dati e gli asset di dati rimangono dove sono, ma i metadati vengono usati dal catalogo per renderli più facilmente individuabili e comprensibili.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Registrare gli asset di dati 
> * Eseguire ricerche negli asset di dati
> * Annotare gli asset di dati
> * Connettersi agli asset di dati
> * Gestire gli asset di dati
> * Eliminare gli asset di dati

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, è necessario completare la guida di [avvio rapido](register-data-assets-tutorial.md).

* Una sottoscrizione di [Microsoft Azure](https://azure.microsoft.com/).
* È necessario avere il proprio [tenant di Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

Per configurare Data Catalog, l'utente deve essere proprietario o comproprietario di una sottoscrizione di Azure.

## <a name="register-data-assets"></a>Registrare gli asset di dati

### <a name="register-a-data-source"></a>Registrazione di un'origine dati

Gli asset di dati (tabelle) verranno registrati da un [esempio di database SQL di Azure](../sql-database/sql-database-single-database-get-started.md), ma è possibile usare qualsiasi origine dati supportata se si preferisce lavorare con dati noti e più attinenti al proprio ruolo. Per un elenco di origini dati supportate, vedere [Origini dati supportate](data-catalog-dsr.md).

Il nome del database SQL di Azure usato in questa esercitazione è *RLSTest*.

È ora possibile registrare gli asset di dati dall'esempio di database SQL di Azure usando Azure Data Catalog.

1. Passare alla [home page di Azure Data Catalog](http://azuredatacatalog.com) e selezionare **Pubblica dati**.

   ![Azure Data Catalog - Pulsante Pubblica dati](media/register-data-assets-tutorial/data-catalog-publish-data.png)

2. Selezionare **Avvia applicazione** per scaricare, installare ed eseguire lo strumento di registrazione nel computer.

   ![Azure Data Catalog - Pulsante Avvia](media/register-data-assets-tutorial/data-catalog-launch-application.png)

3. Nella pagina iniziale selezionare **Accedi** e immettere le **credenziali **.

    ![Azure Data Catalog - Pagina Introduzione](media/register-data-assets-tutorial/data-catalog-welcome-dialog.png)

4. Nella pagina **Microsoft Azure Data Catalog** selezionare **SQL Server** e quindi **Avanti**.

    ![Azure Data Catalog - Origini dati](media/register-data-assets-tutorial/data-catalog-data-sources.png)

5. Immettere le proprietà della connessione a SQL Server per l'esempio di database SQL di Azure e selezionare **CONNETTI**.

   ![Azure Data Catalog - Impostazioni di connessione SQL Server](media/register-data-assets-tutorial/data-catalog-sql-server-connection.png)

6. Registrare i metadati degli asset di dati. In questo esempio vengono registrati oggetti **Product** dallo spazio dei nomi dell'esempio di database SQL di Azure:

    1. Nell'albero **Gerarchia server** espandere l'esempio di database SQL di Azure e selezionare **SalesLT**.

    2. Selezionare **Product**, **ProductCategory**, **ProductDescription** e **ProductModel** usando CTRL+selezione.

    3. Selezionare la **freccia verso destra** ( **>** ) per spostare la selezione. Questa azione sposta tutti gli oggetti selezionati nell'elenco **Oggetti da registrare** .

          ![Esercitazione di Azure Data Catalog - Esplorare e selezionare gli oggetti](media/register-data-assets-tutorial/data-catalog-server-hierarchy.png)

    4. Selezionare **Includi anteprima** per includere un'anteprima sotto forma di snapshot dei dati. Lo snapshot include fino a 20 record di ogni tabella e viene copiato nel catalogo.

    5. Selezionare **Includi profilo dati** per includere uno snapshot delle statistiche per il profilo dei dati, ad esempio valori minimi, massimi e medi per una colonna e il numero di righe.

    6. Nel campo **Aggiungi tag** immettere **sales, product, azure sql**. Questa azione aggiunge i tag di ricerca per gli asset di dati. I tag sono un modo eccezionale per consentire agli utenti di trovare un'origine dati registrata.

    7. Specificare il nome di un **esperto** per i dati (facoltativo).

          ![Esercitazione di Azure Data Catalog - Oggetti da registrare](media/register-data-assets-tutorial/data-catalog-objects-register.png)

    8. Selezionare **REGISTRA**. Nel Catalogo dati di Azure vengono registrati gli oggetti selezionati. In questo esercizio vengono registrati gli oggetti selezionati nell'esempio di database SQL di Azure. Lo strumento di registrazione estrae i metadati dall'asset di dati e li copia nel servizio Azure Data Catalog. I dati non vengono rimossi e rimangono sotto il controllo degli amministratori e dei criteri del sistema di origine.

          ![Azure Data Catalog - Oggetti registrati](media/register-data-assets-tutorial/data-catalog-registered-objects.png)

    9. Per visualizzare gli oggetti origine dati registrati, selezionare **Visualizza portale**. Nel portale di Azure Data Catalog verificare che tutte e quattro le tabelle e il database siano visualizzati nella griglia (verificare che la barra di ricerca sia vuota).

        ![Oggetti nel portale di Azure Data Catalog](media/register-data-assets-tutorial/data-catalog-view-portal.png)

In questo esercizio sono stati registrati gli oggetti dell'esempio di database SQL di Azure in modo che possano essere facilmente individuati dagli utenti dell'organizzazione.

Nel prossimo esercizio verrà illustrato come individuare gli asset di dati registrati.

## <a name="discover-data-assets"></a>Individuare gli asset di dati

L'individuazione in Azure Data Catalog usa due meccanismi principali: ricerca e filtri.

La ricerca è stata progettata in modo da essere intuitiva ed efficiente. Per impostazione predefinita, i termini di ricerca vengono confrontati con qualsiasi proprietà del catalogo, tra cui le annotazioni indicate dall'utente.

I filtri sono progettati per completare la ricerca. Gli utenti possono selezionare caratteristiche specifiche, ad esempio esperti, tipo di origine dati, tipo di oggetto e tag, per visualizzare gli asset di dati corrispondenti e per limitare i risultati della ricerca agli asset corrispondenti.

Usando una combinazione di ricerca e filtri, gli utenti possono spostarsi rapidamente tra le origini dati registrate con Azure Data Catalog.

In questo esercizio si userà il portale di Azure Data Catalog per individuare gli asset di dati registrati nell'esercizio precedente. Per informazioni dettagliate sulla sintassi di ricerca, vedere [Riferimento alla sintassi di ricerca in Data Catalog](/rest/api/datacatalog/#search-syntax-reference) .

Ecco alcuni esempi di individuazione degli asset di dati nel catalogo.  

### <a name="discover-data-assets-with-basic-search"></a>Individuare gli asset di dati con la ricerca di base

La ricerca di base consente di eseguire ricerche nel catalogo con uno o più termini di ricerca. I risultati sono gli asset che corrispondono alle proprietà di uno o più  termini specificati.

1. Selezionare **Home** nel portale di Azure Data Catalog. Se il Web browser è stato chiuso, passare alla [home page di Azure Data Catalog](https://www.azuredatacatalog.com).

2. Nella casella di ricerca immettere `product` e premere **INVIO**.

    ![Azure Data Catalog - Ricerca di testo di base](media/register-data-assets-tutorial/data-catalog-basic-text-search.png)

3. Verificare che tutte e quattro le tabelle e il database siano visualizzati nei risultati. È possibile passare dalla **visualizzazione griglia** alla **visualizzazione elenco** e viceversa selezionando i pulsanti sulla barra degli strumenti, come illustrato nella figura seguente. Si noti che la parola chiave di ricerca è evidenziata nei risultati della ricerca perché l'opzione **Evidenzia** è **ATTIVATA**. È anche possibile specificare il numero di **risultati per pagina** nei risultati della ricerca.

    ![Azure Data Catalog - Risultati della ricerca di testo di base](media/register-data-assets-tutorial/data-catalog-basic-text-search-results.png)

    Il pannello **Ricerche** è visualizzato a sinistra e il pannello **Proprietà** a destra. Il pannello **Ricerche** consente di modificare i criteri di ricerca e filtrare i risultati. Il pannello **Proprietà** visualizza le proprietà di un oggetto selezionato nella griglia o nell'elenco.

4. Selezionare **Product** nei risultati della ricerca. Selezionare le schede **Anteprima**, **Colonne**, **Profilo dati** e **Documentazione** oppure usare la freccia per espandere il riquadro in basso.  

    ![Azure Data Catalog - Riquadro inferiore](media/register-data-assets-tutorial/data-catalog-data-asset-preview.png)

    Nella scheda **Anteprima** viene visualizzata un'anteprima dei dati nella tabella **Product**.  
5. Selezionare la scheda **Colonne** per informazioni dettagliate sulle colonne dell'asset di dati, ad esempio **nome** e **tipo di dati**.

6. Selezionare la scheda **Profilo dati** per visualizzare informazioni dettagliate sui dati dell'asset di dati, ad esempio numero di righe, dimensioni dei dati o valore minimo in una colonna.

### <a name="discover-data-assets-with-property-scoping"></a>Individuare gli asset di dati con la ricerca dell'ambito della proprietà

L'ambito della proprietà consente di individuare gli asset di dati in cui il termine di ricerca corrisponde alla proprietà specificata.

1. Deselezionare il filtro **Tabella** in **Tipo oggetto** in **Filtri**.  

2. Nella casella di ricerca immettere `tags:product` e premere **INVIO**. Per informazioni su tutte le proprietà che possono essere usate per le ricerche nel catalogo dati, vedere [Riferimento alla sintassi di ricerca in Data Catalog](/rest/api/datacatalog/#search-syntax-reference) .

3. Verificare che le tabelle e il database siano visualizzati nei risultati.  

    ![Data Catalog - Risultati della ricerca dell'ambito della proprietà](media/register-data-assets-tutorial/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>Salvare la ricerca

1. Nella sezione **Ricerca corrente** del riquadro **Ricerche** immettere un nome per la ricerca e selezionare **Salva**.

    ![Azure Data Catalog - Salvare la ricerca](media/register-data-assets-tutorial/data-catalog-save-search.png)

2. Verificare che la ricerca salvata venga visualizzata in **Ricerche salvate**.

3. Selezionare una delle azioni che possono essere eseguite sulla ricerca salvata, ovvero **Rinomina**, **Elimina** o **Salva come predefinita**.

### <a name="grouping-with-parentheses"></a>Raggruppamento con parentesi

Il raggruppamento con parentesi consente di raggruppare parti della query per ottenere l'isolamento logico, in particolare con operatori booleani.

1. Nella casella di ricerca immettere `name:product AND (tags:product AND objectType:table)` e premere **INVIO**.

2. Verificare che nei risultati della ricerca venga visualizzata solo la tabella **Product** .

    ![Azure Data Catalog - Raggruppamento delle ricerche](media/register-data-assets-tutorial/data-catalog-grouping-search.png)

### <a name="comparison-operators"></a>Operatori di confronto

Gli operatori di confronto consentono di usare confronti diversi dall'uguaglianza per le proprietà che hanno dati di tipo numero e data.

1. Nella casella di ricerca immettere `lastRegisteredTime:>"06/09/2016"`.

2. Deselezionare il filtro **Tabella** in **Tipo oggetto**.

3. Premere **INVIO**.

4. Verificare che nei risultati della ricerca siano visibili le tabelle **Product**, **ProductCategory** e **ProductDescription**, oltre al database SQL di Azure registrato.

    ![Azure Data Catalog - Risultati della ricerca di confronto](media/register-data-assets-tutorial/data-catalog-comparison-operator-results.png)

Per informazioni dettagliate sull'individuazione di asset di dati, vedere [Come individuare gli asset di dati](data-catalog-how-to-discover.md). Per altre informazioni sulla sintassi di ricerca, vedere [Informazioni di riferimento sulla sintassi di ricerca di Data Catalog](/rest/api/datacatalog/#search-syntax-reference).

## <a name="annotate-data-assets"></a>Annotare gli asset di dati

In questo esercizio verrà usato il portale di Azure Data Catalog per annotare gli asset di dati esistenti nel catalogo, con informazioni come descrizioni, tag o esperti. Le annotazioni integrano i metadati strutturali estratti dall'origine dati durante la registrazione. Con le annotazioni gli asset di dati diventano molto più facili da individuare e interpretare.

In questo esercizio verrà annotato un singolo asset di dati (ProductPhoto). Verranno aggiunti un nome descrittivo e una descrizione all'asset di dati ProductPhoto.  

1. Passare alla [home page di Azure Data Catalog](https://www.azuredatacatalog.com) ed eseguire una ricerca con `tags:product` per trovare gli asset di dati registrati.

2. Selezionare **ProductModel** nei risultati della ricerca.  

3. Immettere **Product images** nel campo **Nome descrittivo** e **Product photos for marketing materials** nel campo **Descrizione**.

    ![Azure Data Catalog - Descrizione di ProductPhoto](media/register-data-assets-tutorial/data-catalog-productmodel-description.png)

    Il contenuto del campo **Descrizione** consente ad altri utenti di individuare l'asset di dati selezionato e di comprendere perché e come usarlo. È anche possibile aggiungere altri tag e visualizzare le colonne. È possibile cercare e filtrare le origini dati usando i metadati descrittivi aggiunti al catalogo.

In questa pagina è anche possibile eseguire queste operazioni:

* Aggiungere esperti per l'asset di dati. Selezionare **Aggiungi** nell'area **Esperti**.

* Aggiungere tag a livello di set di dati. Selezionare **Aggiungi** nell'area **Tag**. Un tag può essere un tag utente o un tag di glossario. L'edizione Standard di Azure Data Catalog include un glossario aziendale che consente agli amministratori del catalogo di definire una tassonomia aziendale centrale. Gli utenti del catalogo possono quindi annotare gli asset di dati con i termini di glossario. Per altre informazioni, vedere [Come configurare il glossario aziendale per l'assegnazione di tag regolamentata](data-catalog-how-to-business-glossary.md)

* Aggiungere tag a livello di colonna. Selezionare **Aggiungi** in **Tag** per la colonna da annotare.

* Aggiungere una descrizione a livello di colonna. Immettere una **Descrizione** per una colonna. È anche possibile visualizzare i metadati di descrizione estratti dall'origine dati.

* In **Richiedi accesso** specificare in che modo gli utenti devono richiedere l'accesso all'asset di dati.
  
* Fare clic sulla scheda **Documentazione** e specificare la documentazione per l'asset di dati. La documentazione di Azure Data Catalog consente di usare il catalogo dati come repository di contenuti per creare un testo descrittivo completo degli asset di dati.
  
È anche possibile aggiungere un'annotazione a più asset di dati. È ad esempio possibile selezionare tutti gli asset di dati registrati e specificare un esperto per gli asset.

![Azure Data Catalog - Annotare più asset di dati](media/register-data-assets-tutorial/data-catalog-multi-select-annotate.png)

Azure Data Catalog supporta un approccio di tipo crowdsourcing alle annotazioni. Qualsiasi utente di Data Catalog può aggiungere tag (utente o glossario), descrizioni e altri metadati. In questo modo, gli utenti aggiungono la loro idea degli asset di dati e del relativo uso e possono condividerla con altri utenti.

Per informazioni dettagliate sull'annotazione di asset di dati, vedere [Come annotare gli asset di dati](data-catalog-how-to-annotate.md) .

## <a name="connect-to-data-assets"></a>Connettersi agli asset di dati

In questo esercizio, gli asset di dati verranno aperti in uno strumento client integrato (Excel) e in uno strumento non integrato (SQL Server Management Studio) usando le informazioni di connessione.

> [!NOTE]
> È importante ricordare che Azure Data Catalog non consente di accedere all'origine dati effettiva, ma ne semplifica l'individuazione e la comprensione. Quando ci si connette a un'origine dati, l'applicazione client scelta usa le credenziali di Windows oppure richiede le credenziali in base alla necessità. Se all'utente non è stato concesso in precedenza l'accesso all'origine dati, dovrà essere concesso per consentirne la connessione.

### <a name="connect-to-a-data-asset-from-excel"></a>Connettersi a un asset di dati da Excel

1. Selezionare **Product** nei risultati della ricerca. Selezionare **Apri in** sulla barra degli strumenti e quindi **Excel**.

    ![Azure Data Catalog - Connettersi all'asset di dati](media/register-data-assets-tutorial/data-catalog-connect1.png)

2. Selezionare **Apri** nella finestra popup di download. Questa esperienza può variare in base al browser.

3. Nella finestra **Avviso di sicurezza di Microsoft Excel** selezionare **Abilita**.

    ![Azure Data Catalog - Popup di sicurezza Excel](media/register-data-assets-tutorial/data-catalog-excel-security-popup.png)

4. Mantenere i valori predefiniti nella finestra di dialogo **Importa dati** e selezionare **OK**.

    ![Azure Data Catalog - Importazione dati di Excel](media/register-data-assets-tutorial/data-catalog-excel-import-data.png)

5. Visualizzare l'origine dati in Excel.

    ![Azure Data Catalog - Tabella Product in Excel](media/register-data-assets-tutorial/data-catalog-connect2.png)

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

In questo esercizio ci si connetterà agli asset di dati individuati usando Azure Data Catalog. Il portale di Azure Data Catalog consente di connettersi direttamente usando le applicazioni client integrate nel menu **Apri in** . È anche possibile connettersi con qualsiasi applicazione scelta usando le informazioni sulla località della connessione incluse nei metadati dell'asset. È ad esempio possibile usare SQL Server Management Studio per connettersi al database SQL di Azure e accedere ai dati degli asset di dati registrati in questa esercitazione.

1. Aprire **SQL Server Management Studio**.

2. Nella finestra di dialogo **Connetti al server** immettere il nome del server presente nel riquadro **Proprietà** del portale di Azure Data Catalog.

3. Usare l'autenticazione e le credenziali appropriate per accedere agli asset di dati. Se non è possibile accedere, usare le informazioni nel campo **Richiedi accesso** per ottenere l'accesso.

    ![Azure Data Catalog - Richiedi accesso](media/register-data-assets-tutorial/data-catalog-request-access.png)

Selezionare **Visualizza stringhe di connessione** per visualizzare e copiare negli Appunti le stringhe di connessione ADO.NET, ODBC e OLEDB da usare nell'applicazione.

## <a name="manage-data-assets"></a>Gestire gli asset di dati

In questo passaggio verrà illustrato come configurare la sicurezza per gli asset di dati. Data Catalog non consente agli utenti di accedere ai dati stessi. Il proprietario dell'origine dati controlla l'accesso ai dati.

È possibile usare Azure Data Catalog per trovare le origini dati e di visualizzare i metadati correlati alle origini registrate nel catalogo. In alcune situazioni, tuttavia, le origini dati devono essere visibili solo a utenti specifici o ai membri di gruppi specifici. Per questi scenari, è possibile usare Data Catalog per acquisire la proprietà degli asset di dati registrati e controllare la visibilità degli asset di cui si è proprietari.

> [!NOTE]
> Le funzionalità di gestione descritte in questo esercizio sono disponibili solo nell'Edizione Standard di Azure Data Catalog e non nell'Edizione gratuita.
> In Azure Data Catalog è possibile assumere la proprietà di asset di dati, aggiungere comproprietari ad asset di dati e impostare la visibilità degli asset di dati.

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Assumere la proprietà di asset di dati e limitarne la visibilità

1. Passare alla [home page di Azure Data Catalog](https://www.azuredatacatalog.com). Nella casella di testo di **ricerca** immettere `tags:cycles` e premere **INVIO**.

2. Selezionare un elemento nell'elenco risultati e quindi **Diventa proprietario** sulla barra degli strumenti.

3. Nella sezione **Gestione** del pannello **Proprietà** selezionare **Diventa proprietario**.

    ![Azure Data Catalog - Diventa proprietario](media/register-data-assets-tutorial/data-catalog-take-ownership.png)

4. Per limitare la visibilità, scegliere **Proprietario e questi utenti** nella sezione **Visibilità** e selezionare **Aggiungi**. Immettere gli indirizzi di posta elettronica degli utenti nella casella di testo e premere **INVIO**.

    ![Azure Data Catalog - Limitazione accesso](media/register-data-assets-tutorial/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Rimuovere gli asset di dati

In questo esercizio si userà il portale di Azure Data Catalog per rimuovere i dati di anteprima dagli asset di dati registrati ed eliminare gli asset di dati dal catalogo.

In Azure Data Catalog è possibile eliminare un singolo asset o più asset contemporaneamente.

1. Passare alla [home page di Azure Data Catalog](https://www.azuredatacatalog.com).

2. Nella casella di testo di **ricerca** immettere `tags:cycles` e premere **INVIO**.

3. Selezionare un elemento nell'elenco risultati e selezionare **Elimina** sulla barra degli strumenti, come illustrato nella figura seguente:

    ![Azure Data Catalog - Eliminare un elemento della griglia](media/register-data-assets-tutorial/data-catalog-delete-grid-item.png)

    Se si usa la visualizzazione elenco, la casella di controllo è a sinistra dell'elemento come illustrato nella figura seguente:

    ![Azure Data Catalog - Eliminare un elemento dell'elenco](media/register-data-assets-tutorial/data-catalog-delete-list-item.png)

    È anche possibile selezionare più asset di dati ed eliminarli come illustrato nell'immagine seguente:

    ![Azure Data Catalog - Eliminare più asset di dati](media/register-data-assets-tutorial/data-catalog-delete-assets.png)

> [!NOTE]
> Il comportamento predefinito del catalogo consente a tutti gli utenti di registrare qualsiasi origine dati e consente a qualsiasi utente di eliminare qualsiasi asset di dati registrato. Le funzionalità di gestione incluse nell'Edizione Standard di Azure Data Catalog forniscono opzioni aggiuntive per acquisire la proprietà degli asset, limitando gli utenti che possono individuare ed eliminare gli asset.

## <a name="summary"></a>Summary

In questa esercitazione sono state analizzate le funzionalità di base di Azure Data Catalog, compresa la registrazione, l'annotazione, l'individuazione e la gestione di asset di dati aziendali. Ora che è stata completata l'esercitazione, è possibile iniziare. È possibile iniziare subito registrando le origini dati usate personalmente o dal team e invitando i colleghi a usare il catalogo.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Origini dati supportate](data-catalog-dsr.md)