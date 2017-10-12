---
title: Risoluzione dei problemi di compressione di file nella rete CDN di Azure | Microsoft Docs
description: Risolvere i problemi relativi alla compressione di file nella rete CDN di Azure.
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 5ef8a8262eb40aa827161764f03a63d031e43273
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-cdn-file-compression"></a>Risoluzione dei problemi della compressione dei file CDN
Questo articolo consente di risolvere i problemi relativi alla [compressione dei file CDN](cdn-improve-performance.md).

Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile contattare gli esperti di Azure nei [forum MSDN e overflow dello stack relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è anche possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del Supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e fare clic su **Ottenere supporto**.

## <a name="symptom"></a>Sintomo
La compressione per l'endpoint è abilitata, ma i file vengono restituiti non compressi.

> [!TIP]
> Per verificare se i file restituiti sono compressi, è necessario usare uno strumento come[Fiddler](http://www.telerik.com/fiddler) o gli [strumenti di sviluppo](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) del browser.  Verificare le intestazioni della risposta HTTP restituite con il contenuto della rete CDN memorizzato nella cache.  Se è presente un'intestazione denominata `Content-Encoding` con un valore **gzip**, **bzip2** o **deflate**, il contenuto è compresso.
> 
> ![Intestazione Content-Encoding](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Causa
Le cause possono essere diverse, ad esempio:

* Il contenuto richiesto non è idoneo per la compressione.
* La compressione non è abilitata per il tipo di file richiesto.
* La richiesta HTTP non include un'intestazione che richiede un tipo di compressione valido.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi
> [!TIP]
> Come avviene con la distribuzione di nuovi endpoint, le modifiche alla configurazione della rete CDN richiedono tempo per propagarsi attraverso la rete.  In genere, le modifiche vengono applicate entro 90 minuti.  Se questa è la prima volta che si configura la compressione per l'endpoint della rete CDN, è consigliabile attendere 1-2 ore per assicurarsi che le impostazioni di compressione impostazioni si siano propagate ai POP. 
> 
> 

### <a name="verify-the-request"></a>Verificare la richiesta
Per prima cosa, eseguire una rapida verifica dell'integrità della richiesta.  Per visualizzare le richieste in corso, è possibile usare gli [strumenti per sviluppatori](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) del browser.

* Verificare che la richiesta venga inviata all'URL dell'endpoint, `<endpointname>.azureedge.net`, non all'origine.
* Verificare che la richiesta contenga un'intestazione **Accept-Encoding** e che il valore di tale intestazione contenga **gzip**, **deflate** o **bzip2**.

> [!NOTE]
> I profili della **rete CDN di Azure fornita da Akamai** supportano solo la codifica **gzip**.
> 
> 

![Intestazioni di richiesta CDN](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profile"></a>Verificare le impostazioni di compressione (profilo di rete CDN Standard)
> [!NOTE]
> Questo passaggio va eseguito solo se il profilo CDN è un profilo di **rete CDN Standard di Azure fornita da Verizon** o di **rete CDN Standard di Azure fornita da Akamai**. 
> 
> 

Passare all'endpoint nel [portale di Azure](https://portal.azure.com) e fare clic sul pulsante **Configura** .

* Verificare se la compressione è abilitata.
* Verificare che il tipo MIME per il contenuto da comprimere sia incluso nell'elenco dei formati compressi.

![Impostazioni di compressione CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profile"></a>Verificare le impostazioni di compressione (profilo di rete CDN Premium)
> [!NOTE]
> Questo passaggio va eseguito solo se il profilo CDN è un profilo di **rete CDN Premium di Azure fornita da Verizon** .
> 
> 

Passare all'endpoint nel [portale di Azure](https://portal.azure.com) e fare clic sul pulsante **Gestisci** .  Verrà aperto il portale supplementare.  Passare il puntatore sulla scheda **HTTP Grande**, quindi passare il puntatore sul riquadro a comparsa **Impostazioni della memorizzazione nella cache**.  Fare clic su **Compressione**. 

* Verificare se la compressione è abilitata.
* Verificare che l'elenco dei **Tipi di file** contenga un elenco di tipi MIME delimitati da virgole (senza spazi).
* Verificare che il tipo MIME per il contenuto da comprimere sia incluso nell'elenco dei formati compressi.

![Impostazioni di compressione CDN premium](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached"></a>Verificare che il contenuto venga memorizzato nella cache
> [!NOTE]
> Questo passaggio va eseguito solo se il profilo CDN è un profilo di **rete CDN di Azure fornita da Verizon** (Standard o Premium).
> 
> 

Usando gli strumenti per sviluppatori del browser, controllare le intestazioni di risposta per verificare se il file è memorizzato nella cache nell'area in cui viene richiesto.

* Controllare l'intestazione della risposta **Server** .  L'intestazione deve avere il formato **Piattaforma (POP/ID server)**, come illustrato nell'esempio seguente.
* Controllare che l'intestazione della risposta **X-Cache** .  corrisponda a **HIT**.  

![Intestazioni di risposta CDN](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements"></a>Verificare che il file soddisfi i requisiti di dimensione
> [!NOTE]
> Questo passaggio va eseguito solo se il profilo CDN è un profilo di **rete CDN di Azure fornita da Verizon** (Standard o Premium).
> 
> 

Per essere idoneo per la compressione, un file deve avere le dimensioni seguenti:

* Maggiore di 128 byte.
* Minore di 1 MB.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Cercare nelle richieste nel server di origine un'intestazione **Via**
L'intestazione HTPP **Via** indica al server Web che la richiesta viene passata da un server proxy.  Per impostazione predefinita, i server Web Microsoft IIS non comprimono le risposte quando la richiesta contiene un'intestazione **Via** .  Per eseguire l'override di questo comportamento, eseguire queste operazioni:

* **IIS 6**: [Impostare HcNoCompressionForProxies="FALSE" nelle proprietà della metabase di IIS](https://msdn.microsoft.com/library/ms525390.aspx)
* **IIS 7 e versioni successive**: [Impostare sia **noCompressionForHttp10** che **noCompressionForProxies** su False nella configurazione del server](http://www.iis.net/configreference/system.webserver/httpcompression)

