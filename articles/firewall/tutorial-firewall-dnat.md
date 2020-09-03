---
title: 'Esercitazione: Filtrare il traffico Internet in ingresso con DNAT di Firewall di Azure tramite il portale'
description: Questa esercitazione mostra come distribuire e configurare DNAT di Firewall di Azure tramite il portale di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 8f528c6be68258400cb3e29582943f1d657c557d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89069273"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Esercitazione: Filtrare il traffico Internet in ingresso con DNAT di Firewall di Azure tramite il portale di Azure

È possibile configurare la modalità DNAT (Destination Network Address Translation) di Firewall di Azure per convertire e filtrare il traffico Internet in ingresso nelle subnet. Quando si configura la modalità DNAT, l'azione di raccolta delle regole NAT è impostata su **Dnat**. Ogni regola nella raccolta regole NAT può quindi essere usata per convertire la porta e l'indirizzo IP pubblici del firewall in porta e indirizzo IP privati. Le regole DNAT aggiungono in modo implicito una regola di rete corrispondente per consentire il traffico convertito. È possibile sostituire questo comportamento aggiungendo in modo esplicito una raccolta regole di rete con regole di negazione corrispondenti al traffico convertito. Per altre informazioni sulla logica di elaborazione delle regole di Firewall di Azure, vedere [Azure Firewall rule processing logic](rule-processing.md) (Logica di elaborazione delle regole di Firewall di Azure).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare un ambiente di rete di test
> * Distribuire un firewall
> * Creare una route predefinita
> * Configurare una regola DNAT
> * Testare il firewall

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.



## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Nella home page del portale di Azure selezionare **Gruppi di risorse** e quindi selezionare **Aggiungi**.
3. In **Nome del gruppo di risorse** digitare **RG-DNAT-Test**.
4. In **Sottoscrizione** selezionare la propria sottoscrizione.
5. In **Località del gruppo di risorse** selezionare una località. Tutte le successive risorse create devono trovarsi nella stessa località.
6. Selezionare **Create** (Crea).

## <a name="set-up-the-network-environment"></a>Configurare l'ambiente di rete

Per questa esercitazione vengono create due reti virtuali con peering:

- **VN-Hub**: in questa rete virtuale si trova il firewall.
- **VN-Spoke**: in questa rete virtuale si trova il server del carico di lavoro.

Creare innanzitutto le reti virtuali e quindi eseguire il peering.

### <a name="create-the-hub-vnet"></a>Creare la rete virtuale dell'hub

1. Nella home page del portale di Azure selezionare **Tutti i servizi**.
2. In **Rete** selezionare **Reti virtuali**.
3. Selezionare **Aggiungi**.
4. In **Nome** digitare **VN-Hub**.
5. In **Spazio degli indirizzi** immettere **10.0.0.0/16**.
6. In **Sottoscrizione** selezionare la propria sottoscrizione.
7. In **Gruppo di risorse** selezionare **Usa esistente** e quindi selezionare **RG-DNAT-Test**.
8. In **Località** selezionare la stessa località usata in precedenza.
9. In **Subnet** immettere **AzureFirewallSubnet** per **Nome**.

     Il firewall si troverà in questa subnet e il nome della subnet **deve** essere AzureFirewallSubnet.
     > [!NOTE]
     > La dimensione della subnet AzureFirewallSubnet è /26. Per altre informazioni sulle dimensioni delle subnet, vedere le [domande frequenti su Firewall di Azure](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

10. In **Intervallo indirizzi** digitare **10.0.1.0/26**.
11. Usare le altre impostazioni predefinite e quindi selezionare **Crea**.

### <a name="create-a-spoke-vnet"></a>Creare un rete virtuale spoke

1. Nella home page del portale di Azure selezionare **Tutti i servizi**.
2. In **Rete** selezionare **Reti virtuali**.
3. Selezionare **Aggiungi**.
4. In **Nome** digitare **VN-Spoke**.
5. In **Spazio degli indirizzi** digitare **192.168.0.0/16**.
6. In **Sottoscrizione** selezionare la propria sottoscrizione.
7. In **Gruppo di risorse** selezionare **Usa esistente** e quindi selezionare **RG-DNAT-Test**.
8. In **Località** selezionare la stessa località usata in precedenza.
9. In **Subnet**, in **Nome** digitare **SN-Workload**.

    Il server sarà in questa subnet.
10. In **Intervallo indirizzi** digitare **192.168.1.0/24**.
11. Usare le altre impostazioni predefinite e quindi selezionare **Crea**.

### <a name="peer-the-vnets"></a>Eseguire il peering delle reti virtuali

Ora è il momento di eseguire il peering delle due reti virtuali.

1. Selezionare la rete virtuale **VN-Hub**.
2. In **Impostazioni** selezionare **Peer**.
3. Selezionare **Aggiungi**.
4. Digitare **Peer-HubSpoke** per il **nome del peering da VN-Hub a VN-Spoke**.
5. Selezionare **VN-Spoke** per la rete virtuale.
6. Digitare **Peer-SpokeHub** per il **nome del peering da VN-Spoke a VN-Hub**.
7. In **Consenti il traffico inoltrato da VN-Spoke a VN-Hub** selezionare **Abilitato**.
8. Selezionare **OK**.

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una macchina virtuale del carico di lavoro e inserirla nella subnet **SN-Workload**.

1. Nel menu del portale di Azure selezionare **Crea una risorsa**.
2. In **Più comuni** selezionare **Windows Server 2016 Datacenter**.

**Nozioni di base**

1. In **Sottoscrizione** selezionare la propria sottoscrizione.
1. In **Gruppo di risorse** selezionare **Usa esistente** e quindi selezionare **RG-DNAT-Test**.
1. In **Nome macchina virtuale** digitare **Srv-Workload**.
1. In **Area** selezionare la stessa località usata in precedenza.
1. Immettere un nome utente e la password.
1. Selezionare **Avanti: Dischi**.

**Dischi**
1. Selezionare **Avanti: Rete**.

**Rete**

1. In **Rete virtuale** selezionare **VN-Spoke**.
2. In **Subnet** selezionare **SN-Workload**.
3. In **Indirizzo IP pubblico** selezionare **Nessuno**.
4. In **Porte in ingresso pubbliche** selezionare **Nessuna**. 
2. Lasciare le altre impostazioni predefinite e selezionare **Avanti: Gestione**.

**Gestione**

1. In **Diagnostica di avvio** selezionare **Disattivato**.
1. Selezionare **Rivedi e crea**.

**Rivedi e crea**

Esaminare il riepilogo e quindi selezionare **Crea**. Questa operazione richiederà qualche minuto.

Al termine della distribuzione, prendere nota dell'indirizzo IP privato della macchina virtuale. Sarà necessario più avanti per la configurazione del firewall. Selezionare il nome della macchina virtuale e in **Impostazioni** selezionare **Rete** per trovare l'indirizzo IP privato.

## <a name="deploy-the-firewall"></a>Distribuire il firewall

1. Dalla home page del portale selezionare **Crea una risorsa**.
2. Selezionare **Rete** e **In primo piano** e quindi selezionare **Visualizza tutto**.
3. Selezionare **Firewall** e quindi **Crea**. 
4. Nella pagina **Crea un firewall** usare la tabella seguente per configurare il firewall:

   |Impostazione  |valore  |
   |---------|---------|
   |Nome     |FW-DNAT-test|
   |Subscription     |\<your subscription\>|
   |Resource group     |**Usa esistente**: RG-DNAT-Test |
   |Location     |Selezionare la stessa località usata in precedenza|
   |Scegliere una rete virtuale     |**Usa esistente**: VN-Hub|
   |Indirizzo IP pubblico     |**Creare un nuovo gruppo di risorse**. L'indirizzo IP pubblico deve essere di tipo SKU Standard.|

5. Selezionare **Rivedi e crea**.
6. Controllare il riepilogo e quindi selezionare **Crea** per creare il firewall.

   La distribuzione richiederà qualche minuto.
7. Al termine della distribuzione, passare al gruppo di risorse **RG-DNAT-Test** e selezionare il firewall **FW-DNAT-test**.
8. Annotare l'indirizzo IP privato. Sarà necessario più avanti per la creazione della route predefinita.

## <a name="create-a-default-route"></a>Creare una route predefinita

Per la subnet **SN-Workload** configurare la route predefinita in uscita per passare attraverso il firewall.

1. Nella home page del portale di Azure selezionare **Tutti i servizi**.
2. In **Rete** selezionare **Tabelle route**.
3. Selezionare **Aggiungi**.
4. In **Nome** digitare **RT-FWroute**.
5. In **Sottoscrizione** selezionare la propria sottoscrizione.
6. In **Gruppo di risorse** selezionare **Usa esistente** e selezionare **RG-DNAT-Test**.
7. In **Località** selezionare la stessa località usata in precedenza.
8. Selezionare **Crea**.
9. Selezionare **Aggiorna** e quindi selezionare la tabella di route **RT-FWroute**.
10. Selezionare **Subnet** e quindi **Associa**.
11. Selezionare **Rete virtuale** e quindi selezionare **VN-Spoke**.
12. In **Subnet** selezionare **SN-Workload**.
13. Selezionare **OK**.
14. Selezionare **Route** e quindi **Aggiungi**.
15. In **Nome route** immettere **FW-DG**.
16. In **Prefisso indirizzo** immettere **0.0.0.0/0**.
17. In **Tipo hop successivo** selezionare **Appliance virtuale**.

    Firewall di Azure è in effetti un servizio gestito, ma in questa situazione è possibile usare un'appliance virtuale.
18. In **Indirizzo hop successivo** immettere l'indirizzo IP privato per il firewall annotato in precedenza.
19. Selezionare **OK**.

## <a name="configure-a-nat-rule"></a>Configurare una regola NAT

1. Aprire **RG-DNAT-Test** e selezionare il firewall **FW-DNAT-test**. 
2. Nella pagina **FW-DNAT-test** selezionare **Regole** in **Impostazioni**. 
3. Selezionare **Aggiungi raccolta regole NAT**. 
4. Per **Nome** digitare **RC-DNAT-01**. 
5. In **Priorità** immettere **200**. 
6. In **Regole** per **Nome** digitare **RL-01**.
7. In **Protocollo** selezionare **TCP**.
8. Per **Indirizzi di origine** digitare *. 
9. Per **Indirizzi di destinazione** digitare l'indirizzo IP pubblico del firewall. 
10. Per **Porte di destinazione** digitare **3389**. 
11. Per **Indirizzo convertito** digitare l'indirizzo IP privato per la macchina virtuale Srv-Workload. 
12. Per **Porta tradotta** digitare **3389**. 
13. Selezionare **Aggiungi**. 

## <a name="test-the-firewall"></a>Testare il firewall

1. Connettere un desktop remoto all'indirizzo IP pubblico del firewall. Dovrebbe essere stabilita una connessione alla macchina virtuale **Srv-Workload**.
2. Chiudere il desktop remoto.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile conservare le risorse del firewall per l'esercitazione successiva oppure, se non è più necessario, eliminare il gruppo di risorse **RG-DNAT-Test** per eliminare tutte le risorse correlate al firewall.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Configurare un ambiente di rete di test
> * Distribuire un firewall
> * Creare una route predefinita
> * Configurare una regola DNAT
> * Testare il firewall

È possibile ora monitorare i log di Firewall di Azure.

> [!div class="nextstepaction"]
> [Esercitazione: monitorare i log del Firewall di Azure](./tutorial-diagnostics.md)
