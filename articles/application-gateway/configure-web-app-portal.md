---
title: Gestire il traffico per le app multi-tenant tramite il portale
titleSuffix: Azure Application Gateway
description: Questo articolo fornisce indicazioni su come configurare app Azure app Web del servizio come membri del pool back-end in un gateway applicazione nuovo o esistente.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: how-to
ms.date: 09/23/2020
ms.author: victorh
ms.openlocfilehash: df92e08e91761d77c606ccb5389eee7dc219c101
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323379"
---
# <a name="configure-app-service-with-application-gateway"></a>Configurare il servizio app con il gateway applicazione

Poiché il servizio app è un servizio multi-tenant anziché una distribuzione dedicata, usa l'intestazione host nella richiesta in ingresso per risolvere la richiesta all'endpoint corretto del servizio app. In genere, il nome DNS dell'applicazione, che a sua volta è il nome DNS associato al gateway applicazione che precede il servizio app, è diverso dal nome di dominio del servizio app back-end. Pertanto, l'intestazione host nella richiesta originale ricevuta dal gateway applicazione non corrisponde al nome host del servizio back-end. Per questo motivo, a meno che l'intestazione host nella richiesta dal gateway applicazione al back-end non venga modificata nel nome host del servizio back-end, i back-end multi-tenant non sono in grado di risolvere la richiesta nell'endpoint corretto.

Il gateway applicazione fornisce un'opzione denominata `Pick host name from backend target` che sostituisce l'intestazione host nella richiesta con il nome host del back-end quando la richiesta viene instradata dal gateway applicazione al back-end. Questa funzionalità consente il supporto per back-end multi-tenant, ad esempio servizio app di Azure e gestione API. 

In questo articolo vengono illustrate le operazioni seguenti:

- Modificare un pool back-end e aggiungervi un servizio app
- Modificare le impostazioni HTTP con l'opzione ' pick hostname ' abilitata

## <a name="prerequisites"></a>Prerequisiti

- Gateway applicazione: creare un gateway applicazione senza una destinazione del pool back-end. Per altre informazioni, vedere [Guida introduttiva: indirizzare il traffico Web con applicazione Azure gateway-portale di Azure](quick-create-portal.md)

- Servizio app: se non si ha un servizio app esistente, vedere la [documentazione del servizio app](https://docs.microsoft.com/azure/app-service/).

## <a name="add-app-service-as-backend-pool"></a>Aggiungere il servizio app come pool back-end

1. Nella portale di Azure selezionare il gateway applicazione.

2. In **pool back-end**selezionare il pool back-end.

4. In **tipo di destinazione**selezionare **Servizi app**.

5. In **destinazione** selezionare il servizio app.

   :::image type="content" source="./media/configure-web-app-portal/backend-pool.png" alt-text="Back-end del servizio app":::
   
   > [!NOTE]
   > L'elenco a discesa popola solo i servizi app che si trovano nella stessa sottoscrizione del gateway applicazione. Se si vuole usare un servizio app che si trova in una sottoscrizione diversa da quella in cui si trova il gateway applicazione, anziché scegliere **Servizi app** nell'elenco a discesa **destinazioni** , scegliere **indirizzo IP o opzione nome host** e immettere il nome host, ad esempio. azurewebsites.net) del servizio app.
1. Selezionare **Salva**.

## <a name="edit-http-settings-for-app-service"></a>Modificare le impostazioni HTTP per il servizio app

1. In **impostazioni http**selezionare l'impostazione HTTP esistente.

2. In **Sostituisci con nuovo nome host**selezionare **Sì**.
3. In **nome host Sostituisci**selezionare Seleziona **nome host dalla destinazione back-end**.
4. Selezionare **Salva**.

   :::image type="content" source="./media/configure-web-app-portal/http-settings.png" alt-text="Back-end del servizio app":::

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>Configurazione aggiuntiva in caso di reindirizzamento al percorso relativo del servizio app

Quando il servizio app invia una risposta di reindirizzamento al client per il reindirizzamento al percorso relativo (ad esempio, un Reindirizzamento da `contoso.azurewebsites.net/path1` a `contoso.azurewebsites.net/path2` ), USA lo stesso nome host nell'intestazione Location della risposta come quello nella richiesta ricevuta dal gateway applicazione. Quindi, il client effettuerà la richiesta direttamente a `contoso.azurewebsites.net/path2` invece di passare attraverso il gateway applicazione ( `contoso.com/path2` ). Non è consigliabile ignorare il gateway applicazione.

Se nel caso d'uso si verificano scenari in cui il servizio app deve inviare una risposta di reindirizzamento al client, eseguire i [passaggi aggiuntivi per riscrivere l'intestazione del percorso](https://docs.microsoft.com/azure/application-gateway/troubleshoot-app-service-redirection-app-service-url#sample-configuration).

## <a name="restrict-access"></a>Limitare l'accesso

L'app Web distribuita in questi esempi usa indirizzi IP pubblici a cui è possibile accedere direttamente da Internet. Questo facilita la risoluzione dei problemi quando si sta imparando una nuova funzionalità e si provano le novità. Ma se si prevede di distribuire una funzionalità in produzione, sarà necessario aggiungere altre restrizioni.

Un modo per limitare l'accesso alle app Web è usare le [Restrizioni IP statico del Servizio app di Azure](../app-service/app-service-ip-restrictions.md). Ad esempio, è possibile limitare l'app Web in modo che riceva solo il traffico del gateway applicazione. Usare la funzionalità di restrizione IP del servizio app per aggiungere l'indirizzo VIP di un gateway applicazione come unico indirizzo con accesso.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul servizio app e su altro supporto multi-tenant con il gateway applicazione, vedere [supporto del servizio multi-tenant con il gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).
