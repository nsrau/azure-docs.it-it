<properties
	pageTitle="Guida introduttiva: API Recommendations di Machine Learning | Microsoft Azure"
	description="Recommendations di Azure Machine Learning - Guida introduttiva"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/26/2016"
	ms.author="luisca"/>

# Guida introduttiva per l'API Recommendations dei servizi cognitivi

Questo documento illustra come configurare il servizio o l'applicazione per l'uso dell'[API Recommendations](http://go.microsoft.com/fwlink/?LinkId=759710). È possibile trovare altre informazioni sull'API Recommendations e sui servizi cognitivi [qui](http://go.microsoft.com/fwlink/?LinkId=759709). In questa guida, è anche possibile trovare un pratico [riferimento per l'API Recommendations](http://go.microsoft.com/fwlink/?LinkId=759348).


<a name="Overview"></a>
## Panoramica generale

Questo argomento costituisce una guida dettagliata. L'obiettivo è illustrare in dettaglio i passaggi necessari per eseguire il training di un modello e indicare le risorse che consentiranno di usare il modello dall'ambiente di produzione.

Questo esercizio richiederà circa 30 minuti.

Per usare l'[API Recommendations](http://go.microsoft.com/fwlink/?LinkId=759710), è necessario eseguire le operazioni seguenti:

1. Creare un modello: un modello è un contenitore per dati di utilizzo, dati del catalogo e modello di raccomandazione.
1. Importare dati del catalogo: un catalogo contiene informazioni sui metadati relativi agli elementi.
1. Importare i dati di utilizzo: i dati di utilizzo possono essere caricati in uno dei due modi seguenti (o entrambi):
  -  Caricando un file contenente i dati di utilizzo.
  -  Inviare eventi di acquisizione dei dati. In genere, si carica un file dei dati di utilizzo per poter creare un modello di raccomandazione iniziale (bootstrap) e usarlo finché il sistema non raccoglie abbastanza dati usando il formato di acquisizione dei dati.
1. Compilare un modello di raccomandazione: si tratta di un'operazione asincrona in cui il sistema di raccomandazione accetta tutti i dati di utilizzo e crea un modello di raccomandazione. Questa operazione può richiedere diversi minuti o diverse ore a seconda delle dimensioni dei dati e dei parametri di configurazione della build. Quando si attiva la compilazione, si ottiene un ID compilazione. Usarlo per verificare se il processo di compilazione è terminato prima di iniziare a usare raccomandazioni.
1. Utilizzare le raccomandazioni: ottenere raccomandazioni per un elemento o un elenco di elementi specifico.

<a name="GetStarted"></a>
### Ecco come procedere.

Sarà possibile iniziare la creazione di un modello di raccomandazioni. Forniremo guida su come usare i risultati generati dal modello per implementare le raccomandazioni in un sito di e-commerce.

<a name="Ex1Task1"></a>
#### Attività 1 - Iscrizione all'API Recommendations ####

In questa attività verranno effettuata l'iscrizione al servizio API Recommendations e verrà creato un modello di raccomandazioni.

1. Andare al **portale di Azure** tramite l'indirizzo [http://portal.azure.com/](http://portal.azure.com/) e accedere con il proprio account Azure.

1.  Fare clic su **+ Nuovo**.

1. Selezionare l'opzione **Intelligence**.

1. Selezionare il prodotto **API dei servizi cognitivi**. Questo prodotto consente di avviare una sottoscrizione per le API dei servizi cognitivi (Viso, Analisi del testo, Visione artificiale e così via). Oggi ci concentreremo sull'API Recommendations.

1. Nella pagina di destinazione dell'API dei servizi cognitivi, immettere il **nome account** per la sottoscrizione a Recommendations. (Ad esempio: "MyRecommendations"). Il nome non deve contenere spazi.

1. In **tipo API**, selezionare **Raccomandazioni**.

1. In **Piano tariffario**, è possibile selezionare un piano. È possibile selezionare il livello **Gratuito** per 10.000 transazioni al mese**. Si tratta di un piano gratuito, pertanto è un buon modo per iniziare a provare il sistema. Quando si passa alla produzione, è consigliabile prendere in considerazione il volume di richiesta e modificare di conseguenza il tipo di piano.

1. Selezionare un **Gruppo di risorse** se esistente o crearne uno nuovo.

1. È possibile modificare gli altri elementi nella finestra di dialogo Crea. Si noti che il provider di risorse attualmente è supportato solo dai data center degli Stati Uniti.

1. Dopo aver completato le selezioni, fare clic su **Crea**.

1. Attendere alcuni minuti per la distribuzione della risorsa. Una volta distribuita, è possibile passare alla sezione **Chiavi** nel pannello **Impostazioni** in cui verrà fornita una chiave primaria e una secondaria per usare l'API. Copiare la chiave primaria, poiché sarà utile durante la creazione del primo modello.

<a name="Ex1Task2"></a>
#### Attività 2 - Si dispone dei dati? ####

L'API Recommendations apprenderà dal catalogo e dalle transazioni per fornire consigli utili sui prodotti. Ciò significa che è necessario immettere dati corretti sui propri prodotti (denominiamolo file del **catalogo**) e un set di transazioni con dimensioni sufficienti per poter trovare modelli interessanti sul consumo (denominiamolo **utilizzo**).

1. Generalmente, è necessario genere una query del database di transazioni per questo genere di informazioni. Se non si dispone dei dati, forniamo alcuni dati di esempio basati sui dati delle transazioni di Microsoft Store.

 I dati sono disponibili per il download [qui](http://aka.ms/RecoSampleData). Copiare e decomprimere il file MsStoreData.Zip in una cartella sul computer locale.

 > **Nota: **il codice di esempio che si scarica e si esegue nell'Attività 3 contiene dati di esempio già incorporati, pertanto questa operazione è facoltativa. Questa attività consente di scaricare il set di dati più realistico e consente di comprendere meglio gli input all'API Recommendations.

1.	Ora esaminiamo il file del catalogo. Passare alla posizione in cui sono stati copiati i dati. Aprire il file del catalogo nel **Blocco note**.

 Si noterà che il file del catalogo è piuttosto semplice e si presenta con il seguente formato: `<itemid>,<item name>,<product category>`

 >  AAA-04294,OfficeLangPack 2013 32/64 E34 Online DwnLd,Office <br> AAA-04303,OfficeLangPack 2013 32/64 ET Online DwnLd,Office <br> C9F-00168,KRUSELL Kiruna Flip Cover for Nokia Lumia 635 - Camel,Accessories

 Si noti che un file di catalogo può essere molto più ampio, ad esempio è possibile aggiungere i metadati sui prodotti (denominiamoli *funzioni dell'elemento*). Verrà visualizzata la sezione [formato catalogo](http://go.microsoft.com/fwlink/?LinkID=760716) nel riferimento API per altre informazioni sul formato del catalogo.

1. Procediamo nello stesso modo per i dati di utilizzo. Si noterà che i dati di utilizzo sono nel formato `<User Id>,<Item Id>,<Time Stamp>,<Event>`.

  > 00037FFEA61FCA16,288186200,2015/08/04T11:02:52,Purchase 0003BFFDD4C2148C,297833400,2015/08/04T11:02:50,Purchase 0003BFFDD4C2118D,297833300,2015/08/04T11:02:40,Purchase 00030000D16C4237,297833300,2015/08/04T11:02:37,Purchase 0003BFFDD4C20B63,297833400,2015/08/04T11:02:12,Purchase 00037FFEC8567FB8,297833400,2015/08/04T11:02:04,Purchase

Si noti che i primi tre elementi sono obbligatori. Il tipo di evento è facoltativo. È possibile ottenere altre informazioni sul [formato di utilizzo](http://go.microsoft.com/fwlink/?LinkID=760712).

 > **Quanti dati sono necessari?**
 <p>
>  Dunque, in realtà dipende dagli stessi dati di utilizzo. Il sistema apprende quando gli utenti acquistano elementi diversi. Per alcune compilazioni come FBT, è importante sapere quali elementi vengono acquistati nelle stesse transazioni (denominiamoli *co-occorrenze*). Una buona regola consiste nella distribuzione della maggior parte degli elementi in almeno 20 transazioni, pertanto se si dispone di 10.000 elementi nel catalogo, è consigliabile disporre di almeno 20 volte il numero di transazioni o circa 200.000 transazioni. Si tratta comunque di una regola generale. È necessario provare a utilizzare i dati.
> </p>

<a name="Ex1Task3"></a>
#### Attività 3 - Creazione di un modello di raccomandazioni ####

Ora che si dispone di un account e dei dati, creiamo il primo modello.

In questa attività, si utilizzerà l'applicazione di esempio per creare il primo modello.

1. Prima di tutto è necessario aver consultato il [riferimento per l'API Consigli](http://go.microsoft.com/fwlink/?LinkId=759348).

1. Scaricare l'[applicazione di esempio](http://go.microsoft.com/fwlink/?LinkID=759344) in una cartella locale.

1. Aprire la soluzione **RecommendationsSample.sln** che si trova nella cartella **c#** con Visual Studio.

1. Aprire il file **SampleApp.cs**. Si noti che i seguenti passaggi nel file:
 + Creare il modello
 + Aggiungono un file del catalogo
 + Aggiungono un file di dati di utilizzo
 + Attivano la creazione di un modello
 + Creare una raccomandazione su una coppia di elementi
<p></p>

1. Sostituire il valore per il **campo AccountKey** con la chiave dell'attività 1.

1. Esaminare la soluzione e verranno visualizzate le modalità di creazione di un modello.

1. Provare a sostituire i file del catalogo e dei dati di utilizzo scaricati per creare un nuovo modello per Microsoft Store o per le raccomandazioni del manuale. È necessario modificare anche il nome del modello e gli elementi per cui si richiedono le raccomandazioni.

1. Dopo aver creato il modello, prendere nota dell'**ID modello** poiché sarà necessario per la richiesta di consigli nell'ambiente di produzione.

>  Altre informazioni sui tipi di compilazione e su come valutare la qualità delle compilazioni sono disponibili [qui](cognitive-services-recommendations-buildtypes.md).

<a name="Ex1Task4"></a>
### Mettere il modello in funzione. ###

Dopo aver appreso come creare un modello e usare le raccomandazioni, il passaggio successivo consiste nella messa in funzione sul proprio sito Web e sulle applicazioni per dispositivi mobili o nell'integrazione nel sistema CRM o ERP. Ovviamente, ognuna di queste implementazioni è differente. Poiché le API Recommendations sono richieste come servizio Web, sarà facilmente possibile richiamarle da qualsiasi di questi ambienti diversi.

**Importante:** è consigliabile creare un server proxy per visualizzare gli elementi consigliati da un client pubblico, ad esempio il proprio sito di e-commerce. Questo è importante per non esporre la chiave API a entità esterne (potenzialmente non attendibili).

Di seguito sono riportati alcuni suggerimenti delle posizioni in cui è possibile usare le raccomandazioni:

### Pagina del prodotto

**Raccomandazioni sugli elementi**
<p>Se il modello è stato sottoposto al training sui dati di acquisto, il cliente potrà *individuare i prodotti particolarmente interessanti per gli utenti che hanno acquistato l'elemento di origine*.</p>
<p>Se il modello è stato sottoposto al training sui dati di clic, il cliente potrà *individuare i prodotti che presentano la probabilità maggiore di essere visitati dagli utenti che hanno visitato l'elemento di origine*. Questo tipo di modello può restituire elementi analoghi.</p>

**Raccomandazioni "Spesso acquistati insieme"**
<p>La compilazione "Spesso acquistati insieme" non può essere sottoposta a training, è possibile pertanto ottenere set di elementi che con molta probabilità verranno acquistati insieme a questo elemento.</p>

### Pagina di check out

**Raccomandazioni sugli elementi**
<p>Un modello di raccomandazione può accettare come input un elenco di elementi. Pertanto, è possibile passare tutti gli elementi nel carrello come input per ottenere le raccomandazioni. In questo caso, il modello fornirà raccomandazioni di interesse sulla base di tutti gli elementi nel carrello.
</p>

### Pagina di destinazione

**Raccomandazioni sugli utenti**
<p>
Un modello di raccomandazione può accettare come input un id utente. La cronologia delle transazioni per tale utente verrà utilizzata per fornire raccomandazioni personalizzate all'utente specificato.
</p>

Vedere la [documentazione su come ottenere gli elementi consigliati](http://go.microsoft.com/fwlink/?LinkID=760719).

<a name="Ex1Task6"></a>
### Passaggi successivi
Congratulazioni per i passaggi effettuati fino ad ora! Per altre informazioni, vedere la versione integrale delle [informazioni di riferimento sull'API Recommendations](http://go.microsoft.com/fwlink/?LinkId=759348). In caso di domande contattare Microsoft all'indirizzo mlapi@microsoft.com

<!---HONumber=AcomDC_0601_2016-->