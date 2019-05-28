---
title: Guida introduttiva di Azure - Creare un profilo di Frontdoor per la disponibilità elevata delle applicazioni usando il portale di Azure
description: Questa guida introduttiva descrive come creare una frontdoor per un'applicazione Web globale con disponibilità e prestazioni elevate.
services: front-door
documentationcenter: ''
author: sharad4u
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2018
ms.author: sharadag
ms.openlocfilehash: 6bcd5bcc2463ec1ab9dcc97644d5046c31bfc78b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461987"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Guida introduttiva: Creare una frontdoor per un'applicazione Web globale a disponibilità elevata

Questa guida introduttiva descrive come creare un profilo di Frontdoor che fornisce disponibilità e prestazioni elevate per un'applicazione Web globale. 

Lo scenario descritto in questa guida introduttiva include due istanze di un'applicazione Web in esecuzione in aree di Azure diverse. Viene creata una configurazione di Frontdoor basata su [back-end ponderati e con la stessa priorità](front-door-routing-methods.md), che consente di instradare il traffico utente al set più vicino di back-end del sito che eseguono l'applicazione. Frontdoor monitora continuamente l'applicazione Web e fornisce il failover automatico nel successivo back-end disponibile quando il sito primario non è disponibile.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure 
Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="prerequisites"></a>Prerequisiti
Per questa guida introduttiva è necessario aver distribuito due istanze di un'applicazione Web in esecuzione in aree di Azure diverse (*Stati Uniti orientali* ed *Europa occidentale*). Entrambe le istanze dell'applicazione Web vengono eseguite in modalità attivo/attivo, vale a dire che entrambe possono ricevere il traffico in qualsiasi momento a differenza di una configurazione attivo-standby, in cui una delle due funge da failover.

1. In alto a sinistra nello schermo selezionare **Crea una risorsa** > **Web** > **App Web** > **Crea**.
2. In **App Web** immettere o selezionare le informazioni seguenti e immettere le impostazioni predefinite nei casi in cui non è specificato alcun valore:

     | Impostazione         | Valore     |
     | ---              | ---  |
     | NOME           | Immettere un nome univoco per l'app Web  |
     | Gruppo di risorse          | Selezionare **Nuovo** e quindi digitare *myResourceGroupFD1* |
     | Piano di servizio app/Località         | Selezionare **Nuovo**.  Nel piano di servizio app immettere *myAppServicePlanEastUS* e quindi selezionare **OK**. 
     |      Località  |   Stati Uniti orientali        |
    |||

3. Selezionare **Create**.
4. Quando l'app Web viene distribuita correttamente, viene creato un sito Web predefinito.
5. Ripetere i passaggi da 1 a 3 per creare un secondo sito Web in un'area di Azure diversa con le impostazioni seguenti:

     | Impostazione         | Valore     |
     | ---              | ---  |
     | NOME           | Immettere un nome univoco per l'app Web  |
     | Gruppo di risorse          | Selezionare **Nuovo** e quindi digitare *myResourceGroupFD2* |
     | Piano di servizio app/Località         | Selezionare **Nuovo**.  Nel piano di servizio app immettere *myAppServicePlanWestEurope* e quindi selezionare **OK**. 
     |      Località  |   Europa occidentale      |
    |||


## <a name="create-a-front-door-for-your-application"></a>Creare una frontdoor per l'applicazione
### <a name="a-add-a-frontend-host-for-front-door"></a>R. Aggiungere un host front-end per la frontdoor
Creare una configurazione di Frontdoor che instrada il traffico utente in base alla latenza più bassa fra i due back-end.

1. In alto a sinistra nella schermata selezionare **Crea una risorsa** > **Rete** > **Frontdoor** > **Crea**.
2. In **Crea una frontdoor** si inizia con l'aggiunta di informazioni di base e si immette la sottoscrizione in cui configurare la frontdoor. Come per qualsiasi altra risorsa di Azure, occorre anche fornire un gruppo di risorse e un'area del gruppo di risorse se se ne sta creando una da zero. Infine, è necessario specificare un nome per la frontdoor.
3. Una volta compilate le informazioni di base, il primo passaggio consiste nel definire l'**host front-end** per la configurazione. Il risultato deve essere un nome di dominio valido, ad esempio `myappfrontend.azurefd.net`. Questo nome host deve essere globalmente univoco, ma la convalida viene effettuata da Frontdoor. 

### <a name="b-add-application-backend-and-backend-pools"></a>B. Aggiungere back-end e pool back-end dell'applicazione

Successivamente, è necessario configurare uno o più back-end dell'applicazione in un pool back-end, per indicare a Frontdoor dove si trova l'applicazione. 

1. Fare clic sull'icona '+' per aggiungere un pool back-end e quindi specificare un **nome** per il pool back-end, ad esempio `myBackendPool`.
2. Successivamente, fare clic su Add Backends (Aggiungi back-end) per aggiungere i siti Web creati in precedenza.
3. Selezionare Servizio app come **Tipo di host di destinazione**, selezionare la sottoscrizione in cui è stato creato il sito Web e quindi scegliere il primo sito Web da **Nome host di destinazione**, ovvero  *myAppServicePlanEastUS.azurewebsites.net*.
4. Lasciare i campi restanti così come sono e fare clic su **Aggiungi**.
5. Ripetere i passaggi da 2 a 4 per aggiungere l'altro sito Web, vale a dire, *myAppServicePlanWestEurope.azurewebsites.net*
6. Facoltativamente, è possibile aggiornare le impostazioni di Probe integrità e Bilanciamento del carico per il pool back-end, ma dovrebbero funzionare anche i valori predefiniti. Fare clic su **Aggiungi**.


### <a name="c-add-a-routing-rule"></a>C. Aggiungere una regola di routing
Infine, fare clic sull'icona '+' in regole di Routing per configurare una regola di routing. Questo è necessario per eseguire il mapping dell'host front-end con il pool back-end, che in sostanza consiste nel configurare che, quando `myappfrontend.azurefd.net` riceve una richiesta, deve inoltrarla al pool back-end`myBackendPool`. Fare clic su **Aggiungi** per aggiungere la regola di routing per la frontdoor. A questo punto si dovrebbe essere pronti per la creazione della frontdoor, quindi fare clic su **Rivedi e crea**.

>[!WARNING]
> È **necessario** assicurarsi che ognuno degli host front-end nella frontdoor disponga di una regola di routing con un percorso predefinito ('/\*') associato. Questo vuol dire che tra tutte le regole di routing deve esisterne almeno una per ognuno degli host front-end definita nel percorso predefinito ('/\*'). In caso contrario, è possibile che il traffico degli utenti finali non venga instradato in modo corretto.

## <a name="view-front-door-in-action"></a>Esaminare Frontdoor in azione
Dopo aver creato una frontdoor, occorreranno alcuni minuti perché la configurazione sia distribuita ovunque a livello globale. Al termine della distribuzione, accedere all'host front-end creato, ovvero aprire un Web browser e raggiungere l'URL `myappfrontend.azurefd.net`. La richiesta verrà automaticamente instradata al back-end più vicino tra quelli specificati nel pool back-end. 

### <a name="view-front-door-handle-application-failover"></a>Esaminare Frontdoor che gestisce il failover dell'applicazione
Se si vuole testare il failover globale istantaneo di Frontdoor, è possibile passare a uno dei siti Web creati e arrestarlo. Base all'impostazione del probe di integrità definita per il pool back-end, verrà eseguito immediatamente il failover del traffico verso l'altra distribuzione del sito Web. È anche possibile testare il comportamento disabilitando il back-end nella configurazione del pool back-end per la frontdoor. 

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non sono più necessari, eliminare i gruppi di risorse, le applicazioni Web e tutte le risorse correlate.

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva è stata creata una frontdoor che consente di indirizzare il traffico utente per le applicazioni Web che richiedono massime prestazioni e disponibilità elevata. Per altre informazioni sul routing del traffico, vedere [Metodi di routing di Frontdoor](front-door-routing-methods.md).