---
title: Creare un passaggio frontale con il reindirizzamento da HTTP a HTTPS tramite il portale di AzureCreate a Front Door with HTTP to HTTPS redirection using the Azure portal
description: Informazioni su come creare una porta frontale con traffico reindirizzato da HTTP a HTTPS tramite il portale di Azure.Learn how to create a Front Door with redirected traffic from HTTP to HTTPS using the Azure portal.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: f1b8c033a3ec230d60c30f6168de8ce013a80ac6
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878001"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Creare un passaggio frontale con il reindirizzamento da HTTP a HTTPS tramite il portale di AzureCreate a Front Door with HTTP to HTTPS redirection using the Azure portal

È possibile usare il portale di Azure per creare una [porta frontale](front-door-overview.md) con un certificato per la terminazione TLS. Una regola di routing viene utilizzata per reindirizzare il traffico HTTP a HTTPS.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Creare uno sportello anteriore con una risorsa di applicazione Web esistente
> * Aggiungere un dominio personalizzato con certificato TLS/SSLAdd a custom domain with TLS/SSL certificate 
> * Reindirizzamento HTTPS della configurazione nel dominio personalizzato

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Creare uno sportello anteriore con una risorsa di applicazione Web esistente

1. Accedere al portale di [https://portal.azure.com](https://portal.azure.com)Azure all'indirizzo .
2. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
3. Cercare **Front Door** utilizzando la barra di ricerca e, una volta trovato il tipo di risorsa, fare clic su **Crea**.
4. Scegliere una sottoscrizione e quindi usare un gruppo di risorse esistente o crearne uno nuovo. Si noti che il percorso richiesto nell'interfaccia utente è solo per il gruppo di risorse. La configurazione della porta frontale verrà distribuita in tutte le posizioni POP di [Azure Front Door.](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door)

    ![Nozioni di base sulla configurazione dei nuovi porti anteriori](./media/front-door-url-redirect/front-door-create-basics.png)

5. Fare clic su **Avanti** per accedere alla scheda di configurazione. La configurazione per Front Door avviene in tre passaggi: aggiungere un host front-end predefinito, aggiungere back-end in un pool back-end e quindi creare regole di routing per mappare il comportamento di routing per l'host front-end. 

     ![Progettista della configurazione della porta anteriore](./media/front-door-url-redirect/front-door-designer.png)

6. Fare clic**+** sull'icona ' negli _host front-end_ per creare un host front-end, immettere un`\<**name**\>.azurefd.net`nome univoco globale per l'host front-end predefinito per la porta frontale ( ). Fare clic su **Aggiungi** per procedere al passaggio successivo.

     ![Aggiungere un host front-end](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Fare clic**+** sull'icona ' ' nei _pool back-end_ per creare un pool back-end. Specificare un nome per il pool back-end e quindi fare clic su '**Aggiungi un back-end**'.
8. Selezionare il tipo di host back-end come _servizio app_. Selezionare la sottoscrizione in cui è ospitata l'app Web e quindi selezionare l'app Web specifica nell'elenco a discesa Nome **host back-end**.
9. Fare clic su **Aggiungi** per salvare il back-end e fare di nuovo clic su **Aggiungi** per salvare la configurazione del pool back-end.   ![Aggiungere un back-end in un pool back-end](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Fare clic**+** sull'icona ' ' nelle _regole di routing_ per creare una route. Specificare un nome per la route, pronunciare 'HttpToHttpsRedirect', quindi impostare il campo _Protocolli accettati_ **su 'Solo HTTP'.** Assicurarsi che sia selezionato _l'host front-end_ appropriato.  
11. Nella sezione _Dettagli route_ impostare Tipo _di route_ su **Reindirizzamento**, verificare che _Tipo di reindirizzamento_ sia impostato su **Trovato (302)** e il _protocollo Redirect_ sia impostato su **SOLO HTTPS**. 
12. Fare clic su Aggiungi per salvare la regola di routing per il reindirizzamento da HTTP a HTTPS.
     ![Aggiungere una route di reindirizzamento HTTP a HTTPSAdd an HTTP to HTTPS redirect route](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Aggiungere un'altra regola di routing per la gestione del traffico HTTPS. Fare clic**+** sul segno ' sulle regole di _routing_ e specificare un nome per la route, pronunciare 'DefaultForwardingRoute', quindi impostare il campo _Protocolli accettati_ **su 'Solo HTTPS'.** Assicurarsi che sia selezionato _l'host front-end_ appropriato.
14. Nella sezione Dettagli route impostare Tipo di _route_ su **Inoltra**, verificare che sia selezionato il pool back-end destro e che il _protocollo di inoltro_ sia impostato **su Solo HTTPS**. 
15. Fare clic su Aggiungi per salvare la regola di routing per l'inoltro delle richieste.
     ![Aggiungere una route di inoltro per il traffico HTTPSAdd a forward route for HTTPS traffic](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Fare clic su **Revisione e quindi su Crea,** quindi su **Crea**per creare il profilo della porta frontale. Passare alla risorsa una volta creata.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Aggiungere un dominio personalizzato alla porta frontale e abilitare HTTPS su di esso
I passaggi seguenti illustrano come aggiungere un dominio personalizzato in una risorsa Front Door esistente e quindi abilitare il reindirizzamento da HTTP a HTTPS. 

### <a name="add-a-custom-domain"></a>Aggiungere un dominio personalizzato

In questo esempio si aggiunge un `www` record CNAME per `www.contosonews.com`il sottodominio, ad esempio ).

#### <a name="create-the-cname-record"></a>Creazione di un record CNAME

Aggiungere un record CNAME per eseguire il mapping di un`<name>.azurefd.net`sottodominio `<name>` all'host frontend predefinito della porta front-end (, dove è il nome del profilo Front Door).

Per `www.contoso.com` il dominio, ad esempio, aggiungere un record `www` `<name>.azurefd.net`CNAME che esegue il mapping del nome a .

Dopo aver aggiunto il record CNAME, la pagina dei record DNS è simile all'esempio seguente:

![Dominio personalizzato CNAME a porte frontale](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>A bordo del dominio personalizzato sulla porta anteriore

1. Nella scheda della finestra di progettazione della porta anteriore, fare clic sull'icona '' nella sezione Host front-end per aggiungere un nuovo dominio personalizzato. 
2. Immettere il nome DNS personalizzato completo nel `www.contosonews.com`campo del nome host personalizzato, ad esempio . 
3. Una volta convalidato il mapping CNAME dal dominio alla porta anteriore, fare clic su **Aggiungi** per aggiungere il dominio personalizzato.
4. Fare clic su **Salva** per inviare le modifiche.

![Menu del dominio personalizzato](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>Abilitare HTTPS nel dominio personalizzatoEnable HTTPS on your custom domain

1. Fare clic sul dominio personalizzato aggiunto e nella sezione **Dominio personalizzato HTTPS**modificare lo stato in **Abilitato**.
2. È possibile lasciare il tipo di **gestione certificati** impostato su _Porta anteriore gestito_ per il certificato gratuito mantenuto, gestito e ruotato automaticamente da Front Door. È anche possibile scegliere di usare il proprio certificato TLS/SSL personalizzato archiviato con l'insieme di credenziali delle chiavi di Azure.You can also choose to use your own custom TLS/SSL certificate stored with Azure Key Vault. In questa esercitazione si presuppone l'utilizzo del certificato gestito Front Door.
![Abilitazione di HTTPS per il dominio personalizzatoEnabling HTTPS for custom domain](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Fare clic su **Aggiorna** per salvare la selezione e quindi fare clic su **Salva**.
4. Fare clic su **Aggiorna** dopo un paio di minuti e quindi fare di nuovo clic sul dominio personalizzato per visualizzare l'avanzamento del provisioning dei certificati. 

> [!WARNING]
> L'abilitazione di HTTPS per un dominio personalizzato può richiedere alcuni minuti e dipende anche `<name>.azurefd.net`dalla convalida della proprietà del dominio se il CNAME non è direttamente mappato all'host Front Door . Ulteriori informazioni su [come abilitare HTTPS per un dominio personalizzato](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Configurare le regole di routing per il dominio personalizzato

1. Fare clic sulla regola di routing di reindirizzamento creata in precedenza.
2. Fare clic sull'elenco a discesa per gli host front-end e selezionare il dominio personalizzato per applicare questo percorso anche al dominio.
3. Fare clic su **Update**.
4. Eseguire la stessa operazione anche per l'altra regola di routing, ovvero per la route di inoltro per aggiungere il dominio personalizzato.
5. Fare clic su **Salva** per inviare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
- Ulteriori informazioni sul [reindirizzamento dell'URL su Front Door](front-door-url-redirect.md).
