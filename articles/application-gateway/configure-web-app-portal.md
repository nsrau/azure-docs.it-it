---
title: Gestire il traffico per le app multi-tenant tramite il portale
titleSuffix: Azure Application Gateway
description: Questo articolo fornisce indicazioni su come configurare app Azure app Web del servizio come membri del pool back-end in un gateway applicazione nuovo o esistente.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 0ec417b3c7a025d2d05bdd74ec683a2891c3b0de
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075170"
---
# <a name="configure-app-service-with-application-gateway"></a>Configurare il servizio app con il gateway applicazione

Poiché il servizio app è un servizio multi-tenant anziché una distribuzione dedicata, usa l'intestazione host nella richiesta in ingresso per risolvere la richiesta all'endpoint corretto del servizio app. In genere, il nome DNS dell'applicazione, che a sua volta è il nome DNS associato al gateway applicazione che precede il servizio app, è diverso dal nome di dominio del servizio app back-end. Pertanto, l'intestazione host nella richiesta originale ricevuta dal gateway applicazione non corrisponde al nome host del servizio back-end. Per questo motivo, a meno che l'intestazione host nella richiesta dal gateway applicazione al back-end non venga modificata nel nome host del servizio back-end, i back-end multi-tenant non sono in grado di risolvere la richiesta nell'endpoint corretto.

Il gateway applicazione fornisce un'opzione denominata `Pick host name from backend address` che sostituisce l'intestazione host nella richiesta con il nome host del back-end quando la richiesta viene instradata dal gateway applicazione al back-end. Questa funzionalità consente il supporto per back-end multi-tenant, ad esempio servizio app di Azure e gestione API. 

In questo articolo viene spiegato come:

> [!div class="checklist"]
>
> - Creare un pool back-end e aggiungervi un servizio app
> - Creare le impostazioni HTTP e il probe personalizzato con le opzioni "pick hostname" abilitate

## <a name="prerequisites"></a>prerequisiti

- Gateway applicazione: se non è presente un gateway applicazione, vedere come [creare un gateway](https://docs.microsoft.com/azure/application-gateway/quick-create-portal) applicazione
- Servizio app: se non si ha un servizio app esistente, vedere la [documentazione del servizio app](https://docs.microsoft.com/azure/app-service/).

## <a name="add-app-service-as-backend-pool"></a>Aggiungere il servizio app come pool back-end

1. Nella portale di Azure aprire la visualizzazione configurazione del gateway applicazione.

2. In **pool back-end**fare clic su **Aggiungi** per creare un nuovo pool back-end.

3. Consente di specificare un nome appropriato per il pool back-end. 

4. In **destinazioni**fare clic sull'elenco a discesa e scegliere **Servizi app** come opzione.

5. Verrà visualizzato un elenco a discesa immediatamente sotto l'elenco a discesa **destinazioni** che conterrà un elenco dei servizi app. Da questo elenco a discesa scegliere il servizio app che si vuole aggiungere come membro del pool back-end e fare clic su Aggiungi.

   ![Back-end del servizio app](./media/configure-web-app-portal/backendpool.png)
   
   > [!NOTE]
   > L'elenco a discesa compilerà solo i servizi app che si trovano nella stessa sottoscrizione del gateway applicazione. Se si vuole usare un servizio app che si trova in una sottoscrizione diversa da quella in cui si trova il gateway applicazione, anziché scegliere **Servizi app** nell'elenco a discesa **destinazioni** , scegliere **indirizzo IP o opzione nome host** e immettere il nome host, ad esempio. azurewebsites.net) del servizio app.

## <a name="create-http-settings-for-app-service"></a>Creare le impostazioni HTTP per il servizio app

1. In **impostazioni http**fare clic su **Aggiungi** per creare una nuova impostazione http.

2. Immettere un nome per l'impostazione HTTP ed è possibile abilitare o disabilitare l'affinità basata su cookie in base al requisito.

3. Scegliere il protocollo come HTTP o HTTPS in base al caso d'uso. 

   > [!NOTE]
   > Se si seleziona HTTPS, non è necessario caricare un certificato di autenticazione o un certificato radice trusted nell'elenco elementi consentiti del back-end del servizio app perché il servizio app è un servizio di Azure attendibile.

4. Selezionare la casella da **usare per il servizio app** . Si noti che le opzioni `Create a probe with pick host name from backend address` e `Pick host name from backend address` verranno abilitate automaticamente.`Pick host name from backend address` eseguirà l'override dell'intestazione host nella richiesta con il nome host del back-end quando la richiesta viene instradata dal gateway applicazione al back-end.  

   `Create a probe with pick host name from backend address` creerà automaticamente un probe di integrità e lo associerà a questa impostazione HTTP. Non è necessario creare altri Probe di integrità per questa impostazione HTTP. È possibile verificare che sia stato aggiunto un nuovo probe con il nome <HTTP Setting name><Unique GUID> nell'elenco dei probe di integrità e che l'opzione sia già `Pick host name from backend http settings enabled`.

   Se si dispone già di una o più impostazioni HTTP che vengono usate per il servizio app e se le impostazioni HTTP usano lo stesso protocollo che si sta usando in quello che si sta creando, anziché l'opzione `Create a probe with pick host name from backend address`, si otterrà un elenco a discesa per selezionare uno dei probe personalizzati. Questo perché poiché esiste già un'impostazione HTTP con il servizio app, è necessario che esista anche un probe di integrità con l'opzione `Pick host name from backend http settings enabled`. Scegliere il probe personalizzato dall'elenco a discesa.

5. Fare clic su **OK** per creare l'impostazione http.

   ![HTTP-setting1](./media/configure-web-app-portal/http-setting1.png)

   ![HTTP-setting2](./media/configure-web-app-portal/http-setting2.png)



## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Creare una regola per collegare il listener, il pool back-end e l'impostazione HTTP

1. In **regole**fare clic su di **base** per creare una nuova regola di base.

2. Specificare un nome adatto e selezionare il listener che accetterà le richieste in ingresso per il servizio app.

3. Nell'elenco a discesa **pool back-end** scegliere il pool back-end creato in precedenza.

4. Nell'elenco a discesa **impostazione http** scegliere l'impostazione http creata in precedenza.

5. Fare clic su **OK** per salvare la regola.

   ![Regola](./media/configure-web-app-portal/rule.png)

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>Configurazione aggiuntiva in caso di reindirizzamento al percorso relativo del servizio app

Quando il servizio app invia una risposta di reindirizzamento al client per il reindirizzamento al percorso relativo (ad esempio, un Reindirizzamento da contoso.azurewebsites.net/path1 a contoso.azurewebsites.net/path2), USA lo stesso nome host nell'intestazione Location della risposta come quello nella richiesta ricevuta dal gateway applicazione. Quindi, il client effettuerà la richiesta direttamente a contoso.azurewebsites.net/path2 anziché passare attraverso il gateway applicazione (contoso.com/path2). Non è consigliabile ignorare il gateway applicazione.

Se nel caso d'uso si verificano scenari in cui il servizio app deve inviare una risposta di reindirizzamento al client, eseguire i [passaggi aggiuntivi per riscrivere l'intestazione del percorso](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration).

## <a name="restrict-access"></a>Limitare l'accesso

L'app Web distribuita in questi esempi usa indirizzi IP pubblici a cui è possibile accedere direttamente da Internet. Questo facilita la risoluzione dei problemi quando si sta imparando una nuova funzionalità e si provano le novità. Ma se si prevede di distribuire una funzionalità in produzione, sarà necessario aggiungere altre restrizioni.

Un modo per limitare l'accesso alle app Web è usare le [Restrizioni IP statico del Servizio app di Azure](../app-service/app-service-ip-restrictions.md). Ad esempio, è possibile limitare l'app Web in modo che riceva solo il traffico del gateway applicazione. Usare la funzionalità di restrizione IP del servizio app per aggiungere l'indirizzo VIP di un gateway applicazione come unico indirizzo con accesso.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul servizio app e su altro supporto multi-tenant con il gateway applicazione, vedere [supporto del servizio multi-tenant con il gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).
