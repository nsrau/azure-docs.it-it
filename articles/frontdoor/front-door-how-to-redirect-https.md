---
title: Creare una porta d'ingresso tramite HTTP per il reindirizzamento HTTPS dal portale di Azure
description: Informazioni su come creare una porta d'ingresso con traffico reindirizzato da HTTP a HTTPS usando il portale di Azure.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 7fabc1e3445d3dbd357700ffde3caeb985cc60c4
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67601966"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Creare una porta d'ingresso tramite HTTP per il reindirizzamento HTTPS dal portale di Azure

È possibile usare il portale di Azure per creare un [porta d'ingresso](front-door-overview.md) con un certificato per la terminazione SSL. Una regola di routing consente di reindirizzare il traffico HTTP a HTTPS.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare una porta d'ingresso con una risorsa di App Web esistente
> * Aggiungere un dominio personalizzato con il certificato SSL 
> * Configurare il reindirizzamento HTTPS nel dominio personalizzato

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Creare una porta d'ingresso con una risorsa di App Web esistente

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
3. Cercare **sportello Front** usando la ricerca a barre e dopo aver trovato il tipo di risorsa, fare clic su **crea**.
4. Scegliere una sottoscrizione e quindi usare un gruppo di risorse esistente o crearne uno nuovo. Si noti che è la posizione frequenti nell'interfaccia utente per il gruppo di risorse solo. La configurazione della porta d'ingresso verrà distribuita in tutti [località POP della porta Front Azure](https://docs.microsoft.com/azure/frontdoor/front-door-faq#what-are-the-pop-locations-for-azure-front-door-service).

    ![Configurare nozioni di base per la nuova porta di ingresso](./media/front-door-url-redirect/front-door-create-basics.png)

5. Fare clic su **successivo** immettere nella scheda configurazione. La configurazione per la porta principale viene eseguita in tre passaggi - aggiunta di un host di front-end predefinito back-end in un pool back-end e quindi creare le regole di routing per mappare il comportamento di routing per l'host di front-end. 

     ![Progettazione di configurazione di ingresso principale](./media/front-door-url-redirect/front-door-designer.png)

6. Fare clic su di ' **+** ' icona sul _front-end ospita_ per creare un host di front-end, immettere un nome univoco globale per l'host di front-end predefinito per la porta Front (`\<**name**\>.azurefd.net`). Fare clic su **Add** per procedere al passaggio successivo.

     ![Aggiunge un host di front-end](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Fare clic su di ' **+** ' icona sul _pool back-end_ per creare un pool back-end. Specificare un nome per il pool back-end e quindi fare clic su '**aggiungere un back-end**'.
8. Selezionare il tipo di Host di back-end come _servizio App_. Selezionare la sottoscrizione in cui è ospitata l'app web e quindi selezionare l'app web specifica nell'elenco a discesa per **nome host di back-end**.
9. Fare clic su **Add** per salvare il back-end e fare clic su **Add** nuovamente per salvare la configurazione del pool di back-end.   ![Aggiungere un back-end in un pool back-end](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Fare clic su di ' **+** ' icona sul _regole di Routing_ per creare una route. Specificare un nome per la route, ad esempio 'HttpToHttpsRedirect' e quindi impostare il _protocolli accettati_ campo **'Solo HTTP'** . Assicurarsi che l'appropriato _front-end host_ sia selezionata.  
11. Nel _dettagli delle rotte_ sezione, impostare il _tipo di Route_ a **reindirizzare**, assicurarsi che il _reindirizzare tipo_ è impostato su  **Trovato (302)** e _reindirizzare protocollo_ è impostata su **solo HTTPS**. 
12. Fare clic su Aggiungi per salvare la regola di routing per il protocollo HTTP per il reindirizzamento HTTPS.
     ![Aggiungere HTTP a HTTPS reindirizzamento route](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Aggiungere un'altra regola di routing per la gestione del traffico HTTPS. Fare clic su di ' **+** ' accedano il _regole di Routing_ e specificare un nome per la route, ad esempio 'DefaultForwardingRoute' e quindi impostare la _protocolli accettato_ campo per **'Solo HTTPS'** . Assicurarsi che l'appropriato _front-end host_ sia selezionata.
14. Nella sezione dei dettagli di Route, impostare il _tipo di Route_ per **Forward**, verificare che sia selezionato il pool back-end corretto e il _inoltro protocollo_ è impostata su  **Solo HTTPS**. 
15. Fare clic su Aggiungi per salvare la regola di routing per l'inoltro delle richieste.
     ![Aggiungere una route di Inoltra per il traffico HTTPS](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Fare clic su **revisione + Crea** e quindi **crea**, per creare il profilo di porta principale. Passare alla risorsa una volta creata.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Aggiungere un dominio personalizzato per l'ingresso principale e abilitare il protocollo HTTPS su di essa
I passaggi seguenti illustrano come aggiungere un dominio personalizzato in una risorsa di ingresso principale esistente e quindi abilitare HTTP per il reindirizzamento HTTPS su di esso. 

### <a name="add-a-custom-domain"></a>Aggiungere un dominio personalizzato

In questo esempio, si aggiunge un record CNAME per il `www` sottodominio (ad esempio, `www.contosonews.com`).

#### <a name="create-the-cname-record"></a>Creazione di un record CNAME

Aggiungere un record CNAME per eseguire il mapping di un sottodominio host front-end predefinito della porta di ingresso (`<name>.azurefd.net`, dove `<name>` è il nome del profilo di porta d'ingresso).

Per il `www.contoso.com` dominio, ad esempio, aggiungere un record CNAME che mappa il nome `www` a `<name>.azurefd.net`.

Dopo aver aggiunto il record CNAME, la pagina dei record DNS è simile all'esempio seguente:

![Dominio personalizzato CNAME all'ingresso principale](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>Eseguire l'onboarding del dominio personalizzato nell'ingresso principale

1. Nella scheda della finestra di progettazione porta principale, fare clic su '+' icona negli host front-end sezione per aggiungere un nuovo dominio personalizzato. 
2. Immettere il nome DNS personalizzato completo nel campo nome host personalizzato, esempio `www.contosonews.com`. 
3. Dopo aver convalidato il mapping CNAME dal dominio per l'ingresso principale, fare clic su **Add** per aggiungere il dominio personalizzato.
4. Fare clic su **salvare** per inviare le modifiche.

![Menu del dominio personalizzato](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>Abilitare HTTPS nel dominio personalizzato

1. Fare clic sul dominio personalizzato è stato aggiunto e nella sezione **HTTPS dominio personalizzato**, impostare lo stato su **abilitato**.
2. È possibile lasciare il **tipo di gestione di certificati** impostata su _porta d'ingresso gestiti_ per il certificato gratuito mantenute, gestiti e autorotated da porta principale. È anche possibile usare il proprio certificato SSL personalizzato archiviato con Azure Key Vault. Questa esercitazione si presuppone che l'utilizzo della porta d'ingresso gestiti certificato.
![Abilitazione di HTTPS dominio personalizzato](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Fare clic su **Update** per salvare la selezione e quindi fare clic su **salvare**.
4. Fare clic su **Aggiorna** dopo un paio di minuti e quindi fare clic sul dominio personalizzato per visualizzare lo stato di avanzamento del provisioning del certificato. 

> [!WARNING]
> Abilitazione di HTTPS per un dominio personalizzato potrebbe richiedere alcuni minuti e dipende anche la convalida della proprietà del dominio, se il record CNAME non è mappato direttamente all'host di ingresso principale `<name>.azurefd.net`. Altre informazioni sulle [come abilitare HTTPS per un dominio personalizzato](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Configurare le regole di routing per il dominio personalizzato

1. Fare clic sulla regola di routing di reindirizzamento creata in precedenza.
2. Fare clic sull'elenco a discesa per gli host di front-end e selezionare il dominio personalizzato per applicare questa route per il dominio nonché.
3. Fare clic su **Aggiorna**.
4. Eseguire, ovvero la stessa operazione per l'altra regola di routing anche per le route di inoltro aggiungere il dominio personalizzato.
5. Fare clic su **salvare** per inviare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
- Altre informazioni sulle [Reindirizzamento URL in ingresso principale](front-door-url-redirect.md).
