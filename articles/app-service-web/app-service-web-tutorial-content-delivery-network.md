---
title: Aggiungere una rete per la distribuzione di contenuti in un servizio app di Azure | Microsoft Docs
description: Aggiungere una rete per la distribuzione di contenuti in un servizio app di Azure per distribuire i file statici da nodi perimetrali.
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
ms.translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 7ba3737566401152a3171e8926beca188045230c
ms.contentlocale: it-it
ms.lasthandoff: 04/22/2017

---
# <a name="add-a-content-deliver-network-on-an-azure-app-service"></a>Aggiungere una rete per la distribuzione di contenuti in un servizio app di Azure

In questa esercitazione si aggiungerà una rete per la distribuzione di contenuti (rete CDN) al servizio app di Azure per esporre i contenuti statici in un server perimetrale. Si creerà un profilo di rete CDN, che è una raccolta di un massimo di 10 endpoint di rete CDN.

Una rete per la distribuzione di contenuti memorizza nella cache il contenuto Web statico in località strategiche per offrire la massima velocità effettiva per la distribuzione del contenuto agli utenti. L'uso della rete CDN per memorizzare nella cache gli asset dei siti Web offre diversi vantaggi, inclusi i seguenti:

* Una migliore esperienza utente e migliori prestazioni per gli utenti finali, specialmente quando usano applicazioni in cui sono necessari più round trip per caricare il contenuto.
* Grande scalabilità per gestire al meglio un carico elevato immediato, ad esempio all'inizio di un evento per il lancio di un prodotto.
* Grazie alla distribuzione delle richieste utente e alla gestione del contenuto da server perimetrali, viene inviata una minore quantità di traffico all'origine.

> [!TIP]
> Vedere l'elenco aggiornato delle [località POP della rete CDN di Azure](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations).
>

## <a name="deploy-the-sample"></a>Distribuire l'esempio

Per completare questa esercitazione, sarà necessaria un'applicazione distribuita nell'app Web. Seguire la [guida introduttiva su HTML statico](app-service-web-get-started-html.md) che costituisce la base per questa esercitazione.

## <a name="step-1---login-to-azure-portal"></a>Passaggio 1: accedere al portale di Azure

Aprire un browser a scelta e passare al [portale](https://portal.azure.com) di Azure.

## <a name="step-2---create-a-cdn-profile"></a>Passaggio 2: creare un profilo della rete CDN

Nel riquadro di spostamento a sinistra fare clic sul pulsante `+ New` e quindi su **Web e dispositivi mobili**. Nella categoria Web e dispositivi mobili selezionare **Rete CDN**.

Specificare i campi seguenti:

| Campo | Valore di esempio | Descrizione |
|---|---|---|
| Nome | myCDNProfile | Un nome per il profilo della rete CDN. |
| Percorso | Europa occidentale | Questo è il percorso di Azure in cui verranno archiviate le informazioni relative al profilo di rete CDN. Non incide sulle posizioni dell’endpoint di rete CDN. |
| Gruppo di risorse | myResourceGroup | Per altre informazioni sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups). |
| Piano tariffario  | Standard Akamai | Per un confronto tra i piani tariffari, vedere [Panoramica della rete CDN](../cdn/cdn-overview.md#azure-cdn-features) . |

Fare clic su **Crea**.

Aprire l'hub dei gruppi di risorse dal riquadro di spostamento sinistro e selezionare **myResourceGroup**. Dall'elenco di risorse selezionare **myCDNProfile**.

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>Passaggio 3: creare un endpoint della rete CDN

Fare clic su **+ Endpoint** nei comandi accanto alla casella di ricerca per aprire il pannello di creazione di endpoint.

Specificare i campi seguenti:

| Campo | Valore di esempio | Descrizione |
|---|---|
| Nome |  | Questo nome verrà usato per accedere alle risorse memorizzate nella cache nel dominio `<endpointname>.azureedge.net`. |
| Tipo di origine | App Web | La selezione di un tipo di origine offre i menu di scelta rapida per i campi rimanenti. La selezione dell'origine personalizzata offrirà un campo di testo per il nome host dell'origine. |
| Nome host dell'origine | |  Nell'elenco a discesa compariranno tutte le origini disponibili del tipo specificato. Se è stato selezionata l'origine personalizzata come tipo di origine, si digiterà il dominio dell'origine personalizzata.  |

Fare clic su **Aggiungi**.

Verrà creato l'endpoint. Dopo aver creato l'endpoint della rete per la distribuzione di contenuti, lo stato diventerà **In esecuzione**.

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---serve-from-azure-cdn"></a>Passaggio 4: Eseguire la distribuzione dalla rete CDN di Azure

Dopo che l'endpoint della rete CDN è **in esecuzione**, dovrebbe essere possibile accedere al contenuto dall'endpoint della rete CDN.

Poiché è stata usata la [guida introduttiva su HTML statico](app-service-web-get-started-html.md) come base per questa esercitazione, nella rete CDN saranno disponibili le cartelle seguenti: `css`, `img`, `js`.

I percorsi del contenuto tra l'URL dell'app Web `http://<app_name>.azurewebsites.net/img/` e l'URL dell'endpoint della rete CDN `http://<endpointname>.azureedge.net/img/` sono gli stessi, quindi è sufficiente sostituire il dominio dell'endpoint della rete CDN in modo che qualsiasi contenuto statico venga distribuito dalla rete CDN.

Per effettuare il pull della prima immagine dall'endpoint della rete CDN, passare all'URL seguente nel Web browser preferito:

```bash
http://<endpointname>.azureedge.net/img/03-enterprise.png
```

Ora che il contenuto statico è disponibile nella rete CDN, è possibile aggiornare l'applicazione per usare l'endpoint della rete CDN per la distribuzione del contenuto all'utente finale.

A seconda del linguaggio con cui è stato compilato il sito, potrebbero essere presenti diversi framework a supporto del fallback della rete CDN. ASP.NET offre ad esempio il supporto per [creazione di bundle e minimizzazione](https://docs.microsoft.com/en-us/aspnet/mvc/overview/performance/bundling-and-minification#using-a-cdn) che abilita anche le funzionalità di fallback della rete CDN.

Se il linguaggio in uso non offre il supporto predefinito o di una libreria per il failback CDN, è possibile usare un framework JavaScript, ad esempio [FallbackJS](http://fallback.io/), che supporta il caricamento di [script](https://github.com/dolox/fallback/tree/master/examples/loading-scripts), [fogli di stile](https://github.com/dolox/fallback/tree/master/examples/loading-stylesheets) e [immagini](https://github.com/dolox/fallback/tree/master/examples/loading-images).

## <a name="step-5---purge-the-cdn"></a>Passaggio 5: Ripulire la rete CDN

In alcuni casi può essere necessario forzare una ripulitura della rete CDN, se si vuole impostare come scaduto il contenuto prima della scadenza della durata (TTL).

È possibile ripulire manualmente la rete CDN di Azure dal pannello Profilo CDN o dal pannello Endpoint rete CDN. Se si seleziona Ripulisci dalla pagina del profilo, sarà necessario selezionare l'endpoint che si vuole ripulire.

Per ripulire il contenuto, digitare i percorsi del contenuto che si vuole ripulire. È possibile passare un percorso file completo per ripulire un singolo file oppure un segmento di percorso per ripulire e aggiornare il contenuto di una determinata cartella.

Dopo avere specificato tutti i percorsi del contenuto che si vuole ripulire, fare clic su **Ripulisci**.

![app-service-web-purge-cdn](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

## <a name="step-6---map-a-custom-domain"></a>Passaggio 6: Eseguire il mapping di un dominio personalizzato

Il mapping di un dominio personalizzato all'endpoint della rete CDN fornisce un dominio uniforme per l'applicazione Web.

Per eseguire il mapping di un dominio personalizzato all'endpoint della rete CDN, creare un record CNAME con il registrar del dominio.

> [!NOTE]
> Un record CNAME è una funzionalità DNS con cui viene eseguito il mapping di un dominio di origine, come `www.contosocdn.com` o `static.contosocdn.com`, a uno di destinazione.

In questo caso si aggiungerà un dominio di origine `static.contosocdn.com` e si punterà al dominio di destinazione che è l'endpoint della rete CDN.

| dominio di origine | dominio di destinazione |
|---|---|
| static.contosocdn.com | &lt;nomeendpoint&gt;.azureedge.net |

Nel pannello di panoramica Endpoint rete CDN fare clic sul pulsante `+ Custom domain`.

Nel pannello Aggiungi un dominio personalizzato immettere il dominio personalizzato, incluso il sottodominio, nella finestra di dialogo. Ad esempio, immettere il nome di dominio nel formato `static.contosocdn.com`.

Fare clic su **Aggiungi**.

## <a name="step-7---version-content"></a>Passaggio 7 : Contenuto della versione

Nel riquadro di spostamento a sinistra di Endpoint rete CDN selezionare **Cache** nell'intestazione Impostazioni.

Il pannello **Cache** consente di configurare come la rete CDN gestisce le stringhe di query nella richiesta.

> [!NOTE]
> Per una descrizione delle opzioni di comportamento di memorizzazione nella cache delle stringhe di query, vedere l'argomento [Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con stringhe di query](../cdn/cdn-query-string.md).

Selezionare **Memorizza nella cache tutti gli URL univoci** nell'elenco a discesa Comportamento di memorizzazione nella cache della stringa di query.

Fare clic su **save**.

## <a name="next-steps"></a>Passaggi successivi

* [What is Azure CDN](../best-practices-cdn.md?toc=%2fazure%2fcdn%2ftoc.json) (Che cos'è la rete CDN di Azure)
* [Abilitare HTTPS in un dominio personalizzato della rete CDN di Azure](../cdn/cdn-custom-ssl.md)
* [Migliorare le prestazioni con la compressione dei file nella rete CDN di Azure](../cdn/cdn-improve-performance.md)
* [Precaricamento di risorse in un endpoint della rete CDN di Azure](../cdn/cdn-preload-endpoint.md)

