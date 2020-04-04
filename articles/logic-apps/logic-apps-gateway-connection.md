---
title: Accedere alle origini dati in localeAccess data sources on premises
description: Connettersi a origini dati locali da App per la logica di Azure creando una risorsa del gateway dati di Azure localeConnect to on-premises data sources from Azure Logic Apps by creating an Azure on-premises data gateway resource
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: 096943ff796f9c12c7f8715cadce5c3085965d4d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657127"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Connettersi a origini dati locali da App per la logica di Azure

Prima di poter accedere alle origini dati in locale dalle app per la logica, è necessario creare una risorsa di Azure dopo aver [installato il gateway dati *locale* in un computer locale.](../logic-apps/logic-apps-gateway-install.md) Le app per la logica usano quindi questa risorsa gateway di Azure nei trigger e nelle azioni forniti dai connettori locali disponibili per le app per la logica di Azure.Your logic apps then use this Azure gateway resource in the triggers and actions provided by the [on-premises connectors](../connectors/apis-list.md#on-premises-connectors) that are available for Azure Logic Apps.

Questo articolo illustra come creare la risorsa gateway di Azure per un gateway installato in precedenza [nel computer locale.](../logic-apps/logic-apps-gateway-install.md) Per ulteriori informazioni sul gateway, vedere [Funzionamento del gateway](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service).

> [!TIP]
> Per connettere le reti virtuali di Azure, considerare invece la possibilità di creare un [*ambiente del servizio di integrazione*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). 

Per informazioni su come usare il gateway con altri servizi, vedere i seguenti articoli:

* [Gateway dati locale di Microsoft Power Automate](/power-automate/gateway-reference)
* [Gateway dati locale di Microsoft Power BI](/power-bi/service-gateway-onprem)
* [Gateway dati locale di Microsoft Power Apps](/powerapps/maker/canvas-apps/gateway-reference)
* [Gateway dati locale di Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Origini dati supportate

Nelle app per la logica di Azure il gateway dati locale supporta i connettori locali per queste origini dati:In Azure Logic Apps, the on-premises data gateway supports the [on-premises connectors](../connectors/apis-list.md#on-premises-connectors) for these data sources:

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

Le app per la logica di Azure supportano le operazioni di lettura e scrittura tramite il gateway dati. Tuttavia, queste operazioni hanno [limiti sulla loro dimensione del payload](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations). Anche se il gateway stesso non comporta costi aggiuntivi, il modello di determinazione dei prezzi app per la logica si applica a questi connettori e ad altre operazioni in App per la logica di Azure.Although the gateway itself doesn't be additional costs, the [Logic Apps pricing model](../logic-apps/logic-apps-pricing.md) applies to these connectors and other operations in Azure Logic Apps.

## <a name="prerequisites"></a>Prerequisiti

* Il gateway dati locale è già [stato installato in un computer locale.](../logic-apps/logic-apps-gateway-install.md)

* Si usa lo [stesso account e sottoscrizione](../logic-apps/logic-apps-gateway-install.md#requirements) di Azure usati durante l'installazione del gateway dati. Questo account di Azure deve appartenere a un singolo tenant o directory di [Azure Active Directory (Azure AD).](../active-directory/fundamentals/active-directory-whatis.md#terminology)

* L'installazione del gateway non è già registrata e rivendicata da un'altra risorsa gateway di Azure.Your gateway installation isn't already registered and claimed by another Azure gateway resource.

  Quando si crea una risorsa gateway nel portale di Azure, si seleziona un'installazione gateway, che si collega alla risorsa gateway e solo a tale risorsa gateway. Nelle app per la logica di Azure i trigger e le azioni locali usano quindi la risorsa gateway per la connessione alle origini dati locali. In questi trigger e azioni si selezionano la sottoscrizione di Azure e la risorsa gateway associata che si vuole usare. Ogni risorsa gateway è collegata a una sola installazione gateway, che si collega a un solo account Azure.Each gateway resource links to only one gateway installation, which links to only One Azure account.

  > [!NOTE]
  > Solo l'amministratore del gateway può creare la risorsa gateway nel portale di Azure.Only the gateway administrator can create the gateway resource in the Azure portal. Attualmente, le entità servizio non sono supportate. 

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Creare una risorsa gateway di AzureCreate Azure gateway resource

Dopo aver installato il gateway in un computer locale, creare la risorsa di Azure per il gateway.

1. Accedere al [portale](https://portal.azure.com) di Azure con lo stesso account di Azure usato per installare il gateway.

1. Nella casella di ricerca del portale di Azure immettere "Gateway dati locale" e selezionare **Gateway dati locali**.

   ![Trovare "Gateway dati locale"](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. In **Gateway dati locali**selezionare **Aggiungi**.

   ![Aggiungere una nuova risorsa di Azure per il gateway datiAdd new Azure resource for data gateway](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. In **Crea gateway di connessione**specificare queste informazioni per la risorsa gateway. Al termine, selezionare **Crea**.

   | Proprietà | Descrizione |
   |----------|-------------|
   | **Nome risorsa** | Specificare un nome per la risorsa gateway contenente`-`solo lettere,`_`numeri, trattini ( ), caratteri di sottolineatura ( ), parentesi (`(`, `)`) o punti ( ).`.` |
   | **Sottoscrizione** | Selezionare la sottoscrizione di Azure per l'account di Azure usato per l'installazione del gateway. La sottoscrizione predefinita si basa sull'account di Azure usato per accedere. |
   | **Gruppo di risorse** | Gruppo di risorse di Azure che si vuole usareThe [Azure resource group](../azure-resource-manager/management/overview.md) that you want to use |
   | **Percorso** | La stessa area o posizione selezionata per il servizio cloud del gateway durante [l'installazione del gateway.](../logic-apps/logic-apps-gateway-install.md) In caso contrario, l'installazione del gateway non verrà visualizzata nell'elenco **Nome installazione.** Il percorso dell'app per la logica può essere diverso dalla posizione della risorsa gateway. |
   | **Nome installazione** | Selezionare un'installazione del gateway, che viene visualizzata nell'elenco solo quando vengono soddisfatte queste condizioni: <p><p>- L'installazione del gateway utilizza la stessa area della risorsa gateway che si desidera creare. <br>- L'installazione del gateway non è collegata a un'altra risorsa gateway di Azure.- The gateway installation isn't linked to another Azure gateway resource. <br>- L'installazione del gateway è collegata allo stesso account di Azure usato per creare la risorsa gateway. <br>- L'account di Azure appartiene a un singolo tenant o directory di Azure Active Directory (Azure AD) ed è lo stesso account usato per l'installazione del gateway.- Your Azure account belongs to a single [Azure Active Directory (Azure AD) tenant](../active-directory/fundamentals/active-directory-whatis.md#terminology) or directory and is the same account that was used for the gateway installation. <p><p>Per ulteriori informazioni, vedere la sezione [Domande frequenti.](#faq) |
   |||

   Ecco un esempio che mostra un'installazione del gateway che si trova nella stessa area della risorsa gateway ed è collegata allo stesso account Azure:Here is an example that shows a gateway installation that's at the same region of your gateway resource and is linked to the same Azure account:

   ![Fornire dettagli per creare una risorsa gateway datiProvide details to create data gateway resource](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Connettersi ai dati locali

Dopo aver creato la risorsa per il gateway e associato la sottoscrizione di Azure a questa risorsa, è possibile creare una connessione tra l'app per la logica e l'origine dati in locale usando il gateway.

1. Nel portale di Azure, creare o aprire l'app per la logica nella finestra di progettazione di app per la logica.

1. Aggiungere un connettore che supporta la connettività locale, ad esempio **SQL Server**.

1. Selezionare **Connect via on-premises data gateway** (Connetti tramite gateway dati locale).

1. In **Gateway**, nell'elenco **Sottoscrizioni** selezionare la sottoscrizione di Azure con la risorsa gateway desiderata.

1. Nell'elenco **Gateway di connessione,** che mostra le risorse gateway disponibili nella sottoscrizione selezionata, selezionare la risorsa gateway desiderata. Ogni risorsa gateway è collegata a un'installazione a gateway singolo.

   > [!NOTE]
   > L'elenco dei gateway include le risorse gateway in altre aree perché la posizione dell'app per la logica può differire dalla posizione della risorsa gateway. 

1. Fornire un nome di connessione univoco e altre informazioni necessarie, che dipendono dalla connessione che si desidera creare.

   Un nome di connessione univoco consente di trovare facilmente tale connessione in un secondo momento, soprattutto se si creano più connessioni. Se applicabile, è necessario includere anche il dominio completo per il proprio nome utente.

   Esempio:

   ![Creare una connessione tra l'app per la logica e il gateway dati](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Al termine, selezionare **Crea**.

A questo punto, la connessione del gateway è pronta per l'uso nell'app per la logica.

## <a name="edit-connection"></a>Modificare la connessione

Per aggiornare le impostazioni per una connessione gateway, è possibile modificare la connessione.

1. Per trovare tutte le connessioni API solo per l'app per la logica, nel menu dell'app per la logica, in Strumenti di **sviluppo,** selezionare **Connessioni API**.

   ![Nel menu dell'app per la logica selezionare "Connessioni API"On your logic app menu, select "API Connections"](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Selezionare la connessione gateway desiderata e quindi selezionare **Modifica connessione API**.

   > [!TIP]
   > Se gli aggiornamenti non hanno effetto, provare ad [arrestare e riavviare l'account del servizio Windows gateway](../logic-apps/logic-apps-gateway-install.md#restart-gateway) per l'installazione del gateway.

Per trovare tutte le connessioni API associate alla sottoscrizione di Azure:

* Nel menu del portale di Azure selezionare**Connessioni API****Web** >  **di tutti i servizi** > .
* In alternativa, scegliere Tutte **le risorse**dal menu del portale di Azure . Impostare il filtro **Tipo** **su Connessione API**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Eliminare la risorsa del gateway

Per creare una risorsa gateway diversa, collegare l'installazione del gateway a una risorsa gateway diversa o rimuovere la risorsa gateway, è possibile eliminare la risorsa gateway senza influire sull'installazione del gateway.

1. Nel menu del portale di Azure selezionare **Tutte le risorse**oppure cercare e selezionare Tutte le **risorse** da qualsiasi pagina. Individuare e selezionare la risorsa del gateway.

1. Se non è già selezionato, nel menu della risorsa del gateway selezionare **Gateway dati locale**. Nella barra degli strumenti delle risorse gateway selezionare **Elimina**.

   Ad esempio:

   ![Eliminare la risorsa gateway in AzureDelete gateway resource in Azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Domande frequenti

**D:** Perché l'installazione del gateway non viene visualizzata quando si crea la risorsa gateway in Azure? <br/>
**R**: Questo problema può verificarsi per i motivi seguenti:

* L'account Azure deve essere lo stesso account collegato all'installazione del gateway nel computer locale. Verificare di aver eseguito l'accesso al portale di Azure con la stessa identità collegata all'installazione del gateway. Assicurarsi inoltre che l'account di Azure appartenga a un singolo [tenant o directory](../active-directory/fundamentals/active-directory-whatis.md#terminology) di Azure AD e che sia impostato sullo stesso tenant o directory di Azure AD usata durante l'installazione del gateway.

* L'installazione della risorsa gateway e del gateway deve usare la stessa area. Tuttavia, il percorso dell'app per la logica può essere diverso dal percorso della risorsa gateway.

* L'installazione del gateway è già registrata e rivendicata da un'altra risorsa gateway. Queste installazioni non verranno visualizzate nell'elenco **Nome installazione.** Per esaminare le registrazioni dei gateway nel portale di Azure, trovare tutte le risorse di Azure con il tipo Gateway dati locale in *tutte le* sottoscrizioni di Azure.To review your gateway registrations in the Azure portal, find all your Azure resources that have the **On-premises Data Gateways** type across all your Azure subscriptions. Per scollegare l'installazione del gateway dall'altra risorsa gateway, vedere [Eliminare la risorsa gateway](#change-delete-gateway-resource).

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere le app per la logica](./logic-apps-securing-a-logic-app.md)
* [Esempi e scenari comuni per le app per la logica](./logic-apps-examples-and-scenarios.md)
