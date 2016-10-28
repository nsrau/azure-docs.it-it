<properties 
	pageTitle="Come usare i profili di assegnazione dei punteggi in Ricerca di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato" 
	description="Ottimizzare le priorità di ricerca tramite i profili di punteggio in Ricerca di Azure, un servizio di ricerca ospitato sul cloud in Microsoft Azure." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="08/04/2016" 
	ms.author="heidist"/>

# Come usare i profili di assegnazione dei punteggi in Ricerca di Azure

Il profilo di punteggio è una funzionalità di Ricerca di Microsoft Azure che personalizza il calcolo dei punteggi di ricerca, influenzando il modo in cui gli elementi vengono classificati in un elenco di risultati della ricerca. Il profilo di punteggio può essere considerato un modo per modellare la rilevanza, assegnando una priorità maggiore agli elementi che soddisfano criteri predefiniti. Si supponga ad esempio che l'applicazione sia un sito per prenotazioni alberghiere. Promuovendo il `location` ricerche che includono un termine come Seattle comporterà un punteggio più alto per gli elementi che dispongono di Seattle in campo la `location` campo. Si noti che è possibile usare più profili di punteggio oppure non usarne nessuno, se i punteggi predefiniti sono sufficienti per l'applicazione specifica.

Per provare a usare il profilo di punteggio, è possibile scaricare un'applicazione di esempio che usa i profili di punteggio per cambiare l'ordine di classificazione dei risultati della ricerca. L'esempio è costituito da un'applicazione console, probabilmente non molto realistica per lo sviluppo di applicazioni nel mondo reale, ma utile comunque come strumento di apprendimento.

L'applicazione di esempio viene illustrato il punteggi comportamenti utilizzando dati fittizi, denominato il `musicstoreindex` La semplicità dell'app di esempio permette di modificare con facilità i profili di punteggio e le query e di verificare l'effetto immediato sull'ordine di classificazione quando si esegue il programma.

<a id="sub-1"></a>
## Prerequisiti

L'applicazione di esempio è scritto in c# utilizzando Visual Studio 2013. Provare il prodotto gratuito [Visual Studio 2013 Express edition](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) se non si dispone di una copia di Visual Studio.

Per completare l'esercitazione è necessario avere una sottoscrizione di Azure e un servizio di ricerca di Azure. Vedere [creare un servizio di ricerca nel portale di](search-create-service-portal.md) per informazioni sulla configurazione del servizio.

[AZURE.INCLUDE [È necessario un account Azure per completare questa esercitazione:](../../includes/free-trial-note.md)]

<a id="sub-2"></a>
## Scaricare l'applicazione di esempio

Andare a [Ricerca di Azure punteggio profili Demo](https://azuresearchscoringprofiles.codeplex.com/) su codeplex per scaricare l'applicazione di esempio descritta in questa esercitazione.

Scegliere la scheda del codice sorgente, **Download** per ottenere un file zip della soluzione.

 ![][12]

<a id="sub-3"></a>
## Modificare il file app.config

1. Dopo l'estrazione dei file, aprire la soluzione in Visual Studio per modificare il file di configurazione.
1. In Esplora soluzioni fare doppio clic su **app. config**. Questo file specifica l'endpoint del servizio e un `api-key` utilizzato per autenticare la richiesta. È possibile ottenere questi valori dal portale classico.
1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare al dashboard del servizio per Ricerca di Azure.
1. Scegliere il **proprietà** riquadro per copiare l'URL del servizio
1. Fare clic sui **chiavi** riquadro per copiare la `api-key`.

Dopo aver terminato di aggiungere l'URL e `api-key` in App. config, le impostazioni dell'applicazione dovrebbero essere simile al seguente:e:

   ![][11]


<a id="sub-4"></a>
## Esplorare l'applicazione

Si è quasi pronti per compilare ed eseguire l'app, ma prima di procedere è consigliabile esaminare i file JSON usati per creare e popolare l'indice.

**Schema.json** definisce l'indice, inclusi i profili di assegnazione dei punteggi vengono evidenziati in questa demo. Si noti che lo schema definisce tutti i campi utilizzati nell'indice, inclusi i campi non è possibile eseguire ricerche, ad esempio `margin`, che è possibile utilizzare in un profilo di assegnazione dei punteggi. Sintassi del profilo di punteggio è documentato in [aggiungere un profilo di assegnazione dei punteggi a un indice di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn798928.aspx).

**Data1 3.json** fornisce i dati, gli 246 album tra una vasta gamma di generi. I dati sono una combinazione di informazioni di album e artista effettivi, con campi fittizi come `price` e `margin` utilizzate per illustrare le operazioni di ricerca. I file di dati sono conformi all'indice e vengono caricati nel servizio di ricerca di Azure. Dopo il caricamento e l'indicizzazione dei dati, sarà possibile eseguire query sui dati.

**Program.cs** esegue le operazioni seguenti:

- Apertura della finestra della console.

- Si connette alla Ricerca di Azure utilizzando l'URL del servizio e `api-key`.

- Elimina il `musicstoreindex` se esistente.

- Crea un nuovo `musicstoreindex` utilizzando il file schema.json.

- Popolamento dell'indice mediante i file di dati.

- Esecuzione di query nell'indice tramite quattro query. Si noti che i profili di punteggio sono specificati come parametro di query. Tutte le query cercano lo stesso termine, 'best'. La prima query illustra l'assegnazione di punteggi predefiniti. Le tre query rimanenti usano un profilo di punteggio.

<a id="sub-5"></a>
## Compilare ed eseguire l'applicazione

Per evitare problemi di connettività o di riferimento ad assembly, compilare ed eseguire prima di tutto l'applicazione per assicurarsi che non siano presenti problemi da risolvere. Un'applicazione console dovrebbe aprirsi in background. Tutte le quattro query vengono eseguite in sequenza, senza pause. In molti sistemi l'intero programma viene eseguito in meno di 15 secondi. Se l'applicazione console include un messaggio analogo a "Operazione completata. Premere INVIO per continuare", l'esecuzione del programma è stata completata correttamente.

Per confrontare le esecuzioni delle query, è possibile contrassegnare e copiare i risultati delle query dalla console, quindi incollarli in un file di Excel.

La figura seguente mostra i risultati affiancati delle prime tre query. Tutte le query usano lo stesso termine di ricerca, 'best', che compare in molti titoli di album.

   ![][10]

La prima query usa il profilo di punteggio. Poiché il termine di ricerca è presente solo nei titoli degli album e non sono stati specificati altri criteri, gli elementi con 'best' nel titolo dell'album verranno restituiti nell'ordine in cui vengono rilevati dal servizio di ricerca.

La seconda query usa un profilo di punteggio, ma, come si può notare, il profilo non ha avuto alcun effetto. I risultati sono identici a quelli della prima query. Il profilo di punteggio, infatti, assegna una priorità maggiore a un campo ('genre') non pertinente alla query. Il termine di ricerca 'best' non esiste in alcun campo 'genre' di alcun documento. Quando un profilo di punteggio non ha alcun effetto, i risultati corrispondono a quelli dell'assegnazione di punteggi predefiniti.

La terza query è la prima dimostrazione dell'effetto del profilo di punteggio. Il termine di ricerca è ancora 'best', quindi viene usato lo stesso insieme di album. Poiché tuttavia il profilo di punteggio fornisce criteri aggiuntivi che assegnano una priorità maggiore a 'rating' e 'last-updated', alcuni elementi compaiono più in alto nell'elenco.

La figura seguente mostra la quarta e ultima query, con priorità incrementata da 'margin'. Il campo 'margin' non supporta la ricerca e non può essere restituito nei risultati della ricerca. Il valore 'margin' è stato aggiunto manualmente al foglio di calcolo per illustrare il concetto in base al quale gli elementi con margini superiori vengono visualizzati nelle posizioni iniziali dell'elenco di risultati della ricerca.

   ![][9]

Dopo avere provato a usare i profili di punteggio, è possibile provare a modificare il programma, in modo da usare sintassi di query e profili di punteggio diversi o dati più complessi. Per altre informazioni, vedere i collegamenti nella sezione seguente.

<a id="next-steps"></a>
## Passaggi successivi

Altre informazioni sui profili di punteggio. Vedere [aggiungere un profilo di assegnazione dei punteggi a un indice di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn798928.aspx) per informazioni dettagliate.

Altre informazioni sulla sintassi di ricerca e sui parametri di query. Vedere [ricerca documenti (API REST di Ricerca di Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx) per informazioni dettagliate.

Per altre informazioni sulla creazione degli indici, [Guardare questo video](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh) per comprendere le nozioni di base.

<!--Anchors-->
[Prerequisites]: #sub-1
[Download the sample application]: #sub-2
[Edit app.config]: #sub-3
[Explore the application]: #sub-4
[Build and run the application]: #sub-5
[Next steps]: #next-steps

<!--Image references-->
[12]: ./media/search-get-started-scoring-profiles/AzureSearch_CodeplexDownload.PNG
[11]: ./media/search-get-started-scoring-profiles/AzureSearch_Scoring_AppConfig.PNG
[10]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX1.PNG
[9]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX2.PNG

<!---HONumber=AcomDC_0907_2016-->