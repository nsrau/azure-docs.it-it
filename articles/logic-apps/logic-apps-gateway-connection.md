---
title: Accedere alle origini dati in locale
description: Connettersi alle origini dati locali da app per la logica di Azure creando una risorsa del gateway dati locale di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 11/06/2019
ms.openlocfilehash: 0e2dcec15566749b58c439b68532829b67716754
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815102"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Connettersi a origini dati locali da App per la logica di Azure

Prima di poter accedere alle origini dati locali dalle app per la logica, è necessario creare una risorsa di Azure dopo aver [installato il *gateway dati* locale in un computer locale](../logic-apps/logic-apps-gateway-install.md). Le app per la logica usano quindi questa risorsa del gateway di Azure nei trigger e nelle azioni fornite dai [connettori locali](../connectors/apis-list.md#on-premises-connectors) disponibili per le app per la logica di Azure.

Questo articolo illustra come creare una risorsa del gateway di Azure per un [gateway installato in precedenza nel computer locale](../logic-apps/logic-apps-gateway-install.md). Per altre informazioni sul gateway, vedere funzionamento [del gateway](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> Per connettere le reti virtuali di Azure, considerare invece la possibilità di creare un [*ambiente del servizio di integrazione*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). 

Per informazioni su come usare il gateway con altri servizi, vedere i seguenti articoli:

* [Microsoft Power automatizza il gateway dati locale](/power-automate/gateway-reference)
* [Gateway dati locale di Microsoft Power BI](/power-bi/service-gateway-onprem)
* [Gateway dati locale di Microsoft Power Apps](/powerapps/maker/canvas-apps/gateway-reference)
* [Gateway dati locale di Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Origini dati supportate

In app per la logica di Azure il gateway dati locale supporta i [connettori locali](../connectors/apis-list.md#on-premises-connectors) per queste origini dati:

* BizTalk Server 2016
* File system
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

App per la logica di Azure supporta operazioni di lettura e scrittura tramite il gateway dati. Tuttavia, queste operazioni hanno [limiti sulle dimensioni del payload](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations). Sebbene il gateway stesso non comporti costi aggiuntivi, il [modello di prezzi di app](../logic-apps/logic-apps-pricing.md) per la logica si applica a questi connettori e ad altre operazioni in app per la logica di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Il gateway dati locale è già stato [installato in un computer locale](../logic-apps/logic-apps-gateway-install.md).

* Si usano lo [stesso account e la stessa sottoscrizione di Azure](../logic-apps/logic-apps-gateway-install.md#requirements) usati durante l'installazione del gateway dati. È necessario che questo account di Azure appartenga a un singolo [tenant o directory di Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md#terminology).

* L'installazione del gateway non è già registrata ed è richiesta da un'altra risorsa del gateway di Azure.

  Quando si crea una risorsa del gateway nel portale di Azure, si seleziona un'installazione del gateway, che collega alla risorsa del gateway e solo a tale risorsa del gateway. In app per la logica di Azure, trigger e azioni locali usare la risorsa gateway per la connessione alle origini dati locali. In questi trigger e azioni è possibile selezionare la sottoscrizione di Azure e la risorsa gateway associata che si vuole usare. Ogni risorsa del gateway è collegata a una sola installazione del gateway, che è collegata a un solo account Azure.

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Creare una risorsa del gateway di Azure

Dopo aver installato il gateway in un computer locale, creare la risorsa di Azure per il gateway.

1. Accedere al [portale di Azure](https://portal.azure.com) con lo stesso account Azure usato per installare il gateway.

1. Nella casella di ricerca portale di Azure immettere "gateway dati locale" e selezionare **gateway dati locali**.

   ![Trovare "Gateway dati locale"](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. In **gateway dati locali**selezionare **Aggiungi**.

   ![Aggiungere una nuova risorsa di Azure per il gateway dati](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. In **Crea gateway di connessione**fornire queste informazioni per la risorsa del gateway. Al termine, selezionare **Crea**.

   | Proprietà | Description |
   |----------|-------------|
   | **Nome risorsa** | Specificare un nome per la risorsa del gateway che contenga solo lettere, numeri, trattini (`-`), caratteri di sottolineatura (`_`), parentesi (`(`, `)`) o punti (`.`). |
   | **Sottoscrizione** | Selezionare la sottoscrizione di Azure per l'account Azure usato per l'installazione del gateway. La sottoscrizione predefinita si basa sull'account di Azure usato per accedere. |
   | **Gruppo di risorse** | Il [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) che si vuole usare |
   | **Località** | La stessa area o la stessa località selezionata per il servizio cloud gateway durante l' [installazione del gateway](../logic-apps/logic-apps-gateway-install.md). In caso contrario, l'installazione del gateway non verrà visualizzata nell'elenco **nome installazione** . Il percorso dell'app per la logica può essere diverso da quello delle risorse del gateway. |
   | **Nome installazione** | Selezionare un'installazione del gateway che viene visualizzata nell'elenco solo quando vengono soddisfatte le condizioni seguenti: <p><p>-L'installazione del gateway usa la stessa area della risorsa del gateway che si vuole creare. <br>-L'installazione del gateway non è collegata a un'altra risorsa del gateway di Azure. <br>-L'installazione del gateway è collegata allo stesso account Azure usato per creare la risorsa del gateway. <br>-L'account Azure appartiene a un singolo [tenant o a una singola Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md#terminology) ed è lo stesso account usato per l'installazione del gateway. <p><p>Per ulteriori informazioni, vedere la sezione [domande frequenti](#faq) . |
   |||

   Di seguito è riportato un esempio che mostra un'installazione del gateway che si trova nella stessa area della risorsa del gateway ed è collegata allo stesso account Azure:

   ![Specificare i dettagli per creare una risorsa del gateway dati](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Connettiti a dati locali

Dopo aver creato la risorsa per il gateway e associato la sottoscrizione di Azure a questa risorsa, è possibile creare una connessione tra l'app per la logica e l'origine dati in locale usando il gateway.

1. Nel portale di Azure, creare o aprire l'app per la logica nella finestra di progettazione di app per la logica.

1. Aggiungere un connettore che supporta la connettività locale, ad esempio **SQL Server**.

1. Selezionare **Connect via on-premises data gateway** (Connetti tramite gateway dati locale).

1. Nell'elenco **sottoscrizioni** in **gateway**selezionare la sottoscrizione di Azure con la risorsa del gateway desiderata.

1. Dall'elenco **gateway connessione** , che mostra le risorse del gateway disponibili nella sottoscrizione selezionata, selezionare la risorsa del gateway desiderata. Ogni risorsa del gateway è collegata a una singola installazione del gateway.

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

   ![Nel menu dell'app per la logica selezionare "connessioni API"](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Selezionare la connessione del gateway desiderata, quindi selezionare **Modifica connessione API**.

   > [!TIP]
   > Se gli aggiornamenti non hanno effetto, provare ad [arrestare e riavviare l'account del servizio Windows del gateway](../logic-apps/logic-apps-gateway-install.md#restart-gateway) per l'installazione del gateway.

Per trovare tutte le connessioni API associate alla sottoscrizione di Azure:

* Dal menu portale di Azure selezionare **tutti i servizi** > **connessioni API** > **Web** .
* In alternativa, scegliere **tutte le risorse**dal menu portale di Azure. Impostare il filtro dei **tipi** su **connessione API**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Eliminare la risorsa del gateway

Per creare una risorsa del gateway diversa, collegare l'installazione del gateway a una risorsa del gateway diversa oppure rimuovere la risorsa del gateway, è possibile eliminare la risorsa del gateway senza influire sull'installazione del gateway.

1. Dal menu portale di Azure selezionare tutte le **risorse**oppure cercare e selezionare **tutte le risorse** da qualsiasi pagina. Individuare e selezionare la risorsa del gateway.

1. Se non è già selezionato, nel menu della risorsa del gateway selezionare **Gateway dati locale**. Sulla barra degli strumenti delle risorse del gateway selezionare **Elimina**.

   ad esempio:

   ![Eliminare la risorsa del gateway in Azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Domande frequenti

**D**: perché non viene visualizzata l'installazione del gateway quando si crea la risorsa del gateway in Azure? <br/>
**R**: Questo problema può verificarsi per i motivi seguenti:

* L'account Azure deve essere lo stesso account collegato all'installazione del gateway nel computer locale. Verificare di aver eseguito l'accesso al portale di Azure con la stessa identità collegata all'installazione del gateway. Assicurarsi anche che l'account di Azure appartenga a un singolo [tenant o directory di Azure ad](../active-directory/fundamentals/active-directory-whatis.md#terminology) e che sia impostato sullo stesso tenant o directory Azure ad usato durante l'installazione del gateway.

* La risorsa gateway e l'installazione del gateway devono usare la stessa area. Tuttavia, la posizione dell'app per la logica può essere diversa da quella della risorsa gateway.

* L'installazione del gateway è già registrata ed è richiesta da un'altra risorsa del gateway. Queste installazioni non verranno visualizzate nell'elenco **nome installazione** . Per esaminare le registrazioni del gateway nel portale di Azure, trovare tutte le risorse di Azure che hanno il tipo **di gateway dati locali** in *tutte* le sottoscrizioni di Azure. Per scollegare l'installazione del gateway dall'altra risorsa del gateway, vedere [eliminare la risorsa](#change-delete-gateway-resource)del gateway.

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere le app per la logica](./logic-apps-securing-a-logic-app.md)
* [Esempi e scenari comuni per le app per la logica](./logic-apps-examples-and-scenarios.md)
