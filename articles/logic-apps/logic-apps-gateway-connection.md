---
title: Accesso alle risorse di dati locali per le App per la logica di Azure | Microsoft Docs
description: Configurare il gateway dati locale in moda da poter accedere alle origini dati in locale dalle app per la logica
keywords: accesso ai dati, locale, trasferimento dei dati, crittografia, origini dei dati
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 6cb4449d-e6b8-4c35-9862-15110ae73e6a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/13/2017
ms.author: LADocs; dimazaid; estfan
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 4744405b2c294be564dedee308b4cba95cfcc3c6
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---
# <a name="access-data-sources-on-premises-from-logic-apps-with-the-on-premises-data-gateway"></a>Accedere alle origini dati in locale dalle app per la logica con il gateway dati locale

Per accedere alle origini dati in locale dalle app per la logica, configurare un gateway dati locale che le app per la logica possono usare con i connettori supportati. Il gateway funge da ponte che fornisce il trasferimento rapido dei dati e la crittografia tra le origini di dati in locale e le app per la logica. Il gateway inoltra i dati da origini locali sui canali crittografati tramite il bus di servizio di Azure. Tutto il traffico ha origine come traffico sicuro in uscita dall'agente di gateway. Altre informazioni sul [funzionamento del gateway dati](logic-apps-gateway-install.md#gateway-cloud-service). 

Il gateway supporta le connessioni alle origine dati locali seguenti:

*   BizTalk Server 2016
*   DB2  
*   File system
*   Informix
*   MQ
*   MySQL
*   Oracle Database
*   PostgreSQL
*   Server applicazioni SAP 
*   Server messaggi SAP
*   SharePoint
*   SQL Server
*   Teradata

Questa procedura mostra come configurare il gateway dati locale per lavorare con le app per la logica. Per altre informazioni sui connettori supportati, vedere [Connettori per le app per la logica di Azure](../connectors/apis-list.md). 

Per informazioni su come usare il gateway con altri servizi, vedere i seguenti articoli:

*   [Gateway dati locale di Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Gateway dati locale di Azure Analysis Services](../analysis-services/analysis-services-gateway.md)
*   [Gateway dati locale di Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Gateway dati locale di Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)

## <a name="requirements"></a>Requisiti

* È necessario già disporre del [gateway dati locale installato in un computer locale](logic-apps-gateway-install.md).

* Quando si accede al portale di Azure, è necessario usare lo stesso account aziendale o dell'istituto di istruzione usato per [installare il gateway dati locale](logic-apps-gateway-install.md#requirements). L'account di accesso deve avere anche una sottoscrizione di Azure da usare quando si crea una risorsa di gateway nel portale di Azure per l'installazione del gateway.

* L'installazione del gateway non può essere già richiesta da una risorsa del gateway di Azure. È possibile associare l'installazione del gateway a una sola risorsa del gateway di Azure. L'attestazione avviene quando si crea la risorsa per il gateway in modo che l'installazione non sia disponibile per le altre risorse.

## <a name="set-up-the-data-gateway-connection"></a>Configurare la connessione al gateway dati

### <a name="1-install-the-on-premises-data-gateway"></a>1. Installare il gateway dati locale

Se l'utente non lo hai già fatto, seguire la [procedura per installare il gateway dati locale](logic-apps-gateway-install.md). Prima di continuare con gli altri passaggi, assicurarsi di aver installato il gateway dati in un computer locale.

<a name="create-gateway-resource"></a>
### <a name="2-create-an-azure-resource-for-the-on-premises-data-gateway"></a>2. Creare una risorsa di Azure per il gateway dati locale

Dopo aver installato il gateway in un computer locale, è necessario creare il gateway dati come una risorsa in Azure. Questo passaggio associa anche la risorsa di gateway con la sottoscrizione di Azure.

1. Accedere al [Portale di Azure](https://portal.azure.com "Portale di Azure"). Assicurarsi di usare lo stesso indirizzo di posta elettronica aziendale o dell'istituto di istruzione usato per installare il gateway.

2. Nel menu a sinistra in Azure, scegliere **Nuovo** > **Integrazione aziendale** > **Gateway dati locale** come illustrato di seguito:

   ![Trovare "Gateway dati locale"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. Nel pannello **Crea gateway di connessione** fornire questi dettagli per creare la risorsa di gateway dati:

    * **Nome**: inserire un nome per la risorsa del gateway. 

    * **Sottoscrizione**: selezionare la sottoscrizione di Azure da associare alla risorsa del gateway. 
    La presente sottoscrizione deve essere la stessa sottoscrizione dell'app per la logica.
   
      La sottoscrizione predefinita si basa sull'account di Azure usato per accedere.

    * **Gruppo di risorse**: creare un gruppo di risorse o selezionarne uno esistente per distribuire la risorsa del gateway. 
    I gruppi di risorse consentono di gestire gli asset di Azure correlati come una raccolta.

    * **Percorso**: Azure limita questo percorso alla stessa area selezionata per il servizio cloud di gateway durante [l'installazione del gateway](logic-apps-gateway-install.md). 

      > [!NOTE]
      > Assicurarsi che il percorso della risorsa di gateway corrisponda al percorso del servizio cloud di gateway. In caso contrario, l'installazione del gateway potrebbe non essere visualizzata nell'elenco dei gateway installati da selezionare nel passaggio successivo.
      > 
      > È possibile usare aree diverse per la risorsa del gateway e per le app per la logica.

    * **Nome installazione**: se l'installazione del gateway non è già selezionata, selezionare il gateway installato in precedenza. 

    Per aggiungere la risorsa del gateway al dashboard di Azure, scegliere **Aggiungi al dashboard**. 
    Al termine dell'operazione, scegliere **Crea**.

    ad esempio:

    ![Fornire i dettagli per creare il gateway dati locale](./media/logic-apps-gateway-connection/createblade.png)

    Per trovare o visualizzare il gateway di dati in qualsiasi momento, dal menu principale di Azure a sinistra, passare ad **Altri servizi**>**Integrazione aziendale** > **Gateway dati locale**.

    ![Passare ad "Altri servizi", "Integrazione aziendale", "Gateway dati locale"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>
### <a name="3-connect-your-logic-app-to-the-on-premises-data-gateway"></a>3. Connettere l'app per la logica al gateway dati locale

Ora che è stata creata la risorsa per il gateway dati ed è stata associata la sottoscrizione di Azure alla risorsa, creare una connessione tra l'app per la logica e il gateway dati.

> [!NOTE]
> Il percorso di connessione del gateway deve essere presente nella stessa area dell'app per la logica, ma è possibile usare un gateway dati esistente in un'area diversa.

1. Nel portale di Azure, creare o aprire l'app per la logica nella finestra di progettazione dell'App per la logica.

2. Aggiungere un connettore che supporta la connettività locale, ad esempio SQL Server.

3. Seguendo l'ordine indicato, selezionare **Connect via on-premises data gateway** (Esegui connessione tramite il gateway dati locale), fornire un nome di connessione univoco e le informazioni necessarie, quindi selezionare la risorsa del gateway dati da usare. Al termine dell'operazione, scegliere **Crea**.

   > [!TIP]
   > Un nome di connessione univoco consente di identificare facilmente la connessione in un secondo momento, soprattutto quando si creano più connessioni. Se applicabile, è necessario includere anche il dominio completo per il proprio nome utente. 

   ![Creare una connessione tra l'app per la logica e il gateway dati](./media/logic-apps-gateway-connection/blankconnection.png)

A questo punto, la connessione del gateway è pronta per l'app per la logica da usare.

## <a name="edit-your-gateway-connection-settings"></a>Modificare le impostazioni di connessione del gateway

Dopo aver creato una connessione al gateway per l'app per la logica, è possibile voler procedere all'aggiornamento delle impostazioni in un secondo momento per quella connessione specifica.

1. Per trovare la connessione del gateway:

   * Nel pannello dell'app per la logica, sotto **Strumenti di sviluppo**, selezionare **Connessioni API**. 
   
     Il pannello **Connessioni API** mostra tutte le connessioni API associate alla propria app per la logica, incluse le connessioni del gateway.

     ![Passare all'app per la logica, selezionare "Connessioni API"](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * In alternativa, dal menu principale di Azure a sinistra, passare ad **Altri servizi** > **Web e dispositivi mobili** > **Connessioni API** per tutte le connessioni API, comprese le connessioni del gateway, che sono associate alla sottoscrizione di Azure. 

   * In alternativa, dal menu principale di Azure a sinistra, passare a **Tutte le risorse** per tutte le connessioni API, comprese le connessioni del gateway, che sono associate alla sottoscrizione di Azure.

2. Selezionare la connessione del gateway che si desidera visualizzare o modificare e scegliere **Modifica connessione API**.

   > [!TIP]
   > Se gli aggiornamenti non hanno effetto, provare ad [arrestare e riavviare il servizio Windows del gateway](./logic-apps-gateway-install.md#restart-gateway).

<a name="change-delete-gateway-resource"></a>
## <a name="switch-or-delete-your-on-premises-data-gateway-resource"></a>Cambiare o eliminare una risorsa gateway dati locale

Per creare una risorsa del gateway diversa, associare il gateway a una risorsa diversa o rimuovere la risorsa per il gateway, è possibile eliminare la risorsa del gateway senza modificare l'installazione del gateway. 

1. Dal menu principale di Azure a sinistra, passare a **Tutte le risorse**. 
2. Individuare e selezionare la risorsa del gateway dati.
3. Scegliere **Gateway dati locale** e, sulla barra degli strumenti della risorse, scegliere **Elimina**.

<a name="faq"></a>
## <a name="frequently-asked-questions"></a>Domande frequenti

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere le app per la logica](./logic-apps-securing-a-logic-app.md)
* [Esempi e scenari comuni per le app per la logica](./logic-apps-examples-and-scenarios.md)

