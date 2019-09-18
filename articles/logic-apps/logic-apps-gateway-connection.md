---
title: Accedere alle origini dati locali da app per la logica di Azure
description: Connettersi a origini dati locali da app per la logica creando un gateway dati locale
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 09/01/2019
ms.openlocfilehash: dcb0fe4da968408a261e387c636cc548fa757a09
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036663"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Connettersi a origini dati locali da App per la logica di Azure

Per accedere a origini dati in locale dalle app per la logica, creare una risorsa gateway dati locale nel portale di Azure. L'app per la logica può quindi usare i [connettori locali](../connectors/apis-list.md#on-premises-connectors). Questo articolo illustra come creare una risorsa del gateway di Azure *dopo* il [download e l'installazione del gateway in un computer locale](../logic-apps/logic-apps-gateway-install.md). Per altre informazioni sul gateway, vedere funzionamento [del gateway](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> Per connettere le reti virtuali di Azure, considerare invece la possibilità di creare un [*ambiente del servizio di integrazione*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). 

Per informazioni su come usare il gateway con altri servizi, vedere i seguenti articoli:

* [Gateway dati locale di Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Gateway dati locale di Microsoft Flow](https://flow.microsoft.com/documentation/gateway-manage/)
* [Gateway dati locale di Microsoft PowerApps](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Gateway dati locale di Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Origini dati supportate

Per le app per la logica di Azure, il gateway dati locale supporta i [connettori locali](../connectors/apis-list.md#on-premises-connectors) per queste origini dati:

* BizTalk Server 2016
* File system
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* Server SharePoint
* SQL Server
* Teradata

Sebbene il gateway stesso non comporti costi aggiuntivi, il [modello di prezzi di app](../logic-apps/logic-apps-pricing.md) per la logica si applica a questi connettori e ad altre operazioni in app per la logica di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Il gateway dati locale è già stato [installato in un computer locale](../logic-apps/logic-apps-gateway-install.md).

* Si hanno lo [stesso account Azure e la sottoscrizione di Azure](../logic-apps/logic-apps-gateway-install.md#requirements) usati durante l'installazione del gateway dati locale.

* In precedenza l'installazione del gateway non è stata collegata a un'altra risorsa del gateway in Azure.

  Quando si crea una risorsa del gateway, si seleziona un'installazione del gateway da associare alla risorsa del gateway. L'installazione di un gateway già collegato non è disponibile per la selezione durante la creazione delle risorse del gateway.

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Creare una risorsa del gateway di Azure

Dopo aver installato il gateway in un computer locale, creare la risorsa di Azure per il gateway. 

1. Accedere al [portale di Azure](https://portal.azure.com) con lo stesso account Azure usato per installare il gateway.

1. Nella casella di ricerca portale di Azure immettere "gateway dati locale" e selezionare **gateway dati locali**.

   ![Trovare "Gateway dati locale"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

1. In **gateway dati locali**selezionare **Aggiungi**.

   ![Aggiungi gateway dati](./media/logic-apps-gateway-connection/add-gateway.png)

1. In **Crea gateway di connessione**fornire queste informazioni per la risorsa del gateway. Al termine, selezionare **Crea**.

   | Proprietà | Descrizione |
   |----------|-------------|
   | **Nome risorsa** | Nome della risorsa del gateway, che può contenere solo lettere, numeri, trattini`-`(), caratteri di sottolineatura (`_`),`(`parentesi `)`(,) e punti`.`(). |
   | **Sottoscrizione** | La sottoscrizione di Azure, che deve corrispondere all'installazione del gateway e all'app per la logica. La sottoscrizione predefinita si basa sull'account di Azure usato per accedere. |
   | **Gruppo di risorse** | Il [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) che si vuole usare |
   | **Location** | La stessa area della località selezionata per il servizio cloud gateway durante l'installazione del [gateway](../logic-apps/logic-apps-gateway-install.md). In caso contrario, l'installazione del gateway non verrà visualizzata nell'elenco **nome installazione** per la selezione. Il percorso dell'app per la logica può essere diverso da quello delle risorse del gateway. |
   | **Nome installazione** | Se l'installazione del gateway non è già selezionata, selezionare il gateway installato in precedenza. Le installazioni del gateway collegato in precedenza non verranno visualizzate in questo elenco per la selezione. |
   |||

   Di seguito è fornito un esempio:

   ![Fornire i dettagli per creare il gateway dati locale](./media/logic-apps-gateway-connection/gateway-details.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Connettersi ai dati locali

Dopo aver creato la risorsa per il gateway e associato la sottoscrizione di Azure a questa risorsa, è possibile creare una connessione tra l'app per la logica e l'origine dati in locale usando il gateway.

1. Nel portale di Azure, creare o aprire l'app per la logica nella finestra di progettazione di app per la logica.

1. Aggiungere un connettore che supporta la connettività locale, ad esempio **SQL Server**.

1. Selezionare **Connect via on-premises data gateway** (Connetti tramite gateway dati locale). 

1. Per i **gateway**selezionare la risorsa del gateway creata.

   > [!NOTE]
   > L'elenco gateway include risorse del gateway in altre aree, perché la posizione dell'app per la logica può essere diversa da quella della risorsa del gateway.

1. Specificare un nome di connessione univoco e altre informazioni necessarie, che dipendono dalla connessione che si desidera creare.

   Un nome di connessione univoco consente di individuare facilmente la connessione in un secondo momento, soprattutto se si creano più connessioni. Se applicabile, è necessario includere anche il dominio completo per il proprio nome utente.
   
   Di seguito è fornito un esempio:

   ![Creare una connessione tra l'app per la logica e il gateway dati](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Al termine, selezionare **Crea**. 

A questo punto, la connessione del gateway è pronta per l'uso nell'app per la logica.

## <a name="edit-connection"></a>Modificare la connessione

Per aggiornare le impostazioni per una connessione gateway, è possibile modificare la connessione.

1. Per trovare tutte le connessioni API solo per l'app per la logica, nel menu dell'app per la logica, in **strumenti di sviluppo**, selezionare **connessioni API**.
   
   ![Nel menu dell'app per la logica selezionare "connessioni API"](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

1. Selezionare la connessione del gateway desiderata, quindi selezionare **Modifica connessione API**.

   > [!TIP]
   > Se gli aggiornamenti non hanno effetto, provare ad [arrestare e riavviare l'account del servizio Windows del gateway](../logic-apps/logic-apps-gateway-install.md#restart-gateway) per l'installazione del gateway.

Per trovare tutte le connessioni API associate alla sottoscrizione di Azure: 

* Dal menu principale di Azure passare a **Tutti i servizi** > **Web** > **Connessioni API**.
* In alternativa, dal menu principale di Azure passare a **Tutte le risorse**. Impostare il filtro dei **tipi** su **connessione API**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Eliminare la risorsa del gateway

Per creare una risorsa del gateway diversa, collegare l'installazione del gateway a una risorsa del gateway diversa oppure rimuovere la risorsa del gateway, è possibile eliminare la risorsa del gateway senza influire sull'installazione del gateway. 

1. Dal menu principale di Azure selezionare **tutte le risorse**. Individuare e selezionare la risorsa del gateway.

1. Se non è già selezionato, nel menu della risorsa del gateway selezionare **Gateway dati locale**. Sulla barra degli strumenti delle risorse del gateway selezionare **Elimina**.

   Esempio:

   ![Elimina gateway](./media/logic-apps-gateway-connection/gateway-delete.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Domande frequenti

**D**: Perché non viene visualizzata l'installazione del gateway durante la creazione della risorsa gateway in Azure? <br/>
**R**: Questo problema può verificarsi per i motivi seguenti:

* L'installazione del gateway è già registrata e richiesta da un'altra risorsa del gateway in Azure. Le installazioni del gateway non vengono visualizzate nell'elenco delle istanze dopo che le risorse del gateway vengono create per loro. Per controllare le registrazioni del gateway nel portale di Azure, esaminare tutte le risorse di Azure di tipo **Gateway dati locali** per *tutte* le sottoscrizioni di Azure.

* L'identità di Azure AD per la persona che ha installato il gateway è diversa da quella della persona che ha effettuato l'accesso al portale di Azure. Verificare di aver eseguito l'accesso con la stessa identità usata per l'installazione del gateway.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere le app per la logica](./logic-apps-securing-a-logic-app.md)
* [Esempi e scenari comuni per le app per la logica](./logic-apps-examples-and-scenarios.md)
