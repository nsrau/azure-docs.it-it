---
title: 'Esempio: Per iniziare - API Knowledge Exploration Service'
titlesuffix: Azure Cognitive Services
description: Usare L'API Knowledge Exploration Service (KES) per creare un motore per un'esperienza di ricerca interattiva all'interno di pubblicazioni accademiche.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: sample
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: e2bb5550cfe07064d595151305955d87f9c61050
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55819536"
---
# <a name="get-started-with-the-knowledge-exploration-service"></a>Introduzione a Knowledge Exploration Service

In questa procedura dettagliata verrà usato Knowledge Exploration Service (KES) per creare il motore per un'esperienza di ricerca interattiva per le pubblicazioni accademiche. È possibile installare lo strumento da riga di comando, [`kes.exe`](CommandLine.md), e tutti i file di esempio dall'[SDK di Knowledge Exploration Service](https://www.microsoft.com/en-us/download/details.aspx?id=51488).

L'esempio delle pubblicazioni accademiche contiene un campione di 1000 documenti accademici pubblicati da ricercatori Microsoft.  Ogni documento è associato a un titolo, un anno di pubblicazione, autori e parole chiave. Ogni autore è rappresentato da un ID, nome e affiliazione al momento della pubblicazione. Ogni parola chiave può essere associata a un set di sinonimi (ad esempio, la parola chiave "macchina a vettori di supporto" può essere associata al sinonimo "mvs").

## <a name="define-the-schema"></a>Definire lo schema

Lo schema descrive la struttura di attributi degli oggetti nel dominio. Specifica nome e tipo di dati per ogni attributo in un formato di file JSON. Il contenuto del file *Academic.schema* è analogo a quello dell'esempio seguente.

```json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

Qui si definiscono *Title*, *Year* e *Keyword* rispettivamente come stringa, numero intero e attributo di stringa. Poiché gli autori sono rappresentati da ID, nome e affiliazione, *Author* viene definito come attributo composito con tre attributi secondari: *Author.Id*, *Author.Name* e *Author.Affiliation*.

Per impostazione predefinita, gli attributi supportano tutte le operazioni disponibili per il relativo tipo di dati, incluse *equals*, *starts_with* e *is_between*. Poiché l'ID autore viene usato solo internamente come identificatore, eseguire l'override dell'impostazione predefinita e specificare *equals* come unica operazione indicizzata.

Per l'attributo *Keyword* consentire sinonimi che corrispondano ai valori canonici delle parole chiave specificando il file di sinonimi *Keyword.syn* nella definizione dell'attributo. Questo file contiene un elenco di coppie di valori canonici e sinonimi:

```json
...
["support vector machine","support vector machines"]
["support vector machine","support vector networks"]
["support vector machine","support vector regression"]
["support vector machine","support vector"]
["support vector machine","svm machine learning"]
["support vector machine","svm"]
["support vector machine","svms"]
["support vector machine","vector machine"]
...
```

Per altre informazioni sulla definizione dello schema, vedere [Formato dello schema](SchemaFormat.md).

## <a name="generate-data"></a>Generare i dati

Il file di dati descrive l'elenco delle pubblicazioni da indicizzare, con ogni riga che specifica i valori di attributo di un documento in [formato JSON](http://json.org/).  L'esempio seguente è una singola riga del file di dati *Academic.data* formattata per migliorare la leggibilità:

```
...
{
    "logprob": -16.714,
    "Title": "the world wide telescope",
    "Year": 2001,
    "Author": [
        {
            "Id": 717694024,
            "Name": "alexander s szalay",
            "Affiliation": "microsoft"
        },
        {
            "Id": 2131537204,
            "Name": "jim gray",
            "Affiliation": "microsoft"
        }
    ]
}
...
```

In questo frammento di codice vengono specificati gli attributi *Title* e *Year* del documento rispettivamente come stringa JSON e numero. Valori multipli sono rappresentati da matrici JSON. Poiché *Author* è un attributo composito, ogni valore viene rappresentato usando un oggetto JSON costituito dai relativi attributi secondari. Gli attributi con valori mancanti, ad esempio *Keyword* in questo caso, possono essere esclusi dalla rappresentazione JSON.

Per differenziare le probabilità di documenti diversi, specificare la relativa probabilità logaritmica usando l'attributo *logprob* integrato. Dato un valore di probabilità *p* compreso tra 0 e 1, la probabilità logaritmica viene calcolata come log(*p*), dove log() è la funzione di logaritmo naturale.

Per altre informazioni, vedere [Formato dati](DataFormat.md).

## <a name="build-a-compressed-binary-index"></a>Compilare un indice binario compresso

Una volta disponibili un file di schema e un file di dati, sarà possibile compilare un indice binario compresso degli oggetti dati usando [`kes.exe build_index`](CommandLine.md#build_index-command). In questo esempio il file di indice *Academic.index* viene compilato dal file di schema di input *Academic.schema* e dal file di dati *Academic.data*. Usare il comando seguente:

`kes.exe build_index Academic.schema Academic.data Academic.index`

Per la creazione rapida di prototipi all'esterno di Azure, [`kes.exe build_index`](CommandLine.md#build_index-command) è in grado di compilare in locale indici ridotti da file di dati contenenti fino a 10.000 oggetti. Per file di dati più grandi, è possibile eseguire il comando da una [macchina virtuale Windows in Azure](../../../articles/virtual-machines/windows/quick-create-portal.md) o eseguire una compilazione remota in Azure. Per informazioni dettagliate, vedere la sezione relativa alla scalabilità verticale.

## <a name="use-an-xml-grammar-specification"></a>Usare una specifica di grammatica XML

La grammatica specifica il set di query in linguaggio naturale che il servizio può interpretare, nonché il modo in cui queste query in linguaggio naturale vengono convertite in espressioni di query semantiche. In questo esempio viene usata la grammatica specificata in *academic.xml*:

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="Academic.schema" name="academic"/>

  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>

    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>

    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>

      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>

        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>

        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>

  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="lt" name="year"/>
          </item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="gt" name="year"/>
          </item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```

Per altre informazioni sulla sintassi della specifica di grammatica, vedere [Formato di grammatica](GrammarFormat.md).

## <a name="compile-the-grammar"></a>Compilare la grammatica

Una volta disponibile una specifica di grammatica XML, sarà possibile compilarla in una grammatica binaria usando [`kes.exe build_grammar`](CommandLine.md#build_grammar-command). Se la grammatica importa uno schema, il file di schema deve trovarsi nello stesso percorso del codice XML della grammatica. In questo esempio si compila il file di grammatica binaria *Academic.grammar* dal file di grammatica XML di input *Academic.xml*. Usare il comando seguente:

`kes.exe build_grammar Academic.xml Academic.grammar`

## <a name="host-the-grammar-and-index-in-a-web-service"></a>Ospitare la grammatica e l'indice in un servizio Web

Per la creazione rapida di prototipi, è possibile ospitare la grammatica e l'indice in un servizio Web nel computer locale, tramite [`kes.exe host_service`](CommandLine.md#host_service-command). Sarà poi possibile accedere al servizio tramite [API Web](WebAPI.md) per convalidare la correttezza dei dati e la struttura della grammatica. In questo esempio il file di grammatica *Academic.grammar* e il file di indice *Academic.index* vengono ospitati in http://localhost:8000/. Usare il comando seguente:

`kes.exe host_service Academic.grammar Academic.index --port 8000`

Verrà avviata un'istanza locale del servizio Web. Per testare in modo interattivo il servizio, visitare `http::localhost:<port>` da un browser. Per altre informazioni, vedere la sezione relativa al test del servizio.

È anche possibile chiamare direttamente varie [API Web](WebAPI.md) per testare l'interpretazione del linguaggio naturale, il completamento delle query, la valutazione delle query strutturate e il calcolo dell'istogramma. Per arrestare il servizio, immettere "quit" al prompt dei comandi `kes.exe host_service` oppure premere CTRL+C. Di seguito sono riportati alcuni esempi:

* [http://localhost:8000/interpret?query=papers by susan t dumais](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20dumais)
* [http://localhost:8000/interpret?query=papers by susan t d&complete=1](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20d&complete=1)
* [http://localhost:8000/evaluate?expr=Composite(Author.Name=='susan t dumais')&attributes=Title,Year,Author.Name,Author.Id&count=2](http://localhost:8000/evaluate?expr=Composite%28Author.Name==%27susan%20t%20dumais%27%29&attributes=Title,Year,Author.Name,Author.Id&count=2)
* [http://localhost:8000/calchistogram?expr=And(Composite(Author.Name=='susan t dumais'),Year>=2013)&attributes=Year,Keyword&count=4](http://localhost:8000/calchistogram?expr=And%28Composite%28Author.Name=='susan%20t%20dumais'%29,Year>=2013%29&attributes=Year,Keyword&count=4)

All'esterno di Azure, [`kes.exe host_service`](CommandLine.md#host_service-command) è limitato agli indici che comprendono fino a 10.000 oggetti. Altri limiti includono una frequenza API di 10 richieste al secondo e un totale di 1000 richieste prima che il processo termini automaticamente. Per ignorare queste restrizioni, eseguire il comando da una [macchina virtuale Windows in Azure](../../../articles/virtual-machines/windows/quick-create-portal.md) o eseguirne la distribuzione in un servizio cloud di Azure con il comando [`kes.exe deploy_service`](CommandLine.md#deploy_service-command). Per informazioni dettagliate, vedere la sezione relativa alla distribuzione del servizio.

## <a name="scale-up-to-host-larger-indices"></a>Eseguire la scalabilità verticale per ospitare indici di dimensioni superiori

Quando si esegue `kes.exe` all'esterno di Azure, l'indice è limitato a 10.000 oggetti. È possibile compilare e ospitare indici di dimensioni maggiori con Azure. Iscriversi per ottenere una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). In alternativa, se si è eseguita la sottoscrizione a Visual Studio o MSDN, è possibile [attivare i vantaggi della sottoscrizione](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), che offrono alcuni crediti Azure ogni mese.

Per consentire a `kes.exe` di accedere a un account di Azure, [scaricare il file di impostazioni di pubblicazione di Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) dal portale di Azure. Se richiesto, accedere all'account di Azure desiderato. Salvare il file come *AzurePublishSettings.xml* nella directory di lavoro in cui viene eseguito `kes.exe`.

Esistono due modi per compilare e ospitare indici di grandi dimensioni. Il primo consiste nel preparare i file di schema e dati in una VM Windows in Azure ed eseguire [`kes.exe build_index`](#building-index) per compilare l'indice in locale nella VM, senza restrizioni a livello di dimensioni. L'indice risultante può essere ospitato in locale nella VM mediante [`kes.exe host_service`](#hosting-service) per la creazione rapida di prototipi, anche in questo caso senza restrizioni. Per la procedura dettagliata, vedere l'[esercitazione incentrata su una VM di Azure](../../../articles/virtual-machines/windows/quick-create-portal.md).

Il secondo metodo consiste nell'eseguire una compilazione remota di Azure mediante [`kes.exe build_index`](CommandLine.md#build_index-command) con il parametro `--remote`, che specifica una dimensione per la VM di Azure. Quando viene specificato il parametro `--remote`, il comando crea una VM temporanea di Azure con questa dimensione. Compila poi l'indice nella VM, lo carica nell'archiviazione BLOB di destinazione ed elimina la VM al completamento. La sottoscrizione di Azure viene addebitata per il costo della VM durante la compilazione dell'indice.

Questa funzionalità di compilazione remota di Azure consente l'esecuzione di [`kes.exe build_index`](CommandLine.md#build_index-command) in qualsiasi ambiente. Quando si esegue una compilazione remota, gli argomenti di schema e dati di input possono essere URL di percorsi file locali o [archiviazioni BLOB di Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). L'argomento dell'indice di output deve essere un URL di archiviazione BLOB. Per creare un account di archiviazione di Azure, vedere [Informazioni sugli account di archiviazione di Azure](../../storage/common/storage-create-storage-account.md). Per copiare i file in modo efficiente da e verso l'archiviazione BLOB, usare l'utilità [AzCopy](../../storage/common/storage-use-azcopy.md).

In questo esempio è possibile presupporre che sia stato già creato il container di archiviazione BLOB seguente: http://&lt;*account*&gt;.blob.core.windows.net/&lt;*container*&gt;/. Contiene lo schema *Academic.schema*, il file di sinonimi referenziato *Keywords.syn* e il file di dati completo *Academic.full.data*. È possibile compilare l'indice completo in remoto usando il comando seguente:

`kes.exe build_index http://<account>.blob.core.windows.net/<container>/Academic.schema http://<account>.blob.core.windows.net/<container>/Academic.full.data http://<account>.blob.core.windows.net/<container>/Academic.full.index --remote <vm_size>`

Potrebbero essere necessari 5-10 minuti per eseguire il provisioning di una VM temporanea per compilare l'indice. Per la creazione rapida di prototipi, è possibile:
- Eseguire lo sviluppo in locale con un set di dati ridotto in qualsiasi computer.
- [Creare manualmente una VM di Azure](../../../articles/virtual-machines/windows/quick-create-portal.md), [connettervisi](../../../articles/virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) tramite Desktop remoto, installare l'[SDK di Knowledge Exploration Service](https://www.microsoft.com/en-us/download/details.aspx?id=51488) ed eseguire [`kes.exe`](CommandLine.md) dalla VM.

Il paging rallenta il processo di compilazione. Per evitarlo, usare una VM con una quantità di RAM di tre volte superiore rispetto alla dimensione del file di dati di input per la compilazione dell'indice. Usare una VM con 1 GB di RAM in più rispetto alla dimensione dell'indice per l'hosting. Per un elenco delle dimensioni di VM disponibili, vedere [Dimensioni delle macchine virtuali](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

## <a name="deploy-the-service"></a>Distribuire il servizio

Una volta disponibili una grammatica e un indice, sarà possibile iniziare a distribuire il servizio in un servizio cloud di Azure. Per creare un nuovo servizio cloud di Azure, vedere [Come creare e distribuire un servizio Cloud](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md). Non specificare in questo momento un pacchetto di distribuzione.  

Dopo avere creato il servizio cloud, sarà possibile usare [`kes.exe deploy_service`](CommandLine.md#deploy_service-command) per distribuire il servizio. Un servizio cloud di Azure include due slot di distribuzione: produzione e staging. Per un servizio che riceve il traffico utente live, è consigliabile eseguire inizialmente la distribuzione nello slot di staging. Attendere l'avvio e l'inizializzazione del servizio. Sarà poi possibile inviare alcune richieste per convalidare la distribuzione e verificare che superi i test di base.

[Scambiare](../../../articles/cloud-services/cloud-services-nodejs-stage-application.md) i contenuti dello slot di staging con lo slot di produzione, in modo che il traffico live venga ora indirizzato al servizio appena distribuito. È possibile ripetere questo processo durante la distribuzione di una versione aggiornata del servizio con nuovi dati. Come con tutti gli altri servizi cloud di Azure, è possibile usare il portale di Azure per configurare la [scalabilità automatica](../../../articles/cloud-services/cloud-services-how-to-scale-portal.md).

In questo esempio l'indice *Academic* viene distribuito nello slot di staging di un servizio cloud esistente con VM *<vm_size>*. Usare il comando seguente:

`kes.exe deploy_service http://<account>.blob.core.windows.net/<container>/Academic.grammar http://<account>.blob.core.windows.net/<container>/Academic.index <serviceName> <vm_size> --slot Staging`

Per un elenco delle dimensioni di VM disponibili, vedere [Dimensioni delle macchine virtuali](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

Dopo aver distribuito il servizio, sarà anche possibile chiamare varie [API Web](WebAPI.md) per testare l'interpretazione del linguaggio naturale, il completamento delle query, la valutazione delle query strutturate e il calcolo dell'istogramma.  

## <a name="test-the-service"></a>Testare il servizio

Per eseguire il debug di un servizio attivo, passare al computer host da un Web browser. Per un servizio locale distribuito tramite host_service, passare a `http://localhost:<port>/`.  Per un servizio cloud di Azure distribuito tramite deploy_service, passare a `http://<serviceName>.cloudapp.net/`.

Questa pagina contiene un collegamento a informazioni sulle statistiche delle chiamate API di base, nonché sulla grammatica e sull'indice ospitati in questo servizio. La pagina contiene anche un'interfaccia di ricerca interattiva che illustra l'uso delle API Web. Immettere query nella casella di ricerca per visualizzare i risultati delle chiamate API [interpret](interpretMethod.md), [evaluate](evaluateMethod.md) e [calchistogram](calchistogramMethod.md). L'origine HTML sottostante di questa pagina rappresenta anche un esempio di come integrare le API Web in un'app, al fine di creare un'esperienza di ricerca interattiva avanzata.


