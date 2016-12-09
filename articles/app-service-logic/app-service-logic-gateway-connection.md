---
title: Connessione al gateway dati locale delle app per la logica | Documentazione Microsoft
description: Informazioni su come creare una connessione al gateway dati locale da un&quot;app per la logica.
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: erikre
editor: 
ms.assetid: 6cb4449d-e6b8-4c35-9862-15110ae73e6a
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/05/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 814ad11e130c197f91e597091fca3afbe8f61cbd


---
# <a name="connect-to-the-on-premises-data-gateway-for-logic-apps"></a>Connessione al gateway dati locale per le app per la logica
I connettori delle app per la logica supportati consentono di configurare la connessione per accedere ai dati locali tramite il gateway dati locale.  La procedura seguente illustra come installare e configurare il gateway dati locale per integrarsi con una app per la logica.

## <a name="prerequisites"></a>Prerequisiti
* Uso di un indirizzo di posta elettronica aziendale o dell'istituto di istruzione in Azure per associare il gateway dati locale con l'account (account basato su Azure Active Directory)
  * Se si usa un account Microsoft, ad esempio @outlook.com, o @live.com), è possibile usare l'account Azure per creare un indirizzo di posta elettronica aziendale o dell'istituito d'istruzione [seguendo questa procedura](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal)

> [!WARNING]
> A causa di un'attuale limitazione, l'installazione del gateway locale verrà completata solo se viene usato un account registrato in Power BI.  Per completare correttamente l'installazione, registrare nel frattempo qualsiasi account in "Power BI (gratuito)".
> 
> 

* Gateway dati locale [installato in un computer locale](app-service-logic-gateway-install.md).
* Per il gateway non deve esistere un'attestazione da parte di un altro gateway dati locale di Azure. L'[attestazione viene eseguita con la creazione illustrata nel passaggio 2 di seguito](#2-create-an-azure-on-premises-data-gateway-resource). Un'installazione può essere associata a una sola risorsa gateway.

## <a name="installing-and-configuring-the-connection"></a>Installazione e configurazione della connessione
### <a name="1-install-the-on-premises-data-gateway"></a>1. Installare il gateway dati locale
Per informazioni sull'installazione del gateway dati locale, vedere [questo articolo](app-service-logic-gateway-install.md).  Prima di procedere con il resto della procedura, è necessario installare il gateway in un computer locale.

### <a name="2-create-an-azure-on-premises-data-gateway-resource"></a>2. Creare una risorsa gateway dati locale di Azure
Una volta installata, è necessario associare la sottoscrizione di Azure al gateway dati locale.

1. Accedere ad Azure con lo stesso o indirizzo di posta elettronica aziendale o dell'istituto di istruzione usato durante l'installazione del gateway
2. Fare clic sul pulsante **Nuovo**
3. Cercare e selezionare **Gateway dati locale**
4. Completare le informazioni per associare il gateway al proprio account, selezionando anche il **nome installazione**
   
    ![Connessione al gateway dati locale][1]
5. Fare clic sul pulsante **Crea** per creare la risorsa

### <a name="3-create-a-logic-app-connection-in-the-designer"></a>3. Creare una connessione della app per la logica nella finestra di progettazione
Ora che la sottoscrizione di Azure è associata a un'istanza del gateway dati locale, è possibile creare una connessione ad essa dall'interno di una app per la logica.

1. Aprire una app per la logica e scegliere un connettore che supporta la connettività locale, che al momento della stesura di questo articolo è SQL Server
2. Selezionare la casella di controllo **Connetti tramite gateway dati locale**
   
    ![Creazione del gateway nella finestra di progettazione della app per la logica][2]
3. Selezionare il **gateway** a cui connettersi e completare eventuali altre informazioni di connessione necessarie
4. Fare clic su **Crea** per creare la connessione

La connessione deve ora essere configurata correttamente per l'uso nella app per la logica.  

## <a name="next-steps"></a>Passaggi successivi
* [Esempi e scenari comuni per le app per la logica](app-service-logic-examples-and-scenarios.md)
* [Funzionalità di Enterprise Integration](app-service-logic-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/app-service-logic-gateway-connection/createblade.PNG
[2]: ./media/app-service-logic-gateway-connection/blankconnection.PNG
[3]: ./media/app-service-logic-gateway-connection/checkbox.PNG



<!--HONumber=Nov16_HO3-->


