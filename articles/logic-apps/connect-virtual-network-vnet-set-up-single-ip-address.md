---
title: Configurare un indirizzo IP in uscita pubblico per gli ISE
description: Informazioni su come configurare un singolo indirizzo IP pubblico in uscita per gli ambienti del servizio di integrazione (ISE) in App per la logica di AzureLearn how to set up a single public outbound IP address for integration service environments (ISEs) in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 619c68b84291bc35b8216194ac4534393fde454c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191527"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Configurare un singolo indirizzo IP per uno o più ambienti del servizio di integrazione in App per la logica di AzureSet up a single IP address for one or more integration service environments in Azure Logic Apps

Quando si lavora con le app per la logica di Azure, è possibile configurare un ambiente del servizio di [ *integrazione* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) per l'hosting di app per la logica che richiedono l'accesso alle risorse in una rete virtuale di [Azure.](../virtual-network/virtual-networks-overview.md) Quando si dispone di più istanze ISE che richiedono l'accesso ad altri endpoint con restrizioni IP, distribuire un firewall di [Azure](../firewall/overview.md) o [un'appliance virtuale](../virtual-network/virtual-networks-overview.md#filter-network-traffic) di rete nella rete virtuale e instradare il traffico in uscita attraverso tale firewall o appliance virtuale di rete. È quindi possibile fare in modo che tutte le istanze di ISE nella rete virtuale utilizzino un unico indirizzo IP pubblico, statico e prevedibile per comunicare con i sistemi di destinazione. In questo modo, non è necessario impostare ulteriori aperture del firewall in tali sistemi di destinazione per ogni ISE.

Questo argomento illustra come instradare il traffico in uscita attraverso un firewall di Azure, ma è possibile applicare concetti simili a un'appliance virtuale di rete, ad esempio un firewall di terze parti da Azure Marketplace.This topic shows how to route outbound traffic through an Azure Firewall, but you can apply similar concepts to a network virtual appliance such as a third-party firewall from the Azure Marketplace. Anche se questo argomento si concentra sulla configurazione per più istanze ISE, è anche possibile utilizzare questo approccio per un singolo ISE quando lo scenario richiede di limitare il numero di indirizzi IP che devono accedere. Valutare se i costi aggiuntivi per il firewall o l'appliance di rete virtuale sono adatti per lo scenario. Altre informazioni sui prezzi di Firewall di Azure .Learn more about [Azure Firewall pricing](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="prerequisites"></a>Prerequisiti

* Un firewall di Azure eseguito nella stessa rete virtuale di ISE. Se non si dispone di un firewall, aggiungere `AzureFirewallSubnet` innanzitutto una [subnet](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) denominata alla rete virtuale. È quindi possibile [creare e distribuire un firewall](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) nella rete virtuale.

* Una [tabella di route](../virtual-network/manage-route-table.md)di Azure . Se non ne è avviene, creare prima [una tabella di route.](../virtual-network/manage-route-table.md#create-a-route-table) Per ulteriori informazioni sul routing, vedere [Routing del traffico](../virtual-network/virtual-networks-udr-overview.md)di rete virtuale .

## <a name="set-up-route-table"></a>Impostare la tabella di percorso

1. Nel [portale di Azure](https://portal.azure.com)selezionare la tabella di route, ad esempio:In the Azure portal , select the route table, for example:

   ![Selezionare la tabella di route con la regola per indirizzare il traffico in uscita](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Per [aggiungere una nuova route,](../virtual-network/manage-route-table.md#create-a-route)nel menu della tabella di route selezionare **Routes** > **Add**.

   ![Aggiungi route per indirizzare il traffico in uscita](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. Nel riquadro **Aggiungi route** [impostare la nuova route](../virtual-network/manage-route-table.md#create-a-route) con una regola che specifichi che tutto il traffico in uscita verso il sistema di destinazione segue questo comportamento:

   * Utilizza [**l'appliance virtuale**](../virtual-network/virtual-networks-udr-overview.md#user-defined) come tipo di hop successivo.

   * Passa all'indirizzo IP privato per l'istanza del firewall come indirizzo dell'hop successivo.

     Per trovare questo indirizzo IP, nel menu del firewall selezionare **Panoramica**, individuare l'indirizzo in **Indirizzo IP privato**, ad esempio:

     ![Trovare l'indirizzo IP privato del firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Ecco un esempio che mostra l'aspetto di una regola di questo tipo:Here's an example that shows how such a rule might look:

   ![Imposta regola per l'indirizzamento del traffico in uscita](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Proprietà | valore | Descrizione |
   |----------|-------|-------------|
   | **Nome route** | <*nome-percorso univoco*> | Un nome univoco per la route nella tabella di route |
   | **Prefisso indirizzo** | <*indirizzo di destinazione*> | Indirizzo del sistema di destinazione in cui si desidera che il traffico vada. Assicurarsi di utilizzare la [notazione CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) per questo indirizzo. |
   | **Tipo di hop successivo** | **Appliance virtuale** | Tipo [di hop](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) utilizzato dal traffico in uscita |
   | **Indirizzo hop successivo** | <*firewall-indirizzo-IP privato*> | L'indirizzo IP privato per il firewall |
   |||

## <a name="set-up-network-rule"></a>Configurare la regola di rete

1. Nel portale di Azure individuare e selezionare il firewall. Nel menu firewall, in **Impostazioni**, selezionare **Regole**. Nel riquadro delle regole selezionare **Raccolta** > regole di**rete Aggiungi raccolta regole**di rete .

   ![Aggiungere la raccolta di regole di rete al firewallAdd network rule collection to firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. Nella raccolta aggiungere una regola che consenta il traffico al sistema di destinazione.

   Si supponga, ad esempio, di disporre di un'app per la logica che viene eseguita in un ISE e deve comunicare con un sistema SFTP. Creare una raccolta di regole `LogicApp_ISE_SFTP_Outbound`di rete denominata `ISE_SFTP_Outbound`, che contiene una regola di rete denominata . Questa regola consente il traffico dall'indirizzo IP di qualsiasi subnet in cui viene eseguito ISE nella rete virtuale al sistema SFTP di destinazione utilizzando l'indirizzo IP privato del firewall.

   ![Configurare la regola di rete per il firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Proprietà raccolta regole di rete**

   | Proprietà | valore | Descrizione |
   |----------|-------|-------------|
   | **Nome** | <*network-rule-collection-name*> | Il nome della raccolta di regole di rete |
   | **Priority** | <*livello di priorità*> | Ordine di priorità da utilizzare per l'esecuzione della raccolta di regole. Per altre informazioni, vedere Quali sono alcuni concetti di Firewall di [Azure?](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts) |
   | **Azione** | **Consentire** | Tipo di azione da eseguire per questa regola |
   |||

   **Proprietà delle regole di rete**

   | Proprietà | valore | Descrizione |
   |----------|-------|-------------|
   | **Nome** | <*nome-regola di rete*> | Il nome della regola di rete |
   | **Protocollo** | <*protocolli di connessione*> | Protocolli di connessione da utilizzare. Ad esempio, se si utilizzano le regole del gruppo di sicurezza di rete, selezionare **TCP** e **UDP**, non solo **TCP**. |
   | **Indirizzi di origine** | <*Indirizzi di subnet ISE*> | Gli indirizzi IP della subnet da dove viene eseguito ISE e dove ha origine il traffico dall'app per la logica |
   | **Indirizzi di destinazione** | <*indirizzo-indirizzo-ip di destinazione*> | L'indirizzo IP del sistema di destinazione in cui si desidera che il traffico |
   | **Porte di destinazione** | <*porte di destinazione*> | Tutte le porte utilizzate dal sistema di destinazione per le comunicazioni in ingresso |
   |||

   Per altre informazioni sulle regole di rete, vedere gli articoli seguenti:For more information about network rules, see these articles:

   * [Configurare una regola di rete](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Logica di elaborazione delle regole del Firewall di Azure](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Domande frequenti su Firewall di Azure](../firewall/firewall-faq.md)
   * [Azure PowerShell: New-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Interfaccia di comando di Azure: az network firewall network network-rule](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi alle reti virtuali di Azure tramite le App per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)