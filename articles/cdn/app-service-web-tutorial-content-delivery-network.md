---
title: Esercitazione - Aggiungere una rete CDN a un'app Web del Servizio app di Azure | Microsoft Docs
description: In questa esercitazione viene aggiunta una rete per la distribuzione di contenuti (CDN) a un'app Web del Servizio app di Azure per memorizzare nella cache e distribuire i file statici dai server vicini ai clienti in tutto il mondo.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: d782f5bf30160d85d7d9ef7a00190551f9a9843a
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161245"
---
# <a name="tutorial-add-azure-cdn-to-an-azure-app-service-web-app"></a>Esercitazione: Aggiungere una rete CDN a un'app Web del Servizio app di Azure

Questa esercitazione illustra come aggiungere la rete CDN di Azure a un'[app Web nel servizio app di Azure](../app-service/app-service-web-overview.md). La [rete per la distribuzione di contenuti (CDN) di Azure](cdn-overview.md) memorizza nella cache il contenuto Web statico in località strategiche per offrire la massima velocità effettiva per la distribuzione del contenuto agli utenti. La rete CDN di Azure riduce anche il carico del server per l'app Web. 

Di seguito è riportata la home page del sito HTML statico di esempio che verrà usato:

![Home page dell'app di esempio](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page.png)

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Creare un endpoint della rete CDN.
> * Aggiornare gli asset memorizzati nella cache.
> * Usare stringhe di query per controllare le versioni memorizzate nella cache.
> * Usare un dominio personalizzato per l'endpoint della rete CDN.

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione:

- [Installare Git](https://git-scm.com/)
- [Installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>Creare l'app Web

Per creare l'app Web che verrà usata, seguire le istruzioni riportate nella [guida introduttiva per siti HTML statici](../app-service/app-service-web-get-started-html.md) fino al passaggio **Pulire le risorse**.

### <a name="have-a-custom-domain-ready"></a>Preparare un dominio personalizzato

Per completare il passaggio di questa esercitazione relativo al dominio personalizzato, è necessario essere proprietario di un dominio personalizzato e avere accesso al registro DNS per il provider di dominio. Ad esempio, per aggiungere le voci DNS per contoso.com e www.contoso.com, è necessario avere accesso per configurare le impostazioni DNS per il dominio radice contoso.com.

Se non si ha ancora un nome di dominio, può essere opportuno seguire l'[esercitazione relativa ai domini nel servizio app](../app-service/custom-dns-web-site-buydomains-web-app.md) per acquistare un dominio usando il portale di Azure. 

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure

Aprire un browser e passare al [portale di Azure](https://portal.azure.com).

## <a name="create-a-cdn-profile-and-endpoint"></a>Creare un profilo e un endpoint della rete CDN

Nel riquadro di spostamento a sinistra selezionare **Servizi app** e quindi l'app creata nella [guida introduttiva per siti HTML statici](../app-service/app-service-web-get-started-html.md).

![Selezionare Servizi app nel portale](media/app-service-web-tutorial-content-delivery-network/portal-select-app-services.png)

Nella sezione **Impostazioni** della pagina **Servizio app** selezionare **Rete > Configurare la rete CDN di Azure per l'app**.

![Selezionare la rete CDN nel portale](media/app-service-web-tutorial-content-delivery-network/portal-select-cdn.png)

Nella pagina **Rete per la distribuzione di contenuti di Azure** specificare le impostazioni in **Nuovo endpoint** come indicato nella tabella.

![Creare un profilo e un endpoint nel portale](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint.png)

| Impostazione | Valore consigliato | DESCRIZIONE |
| ------- | --------------- | ----------- |
| **Profilo CDN** | myCDNProfile | Selezionare **Crea nuovo** per creare un profilo di rete CDN. Un profilo di rete CDN è una raccolta di endpoint della rete CDN con lo stesso piano tariffario. |
| **Piano tariffario** | Standard Akamai | Il [piano tariffario](cdn-features.md) specifica il provider e le funzionalità disponibili. Questa esercitazione usa *Akamai standard*. |
| **Nome endpoint rete CDN** | Qualsiasi nome univoco nel dominio azureedge.net | Si accede alle risorse memorizzate nella cache nel dominio *\<nomeendpoint>.azureedge.net*.

Selezionare **Create**.

Azure crea il profilo e l'endpoint. Il nuovo endpoint viene visualizzato nell'elenco **Endpoint** e al termine del relativo provisioning lo stato è **In esecuzione**.

![Nuovo endpoint nell'elenco](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>Testare l'endpoint della rete CDN

Se è stato selezionato il piano tariffario Verizon, la propagazione dell'endpoint richiede in genere circa 90 minuti. Per Akamai è sufficiente qualche minuto.

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

![Home page dell'app di esempio fornita dalla rete CDN](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page-cdn.png)

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

!["V2" nel titolo nell'app Web](media/app-service-web-tutorial-content-delivery-network/v2-in-web-app-title.png)

Se si passa all'URL dell'endpoint della rete CDN per la home page, la modifica non verrà visualizzata perché la versione memorizzata nella cache nella rete CDN non è ancora scaduta. 

```
http://<endpointname>.azureedge.net/index.html
```

![Titolo nella rete CDN senza "V2"](media/app-service-web-tutorial-content-delivery-network/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>Ripulire la rete CDN nel portale

Per attivare l'aggiornamento della versione memorizzata nella cache nella rete CDN, ripulire la rete CDN.

Nel riquadro di spostamento a sinistra nel portale selezionare **Gruppi di risorse** e quindi il gruppo di risorse creato per l'app Web (myResourceGroup).

![Selezionare il gruppo di risorse](media/app-service-web-tutorial-content-delivery-network/portal-select-group.png)

Nell'elenco delle risorse selezionare l'endpoint della rete CDN.

![Selezionare l'endpoint](media/app-service-web-tutorial-content-delivery-network/portal-select-endpoint.png)

Nella parte superiore della pagina **Endpoint** selezionare **Ripulisci**.

![Selezionare Ripulisci](media/app-service-web-tutorial-content-delivery-network/portal-select-purge.png)

Immettere i percorsi del contenuto che si vuole ripulire. È possibile passare un percorso file completo per ripulire un singolo file oppure un segmento di percorso per ripulire e aggiornare tutto il contenuto in una cartella. Poiché è stato modificato *index.html*, assicurarsi che si trovi in uno dei percorsi.

Nella parte inferiore della pagina selezionare **Ripulisci**.

![Pagina Ripulisci](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>Verificare che la rete CDN sia aggiornata

Attendere il completamento dell'elaborazione della richiesta di ripulitura, che in genere richiede qualche minuto. Per visualizzare lo stato corrente, selezionare l'icona a forma di campana nella parte superiore della pagina. 

![Notifica di ripulitura](media/app-service-web-tutorial-content-delivery-network/portal-purge-notification.png)

Quando si passa all'URL dell'endpoint della rete CDN per *index.html*, verrà visualizzato il valore *V2* aggiunto al titolo nella home page, che indica che la cache della rete CDN è stata aggiornata.

```
http://<endpointname>.azureedge.net/index.html
```

!["V2" nel titolo nella rete CDN](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title.png)

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

![Selezionare il comportamento di memorizzazione nella cache della stringa di query](media/app-service-web-tutorial-content-delivery-network/portal-select-caching-behavior.png)

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

!["V3" nel titolo nella rete CDN, con stringa di query 2](media/app-service-web-tutorial-content-delivery-network/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

!["V2" nel titolo nella rete CDN, con stringa di query 1](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title-qs1.png)

Questo output mostra che ogni stringa di query viene trattata in modo diverso:

* Poiché q=1 è stata usata in precedenza, vengono restituiti i contenuti memorizzati nella cache (V2).
* Poiché q=2 non è mai stata usata, vengono recuperati e restituiti i contenuti dell'app Web più recenti (V3).

Per altre informazioni, vedere [Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con stringhe di query](../cdn/cdn-query-string.md).

## <a name="map-a-custom-domain-to-a-cdn-endpoint"></a>Eseguire il mapping di un dominio personalizzato a un endpoint della rete CDN

Per eseguire il mapping del dominio personalizzato all'endpoint della rete CDN si creerà un record CNAME. Un record CNAME è una funzionalità DNS tramite cui viene eseguito il mapping di un dominio di origine a uno di destinazione. Ad esempio, si potrebbe mappare cdn.contoso.com o static.contoso.com a contoso.azureedge.net.

Se non si ha un dominio personalizzato, può essere opportuno seguire l'[esercitazione relativa ai domini nel servizio app](../app-service/custom-dns-web-site-buydomains-web-app.md) per acquistare un dominio usando il portale di Azure. 

### <a name="find-the-hostname-to-use-with-the-cname"></a>Trovare il nome host da usare con il record CNAME

Nella pagina **Endpoint** del portale di Azure assicurarsi che nel riquadro di spostamento a sinistra sia selezionata l'opzione **Panoramica** e quindi selezionare il pulsante **+ Dominio personalizzato** nella parte superiore della pagina.

![Selezionare l'aggiunta di un dominio personalizzato](media/app-service-web-tutorial-content-delivery-network/portal-select-add-domain.png)

Nella pagina **Aggiungi dominio personalizzato** verrà visualizzato il nome host dell'endpoint da usare per la creazione di un record CNAME. Il nome host è derivato dall'URL dell'endpoint della rete CDN: **&lt;NomeEndpoint>.azureedge.net**. 

![Pagina per l'aggiunta di un dominio](media/app-service-web-tutorial-content-delivery-network/portal-add-domain.png)

### <a name="configure-the-cname-with-your-domain-registrar"></a>Configurare il record CNAME con il registrar

Passare al sito Web del registrar e individuare la sezione per la creazione di record DNS. Ad esempio, questa sezione potrebbe essere denominata **Nome di dominio**, **DNS** o **Gestione del server dei nomi**.

Individuare la sezione per la gestione dei record CNAME. Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole CNAME, Alias o Subdomains.

Creare un record CNAME per il mapping del sottodominio scelto (ad esempio, **static** o **cdn**) al **nome host dell'endpoint** visualizzato in precedenza nel portale. 

### <a name="enter-the-custom-domain-in-azure"></a>Immettere il dominio personalizzato in Azure

Tornare alla pagina **Aggiungi dominio personalizzato** e immettere il dominio personalizzato, includendo il sottodominio, nella finestra di dialogo. Ad esempio, immettere *cdn.contoso.com*.   
   
Azure verifica l'esistenza del record CNAME per il nome di dominio immesso. Se il record CNAME è corretto, il dominio personalizzato viene convalidato.

La propagazione del record CNAME nei server dei nomi in Internet può richiedere tempo. Se il dominio non viene convalidato immediatamente, attendere qualche minuto e riprovare.

### <a name="test-the-custom-domain"></a>Testare il dominio personalizzato

In un browser passare al file *index.html* usando il dominio personalizzato (ad esempio, cdn.contoso.com/index.html) per verificare che il risultato sia lo stesso ottenuto passando direttamente a &lt;nomeendpoint&gt;azureedge.net/index.html.

![Home page dell'app di esempio con URL del dominio personalizzato](media/app-service-web-tutorial-content-delivery-network/home-page-custom-domain.png)

Per altre informazioni, vedere [Eseguire il mapping del contenuto della rete CDN di Azure a un dominio personalizzato](../cdn/cdn-map-content-to-custom-domain.md).

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Creare un endpoint della rete CDN.
> * Aggiornare gli asset memorizzati nella cache.
> * Usare stringhe di query per controllare le versioni memorizzate nella cache.
> * Usare un dominio personalizzato per l'endpoint della rete CDN.

Per informazioni su come ottimizzare la rete CDN, vedere gli articoli seguenti:

> [!div class="nextstepaction"]
> [Esercitazione: Aggiungere un dominio personalizzato all'endpoint della rete CDN di Azure](cdn-map-content-to-custom-domain.md)


