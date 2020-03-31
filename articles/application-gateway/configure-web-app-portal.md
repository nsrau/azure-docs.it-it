---
title: Gestire il traffico verso app multi-tenant tramite il portale
titleSuffix: Azure Application Gateway
description: Questo articolo fornisce indicazioni su come configurare le app Web del servizio app di Azure come membri del pool back-end in un gateway applicazione nuovo o esistente.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 0ec417b3c7a025d2d05bdd74ec683a2891c3b0de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075170"
---
# <a name="configure-app-service-with-application-gateway"></a>Configurare il servizio app con il gateway applicazione

Poiché il servizio app è un servizio multi-tenant anziché una distribuzione dedicata, usa l'intestazione host nella richiesta in ingresso per risolvere la richiesta nell'endpoint del servizio app corretto. In genere, il nome DNS dell'applicazione, che a sua volta è il nome DNS associato al gateway applicazione che antepo al servizio app, è diverso dal nome di dominio del servizio app back-end. Pertanto, l'intestazione host nella richiesta originale ricevuta dal gateway applicazione non corrisponde al nome host del servizio back-end. Per questo motivo, a meno che l'intestazione host nella richiesta dal gateway applicazione al back-end non venga modificata nel nome host del servizio back-end, i back-end multi-tenant non sono in grado di risolvere la richiesta nell'endpoint corretto.

Il gateway applicazione `Pick host name from backend address` fornisce un'opzione denominata che esegue l'override dell'intestazione host nella richiesta con il nome host del back-end quando la richiesta viene instradata dal gateway applicazione al back-end. Questa funzionalità abilita il supporto per i back-end multi-tenant, ad esempio il servizio app di Azure e la gestione delle API. 

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
>
> - Creare un pool back-end e aggiungervi un servizio appCreate a backend pool and add an App Service to it
> - Creare impostazioni HTTP e probe personalizzato con le opzioni "Seleziona nome host" abilitate

## <a name="prerequisites"></a>Prerequisiti

- Gateway applicazione: se non si dispone di un gateway applicazione esistente, vedere come [creare un gateway applicazione](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
- Servizio app: se non si dispone di un servizio app esistente, vedere [Documentazione del servizio app.](https://docs.microsoft.com/azure/app-service/)

## <a name="add-app-service-as-backend-pool"></a>Aggiungere il servizio app come pool back-endAdd App service as backend pool

1. Nel portale di Azure aprire la visualizzazione di configurazione del gateway applicazione.

2. In **Pool back-end**fare clic su **Aggiungi** per creare un nuovo pool back-end.

3. Specificare un nome appropriato per il pool back-end. 

4. In **Destinazioni**fare clic sull'elenco a discesa e scegliere **Servizi app** come opzione.

5. Apparirà un menu a discesa immediatamente sotto il menu a discesa **Destinazioni** che conterrà un elenco dei servizi app. Nell'elenco a discesa scegliere il servizio app che si desidera aggiungere come membro del pool back-end e fare clic su Aggiungi.From this dropdown, choose the App Service you want to add as a backend pool member and click Add.

   ![Back-end del servizio app](./media/configure-web-app-portal/backendpool.png)
   
   > [!NOTE]
   > L'elenco a discesa popolerà solo i servizi dell'app che si trovano nella stessa sottoscrizione del gateway applicazione. Se si vuole usare un servizio app in una sottoscrizione diversa da quella in cui si trova il gateway applicazione, anziché scegliere **Servizi app** nell'elenco a discesa **Destinazioni,** scegliere l'opzione **Indirizzo IP o nome host** e immettere il nome host (ad esempio). azurewebsites.net) del servizio app.

## <a name="create-http-settings-for-app-service"></a>Creare impostazioni HTTP per il servizio appCreate HTTP settings for App service

1. In **Impostazioni HTTP**fare clic su **Aggiungi** per creare una nuova impostazione HTTP.

2. Immettere un nome per l'impostazione HTTP ed è possibile abilitare o disabilitare l'affinità basata su cookie in base alle proprie esigenze.

3. Scegliere il protocollo come HTTP o HTTPS in base al caso d'uso. 

   > [!NOTE]
   > Se si seleziona HTTPS, non è necessario caricare alcun certificato di autenticazione o certificato radice attendibile per autorizzare il back-end del servizio app poiché il servizio app è un servizio di Azure attendibile.

4. Selezionare la casella **Usa per il servizio app** . Si noti `Create a probe with pick host name from backend address` `Pick host name from backend address` che gli interruttori e verranno abilitati automaticamente.`Pick host name from backend address` sovrascriverà l'intestazione host nella richiesta con il nome host del back-end quando la richiesta viene instradata dal gateway applicazione al back-end.  

   `Create a probe with pick host name from backend address`creerà automaticamente un probe di integrità e lo associerà a questa impostazione HTTP. Non è necessario creare altri probe di integrità per questa impostazione HTTP. È possibile verificare che un <HTTP Setting name> <Unique GUID> nuovo probe con il nome sia stato aggiunto `Pick host name from backend http settings enabled`nell'elenco dei probe di stato e che sia già presente l'opzione .

   Se si dispone già di una o più impostazioni HTTP che vengono utilizzate per il servizio App e se tali impostazioni `Create a probe with pick host name from backend address` HTTP utilizzano lo stesso protocollo di quello che si sta creando in quello che si sta creando, anziché il pulsante di selezione, si otterrà un elenco a discesa per selezionare uno dei probe personalizzati . Questo perché, poiché esiste già un'impostazione HTTP con il servizio app, `Pick host name from backend http settings enabled` pertanto esisterebbe anche un probe di integrità con l'opzione . Scegliere il probe personalizzato dall'elenco a discesa.

5. Fare clic **su OK** per creare l'impostazione HTTP.

   ![Impostazione HTTP1](./media/configure-web-app-portal/http-setting1.png)

   ![Impostazione HTTP2](./media/configure-web-app-portal/http-setting2.png)



## <a name="create-rule-to-tie-the-listener-backend-pool-and-http-setting"></a>Crea regola per legare il listener, il pool di back-end e l'impostazione HTTPCreate Rule to tie the Listener, Backend Pool and HTTP Setting

1. In **Regole**fare clic su **Di base** per creare una nuova regola di base.

2. Specificare un nome appropriato e selezionare il listener che accetterà le richieste in ingresso per il servizio app.

3. Nell'elenco a discesa **Pool back-end** scegliere il pool back-end creato in precedenza.

4. Nell'elenco a discesa **Impostazione HTTP** scegliere l'impostazione HTTP creata in precedenza.

5. Fare clic **su OK** per salvare la regola.

   ![Regola](./media/configure-web-app-portal/rule.png)

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>Configurazione aggiuntiva in caso di reindirizzamento al percorso relativo del servizio appAdditional configuration in case of redirection to app service's relative path

Quando il servizio app invia una risposta di reindirizzamento al client per reindirizzare al relativo percorso (ad esempio, un reindirizzamento da contoso.azurewebsites.net/path1 a contoso.azurewebsites.net/path2), usa lo stesso nome host nell'intestazione del percorso della risposta come quella nella richiesta ricevuta dal gateway applicazione. In questo modo il client effettuerà la richiesta direttamente per contoso.azurewebsites.net/path2 anziché passare attraverso il gateway applicazione (contoso.com/path2). Ignorare il gateway applicazione non è auspicabile.

Se nel caso d'uso, esistono scenari in cui il servizio app dovrà inviare una risposta di reindirizzamento al client, eseguire [i passaggi aggiuntivi per riscrivere l'intestazione del percorso.](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration)

## <a name="restrict-access"></a>Limitare l'accesso

L'app Web distribuita in questi esempi usa indirizzi IP pubblici a cui è possibile accedere direttamente da Internet. Questo facilita la risoluzione dei problemi quando si sta imparando una nuova funzionalità e si provano le novità. Ma se si prevede di distribuire una funzionalità in produzione, sarà necessario aggiungere altre restrizioni.

Un modo per limitare l'accesso alle app Web è usare le [Restrizioni IP statico del Servizio app di Azure](../app-service/app-service-ip-restrictions.md). Ad esempio, è possibile limitare l'app Web in modo che riceva solo il traffico del gateway applicazione. Usare la funzionalità di restrizione IP del servizio app per aggiungere l'indirizzo VIP di un gateway applicazione come unico indirizzo con accesso.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul servizio app e su altri supporti multi-tenant con gateway applicazione, vedere Supporto del [servizio multi-tenant con gateway applicazione.](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)
