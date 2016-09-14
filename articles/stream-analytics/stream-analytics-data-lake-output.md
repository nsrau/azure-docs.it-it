<properties
	pageTitle="Output di Archivio Data Lake per Analisi di flusso | Microsoft Azure"
	description="Configurazione dell'autenticazione e dell'autorizzazione di un Archivio Data Lake di Azure in un processo di analisi di flusso"
	keywords=""
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"
/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="08/30/2016"
	ms.author="jeffstok"
/>

# Output di Archivio Data Lake per Analisi di flusso

I processi di Analisi di flusso supportano numerosi metodi di output, tra cui [Archivio Data Lake di Azure](https://azure.microsoft.com/services/data-lake-store/). Azure Data Lake Store è un repository su vasta scala a livello aziendale per carichi di lavoro di analisi di Big Data. Archivio Data Lake consente di archiviare dati di qualsiasi dimensione, tipo e velocità di inserimento per le analisi esplorative e operative.

## Autorizzare un account Archivio Data Lake

1.  Quando si seleziona Archivio Data Lake come output nel portale di gestione di Azure, verrà richiesto di autorizzare l'uso dell'Archivio Data Lake esistente o richiedere l'accesso all'anteprima di Archivio Data Lake tramite il portale di Azure classico.

    ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.png)

2.  Se si dispone già di un accesso ad Archivio Data Lake, fare clic su "Autorizza ora" e per un istante viene visualizzata una pagina che indica "Reindirizzamento all'autorizzazione...". La pagina si chiude automaticamente e verrà visualizzata la pagina che consente di configurare l'output di Archivio Data Lake.

Se non si è iscritti all'anteprima di Data Lake Store, è possibile selezionare il collegamento "Effettua l'iscrizione ora" per avviare la richiesta, oppure seguire le [istruzioni introduttive](../data-lake-store/data-lake-store-get-started-portal.md).

## Configurare le proprietà dell'output di Archivio Data Lake

Dopo aver autenticato l'account, è possibile configurare le proprietà per l'output di Archivio Data Lake. La tabella seguente elenca i nomi di proprietà e le relative descrizioni per configurare l'output di Archivio Data Lake.

<table>
<tbody>
<tr>
<td><B>NOME PROPRIETÀ</B></td>
<td><B>DESCRIZIONE</B></td>
</tr>
<tr>
<td>Alias di output</td>
<td>È un nome descrittivo usato nelle query per indirizzare l'output delle query ad Archivio Data Lake in uso.</td>
</tr>
<tr>
<td>Account di Archivio Data Lake</td>
<td>Nome dell'account di archiviazione a cui si sta inviando l'output. Verrà visualizzato un elenco a discesa degli account di Archivio Data Lake ai quali ha accesso l'utente connesso al portale.</td>
</tr>
<tr>
<td>Schema prefisso percorso [<I>facoltativo</I>]</td>
<td>Percorso del file usato per scrivere i file nell'account di Archivio Data Lake specificato. <BR>{date}, {time}<BR>Esempio 1: folder1/logs/{date}/{time}<BR>Esempio 2: folder1/logs/{date}</td>
</tr>
<tr>
<td>Formato data [<I>facoltativo</I>]</td>
<td>Se nel percorso di prefisso viene usato il token di data, è possibile selezionare il formato della data in cui sono organizzati i file. Esempio: AAAA/MM/GG</td>
</tr>
<tr>
<td>Formato ora [<I>facoltativo</I>]</td>
<td>Se nel percorso di prefisso viene usato il token dell'ora, specificare il formato dell'ora in cui sono organizzati i file. Al momento, l'unico valore supportato è HH.</td>
</tr>
<tr>
<td>Formato di serializzazione eventi</td>
<td>Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro.</td>
</tr>
<tr>
<td>Codifica</td>
<td>Se il formato è CSV o JSON, è necessario specificare un formato di codifica. Al momento UTF-8 è l'unico formato di codifica supportato.</td>
</tr>
<tr>
<td>Delimitatore</td>
<td>Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale.</td>
</tr>
<tr>
<td>Format</td>
<td>Applicabile solo per la serializzazione JSON. Separato da righe specifica che l'output verrà formattato separando ciascun oggetto JSON con una nuova riga. Array specifica che l'output verrà formattato come array di oggetti JSON.</td>
</tr>
</tbody>
</table>

## Rinnovare l'autorizzazione per Archivio Data Lake

Attualmente esiste una limitazione secondo cui il token di autenticazione deve essere aggiornato manualmente ogni 90 giorni per tutti i processi con output di Archivio Data Lake. È necessario anche autenticare nuovamente l'account di Archivio Data Lake se la password è stata modificata dopo la creazione del processo o dopo l'ultima autenticazione. Un sintomo di questo problema è la mancanza di output del processo e un errore nei log delle operazioni che indicano la necessità di una nuova autorizzazione.

Per risolvere questo problema, arrestare il processo in esecuzione e passare all'output di Archivio Data Lake. Fare clic sul collegamento "Rinnova autorizzazione" e per un istante viene visualizzata una pagina che indica "Reindirizzamento all'autorizzazione..." La pagina verrà chiusa automaticamente e, in caso di esito positivo, verrà indicato "Autorizzazione rinnovata". È quindi necessario fare clic su "Salva" nella parte inferiore della pagina e, per continuare, riavviare il processo dall'ora dell'ultimo arresto per evitare la perdita di dati.

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.png)

<!---HONumber=AcomDC_0831_2016-->