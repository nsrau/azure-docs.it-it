<properties
   pageTitle="Introduzione al Catalogo dei dati di Azure con catalogo dati"
   description="Esercitazione end-to-end degli scenari e delle funzionalità del Catalogo dati di Azure"
   documentationCenter=""
   services="data-catalog"
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="07/13/2015"
   ms.author="derrickv"/>

# Introduzione al Catalogo dati di Azure

In questo articolo è riportata un'esercitazione end-to-end degli scenari e delle funzionalità dell’anteprima pubblica di Catalogo dati di Azure. Quando si effettua l'iscrizione per l'anteprima, è possibile seguire questi passaggi per creare un catalogo dati e registrare, annotare e individuare le origini dati.

## Prerequisiti per l'esercitazione

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

-	**Sottoscrizione di Azure**: se non si dispone di una sottoscrizione, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere l'articolo [Versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).
-	**Azure Active Directory** - Catalogo dati di Azure utilizza [Azure Active Directory](http://azure.microsoft.com/services/active-directory/)per la gestione delle identità e degli accessi.
-	**Origini dati** - Catalogo dati di Azure offre funzionalità per l'individuazione delle origine dati e per eseguire l'esercitazione è necessario avere accesso a una o più origini dati. L'esercitazione è stata scritta utilizzando i database di esempio Adventure Works, ma è possibile utilizzare qualsiasi origine dati supportata se si preferisce lavorare con dati che sono familiari e pertinenti al ruolo in uso.

## Esercizio 1: installare il database di esempio Adventure Works

In questo esercizio, si installa l'esempio Adventure Works per il motore di database di SQL Server e SQL Server Analysis Services - Dati multidimensionali. Questi esempi vengono utilizzati negli esercizi seguenti.

> [AZURE.NOTE]Questo esercizio è facoltativo. Gli esercizi rimanenti dell'esercitazione sono scritti per fare riferimento ai database di esempio Adventure Works, ma è anche possibile scegliere di ignorare l’esercizio e utilizzare proprie origini dati. Ecco i passaggi per installare Adventure Works.

### Installare i database Adventure Works 2014 OLTP e Data Warehouse

Il database Adventure Works OLTP supporta scenari di elaborazione delle transazioni online standard per un produttore di biciclette fittizio (Adventure Works Cycles) incluso lo scenario di fabbricazione, vendite e acquisti. Il database Adventure Works DW illustra come compilare un data warehouse.

I database si trovano in http://msftdbprodsamples.codeplex.com/ e possono essere installati seguendo i passaggi in [Come installare i database di esempio Adventure Works 2014].

In questo esercizio sono stati installati i database di esempio Adventure Works che vengono utilizzati negli esercizi rimanenti. Se si sceglie di ignorare questo esercizio e di utilizzare le origini dati aziendali, è necessario conoscere nomi, tag e altri metadati.

## Esercizio 2: registrazione delle origini dati

In questo esercizio si utilizzerà lo strumento di registrazione del Catalogo dati di Azure per registrare le origini dati nel catalogo. La registrazione è il processo di estrazione dei metadati strutturali chiave, quali nomi, tipi e percorsi, dall'origine dati e dall’asset che lo contiene e di copia dei metadati nel catalogo. Le origini dati e i relativi dati rimangono dove sono, ma i metadati vengono utilizzati dal catalogo per renderli più facilmente individuabili e comprensibili.

### Di seguito viene illustrato come registrare un'origine dati

1.	Accedere al portale del Catalogo dati di Azure.

    ![register1][1]

2.	Scorrere verso il basso e fare clic su **Pubblica dati**.

    ![register2][2]
3.	Fare clic su **Avvia applicazione**.
4.	Nella pagina **iniziale** fare clic su **Accedi** e immettere le credenziali.
5.	Nella pagina **Catalogo dati di Microsoft Azure** fare clic su **SQL Server**.

    ![register3][3]
6.	Immettere il **nome server** e fare clic su **CONNETTI**.
7.	Nella pagina successiva è possibile registrare i metadati dell'origine dati. In questo esempio, si registrano oggetti **Product** dello spazio dei nomi di produzione di AdventureWorks. Ecco come fare:
    
    a. Nell'albero della gerarchia, fare clic su **Produzione**.

    b. CTRL + clic su Product, ProductCategory, ProductDescription e ProductPhoto.

    ![register4][4]

    c. Fare clic sulla freccia per lo spostamento dell’elemento selezionato (\*\*>\*\*). In tal modo si spostano tutti gli oggetti Product nell’elenco **Da registrare**.

    ![register5][5]

    d. **Facoltativo**: è possibile **includere un'anteprima** e **aggiungere un esperto di origine dati**.

    e. In **Aggiungi tag** immettere la descrizione e la foto. Verrà aggiunto il tag di ricerca per questi asset di dati. I tag sono un modo eccezionale per consentire agli utenti di trovare un'origine dati registrata.
 
    f. Fare clic su **REGISTRA**. Nel Catalogo dati di Azure vengono registrati gli oggetti selezionati. In questo esercizio, vengono registrati gli oggetti selezionati di Adventure Works.

    ![register6][6]

    g. Per visualizzare gli oggetti origine dati registrati, fare clic su **Visualizza portale**. Nel portale del Catalogo dati di Azure, è possibile visualizzare gli oggetti origine dati in **riquadri** o in un **elenco**.

    ![register7][7]

In questo esercizio sono stati registrati gli oggetti del database di esempio Adventure Works in modo che possano essere facilmente individuati dagli utenti dell'organizzazione. Nel prossimo esercizio si apprende come individuare gli asset di dati registrati.

## Esercizio 3: individuazione di asset di dati registrati

In questo esercizio si utilizza il portale del Catalogo dati di Azure per individuare gli asset di dati registrati e visualizzare i relativi metadati. Il Catalogo dati di Azure fornisce più strumenti per l'individuazione degli asset di dati, tra cui la ricerca semplice per parola chiava, i filtri interattivi e una sintassi di ricerca avanzata per gli utenti di "avanzati".

### Ecco come individuare gli asset di dati registrati

Il **Catalogo dati di Azure** ha una sintassi semplice ma efficace che consente di creare con facilità le query che restituiscono i dati necessari agli utenti. Per informazioni dettagliate sul **Catalogo dati di Azure**, vedere il riferimento alla sintassi di ricerca.

Il **Catalogo dati di Azure** ha le seguenti opzioni di ricerca:

- Ricerca per parola chiave
- Filtro
- Ricerca avanzata

È inoltre possibile definire gli asset di dati da visualizzare. Il **Catalogo dati di Azure** ha le seguenti opzioni di visualizzazione:

- Visualizza proprietà
- Visualizza colonne
- Visualizza anteprima

Per questo esempio, si utilizzerà una ricerca per parola chiave. La ricerca del **Catalogo dati di Azure** dispone di diverse tecniche di query. In questo esempio viene utilizzato una query di ricerca **raggruppamento**.

**Tecniche di query**<table><tr><td><b>Tecnica</b></td><td><b>Utilizzo</b></td><td><b>Esempio</b></td></tr><tr><td>Ambito proprietà</td><td>Restituire solo le origini di dati in cui viene trovato il termine di ricerca corrispondente nella proprietà specificata</td><td>name:product</td></tr><tr><td>Operatori logici</td><td>Ampliare o limitare una ricerca utilizzando operazioni booleane, come descritto nella sezione Operatori booleani in questa pagina</td><td>finance NOT corporate</td></tr><tr><td>Raggruppamento con parentesi</td><td>Utilizzare le parentesi per raggruppare parti della query per ottenere l'isolamento logico , soprattutto in combinazione con operatori booleani</td><td>name:product AND (tags:illustration OR tags:photo)</td></tr><tr><td>Operatori di confronto</td><td>Utilizzare confronti diversi dall'uguaglianza per le proprietà che contengono dati di tipo data o numerici</td><td>creationTime:&gt;11/05/14</td></tr></table>

In questo esempio, eseguire una ricerca nel **raggruppamento** degli asset di dati dove name è product e tags è illustration o photo.

1.	Accedere al portale del **Catalogo dati di Azure**.
2.	Fare clic su **Individua**.
3.	Nella casella **Cerca** immettere una query di **raggruppamento**: (tags:description OR tags:photo). 
4.	Fare clic sull'icona di ricerca o premere INVIO. Nel **Catalogo dati di Azure** vengono visualizzati gli asset di dati per questa query di ricerca.
    
    ![cerca][8]

In questo esercizio è stato utilizzato il portale del **Catalogo dati si Azure** per individuare e visualizzare gli asset di dati registrati nel catalogo.

## Esercizio 4: annotazione delle origini dati registrate

In questo esercizio si utilizza il portale del **Catalogo dati di Azure** per annotare gli asset di dati che sono stati registrati in precedenza nel catalogo. Le annotazioni immesse andranno a integrare e migliorare i metadati strutturali estratti dall’origine dati durante la registrazione e sarà ancora più semplice individuare e comprendere gli asset di dati. Poiché ogni utente del **Catalogo dati** può fornire proprie annotazioni, è facile condividerle per tutti gli utenti con una prospettiva dei dati.

### Ecco come è possibile annotare gli asset di dati

1.	Accedere al portale del **Catalogo dati di Azure**.
2.	Fare clic su **Individua**.
3.	Scegliere uno o più **asset di dati**. In questo esempio, scegliere **ProductPhoto** e immettere "Foto di prodotti per materiale di marketing".
4.	Immettere una **descrizione** che consente ad altri di individuare e comprendere perché e come utilizzare l’asset di dati selezionato. Ad esempio, immettere "Immagini di prodotto". È inoltre possibile aggiungere ulteriori tag e visualizzare le colonne.
5.	Ora è possibile provare a usare la ricerca e il filtro per individuare gli asset di dati utilizzando i metadati descrittivi aggiunti al catalogo.

    ![annotare][9]

In questo esercizio sono state aggiunte informazioni descrittive agli asset di dati registrati in modo che gli utenti del catalogo possono individuare l'origine dati utilizzando termini comprensibili.

## Esercizio 5: crowdsourcing di metadati

In questo esercizio si utilizza un altro utente per aggiungere i metadati agli asset di dati nel catalogo. L’approccio crowdsourcing del Catalogo dati di Azure per le annotazioni consente agli utenti di aggiungere tag, descrizioni e altri metadati in modo che qualsiasi utente con una prospettiva su un asset di dati e sul relativo utilizzo possa rendere tale prospettiva acquisita e disponibile per altri utenti.

> [AZURE.NOTE]Se un altro utente non è disponibile per questa esercitazione, non è un problema. Qualsiasi utente che accede al catalogo dati può aggiungere la propria prospettiva quando sceglie di eseguire questa operazione. Questo approccio crowdsourcing per i metadati consente di incrementare nel tempo il contenuto del catalogo e la quantità di metadati del catalogo.

### Ecco come è possibile eseguire il crowdsourcing dei metadati relativi ad asset di dati

Chiedere a un collega di ripetere l’esercizio precedente **Annotazione delle origini dati registrate**. Dopo che il collega ha aggiunto una descrizione a un asset di dati, ad esempio ProductPhoto, verranno visualizzate più annotazioni.

In questo esercizio si sono utilizzate le funzionalità del Catalogo dati di Azure per i metadati crowdsourced, dove qualsiasi utente del catalogo può annotare gli asset di dati individuati. Esercizio: connessione alle origini dati In questo esercizio si utilizzerà il portale del **Catalogo dati di Azure** per la connessione alle origini dati utilizzando Microsoft Excel.

> [AZURE.NOTE]È importante ricordare che il **Catalogo dati di Azure** non consente agli utenti di accedere all'origine dati effettiva, ma ne semplifica per gli utenti l’individuazione e la comprensione. Quando gli utenti si connettono a un'origine dati, l'applicazione client scelta utilizza le credenziali di Windows o richiede le credenziali necessarie. Se l'utente non ha già ottenuto l'accesso all'origine dati, dovrà essere concesso l'accesso prima che possa connettersi.

### Di seguito viene illustrato come connettersi a un'origine dati da Excel

1.	Accedere al portale del **Catalogo dati di Azure**.
2.	Fare clic su **Individua**.
3.	Scegliere un asset di dati. In questo esempio, scegliere ProductCategory.
4.	Scegliere **Apri in** > **Excel**.

    ![connect1][10]
5.	Nella finestra **Avviso di sicurezza di Microsoft Excel**, fare clic su **Abilita**.
6.	Aprire il file **ProductCategory.odc**.
7.	L'origine dati viene aperta in Excel.

    ![connect2][11]

In questo esercizio ci si connette alle origini dati individuate tramite il Catalogo dati di Azure. Il portale del **Catalogo dati di Azure** consente agli utenti di connettersi direttamente tramite le applicazioni client integrate nel menu **Apri in...** e consente agli utenti di connettersi mediante qualsiasi applicazione scelta utilizzando le informazioni sul percorso di connessione incluse nei metadati dell’asset.

## Esercizio 6: rimozione dei metadati dell'origine dati

In questo esercizio si utilizzerà il portale del **Catalogo dati di Azure** per rimuovere i dati di anteprima dagli asset di dati registrati ed eliminare gli asset di dati dal catalogo.

> [AZURE.NOTE]Il comportamento predefinito del catalogo consente a tutti gli utenti di registrare qualsiasi origine dati e consente a qualsiasi utente di eliminare qualsiasi asset di dati registrato. Le funzionalità di gestione incluse nel **Catalogo dati di Azure Standard Edition** forniscono opzioni aggiuntive per acquisire la proprietà degli asset, limitando gli utenti che possono individuare ed eliminare gli asset.

Nel **Catalogo dati di Azure** è possibile rimuovere l’anteprima dall'eliminazione di un singolo asset o di più asset.

### Ecco come si eliminano più asset di dati

1.	Accedere al portale del **Catalogo dati di Azure**.
2.	Fare clic su **Individua**.
3.	Scegliere uno o più asset di dati.
4.	Fare clic su **Elimina**.

In questo esercizio sono stati rimossi asset di dati registrati dal catalogo.

## Esercizio 7: gestione delle origini dati registrate

In questo esercizio si utilizzerà la funzionalità di gestione del **Catalogo dati di Azure** per assumere la proprietà di asset di dati e per controllare quali utenti possono individuare e gestire tali asset.

Nota: le funzionalità di gestione descritte in questo esercizio sono disponibili solo nel Catalogo dati di Azure Standard Edition e non nella versione gratuita. Nel **Catalogo dati di Azure** è possibile assumere la proprietà di asset di dati, aggiungere coproprietari ad asset di dati e impostare la visibilità degli asset di dati.

### Ecco come si assume la proprietà di asset di dati si limita la visibilità

1.	Accedere al portale del **Catalogo dati di Azure**.
2.	Fare clic su **Individua**.
3.	Scegliere uno o più asset di dati.
4.	Nel pannello **Proprietà**, sezione **Gestione**, fare clic su **Assumi proprietà**.
5.	Per limitare la visibilità, fare clic su **Proprietari e utenti**.

    ![proprietà][12]

In questo esercizio si sono utilizzate le funzionalità di gestione del catalogo e si è limitata la visibilità di asset di dati selezionati.

## Riepilogo

In questa esercitazione sono state analizzate le funzionalità essenziali dell’anteprima del **Catalogo dati di Azure**, compresa la registrazione, l'annotazione, l’individuazione e la gestione di origini dati aziendali. Ora che è stata completata l'esercitazione, è possibile iniziare. È possibile iniziare subito registrando le origini dati utilizzate personalmente o dal team e invitando i colleghi a utilizzare il catalogo.


<!--Image references-->
[1]: ./media/data-catalog-get-started/register1.png
[2]: ./media/data-catalog-get-started/register2.png
[3]: ./media/data-catalog-get-started/register3.png
[4]: ./media/data-catalog-get-started/register4.png
[5]: ./media/data-catalog-get-started/register5.png
[6]: ./media/data-catalog-get-started/register6.png
[7]: ./media/data-catalog-get-started/register7.png
[8]: ./media/data-catalog-get-started/search.png
[9]: ./media/data-catalog-get-started/annotate.png
[10]: ./media/data-catalog-get-started/connect1.png
[11]: ./media/data-catalog-get-started/connect2.png
[12]: ./media/data-catalog-get-started/ownership.png

<!---HONumber=July15_HO5-->