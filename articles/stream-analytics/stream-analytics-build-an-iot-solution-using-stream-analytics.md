<properties 
	pageTitle="Creare una soluzione IoT con analisi di flusso | Microsoft Azure" 
	description="Esercitazione di introduzione alla soluzione IoT di analisi di flusso nello scenario del casello"
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"
/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="07/27/2016" 
	ms.author="jeffstok"
/>

# Creare una soluzione IoT con analisi di flusso

## Introduzione

In questa esercitazione si apprenderà come acquisire informazioni dettagliate in tempo reale a partire dai dati con l'analisi di flusso di Azure. Il servizio di elaborazione del flusso di Azure consente agli sviluppatori di gestire facilmente lo spazio dei dati in movimento combinando i flussi dei dati come eventi generati da clickstream, log e dispositivi con record cronologici o dati di riferimento per estrarre informazioni dettagliate aziendali in modo facile e veloce. L'analisi di flusso di Azure, un servizio di calcolo di flusso completamente gestito e in tempo reale ospitato in Microsoft Azure, fornisce resilienza predefinita, bassa latenza e scalabilità per garantire operatività in pochi minuti.

Dopo aver completato questa esercitazione, si sarà in grado di:

-   Familiarizzare con il portale di analisi di flusso di Azure.
-   Configurare e distribuire un processo di flusso.
-   Articolare i problemi reali e risolverli con il linguaggio di query di analisi di flusso.
-   Sviluppare in tutta sicurezza delle soluzioni di flusso per i clienti con l'analisi di flusso di Azure.
-   Usare l'esperienza di monitoraggio e registrazione per risolvere i problemi.

## Prerequisiti

Sono necessari i prerequisiti seguenti per svolgere correttamente questa esercitazione.

-   La versione più recente di [Azure PowerShell](../powershell-install-configure.md)
-   Visual Studio 2015 o la versione gratuita [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
-   [Sottoscrizione di Azure](https://azure.microsoft.com/pricing/free-trial/)
-   Privilegi di amministratore nel computer
-   Scaricare [TollApp.zip](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip) dall'Area download Microsoft.
-   Facoltativo: codice sorgente per il generatore di eventi TollApp in [GitHub](https://github.com/streamanalytics/samples/tree/master/TollApp)

## Presentazione dello scenario: il casello


I caselli sono una realtà conosciuta, presente su autostrade, ponti e gallerie di tutto il mondo. Ogni casello comprende una serie di cabine, manuali quando ci si ferma per pagare il pedaggio al personale addetto o automatiche quando il sensore posizionato sulla cabina esegue la scansione della scheda RFID, posta sul parabrezza del veicolo, al momento del passaggio nel casello. È semplice visualizzare il passaggio dei veicoli nei caselli come un flusso di eventi, sui quali è possibile eseguire alcune operazioni interessanti.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## Dati di ingresso

I flussi di dati su cui si lavora sono due e sono prodotti dai sensori installati all'entrata e all'uscita del casello, oltre a un set di dati di ricerca statico con dati di registrazione del veicolo.

### Flusso di dati di ingresso

Il flusso di dati di ingresso contiene informazioni sulle automobili che entrano nel casello.
  
  
| ID casello | Tempo ingresso | Targa | Stato | Assicurarsi | Modello | Tipo veicolo | Peso veicolo | Casello | Tag |
|---------|-------------------------|--------------|-------|--------|---------|--------------|----------------|------|-----------|
| 1 | 2014-09-10 12:01:00.000 | JNB 7001 | NY | Honda | CRV | 1 | 0 | 7 | |
| 1 | 2014-09-10 12:02:00.000 | YXZ 1001 | NY | Toyota | Camry | 1 | 0 | 4 | 123456789 |
| 3 | 2014-09-10 12:02:00.000 | ABC 1004 | CT | Ford | Taurus | 1 | 0 | 5 | 456789123 |
| 2 | 2014-09-10 12:03:00.000 | XYZ 1003 | CT | Toyota | Corolla | 1 | 0 | 4 | |
| 1 | 2014-09-10 12:03:00.000 | BNJ 1007 | NY | Honda | CRV | 1 | 0 | 5 | 789123456 |
| 2 | 2014-09-10 12:05:00.000 | CDE 1007 | NJ | Toyota | 4x4 | 1 | 0 | 6 | 321987654 |
  

Ecco una breve descrizione delle colonne:
  
  
| ID casello | ID della cabina del casello che identifica in modo univoco una cabina del casello |
|--------------|----------------------------------------------------------------|
| Tempo ingresso | Data e ora di ingresso del veicolo nella corsia della cabina del casello in UTC |
| Targa | Numero di targa del veicolo |
| Stato | Uno stato negli Stati Uniti |
| Assicurarsi | Il produttore dell'automobile |
| Modello | Numero di modello dell'automobile |
| Tipo veicolo | 1 per veicoli passeggeri e 2 per veicoli commerciali |
| Peso veicolo | Peso del veicolo in tonnellate, 0 per veicoli passeggeri |
| Casello | Il valore del pedaggio in USD |
| Tag | e-Tag sull'automobile che automatizza il pagamento, lasciato vuoto quando il pagamento viene effettuato manualmente |


### Flusso di dati di uscita

Il flusso di dati di uscita contiene informazioni sulle automobili che lasciano il casello.
  
  
| **ID casello** | **Tempo ingresso** | **Targa** |
|------------|------------------------------|------------------|
| 1 | 2014-09-10T12:03:00.0000000Z | JNB 7001 |
| 1 | 2014-09-10T12:03:00.0000000Z | YXZ 1001 |
| 3 | 2014-09-10T12:04:00.0000000Z | ABC 1004 |
| 2 | 2014-09-10T12:07:00.0000000Z | XYZ 1003 |
| 1 | 2014-09-10T12:08:00.0000000Z | BNJ 1007 |
| 2 | 2014-09-10T12:07:00.0000000Z | CDE 1007 |

Ecco una breve descrizione delle colonne:
  
  
| Colonna | Descrizione |
|--------------|-----------------------------------------------------------------|
| ID casello | ID della cabina del casello che identifica in modo univoco una cabina del casello |
| Tempo ingresso | Data e ora di uscita del veicolo dalla corsia della cabina del casello in UTC |
| Targa | Numero di targa del veicolo |

### Dati di registrazione del veicolo commerciale

Viene riportato uno snapshot statico del database di registrazione del veicolo commerciale.
  
  
| Targa | ID registrazione | Scaduto |
|--------------|----------------|---------|
| SVT 6023 | 285429838 | 1 |
| XLZ 3463 | 362715656 | 0 |
| BAC 1005 | 876133137 | 1 |
| RIV 8632 | 992711956 | 0 |
| SNY 7188 | 592133890 | 0 |
| ELH 9896 | 678427724 | 1 |                      

Ecco una breve descrizione delle colonne:
  
  
| Colonna | Descrizione |
|--------------|-----------------------------------------------------------------|
| Targa | Numero di targa del veicolo |
| ID registrazione | ID registrazione |
| Scaduto | 0 se la registrazione del veicolo è attiva, 1 se la registrazione è scaduta |


## Impostare l'ambiente per l'analisi di flusso di Azure

Per eseguire questa esercitazione, è necessaria una sottoscrizione Microsoft Azure. Microsoft offre la versione di prova gratuita per i servizi Microsoft Azure come illustrato di seguito.

Se non si dispone di un account Azure, è possibile richiedere una versione di prova gratuita visitando <http://azure.microsoft.com/pricing/free-trial/> .

Nota: per iscriversi a una versione di prova gratuita, è necessario disporre di un dispositivo mobile in grado di ricevere messaggi di testo e di una carta di credito valida.

Assicurarsi di seguire i passaggi nella sezione "Eliminare l'account di Azure" alla fine di questa esercitazione per sfruttare al meglio il credito Azure gratuito di \\$200.

## È obbligatorio eseguire il provisioning delle risorse di Azure per l'esercitazione

Questa esercitazione richiede 2 Hub eventi di Azure per ricevere i flussi di dati "Entry" ed "Exit". Viene utilizzato il database SQL di Azure per restituire i risultati dei processi di analisi di flusso. Si usa Archiviazione di Azure anche per archiviare i dati di riferimento sulla registrazione del veicolo.

Lo script Setup.ps1 nella cartella TollApp in GitHub può essere usato per creare tutte le risorse necessarie. Per ottimizzare i tempi, si consiglia di eseguirlo. Se si desidera sapere di più sulla configurazione di queste risorse nel portale di Azure, consultare l'appendice sulla configurazione delle risorse dell'esercitazione nel portale di Azure

Scaricare e salvare la cartella e i file di supporto di [TollApp](http://download.microsoft.com/download/D/4/A/D4A3C379-65E8-494F-A8C5-79303FD43B0A/TollApp.zip).

Aprire una finestra di "Microsoft Azure PowerShell" **COME AMMINISTRATORE**. Se non si ha ancora Azure PowerShell, seguire le istruzioni per installarlo in [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

Windows blocca automaticamente i file ps1, dll ed exe scaricati da Internet. È necessario impostare i criteri di esecuzione prima di eseguire lo script. Assicurarsi che la finestra di Azure PowerShell sia in esecuzione come amministratore. Eseguire "Set-ExecutionPolicy unrestricted". Quando richiesto, digitare "Y".

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Eseguire Get-ExecutionPolicy per assicurarsi che il comando sia stato eseguito correttamente.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

Passare alla directory con gli script e l'applicazione del generatore.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

Digitare ".\\Setup.ps1" per configurare l'account Azure, creare e configurare tutte le risorse necessarie, quindi avviare la generazione di eventi. Lo script seleziona in modo casuale un'area per creare le risorse. Se si desidera specificare in modo esplicito l'area, è possibile passare il parametro -location come illustrato nell'esempio riportato di seguito:

**.\\Setup.ps1 -location "Stati Uniti centrali"**

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

Lo script apre quindi la pagina di accesso a Microsoft Azure. Immettere le credenziali dell'account.

Notare che se l'account ha accesso a più sottoscrizioni, viene richiesto di immettere il nome della sottoscrizione che si desidera usare per l'esercitazione.

L'esecuzione dello script può richiedere qualche minuto. Una volta completato, l'output deve corrispondere allo screenshot riportato di seguito.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

Viene visualizzata anche un'altra finestra simile allo screenshot riportato sotto. Questa applicazione invia gli eventi all'EventHub e deve eseguire gli esercizi dell'esercitazione. Non si deve quindi arrestare l'applicazione o chiudere la finestra prima del termine dell'esercitazione.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

Tutte le risorse create nel portale di gestione di Azure devono poter essere visualizzate in questo momento. Andare a <https://manage.windowsazure.com> e accedere con le credenziali dell'account.

### Hub eventi

Fare clic sulla voce di menu "Bus di servizio" sul lato sinistro del portale di gestione di Azure per visualizzare l'Hub eventi creato dallo script nella sezione precedente.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

Vengono visualizzati tutti gli spazi dei nomi disponibili nella sottoscrizione. Fare clic su quello che inizia con "TollData". (TollData4637388511 in questo esempio). Fare clic sulla scheda "Hub eventi".

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

Vengono visualizzati due Hub eventi denominati *entry* ed *exit* creati in questo spazio dei nomi.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)

### Contenitore Archiviazione di Azure

Fare clic sulla voce di menu "Archiviazione" sul lato sinistro del portale di gestione di Azure per visualizzare il contenitore di archiviazione usato nel laboratorio.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)

Fare clic su quello che inizia per "tolldata" (tolldata4637388511 in questo esempio). Aprire la scheda "Contenitori" per visualizzare il contenitore creato.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

Fare clic sul contenitore "tolldata" per visualizzare il file JSON caricato con i dati di registrazione del veicolo.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image13.png)

### Database SQL di Azure

Fare clic sulla voce di menu "Database SQL" sul lato sinistro del portale di gestione di Azure per visualizzare il database SQL di Azure da usare in seguito nel laboratorio.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image14.png)

Fare clic su "TollDataDB"

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)

Copiare il nome del server senza il numero di porta (*servername*.database.windows.net, ad esempio)

## Connettersi al database da Visual Studio

Si usa Visual Studio per accedere ai risultati di query nel database di output.

Connettersi al database di Azure (la destinazione) da Visual Studio:

1) Aprire Visual Studio, fare clic su "Strumenti", quindi sulla voce di menu "Connetti al database...".

2) Se richiesto, selezionare "Microsoft SQL Server" come origine dati

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)

3) Nel campo Nome server, incollare il nome di SQL Server copiato nella sezione precedente dal portale di Azure (ovvero *servername*.database.windows.net)

4) Nel campo Autenticazione, scegliere Autenticazione di SQL Server

5) Immettere un NOME DI ACCESSO come "tolladmin" e una PASSWORD DI ACCESSO come "123toll!"

6) Scegliere TollDataDB come database

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)
    
7) Fare clic su OK.

8) Aprire Esplora server

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)
  
9) È possibile visualizzare 4 tabelle create nel database TollDataDB.
  
![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)
  
## Generatore di eventi: progetto di esempio TollApp

Lo script di PowerShell invia automaticamente gli eventi tramite il programma dell'applicazione di esempio TollApp. Non è necessario eseguire ulteriori passaggi.

Tuttavia, se si desidera conoscere i dettagli di implementazione, è possibile trovare il codice sorgente dell'applicazione TollApp in [samples/TollApp](https://github.com/streamanalytics/samples/tree/master/TollApp) di GitHub

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

##Creare un processo di analisi di flusso

Nel portale di Azure, aprire l'analisi di flusso e fare clic su "Nuovo" nell'angolo in basso a sinistra della pagina per creare un nuovo processo di analisi.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)

Fare clic su "Creazione rapida". Selezionare la stessa area in cui vengono create dallo script le altre risorse.

Per l'impostazione "Account di archiviazione di monitoraggio regionale", selezionare "Crea un nuovo account di archiviazione" e assegnare un nome univoco qualsiasi. Analisi di flusso di Azure usa questo account per archiviare informazioni di monitoraggio per tutti i processi futuri.

Fare clic su "Crea processo di Analisi di flusso" in fondo alla pagina.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## Definire le origini di input

Fare clic sul processo di analisi creato nel portale.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image23.jpg)

Aprire la scheda "Input" per definire i dati di origine.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.jpg)

Fare clic su "Aggiungere un input"

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)

Selezionare "Flusso dati" nella prima pagina

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image26.png)

Selezionare "Hub eventi" nella seconda pagina della procedura guidata.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image27.png)

Immettere "EntryStream" come alias di input.

Fare clic sull'elenco a discesa di "Hub eventi" e selezionare quello che inizia con "TollData" (ad esempio TollData9518658221).

Selezionare "entry" come nome Hub eventi e "tutti" come nome criterio Hub eventi.

Le impostazioni vengono visualizzate in questo modo:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

Andare alla pagina successiva. Selezionare i valori come JSON e codifica UTF8.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image29.png)

Fare clic su OK nella parte inferiore della finestra di dialogo per terminare la procedura guidata.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image30.jpg)

È necessario seguire la stessa sequenza di passaggi per creare il secondo input Hub eventi per il flusso con gli eventi Exit. Nella terza pagina si devono immettere i valori come nello screenshot riportato di seguito.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)

Sono ora definiti due flussi di input:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.jpg)

Successivamente, vengono aggiunti input di dati di "Riferimento" per il file BLOB con i dati di registrazione dell'automobile.

Fare clic su "Aggiungi Input". Selezionare "Dati di riferimento"

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image33.png)

Nella pagina successiva, selezionare l'account di archiviazione che inizia con "tolldata". Il nome del contenitore deve essere "tolldata" e il nome BLOB in Modello percorso deve essere "registration.json". Questo nome file è soggetto a distinzione Maiuscole/minuscole, quindi deve contenere solo lettere minuscole.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)

Selezionare i valori come illustrato di seguito nella pagina successiva e fare clic su OK per terminare la procedura guidata.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image35.png)

Ora sono definiti tutti gli input.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image36.jpg)

## Definire l'output

Andare alla scheda "Output" e fare clic su "Aggiungi un output".

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.jpg)

Scegliere "Database SQL".

Selezionare il nome del server usato in "Connettersi al database da Visual Studio". Il nome del database deve essere TollDataDB.

Immettere "tolladmin" come nome utente e "123toll!" come password. Il nome della tabella deve essere impostato su "TollDataRefJoin"

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.jpg)

## Query di analisi di flusso di Azure

La scheda Query contiene una query SQL che esegue la trasformazione dei dati in ingresso.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.jpg)

L'obiettivo di questa esercitazione è quello di rispondere a diverse domande di business relative ai dati dei caselli e di costruire query di analisi di flusso da poter usare in analisi di flusso di Azure per fornire risposte efficaci.

Prima di iniziare il primo processo di analisi di flusso di Azure, è il caso di esaminare alcuni scenari e la sintassi delle query.

## Introduzione al linguaggio di query di analisi di flusso di Azure
-----------------------------------------------------

Si immagini di dover contare il numero di veicoli che entra nel casello. Trattandosi di un flusso continuo di eventi, è essenziale stabilire un "periodo di tempo". È necessario quindi modificare la domanda e incentrarla sul "numero di veicoli che entra nel casello ogni 3 minuti". Questo viene generalmente definito come Tumbling Count.

Si osservi la query di analisi di flusso di Azure in risposta alla domanda:

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

È possibile vedere che l'analisi di flusso di Azure usa un linguaggio di query simile a SQL con alcune estensioni aggiuntive per consentire di specificare gli aspetti della query relativi al tempo.

Per altri dettagli, è possibile leggere gli articoli sui costrutti relativi a [Gestione del tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e [Windowing](https://msdn.microsoft.com/library/azure/dn835019.aspx) usati nella query da MSDN.

## Eseguire i test delle query di analisi di flusso di Azure

Una volta scritta la prima query di analisi di flusso di Azure, è necessario eseguirne il test con file di dati di esempio disponibili nella cartella TollApp nel percorso seguente:

**.. \\TollApp\\TollApp\\Data**

Questa cartella contiene i file seguenti:

1.  Entry.json

2.  Exit.json

3.  Registration.json

## Domanda 1: Numero di veicoli che entrano nella corsia della cabina del casello

Aprire il portale di gestione di Azure e passare al processo di analisi di flusso di Azure creato. Aprire la scheda Query, quindi copiare e incollare la query dalla sezione precedente.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image40.png)

Per convalidare la query con i dati di esempio, fare clic sul pulsante Test. Nella finestra di dialogo che si apre, passare a Entry.json, un file con dati di esempio dal flusso di eventi EntryTime.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)

Verificare che l'output della query sia come previsto:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.jpg)

## Domanda 2: Report del tempo totale necessario a ciascuna automobile per attraversare il casello

Si desidera calcolare quanto tempo impiega in media l'automobile per attraversare il casello e valutare efficienza ed esperienza cliente.

Per questo è necessario unire il flusso contenente EntryTime con il flusso contenente ExitTime. I flussi vengono aggiunti nelle colonne TollId e Targa. L'operatore JOIN richiede di specificare un margine di manovra temporale che descrive la differenza di tempo accettabile tra gli eventi collegati. La funzione DATEDIFF viene usata per specificare che gli eventi non devono essere più di 15 minuti distanti l'uno dall'altro. La funzione DATEDIFF viene applicata anche ai tempi Exit ed Entry per il calcolo del tempo effettivo che l'automobile trascorre nel casello. Notare la differenza tra l'utilizzo di DATEDIFF in un'istruzione SELECT rispetto a una condizione JOIN.

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

Per eseguire il test della query, aggiornare la query nella scheda Query del processo:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.jpg)

Fare clic su Test e specificare i file di input di esempio per EntryTime ed ExitTime.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image44.png)

Selezionare la casella di controllo per eseguire il test della query e visualizzare l'output:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## Domanda 3: Report di tutti i veicoli commerciali con registrazione scaduta

Analisi di flusso di Azure può usare snapshot statici dei dati da unire ai flussi di dati temporali. La domanda di esempio seguente viene usata per illustrare questa funzionalità.

Se il veicolo commerciale è stato registrato nell'azienda del casello, non viene fermato per controlli al momento del transito. La tabella di ricerca relativa alla registrazione del veicolo commerciale viene usata per identificare tutti i veicoli commerciali con registrazione scaduta.

    SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN Registration
    ON EntryStream.LicensePlate = Registration.LicensePlate
    WHERE Registration.Expired = '1'

Notare che l'esecuzione del test di una query con dati di riferimento richiede la definizione di un'origine di input per i dati di riferimento, come illustrato nel passaggio 5.

Per eseguire il test della query, incollare la query nella scheda Query, quindi fare clic su Test e specificare le 2 origini di input:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

Visualizzare l'output della query:

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image47.png)

## Avviare il processo di analisi di flusso


Una volta scritta la prima query di analisi di flusso di Azure, è necessario completare la configurazione e avviare il processo. Salvare la query dalla domanda 3, generando un output corrispondente allo schema della tabella output **TollDataRefJoin**.

Passare al dashboard del processo e fare clic su Avvia.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.jpg)

Nella finestra di dialogo visualizzata, modificare l'ora di Avvia output con Ora personalizzata. Modificare l'ora e spostarla indietro di una unità. Questo garantisce l'elaborazione di tutti gli eventi dall'Hub eventi a partire dal momento in cui è stata avviata la generazione di eventi all'inizio dell'esercitazione. Fare clic sul segno di spunta per avviare il processo.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

L'avvio del processo può richiedere alcuni minuti. È possibile visualizzare lo stato nella pagina di livello superiore per analisi di flusso.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.jpg)

## Controllare i risultati in Visual Studio

Aprire Esplora server di Visual Studio e fare clic con il pulsante destro del mouse sulla tabella TollDataRefJoin. Selezionare "Mostra dati tabella" per visualizzare l'output del processo.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## Ridimensionare i processi di analisi di flusso di Azure

Analisi di flusso di Azure è progettato per garantire una scalabilità elastica e per gestire un carico elevato di dati. La query di analisi di flusso di Azure può usare la clausola **PARTITION BY** per comunicare al sistema che questo passaggio aumenterà il numero di istanze. PartitionId è una colonna speciale aggiunta dal sistema corrispondente all'id di partizione dell'input (Hub eventi)

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId    

Arrestare il processo corrente, aggiornare la query nella scheda Query e aprire la scheda Scalabilità.

Le unità di flusso definiscono la quantità di potenza di calcolo che il processo può ricevere.

Spostare il dispositivo di scorrimento su 6.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.jpg)

Andare alla scheda di output e modificare il nome della tabella SQL in "TollDataTumblingCountPartitioned"

A questo punto, se si avvia il processo, l'analisi di flusso di Azure può distribuire il lavoro tra più risorse di calcolo e ottenere maggiore produttività. Notare che l'applicazione TollApp invia anche eventi partizionati in base a TollId.

## Monitoraggio

La scheda Monitoraggio contiene le statistiche relative al processo in esecuzione.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image53.png)

È possibile accedere a Log operazioni dalla scheda Dashboard.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image54.jpg)

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image55.png)

Per visualizzare altre informazioni su un evento specifico, selezionare l'evento e fare clic sul pulsante "Dettagli".

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image56.png)

## Conclusioni

Questa esercitazione ha presentato il servizio di analisi di flusso di Azure. È stato illustrato come configurare input e output per il processo di analisi di flusso. Con lo scenario dei dati del casello, è stato possibile spiegare le tipologie più comuni di problemi che si verificano nello spazio dei dati in movimento e come possono essere risolti tramite semplici query simili a SQL nell'analisi di flusso di Azure. Sono stati descritti i costrutti di estensioni SQL per lavorare con i dati temporali. È stato illustrato come unire i flussi di dati e come arricchirli tramite dati di riferimento statici. È stato spiegato come aumentare il numero di istanze di una query per ottenere maggiore produttività.

Anche se questa esercitazione offre una panoramica dettagliata del prodotto, non è assolutamente comprensiva di tutto. È possibile scoprire altri modelli di query che usano il linguaggio SAQL [qui](stream-analytics-stream-analytics-query-patterns.md). Consultare la [documentazione online](https://azure.microsoft.com/documentation/services/stream-analytics/) per sapere di più sull'analisi di flusso di Azure.

## Eliminare l'account di Azure.

Arrestare il processo di analisi di flusso dal portale di Azure.

Lo script Setup.ps1 crea 2 Hub eventi di Azure e il server di database SQL di Azure. Le istruzioni seguenti consentono di eliminare le risorse al termine dell'esercitazione.

Nella finestra di PowerShell digitare ".\\Cleanup.ps1" per avviare lo script che elimina le risorse usate nell'esercitazione.

Tenere presente che le risorse possono essere identificate in base al nome. Assicurarsi di controllare attentamente ogni elemento prima di confermarne la rimozione.

![](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image57.png)

<!---HONumber=AcomDC_0727_2016-->