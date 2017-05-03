---
title: Accesso ai dati locali - App per la logica di Azure | Microsoft Docs
description: Procedura per consentire alle app per la logica di accedere ai dati locali tramite la connessione a un gateway dati locale.
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
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
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 3e9b95e6e9e84f8c2b615f43783d9fec5a2c09b6
ms.lasthandoff: 04/14/2017


---
# <a name="connect-to-on-premises-data-from-logic-apps"></a>Connettersi ai dati locali dalle app per la logica

Per accedere ai dati locali è possibile configurare una connessione a un gateway dati locale per i connettori di App per la logica di Azure supportati. La procedura seguente illustra come installare e configurare il gateway dati locale per affinché si integri con le app per la logica. Il gateway dati locale supporta le connessioni seguenti:

*   BizTalk Server
*   DB2  
*   File system
*   Informix
*   MQ
*   MySQL
*   Oracle Database 
*   Server applicazioni SAP 
*   Server messaggi SAP
*   SharePoint solo per HTTP, non per HTTPS
*   SQL Server
*   Teradata

Per altre informazioni su queste connessioni, vedere [Connettori per App per la logica di Azure](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="requirements"></a>Requisiti

* È necessario un indirizzo di posta elettronica aziendale o dell'istituto di istruzione in Azure per associare il gateway dati locale con l'account (account basato su Azure Active Directory).

* Se si usa un account Microsoft, ad esempio @outlook.com, è possibile usare l'account di Azure per [creare un indirizzo di posta elettronica aziendale o dell'istituito d'istruzione](../virtual-machines/windows/create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal).

* [Il gateway dati locale deve essere già installato in un computer locale](logic-apps-gateway-install.md).

* È possibile associare l'installazione solo a una risorsa per il gateway. Il gateway non può essere reclamato da un altro gateway dati locale di Azure. L'attestazione si verifica durante la ([creazione nel passaggio 2 in questo argomento](#2-create-an-azure-on-premises-data-gateway-resource)).

## <a name="install-and-configure-the-connection"></a>Installare e configurare la connessione

### <a name="1-install-the-on-premises-data-gateway"></a>1. Installare il gateway dati locale

Se l'utente non lo hai già fatto, seguire questa procedura per [installare il gateway dati locale](logic-apps-gateway-install.md). Prima di continuare con gli altri passaggi, assicurarsi che il gateway dati sia installato in un computer locale.

### <a name="2-create-an-azure-on-premises-data-gateway-resource"></a>2. Creare una risorsa gateway dati locale di Azure

Dopo l'installazione del gateway, è necessario associare la sottoscrizione di Azure al gateway.

> [!IMPORTANT] 
> Assicurarsi che la risorsa gateway venga creata nella stessa area di Azure in cui si trova l'app per la logica. Se la risorsa per il gateway non viene distribuita nella stessa area, l'app per la logica non può accedere al gateway. 
> 

1. Accedere ad Azure con lo stesso indirizzo di posta elettronica aziendale o dell'istituto di istruzione usato durante l'installazione del gateway.
2. Scegliere **Nuovo**.
3. Trovare e selezionare **Gateway dati locale**.
4. Completare le informazioni per associare il gateway al proprio account, selezionando anche il **Nome installazione** appropriato.
   
    ![Connessione al gateway dati locale][1]

5. Scegliere **Crea** per creare la risorsa.

### <a name="3-create-a-logic-app-connection-in-logic-app-designer"></a>3. Creare una connessione dell'app per la logica nella Finestra di progettazione di app per la logica

Ora che la sottoscrizione di Azure è associata a un'istanza del gateway dati locale, è possibile creare una connessione al gateway dall'app per la logica.

1. Aprire un'app per la logica e scegliere un connettore che supporta la connettività locale, ad esempio SQL Server.
2. Selezionare **Connect via on-premises data gateway** (Connetti tramite gateway dati locale).
   
    ![Creazione del gateway nella finestra di progettazione della app per la logica][2]

3. Selezionare il **gateway** a cui si desidera connettersi e completare eventuali altre informazioni di connessione necessarie.
4. Scegliere **Crea** per creare la connessione.

La connessione è ora configurata per l'app per la logica da usare.

## <a name="edit-your-data-gateway-connection-settings"></a>Modificare le impostazioni di connessione del gateway dati

Dopo avere aggiunto la connessione al gateway dati all'app per la logica, potrebbero richiedersi delle modifiche in modo da poter regolare le impostazioni specifiche per questa connessione. La connessione è disponibile in una delle due posizioni:

* Nel pannello dell'app per la logica, sotto **Strumenti di sviluppo**, selezionare **Connessioni API**. Questo elenco mostra tutte le connessioni API associate alla propria app per la logica, inclusa la connessione del gateway dati. Per visualizzare e modificare le impostazioni della connessione, selezionare la connessione.

* Nel pannello principale di Connessioni API sono presenti tutte le connessioni API associate alla sottoscrizione di Azure, inclusa la connessione del gateway dati. Per visualizzare e modificare le impostazioni di connessione, selezionare la connessione.

## <a name="next-steps"></a>Passaggi successivi

* [Esempi e scenari comuni per le app per la logica](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Funzionalità di Enterprise Integration](../logic-apps/logic-apps-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-gateway-connection/createblade.png
[2]: ./media/logic-apps-gateway-connection/blankconnection.png
[3]: ./media/logic-apps-logic-gateway-connection/checkbox.png

