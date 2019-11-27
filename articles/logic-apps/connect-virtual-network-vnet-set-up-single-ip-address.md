---
title: Configurare l'accesso per più ISEs-app per la logica di Azure
description: Per più ambienti del servizio di integrazione (ISEs) è possibile configurare un singolo indirizzo IP in uscita pubblico per accedere a sistemi esterni da app per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: f35b6836012df7ac3879070f7a85fbfb21b456c0
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74549245"
---
# <a name="set-up-access-for-multiple-integration-service-environments-in-azure-logic-apps"></a>Configurare l'accesso per più ambienti del servizio di integrazione in app per la logica di Azure

Quando si lavora con app per la logica di Azure, è possibile configurare un [ *ambiente di Integration Services* ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) per l'hosting di app per la logica che richiedono l'accesso alle risorse in una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md). Se sono presenti più istanze di ISE che richiedono l'accesso ad altri endpoint con restrizioni IP, distribuire un [firewall di Azure](../firewall/overview.md) o un' [appliance di rete virtuale](../virtual-network/virtual-networks-overview.md#filter-network-traffic) nella rete virtuale e indirizzare il traffico in uscita attraverso il firewall o l'appliance virtuale di rete. È quindi possibile fare in modo che tutte le istanze di ISE nella rete virtuale usino un singolo indirizzo IP prevedibile e pubblico per comunicare con i sistemi di destinazione. In questo modo, non è necessario configurare ulteriori aperture del firewall nei sistemi di destinazione per ogni ISE. Questo argomento illustra come instradare il traffico in uscita attraverso un firewall di Azure, ma è possibile applicare concetti simili a un'appliance virtuale di rete virtuale, ad esempio un firewall di terze parti da Azure Marketplace.

## <a name="prerequisites"></a>prerequisiti

* Un firewall di Azure in esecuzione nella stessa rete virtuale di ISE. Se non si dispone di un firewall, aggiungere prima di tutto [una subnet](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) denominata `AzureFirewallSubnet` alla rete virtuale. È quindi possibile [creare e distribuire un firewall](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) nella rete virtuale.

* Tabella di [Route](../virtual-network/manage-route-table.md)di Azure. Se non si dispone di un, [creare innanzitutto una tabella di route](../virtual-network/manage-route-table.md#create-a-route-table). Per ulteriori informazioni sul routing, vedere [routing del traffico di rete virtuale](../virtual-network/virtual-networks-udr-overview.md).

## <a name="set-up-route-table"></a>Configurare la tabella di route

1. Nella [portale di Azure](https://portal.azure.com)selezionare la tabella di route, ad esempio:

   ![Selezionare la tabella di route con la regola per indirizzare il traffico in uscita](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Per [aggiungere una nuova route](../virtual-network/manage-route-table.md#create-a-route), scegliere **Route** > **Aggiungi**dal menu tabella di route.

   ![Aggiungi route per indirizzare il traffico in uscita](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. Nel riquadro **Aggiungi route** [configurare la nuova route](../virtual-network/manage-route-table.md#create-a-route) con una regola che specifica che tutto il traffico in uscita verso il sistema di destinazione segue questo comportamento:

   * Usa l' [**appliance virtuale**](../virtual-network/virtual-networks-udr-overview.md#user-defined) come tipo di hop successivo.

   * Passa all'indirizzo IP privato per l'istanza del firewall come indirizzo hop successivo.

     Per trovare questo indirizzo IP, nel menu del firewall selezionare **Panoramica**, trovare l'indirizzo in **indirizzo IP privato**, ad esempio:

     ![Trovare l'indirizzo IP privato del firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Ecco un esempio che illustra come potrebbe apparire una regola di questo tipo:

   ![Configurare la regola per indirizzare il traffico in uscita](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Proprietà | Valore | DESCRIZIONE |
   |----------|-------|-------------|
   | **Nome route** | <*Unique-route-name*> | Nome univoco per la route nella tabella di route |
   | **Prefisso indirizzo** | <*indirizzo di destinazione*> | Indirizzo del sistema di destinazione in cui si desidera che il traffico passi. Assicurarsi di usare la [notazione CIDR (Inter-Domain Routing) con classe](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) per questo indirizzo. |
   | **Tipo hop successivo** | **Appliance virtuale** | [Tipo di hop](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) usato dal traffico in uscita |
   | **Indirizzo hop successivo** | <*Firewall-Private-IP-address*> | Indirizzo IP privato per il firewall |
   |||

## <a name="set-up-network-rule"></a>Configurare la regola di rete

1. Nella portale di Azure individuare e selezionare il firewall. Nel menu Firewall, in **Impostazioni**, selezionare **regole**. Nel riquadro regole selezionare **raccolta regole di rete** > **Aggiungi raccolta regole di rete**.

   ![Aggiungi raccolta regole di rete al firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. Nella raccolta aggiungere una regola che consenta il traffico verso il sistema di destinazione.

   Si supponga, ad esempio, di avere un'app per la logica che viene eseguita in ISE e che deve comunicare con un sistema SFTP. Si crea una raccolta di regole di rete denominata `LogicApp_ISE_SFTP_Outbound`, che contiene una regola di rete denominata `ISE_SFTP_Outbound`. Questa regola consente il traffico dall'indirizzo IP di qualsiasi subnet in cui ISE viene eseguito nella rete virtuale al sistema SFTP di destinazione usando l'indirizzo IP privato del firewall.

   ![Configurare la regola di rete per il firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Proprietà della raccolta regole di rete**

   | Proprietà | Valore | DESCRIZIONE |
   |----------|-------|-------------|
   | **Nome** | <*Network-Rule-Collection-name*> | Nome della raccolta di regole di rete |
   | **Priorità** | <> *a livello di priorità* | Ordine di priorità da utilizzare per l'esecuzione della raccolta regole. Per altre informazioni, vedere [quali sono alcuni concetti di Azure firewall](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts)? |
   | **Azione** | **Consenti** | Tipo di azione da eseguire per questa regola |
   |||

   **Proprietà della regola di rete**

   | Proprietà | Valore | DESCRIZIONE |
   |----------|-------|-------------|
   | **Nome** | <*Network-rule-name*> | Nome della regola di rete |
   | **Protocollo** | *protocolli di connessione* <> | Protocolli di connessione da utilizzare. Ad esempio, se si usano le regole di NSG, selezionare sia **TCP** che **UDP**, non solo **TCP**. |
   | **Indirizzi di origine** | <*ISE-subnet-addresss*> | Indirizzi IP della subnet in cui viene eseguito ISE e da dove proviene il traffico proveniente dall'app per la logica |
   | **Indirizzi di destinazione** | <*destinazione-IP-address*> | Indirizzo IP del sistema di destinazione in cui si vuole che il traffico vada |
   | **Porte di destinazione** | <*destinazione-porte*> | Tutte le porte utilizzate dal sistema di destinazione per le comunicazioni in ingresso |
   |||

   Per ulteriori informazioni sulle regole di rete, vedere i seguenti articoli:

   * [Configurare una regola di rete](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Logica di elaborazione della regola del firewall di Azure](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Domande frequenti su firewall di Azure](../firewall/firewall-faq.md)
   * [Azure PowerShell: New-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [INTERFACCIA della riga di comando di Azure: AZ Network Firewall Network-Rule](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi alle reti virtuali di Azure da app per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)