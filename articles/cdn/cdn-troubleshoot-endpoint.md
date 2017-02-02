---
title: Risoluzione dei problemi degli endpoint di rete CDN di Azure che restituiscono stati di tipo 404 | Documentazione Microsoft
description: Risolvere i problemi relativi ai codici di risposta 404 con endpoint della rete CDN.
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 53fdf1fe661167b468ef602634528e4d4173f606


---
# <a name="troubleshooting-cdn-endpoints-returning-404-statuses"></a>Risoluzione dei problemi degli endpoint della rete CDN che restituiscono stati 404
Questo articolo consente di risolvere i problemi relativi agli [endpoint della rete CDN](cdn-create-new-endpoint.md) che restituiscono errori 404.

Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile contattare gli esperti di Azure nei [forum MSDN e Stack Overflow dedicati ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è anche possibile archiviare un evento imprevisto di supporto tecnico di Azure. Passare al [sito di supporto per Azure](https://azure.microsoft.com/support/options/) e fare clic su **Ottenere supporto**.

## <a name="symptom"></a>Sintomo
Si crea un profilo di rete CDN e un endpoint, ma sembra che il contenuto non sia disponibile nella rete CDN.  Gli utenti che provano ad accedere ai contenuti tramite l'URL della rete CDN ricevono codici di stato HTTP 404. 

## <a name="cause"></a>Causa
Le cause possono essere diverse, ad esempio:

* L'origine del file non è visibile per la rete CDN
* L'endpoint non è configurato correttamente e causa la ricerca in una posizione errata da parte della rete CDN
* L'host rifiuta l'intestazione host dalla rete CDN
* L'endpoint non ha avuto tempo per propagarsi in tutta la rete CDN

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi
> [!IMPORTANT]
> Dopo aver creato un endpoint della rete CDN, questo non sarà disponibile immediatamente per l'uso, perché la propagazione della registrazione nella rete CDN richiede tempo.  La propagazione dei profili della <b>rete CDN di Azure fornita da Akamai</b> di solito dura meno di un minuto.  Per i profili della <b>rete CDN di Azure fornita da Verizon</b>, la propagazione in genere viene completata entro 90 minuti, ma in alcuni casi può richiedere più tempo.  Se si completa la procedura in questo documento e si ricevono comunque risposte 404, è consigliabile attendere alcune ore e controllare nuovamente prima di aprire un ticket di supporto.
> 
> 

### <a name="check-the-origin-file"></a>Controllare il file di origine
Prima di tutto, è necessario verificare che il file che si vuole memorizzare nella cache sia disponibile nell'origine e accessibile pubblicamente.  Il modo più rapido per eseguire questa operazione consiste nell'aprire un browser in una sessione InPrivate o anonima e passare direttamente al file.  Digitare o incollare semplicemente l'URL nella casella dell'indirizzo e verificare se il file previsto è disponibile.  Per questo esempio si userà un file disponibile in un account di archiviazione di Azure, accessibile all'indirizzo `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`.  Come si può notare, il test viene superato.

![Completamento della procedura](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Anche se questo è il modo più rapido e semplice per verificare che il file sia disponibile pubblicamente, alcune configurazioni di rete dell'organizzazione potrebbero creare l'illusione che il file sia disponibile pubblicamente mentre, in effetti, è visibile solo agli utenti della rete, anche se è ospitato in Azure.  Avere un browser esterno da cui è eseguire il test, ad esempio un dispositivo mobile non connesso alla rete dell'organizzazione oppure una macchina virtuale in Azure, sarebbe la scelta ottimale.
> 
> 

### <a name="check-the-origin-settings"></a>Controllare le impostazioni dell'origine
Dopo avere verificato che il file è disponibile pubblicamente su Internet, è necessario verificare le impostazioni dell'origine.  Nel [portale di Azure](https://portal.azure.com)passare al profilo della rete CDN e fare clic sull'endpoint di cui si stanno risolvendo i problemi.  Nel pannello **Endpoint** risultante fare clic sull'origine.  

![Pannello Endpoint con origine evidenziata](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

Verrà visualizzato il pannello **Origine** . 

![Pannello Origine](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Tipo di origine e nome host
Verificare che il **tipo di origine** sia corretto e il **nome host dell'origine**.  Nell'esempio `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`, la parte nome host dell'URL è `cdndocdemo.blob.core.windows.net`.  Come si può vedere nello screenshot, è corretto.  Per le origini Archiviazione di Azure, App Web e Servizio Cloud, il campo **Nome host dell'origine** è un elenco a discesa, quindi non è necessario preoccuparsi di digitarlo correttamente.  Tuttavia, se si usa un'origine personalizzata, è *assolutamente fondamentale* che l'ortografia del nome host sia corretta.

#### <a name="http-and-https-ports"></a>Porte HTTP e HTTPS
Un altro elemento da controllare sono le porte **HTTP** e **HTTPS**.  Nella maggior parte dei casi, 80 e 443 sono corrette e non saranno necessarie modifiche.  Tuttavia, se il server di origine è in ascolto su una porta diversa, dovrà essere rappresentata qui.  Se non si è certi, esaminato solo l'URL del file di origine.  Le specifiche per HTTP e HTTPS indicano le porte 80 e 443 come impostazioni predefinite. Nell'URL, `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`, una porta non è specificata, quindi viene presupposto il valore predefinito 443 e le impostazioni sono corrette.  

Si supponga tuttavia che l'URL per il file di origine testato in precedenza sia `http://www.contoso.com:8080/file.txt`.  Si noti `:8080` alla fine del segmento del nome host.  Indica al browser di usare la porta `8080` per connettersi al server Web `www.contoso.com` e sarà quindi necessario immettere 8080 nel campo **Porta HTTP**.  È importante notare che queste impostazioni della porta hanno effetto solo sulla porta usata dall'endpoint per recuperare informazioni dall'origine.

> [!NOTE]
> **rete CDN di Azure fornita da Akamai** non consentono l'intera gamma di porte TCP per le origini.  Per un elenco delle porte di origine non consentite, vedere [Azure CDN from Akamai Allowed Origin Ports](https://msdn.microsoft.com/library/mt757337.aspx)(Porte di origine consentite in Rete CDN di Azure da Akamai).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Controllare le impostazioni dell'endpoint
Nel pannello **Endpoint** fare clic sul pulsante **Configura**.

![Pannello Endpoint con il pulsante Configura evidenziato](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Verrà visualizzato il pannello **Configura** dell'endpoint.

![Pannello Configura](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protocolli
In **Protocolli**verificare che sia selezionato il protocollo usato dai client.  Lo stesso protocollo usato dal client sarà quello usato per accedere all'origine, quindi è importante che le porte di origine siano configurate correttamente nella sezione precedente.  L'endpoint sarà in ascolto solo sulle porte HTTP e HTTPS (80 e 443) predefinite, indipendentemente dalle porte di origine.

Tornare all'esempio ipotetico con `http://www.contoso.com:8080/file.txt`.  Come si ricorderà, per Contoso è stato specificato `8080` come porta HTTP, ma si supponga anche che sia stato specificato `44300` come porta HTTPS.  Se è stato creato un endpoint denominato `contoso`, il nome host dell'endpoint di rete CDN sarà `contoso.azureedge.net`.  Una richiesta per `http://contoso.azureedge.net/file.txt` è una richiesta HTTP, quindi l'endpoint userà HTTP sulla porta 8080 per recuperarlo dall'origine.  Una richiesta sicura tramite HTTPS, `https://contoso.azureedge.net/file.txt`, farà sì che l'endpoint usi HTTPS sulla porta 44300 quando recupera il file dall'origine.

#### <a name="origin-host-header"></a>Intestazione host di origine
L'opzione **Intestazione host di origine** è il valore dell'intestazione host inviato all'origine con ogni richiesta.  Nella maggior parte dei casi deve essere identico al valore di **Nome host dell'origine** verificato in precedenza.  Un valore non corretto in questo campo non causa in genere stati 404, ma può causare altri stati 4xx, a seconda di quanto previsto dall'origine.

#### <a name="origin-path"></a>Percorso dell'origine
Infine è necessario verificare il **Percorso dell'origine**.  Per impostazione predefinita è vuoto.  Usare questo campo solo se si vuole limitare l'ambito delle risorse ospitate dall'origine da rendere disponibili nella rete CDN.  

Ad esempio, nell'endpoint di esempio si vuole che tutte le risorse nell'account di archiviazione siano disponibili, quindi **Percorso dell'origine** è stato lasciato vuoto.  Di conseguenza, una richiesta a `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` determina una connessione dall'endpoint a `cdndocdemo.core.windows.net` che richiede `/publicblob/lorem.txt`.  Analogamente, una richiesta per `https://cdndocdemo.azureedge.net/donotcache/status.png` genera la richiesta di `/donotcache/status.png` da parte dell'endpoint all'origine.

Cosa accade se si vuole usare la rete CDN per ogni percorso nell'origine?  Si supponga di voler esporre solo il percorso `publicblob` .  Se si immette */publicblob* nel campo **Percorso dell'origine**, l'endpoint inserirà */publicblob* prima di ogni richiesta all'origine.  Di conseguenza, la richiesta per `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` userà di fatto la parte della richiesta dell'URL, `/publicblob/lorem.txt`, aggiungendo `/publicblob` all'inizio. Ciò comporta una richiesta per `/publicblob/publicblob/lorem.txt` dall'origine.  Se tale percorso non viene risolto in un file effettivo, l'origine restituirà uno stato 404.  L'URL corretto per recuperare lorem.txt in questo esempio sarà in effetti `https://cdndocdemo.azureedge.net/lorem.txt`.  Si noti che non è incluso il percorso */publicblob*, perché la parte della richiesta dell'URL è `/lorem.txt` e l'endpoint aggiunge `/publicblob`, con il conseguente passaggio della richiesta `/publicblob/lorem.txt` all'origine.




<!--HONumber=Nov16_HO3-->


