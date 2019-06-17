---
title: Gestire il traffico verso le app multi-tenant, ad esempio App web del servizio App con il Gateway applicazione di Azure - portale
description: Questo articolo fornisce indicazioni su come configurare App web del servizio App di Azure come membri del pool back-end in un gateway applicazione nuovo o esistente.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/11/2019
ms.author: absha
ms.openlocfilehash: 4dae04c14f9132c54dcc0575ccb2841a4742a626
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60831190"
---
# <a name="configure-app-service-with-application-gateway"></a>Configurare il servizio app con il gateway applicazione

Il gateway applicazione consente di disporre di un'App Web del servizio App di Azure o altri servizi multi-tenant come un membro del pool back-end. 

In questo articolo viene spiegato come:

> [!div class="checklist"]
>
> - Creare un pool back-end e aggiungervi un servizio App
> - Creare le impostazioni HTTP e un Probe personalizzato con opzioni "Selezionare il nome host" abilitate

## <a name="prerequisites"></a>Prerequisiti

- Gateway applicazione: Se non si dispone di un gateway applicazione esistente, vedere come [creare un gateway applicazione](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- Servizio App: Se non si dispone di un servizio App esistente, vedere [documentazione del servizio App](https://docs.microsoft.com/azure/app-service/).

## <a name="add-app-service-as-backend-pool"></a>Aggiungere il servizio App come pool back-end

1. Nel portale di Azure, aprire la visualizzazione di configurazione del gateway applicazione è.

2. Sotto **pool back-end**, fare clic su **Add** per creare un nuovo pool di back-end.

3. Specificare un nome adatto per il pool back-end. 

4. Sotto **destinazioni**, fare clic sull'elenco a discesa e scegliere **servizi App** come opzione.

5. Un elenco a discesa immediatamente di sotto di **destinazioni** elenco a discesa verrà visualizzati che conterrà un elenco dei servizi App. Da questo elenco a discesa, scegliere il servizio App si desidera aggiungere come membro del pool back-end e fare clic su Aggiungi.

   ![Back-end del servizio App](./media/configure-web-app-portal/backendpool.png)

## <a name="create-http-settings-for-app-service"></a>Creare le impostazioni HTTP per il servizio App

1. Sotto **delle impostazioni HTTP**, fare clic su **Add** per creare una nuova impostazione HTTP.

2. Immettere un nome per l'impostazione HTTP ed è possibile abilitare o disabilitare l'affinità dei Cookie basata in base alle proprie esigenze.

3. Scegliere il protocollo come HTTP o HTTPS in base al caso d'uso. 

4. Selezionare la casella **utilizzare per il servizio App** e attiverà il **creare un probe con il nome host di prelievo di indirizzi back-end** e **selezione nome host di indirizzi back-end** opzioni. Questa opzione consente inoltre creare automaticamente un probe con l'opzione abilitato e associarlo a questa impostazione HTTP.

5. Fare clic su **OK** per creare l'impostazione HTTP.

   ![HTTP-setting1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP-setting2](./media/configure-web-app-portal/http-setting2.png)

## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Creare una regola per collegare il Listener, Pool back-end e le impostazioni HTTP

1. Sotto **regole**, fare clic su **base** per creare una nuova regola di base.

2. Specificare un nome appropriato e selezionare il listener che accetta le richieste in ingresso per il servizio App.

3. Nel **pool back-end** elenco a discesa, scegliere il back-end pool creato in precedenza.

4. Nel **impostazione HTTP** elenco a discesa, scegliere il protocollo HTTP impostazione creato in precedenza.

5. Fare clic su **OK** per salvare questa regola.

   ![Regola](./media/configure-web-app-portal/rule.png)

## <a name="restrict-access"></a>Limitare l'accesso

L'app Web distribuita in questi esempi usa indirizzi IP pubblici a cui è possibile accedere direttamente da Internet. Questo facilita la risoluzione dei problemi quando si sta imparando una nuova funzionalità e si provano le novità. Ma se si prevede di distribuire una funzionalità in produzione, sarà necessario aggiungere altre restrizioni.

Un modo per limitare l'accesso alle app Web è usare le [Restrizioni IP statico del Servizio app di Azure](../app-service/app-service-ip-restrictions.md). Ad esempio, è possibile limitare l'app Web in modo che riceva solo il traffico del gateway applicazione. Usare la funzionalità di restrizione IP del servizio app per aggiungere l'indirizzo VIP di un gateway applicazione come unico indirizzo con accesso.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul servizio App e altre opzioni di supporto multi-tenant con il gateway applicazione, vedere [supporto del servizio multi-tenant con il gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

Nel caso in cui la risposta dal servizio App viene eseguito il reindirizzamento all'URL del servizio App, vedere come [risolvere i problemi il reindirizzamento al problema di URL del servizio App](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url).
