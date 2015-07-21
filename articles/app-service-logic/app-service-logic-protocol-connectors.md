<properties 
	pageTitle="Connettori per protocolli delle app per le API di Microsoft Azure | Microservizio app per le API" 
	description="Informazioni su come creare app per le API per i connettori per protocolli di Microsoft Azure e aggiungere l'app per le API all'app per la logica; microservizi" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="deonhe"/>


# Connettori per protocolli nel servizio app di Microsoft Azure


## Cos'è un connettore per protocolli
I connettori per protocolli sono app per le API che si usano per creare app che comunicano usando un'ampia gamma di protocolli come HTTP, SMTP, FTP e così via.

Per trovare i connettori per protocolli nel portale di Microsoft di Azure, fare clic su **Nuovo** > **Web e dispositivi mobili** > **Azure Marketplace** > immettere la parola chiave di ricerca **protocollo** > premere invio

I connettori per protocolli seguenti sono disponibili in Azure Marketplace:

- SFTP
- POP3
- FTP
- HTTP
- SMTP
- File

Di seguito è riportata una breve descrizione di ogni connettore per protocolli:

<table>
<tr>
<th> Nome</th>
<th> Descrizione</th>
<th> Trigger</th>
<th> Azioni</th>

<tr>
<td>SFTP
<td>Un connettore SFTP consente di scaricare file da un server SFTP oppure di caricarveli.
<td>Nuovo file disponibile nella cartella SFTP

<td>File: caricare, eliminare, elencare, scaricare 

</tr>

<tr>
<td>POP3
<td>Un connettore POP3 consente di scaricare la posta elettronica da un server POP3.
<td>Arriva un nuovo messaggio
<td>N/D
</tr>

<tr>
<td>FTP
<td>Un connettore FTP consente di scaricare file da un server FTP oppure di caricarveli.
<td>Nuovo file disponibile nella cartella FTP
<td>File: caricare, scaricare, eliminare ed elencare
</tr>

<tr>
<td>HTTP
<td>Un connettore HTTP consente di inviare dati ai server HTTP usando il protocollo HTTP o HTTPS.
<td>Nessuno
<td>Pubblicare, scaricare, eliminare, inserire
</tr>

<tr>
<td>SMTP
<td>Un connettore SMTP consente di inviare la posta elettronica da un server SMTP.
<td>N/D
<td>Invio di posta elettronica
</tr>

<tr>
<td>File
<td>Un connettore file consente di connettersi al file server locale ed eseguire azioni come caricare, scaricare, eliminare ed elencare file. È anche disponibile un trigger per recuperare i file.
<td>Nuovo file aggiunto al dispositivo
<td>File: caricare, scaricare, eliminare ed elencare
</tr>


</table>

Dopo aver illustrato le funzionalità dei connettori per protocolli, verranno esaminati alcuni semplici casi di utilizzo per questi connettori.

### Monitoraggio dei suggerimenti clienti ###
Si supponga che la propria società abbia recentemente rilasciato una nuova app e che il team voglia sapere cosa ne dicono i clienti sui social media. Ogni membro del team potrà controllare periodicamente i vari siti dei social media e ipotizzare quali parole chiave i clienti potrebbero usare per discutere dell'app. In maniera molto più lineare, è possibile creare un semplice connettore Twitter e configurarlo in modo da monitorare specifici hashtag, citazioni e parole chiave su Twitter. Sarà quindi possibile usare il connettore SMTP per creare un messaggio di posta elettronica che includa il contenuto dei tweet corrispondenti, quindi inviarlo ai membri del team. A questo punto, sarà possibile usare un connettore HTTP per pubblicare questi tweet in un sito Web interno della società. Tutto questo è possibile senza scrivere una singola riga di codice.

Di seguito sono riportati i requisiti iniziali.

## Creare un connettore

Un connettore può essere creato usando il portale di Azure.

### Creare un connettore nel portale di Azure

Verrà ora illustrata la creazione di un connettore SMTP usando Azure Marketplace

1. Accedere al [portale di anteprima](https://portal.azure.com) di Microsoft Azure.
2. Selezionare **NUOVO** > **Web e dispositivi mobili** > **Azure Marketplace**.
3. Nella **casella di ricerca** immettere **protocollo**, quindi premere **Invio**. È anche possibile selezionarne uno dall'elenco. Una volta selezionato, si apre un nuovo pannello o finestra. Selezionare **Crea**. 
4. Immettere le seguenti proprietà:

<table>
<tr><th>Proprietà</th> <th>Descrizione</th> </tr>
<tr><td>Nome</td> <td>Descrizione</td> </tr>
<tr><td>Impostazioni pacchetto</td> <td>Usare la proprietà delle impostazioni del pacchetto per immettere tutte le informazioni di autenticazione, come nome utente, password, numero di porta, indirizzo del server e così via per il server SMTP. </td> </tr>
<tr><td>Piano di servizio app</td> <td>Elenca il piano di pagamento. È possibile modificarlo se sono necessarie più o meno risorse.</th> </td>
<tr><td>Piano tariffario</td> <td>Proprietà di sola lettura che elenca la categoria di prezzo nella sottoscrizione di Azure.</td> </tr>
<tr><td>Gruppo di risorse</td> <td>Creare un nuovo gruppo di risorse o usare un gruppo esistente. L'articolo Uso dei gruppi di risorse illustra questa proprietà.</td> </tr>
<tr><td>Sottoscrizione</td> <td>Proprietà di sola lettura che elenca la sottoscrizione corrente.</td> </tr>
<tr><td>Location</td> <td>La località geografica che ospita il servizio di Azure.</th> </td>
<tr><td>Aggiungi a schermata iniziale</td> <td>Selezionare questa opzione per aggiungere l'app per le API alla schermata iniziale (home page).</td></tr>
</table>

### Accedere ai connettori con le API REST
[Accedere ai connettori con le API REST](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## Aggiungere un connettore all'applicazione 
Il servizio app di Microsoft Azure espone diversi tipi di applicazione che possono usare questi connettori. Ad esempio, è possibile creare un'app per la *logica* combinando uno o più connettori *logicamente* in un'unica app.

Per usare i connettori nell'app per l'app per la *logica*, è sufficiente selezionare un connettore preconfigurato nell'elenco, aggiungerlo al flusso di lavoro di progettazione e apportare le modifiche necessarie alla configurazione.

Per seguire questa procedura, è necessaria un'app Web, un'app mobile o un'app per la logica. Una volta che l'applicazione è disponibile, aggiungere i connettori. Ecco come:

Usare i passaggi seguenti per aggiungere un connettore a un'app per la logica:

1. Nella schermata iniziale del portale di Azure (la home page) passare a **Marketplace** e cercare la propria app per la logica, per dispositivi mobili o Web. 

	Se si sta creando una nuova app, cercare un'app per la logica, un'app mobile o un'app Web. Selezionare l'app e nel nuovo pannello selezionare **Crea**. La procedura è descritta in [Creare un'app per la logica](app-service-logic-create-a-logic-app.md).

2. Aprire l'app e selezionare **Trigger e azioni**.
3. Nella **Raccolta** selezionare il connettore. Verrà aggiunto all'app.
4. Configurare il connettore:
5. Le proprietà di ogni connettore sono specifiche del servizio e dell'ambiente a cui si connette. Immettere i dettagli per le proprietà. Tenere presente che alcune proprietà sono facoltative.
6. Selezionare **OK** per salvare le modifiche.


## Sicurezza
I connettori usano OAuth oppure nomi utente e password.

## Informazioni sulle app per la logica e le app Web
[Cosa sono le app per la logica?](app-service-logic-what-are-logic-apps.md) | [Siti Web e app Web nel servizio app di Azure](../app-service-web/app-service-web-overview.md) |


## Altri connettori

[Connettori di integrazione di BizTalk](app-service-logic-integration-connectors.md) | 
[Connettori Enterprise](app-service-logic-enterprise-connectors.md) | 
[Connettori di Business-to-Business](app-service-logic-b2b-connectors.md) | 
[Connettori per social network](app-service-logic-social-connectors.md) | 
[Connettori per i servizi app e dati](app-service-logic-data-connectors.md) | 
[Elenco di connettori e app per le API](app-service-logic-connectors-list.md)<br/><br/> 
[Cosa sono i connettori e le app per le API di BizTalk](app-service-logic-what-are-biztalk-api-apps.md)
 

<!---HONumber=62-->