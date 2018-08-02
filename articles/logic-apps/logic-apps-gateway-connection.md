---
title: Accesso alle risorse di dati locali per le App per la logica di Azure | Microsoft Docs
description: Creare e configurare il gateway dati locale in modo da poter accedere alle origini dati in locale dalle app per la logica
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/20/2018
ms.reviewer: yshoukry, LADocs
ms.suite: integration
ms.openlocfilehash: 65c7e03b349314ad61fa5f1ea8322f4d1352b8e6
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145690"
---
# <a name="connect-to-data-sources-on-premises-from-azure-logic-apps-with-on-premises-data-gateway"></a>Connettersi a origini dati in locale da app per la logica di Azure con il gateway dati locale

Per accedere a origini dati in locale dalle app per la logica, è possibile creare una risorsa per il gateway dati in Azure, in modo da consentire alle app per la logica di usare i [connettori locali](../logic-apps/logic-apps-gateway-install.md#supported-connections). Questo articolo illustra come creare la risorsa per il gateway di Azure *dopo* aver [scaricato e installato il gateway nel computer locale](../logic-apps/logic-apps-gateway-install.md). 

Per informazioni su come usare il gateway con altri servizi, vedere i seguenti articoli:

* [Gateway dati locale di Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Gateway dati locale di Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)
* [Gateway dati locale di Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Gateway dati locale di Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

## <a name="prerequisites"></a>Prerequisiti

* È necessario avere già [scaricato e installato il gateway dati locale in un computer locale](../logic-apps/logic-apps-gateway-install.md).

* L'installazione del gateway non è già associata a una risorsa per il gateway in Azure. È possibile collegare l'installazione del gateway a una sola risorsa per il gateway. Questa operazione viene eseguita quando si crea la risorsa del gateway e si seleziona l'installazione del gateway. Questo collegamento rende l'installazione del gateway non disponibile per le altre risorse.

* Quando si accede al portale di Azure e si crea la risorsa del gateway, è necessario usare lo stesso account di accesso che è stato usato in precedenza per [installare il gateway dati locale](../logic-apps/logic-apps-gateway-install.md#requirements).
È anche necessario usare la stessa [sottoscrizione di Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption-guide/adoption-intro/subscription-explainer) che è stata usata per installare il gateway. Se non si dispone ancora di una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">registrarsi per creare un account Azure gratuito</a>.

* Per creare e gestire la risorsa per il gateway nel portale di Azure, l'[account del servizio di Windows](../logic-apps/logic-apps-gateway-install.md#windows-service-account) deve avere almeno autorizzazioni di **Collaboratore**. Il gateway dati locale viene eseguito come servizio di Windows ed è configurato per usare `NT SERVICE\PBIEgwService` come credenziali di accesso al servizio di Windows. 

  > [!NOTE]
  > Questo account del servizio Windows è diverso dall'account usato per la connessione a origini dati locali e dall'account di Azure aziendale o dell'istituto di istruzione usato per accedere ai servizi cloud.

## <a name="download-and-install-gateway"></a>Scaricare e installare il gateway

Prima di procedere con la procedura descritta in questo articolo, il gateway deve essere già installato in un computer locale.
Se questa operazione non è già stata eseguita, seguire la [procedura per scaricare e installare il gateway dati locale](../logic-apps/logic-apps-gateway-install.md). 

<a name="create-gateway-resource"></a>

## <a name="create-azure-resource-for-gateway"></a>Creare la risorsa di Azure per il gateway

Dopo aver installato il gateway in un computer locale, è possibile creare una risorsa di Azure per il gateway. Questo passaggio associa anche la risorsa di gateway con la sottoscrizione di Azure.

1. Accedere al <a href="https://portal.azure.com" target="_blank">portale di Azure</a>. Assicurarsi di usare lo stesso indirizzo di posta elettronica aziendale o dell'istituto di istruzione usato per installare il gateway.

2. Nel menu principale di Azure selezionare **Crea una risorsa** > 
**Integrazione** > **Gateway dati locale**.

   ![Trovare "Gateway dati locale"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. Nella pagina **Crea gateway di connessione** specificare le seguenti informazioni per creare la risorsa per il gateway:

   | Proprietà | DESCRIZIONE | 
   |----------|-------------|
   | **Nome** | Nome per la risorsa del gateway. | 
   | **Sottoscrizione** | Nome della sottoscrizione di Azure, che deve essere la stessa sottoscrizione dell'app per la logica. La sottoscrizione predefinita è basata sull'account di Azure usato per l'accesso. | 
   | **Gruppo di risorse** | Nome del [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) per l'organizzazione delle risorse correlate. | 
   | **Posizione** | Azure limita questa posizione alla stessa area selezionata per il servizio cloud del gateway durante l'[installazione del gateway](../logic-apps/logic-apps-gateway-install.md). <p>**Nota**: assicurarsi che la posizione della risorsa per il gateway corrisponda a quella del servizio cloud del gateway. In caso contrario, l'installazione del gateway potrebbe non essere visualizzata nell'elenco dei gateway installati da selezionare nel passaggio successivo. È possibile usare aree diverse per la risorsa del gateway e per le app per la logica. | 
   | **Nome installazione** | Se l'installazione del gateway non è già selezionata, selezionare il gateway installato in precedenza. | 
   | | | 

   Di seguito è fornito un esempio: 

   ![Fornire i dettagli per creare il gateway dati locale](./media/logic-apps-gateway-connection/createblade.png)

4. Per aggiungere la risorsa del gateway al dashboard di Azure, selezionare **Aggiungi al dashboard**. Al termine dell'operazione, scegliere **Crea**.

   Per trovare o visualizzare il gateway in qualsiasi momento, selezionare **Tutti i servizi** nel menu principale di Azure. 
   Nella casella di ricerca immettere "gateway dati locali" e quindi selezionare **Gateway dati locali**.

   ![Trovare "Gateway dati locali"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Connettersi ai dati locali

Dopo aver creato la risorsa per il gateway e associato la sottoscrizione di Azure a questa risorsa, è possibile creare una connessione tra l'app per la logica e l'origine dati in locale usando il gateway.

1. Nel portale di Azure, creare o aprire l'app per la logica nella finestra di progettazione di app per la logica.

2. Aggiungere un connettore che supporta la connettività locale, ad esempio **SQL Server**.

3. A questo punto, configurare la connessione:

   1. Selezionare **Connect via on-premises data gateway** (Connetti tramite gateway dati locale). 

   2. Per **Gateway**, selezionare la risorsa per il gateway creata in precedenza. 

      Anche se la posizione di connessione del gateway deve essere nella stessa area dell'app per la logica, è possibile selezionare un gateway in un'area diversa.

   3. Specificare un nome univoco per la connessione e le altre informazioni richieste. 

      Il nome di connessione univoco consente di identificare facilmente la connessione in un secondo momento, soprattutto quando si creano più connessioni. Se applicabile, è necessario includere anche il dominio completo per il proprio nome utente.
   
      Di seguito è fornito un esempio: 

      ![Creare una connessione tra l'app per la logica e il gateway dati](./media/logic-apps-gateway-connection/blankconnection.png)

   4. Al termine dell'operazione, scegliere **Crea**. 

A questo punto, la connessione del gateway è pronta per l'uso nell'app per la logica.

## <a name="edit-connection"></a>Modificare la connessione

Dopo aver creato una connessione al gateway per l'app per la logica, è possibile voler procedere all'aggiornamento delle impostazioni in un secondo momento per quella connessione specifica.

1. Individuare la connessione gateway:

   * Per trovare tutte le connessioni API solo per l'app per la logica, nel menu dell'app per la logica, in **Strumenti di sviluppo**, selezionare **Connessioni API**. 
   
     ![Passare all'app per la logica, selezionare "Connessioni API"](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * Per trovare tutte le connessioni API associate alla sottoscrizione di Azure: 

     * Dal menu principale di Azure passare a **Tutti i servizi** > **Web** > **Connessioni API**. 
     * In alternativa, dal menu principale di Azure passare a **Tutte le risorse**.

2. Selezionare la connessione gateway desiderata, quindi scegliere **Modifica connessione API**.

   > [!TIP]
   > Se gli aggiornamenti non hanno effetto, provare ad [arrestare e riavviare il servizio Windows del gateway](./logic-apps-gateway-install.md#restart-gateway).

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Eliminare la risorsa del gateway

Per creare una risorsa del gateway diversa, associare il gateway a una risorsa diversa o rimuovere la risorsa per il gateway, è possibile eliminare la risorsa del gateway senza modificare l'installazione del gateway. 

1. Dal menu principale di Azure passare a **Tutte le risorse**. 

2. Individuare e selezionare la risorsa del gateway.

3. Se non è già selezionato, nel menu della risorsa del gateway selezionare **Gateway dati locale**. 

4. Sulla barra degli strumenti della risorsa scegliere **Elimina**.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Domande frequenti

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il [forum di App per la logica di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Per votare o inviare idee relative alle funzionalità, visitare il [sito dei commenti e suggerimenti degli utenti di App per la logica](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere le app per la logica](./logic-apps-securing-a-logic-app.md)
* [Esempi e scenari comuni per le app per la logica](./logic-apps-examples-and-scenarios.md)
