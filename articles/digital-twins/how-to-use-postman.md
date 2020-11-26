---
title: Invia le richieste con il post
titleSuffix: Azure Digital Twins
description: Informazioni su come configurare e usare il post per testare le API dei dispositivi gemelli digitali di Azure.
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: f9be9dd94aad8c206b562f2c984ec944f70d3957
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188050"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Come usare il post per inviare richieste alle API dei dispositivi gemelli digitali di Azure

Il [post](https://www.getpostman.com/) è uno strumento di test REST che fornisce funzionalità di richiesta http chiave in un'interfaccia utente grafica basata su desktop e plug-in. È possibile usarlo per creare richieste HTTP e inviarle alle [API REST di Digital gemelli di Azure](how-to-use-apis-sdks.md).

Questo articolo descrive come configurare il [client REST di post](https://www.getpostman.com/) per interagire con le API di Azure Digital gemelli, seguendo questa procedura:

1. Usare l' [interfaccia](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) della riga di comando di Azure per ottenere un Bearer token che si userà per eseguire richieste API in un altro utente.
1. Configurare una raccolta di post e configurare il client REST per l'uso del bearer token per l'autenticazione.
1. Usare il post di pubblicazione configurato per creare e inviare una richiesta alle API dei dispositivi gemelli digitali di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per continuare a usare il post per accedere alle API dei dispositivi gemelli digitali di Azure, è necessario configurare un'istanza di dispositivi gemelli digitali di Azure e scaricare il post. Nella parte restante di questa sezione vengono illustrati questi passaggi.

### <a name="set-up-azure-digital-twins-instance"></a>Configurare l'istanza di Gemelli digitali di Azure

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Scarica il post

Scaricare quindi la versione desktop del client di post. Passare a [*www.getpostman.com/Apps*](https://www.getpostman.com/apps) e seguire le istruzioni per scaricare l'app.

## <a name="get-bearer-token"></a>Ottenere bearer token

Dopo aver configurato il post e l'istanza di Azure Digital gemelli, è necessario ottenere un bearer token che le richieste del post di pubblicazione possano usare per l'autorizzazione alle API dei dispositivi gemelli digitali di Azure.

È possibile ottenere questo token in diversi modi. Questo articolo usa l' [interfaccia](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) della riga di comando di Azure per accedere all'account Azure e ottenere un token in questo modo.

Se è installata un'interfaccia della riga di comando di Azure in [locale](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), è possibile avviare un prompt dei comandi nel computer per eseguire i comandi seguenti.
In caso contrario, è possibile aprire una finestra di [Azure cloud Shell](https://shell.azure.com) nel browser ed eseguire i comandi in tale finestra.

1. Prima di tutto, assicurarsi di essere connessi ad Azure con le credenziali appropriate eseguendo questo comando:

    ```azurecli-interactive
    az login
    ```

1. Usare quindi il comando [AZ account Get-Access-token](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest&preserve-view=true#az_account_get_access_token) per ottenere un Bearer token con accesso al servizio Azure Digital Twins.

    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

1. Copiare il valore di `accessToken` nel risultato e salvarlo per usarlo nella sezione successiva. Si tratta del **valore del token** che verrà fornito a post per autenticare le richieste.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Visualizzazione di una finestra della console locale che mostra il risultato del comando AZ account Get-Access-token. Uno dei campi nel risultato è denominato accessToken e il relativo valore di esempio, a partire da EY, viene evidenziato.":::

>[!TIP]
>Questo token è valido per almeno cinque minuti e un massimo di 60 minuti. Se si esaurisce il tempo assegnato per il token corrente, è possibile ripetere i passaggi in questa sezione per ottenerne uno nuovo.

## <a name="set-up-postman-collection-and-authorization"></a>Configurare la raccolta e l'autorizzazione di post-backup

Successivamente, configurare il post per eseguire le richieste API.
Questi passaggi si verificano nell'applicazione locale del post, quindi aprire l'applicazione postazione nel computer.

### <a name="create-a-postman-collection"></a>Creare una raccolta di Postman

Le richieste in Posting vengono salvate in **raccolte** (gruppi di richieste). Quando si crea una raccolta per raggruppare le richieste, è possibile applicare le impostazioni comuni a numerose richieste contemporaneamente. Questo può semplificare notevolmente l'autorizzazione se si prevede di creare più di una richiesta per le API dei dispositivi gemelli digitali di Azure, perché è necessario configurare l'autenticazione una sola volta per l'intera raccolta.

1. Per creare una raccolta, fare clic sul pulsante *+ nuovo insieme* .

    :::image type="content" source="media/how-to-use-postman/postman-new-collection.png" alt-text="Visualizzazione di una nuova finestra di post-apertura. Il pulsante ' nuova raccolta ' è evidenziato":::

1. Nella finestra *Crea una nuova raccolta* che segue specificare un **nome** e una **Descrizione** facoltativa per la raccolta.

Continuare quindi con la sezione successiva per aggiungere un bearer token alla raccolta per l'autorizzazione.

### <a name="add-authorization-token-and-finish-collection"></a>Aggiungere un token di autorizzazione e completare la raccolta

1. Nella finestra di dialogo *Crea una nuova raccolta* passare alla scheda *autorizzazione* . Questo è il punto in cui verrà inserito il **valore del token** raccolto nella sezione [Get Bearer token](#get-bearer-token) per usarlo per tutte le richieste API nella raccolta.

    :::image type="content" source="media/how-to-use-postman/postman-authorization.png" alt-text="La finestra &quot;crea una nuova raccolta&quot;, che mostra la scheda &quot;autorizzazione&quot;.":::

1. Impostare il *tipo* su _**OAuth 2,0**_ e incollare il token di accesso nella casella *token di accesso* .

    :::image type="content" source="media/how-to-use-postman/postman-paste-token.png" alt-text="La finestra &quot;crea una nuova raccolta&quot;, che mostra la scheda &quot;autorizzazione&quot;. È stato selezionato un tipo di &quot;OAuth 2,0&quot; e la casella token di accesso in cui è possibile incollare il valore del token di accesso è evidenziata.":::

1. Dopo aver incollato nella bearer token, fare clic su *Crea* per completare la creazione della raccolta.

La nuova raccolta ora può essere visualizzata dalla vista principale, in *raccolte*.

:::image type="content" source="media/how-to-use-postman/postman-post-collection.png" alt-text="Visualizzazione della finestra principale del post. La raccolta appena creata viene evidenziata nella scheda ' Collections ' (raccolte).":::

## <a name="create-a-request"></a>Creare una richiesta

Dopo aver completato i passaggi precedenti, è possibile creare richieste alle API dei dispositivi gemelli digitali di Azure.

1. Per creare una richiesta, fare clic sul pulsante *+ nuovo* .

    :::image type="content" source="media/how-to-use-postman/postman-new-request.png" alt-text="Visualizzazione della finestra principale del post. Il pulsante ' nuovo ' è evidenziato":::

1. Scegliere *Request*.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Visualizzazione delle opzioni che è possibile selezionare per creare qualcosa di nuovo. L'opzione ' request ' è evidenziata":::

1. Questa azione apre la finestra *Salva richiesta* , in cui è possibile immettere un nome per la richiesta, assegnarle una descrizione facoltativa e scegliere la raccolta di cui fa parte. Inserire i dettagli e salvare la richiesta nella raccolta creata in precedenza.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Visualizzazione della finestra ' Salva richiesta ' in cui è possibile compilare i campi descritti. Il pulsante ' Salva in Azure Digital Twins Collection ' è evidenziato":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

È ora possibile visualizzare la richiesta nella raccolta e selezionarla per estrarre i dettagli modificabili.

:::image type="content" source="media/how-to-use-postman/postman-request-details.png" alt-text="Visualizzazione della finestra principale del post. La raccolta di dispositivi gemelli digitali di Azure è stata espansa e la richiesta ' query Twins ' è evidenziata. I dettagli della richiesta vengono visualizzati al centro della pagina." lightbox="media/how-to-use-postman/postman-request-details.png":::

### <a name="set-request-details"></a>Imposta dettagli richiesta

Per creare una richiesta post a una delle API dei dispositivi gemelli digitali di Azure, è necessario l'URL dell'API e informazioni sui dettagli necessari. Queste informazioni sono disponibili nella documentazione di [riferimento dell'API REST di Digital gemelli di Azure](/rest/api/azure-digitaltwins/).

Per procedere con una query di esempio, in questo articolo verrà usata l'API di query (e la relativa [documentazione di riferimento](/rest/api/digital-twins/dataplane/query/querytwins)) per eseguire una query per tutti i dispositivi gemelli digitali in un'istanza.

1. Ottenere l'URL della richiesta e il tipo dalla documentazione di riferimento. Per l'API di query, si tratta di *Post https://digitaltwins-name.digitaltwins.azure.net/query?api-version=2020-10-31*.
1. In postazione impostare il tipo per la richiesta e immettere l'URL della richiesta, inserendo i segnaposto nell'URL in base alle esigenze. Questa è la posizione in cui verrà usato il **nome host** dell'istanza dalla sezione [*prerequisiti*](#prerequisites) .
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Nei dettagli della nuova richiesta, l'URL della query dalla documentazione di riferimento è stato compilato nella casella URL richiesta." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Verificare che i parametri visualizzati per la richiesta nella scheda *params* corrispondano a quelli descritti nella documentazione di riferimento. Per questa richiesta in postazione, il `api-version` parametro è stato compilato automaticamente quando l'URL della richiesta è stato immesso nel passaggio precedente. Per l'API di query, questo è l'unico parametro obbligatorio, quindi questo passaggio viene eseguito.
1. Nella scheda *autorizzazione* impostare il *tipo* su *eredita autenticazione da padre*. Ciò indica che questa richiesta utilizzerà l'autenticazione configurata in precedenza per l'intera raccolta.
1. Verificare che le intestazioni visualizzate per la richiesta nella scheda *intestazioni* corrispondano a quelle descritte nella documentazione di riferimento. Per questa richiesta sono state compilate automaticamente diverse intestazioni. Per l'API di query, nessuna delle opzioni di intestazione è obbligatoria, quindi questo passaggio viene eseguito.
1. Verificare che il corpo visualizzato per la richiesta nella scheda *corpo* corrisponda alle esigenze descritte nella documentazione di riferimento. Per l'API di query, è necessario un corpo JSON per fornire il testo della query. Di seguito è riportato un esempio di corpo per questa richiesta che esegue query per tutti i dispositivi gemelli digitali nell'istanza:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="Nei dettagli della nuova richiesta viene visualizzata la scheda corpo. Contiene un corpo JSON non elaborato con una query di ' SELECT * FROM DIGITALTWINS '."lightbox="media/how-to-use-postman/postman-request-body.png":::

   Per altre informazioni sulla creazione di query dei dispositivi gemelli digitali di Azure, vedere [*procedura: eseguire una query sul grafico gemello*](how-to-query-graph.md).

1. Controllare la documentazione di riferimento per tutti gli altri campi che potrebbero essere necessari per il tipo di richiesta. Per l'API di query, tutti i requisiti sono ora soddisfatti nella richiesta di invio, quindi questo passaggio viene eseguito.
1. Usare il pulsante *Send (Invia* ) per inviare la richiesta completata.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Accanto ai dettagli della nuova richiesta, il pulsante Invia è evidenziato." lightbox="media/how-to-use-postman/postman-request-send.png":::

Dopo l'invio della richiesta, i dettagli della risposta verranno visualizzati nella finestra di posting sotto la richiesta. È possibile visualizzare il codice di stato della risposta e qualsiasi testo del corpo.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Sotto i dettagli della richiesta inviata, vengono evidenziati i dettagli della risposta. Lo stato è 200 OK e testo del corpo che descrive i gemelli digitali restituiti dalla query." lightbox="media/how-to-use-postman/postman-request-response.png":::

È anche possibile confrontare la risposta ai dati di risposta previsti specificati nella documentazione di riferimento per verificare il risultato o ottenere ulteriori informazioni sugli errori che si verificano.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle API dei dispositivi gemelli digitali, vedere [*procedura: usare le API e gli SDK di dispositivi digitali gemelli di Azure*](how-to-use-apis-sdks.md)o visualizzare la [documentazione di riferimento per le API REST](/rest/api/azure-digitaltwins/).