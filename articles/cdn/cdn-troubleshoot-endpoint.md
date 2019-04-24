---
title: Risoluzione dei problemi degli endpoint della rete CDN di Azure con codice stato 404 | Microsoft Docs
description: Risolvere i problemi relativi ai codici di risposta 404 con endpoint della rete CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 66ee211856bb451caad7af02103aa306d76e8f97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60323714"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>Risoluzione dei problemi degli endpoint della rete CDN di Azure che restituiscono un codice stato 404
Questo articolo consente di risolvere i problemi relativi agli endpoint di rete CDN di Azure che restituiscono codici di stato di risposta HTTP 404.

Se in qualsiasi punto dell'articolo sono necessarie altre informazioni, è possibile contattare gli esperti di Azure nei [forum MSDN e overflow dello stack relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è anche possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Richiesta di supporto**.

## <a name="symptom"></a>Sintomo
Si crea un profilo di rete CDN e un endpoint, ma sembra che il contenuto non sia disponibile nella rete CDN. Gli utenti che provano ad accedere ai contenuti tramite l'URL della rete CDN ricevono un codice di stato HTTP 404. 

## <a name="cause"></a>Causa
Le cause possono essere diverse, ad esempio:

* L'origine del file non è visibile alla rete CDN.
* L'endpoint non è configurato correttamente e causa la ricerca in una posizione errata da parte della rete CDN.
* L'host rifiuta l'intestazione host dalla rete CDN.
* L'endpoint non ha avuto tempo per propagarsi in tutta la rete CDN.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi
> [!IMPORTANT]
> Dopo aver creato un endpoint della rete CDN, questo non sarà disponibile immediatamente per l'uso, perché la propagazione della registrazione nella rete CDN richiede tempo:
> - La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Microsoft** viene in genere completata in dieci minuti. 
> - La propagazione dei profili di **rete CDN Standard di Azure con tecnologia Akamai** viene in genere completata entro un minuto. 
> - La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Verizon** e della **rete CDN Premium di Azure con tecnologia Verizon** viene in genere completata entro 90 minuti. 
> 
> Se si completa la procedura in questo documento e si ricevono comunque risposte 404, è consigliabile attendere alcune ore e controllare nuovamente prima di aprire un ticket di supporto.
> 
> 

### <a name="check-the-origin-file"></a>Controllare il file di origine
In primo luogo, verificare che il file da memorizzare nella cache sia disponibile nel server di origine e accessibile pubblicamente In Internet. Il modo più rapido per eseguire questa operazione consiste nell'aprire un browser in una sessione privata o anonima e passare direttamente al file. Digitare o incollare l'URL nella casella dell'indirizzo e verificare che restituisca il file desiderato. Si supponga ad esempio che si disponga di un file in account di Archiviazione di Azure, accessibile all'indirizzo https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Se è stato possibile caricare il contenuto del file, il test è stato superato.

![Completamento della procedura](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Anche se questo è il modo più rapido e semplice per verificare che il file sia disponibile pubblicamente, per alcune configurazioni di rete dell'organizzazione potrebbe sembrare che il file sia disponibile pubblicamente mentre in realtà è visibile solo agli utenti della rete (anche se è ospitato in Azure). Per garantire che la situazione è diversa, eseguire il test sul file con un browser esterno, ad esempio in un dispositivo mobile non connesso alla rete dell'organizzazione, oppure in una macchina virtuale in Azure.
> 
> 

### <a name="check-the-origin-settings"></a>Controllare le impostazioni dell'origine
Dopo aver verificato che il file sia disponibile pubblicamente in Internet, verificare le impostazioni di origine. Nel [portale di Azure](https://portal.azure.com) passare al profilo della rete CDN e selezionare l'endpoint di cui è necessario risolvere i problemi. Nella pagina **Endpoint** visualizzata selezionare l'origine.  

![Pagina dell'endpoint con l'origine evidenziata](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

Viene visualizzata la pagina **Origine**. 

![Pagina di origine](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Tipo di origine e nome host
Verificare che i valori nei campi **Tipo di origine** e **Nome host dell'origine** siano corretti. Nell'esempio https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt la parte di nome host dell'URL è *cdndocdemo.blob.core.windows.net*, che è corretta. Poiché le origini di Archiviazione di Azure, App Web e servizio cloud usano un valore di un elenco a discesa per il campo **Nome host dell'origine**, l'ortografia errata non rappresenta un problema. Se si usa un'origine personalizzata, verificare tuttavia che il nome host sia stato digitato correttamente.

#### <a name="http-and-https-ports"></a>Porte HTTP e HTTPS
Controllare le porte **HTTP** e **HTTPS**. Nella maggior parte dei casi, 80 e 443 sono corrette e non saranno necessarie modifiche.  Tuttavia, se il server di origine è in ascolto su una porta diversa, dovrà essere rappresentata qui. Se non si è certi, visualizzare l'URL per il file di origine. Le specifiche HTTP e HTTPS indicano le porte 80 e 443 per impostazione predefinita. Nell'URL di esempio https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt non è specificata alcuna porta, quindi viene usato il valore predefinito 443 e le impostazioni sono corrette.  

Si supponga tuttavia che l'URL per il file di origine sia testato in precedenza sia http:\//www.contoso.com:8080/file.txt. Si noti la parte *:8080* alla fine del segmento del nome host. Che numero indica al browser di usare la porta 8080 per connettersi al server web all'indirizzo www\.contoso.com, pertanto sarà necessario immettere *8080* nel **porta HTTP** campo. È importante notare che queste impostazioni della porta hanno effetto solo sulla porta usata dall'endpoint per recuperare informazioni dall'origine.

> [!NOTE]
> Gli endpoint di **rete CDN Standard di Azure con tecnologia Akamai** non consentono di usare l'intera gamma di porte TCP per le origini.  Per un elenco delle porte di origine non consentite, vedere l'articolo relativo ai [Azure CDN from Akamai Allowed Origin Ports](/previous-versions/azure/mt757337(v=azure.100))(Porte di origine consentite in Rete CDN di Azure da Akamai).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Controllare le impostazioni dell'endpoint
Nella pagina **Endpoint** selezionare il pulsante **Configura**.

![Pagina Endpoint con il pulsante Configura evidenziato](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Viene visualizzata la pagina **Configura** dell'endpoint di rete CDN.

![Pagina Configura](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protocolli
In **Protocolli**verificare che sia selezionato il protocollo usato dai client. Lo stesso protocollo usato dal client è quello usato per accedere all'origine, quindi è importante che le porte di origine siano configurate correttamente nella sezione precedente. L'endpoint di rete CDN è in ascolto solo sulle porte HTTP e HTTPS (80 e 443) predefinite, indipendentemente dalle porte di origine.

Torniamo all'esempio con http:\//www.contoso.com:8080/file.txt.  Come indicato in precedenza, Contoso ha specificato *8080* come porta HTTP, ma si supponga che sia stata specificata la porta *44300* come porta HTTPS.  Se è stato creato un endpoint denominato *contoso*, il nome host dell'endpoint di rete CDN sarebbe *contoso.azureedge.net*.  Una richiesta per http:\//contoso.azureedge.net/file.txt è una richiesta HTTP, pertanto l'endpoint userebbe HTTP sulla porta 8080 per eseguire il recupero dall'origine.  Una richiesta protetta su HTTPS, ovvero https:\//contoso.azureedge.net/file.txt, indicherebbe all'endpoint di usare HTTPS sulla porta 44300 quando recupera il file dall'origine.

#### <a name="origin-host-header"></a>Intestazione host di origine
L'opzione **Intestazione host di origine** è il valore dell'intestazione host inviato all'origine con ogni richiesta.  Nella maggior parte dei casi deve essere identico al valore di **Nome host dell'origine** verificato in precedenza.  Un valore non corretto in questo campo non causa in genere stati 404, ma può causare altri stati 4xx, a seconda di quanto previsto dall'origine.

#### <a name="origin-path"></a>Percorso dell'origine
Infine è necessario verificare il **Percorso dell'origine**.  Per impostazione predefinita è vuoto.  Usare questo campo solo se si vuole limitare l'ambito delle risorse ospitate dall'origine da rendere disponibili nella rete CDN.  

Nell'endpoint di esempio tutte le risorse dell'account di archiviazione dovevano essere disponibili e di conseguenza il campo **Percorso dell'origine** è stato lasciato vuoto.  Ciò significa che una richiesta a https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt restituisce una connessione dall'endpoint a cdndocdemo.core.windows.net che richiede */publicblob/lorem.txt*.  In modo analogo, una richiesta per https:\//cdndocdemo.azureedge.net/donotcache/status.png comporta una richiesta */donotcache/status.png* da parte dell'endpoint all'origine.

Cosa accade se non si intende usare la rete CDN per ogni percorso dell'origine?  Supponiamo ad esempio che si desideri esporre il percorso *publicblob*.  Se si immette */publicblob* nel campo **Percorso dell'origine**, l'endpoint inserirà */publicblob* prima di ogni richiesta inviata all'origine.  Ciò significa che la richiesta per https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt accetta la parte di richiesta dell'URL, ovvero */publicblob/lorem.txt*, e aggiunge */publicblob* all'inizio. Viene quindi eseguita una richiesta per */publicblob/publicblob/lorem.txt* all'origine.  Se tale percorso non viene risolto in un file effettivo, l'origine restituirà uno stato 404.  L'URL corretto per il recupero del file lorem.txt in questo esempio sarebbe https:\//cdndocdemo.azureedge.net/lorem.txt.  Si noti che non si include il percorso */publicblob* perché la parte di richiesta dell'URL è */lorem.txt* e l'endpoint aggiunge */publicblob*, che determina l'invio della richiesta di */publicblob/lorem.txt* all'origine.

