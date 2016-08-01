<properties
	pageTitle="CDN: Risoluzione dei problemi di compressione dei file"
	description="Risolvere i problemi relativi alla compressione dei file CDN."
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2016"
	ms.author="casoper"/>
    
# Risoluzione dei problemi della compressione dei file CDN

Questo articolo consente di risolvere i problemi relativi alla [compressione dei file CDN](cdn-improve-performance.md).

Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile contattare gli esperti di Azure nei [forum MSDN e Stack Overflow dedicati ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è anche possibile archiviare un evento imprevisto di supporto tecnico di Azure. Passare al [sito di supporto per Azure](https://azure.microsoft.com/support/options/) e fare clic su **Ottenere supporto**.

## Sintomo

La compressione per l'endpoint è abilitata, ma i file vengono restituiti non compressi.

## Causa

Le cause possono essere diverse, ad esempio:

- Il contenuto richiesto non è idoneo per la compressione.
- La compressione non è abilitata per il tipo di file richiesto.
- La richiesta HTTP non include un'intestazione che richiede un tipo di compressione valido.

## Passaggi per la risoluzione dei problemi

> [AZURE.TIP] Come avviene con la distribuzione di nuovi endpoint, le modifiche alla configurazione della rete CDN richiedono tempo per propagarsi attraverso la rete. Nella maggior parte dei casi le modifiche vengono applicate entro 90 minuti. Se questa è la prima volta che si configura la compressione per l'endpoint della rete CDN, è consigliabile attendere 1-2 ore per assicurarsi che le impostazioni di compressione impostazioni si siano propagate ai POP.

### Verificare la richiesta

Per prima cosa, eseguire una rapida verifica dell'integrità della richiesta. Per visualizzare le richieste in corso, è possibile usare gli [strumenti per sviluppatori](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) del browser.

- Verificare che la richiesta venga inviata all'URL dell'endpoint, `<endpointname>.azureedge.net`, non all'origine.
- Verificare che la richiesta contenga un'intestazione **Accept-Encoding** e che il valore di tale intestazione contenga **gzip**, **deflate** o **bzip2**.

> [AZURE.NOTE] I profili della **rete CDN di Azure fornita da Akamai** supportano soltanto la codifica **gzip**.

![Intestazioni di richiesta CDN](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### Verificare le impostazioni di compressione (profilo di rete CDN Standard)

> [AZURE.NOTE] Questo passaggio va eseguito solo se il profilo della rete CDN è un profilo **Rete CDN Standard di Azure fornita da Verizon** o **Rete CDN Standard di Azure fornita da Akamai**.

Passare all'endpoint nel [portale di Azure](https://portal.azure.com) e fare clic sul pulsante **Configura**.

- Verificare se la compressione è abilitata.
- Verificare che il tipo MIME per il contenuto da comprimere sia incluso nell'elenco dei formati compressi.

![Impostazioni di compressione CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### Verificare le impostazioni di compressione (profilo di rete CDN Premium)

> [AZURE.NOTE] Questo passaggio va eseguito solo se il profilo della rete CDN è un profilo **Rete CDN Premium di Azure fornita da Verizon**.

Passare all'endpoint nel [portale di Azure](https://portal.azure.com) e fare clic sul pulsante **Gestisci**. Verrà aperto il portale supplementare. Passare il puntatore sulla scheda **HTTP Grande**, quindi passare il puntatore sul riquadro a comparsa **Impostazioni della memorizzazione nella cache**. Fare clic su **Compressione**.

- Verificare se la compressione è abilitata.
- Verificare che l'elenco dei **Tipi di file** contenga un elenco di tipi MIME delimitato da virgole (senza spazi).
- Verificare che il tipo MIME per il contenuto da comprimere sia incluso nell'elenco dei formati compressi.

![Impostazioni di compressione CDN premium](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### Verificare che il contenuto venga memorizzato nella cache

> [AZURE.NOTE] Questo passaggio va eseguito solo se il profilo della rete CDN è un profilo **Rete CDN di Azure fornita da Verizon** Standard o Premium.

Usando gli strumenti per sviluppatori del browser, controllare le intestazioni di risposta per verificare se il file è memorizzato nella cache nell'area in cui viene richiesto.

- Controllare l'intestazione di risposta **Server**. L'intestazione deve avere il formato **Piattaforma (POP/Server ID)**, come illustrato nell'esempio seguente.
- Controllare l'intestazione di risposta **X-Cache**. L'intestazione deve corrispondere a **HIT**.

![Intestazioni di risposta CDN](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### Verificare che il file soddisfi i requisiti di dimensione

> [AZURE.NOTE] Questo passaggio va eseguito solo se il profilo della rete CDN è un profilo **Rete CDN di Azure fornita da Verizon** Standard o Premium.

Per poter eseguire la compressione, la dimensione del file deve soddisfare i requisiti seguenti:

- Maggiore di 128 byte.
- Minore di 1 MB.

### Cercare nelle richieste nel server di origine un'intestazione **Tramite**

L'intestazione HTPP **ramite** indica al server Web che la richiesta viene passata da un server proxy. I server Web IIS Microsoft per impostazione predefinita non comprimere le risposte quando la richiesta contiene un'intestazione **Tramite**. Per eseguire l'override di questo comportamento, eseguire queste operazioni:

- **IIS 6**: [impostare HcNoCompressionForProxies = "FALSE" nelle proprietà della Metabase di IIS](https://msdn.microsoft.com/library/ms525390.aspx)
- **IIS 7 e versioni successive**: [impostare sia **noCompressionForHttp10** che **noCompressionForProxies** su False nella configurazione del server](http://www.iis.net/configreference/system.webserver/httpcompression)

<!---HONumber=AcomDC_0720_2016-->