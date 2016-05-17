<properties
	pageTitle="Rete CDN - Miglioramento delle prestazioni attraverso la compressione dei file"
	description="È possibile migliorare la velocità di trasferimento dei file e aumentare le prestazioni di caricamento delle pagine comprimendo i file."
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016" 
	ms.author="casoper"/>

# Compressione dei file per migliorare le prestazioni

In questo argomento viene descritto come migliorare la velocità di trasferimento del file e aumentare le prestazioni di caricamento delle pagine comprimendo i file.

Esistono due modi con cui la rete CDN può supportare la compressione:

- È possibile abilitare la compressione nel server di origine, nel qual caso la rete CDN supporterà la compressione per impostazione predefinita e distribuirà file compressi ai client.
- È possibile abilitare la compressione direttamente sui server edge CDN, nel qual caso la rete CDN comprimerà i file e li fornirà agli utenti finali.

## Abilitare la compressione

> [AZURE.NOTE] I livelli della rete CDN Standard e Premium forniscono la stessa funzionalità di compressione, ma l'interfaccia utente è diversa. Per altre informazioni sulle differenze tra i livelli della rete CDN Standard e Premium, vedere [Panoramica della rete CDN di Azure](cdn-overview.md).

### Livello Standard

1. Dal pannello del profilo di rete CDN, fare clic sull'endpoint della rete CDN che si desidera gestire.

	![Endpoint del pannello del profilo di rete CDN](./media/cdn-file-compression/cdn-endpoints.png)

	Viene visualizzato il pannello di endpoint della rete CDN.

2. Fare clic sul pulsante **Configura**.

	![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-file-compression/cdn-config-btn.png)

	Si apre il pannello di configurazione della rete CDN.

3. Attivare **compressione**.

	![Opzioni di compressione della rete CDN](./media/cdn-file-compression/cdn-compress-standard.png)

4. Usare i tipi predefiniti, o modificare l'elenco eliminando o aggiungendo tipi di file.

5. Dopo aver apportato le modifiche, fare clic sul pulsante **Salva**.

### Livello Premium

1. Dal pannello del profilo della rete CDN fare clic sul pulsante **Gestisci**.

	![Pulsante Gestisci pannello del profilo di rete CDN](./media/cdn-file-compression/cdn-manage-btn.png)

	Si aprirà il portale di gestione della rete CDN.

2. Passare il puntatore sulla scheda **HTTP Grande**, quindi passare il puntatore sul riquadro a comparsa **Impostazioni della memorizzazione nella cache**. Fare clic su **Compressione**.

	Vengono visualizzate le opzioni di compressione.

	![Compressione dei file](./media/cdn-file-compression/cdn-compress-files.png)

3. Abilitare la compressione facendo clic sul pulsante di opzione **Compressione abilitata**. Immettere i tipi MIME che si vogliono comprimere sotto forma di elenco delimitato da virgole (senza spazi) nella casella di testo **Tipi di file**.

4. Dopo aver apportato le modifiche, fare clic sul pulsante **Aggiorna**.


## Processo di compressione

### Panoramica

1. Il richiedente invia una richiesta di contenuti.

2. Un server edge verifica la presenza di un'intestazione **Accept-Encoding**.
	1. Se inclusa, questa intestazione identifica il metodo di compressione richiesto.
		> [AZURE.NOTE] I metodi di compressione supportati sono **gzip**, **deflate** e **bzip2**.
	2. Se manca, questo tipo di richiesta verrà gestito in un formato non compresso.
	
3.	Il POP edge più vicino controlla lo stato della cache, il metodo di compressione e la presenza di una durata (TTL) valida.
	1.	**Mancato riscontro** nella cache: se la versione richiesta non è memorizzata nella cache, la richiesta viene inoltrata all'origine.
	2.	**Riscontro** nella cache: se la versione richiesta è memorizzata nella cache con il metodo di compressione richiesto, il server edge fornisce immediatamente il contenuto compresso al client.
	3.	**Riscontro** nella cache : se il file è memorizzato nella cache con un metodo di compressione diverso, il server edge transcodifica l'asset nel metodo di compressione richiesto.
	4.	**Riscontro** nella cache: se il file è memorizzato nella cache in un formato non compresso, viene eseguita una verifica per determinare se la richiesta è idonea per la compressione del server edge (vedere la nota seguente). Se idoneo, il server edge comprimerà il file e lo fornirà al client. In caso contrario, restituirà il contenuto non compresso.
		
> [AZURE.IMPORTANT] Per essere idoneo per la compressione, un file deve essere:
>
> - Maggiore di 128 byte.
> - Minore di 1 MB.
> - Di un tipo MIME [configurato per la compressione](#enabling-compression).

### Tables

Le tabelle seguenti descrivono il comportamento della compressione della rete CDN per ogni scenario.

#### Compressione disabilitata o file non idoneo per la compressione

|Formato richiesto|File memorizzato nella cache|Risposta rete CDN|Note|
|----------------|-----------|------------|-----|
|Compresso|Compresso|Compresso|Transcodifica della rete CDN tra formati supportati|
|Compresso|Non compresso|Non compressa| |	
|Compresso|Non memorizzato nella cache|Compressa o non compressa|A seconda della risposta dell'origine|
|Non compresso|Compresso|Non compressa|Ritorno all'origine della rete CDN per la versione non compressa|
|Non compresso|Non compresso|Non compressa| |	
|Non compresso|Non memorizzato nella cache|Non compressa| |

#### Compressione abilitata e file idoneo per la compressione

|Formato richiesto|File memorizzato nella cache|Risposta rete CDN|Note|
|----------------|-----------|------------|-----|
|Compresso|Compresso|Compresso|Transcodifica della rete CDN tra formati supportati|
|Compresso|Non compresso|Compresso|Compressione da parte della rete CDN|
|Compresso|Non memorizzato nella cache|Compresso|La rete CDN esegue la compressione se l'origine restituisce Non compresso|
|Non compresso|Compresso|Non compressa|Decompressione da parte della rete CDN|
|Non compresso|Non compresso|Non compressa| |	
|Non compresso|Non memorizzato nella cache|Non compressa| |	


## Note

1. Come avviene con la distribuzione di nuovi endpoint, le modifiche alla configurazione della rete CDN richiedono tempo per propagarsi attraverso la rete. Nella maggior parte dei casi le modifiche vengono applicate entro 90 minuti. Se questa è la prima volta che si configura la compressione per l'endpoint della rete CDN, è consigliabile attendere 1-2 ore per assicurarsi che le impostazioni di compressione si siano propagate ai POP prima di intraprendere la risoluzione di eventuali problemi.
2. Una sola versione del file (compressa o non compressa) verrà memorizzata nella cache del server edge. La richiesta di una versione diversa genererà una transcodifica del contenuto dal server edge.
3. Per endpoint di streaming abilitati alla rete CDN dei servizi multimediali, la compressione è abilitata per impostazione predefinita per i seguenti tipi di contenuto: application/vnd.ms-sstr+xml,application/dash+xml,application/vnd.apple.mpegurl,application/f4m+xml. Non è possibile attivare o disattivare la compressione per i tipi indicati tramite il portale di Azure.  
4. Se possibile, non è consigliabile applicare la compressione a formati compressi, ad esempio ZIP, MP3, MP4, JPG e così via.

## Vedere anche
- [Risoluzione dei problemi della compressione dei file CDN](cdn-troubleshoot-compression.md)    

<!---HONumber=AcomDC_0504_2016-->