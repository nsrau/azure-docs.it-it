---
title: Connettere le reti virtuali di Azure da App per la logica di Azure attraverso un ambiente del servizio di integrazione (ISE)
description: Creare un ambiente del servizio di integrazione (ISE) in modo che le app per la logica e gli account di integrazione possano accedere alle reti virtuali di Azure, rimanendo al contempo private e isolate da Azure pubblico o "globale"
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/25/2018
ms.openlocfilehash: b4e4e801c3c54b635f2f13b319257018ea544c03
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "49404121"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-through-an-integration-service-environment-ise"></a>Connettere le reti virtuali di Azure da App per la logica di Azure attraverso un ambiente del servizio di integrazione (ISE)

> [!NOTE]
> Questa funzionalità è in *anteprima privata*. Per richiedere l'accesso, [creare la richiesta di iscrizione qui](https://aka.ms/iseprivatepreview).

Per gli scenari di integrazione in cui le app per la logica e gli account di integrazione devono accedere a una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md), creare un [*ambiente del servizio di integrazione* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), che è un ambiente privato e isolato che usa una risorsa di archiviazione dedicata e altre risorse che vengono mantenute separate dal servizio App per la logica pubblico o *globale*. Questa separazione riduce anche qualsiasi impatto che altri tenant di Azure possono avere sulle prestazioni delle app create. È possibile collegare questo ambiente del servizio di integrazione alla rete virtuale di Azure, che quindi distribuisce il servizio App per la logica nella rete virtuale. Quando si crea un'app per la logica o un account di integrazione, selezionare questo ISE come posizione per l'app o l'account. L'app per la logica o l'account di integrazione può quindi accedere direttamente alle risorse, ad esempio alle macchine virtuali (VM), ai server, ai sistemi e ai servizi della rete virtuale in uso. 

![Selezionare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Questo articolo descrive come eseguire le attività seguenti:

* Configurare le autorizzazioni per la rete virtuale di Azure in modo che l'istanza privata delle app per la logica possa accedere alla rete virtuale.

* Creare un ambiente del servizio di integrazione (ISE). 

* Creare un'app per la logica che possa essere eseguita nell'ISE. 

* Creare un account di integrazione e collegarlo alle app per la logica presenti nell'ISE.

Per altre informazioni sugli ambienti del servizio di integrazione, vedere [Accedere alle risorse della rete virtuale di Azure tramite App per la logica di Azure isolate](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>. 

* Se non si dispone di una rete virtuale di Azure, vedere l'articolo su come [creare una rete virtuale di Azure](../virtual-network/quick-create-portal.md). 

  > [!IMPORTANT]
  > Per la creazione dell'ambiente non è necessaria una rete virtuale di Azure, ma è possibile selezionare *solo* una rete virtuale come peer dell'ambiente in uso quando si crea tale ambiente. 

* Per consentire alle app per la logica un accesso diretto alla rete virtuale di Azure [configurare le autorizzazioni del Controllo degli accessi in base al ruolo (RBAC)](#vnet-access) in modo che il servizio delle app per la logica disponga delle autorizzazioni di accesso alla rete virtuale. 

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="vnet-access"></a>

## <a name="set-virtual-network-permissions"></a>Configurare le autorizzazioni della rete virtuale

Quando si crea un ambiente del servizio di integrazione in Azure, è possibile selezionare una rete virtuale di Azure come *peer* per l'ambiente. È tuttavia possibile creare solo questa relazione, o *peering*, durante la creazione dell'ambiente. Questa relazione consente al servizio delle App per la logica di Azure di connettersi direttamente alle risorse della rete virtuale e all'ambiente di accedere a tali risorse. 

Prima di poter selezionare la rete virtuale, è necessario configurare le autorizzazioni del Controllo degli accessi in base al ruolo (RBAC) nella rete virtuale. Per completare questa attività, è necessario assegnare i ruoli specifici del servizio App per la logica di Azure.

1. Nel [portale di Azure](https://portal.azure.com) trovare e selezionare la rete virtuale in questione. 

1. Nel menu della rete virtuale, selezionare **Controllo dei diritti di accesso (IAM)**. 

1. Sotto **Controllo dei diritti di accesso**, selezionare **Assegnazione ruolo**, se non è già selezionato. Nella barra degli strumenti **Assegnazione ruolo**, scegliere **Aggiungi**. 

   ![Aggiungere assegnazioni di ruolo](./media/connect-virtual-network-vnet-isolated-environment/set-up-role-based-access-control-vnet.png)

1. Nel riquadro **Aggiungi autorizzazioni**, configurare ogni ruolo in questa tabella per il servizio delle App per la logica di Azure. Assicurarsi di selezionare **Salva** dopo aver configurato ogni ruolo:

   | Ruolo | Assegna accesso a | Select | 
   |------|------------------|--------|
   | **Collaboratore di rete** | **Applicazione, gruppo o utente di Azure AD** | Immettere **App per la logica di Azure**. Quando viene visualizzato l'elenco dei membri, selezionare lo stesso valore. <p>**Suggerimento**: se non è possibile trovare questo servizio, immettere l'ID del servizio delle App per la logica di Azure: `7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   | **Collaboratore classico** | **Applicazione, gruppo o utente di Azure AD** | Immettere **App per la logica di Azure**. Quando viene visualizzato l'elenco dei membri, selezionare lo stesso valore. <p>**Suggerimento**: se non è possibile trovare questo servizio, immettere l'ID del servizio delle App per la logica di Azure: `7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   |||| 

   Ad esempio: 

   ![Aggiungere autorizzazioni](./media/connect-virtual-network-vnet-isolated-environment/add-contributor-roles.png)

   Per altre informazioni sulle autorizzazioni dei ruoli necessarie per il peering, vedere la [sezione Autorizzazioni in Creare, modificare o eliminare un peering reti virtuali](../virtual-network/virtual-network-manage-peering.md#permissions). 

Se la rete virtuale è connessa tramite ExpressRoute di Azure, VPN Azure da punto a sito o VPN Azure da sito a sito, continuare con la sezione successiva per aggiungere la subnet del gateway necessaria. In caso contrario, proseguire con [Creare l'ambiente](#create-environment).

<a name="add-gateway-subnet"></a>

## <a name="add-gateway-subnet-for-virtual-networks-with-expressroute-or-vpns"></a>Aggiungere la subnet del gateway per le reti virtuali con ExpressRoute o VPN

Dopo aver completato i passaggi precedenti, per consentire all'ambiente del servizio di integrazione (ISE) di accedere a una rete virtuale di Azure connessa tramite [Azure ExpressRoute](../expressroute/expressroute-introduction.md), [VPN Azure da punto a sito](../vpn-gateway/point-to-site-about.md) o [VPN Azure da sito a sito](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), alla rete virtuale è necessario aggiungere anche una [*subnet del gateway*](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsub):

1. Nel [portale di Azure](https://portal.azure.com) trovare e selezionare la rete virtuale in questione. Nel menu della rete virtuale, selezionare **Subnet**, quindi scegliere **Subnet Gateway** > **OK**.

   ![Aggiungere la subnet del gateway](./media/connect-virtual-network-vnet-isolated-environment/add-gateway-subnet.png)

1. A questo punto, creare una [*tabella di route*](../virtual-network/manage-route-table.md) da associare alla subnet del gateway creata in precedenza.

   1. Nel menu principale di Azure selezionare **Crea una risorsa** > 
   **Networking** > **Tabella di route**.

      ![Creare una tabella di route](./media/connect-virtual-network-vnet-isolated-environment/create-route-table.png)

   1. Fornire informazioni sulla tabella di route, ad esempio il nome, la sottoscrizione di Azure da usare, il gruppo di risorse di Azure e la posizione. Assicurarsi che la proprietà **propagazione route BGP** sia **abilitata**, quindi scegliere **Creare**.

      ![Fornire i dettagli di tabella di route](./media/connect-virtual-network-vnet-isolated-environment/enter-route-table-information.png)

   1. Nel menu della tabella di route, selezionare **Subnet**, quindi scegliere **Associare**. 

      ![Collegare la tabella di route alla subnet](./media/connect-virtual-network-vnet-isolated-environment/associate-route-table.png)

   1. Selezionare **Rete virtuale**, quindi selezionare la rete virtuale in questione.
   
   1. Selezionare **Subnet**e quindi selezionare la subnet gateway creata in precedenza.

   1. Al termine dell'operazione, scegliere **OK**.

1. Se si dispone di una VPN da punto a sito, completare anche questi passaggi:

   1. In Azure trovare e selezionare la risorsa del gateway della rete virtuale.

   1. Nel menu del gateway, selezionare **Configurazione da punto a sito**. 
   quindi scegliere **Scarica client VPN** in modo da usare la configurazione più recente del client VPN.

      ![Scaricare il client VPN più recente](./media/connect-virtual-network-vnet-isolated-environment/download-vpn-client.png)

A questo punto si conclude la procedura di configurazione della subnet del gateway per reti virtuali che usano ExpressRoute, VPN sa punto a sito o VPN da sito a sito. Per proseguire con la creazione dell'ambiente del servizio di integrazione, seguire i passaggi successivi.

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Creare l'ISE

Per creare un ambiente del servizio di integrazione (ISE), seguire questa procedura:

1. Scegliere [Crea una risorsa](https://portal.azure.com) dal menu principale di Azure nel **portale di Azure**.

   ![Create new resource (Crea nuova risorsa)](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Nella casella di ricerca, digitare "ambiente del servizio di integrazione" come filtro.
Nell'elenco dei risultati, selezionare **ambiente del servizio di integrazione (anteprima)**, quindi scegliere **Creare**.

   ![Selezionare "Ambiente del servizio di integrazione"](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   ![Scegliere "Creare"](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Fornire questi dettagli per l'ambiente:

   ![Fornire i dettagli dell'ambiente](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Proprietà | Obbligatoria | Valore | DESCRIZIONE |
   |----------|----------|-------|-------------|
   | **Nome** | Yes | <*Nome ambiente*> | Il nome da assegnare all'ambiente | 
   | **Sottoscrizione** | Yes | <*nome sottoscrizione di Azure*> | Sottoscrizione di Azure da usare per l'ambiente | 
   | **Gruppo di risorse** | Yes | <*Azure-resource-group-name*> | Il gruppo di risorse di Azure in cui si desidera creare l'ambiente |
   | **Posizione** | Yes | <*Azure-datacenter-region*> | L'area del datacenter di Azure in cui archiviare le informazioni sull'ambiente |
   | **Peer VNET** | No  | <*Azure-VNET-name*> | La rete virtuale di Azure da associare con l'ambiente come *peer* in modo che le app per la logica in quell'ambiente possano accedere alla rete virtuale. Prima di creare questa relazione, assicurarsi che nella rete virtuale sia già [configurato il controllo degli accessi in base al ruolo per le App per la logica di Azure](#vnet-access). <p>**Importante**: anche se una rete virtuale non è necessaria, è possibile selezionare una rete virtuale *solo* quando si crea l'ambiente. | 
   | **Nome peering** | Sì con una rete virtuale selezionata | <*peering-name*> | Il nome da assegnare alla relazione peer | 
   | **Intervallo IP rete virtuale** | Sì con una rete virtuale selezionata | <*IP-address-range*> | L'intervallo di indirizzi IP da usare per creare le risorse nell'ambiente in uso. Questo intervallo deve usare il [formato Classless Interdomain Routing (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing), ad esempio 10.0.0.1/16, e richiede uno spazio di indirizzamento di classe B. L'intervallo non deve esistere all'interno dello spazio di indirizzamento della rete virtuale selezionata nella proprietà **Peer VNET**, né all'interno di tutti gli altri indirizzi IP privati a cui è connessa la rete di peer tramite peering o gateway. <p><p>**Importante**: *non è possibile modificare* questo intervallo di indirizzi dopo aver creato l'ambiente. |
   |||||
   
1. Al termine dell'operazione, scegliere **Crea**. 

   Azure avvia la distribuzione dell'ambiente, ma questo processo potrebbe richiedere *fino a due ore* per essere completato. 
   Per controllare lo stato della distribuzione, sulla barra degli strumenti di Azure, scegliere l'icona delle notifiche, che consente di aprire il riquadro notifiche.

   ![Controllare lo stato della distribuzione](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Al completamento della distribuzione, Azure Mostra questa notifica:

   ![Distribuzione completata](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

1. Per visualizzare l'ambiente, scegliere **Vai alla risorsa** se Azure non mostra automaticamente l'ambiente al termine della distribuzione.  

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Creare un'app per la logica - ISE

Per creare app per la logica che usano l'ambiente del servizio di integrazione (ISE), seguire la procedura consueta illustrata sotto la sezione [come creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md) ma tenendo conto delle seguenti differenze e considerazioni: 

* Quando si crea l'app per la logica, la proprietà **Posizione** elenca gli ISE sotto **Ambienti del servizio di integrazione**, oltre alle aree disponibili. Selezionare l'ISE, anziché un'area, ad esempio:

  ![Selezionare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* È possibile usare le stesse impostazioni, ad esempio il trigger HTTP o l'azione, che vengono eseguite nello stesso ISE dell'app per la logica padre. Anche i connettori con l'etichetta **ISE** vengono eseguiti nello stesso ISE dell'app per la logica padre. I connettori senza l'etichetta **ISE** vengono eseguiti nel servizio App per la logica globale.

  ![Selezionare i connettori ISE](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* Se in precedenza è stato configurato l'ISE con una rete virtuale di Azure come peer, le app per la logica nell'ISE possono accedere direttamente alle risorse di quella rete virtuale. Le app per la logica possono accedere direttamente ai sistemi locali in una rete virtuale collegata a un ambiente del servizio di integrazione usando uno di questi elementi: 

  * Connettore ISE per tale sistema, ad esempio, SQL Server

  * Azione HTTP 

  * Connettore personalizzato

  È possibile connettersi ai sistemi locali non inclusi in una rete virtuale o senza connettori ISE dopo aver [configurato e usato il gateway dati locale](../logic-apps/logic-apps-gateway-install.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Creare account di integrazione - ISE

Per usare un account di integrazione con app per la logica in un ambiente del servizio di integrazione (ISE), l'account di integrazione deve usare lo *stesso ambiente* usato dalle app per la logica. Le app per la logica in un ISE possono fare riferimento solo agli account di integrazione che sono nello stesso ISE. 

Per creare un account di integrazione che usa un ISE, seguire la procedura consueta riportata nella sezione su [come creare gli account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), ma la proprietà **Posizione** ora elencherà gli ISE sotto **Ambienti del servizio integrazione**, oltre alle aree disponibili. Selezionare l'ISE, anziché un'area, ad esempio:

![Selezionare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forum di App per la logica di Azure</a>.
* Per votare o inviare idee relative alle funzionalità, visitare il <a href="http://aka.ms/logicapps-wish" target="_blank">sito dei commenti e suggerimenti degli utenti di App per la logica</a>.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md)
* Informazioni sull'[integrazione della rete virtuale per i servizi di Azure](../virtual-network/virtual-network-for-azure-services.md)
