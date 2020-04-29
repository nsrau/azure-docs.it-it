---
title: Creare una porta anteriore con Reindirizzamento da HTTP a HTTPS usando il portale di Azure
description: Informazioni su come creare una porta anteriore con traffico reindirizzato da HTTP a HTTPS usando il portale di Azure.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: f1b8c033a3ec230d60c30f6168de8ce013a80ac6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878001"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Creare una porta anteriore con Reindirizzamento da HTTP a HTTPS usando il portale di Azure

È possibile usare la portale di Azure per creare una [porta anteriore](front-door-overview.md) con un certificato per la terminazione TLS. Viene usata una regola di routing per reindirizzare il traffico HTTP a HTTPS.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Creare una porta anteriore con una risorsa dell'app Web esistente
> * Aggiungere un dominio personalizzato con certificato TLS/SSL 
> * Configurare il reindirizzamento HTTPS nel dominio personalizzato

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Creare una porta anteriore con una risorsa dell'app Web esistente

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
3. Cercare lo **sportello anteriore** usando la barra di ricerca e, una volta individuato il tipo di risorsa, fare clic su **Crea**.
4. Scegliere una sottoscrizione e quindi usare un gruppo di risorse esistente o crearne uno nuovo. Si noti che il percorso richiesto nell'interfaccia utente è solo per il gruppo di risorse. La configurazione della porta anteriore viene distribuita in tutte le [località pop di Azure front door](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door).

    ![Configurare le nozioni di base per la nuova porta anteriore](./media/front-door-url-redirect/front-door-create-basics.png)

5. Fare clic su **Avanti** per immettere la scheda configurazione. La configurazione per la porta anteriore si verifica in tre passaggi: aggiungere un host front-end predefinito, aggiungere back-end in un pool back-end e quindi creare regole di routing per eseguire il mapping del comportamento di routing per l'host front-end. 

     ![Finestra di progettazione della configurazione della porta anteriore](./media/front-door-url-redirect/front-door-designer.png)

6. Fare clic sull'**+** icona '' sugli _host_ front-end per creare un host front-end, immettere un nome univoco globale per l'host front-end predefinito`\<**name**\>.azurefd.net`per la porta anteriore (). Fare clic su **Aggiungi** per procedere al passaggio successivo.

     ![Aggiungere un host front-end](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Fare clic sull'**+** icona '' dei _pool back-end_ per creare un pool back-end. Specificare un nome per il pool back-end e quindi fare clic su'**Aggiungi un back-end**'.
8. Selezionare il tipo di host back-end come _servizio app_. Selezionare la sottoscrizione in cui è ospitata l'app Web e quindi selezionare l'app Web specifica nell'elenco a discesa per il **nome host back-end**.
9. Fare clic su **Aggiungi** per salvare il back-end e fare nuovamente clic su **Aggiungi** per salvare la configurazione del pool back-end.   ![Aggiungere un back-end in un pool back-end](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Fare clic sull'**+** icona '' sulle _regole di routing_ per creare una route. Specificare un nome per la route, ad indicare "HttpToHttpsRedirect", quindi impostare il campo _protocolli accettati_ su **"solo http"**. Verificare che sia selezionato l'host front- _end_ appropriato.  
11. Nella sezione _Dettagli Route_ impostare il tipo di _Route_ su **Reindirizzamento**, verificare che il _tipo di reindirizzamento_ sia impostato su **trovato (302)** e che il _protocollo di reindirizzamento_ sia impostato su **solo HTTPS**. 
12. Fare clic su Aggiungi per salvare la regola di routing per il Reindirizzamento da HTTP a HTTPS.
     ![Aggiungere una route di Reindirizzamento da HTTP a HTTPS](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Aggiungere un'altra regola di routing per gestire il traffico HTTPS. Fare clic sul**+** segno "" delle _regole di routing_ e specificare un nome per la route, ad indicare "DefaultForwardingRoute", quindi impostare il campo _protocolli accettati_ su **"solo HTTPS"**. Verificare che sia selezionato l'host front- _end_ appropriato.
14. Nella sezione dettagli Route impostare il tipo di _Route_ su **Avanti**, assicurarsi che sia selezionato il pool back-end corretto e che il _protocollo di inoltro_ sia impostato **solo su HTTPS**. 
15. Fare clic su Aggiungi per salvare la regola di routing per l'inoltro della richiesta.
     ![Aggiungere una route di inoltro per il traffico HTTPS](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Fare clic su **Verifica + crea** e quindi su **Crea**per creare il profilo di porta anteriore. Passare alla risorsa dopo la creazione.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Aggiungere un dominio personalizzato alla porta anteriore e abilitare HTTPS su di esso
I passaggi seguenti illustrano come è possibile aggiungere un dominio personalizzato in una risorsa front door esistente e quindi abilitare il Reindirizzamento da HTTP a HTTPS. 

### <a name="add-a-custom-domain"></a>Aggiungere un dominio personalizzato

In questo esempio si aggiunge un record CNAME per il `www` sottodominio (ad esempio, `www.contosonews.com`).

#### <a name="create-the-cname-record"></a>Creazione di un record CNAME

Aggiungere un record CNAME per eseguire il mapping di un sottodominio all'host front-end predefinito`<name>.azurefd.net`della porta `<name>` anteriore (, dove è il nome del profilo della porta anteriore).

Per il `www.contoso.com` dominio, ad esempio, aggiungere un record CNAME che esegue il mapping del `www` nome `<name>.azurefd.net`a.

Dopo aver aggiunto il record CNAME, la pagina dei record DNS è simile all'esempio seguente:

![Da dominio personalizzato CNAME a sportello anteriore](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>Onboarding del dominio personalizzato nella porta anteriore

1. Nella scheda Progettazione porta anteriore fare clic sull'icona "+" nella sezione host front-end per aggiungere un nuovo dominio personalizzato. 
2. Immettere il nome DNS personalizzato completo nel campo nome host personalizzato, ad esempio `www.contosonews.com`. 
3. Dopo aver convalidato il mapping CNAME dal dominio alla porta anteriore, fare clic su **Aggiungi** per aggiungere il dominio personalizzato.
4. Fare clic su **Salva** per inviare le modifiche.

![Menu del dominio personalizzato](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>Abilitare HTTPS nel dominio personalizzato

1. Fare clic sul dominio personalizzato che è stato aggiunto e nella sezione **dominio personalizzato HTTPS**impostare lo stato su **abilitato**.
2. È possibile lasciare il **tipo di gestione certificati** impostato su _sportello anteriore gestito_ per il certificato gratuito mantenuto, gestito e ruotato in modo autonomo da sportello anteriore. È anche possibile scegliere di usare un certificato TLS/SSL personalizzato archiviato con Azure Key Vault. Questa esercitazione presuppone che l'uso del certificato gestito da sportello anteriore.
![Abilitazione di HTTPS per il dominio personalizzato](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Fare clic su **Aggiorna** per salvare la selezione e quindi fare clic su **Salva**.
4. Fare clic su **Aggiorna** dopo un paio di minuti e quindi fare di nuovo clic sul dominio personalizzato per visualizzare lo stato di avanzamento del provisioning del certificato. 

> [!WARNING]
> L'abilitazione di HTTPS per un dominio personalizzato potrebbe richiedere diversi minuti e dipende anche dalla convalida della proprietà del dominio se il CNAME non è mappato direttamente `<name>.azurefd.net`all'host della porta anteriore. Altre informazioni su [come abilitare HTTPS per un dominio personalizzato](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Configurare le regole di routing per il dominio personalizzato

1. Fare clic sulla regola di routing di reindirizzamento creata in precedenza.
2. Fare clic sull'elenco a discesa per gli host front-end e selezionare il dominio personalizzato per applicare anche questa route per il dominio.
3. Fare clic su **Update**.
4. Eseguire la stessa operazione anche per l'altra regola di routing, in modo che la route di inoltro aggiunga il dominio personalizzato.
5. Fare clic su **Salva** per inviare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
- Altre informazioni sul [Reindirizzamento degli URL sull'anta anteriore](front-door-url-redirect.md).
