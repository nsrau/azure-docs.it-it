# Guida per i collaboratori alla documentazione tecnica di Azure

Questo è il repository GitHub in cui è disponibile l'origine della documentazione tecnica pubblicata nel Centro documentazione di Azure all'indirizzo [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation).

Questo repository include anche informazioni aggiuntive per chi volesse fornire il proprio contributo al miglioramento della documentazione tecnica. Per un elenco degli articoli della Guida per i collaboratori, vedere [l'indice](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).

## Contribuire alla documentazione di Azure

Grazie per l'interesse dimostrato nei confronti della documentazione di Azure.

* [Come fornire il proprio contributo](#ways-to-contribute)
* [Informazioni sui contributi al contenuto di Azure](#about-your-contributions-to-azure-content)
* [Organizzazione del repository](#repository-organization)
* [Usare GitHub, Git e questo repository](#use-github-git-and-this-repository)
* [Come usare markdown per formattare l'argomento](#how-to-use-markdown-to-format-your-topic)
* [Altre risorse](#more-resources)
* [Indice di tutti gli articoli della Guida per i collaboratori](./contributor-guide/contributor-guide-index.md) (viene aperta una nuova pagina)

## Come fornire il proprio contributo

È possibile contribuire facilmente agli articoli tecnici nell'interfaccia utente di GitHub. Trovare l'articolo in questo repository oppure all'indirizzo [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation) e fare clic sul collegamento presente nell'articolo che rimanda all'origine GitHub dell'articolo desiderato.

##Informazioni sui contributi al contenuto di Azure

###Correzioni secondarie

Le correzioni secondarie o i chiarimenti inviati per la documentazione e gli esempi di codice in questo repository sono coperti dalle [Condizioni per l'utilizzo del sito Web di Azure](http://azure.microsoft.com/support/legal/website-terms-of-use/).


###Invii di dimensioni maggiori

Se si invia una richiesta pull con modifiche nuove o significative alla documentazione e agli esempi di codice, verrà visualizzato un commento in GitHub con la richiesta di inviare un contratto di licenza online per i contributi, se si fa parte di uno di questi gruppi:

* Membri del gruppo Microsoft Open Technologies.
* Collaboratori che non lavorano per Microsoft.

Prima che la richiesta pull venga accettata, è necessario completare il modulo online.

Informazioni dettagliate sono disponibili all'indirizzo [http://azure.github.io/guidelines.html#cla](http://azure.github.io/guidelines.html#cla).

## Organizzazione del repository

Il contenuto del repository azure-content è organizzato in modo analogo alla documentazione disponibile in [Azure.Microsoft.com](http://azure.microsoft.com). Questo repository contiene due cartelle radice:

### \articles

La cartella *\articles* contiene gli articoli della documentazione formattati come file markdown con estensione *MD*.

Gli articoli nella directory radice sono pubblicati in Azure.Microsoft.com nel percorso *http://azure.microsoft.com/documentation/articles/{article-name-without-md}/*.

* **Nomi file degli articoli:** vedere le [informazioni aggiuntive sulla denominazione dei file](./contributor-guide/file-names-and-locations.md).

Gli articoli nella relativa cartella del servizio sono pubblicati in Azure.Microsoft.com nel percorso 
*http://azure.microsoft.com/documentation/articles/service-folder/{article-name-without-md}/*

* **Sottocartelle di contenuto multimediale:** la cartella *\articles* contiene la cartella *\media* per i file multimediali degli articoli nella directory radice, che include le sottocartelle con le immagini per ogni articolo. Le cartelle del servizio contengono una cartella di file multimediali separata per gli articoli all'interno di ogni cartella del servizio. Le cartelle di immagini degli articoli sono denominate esattamente come il file dell'articolo, tranne l'estensione di file *MD*.

### \includes

È possibile creare sezioni di contenuto riutilizzabile da includere in uno o più articoli. Vedere la sezione relativa alle [estensioni personalizzate usate nel contenuto tecnico](./contributor-guide/custom-markdown-extensions.md).

### \markdown templates

Questa cartella contiene il modello markdown standard con la formattazione markdown di base necessaria per un articolo.

### \contributor-guide

Questa cartella contiene gli articoli che fanno parte della Guida per i collaboratori.

## Usare GitHub, Git e questo repository

Per informazioni su come fornire il proprio contributo, usare l'interfaccia utente di GitHub per contribuire con piccole modifiche e creare un fork del repository e clonarlo per i contributi più significativi, vedere l'articolo che illustra come [installare e configurare gli strumenti per la creazione in GitHub](./contributor-guide/tools-and-setup.md).

Se si installa GitBash e si sceglie di lavorare in locale, la procedura per creare un nuovo ramo di lavoro locale, apportare modifiche e rinviare le modifiche al ramo principale è descritta nell'articolo relativo ai [comandi Git per la creazione di un nuovo articolo o l'aggiornamento di un articolo esistente](./contributor-guide/git-commands-for-master.md)

### Rami

È consigliabile creare rami di lavoro locali destinati a un ambito di modifica specifico. Ogni ramo deve essere limitato a un singolo concetto o articolo, sia per ottimizzare il flusso di lavoro che per ridurre la possibilità di conflitti di unione. L'ambito delle attività seguenti è appropriato per un nuovo ramo:

* Nuovo articolo e immagini associate.
* Modifiche di ortografia e grammatica in un articolo.
* Applicazione di una singola modifica di formattazione per un ampio set di articoli, ad esempio un nuovo piè di pagina con il copyright.

## Come usare markdown per formattare l'argomento

Tutti gli articoli in questo repository usano GitHub Flavored Markdown (GFM). Ecco un elenco di risorse.

- [Nozioni di base su markdown](https://help.github.com/articles/markdown-basics/)

- [Foglio informativo stampabile relativo a markdown](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

- Per un elenco di editor di markdown, vedere l'[argomento su strumenti e installazione](./contributor-guide/tools-and-setup.md#install-a-markdown-editor).

## Metadati dell'articolo

I metadati dell'articolo abilitano determinate funzionalità nel sito Web azure.microsoft.com, ad esempio attribuzione dell'autore e del collaboratore, percorsi, descrizioni di articoli e ottimizzazioni motore di ricerca, nonché segnalazione degli usi da parte di Microsoft per valutare le prestazioni del contenuto. I metadati sono quindi importanti. [Ecco le informazioni aggiuntive per verificare che i metadati siano configurati correttamente](./contributor-guide/article-metadata.md).

## Altre risorse

Per un elenco di tutti gli argomenti, vedere l'[indice della Guida per i collaboratori](./contributor-guide/contributor-guide-index.md).

<!---HONumber=AcomDC_0307_2016-->
