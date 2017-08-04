## <a name="microsoft-open-source-code-of-conduct"></a>Codice di comportamento Open Source di Microsoft

Questo progetto ha adottato il [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) (Codice di comportamento Open Source di Microsoft).
Per altre informazioni, vedere [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) (Domande frequenti sul Codice di comportamento Open Source di Microsoft) oppure contattare [opencode@microsoft.com](mailto:opencode@microsoft.com) per eventuali altre domande o commenti.

## <a name="contribute-to-azure-technical-documentation"></a>Contributo alla documentazione tecnica di Azure
Microsoft accoglie con piacere i contributi della propria community, ovvero utenti, clienti, partner, dipendenti Microsoft all'esterno e all'interno delle principali unità di produzione Azure e così via. Il tipo di contributo varia a seconda del ruolo dell'utente:

* **Community - aggiornamenti minori**: se l'utente contribuisce creando degli aggiornamenti minori solo per il piacere di contribuire, è possibile consultare l'articolo in questo repository oppure all'indirizzo [https://docs.microsoft.com/azure](https://docs.microsoft.com/azure) e fare clic su **Modifica** nell'articolo che rimanda all'origine GitHub dell'articolo desiderato. Usare quindi solo l'interfaccia utente di GitHub per apportare le modifiche. In alternativa, è possibile creare un fork del repository e inviare gli aggiornamenti dal fork.

* **Community - nuovi articoli**: se l'utente fa parte della community di Azure e desidera creare un nuovo articolo, deve collaborare con un dipendente per poter organizzare il nuovo contenuto attraverso una collaborazione tra repository pubblico e privato.

* **Dipendenti**: se l'utente è redattore tecnico, responsabile del programma o sviluppatore del team di prodotto per un servizio di Azure e il suo compito è quello di contribuire o redigere articoli tecnici, deve usare il repository privato, ovvero https://github.com/MicrosoftDocs/azure-docs-pr. Se si apportano modifiche sostanziali a un articolo esistente, aggiungendo o modificando immagini oppure fornendo il proprio contributo con un nuovo articolo, è necessario creare una copia tramite fork di questo repository, installare Git Bash, un markdown editor, e apprendere alcuni comandi git. Per altre informazioni consultare la [guida per i collaboratori interni](https://review.docs.microsoft.com/en-us/help/contribute/?branch=master).


## <a name="about-your-contributions-to-azure-content"></a>Informazioni sui contributi al contenuto di Azure
### <a name="minor-corrections"></a>Correzioni secondarie
Le correzioni secondarie o i chiarimenti inviati per la documentazione e gli esempi di codice in questo repository sono coperti dalle [Condizioni per l'utilizzo di docs.microsoft.com](https://docs.microsoft.com/legal/termsofuse).

### <a name="larger-submissions"></a>Correzioni di impatto maggiore
Se si invia una richiesta pull con modifiche nuove o significative alla documentazione e agli esempi di codice, verrà visualizzato un commento in GitHub con la richiesta di inviare un contratto di licenza online per i contributi (Contribution License Agreement, CLA) se non si è dipendenti Microsoft. Prima che la richiesta pull venga accettata, è necessario completare il modulo online.

## <a name="tools-and-setup"></a>Strumenti e configurazione
I collaboratori della community possono usare l'interfaccia utente di GitHub o dividere il repository per offrire il proprio contributo. I dipendenti devono consultare la [guida per i collaboratori interni](https://review.docs.microsoft.com/en-us/help/contribute/?branch=master) per avere altre informazioni su come contribuire alla documentazione tecnica.

## <a name="repository-organization"></a>Organizzazione del repository
Il contenuto del repository azure-docs è organizzato in modo analogo alla documentazione disponibile in https://docs.microsoft.com/azure. Questo repository contiene due cartelle radice:

### <a name="articles"></a>\articles
La cartella *\articles* contiene gli articoli della documentazione formattati come file markdown con estensione *md*. Gli articoli vengono in genere raggruppati in base al servizio di Azure.

La cartella *\articles* contiene la cartella *\media* per i file multimediali degli articoli nella directory radice, che include le sottocartelle con le immagini per ogni articolo.  Le cartelle del servizio contengono una cartella di file multimediali separata per gli articoli all'interno di ogni cartella del servizio. Le cartelle di immagini degli articoli sono denominate esattamente come il file dell'articolo, esclusa l'estensione di file *MD* .

### <a name="includes"></a>\includes
È possibile creare sezioni di contenuto riutilizzabile da includere in uno o più articoli. 

## <a name="how-to-use-markdown-to-format-your-topic"></a>Come usare la sintassi markdown per formattare l'argomento
Tutti gli articoli in questo repository usano GitHub Flavored Markdown.  Ecco un elenco di risorse.

* [Nozioni di base sulla sintassi markdown](https://help.github.com/articles/markdown-basics/)
* [Foglio informativo relativo alla sintassi markdown in formato stampabile](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)


## <a name="labels"></a>Etichette
Nel repository pubblico azure-docs alle richieste pull vengono assegnate etichette automatizzate, che consentono di gestire il flusso di lavoro delle richieste pull e di conoscere lo stato della richiesta pull:

* Informazioni correlate al Contratto di Licenza per i contributi
  * cla-not-required: la modifica è relativamente minima e non necessita della firma di un Contratto di Licenza.
  * cla-required: l'ambito della modifica è relativamente ampio ed è necessaria la firma di un Contratto di Licenza .
  * cla-signed: il collaboratore ha firmato il Contratto di Licenza , quindi la richiesta pull può procedere alla verifica.
* Modifica inviata all'autore: l'autore è stato informato in merito alla richiesta pull in sospeso.
* ready-to-merge: pronto per la revisione dal team di revisione della richiesta pull.


