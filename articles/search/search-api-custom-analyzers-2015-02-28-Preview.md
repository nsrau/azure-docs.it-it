<properties
	pageTitle="Analizzatori personalizzati (API REST di Ricerca di Azure versione 2015-02-28-Preview) | Microsoft Azure"
	description="Analizzatori personalizzati (API REST di Ricerca di Azure versione 2015-02-28-Preview)"
	services="search"
	documentationCenter=""
	authors="Yahnoosh"
	manager="pablocas"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.author="jlembicz"
	ms.date="02/18/2016" />

# Analizzatori (API REST di Ricerca di Azure versione 2015-02-28-Preview)

> [AZURE.NOTE] Il supporto per gli analizzatori personalizzati è attualmente in anteprima. Per sfruttare questa funzionalità, è necessario usare la versione 2015-02-28-Preview dell'API REST per il servizio Ricerca di Azure. Si noti che le funzionalità di anteprima non sono ancora state aggiunte a .NET SDK, quindi l'uso dell'API REST di anteprima è attualmente la sola opzione di programmazione.

## Panoramica

Il ruolo di un motore di ricerca full-text è, in breve, quello di elaborare e archiviare i documenti per poter eseguire in modo efficiente le operazioni di query e recupero. In generale si riduce tutto all'estrazione di parole importanti dai documenti, al loro inserimento in un indice e infine all'uso dell'indice per trovare i documenti che corrispondono alle parole di una determinata query. Il processo di estrazione delle parole dai documenti e dalle query di ricerca è detto analisi lessicale. I componenti che eseguono l'analisi lessicale sono detti analizzatori. In Ricerca di Azure è possibile scegliere tra un set di [analizzatori predefiniti indipendenti dalla lingua](#Analyzers) e di [analizzatori specifici della lingua](https://msdn.microsoft.com/library/azure/dn879793.aspx). È anche possibile definire analizzatori personalizzati. Un analizzatore personalizzato consente di controllare il processo di conversione del testo in token indicizzabili/ricercabili. Si tratta di una configurazione definita dell'utente che include un singolo tokenizer predefinito e uno o più filtri di token. Il tokenizer è responsabile della suddivisione del testo in token, i filtri di token invece sono responsabili della modifica dei token emessi dal tokenizer.

Gli scenari comuni abilitati dagli analizzatori personalizzati includono:

- Ricerca fonetica: aggiungere un filtro fonetico per abilitare la ricerca basata sul suono e sulla compitazione di una parola.
- Analisi lessicale disabilitata: usare l'analizzatore Keyword per creare campi ricercabili che non vengono analizzati.
- Ricerca rapida di prefissi/suffissi: aggiungere il filtro di token Edge N-gram per indicizzare i prefissi delle parole per poter abilitare la corrispondenza rapida dei prefissi. Combinarlo con il filtro di token Reverse per eseguire la corrispondenza dei suffissi.
- Tokenizzazione personalizzata: usare, ad esempio, il tokenizer Whitespace per suddividere le frasi in token usando lo spazio vuoto come delimitatore.
- ASCII folding: aggiungere il filtro ASCII folding per normalizzare i segni diacritici, ad esempio ö oppure ê, nei termini di ricerca.

È possibile definire più analizzatori personalizzati per variare la combinazione dei filtri, ma ogni campo può usare solo un analizzatore per l'analisi dell'indicizzazione e uno per l'analisi della ricerca.
 
Questa pagina fornisce un elenco di analizzatori, tokenizer e filtri di token supportati. È disponibile anche una descrizione delle modifiche apportate alla definizione di indice con un esempio di utilizzo. Per un'introduzione e per l'esplorazione degli scenari, vedere [Analizzatori personalizzati in Ricerca di Azure](link). Per altre informazioni sulla tecnologia sottostante sfruttata nell'implementazione di Ricerca di Azure, vedere il [riepilogo del pacchetto di analisi (Lucene)](http://lucene.apache.org/core/4_10_3/core/org/apache/lucene/analysis/package-summary.html).


## Analizzatore predefinito

L'analizzatore predefinito è l'analizzatore [standard Apache Lucene](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html).

## Regole di convalida

I nomi di analizzatori, tokenizer e filtri di token personalizzati devono essere univoci e non possono essere uguali a quelli di analizzatori, tokenizer o filtri di token predefiniti.
 

## Definizione di indice con analizzatori personalizzati 

Gli analizzatori personalizzati vengono definiti durante la creazione dell'indice. La sintassi dei componenti di analisi personalizzati nella definizione di indice è illustrata di seguito. Un esempio completo è disponibile [qui](#Example).

	{
	  	. . .
	 	(standard create index request body)
	 	. . .  
		"analyzers": (optional) [ 
		    {  
		       "name": "name of analyzer",  
		       "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer", 
		       "tokenizer": "tokenizer_name", (tokenizer_name is the name of a tokenizer from the [Tokenizers](#Tokenizers) table)
		       "tokenFilters": [
				  "token_filter_name", (token_filter_name is the name of a token filter from the [Token Filters](#TokenFilters) table)
				  "token_filter_name"
				] (token filters will be applied from left to right)
		    },
			{  
		       "name": "name of analyzer",  
		       "@odata.type": "#analyzer_type", 
		       "option1": value1,
		       "option2": value2,
		       ...  
		    }
		],  
		"tokenizers": (optional) [ 
		    {  
		       "name": "tokenizer_name",  
		       "@odata.type": "#tokenizer_type", 
		       "option1": value1,  
		       "option2": value2, 
		       ...  
		    }
		],
		"tokenFilters": (optional) [ 
		    {  
		       "name": "token_filter_name",  
		       "@odata.type": "#token_filter_type", 
		       "option1": value1,  
		       "option2": value2, 
		       ...  
		    }
		]	
	}

> [AZURE.NOTE] Gli analizzatori personalizzati creati non vengono esposti nel portale di Azure. Il solo modo per aggiungere un analizzatore personalizzato consiste nell'usare il codice che effettua le chiamate all'API REST quando si definisce un indice.

##Attributi dell'indice per analizzatori, tokenizer e filtri di token personalizzati 

Questa sezione specifica le proprietà di configurazione per la sezione degli analizzatori, dei tokenizer e dei filtri di token di una definizione di indice.

###Analizzatori

<table>
  <tbody>
    <tr>
      <td>
        <b>Nome</b>
      </td>
      <td>
		Deve contenere solo lettere, cifre, spazi, trattini o caratteri di sottolineatura, può iniziare e terminare solo con caratteri alfanumerici ed è limitato a 128 caratteri.
      </td>
    </tr>
    <tr>
      <td>
        <b>Tipo</b>
      </td>
      <td>
		I valori validi sono "#Microsoft.Azure.Search.CustomAnalyzer" o il nome di un analizzatore dell'elenco di analizzatori supportati. Vedere la colonna <b>analyzer_type</b> nella tabella [Analizzatori](#Analyzers) più avanti.
      </td>
	</tr>
    <tr>
      <td>
        <b>Tokenizer</b> (valido solo per #Microsoft.Azure.Search.CustomAnalyzer)
      </td>
      <td>
		Obbligatorio. Deve essere uno dei tokenizer predefiniti elencati più avanti nella tabella [Tokenizer](#Tokenizers) o uno dei tokenizer personalizzati definiti nella definizione di indice.
      </td>
	</tr>
    <tr>
      <td>
        <b>TokenFilters</b> (valido solo per #Microsoft.Azure.Search.CustomAnalyzer)
      </td>
      <td>
		Tutti i filtri di token sono uno dei filtri di token predefiniti elencati nella tabella [Filtri di token](#TokenFilters) o uno dei filtri di token personalizzati definiti nella definizione di indice.
      </td>
	</tr>
    <tr>
      <td>
        <b>Opzioni</b>
      </td>
      <td>
		Devono essere [opzioni valide](#Analyzers) di un analizzatore predefinito (non personalizzato).
      </td>
	</tr>
  </tbody>
</table>

###Tokenizer

Un tokenizer divide un testo continuo in una sequenza di token, ad esempio suddividendo una frase in parole. È possibile specificare esattamente un tokenizer per ogni analizzatore personalizzato. Se è necessario più di un tokenizer, è possibile creare più analizzatori personalizzati e assegnarli a ogni singolo campo nello schema dell'indice. Un analizzatore personalizzato può usare un tokenizer predefinito con opzioni predefinite o personalizzate.

<table>
  <tbody>
    <tr>
      <td>
        <b>Nome</b>
      </td>
      <td>
		Deve contenere solo lettere, cifre, spazi, trattini o caratteri di sottolineatura, può iniziare e terminare solo con caratteri alfanumerici ed è limitato a 128 caratteri.
      </td>
    </tr>
    <tr>
      <td>
        <b>Tipo</b>
      </td>
      <td>
		Nome del tokenizer dall'elenco di tokenizer supportati. Vedere la colonna <b>tokenizer_type</b> più avanti nella tabella [Tokenizer](#Tokenizers).
      </td>
	</tr>
     <tr>
      <td>
        <b>Opzioni</b>
      </td>
      <td>
		Devono essere [opzioni valide](#Tokenizers) di un determinato tipo di tokenizer.
      </td>
	</tr>
  </tbody>
</table>

###Filtri di token

Un filtro di token viene usato per filtrare o modificare i token generati da un tokenizer. È possibile, ad esempio, specificare un filtro lowercase che converte tutti i caratteri in lettere minuscole. In un analizzatore personalizzato possono essere presenti più filtri di token. I filtri di token vengono eseguiti nello stesso ordine in cui sono elencati.

<table>
  <tbody>
    <tr>
      <td>
        <b>Nome</b>
      </td>
      <td>
		Deve contenere solo lettere, cifre, spazi, trattini o caratteri di sottolineatura, può iniziare e terminare solo con caratteri alfanumerici ed è limitato a 128 caratteri.
      </td>
    </tr>
    <tr>
      <td>
        <b>Tipo</b>
      </td>
      <td>
		Nome del filtro di token dall'elenco di filtri di token supportati. Vedere la colonna <b>token_filter_type</b> più avanti nella tabella [Filtri di token](#TokenFilters).
      </td>
	</tr>
     <tr>
      <td>
        <b>Opzioni</b>
      </td>
      <td>
		Devono essere [opzioni valide](#TokenFilters) di un determinato tipo di filtro di token.
      </td>
	</tr>
  </tbody>
</table>

<a name="Example"></a>
##Esempio di definizione dell'indice

Questo esempio di definizione dell'indice include un campo che usa un analizzatore personalizzato "my\_analyzer" che a sua volta usa un tokenizer Standard personalizzato "my\_standard\_tokenizer" e due filtri di token: lowercase e il filtro asciifolding personalizzato "my\_asciifolding".

	{
	   "name":"myindex",
	   "fields":[
	      {
	         "name":"id",
	         "type":"Edm.String",
	         "key":true,
	         "searchable":false
	      },
	      {
	         "name":"text",
	         "type":"Edm.String",
	         "searchable":true,
	         "analyzer":"my_analyzer"
	      }
	   ],
	   "analyzers":[
	      {
	         "name":"my_analyzer",
	         "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
	         "tokenizer":"my_standard_tokenizer",
	         "tokenFilters":[
	            "my_asciifolding",
	            "lowercase"
	         ]
	      }
	   ],
	   "tokenizers":[
	      {
	         "name":"my_standard_tokenizer",
	         "@odata.type":"#Microsoft.Azure.Search.StandardTokenizer",
	         "maxTokenLength":20
	      }
	   ],
	   "tokenFilters":[
	      {
	         "name":"my_asciifolding",
	         "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
	         "preserveOriginal":true
	      }
	   ]
	}

<a name="Analyzers"></a>
##Analizzatori

<table>
  <tbody>
	<thead>
	<tr>
		<td>analyzer_name</td>
		<td>analyzer_type</td>
		<td>Descrizione</td>
		<td>Opzioni</td>
	</tr>
	</thead>
    <tr>
      <td>[keyword](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.KeywordAnalyzer</td>
	  <td>Considera tutto il contenuto di un campo come un unico token. È utile per dati come codici postali ZIP (Stati Uniti), ID e alcuni nomi di prodotto</td>
	  <td></td>
    </tr>
    <tr>
      <td>[pattern](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.PatternAnalyzer</td>
	  <td>Separa in modo flessibile il testo in termini tramite un modello di espressione regolare</td>
	  <td>
		- lowercase - tipo: bool - se i termini sono in minuscolo, il valore predefinito è true - [pattern](http://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) - tipo: stringa - modello di espressione regolare per la corrispondenza con i separatori di token, valore predefinito: \w+ - [flags](http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) - tipo: stringa - flag di espressione regolare, valore predefinito: una stringa vuota - stopwords - tipo: matrice di stringhe - elenco di parole non significative, valore predefinito: un elenco vuoto
	  </td>
    </tr>
    <tr>
      <td>[simple](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.SimpleAnalyzer</td>	 
	  <td>Divide il testo in caratteri diversi da lettere e li converte in minuscolo</td>
	  <td></td>
    </tr>
    <tr>
      <td>[snowball](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.SnowballAnalyzer</td>
	  <td>Analizzatore standard con il [filtro di stemming snowball](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)</td>
	  <td>
		- language - tipo: stringa - valori consentiti: danish, dutch, english, finnish, french, german, hungarian, italian, norwegian, portuguese, russian, spanish, swedish - stopwords - tipo: matrice di stringhe - elenco di parole non significative, valore predefinito: un elenco vuoto
	  </td>
    </tr>
    <tr>
      <td>[standard](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.StandardAnalyzer</td>
	  <td>Analizzatore Lucene standard - costituito dal tokenizer standard, dal filtro lowercase e dal filtro stop</td>
	  <td>
		- maxTokenLength - tipo: int - lunghezza massima token, valore predefinito: 255. I token con lunghezza superiore a quella massima vengono suddivisi. - stopwords - tipo: matrice di stringhe - elenco di parole non significative, valore predefinito: un elenco vuoto
	  </td>
    </tr>
    <tr>
      <td>standardasciifolding.lucene</td>
      <td>#Microsoft.Azure.Search.StandardAsciiFoldingAnalyzer</td>	  
	  <td>Analizzatore standard con filtro Ascii folding</td>
	  <td></td>
    </tr>
    <tr>
      <td>[stop](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.StopAnalyzer</td>
	  <td>Divide il testo in caratteri diverse da lettere, applica i filtri di token lowercase e stopword</td>
	  <td>
		- stopwords - tipo: matrice di stringhe - elenco di parole non significative, valore predefinito: un elenco vuoto
	  </td>
    </tr>
    <tr>
      <td>[whitespace](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.WhitespaceAnalyzer</td>
	  <td>Analizzatore che usa il tokenizer whitespace.</td>
	  <td></td>
    </tr>
  </tbody>
</table>

<a name="Tokenizers"></a>
##Tokenizer

<table>
  <tbody>
	<thead>
	<tr>
		<td>tokenizer_name</td>
		<td>tokenizer_type</td>
		<td>Descrizione</td>
		<td>Opzioni</td>
	</tr>
	</thead>
    <tr>
      <td>[classic](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.ClassicTokenizer</td>
	  <td>Tokenizer basato sulla grammatica adatto per l'elaborazione di documenti nella maggior parte delle lingue europee</td>
	  <td>
		- maxTokenLength - tipo: int - lunghezza massima token, valore predefinito: 255. I token con lunghezza superiore a quella massima vengono suddivisi. 
	  </td>	  
    </tr>
    <tr>
      <td>[edgeNGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.EdgeNGramTokenizer</td>
	  <td>Converte in token l'input da un bordo in n-grammi di una o più dimensioni specificate</td>
	  <td>
		- minGram - tipo: int - valore predefinito: 1 - maxGram - tipo: int - valore predefinito: 2 - tokenChars - tipo: matrice di stringhe - classi di caratteri da conservare nei token, valori consentiti: lettera, cifra, spazio vuoto, segni di punteggiatura, simbolo
	  </td>	  
    </tr>
    <tr>
      <td>[keyword](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.KeywordTokenizer</td>
	  <td>Emette l'intero input come singolo token</td>
	  <td>
		- bufferSize - tipo: int - dimensione buffer di lettura, valore predefinito: 256
	  </td>
    </tr>
    <tr>
      <td>[letter](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.LetterTokenizer</td>
	  <td>Divide il testo in caratteri diversi da lettere</td>
	  <td></td>
    </tr>
    <tr>
      <td>[lowercase](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.LowercaseTokenizer</td>
	  <td>Divide il testo in caratteri diversi da lettere e li converte in minuscolo</td>
	  <td></td>
    </tr>
    <tr>
      <td>[nGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.NGramTokenizer</td>
	  <td>Converte in token l'input in n-grammi di una o più dimensioni specificate</td>
	  <td>
		- minGram - tipo: int - valore predefinito: 1 - maxGram - tipo: int - valore predefinito: 2 - tokenChars - tipo: matrice di stringhe - classi di caratteri da conservare nei token, valori consentiti: lettera, cifra, spazio vuoto, segni di punteggiatura, simbolo
	  </td>
    </tr>
    <tr>
      <td>[path_hierarchy](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.PathHierarchyTokenizer</td>
	  <td>Tokenizer per gerarchie in formato percorso</td>
	  <td>
		- delimiter - tipo: stringa - valore predefinito: "/" - replacement - tipo: stringa - se impostato, sostituisce il carattere delimitatore, valore predefinito: delimiter - bufferSize - tipo: int - valore predefinito: 1024 - reverse - tipo: bool - se impostato, genera il token in ordine inverso, valore predefinito: true - skip - tipo: bool - token iniziali da ignorare, valore predefinito: 0
	  </td>
    </tr>
    <tr>
      <td>[pattern](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.PatternTokenizer</td>
	  <td>Il tokenizer usa il modello Regex corrispondente per costruire token distinti</td>
	  <td>
		 - pattern - tipo: stringa - modello di espressione regolare, valore predefinito: \w+ - [flags](http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) - tipo: stringa - flag di espressione regolare, valore predefinito: una stringa vuota - group - tipo: int - gruppo da estrarre in token, valore predefinito: -1 (dividi)
	  </td>
    </tr>
    <tr>
      <td>[standard](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.StandardTokenizer</td>
	  <td>Suddivide il testo che segue le [regole di segmentazione testo Unicode](http://unicode.org/reports/tr29/)</td>
	  <td>
		 - maxTokenLength - tipo: int - lunghezza massima token, valore predefinito: 255. I token con lunghezza superiore a quella massima vengono suddivisi. 
	  </td>
    </tr>
    <tr>
      <td>[uax_url_email](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.UaxEmailUrlTokenizer</td>
	  <td>Converte in un unico token URL e indirizzi di posta elettronica</td>
	  <td>
		- maxTokenLength - tipo: int - lunghezza massima token, valore predefinito: 255. I token con lunghezza superiore a quella massima vengono suddivisi.
	  </td>
    </tr>
    <tr>
      <td>[whitespace](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.WhitespaceTokenizer</td>
	  <td>Divide il testo in corrispondenza dello spazio vuoto</td>
	  <td></td>
    </tr>
  </tbody>
</table>

<a name="TokenFilters"></a>
##Filtri di token

<table>
  <tbody>
	<thead>
	<tr>
		<td>token_filter_name</td>
		<td>token_filter_type</td>
		<td>Descrizione</td>
		<td>Opzioni</td>
	</tr>
	</thead>
    <tr>
      <td>[arabic_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.ArabicNormalizationTokenFilter</td>
	  <td>Rimuove tutti i caratteri dopo un apostrofo (incluso l'apostrofo stesso)</td>
    </tr>
    <tr>
      <td>[apostrophe](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)</td>
      <td>#Microsoft.Azure.Search.ApostropheTokenFilter</td>
	  <td>Rimuove tutti i caratteri dopo un apostrofo</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[asciifolding](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)</td>
      <td>#Microsoft.Azure.Search.AsciiFoldingTokenFilter</td>
	  <td>Converte i caratteri alfabetici, numerici e simbolici Unicode non compresi nei primi 127 caratteri ASCII (blocco Unicode "Latino di base") negli equivalenti ASCII, se esistenti</td>
	  <td>
		- preserveOriginal - tipo: bool - se true, il token originale verrà conservato, valore predefinito: false
	  </td>	  
    </tr>
    <tr>
      <td>[cjk_bigram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)</td>
      <td>#Microsoft.Azure.Search.CjkBigramTokenFilter</td>
	  <td>Formatta i bigrammi dei termini CJK generati da StandardTokenizer</td>
	  <td>
		- ignoreScripts - tipo: bool - script da ignorare, valori consentiti: han, hiragana, katakana, hangul. Valore predefinito: un elenco vuoto - outputUnigrams tipo: bool -- impostarlo su true se si vuole sempre ottenere l'output sia degli unigrammi che dei bigrammi, valore predefinito: false
	  </td>	  
    </tr>
    <tr>
      <td>[cjk_width](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)</td>
      <td>#Microsoft.Azure.Search.CjkWidthTokenFilter</td>
	  <td>Normalizza le differenze di larghezza CJK. Riduce le varianti ASCII a larghezza intera nei caratteri del latino di base equivalenti e le varianti Katakana a metà larghezza nei caratteri kana equivalenti</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[classic](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)</td>
      <td>#Microsoft.Azure.Search.ClassicTokenFilter</td>
	  <td>Rimuove i possessivi inglesi e i punti dagli acronimi</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[common_grams](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)</td>
      <td>#Microsoft.Azure.Search.CommonGramTokenFilter</td>
	  <td>Costruisce bigrammi per i termini che ricorrono di frequente durante l'indicizzazione. Anche i singoli termini vengono tuttavia indicizzati, con i bigrammi sovrapposti.</td>
	  <td>
		- commonWords - tipo: matrice di stringhe - set di parole comuni, valore predefinito: un elenco vuoto - ignoreCase - tipo: bool - se true, le parole comuni corrispondenti saranno senza distinzione tra maiuscole e minuscole, valore predefinito: false - queryMode - tipo: bool - genera i bigrammi, quindi rimuove le parole comuni e i singoli termini seguiti da una parola comune, valore predefinito: false
	  </td>	  
    </tr>
    <tr>
      <td>[delimited_payload_filter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/payloads/DelimitedPayloadTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.DelimitedPayloadTokenFilter</td>
	  <td>I caratteri prima del delimitatore sono i "token", quelli dopo sono il payload. Se, ad esempio, il delimitatore è "|", per la stringa "hello|world", "hello" è il token e "world" è il payload. È anche possibile includere un codificatore per convertire il payload in modo appropriato (da caratteri a byte)</td>
	  <td>
		- delimiter - tipo: char - valore predefinito: "|" - encoding - tipo: stringa - valori consentiti: int, float, identity. Valore predefinito: float
	  </td>	  
    </tr>
    <tr>
      <td>[dictionary_decompounder](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.DictionaryDecompounderTokenFilter</td>
	  <td>Scompone le parole composte presenti in diverse lingue germaniche</td>
	  <td>
		- wordList - tipo: matrice di stringhe - elenco di parole da confrontare, valore predefinito: un elenco vuoto - minWordSize - tipo: int - vengono elaborate solo parole più lunghe di questo valore, valore predefinito: 5 - minSubwordSize - tipo: int - vengono generate solo parole secondarie più lunghe di questo valore, valore predefinito: 2 - maxSubwordSize - tipo: int - vengono generate solo parole secondarie più brevi di questo valore, valore predefinito: 15 - onlyLongestMatch - tipo: bool - aggiunge all'output solo la parola secondaria più lunga corrispondente, valore predefinito: false 
	  </td>	  
    </tr>
    <tr>
      <td>[edgeNGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.EdgeNGramTokenFilter</td>
	  <td>Genera n-grammi di una o più dimensioni specificate a partire dall'inizio o dalla fine di un token di input</td>
	  <td>
		- minGram - tipo: int - valore predefinito: 1 - maxGram - tipo: int - valore predefinito: 2 - side - tipo: stringa - specifica da quale lato dell'input deve essere generato l'n-gramma. Valori consentiti: front, back
	  </td>	  
    </tr>   
    <tr>
      <td>[elision](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)</td>
      <td>#Microsoft.Azure.Search.ElisionTokenFilter</td>
	  <td>Rimuove le elisioni. Ad esempio, "l'avion" (l'aeroplano) verrà convertito in "avion" (aeroplano).</td>
	  <td>
		- articles - tipo: matrice di stringhe - un set di articoli da rimuovere, valore predefinito: un elenco vuoto
	  </td>	  
    </tr>
    <tr>
      <td>[german_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.GermanNormalizationTokenFilter</td>
	  <td>Normalizza i caratteri del tedesco in base all'euristica dell'[algoritmo snowball German2](http://snowball.tartarus.org/algorithms/german2/stemmer.html)</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[hindi_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.HindiNormalizationTokenFilter</td>
	  <td>Normalizza il testo in hindi per rimuovere alcune differenze nelle variazioni di ortografia</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[indic_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.IndicNormalizationTokenFilter</td>
	  <td>Normalizza la rappresentazione Unicode del testo nelle lingue indiane</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[keep](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeepTokenFilter</td>
	  <td>Filtro di token che conserva solo i token con testo contenuto in un elenco specificato di parole</td>
	  <td>
		- keepWords - tipo: matrice di stringhe - elenco di parole da conservare, valore predefinito: un elenco vuoto - keepWordsCase - tipo: bool - se true, converte prima in minuscolo tutte le parole, valore predefinito: false
	  </td>	  
    </tr>
    <tr>
      <td>[keep_types](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/TypeTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeepTypesTokenFilter</td>
	  <td>Conserva i token i cui tipi vengono visualizzati nell'elenco specificato di tipi consentiti</td>
	  <td>
		- types - tipo: matrice di stringhe - elenco di tipi da conservare
	  </td>	  
    </tr>
    <tr>
      <td>[keyword_marker](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeywordMarkerTokenFilter</td>
	  <td>Contrassegna i termini come parole chiave</td>
	  <td>
		- keywords - tipo: matrice di stringhe - elenco di parole da contrassegnare come parole chiave, valore predefinito: un elenco vuoto - ignoreCase - tipo: bool - se true, converte prima in minuscolo tutte le parole, valore predefinito: false
	  </td>	  
    </tr>
    <tr>
      <td>[keyword_repeat](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeywordRepeatTokenFilter</td>
	  <td>Emette ogni token in ingresso due volte come parola chiave e una volta non come parola chiave</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[kstem](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)</td>
      <td>#Microsoft.Azure.Search.KStemTokenFilter</td>
	  <td>Filtro kstem dalle prestazioni elevate per l'inglese</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[length](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)</td>
      <td>#Microsoft.Azure.Search.LengthTokenFilter</td>
	  <td>Rimuove le parole troppo lunghe o troppo corte</td>
	  <td>
		- min - tipo: int - numero minimo, valore predefinito: 0 - max - tipo: int - numero massimo, valore predefinito: valore intero massimo
	  </td>	  
    </tr>
    <tr>
      <td>[limit](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)</td>
      <td>#Microsoft.Azure.Search.LimitTokenFilter</td>
	  <td>Limita il numero di token durante l'indicizzazione</td>
	  <td>
		- maxTokenCount - tipo: int - numero massimo di token da generare, valore predefinito: 1 - consumeAllTokens - tipo: bool - indica se devono essere utilizzati tutti i token dall'input anche se viene raggiunto maxTokenCount, valore predefinito: false
	  </td>	  
    </tr>
    <tr>
      <td>[lowercase](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)</td>
      <td>#Microsoft.Azure.Search.LowercaseTokenFilter</td>
	  <td>Normalizza il testo dei token in lettere minuscole</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[nGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.NGramTokenFilter</td>
	  <td>Genera n-grammi di una o più dimensioni specificate</td>
	  <td>
		- minGram - tipo: int - valore predefinito: 1 - maxGram - tipo: int - valore predefinito: 2
	  </td>	  
    </tr>
    <tr>
      <td>[pattern_capture](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.PatternCaptureTokenFilter</td>
	  <td>Usa i regex Java per emettere più token - uno per ogni gruppo Capture in uno o più modelli</td>
	  <td>
		- patterns - tipo: matrice di stringhe - elenco di modelli con cui confrontare ogni token - preserveOriginal - tipo: bool - impostarlo su true per restituire il token originale anche se uno dei modelli corrisponde
	  </td>	  
    </tr>
    <tr>
      <td>[persian_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.PersianNormalizationTokenFilter</td>
	  <td>Applica la normalizzazione per il persiano</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[phonetic]https://lucene.apache.org/core/4_10_3/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)</td>
      <td>#Microsoft.Azure.Search.PhoneticTokenFilter</td>
	  <td>Crea i token per le corrispondenze fonetiche.</td>
	  <td>
		- encoder - tipo: stringa - Codificatore fonetico da usare, valori consentiti: metaphone, doublemetaphone, soundex, refinedsoundex, caverphone1, caverphone2, cologne, nysiis, koelnerphonetik, haasephonetik, beidermorse. Valore predefinito: metaphone - replace - tipo: bool - true se i token codificati devono sostituire i token originali, false se devono essere aggiunti come sinonimi, valore predefinito: true
	  </td>	  
    </tr>
    <tr>
      <td>[porter_stem](http://lucene.apache.org/core/4_10_3/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)</td>
      <td>#Microsoft.Azure.Search.PorterStemTokenFilter</td>
	  <td>Trasforma il flusso di token in base all'[algoritmo di stemming Porter](http://tartarus.org/~martin/PorterStemmer/)</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[reverse](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)</td>
      <td>#Microsoft.Azure.Search.ReverseTokenFilter</td>
	  <td>Inverte la stringa di token</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[scandinavian_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.ScandinavianNormalizationTokenFilter</td>
	  <td>Normalizza l'uso dei caratteri scandinavi intercambiabili</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[scandinavian_folding](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)</td>
      <td>#Microsoft.Azure.Search.ScandinavianFoldingNormalizationTokenFilter</td>
	  <td>Riduce i caratteri scandinavi åÅäæÄÆ->a and öÖøØ->o. Distingue anche l'uso delle vocali doppie aa, ae, ao, oe e oo, lasciando solo la prima.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[shingle](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)</td>
      <td>#Microsoft.Azure.Search.ShingleTokenFilter</td>
	  <td>Crea combinazioni di token come un unico token</td>
	  <td>
		- maxShingleSize - tipo: int - valore predefinito: 2 - minShingleSize - tipo: int - valore predefinito: 2 - outputUnigrams - tipo: bool - se true, il flusso di output conterrà i token di input (unigrammi) oltre agli shingle, valore predefinito: true - outputUnigramsIfNoShingles - tipo: bool - se true, esegue l'override del comportamento outputUnigrams==false ogni volta che non sono disponibili shingle, valore predefinito: false - tokenSeparator - tipo: stringa - stringa da usare quando si uniscono token adiacenti per formare uno shingle, valore predefinito: " " - filterToken - tipo: stringa - stringa da inserire per ogni posizione in cui non sono presenti token, valore predefinito: "_"
	  </td>	  
    </tr>
    <tr>
      <td>[snowball](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)</td>
      <td>#Microsoft.Azure.Search.SnowballTokenFilter</td>
	  <td>Filtro di token di stemming Snowball</td>
	  <td>
		- language - tipo: stringa - valori consentiti: Armenian, Basque, Catalan, Danish, Dutch, English, Finnish, French, German, German2, Hungarian, Italian, Kp, Lovins, Norwegian, Porter, Portuguese, Romanian, Russian, Spanish, Swedish, Turkish
	  </td>	  
    </tr>
	<tr>
      <td>[sorani_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.SoraniNormalizationTokenFilter</td>
	  <td>Normalizza la rappresentazione Unicode del testo sorani</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>stemmer</td>
      <td>#Microsoft.Azure.Search.StemmerTokenFilter</td>
	  <td>Filtro di stemming specifico della lingua</td>
	  <td>
		- language - tipo: stringa - valori consentiti: - [arabic](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html) - [armenian](http://snowball.tartarus.org/algorithms/armenian/stemmer.html) - [basque](http://snowball.tartarus.org/algorithms/basque/stemmer.html) - [brazilian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html) - [bulgarian](http://members.unine.ch/jacques.savoy/Papers/BUIR.pdf) - [catalan](http://snowball.tartarus.org/algorithms/catalan/stemmer.html) - [czech](http://portal.acm.org/citation.cfm?id=1598600) - [danish](http://snowball.tartarus.org/algorithms/danish/stemmer.html) - [dutch](http://snowball.tartarus.org/algorithms/dutch/stemmer.html) - [dutch_kp](http://snowball.tartarus.org/algorithms/kraaij_pohlmann/stemmer.html) - [english](http://snowball.tartarus.org/algorithms/porter/stemmer.html) - [light_english](http://ciir.cs.umass.edu/pubfiles/ir-35.pdf) - [minimal_english](http://www.researchgate.net/publication/220433848_How_effective_is_suffixing) - [possessive_english](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html) - [porter2](http://snowball.tartarus.org/algorithms/english/stemmer.html) - [lovins](http://snowball.tartarus.org/algorithms/lovins/stemmer.html) - [finnish](http://snowball.tartarus.org/algorithms/finnish/stemmer.html) - [light_finnish](http://clef.isti.cnr.it/2003/WN_web/22.pdf) - [french](http://snowball.tartarus.org/algorithms/french/stemmer.html) - [light_french](http://dl.acm.org/citation.cfm?id=1141523) - [minimal_french](http://dl.acm.org/citation.cfm?id=318984) - [galician](http://bvg.udc.es/recursos_lingua/stemming.jsp) - [minimal_galician](http://bvg.udc.es/recursos_lingua/stemming.jsp) - [german](http://snowball.tartarus.org/algorithms/german/stemmer.html) - [german2](http://snowball.tartarus.org/algorithms/german2/stemmer.html) - [light_german](http://dl.acm.org/citation.cfm?id=1141523) - [minimal_german](http://members.unine.ch/jacques.savoy/clef/morpho.pdf) - [greek](http://sais.se/mthprize/2007/ntais2007.pdf) - [hindi](http://computing.open.ac.uk/Sites/EACLSouthAsia/Papers/p6-Ramanathan.pdf) - [hungarian](http://snowball.tartarus.org/algorithms/hungarian/stemmer.html) - [light_hungarian](http://dl.acm.org/citation.cfm?id=1141523&amp;dl=ACM&amp;coll=DL&amp;CFID=179095584&amp;CFTOKEN=80067181) - [indonesian](http://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf) - [irish](http://snowball.tartarus.org/otherapps/oregan/intro.html) - [italian](http://snowball.tartarus.org/algorithms/italian/stemmer.html) - [light_italian](http://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf) - [sorani](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html) - [latvian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html) - [norwegian](http://snowball.tartarus.org/algorithms/norwegian/stemmer.html) - [light_norwegian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html) - [minimal_norwegian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html) - [light_nynorsk](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html) - [minimal_nynorsk](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html) - [portuguese](http://snowball.tartarus.org/algorithms/portuguese/stemmer.html) - [light_portuguese](http://dl.acm.org/citation.cfm?id=1141523&amp;dl=ACM&amp;coll=DL&amp;CFID=179095584&amp;CFTOKEN=80067181) - [minimal_portuguese](http://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf) - [portuguese_rslp](http://www.inf.ufrgs.br//~viviane/rslp/index.htm) - [romanian](http://snowball.tartarus.org/algorithms/romanian/stemmer.html) - [russian](http://snowball.tartarus.org/algorithms/russian/stemmer.html) - [light_russian](http://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf) - [spanish](http://snowball.tartarus.org/algorithms/spanish/stemmer.html) - [light_spanish](http://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf) - [swedish](http://snowball.tartarus.org/algorithms/swedish/stemmer.html) - [light_swedish](http://clef.isti.cnr.it/2003/WN_web/22.pdf) - [turkish](http://snowball.tartarus.org/algorithms/turkish/stemmer.html)
	  </td>	  
    </tr>
	<tr>
      <td>[stemmer_override](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)</td>
      <td>#Microsoft.Azure.Search.StemmerOverrideTokenFilter</td>
	  <td>I termini sottoposti a stemming nel dizionario verranno contrassegnati come parole chiave in modo che non vengano sottoposti a stemming con gli stemmer lungo la catena. Da inserire prima dei filtri di stemming.</td>
	  <td>
		- rules - tipo: matrice di stringhe - regole di stemming nel formato seguente "word => stem", ad esempio "ran => run", valore predefinito: un elenco vuoto
	  </td>	  
    </tr>
	<tr>
      <td>[stop](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)</td>
      <td>#Microsoft.Azure.Search.StopTokenFilter</td>
	  <td>Rimuove le parole non significative da un flusso di token</td>
	  <td>
		- stopwords - tipo: matrice di stringhe - elenco di parole non significative, valore predefinito: un elenco vuoto. - stopwords_list - tipo: stringa - elenco predefinito di parole non significative, valori consentiti: _arabic_, _armenian_, _basque_, _brazilian_, _bulgarian_, _catalan_, _czech_, _danish_, _dutch_, _english_, _finnish_, _french_, _galician_, _german_, _greek_, _hindi_, _hungarian_, _indonesian_, _irish_, _italian_, _latvian_, _norwegian_, _persian_, _portuguese_, _romanian_, _russian_, _sorani_, _spanish_, _swedish_, _thai_, _turkish_, default: _english_ - ignoreCase - tipo: bool - se true, tutte le parole vengono prima convertite in lettere minuscole, valore predefinito: false - removeTrailing - tipo: bool - se true, ignora l'ultimo termine di ricerca se è una parola non significativa, valore predefinito: true
	  </td>	  
    </tr>
	<tr>
      <td>[synonym](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)</td>
      <td>#Microsoft.Azure.Search.SynonymTokenFilter</td>
	  <td>Associa uno o più sinonimi in un flusso di token</td>
	  <td>
		- synonyms - tipo: matrice di stringhe - elenco di sinonimi in uno dei due formati seguenti: - incredible, unbelievable, fabulous => amazing - tutti i termini a sinistra del simbolo => verranno sostituiti con tutti i termini sulla destra - incredible, unbelievable, fabulous, amazing - elenco delimitato da virgole di parole equivalenti. Impostare l'opzione expand per modificare l'interpretazione di questo elenco - ignoreCase - tipo: bool - elimina dall'input la distinzione tra maiuscole e minuscole per la corrispondenza, valore predefinito: false - expand - tipo: bool - - se true, verrà eseguito il mapping di tutte le parole nell'elenco di sinonimi (se la notazione => non viene usata) l'una all'altra. L'elenco seguente: incredible, unbelievable, fabulous, amazing equivale a: incredible, unbelievable, fabulous, amazing => incredible, unbelievable, fabulous, amazing - se false, l'elenco seguente: incredible, unbelievable, fabulous, amazing equivarrà a: incredible, unbelievable, fabulous, amazing => incredible   
	  </td>	  
    </tr>
	<tr>
      <td>[trim](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)</td>
      <td>#Microsoft.Azure.Search.TrimTokenFilter</td>
	  <td>Taglia gli spazi vuoti iniziali e finali dai token</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>[truncate](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.TruncateTokenFilter</td>
	  <td>Tronca i termini a una lunghezza specifica</td>
	  <td>
		- length - tipo: int - opzione obbligatoria
	  </td>	  
    </tr>
	<tr>
      <td>[unique](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.UniqueTokenFilter</td>
	  <td>Filtra i token con lo stesso testo del token precedente</td>
	  <td>
		- onlyOnSamePosition - tipo: bool - se impostato, rimuove i duplicati solo nella stessa posizione, valore predefinito: true
	  </td>	  
    </tr>
    <tr>
      <td>[uppercase](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)</td>
      <td>#Microsoft.Azure.Search.UppercaseTokenFilter</td>
	  <td>Normalizza il testo dei token in lettere maiuscole</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>[word_delimiter](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)</td>
      <td>#Microsoft.Azure.Search.WordDelimiterTokenFilter</td>
	  <td>Divide le parole in parole secondarie ed esegue trasformazioni facoltative sui gruppi di parole secondarie</td>
	  <td>
		- generateWordParts - tipo: bool - attiva la generazione di parti di parole, ad esempio "AzureSearch" -> "Azure" "Search", valore predefinito: true - generateNumberParts - tipo: bool - attiva la generazione di parole secondarie numeriche, valore predefinito: true - catenateWords - tipo: bool - attiva la concatenazione del numero massimo di esecuzioni di parti di parole, ad esempio "Azure-Search" -> "AzureSearch", valore predefinito: false - catenateNumbers - tipo: bool - attiva la concatenazione del numero massimo di esecuzioni di parti di numeri, ad esempio "1-2" -> "12", valore predefinito: false - catenateAll - tipo: bool - attiva la concatenazione di tutte le parti di parole secondarie, ad esempio "Azure-Search-1" -> "AzureSearch1", valore predefinito: false - splitOnCaseChange - tipo: bool - se true, divide le parole in caseChange, ad esempio "AzureSearch" -> "Azure" "Search", valore predefinito: true - preserveOriginal - le parole originali vengono mantenute e aggiunte all'elenco di parole secondarie, valore predefinito: false - splitOnNumerics - tipo: bool - se true, divide in numeri, ad esempio, "Azure1Search" -> "Azure" "8" "Search", valore predefinito: true - stemEnglishPossessive - tipo: bool - la lettera "'s" finale viene rimossa per ogni parola secondaria, valore predefinito: true - protectedWords - tipo: matrice di stringhe - token per evitare la delimitazione, valore predefinito: un elenco vuoto
	  </td>	  
    </tr>
  </tbody>
</table>

**Vedere anche** [API REST di Ricerca di Azure](http://msdn.microsoft.com/library/azure/dn798935.aspx) su MSDN <br/> [Creare un indice (API di Ricerca di Azure)](http://msdn.microsoft.com/library/azure/dn798941.aspx) su MSDN<br/>

<!---HONumber=AcomDC_0224_2016-->