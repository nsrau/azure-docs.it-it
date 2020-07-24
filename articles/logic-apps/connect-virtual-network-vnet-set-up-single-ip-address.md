---
title: Configurare un indirizzo IP in uscita pubblico per ISEs
description: Informazioni su come configurare un singolo indirizzo IP in uscita pubblico per gli ambienti di Integration Services (ISEs) in app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 9ee804f7bed01ca0c7f365a04e6108afd9598157
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87066389"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Configurare un singolo indirizzo IP per uno o più ambienti del servizio di integrazione in app per la logica di Azure

Quando si lavora con app per la logica di Azure, è possibile configurare un [ *ambiente di Integration Services* ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) per l'hosting di app per la logica che richiedono l'accesso alle risorse in una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md). Quando si hanno più istanze di ISE che devono accedere ad altri endpoint con restrizioni IP, distribuire un [firewall di Azure](../firewall/overview.md) o un' [appliance di rete virtuale](../virtual-network/virtual-networks-overview.md#filter-network-traffic) nella rete virtuale e indirizzare il traffico in uscita attraverso il firewall o l'appliance virtuale di rete. È quindi possibile fare in modo che tutte le istanze di ISE nella rete virtuale usino un solo indirizzo IP pubblico, statico e prevedibile per comunicare con i sistemi di destinazione desiderati. In questo modo, non è necessario configurare ulteriori aperture del firewall nei sistemi di destinazione per ogni ISE.

Questo argomento illustra come instradare il traffico in uscita attraverso un firewall di Azure, ma è possibile applicare concetti simili a un'appliance virtuale di rete, ad esempio un firewall di terze parti da Azure Marketplace. Sebbene questo argomento sia incentrato sul programma di installazione di più istanze di ISE, è possibile usare questo approccio anche per un singolo ISE quando lo scenario richiede la limitazione del numero di indirizzi IP a cui è necessario accedere. Valutare se i costi aggiuntivi per il firewall o l'appliance di rete virtuale hanno senso per lo scenario. Altre informazioni su [Prezzi del servizio Firewall di Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="prerequisites"></a>Prerequisiti

* Un firewall di Azure in esecuzione nella stessa rete virtuale di ISE. Se non si dispone di un firewall, aggiungere prima di tutto [una subnet](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) denominata `AzureFirewallSubnet` alla rete virtuale. È quindi possibile [creare e distribuire un firewall](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) nella rete virtuale.

* Tabella di [Route](../virtual-network/manage-route-table.md)di Azure. Se non si dispone di un, [creare innanzitutto una tabella di route](../virtual-network/manage-route-table.md#create-a-route-table). Per ulteriori informazioni sul routing, vedere [routing del traffico di rete virtuale](../virtual-network/virtual-networks-udr-overview.md).

## <a name="set-up-route-table"></a>Configurare la tabella di route

1. Nella [portale di Azure](https://portal.azure.com)selezionare la tabella di route, ad esempio:

   ![Selezionare la tabella di route con la regola per indirizzare il traffico in uscita](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Per [aggiungere una nuova route](../virtual-network/manage-route-table.md#create-a-route), scegliere **Route**  >  **Aggiungi**dal menu tabella di route.

   ![Aggiungi route per indirizzare il traffico in uscita](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. Nel riquadro **Aggiungi route** [configurare la nuova route](../virtual-network/manage-route-table.md#create-a-route) con una regola che specifica che tutto il traffico in uscita verso il sistema di destinazione segue questo comportamento:

   * Usa l' [**appliance virtuale**](../virtual-network/virtual-networks-udr-overview.md#user-defined) come tipo di hop successivo.

   * Passa all'indirizzo IP privato per l'istanza del firewall come indirizzo hop successivo.

     Per trovare questo indirizzo IP, nel menu del firewall selezionare **Panoramica**, trovare l'indirizzo in **indirizzo IP privato**, ad esempio:

     ![Trovare l'indirizzo IP privato del firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Ecco un esempio che illustra come potrebbe apparire una regola di questo tipo:

   ![Configurare la regola per indirizzare il traffico in uscita](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Proprietà | Valore | Descrizione |
   |----------|-------|-------------|
   | **Nome route** | <*Unique-route-name*> | Nome univoco per la route nella tabella di route |
   | **Prefisso indirizzo** | <*Indirizzo di destinazione*> | Prefisso dell'indirizzo per il sistema di destinazione in cui si vuole che il traffico in uscita vada. Assicurarsi di usare la [notazione CIDR (Inter-Domain Routing) con classe](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) per questo indirizzo. In questo esempio, questo prefisso dell'indirizzo è per un server SFTP, descritto nella sezione [configurare la regola di rete](#set-up-network-rule). |
   | **Tipo hop successivo** | **Appliance virtuale** | [Tipo di hop](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) usato dal traffico in uscita |
   | **Indirizzo hop successivo** | <*firewall-privato-IP-Address*> | Indirizzo IP privato per il firewall |
   |||

<a name="set-up-network-rule"></a>

## <a name="set-up-network-rule"></a>Configurare la regola di rete

1. Nella portale di Azure individuare e selezionare il firewall. Nel menu Firewall, in **Impostazioni**, selezionare **regole**. Nel riquadro regole selezionare **raccolta regole di rete**  >  **Aggiungi raccolta regole di rete**.

   ![Aggiungi raccolta regole di rete al firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. Nella raccolta aggiungere una regola che consenta il traffico verso il sistema di destinazione.

   Si supponga, ad esempio, di avere un'app per la logica che viene eseguita in ISE e che deve comunicare con un server SFTP. Si crea una raccolta di regole di rete denominata che `LogicApp_ISE_SFTP_Outbound` contiene una regola di rete denominata `ISE_SFTP_Outbound` . Questa regola consente il traffico dall'indirizzo IP di qualsiasi subnet in cui ISE viene eseguito nella rete virtuale al server SFTP di destinazione usando l'indirizzo IP privato del firewall.

   ![Configurare la regola di rete per il firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Proprietà della raccolta regole di rete**

   | Proprietà | Valore | Descrizione |
   |----------|-------|-------------|
   | **Nome** | <*Network-Rule-Collection-Name*> | Nome della raccolta di regole di rete |
   | **Priority** | <*livello di priorità*> | Ordine di priorità da utilizzare per l'esecuzione della raccolta regole. Per altre informazioni, vedere [quali sono alcuni concetti di Azure firewall](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts)? |
   | **Azione** | **Consentito** | Tipo di azione da eseguire per questa regola |
   |||

   **Proprietà della regola di rete**

   | Proprietà | Valore | Descrizione |
   |----------|-------|-------------|
   | **Nome** | <*Network-Rule-Name*> | Nome della regola di rete |
   | **Protocollo** | <*protocolli di connessione*> | Protocolli di connessione da utilizzare. Ad esempio, se si usano le regole di NSG, selezionare sia **TCP** che **UDP**, non solo **TCP**. |
   | **Indirizzi di origine** | <*ISE-subnet-indirizzi*> | Indirizzi IP della subnet in cui viene eseguito ISE e da dove proviene il traffico proveniente dall'app per la logica |
   | **Indirizzi di destinazione** | <*destinazione-IP-Address*> | Indirizzo IP del sistema di destinazione in cui si vuole che il traffico in uscita passi. In questo esempio l'indirizzo IP è per il server SFTP. |
   | **Porte di destinazione** | <*porte di destinazione*> | Tutte le porte utilizzate dal sistema di destinazione per le comunicazioni in ingresso |
   |||

   Per ulteriori informazioni sulle regole di rete, vedere i seguenti articoli:

   * [Configurare una regola di rete](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Logica di elaborazione delle regole del Firewall di Azure](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Domande frequenti su Firewall di Azure](../firewall/firewall-faq.md)
   * [Azure PowerShell: New-AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule)
   * [INTERFACCIA della riga di comando di Azure: AZ Network Firewall Network-Rule](/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi alle reti virtuali di Azure tramite le App per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
