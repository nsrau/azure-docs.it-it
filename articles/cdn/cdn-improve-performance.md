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
	ms.date="02/25/2016" 
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

3. Dopo aver modificato l'elenco dei tipi di file, fare clic sul pulsante **Aggiornamento**.


## Regole e processo di compressione

1. Il richiedente invia una richiesta di contenuti.
2. Un server edge verifica la presenza dell’intestazione **Accept-Encoding**.
	1. Se inclusa, questa intestazione identifica il metodo di compressione richiesto.
	1. Se manca, questo tipo di richiesta verrà gestito in un formato non compresso.
3.	Il POP edge più vicino controlla lo stato della cache, il metodo di compressione e se è ancora presente un time-to-live valido.
	1.	Mancato riscontro nella cache: se la versione richiesta non è memorizzata nella cache, la richiesta viene inoltrata all'origine.
	2.	Riscontro nella cache con stesso metodo di compressione: il server edge fornirà immediatamente il contenuto compresso al client.
	3.	Riscontro nella cache con metodo di compressione diverso: il server edge transcodificherà l'asset nel metodo di compressione richiesto.
	4.	Riscontro nella cache e non compresso: se la richiesta iniziale ha causato la memorizzazione dell'asset nella cache in formato non compresso, verrà eseguita una verifica per controllare se la richiesta è idonea per la compressione del server edge (in base ai criteri nella sezione della definizione/dei requisiti precedente).
		1.	Se idoneo, il server edge comprimerà il file e lo fornirà al client.
		2.	Se non idoneo: il server edge fornirà immediatamente il contenuto non compresso al client.



## Considerazioni

1. Per endpoint di streaming abilitati alla rete CDN dei servizi multimediali, la compressione è abilitata per impostazione predefinita per i seguenti tipi di contenuto: application/vnd.ms-sstr+xml,application/dash+xml,application/vnd.apple.mpegurl,application/f4m+xml. Non è possibile attivare o disattivare la compressione per i tipi indicati tramite il portale di Azure.  
2. Una sola versione del file (compressa o non compressa) verrà memorizzata nella cache del server edge. La richiesta di una versione diversa genererà una transcodifica del contenuto dal server edge.  

<!---HONumber=AcomDC_0302_2016-->