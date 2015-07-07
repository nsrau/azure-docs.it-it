<properties
   pageTitle="Analizzare ed elaborare documenti JSON con Hive in HDInsight | Microsoft Azure"
   description="Informazioni su come usare e analizzare i documenti JSON usando Hive in HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/14/2015"
   ms.author="rashimg"/>


# Informazioni su come elaborare e analizzare i documenti JSON in Hive

JSON è uno dei formati più usati sul Web. Questa esercitazione permette di risolvere uno dei dubbi più frequenti degli utenti di Hive, ovvero come usare i documenti JSON in Hive e quindi eseguire analisi su tali documenti.

##Esempio di JSON

Ad esempio, si supponga di avere a disposizione il documento JSON riportato di seguito. L'obiettivo consiste nell'essere in grado di analizzare questo documento JSON e quindi di eseguire query nel documento, ad esempio la ricerca di un valore su una chiave, l'aggregazione e così via.

	  {
        "StudentId": "trgfg-5454-fdfdg-4346",
	    "Grade": 7,
        "StudentDetails": [
          {
            "FirstName": "Peggy",
            "LastName": "Williams",
            "YearJoined": 2012
          }
        ],
        "StudentClassCollection": [
          {
            "ClassId": "89084343",
            "ClassParticipation": "Satisfied",
            "ClassParticipationRank": "High",
            "Score": 93,
            "PerformedActivity": false
          },
          {
            "ClassId": "78547522",
            "ClassParticipation": "NotSatisfied",
            "ClassParticipationRank": "None",
            "Score": 74,
            "PerformedActivity": false
          },
          {
            "ClassId": "78675563",
            "ClassParticipation": "Satisfied",
            "ClassParticipationRank": "Low",
            "Score": 83,
            "PerformedActivity": true
          }
        ] 
      }

##Rendere flat un documento JSON (passaggio necessario solo se si usa Pretty JSON)

Prima di usare gli operatori Hive per eseguire l'analisi, è necessario eseguire l'elaborazione preliminare del documento JSON, in modo che sia pronto per essere usato da Hive.

Tutte le opzioni elencate nella sezione seguente presuppongono che il documento JSON si trovi su un'unica riga. In altri termini, è necessario ridurre il documento JSON a una stringa, in modo che possa essere usato dagli operatori Hive.

**Se il documento JSON è già flat e l'intero documento si trova su una riga, è possibile saltare questo passaggio e procedere alla sezione successiva relativa all'analisi dei dati JSON.**

Si supponga che nell'archivio BLOB di Azure sia presente un documento JSON non flat su più righe nella cartella */json/input/* del contenitore predefinito. Il codice seguente crea una tabella jsonexample che fa riferimento al documento JSON originale non flat.

    drop table jsonexample;
    CREATE EXTERNAL TABLE jsonexample (textcol string) stored as textfile location '/json/input/';

Verrà quindi creata una nuova tabella denominata one_line_json, che farà riferimento al documento JSON flat e verrà archiviata nell'archivio BLOB di Azure nella cartella */json/temp/* del contenitore predefinito.

    drop table if exists one_line_json;
    create external table one_line_json
    (
      json_body string
    )
    stored as textfile location '/json/temp';

Questa tabella verrà infine popolata con i dati JSON flat.

    insert overwrite table one_line_json
    select concat_ws(' ',collect_list(textcol)) as singlelineJSON 
      from (select INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE,textcol from jsonexample distribute by INPUT__FILE__NAME sort by BLOCK__OFFSET__INSIDE__FILE ) 
      x group by INPUT__FILE__NAME;

In questo momento, se si esamina la tabella one_line_json appena creata, si noterà che include il documento JSON su una sola riga.

    select * from one_line_json;

Questo è l'output della query:

![Rendere flat il documento JSON.][image-hdi-hivejson-flatten]

##Opzioni per l'analisi dei documenti JSON in Hive

Dopo avere inserito il documento JSON in una tabella con una sola colonna, è possibile usare Hive per eseguire query sui dati. Hive offre tre meccanismi diversi per l'esecuzione di query nei documenti JSON:

1.	Uso della funzione definita dall'utente (UDF, User Defined Function): get_json_object
2.	Uso della funzione definita dall'utente: json_tuple
3.	Uso di SerDe personalizzato

Ogni approccio verrà esaminato nel dettaglio.

##Funzione definita dall'utente get_json_object
Hive offre una funzione definita dall'utente predefinita denominata [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object), che permette di eseguire query JSON in fase di esecuzione. Questo metodo accetta due argomenti, ovvero il nome della tabella e il nome del metodo che include il documento JSON flat e il campo JSON da analizzare. L'esempio seguente illustra il funzionamento di questa funzione definita dall'utente.

Ottenere il nome e il cognome di ogni studente

    select get_json_object(one_line_json.json_body, '$.StudentDetails.FirstName'), get_json_object(one_line_json.json_body, '$.StudentDetails.LastName') from one_line_json;

Questo è l'output ottenuto quando si esegue la query nella finestra della console.

![Funzione definita dall'utente get_json_object][image-hdi-hivejson-getjsonobject]

Questa funzione definita dall'utente presenta alcune limitazioni.


- Una delle limitazioni principali è dovuta al fatto che questa funzione definita dall'utente non offre prestazioni elevate, poiché ogni campo della query richiede una nuova analisi della query, influendo negativamente sulle prestazioni.
- Secondariamente, get_json_object() restituisce una rappresentazione di stringa di una matrice. Per convertirla in una matrice Hive, sarà necessario usare le espressioni regolari per sostituire le parentesi quadre (‘[' e ']’), quindi chiamare una suddivisione per ottenere la matrice.


Il wiki relativo a Hive consiglia quindi di usare json_tuple, come illustrato più avanti.


##Funzione definita dall'utente json_tuple

L'altra funzione definita dall'utente fornita da Hive è denominata [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple) e offre prestazioni migliori rispetto a [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Questo metodo accetta un insieme di chiavi e una stringa JSON e restituisce una tupla di valori usando una funzione. L'esempio seguente illustra il funzionamento di questa funzione definita dall'utente.

Ottenere l'ID e i voti dello studente dal documento JSON

    select q1.StudentId, q1.Grade 
      from one_line_json jt
      LATERAL VIEW json_tuple(jt.json_body, 'StudentId', 'Grade') q1
        as StudentId, Grade;

È possibile usare la sintassi di tipo [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) in Hive, che permette a json_tuple di creare una tabella virtuale applicando la funzione UDT a ogni riga della tabella originale.

Questo è l'output dello script nella console di Hive:

![Funzione definita dall'utente json_tuple][image-hdi-hivejson-jsontuple]

Uno degli svantaggi principali di questa funzione definita dall'utente è costituito dal fatto che i documenti JSON complessi diventano troppo difficili da gestire a causa dell'uso ripetuto di LATERAL VIEW. Questa funzione definita dall'utente, infatti, non è in grado di gestire documenti JSON annidati.

##Uso di SerDe personalizzato

SerDe è la **scelta migliore** per l'analisi di documenti JSON annidati, poiché è possibile definire lo schema JSON, semplificando molto l'esecuzione di query in questi documenti.

Verrà illustrato l'uso di uno dei SerDe più diffusi, sviluppato da [rcongiu](https://github.com/rcongiu).

Passaggio 1: Assicurarsi che [Java SE Development Kit 7u55 JDK 1.7.0_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR) sia installato. Nota: se JDK 1.8 è installato, non funzionerà con questo SerDe.


- Se si usa la distribuzione Windows di HDInsight, scegliere la versione Windows x64 del JDK.
- Al termine dell'installazione, passare a Pannello di controllo --> Aggiungi Variabili di ambiente e aggiungere una nuova variabile di ambiente JAVA_HOME facendo riferimento a C:\Programmi\Java\jdk1.7.0_55 o al percorso di installazione del JDK. Le schermate seguenti illustrano la procedura di configurazione della variabile di ambiente.

![Configurazione dei valori di configurazione corretti per JDK][image-hdi-hivejson-jdk]

Passaggio 2: Selezionare [questo](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip) collegamento e scaricare Maven 3.3.1. Decomprimere l'archivio in cui archiviare i file binari. In questo caso verrà decompresso in C:\Programmi\Maven. Aggiungere la cartella bin al percorso scegliendo Pannello di controllo-->Modificare le variabili di ambiente relative al sistema per l'account. La schermata seguente illustra la procedura.

![Configurazione di Maven][image-hdi-hivejson-maven]

Passaggio 3: Clonare il progetto dal sito GitHub [Hive-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master). Per eseguire questa operazione, fare clic sul pulsante "Download Zip", come mostrato nella schermata seguente.

![Clonazione del progetto][image-hdi-hivejson-serde]

Passaggio 4: Passare alla cartella in cui è stato scaricato il pacchetto, quindi digitare "mvn package". Verranno creati i file jar necessari, che verranno quindi copiati nel cluster.

Passaggio 5: Passare alla cartella di destinazione nella cartella radice in cui è stato scaricato il pacchetto. Caricare il file json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar nel nodo head del cluster. È in genere consigliabile inserirlo sotto la cartella dei file binari di Hive, ad esempio C:\apps\dist\hive-0.13.0.2.1.11.0-2316\bin o in una cartella analoga.
 
Passaggio 6: Nel prompt di Hive digitare "add jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar". Poiché in questo esempio il file jar si trova nella cartella C:\apps\dist\hive-0.13.x\bin, è possibile aggiungere direttamente il file jar con il nome, come illustrato di seguito:

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

![Aggiunta di JAR al progetto][image-hdi-hivejson-addjar]

È ora possibile usare SerDe per eseguire le query nel documento JSON.

Creare prima di tutto la tabella con lo schema del documento JSON. Si noti che è possibile gestire tipi più avanzati, incluse mappe, matrici e costrutti. Digitare il codice seguente nella console di Hive, in modo che Hive crei una tabella denominata json_table, che è in grado di leggere il documento JSON in base allo schema seguente.

    drop table json_table;
    create external table json_table (
      StudentId string, 
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) row format serde 'org.openx.data.jsonserde.JsonSerDe'
    location '/json/temp';

Dopo avere definito lo schema, è possibile eseguire alcuni esempi per verificare la procedura di esecuzione delle query nel documento JSON mediante Hive:

a) Elencare il nome e il cognome dello studente

    select StudentDetails.FirstName, StudentDetails.LastName from json_table;

Questo è il risultato ottenuto dalla console di Hive.

![Query SerDe 1][image-hdi-hivejson-serde_query1]

b) Questa query calcola la somma dei punteggi del documento JSON

    select sum(scores)
    from json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;
       
La query precedente usa la funzione definita dall'utente di tipo [lateral view explode](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) per espandere la matrice di punteggi, in modo che sia possibile riepilogarli.

Questo è l'output ottenuto dalla console di Hive.

![Query SerDe 2][image-hdi-hivejson-serde_query2]

c) Individuare le materie in cui un determinato studente ha ottenuto un punteggio superiore a 80. Usare la query seguente: select jt.StudentClassCollection.ClassId da json_table jt lateral view explode(jt.StudentClassCollection.Score) collection as score where score > 80;
      
La query precedente restituisce una matrice Hive, a differenza di get_json_object che restituisce una stringa.

![Query SerDe 3][image-hdi-hivejson-serde_query3]

Se si vogliono ignorare documenti JSON non validi, come illustrato nella [pagina wiki](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) di questo SerDe, è possibile ottenere questo risultato digitando il codice seguente:

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");


##Altre opzioni
Oltre alle opzioni elencate di seguito, è possibile scrivere codice personalizzato usando Python o altri linguaggi specifici per lo scenario in uso. Dopo avere preparato lo script Python, vedere [questo articolo][hdinsight-python] sull'esecuzione di codice Python personalizzato con Hive.

##Riepilogo
In conclusione, il tipo di operatore JSON in Hive scelto dipende dallo scenario. Se è disponibile un documento JSON semplice ed è necessario eseguire ricerche in un solo campo, è possibile scegliere di usare la funzione definita dall'utente get_json_object di Hive. Se è necessario cercare più di una chiave, sarà possibile usare json_tuple. Se è disponibile un documento annidato, è consigliabile usare il SerDe JSON.

Il team di HDInsight si impegna per semplificare sempre più l'uso di più formati in modalità nativa in Hive senza richiedere impegno eccessivo da parte dell'utente. Questa esercitazione verrà aggiornata con altri dettagli non appena saranno disponibili.

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png
 

<!---HONumber=62-->