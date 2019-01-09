---
title: Esercitazione - Aggiungere una rete CDN a un'app Web del Servizio app di Azure | Microsoft Docs
description: In questa esercitazione viene aggiunta una rete per la distribuzione di contenuti (CDN) a un'app Web del Servizio app di Azure per memorizzare nella cache e distribuire i file statici dai server vicini ai clienti in tutto il mondo.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 33b47d33262a4968a0eafb9ec70ef73e50975735
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2018
ms.locfileid: "53602818"
---
# <a name="tutorial-add-azure-cdn-to-an-azure-app-service-web-app"></a>Esercitazione: Aggiungere una rete CDN di Azure a un'app Web di Servizio app di Azure

Questa esercitazione illustra come aggiungere la [rete CDN di Azure](cdn-overview.md) a un'[app Web nel servizio app di Azure](../app-service/overview.md). Le app Web sono un servizio per l'hosting di applicazioni Web, API REST e back-end mobili. 

Di seguito è riportata la home page del sito HTML statico di esempio che verrà usato:

![Home page dell'app di esempio](media/cdn-add-to-web-app/sample-app-home-page.png)

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Creare un endpoint della rete CDN.
> * Aggiornare gli asset memorizzati nella cache.
> * Usare stringhe di query per controllare le versioni memorizzate nella cache.


## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

- [Installare Git](https://git-scm.com/)
- [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>Creare l'app Web

Per creare l'app Web che verrà usata, seguire le istruzioni riportate nella [guida introduttiva per siti HTML statici](../app-service/app-service-web-get-started-html.md) fino al passaggio **Pulire le risorse**.

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure

Aprire un browser e passare al [portale di Azure](https://portal.azure.com).

### <a name="dynamic-site-acceleration-optimization"></a>Ottimizzazione dell'accelerazione sito dinamico
Se si vuole ottimizzare l'endpoint della rete CDN per l'accelerazione sito dinamico (DSA), usare il [portale CDN](cdn-create-new-endpoint.md) per creare il profilo e l'endpoint. L'[ottimizzazione DSA](cdn-dynamic-site-acceleration.md) migliora in modo misurabile le prestazioni delle pagine Web con contenuto dinamico. Per istruzioni su come ottimizzare un endpoint rete CDN per DSA dal portale della rete CDN, vedere [Configurazione di un endpoint di rete CDN per accelerare la distribuzione di file dinamici](cdn-dynamic-site-acceleration.md#cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files). In caso contrario, se non si vuole ottimizzare il nuovo endpoint, è possibile usare il portale dell'app Web per crearlo seguendo i passaggi descritti nella sezione successiva. Si noti che per i profili di **rete CDN di Azure con tecnologia Verizon**, non è possibile modificare l'ottimizzazione di un endpoint di rete CDN dopo che è stato creato.

## <a name="create-a-cdn-profile-and-endpoint"></a>Creare un profilo e un endpoint della rete CDN

Nel riquadro di spostamento a sinistra selezionare **Servizi app** e quindi l'app creata nella [guida introduttiva per siti HTML statici](../app-service/app-service-web-get-started-html.md).

![Selezionare Servizi app nel portale](media/cdn-add-to-web-app/portal-select-app-services.png)

Nella sezione **Impostazioni** della pagina **Servizio app** selezionare **Rete > Configurare la rete CDN di Azure per l'app**.

![Selezionare la rete CDN nel portale](media/cdn-add-to-web-app/portal-select-cdn.png)

Nella pagina **Rete per la distribuzione di contenuti di Azure** specificare le impostazioni in **Nuovo endpoint** come indicato nella tabella.

![Creare un profilo e un endpoint nel portale](media/cdn-add-to-web-app/portal-new-endpoint.png)

| Impostazione | Valore consigliato | Descrizione |
| ------- | --------------- | ----------- |
| **Profilo CDN** | myCDNProfile | Un profilo di rete CDN è una raccolta di endpoint della rete CDN con lo stesso piano tariffario. |
| **Piano tariffario** | Standard Akamai | Il [piano tariffario](cdn-features.md) specifica il provider e le funzionalità disponibili. Questa esercitazione usa *Akamai standard*. |
| **Nome endpoint rete CDN** | Qualsiasi nome univoco nel dominio azureedge.net | Si accede alle risorse memorizzate nella cache nel dominio *&lt;nomeendpoint&gt;*.azureedge.net.

Selezionare **Crea** per creare un profilo di rete CDN.

Azure crea il profilo e l'endpoint. Il nuovo endpoint viene visualizzato nell'elenco **Endpoint** e al termine del relativo provisioning lo stato è **In esecuzione**.

![Nuovo endpoint nell'elenco](media/cdn-add-to-web-app/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>Testare l'endpoint della rete CDN

 Dato che la propagazione della registrazione richiede tempo, l'endpoint non è immediatamente disponibile per l'uso: 
   - La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Microsoft** viene in genere completata in 10 minuti. 
   - La propagazione dei profili di **rete CDN Standard di Azure con tecnologia Akamai** viene in genere completata entro un minuto. 
   - La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Verizon** e della **rete CDN Premium di Azure con tecnologia Verizon** viene in genere completata entro 90 minuti. 

L'app di esempio include un file *index.html* e le cartelle *css*, *img* e *js* che contengono altri asset statici. I percorsi del contenuto per tutti questi file sono gli stessi nell'endpoint della rete CDN. Entrambi gli URL seguenti, ad esempio, accedono al file *bootstrap.css* nella cartella *css*:

```
http://<appname>.azurewebsites.net/css/bootstrap.css
```

```
http://<endpointname>.azureedge.net/css/bootstrap.css
```

Usare un browser per passare all'URL seguente:

```
http://<endpointname>.azureedge.net/index.html
```

![Home page dell'app di esempio fornita dalla rete CDN](media/cdn-add-to-web-app/sample-app-home-page-cdn.png)

 Viene visualizzata la stessa pagina eseguita in precedenza in un'app Web di Azure. La rete CDN di Azure ha recuperato gli asset dell'app Web di origine e li specifica dal proprio endpoint

Per assicurarsi che questa pagina sia memorizzata nella cache nella rete CDN, aggiornare la pagina. Affinché la rete CDN memorizzi nella cache il contenuto richiesto sono talvolta necessarie due richieste dello stesso asset.

Per altre informazioni sulla creazione dei profili e degli endpoint della rete CDN di Azure, vedere [Introduzione alla rete CDN di Azure](cdn-create-new-endpoint.md).

## <a name="purge-the-cdn"></a>Ripulire la rete CDN

La rete CDN aggiorna periodicamente le proprie risorse dall'app Web di origine in base alla configurazione della durata (TTL). La durata predefinita è di sette giorni.

Potrebbe essere talvolta necessario aggiornare la rete CDN prima della scadenza del valore TTL, ad esempio quando si distribuisce contenuto aggiornato nell'app Web. Per attivare un aggiornamento, ripulire manualmente le risorse della rete CDN. 

In questa sezione dell'esercitazione si distribuirà una modifica nell'app Web e si ripulirà la rete CDN per attivare l'aggiornamento della cache.

### <a name="deploy-a-change-to-the-web-app"></a>Distribuire una modifica nell'app Web

Aprire il file *index.html* e aggiungere *- V2* all'intestazione H1 come illustrato nell'esempio seguente: 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

Eseguire il commit della modifica e distribuirla nell'app Web.

```bash
git commit -am "version 2"
git push azure master
```

Al termine della distribuzione, passare all'URL dell'app Web per visualizzare la modifica.

```
http://<appname>.azurewebsites.net/index.html
```

!["V2" nel titolo nell'app Web](media/cdn-add-to-web-app/v2-in-web-app-title.png)

Se si passa all'URL dell'endpoint della rete CDN per la home page, la modifica non verrà visualizzata perché la versione memorizzata nella cache nella rete CDN non è ancora scaduta. 

```
http://<endpointname>.azureedge.net/index.html
```

![Titolo nella rete CDN senza "V2"](media/cdn-add-to-web-app/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>Ripulire la rete CDN nel portale

Per attivare l'aggiornamento della versione memorizzata nella cache nella rete CDN, ripulire la rete CDN.

Nel riquadro di spostamento a sinistra nel portale selezionare **Gruppi di risorse** e quindi il gruppo di risorse creato per l'app Web (myResourceGroup).

![Selezionare il gruppo di risorse](media/cdn-add-to-web-app/portal-select-group.png)

Nell'elenco delle risorse selezionare l'endpoint della rete CDN.

![Selezionare l'endpoint](media/cdn-add-to-web-app/portal-select-endpoint.png)

Nella parte superiore della pagina **Endpoint** selezionare **Ripulisci**.

![Selezionare Ripulisci](media/cdn-add-to-web-app/portal-select-purge.png)

Immettere i percorsi del contenuto che si vuole ripulire. È possibile passare un percorso file completo per ripulire un singolo file oppure un segmento di percorso per ripulire e aggiornare tutto il contenuto in una cartella. Poiché è stato modificato *index.html*, assicurarsi che si trovi in uno dei percorsi.

Nella parte inferiore della pagina selezionare **Ripulisci**.

![Pagina Ripulisci](media/cdn-add-to-web-app/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>Verificare che la rete CDN sia aggiornata

Attendere il completamento dell'elaborazione della richiesta di ripulitura, che in genere richiede qualche minuto. Per visualizzare lo stato corrente, selezionare l'icona a forma di campana nella parte superiore della pagina. 

![Notifica di ripulitura](media/cdn-add-to-web-app/portal-purge-notification.png)

Quando si passa all'URL dell'endpoint della rete CDN per *index.html*, verrà visualizzato il valore *V2* aggiunto al titolo nella home page, che indica che la cache della rete CDN è stata aggiornata.

```
http://<endpointname>.azureedge.net/index.html
```

!["V2" nel titolo nella rete CDN](media/cdn-add-to-web-app/v2-in-cdn-title.png)

Per altre informazioni, vedere [Ripulire un endpoint della rete CDN di Azure](../cdn/cdn-purge-endpoint.md). 

## <a name="use-query-strings-to-version-content"></a>Usare le stringhe di query per il controllo delle versioni del contenuto

Per il comportamento di memorizzazione nella cache, la rete CDN di Azure offre le opzioni seguenti:

* Ignora stringhe di query
* Disabilita la memorizzazione nella cache per le stringhe di query
* Memorizza nella cache tutti gli URL univoci 

La prima è l'opzione predefinita, ovvero esiste una sola versione di un asset memorizzata nella cache indipendentemente dalla stringa di query nell'URL. 

In questa sezione dell'esercitazione si modificherà il comportamento per memorizzare nella cache tutti gli URL univoci.

### <a name="change-the-cache-behavior"></a>Modificare il comportamento della cache

Nella pagina **Endpoint rete CDN** del portale di Azure selezionare **Cache**.

Selezionare **Memorizza nella cache tutti gli URL univoci** nell'elenco a discesa **Comportamento di memorizzazione nella cache della stringa di query**.

Selezionare **Salva**.

![Selezionare il comportamento di memorizzazione nella cache della stringa di query](media/cdn-add-to-web-app/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>Verificare che gli URL univoci vengano memorizzati nella cache separatamente

In un browser passare alla home page nell'endpoint della rete CDN e includere una stringa di query: 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

La rete CDN d Azure restituisce il contenuto corrente dell'app Web, che include *V2* nell'intestazione. 

Per assicurarsi che questa pagina sia memorizzata nella cache nella rete CDN, aggiornare la pagina. 

Aprire *index.html*, modificare *V2* in *V3* e quindi distribuire la modifica. 

```bash
git commit -am "version 3"
git push azure master
```

In un browser passare all'URL dell'endpoint della rete CDN con una nuova stringa di query, ad esempio `q=2`. La rete CDN recupera il file *index.html* corrente e visualizza *V3*. Se invece si passa all'endpoint della rete CDN con la stringa di query `q=1`, viene visualizzato *V2*.

```
http://<endpointname>.azureedge.net/index.html?q=2
```

!["V3" nel titolo nella rete CDN, con stringa di query 2](media/cdn-add-to-web-app/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

!["V2" nel titolo nella rete CDN, con stringa di query 1](media/cdn-add-to-web-app/v2-in-cdn-title-qs1.png)

Questo output mostra che ogni stringa di query viene trattata in modo diverso:

* Poiché q=1 è stata usata in precedenza, vengono restituiti i contenuti memorizzati nella cache (V2).
* Poiché q=2 non è mai stata usata, vengono recuperati e restituiti i contenuti dell'app Web più recenti (V3).

Per altre informazioni, vedere [Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con stringhe di query](../cdn/cdn-query-string.md).

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Creare un endpoint della rete CDN.
> * Aggiornare gli asset memorizzati nella cache.
> * Usare stringhe di query per controllare le versioni memorizzate nella cache.

Per informazioni su come ottimizzare la rete CDN, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Esercitazione: Aggiungere un dominio personalizzato all'endpoint della rete CDN di Azure](cdn-map-content-to-custom-domain.md)


