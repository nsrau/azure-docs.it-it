---
title: Esercitazione Registro contenitori di Azure - Eseguire il push di un'immagine aggiornata nelle distribuzioni regionali
description: "Eseguire il push di un'immagine Docker modificata nel registro contenitori di Azure con replica geografica e quindi visualizzare le modifiche distribuite automaticamente alle app Web in esecuzione in più aree. Terza parte di una serie in tre parti."
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: mmacy
tags: acr, azure-container-registry, geo-replication
keywords: Docker, contenitori, Registro, Azure
ms.service: container-registry
ms.devlang: 
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 049fba28d0783a79331e8bc8de741f55e9caf828
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="push-an-updated-image-to-regional-deployments"></a>Eseguire il push di un'immagine aggiornata nelle distribuzioni regionali

Questa è la terza parte di un'esercitazione in tre parti. Nell'[esercitazione precedente](container-registry-tutorial-deploy-app.md) è stata configurata la replica geografica per due diverse distribuzioni regionali di app Web. In questa esercitazione, verrà prima di tutto modificata l'applicazione, poi si compilerà una nuova immagine del contenitore e si eseguirà il push di tale immagine nel registro con replica geografica. Infine, si visualizzerà la modifica, distribuita automaticamente dai webhook di Registro contenitori di Azure, in entrambe le istanze delle app Web.

In questa esercitazione, l'ultima parte della serie, vengono illustrate le seguenti attività:

> [!div class="checklist"]
> * Modificare il codice HTML dell'applicazione Web
> * Compilare l'immagine Docker a aggiungere tag per l'immagine
> * Eseguire il push della modifica in Registro contenitori di Azure
> * Visualizzare l'app aggiornata in due aree diverse

Se non sono ancora state configurate le due distribuzioni regionali di *app Web per contenitori*, tornare all'esercitazione precedente della serie, [Distribuire un'app Web dal Registro contenitori di Azure](container-registry-tutorial-deploy-app.md).

## <a name="modify-the-web-application"></a>Modificare l'applicazione web

In questo passaggio verrà apportata una modifica all'applicazione Web che risulterà molto visibile dopo il push dell'immagine del contenitore aggiornata in Registro contenitori di Azure.

Trovare il file `AcrHelloworld/Views/Home/Index.cshtml` nell'origine dell'applicazione [clonata da GitHub](container-registry-tutorial-prepare-registry.md#get-application-code) in un'esercitazione precedente e aprirlo nell'editor di testo preferito. Aggiungere la riga seguente sotto la riga `<h1>` esistente:

```html
<h1>MODIFIED</h1>
```

Il file `Index.cshtml` modificato deve essere simile al seguente:

```html
@{
    ViewData["Title"] = "Azure Container Registry :: Geo-replication";
}
<style>
    body {
        background-image: url('images/azure-regions.png');
        background-size: cover;
    }
    .footer {
        position: fixed;
        bottom: 0px;
        width: 100%;
    }
</style>

<h1 style="text-align:center;color:blue">Hello World from:  @ViewData["REGION"]</h1>
<h1>MODIFIED</h1>
<div class="footer">
    <ul>
        <li>Registry URL: @ViewData["REGISTRYURL"]</li>
        <li>Registry IP: @ViewData["REGISTRYIP"]</li>
        <li>Registry Region: @ViewData["REGION"]</li>
    </ul>
</div>
```

## <a name="rebuild-the-image"></a>Ricompilare l'immagine

Dopo aver aggiornato l'applicazione Web, è necessario ricompilare l'immagine del contenitore. Come in precedenza, usare il nome dell'immagine completo, incluso l'URL del server di accesso, per il tag:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="push-image-to-azure-container-registry"></a>Eseguire il push dell'immagine in Registro contenitori di Azure

A questo punto, eseguire il push dell'immagine del contenitore aggiornata *acr helloworld* nel registro con replica geografica. In questo caso, viene eseguito un singolo comando `docker push` per distribuire l'immagine aggiornata nelle repliche del registro in entrambe le aree *Stati Uniti occidentali* e *Stati Uniti orientali*.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

L'output sarà simile al seguente:

```bash
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
5b9454e91555: Pushed
d6803756744a: Layer already exists
b7b1f3a15779: Layer already exists
a89567dff12d: Layer already exists
59c7b561ff56: Layer already exists
9a2f9413d9e4: Layer already exists
a75caa09eb1f: Layer already exists
v1: digest: sha256:4c3f2211569346fbe2d1006c18cbea2a4a9dcc1eb3a078608cef70d3a186ec7a size: 1792
```

## <a name="view-the-webhook-logs"></a>Visualizzare i log dei webhook

Durante la replica dell'immagine è possibile visualizzare i webhook di Registro contenitori di Azure attivati.

Per visualizzare i webhook regionali creati in seguito alla distribuzione del contenitore in *app Web per contenitori* in un'esercitazione precedente, passare al registro di contenitori nel portale di Azure e quindi selezionare **Webhook**in **SERVIZI**.

![Webhook del registro contenitori nel portale di Azure][tutorial-portal-01]

Selezionare ogni webhook per visualizzare la cronologia delle chiamate e delle risposte. Nei log di entrambi i webhook dovrebbe essere presente una riga per l'azione **push**. In questo caso, il log per il webhook nell'area *Stati Uniti occidentali* mostra l'azione **push** attivata tramite `docker push` nel passaggio precedente:

![Log dei webhook del registro contenitori nel portale di Azure (Stati Uniti occidentali)][tutorial-portal-02]

## <a name="view-the-updated-web-app"></a>Visualizzare l'app Web aggiornata

I webhook inviano notifica alle app Web del push di una nuova immagine nel registro, che distribuisce automaticamente il contenitore aggiornato nelle due app Web regionali.

Verificare che l'applicazione sia stata aggiornata in entrambe le distribuzioni passando a entrambe le distribuzioni di app Web regionali nel Web browser. Se occorre, è possibile trovare l'URL per l'app Web distribuita in alto a destra di ogni scheda di panoramica del servizio app.

![Panoramica del servizio app nel portale di Azure][tutorial-portal-03]

Per visualizzare l'applicazione aggiornata, selezionare il collegamento nella panoramica del servizio app. Ecco una visualizzazione di esempio dell'app in esecuzione nell'area *Stati Uniti occidentali*:

![Visualizzazione nel browser dell'app Web modificata in esecuzione nell'area Stati Uniti occidentali][deployed-app-westus-modified]

Verificare che l'immagine del contenitore aggiornata sia stata distribuita anche nella distribuzione per l'area *Stati Uniti orientali* visualizzandola nel browser.

![Visualizzazione nel browser dell'app Web modificata in esecuzione nell'area Stati Uniti orientali][deployed-app-eastus-modified]

Con una singola operazione `docker push` sono state aggiornate entrambe le distribuzioni di app Web regionali e Registro contenitori di Azure ha fornito le immagini del contenitore dai repository vicini in rete.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è visto come aggiornare il contenitore dell'applicazione Web ed eseguire il push della nuova versione nel registro con replica geografica. I webhook in Registro contenitori di Azure inviano una notifica dell'aggiornamento all'app Web per contenitori, che attiva un pull locale delle repliche dei registri.

In questa esercitazione, l'ultima parte della serie, sono state completate le attività seguenti:

> [!div class="checklist"]
> * Aggiornamento del codice HTML dell'applicazione Web
> * Compilazione e aggiunta di tag all'immagine Docker
> * Push della modifica in Registro contenitori di Azure
> * Visualizzazione dell'app aggiornata in due aree diverse

<!-- IMAGES -->
[deployed-app-eastus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-eastus-modified.png
[deployed-app-westus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-westus-modified.png
[local-container-01]: ./media/container-registry-tutorial-deploy-update/local-container-01.png
[tutorial-portal-01]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-01.png
[tutorial-portal-02]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-02.png
[tutorial-portal-03]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-03.png