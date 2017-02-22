# <a name="azure-technical-documentation-contributor-guide"></a>Guida per i collaboratori alla documentazione tecnica di Azure
Questo è il repository GitHub in cui è disponibile l'origine della documentazione tecnica di Azure pubblicata in [https://docs.microsoft.com/azure](https://docs.microsoft.com/azure).

Questo repository include anche informazioni aggiuntive per chi volesse fornire il proprio contributo al miglioramento della documentazione tecnica. Per un elenco degli articoli della Guida per i collaboratori, vedere [l'indice](contributor-guide/contributor-guide-index.md).

## <a name="contribute-to-azure-documentation"></a>Contribuire alla documentazione di Azure
Grazie per l'interesse dimostrato nei confronti della documentazione di Azure.

* [Come fornire il proprio contributo](#ways-to-contribute)
* [Codice di comportamento](#code-of-conduct)
* [Informazioni sui contributi al contenuto di Azure](#about-your-contributions-to-azure-content)
* [Organizzazione del repository](#repository-organization)
* [Usare GitHub, Git e questo repository](#use-github-git-and-this-repository)
* [Come usare la sintassi markdown per formattare l'argomento](#how-to-use-markdown-to-format-your-topic)
* [Altre risorse](#more-resources)
* [Indice di tutti gli articoli della Guida per i collaboratori](contributor-guide/contributor-guide-index.md) (viene aperta una nuova pagina)

## <a name="ways-to-contribute"></a>Come fornire il proprio contributo
È possibile inviare gli aggiornamenti alla [documentazione di Azure](https://docs.microsoft.com/azure) come indicato di seguito:

* È possibile contribuire facilmente agli articoli tecnici nell'interfaccia utente di GitHub. Trovare l'articolo in questo repository oppure all'indirizzo [https://docs.microsoft.com/azure](https://docs.microsoft.com/azure) e fare clic sul collegamento presente nell'articolo che rimanda all'origine GitHub dell'articolo desiderato.
* Se si apportano modifiche sostanziali a un articolo esistente, aggiungendo o modificando immagini oppure fornendo il proprio contributo con un nuovo articolo, è necessario creare una copia tramite fork di questo repository, installare Git Bash, Markdown Pad e apprendere alcuni comandi git.

## <a name="code-of-conduct"></a>Codice di comportamento
Questo progetto ha adottato il [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) (Codice di comportamento Open Source di Microsoft). Per altre informazioni, vedere [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) (Domande frequenti sul Codice di comportamento Open Source di Microsoft) oppure contattare [opencode@microsoft.com](mailto:opencode@microsoft.com) per eventuali altre domande o commenti.

## <a name="about-your-contributions-to-azure-content"></a>Informazioni sui contributi al contenuto di Azure
### <a name="minor-corrections"></a>Correzioni secondarie
Le correzioni secondarie o i chiarimenti inviati per la documentazione e gli esempi di codice in questo repository sono coperti dalle [Condizioni per l'utilizzo di docs.microsoft.com](https://docs.microsoft.com/legal/termsofuse).

### <a name="larger-submissions"></a>Correzioni di impatto maggiore
Se si invia una richiesta pull con modifiche nuove o significative alla documentazione e agli esempi di codice, verrà visualizzato un commento in GitHub con la richiesta di inviare un contratto di licenza online per i contributi (Contribution License Agreement, CLA) se non si è dipendenti Microsoft. Prima che la richiesta pull venga accettata, è necessario completare il modulo online.

## <a name="repository-organization"></a>Organizzazione del repository
Il contenuto del repository azure-docs è organizzato in modo analogo alla documentazione disponibile in https://docs.microsoft.com/azure. Questo repository contiene due cartelle radice:

### <a name="articles"></a>\articles
La cartella *\articles* contiene gli articoli della documentazione formattati come file markdown con estensione *md*. Gli articoli vengono in genere raggruppati in base al servizio di Azure.

Gli articoli devono rispettare convenzioni di denominazione molto rigorose. Per informazioni dettagliate, vedere le [convenzioni di denominazione dei file](contributor-guide/file-names-and-locations.md).

La cartella *\articles* contiene la cartella *\media* per i file multimediali degli articoli nella directory radice, che include le sottocartelle con le immagini per ogni articolo.  Le cartelle del servizio contengono una cartella di file multimediali separata per gli articoli all'interno di ogni cartella del servizio. Le cartelle di immagini degli articoli sono denominate esattamente come il file dell'articolo, esclusa l'estensione di file *MD* .

### <a name="includes"></a>\includes
È possibile creare sezioni di contenuto riutilizzabile da includere in uno o più articoli. Vedere [Custom extensions used in our technical content](contributor-guide/custom-markdown-extensions.md) (Estensioni personalizzate usate nel contenuto tecnico).

### <a name="markdown-templates"></a>\markdown templates
Questa cartella contiene il modello markdown standard con la formattazione markdown di base necessaria per un articolo.

### <a name="contributor-guide"></a>\contributor-guide
Questa cartella contiene gli articoli che fanno parte della Guida per i collaboratori.

## <a name="use-github-git-and-this-repository"></a>Usare GitHub, Git e questo repository
Per informazioni su come fornire il proprio contributo, usare l'interfaccia utente di GitHub per contribuire con piccole modifiche, creare una copia tramite fork del repository e clonarlo per i contributi più significativi, vedere [Install and set up tools for authoring in GitHub](contributor-guide/tools-and-setup.md) (Installare e configurare gli strumenti per la creazione in GitHub).

Se si installa GitBash e si sceglie di lavorare in locale, la procedura per creare un nuovo ramo di lavoro locale, apportare modifiche e rinviare le modifiche al ramo principale è descritta in [Git commands for creating a new article or updating an existing article](contributor-guide/git-commands-for-master.md) (Comandi Git per la creazione di un nuovo articolo o l'aggiornamento di un articolo esistente).

### <a name="branches"></a>Rami
È consigliabile creare rami di lavoro locali destinati a un ambito di modifica specifico. Ogni ramo deve essere limitato a un singolo concetto o articolo, sia per ottimizzare il flusso di lavoro che per ridurre la possibilità di conflitti di unione.  L'ambito delle attività seguenti è appropriato per un nuovo ramo:

* Nuovo articolo e immagini associate.
* Modifiche di ortografia e grammatica in un articolo.
* Applicazione di una singola modifica di formattazione per un ampio set di articoli, ad esempio un nuovo piè di pagina con il copyright.

## <a name="how-to-use-markdown-to-format-your-topic"></a>Come usare la sintassi markdown per formattare l'argomento
Tutti gli articoli in questo repository usano GitHub Flavored Markdown.  Ecco un elenco di risorse.

* [Nozioni di base sulla sintassi markdown](https://help.github.com/articles/markdown-basics/)
* [Foglio informativo relativo alla sintassi markdown in formato stampabile](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

## <a name="article-metadata"></a>Metadati dell'articolo
I metadati dell'articolo abilitano determinate funzionalità, ad esempio attribuzione dell'autore e del collaboratore, percorsi di navigazione, descrizioni di articoli e ottimizzazioni del motore di ricerca, nonché la creazione di report usati da Microsoft per valutare le prestazioni del contenuto. I metadati sono quindi importanti. [Ecco le informazioni aggiuntive per verificare che i metadati siano corretti](contributor-guide/article-metadata.md).

### <a name="labels"></a>Etichette
Alle richieste pull vengono assegnate etichette automatizzate, che consentono di gestire il flusso di lavoro delle richieste pull e di segnalare all'utente lo stato della richiesta pull:

* Informazioni correlate al Contratto di Licenza per i contributi
  * cla-not-required: la modifica è relativamente minima e non necessita della firma di un Contratto di Licenza.
  * cla-required: l'ambito della modifica è relativamente ampio ed è necessaria la firma di un Contratto di Licenza .
  * cla-signed: il collaboratore ha firmato il Contratto di Licenza , quindi la richiesta pull può procedere alla verifica.
* Etichette Pillar: le etichette di tipo PnP, App moderne e TDC consentono di classificare le richieste pull in base all'organizzazione interna che deve verificare la richiesta pull.
* Modifica inviata all'autore: l'autore è stato informato in merito alla richiesta pull in sospeso.

## <a name="more-resources"></a>Altre risorse
Per un elenco di tutti gli argomenti, vedere l'[indice della Guida per i collaboratori](contributor-guide/contributor-guide-index.md).



<!--HONumber=Feb17_HO2-->


