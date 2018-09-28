---
title: Filtrare il traffico in ingresso con DNAT di Firewall di Azure tramite il portale di Azure
description: Questa esercitazione mostra come distribuire e configurare DNAT di Firewall di Azure tramite il portale di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 766ad04251fbe404d43734115e41e23ae0a4be28
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982050"
---
# <a name="tutorial-filter-inbound-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Esercitazione: Filtrare il traffico in ingresso con DNAT di Firewall di Azure tramite il portale di Azure

È possibile configurare la modalità DNAT (Destination Network Address Translation) di Firewall di Azure per convertire e filtrare il traffico in ingresso nelle subnet. In Firewall di Azure non esiste il concetto di regole in ingresso e regole in uscita. Sono presenti regole di applicazione e regole di rete che vengono applicate al traffico che entra nel firewall. Le regole di rete vengono applicate per prime, seguite da quelle di applicazione.

>[!NOTE]
>La modalità DNAT di Firewall è attualmente disponibile solo in Azure PowerShell e REST.

Ad esempio, se viene trovata una corrispondenza con una regola di rete, il pacchetto non sarà valutato dalle regole di applicazione. Se non esiste alcuna corrispondenza con una regola di rete e se il protocollo dei pacchetti è HTTP/HTTPS, il pacchetto viene quindi valutato dalle regole di applicazione. Se ancora non viene trovata una corrispondenza, il pacchetto viene valutato in base a una [raccolta di regole dell'infrastruttura](infrastructure-fqdns.md). Se non è ancora presente alcuna corrispondenza, il pacchetto viene rifiutato per impostazione predefinita.

Quando si configura la modalità DNAT, l'azione di raccolta delle regole NAT è impostata su **Destination Network Address Translation (DNAT)**. La porta e l'indirizzo IP pubblico del firewall vengono convertiti in una porta e indirizzo IP privato. Le regole vengono quindi applicate come di consueto, applicando per prime le regole di rete seguite da quelle di applicazione. Ad esempio, è possibile configurare una regola di rete per consentire il traffico di Desktop remoto sulla porta TCP 3389. Per prima cosa ha luogo la conversione degli indirizzi e quindi vengono applicate le regole di rete e di applicazione usando gli indirizzi convertiti.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare un ambiente di rete di test
> * Distribuire un firewall
> * Creare una route predefinita
> * Configurare una regola DNAT
> * Configurare una regola di rete
> * Testare il firewall

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Per questa esercitazione vengono create due reti virtuali con peering:
- **VN-Hub**: in questa rete virtuale si trova il firewall.
- **VN-Spoke**: in questa rete virtuale si trova il server del carico di lavoro.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
1. Accedere al portale di Azure all'indirizzo [http://portal.azure.com](http://portal.azure.com).
1. Nella home page del portale di Azure fare clic su **Gruppi di risorse**, quindi fare clic su **Aggiungi**.
2. In **Nome del gruppo di risorse** digitare **RG-DNAT-Test**.
3. In **Sottoscrizione** selezionare la propria sottoscrizione.
4. In **Località del gruppo di risorse** selezionare una località. Tutte le successive risorse create devono trovarsi nella stessa località.
5. Fare clic su **Create**(Crea).

## <a name="set-up-the-network-environment"></a>Configurare l'ambiente di rete
Creare innanzitutto le reti virtuali e quindi eseguire il peering.

### <a name="create-the-hub-vnet"></a>Creare la rete virtuale dell'hub
1. Nella home page del portale di Azure fare clic su **Tutti i servizi**.
2. In **Rete** fare clic su **Reti virtuali**.
3. Fare clic su **Aggiungi**.
4. In **Nome** digitare **VN-Hub**.
5. In **Spazio degli indirizzi** immettere **10.0.0.0/16**.
7. In **Sottoscrizione** selezionare la propria sottoscrizione.
8. In **Gruppo di risorse** selezionare **Usa esistente** e quindi selezionare **RG-DNAT-Test**.
9. In **Località** selezionare la stessa località usata in precedenza.
10. In **Subnet** immettere **AzureFirewallSubnet** per **Nome**.

     Il firewall si troverà in questa subnet e il nome della subnet **deve** essere AzureFirewallSubnet.
     > [!NOTE]
     > La dimensione minima della subnet AzureFirewallSubnet è /25.
11. In **Intervallo indirizzi** immettere **10.0.1.0/24**.
12. Usare le altre impostazioni predefinite e quindi fare clic su **Crea**.

### <a name="create-a-spoke-vnet"></a>Creare un rete virtuale spoke

1. Nella home page del portale di Azure fare clic su **Tutti i servizi**.
2. In **Rete** fare clic su **Reti virtuali**.
3. Fare clic su **Aggiungi**.
4. In **Nome** digitare **VN-Spoke**.
5. In **Spazio degli indirizzi** digitare **192.168.0.0/16**.
7. In **Sottoscrizione** selezionare la propria sottoscrizione.
8. In **Gruppo di risorse** selezionare **Usa esistente** e quindi selezionare **RG-DNAT-Test**.
9. In **Località** selezionare la stessa località usata in precedenza.
10. In **Subnet**, in **Nome** digitare **SN-Workload**.

    Il server sarà in questa subnet.
1. In **Intervallo indirizzi** digitare **192.168.1.0/24**.
2. Usare le altre impostazioni predefinite e quindi fare clic su **Crea**.

### <a name="peer-the-vnets"></a>Eseguire il peering delle reti virtuali

Ora è il momento di eseguire il peering delle due reti virtuali.

#### <a name="hub-to-spoke"></a>Da hub a spoke

1. Fare clic sulla rete virtuale **VN-Hub**.
2. In **Impostazioni** fare clic su **Peering**.
3. Fare clic su **Aggiungi**.
4. Digitare **Peer-HubSpoke** per il nome.
5. Selezionare **VN-Spoke** per la rete virtuale.
7. Fare clic su **OK**.

#### <a name="spoke-to-hub"></a>Da spoke a hub

1. Fare clic sulla rete virtuale **VN-Spoke**.
2. In **Impostazioni** fare clic su **Peering**.
3. Fare clic su **Aggiungi**.
4. Digitare **Peer-SpokeHub** per il nome.
5. Selezionare **VN-Hub** per la rete virtuale.
6. Fare clic su **Consenti traffico inoltrato**.
7. Fare clic su **OK**.

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una macchina virtuale del carico di lavoro e inserirla nella subnet **SN-Workload**.

1. Nella home page del portale di Azure fare clic su **Tutti i servizi**.
2. In **Calcolo** fare clic su **Macchine virtuali**.
3. Fare clic su **Aggiungi**, su **Windows Server**, su **Windows Server 2016 Datacenter** e quindi fare clic su **Crea**.

**Nozioni di base**

1. In **Nome** digitare **Srv-Workload**.
5. Immettere un nome utente e la password.
6. In **Sottoscrizione** selezionare la propria sottoscrizione.
7. In **Gruppo di risorse** fare clic su **Usa esistente** e quindi selezionare **RG-DNAT-Test**.
8. In **Località** selezionare la stessa località usata in precedenza.
9. Fare clic su **OK**.

**Dimensione**

1. Scegliere le dimensioni appropriate per una macchina virtuale di test che esegue Windows Server. Ad esempio, **B2ms** (8 GB di RAM, 16 GB di archiviazione).
2. Fare clic su **Seleziona**.

**Impostazioni**

1. In **Rete** selezionare **VN-Spoke** per **Rete virtuale**.
2. In **Subnet** selezionare **SN-Workload**.
3. Fare clic su **Indirizzo IP pubblico** e quindi su **Nessuno**.
4. In **Selezionare le porte in ingresso pubbliche** selezionare **Nessuna porta in ingresso pubblica**. 
2. Lasciare le altre impostazioni predefinite e fare clic su **OK**.

**Summary**

Controllare il riepilogo e quindi fare clic su **Crea**. Questa operazione richiederà qualche minuto.

Al termine della distribuzione, prendere nota dell'indirizzo IP privato della macchina virtuale. Sarà necessario più avanti per la configurazione del firewall. Fare clic sul nome della macchina virtuale e in **Impostazioni** fare clic su **Rete** per trovare l'indirizzo IP privato.


## <a name="deploy-the-firewall"></a>Distribuire il firewall

1. Nella home page del portale fare clic su **Crea una risorsa**.
2. Fare clic su **Rete** e su **In primo piano** e quindi fare clic su **Visualizza tutto**.
3. Fare clic su **Firewall** e quindi su **Crea**. 
4. Nella pagina **Crea un firewall** usare la tabella seguente per configurare il firewall:
   
   |Impostazione  |Valore  |
   |---------|---------|
   |NOME     |FW-DNAT-test|
   |Sottoscrizione     |\<sottoscrizione in uso\>|
   |Gruppo di risorse     |**Usa esistente**: RG-DNAT-Test |
   |Località     |Selezionare la stessa località usata in precedenza|
   |Scegliere una rete virtuale     |**Usa esistente**: VN-Hub|
   |Indirizzo IP pubblico     |**Creare un nuovo gruppo di risorse**. L'indirizzo IP pubblico deve essere di tipo SKU Standard.|

2. Fare clic su **Rivedi e crea**.
3. Controllare il riepilogo e quindi fare clic su **Crea** per creare il firewall.

   La distribuzione richiederà qualche minuto.
4. Al termine della distribuzione, passare al gruppo di risorse **RG-DNAT-Test** e fare clic sul firewall **FW-DNAT-test**.
6. Annotare l'indirizzo IP privato. Sarà necessario più avanti per la creazione della route predefinita.


## <a name="create-a-default-route"></a>Creare una route predefinita

Per la subnet **SN-Workload** configurare la route predefinita in uscita per passare attraverso il firewall.

1. Nella home page del portale di Azure fare clic su **Tutti i servizi**.
2. In **Rete** fare clic su **Tabelle route**.
3. Fare clic su **Aggiungi**.
4. In **Nome** digitare **RT-FWroute**.
5. In **Sottoscrizione** selezionare la propria sottoscrizione.
6. In **Gruppo di risorse** selezionare **Usa esistente** e selezionare **RG-DNAT-Test**.
7. In **Località** selezionare la stessa località usata in precedenza.
8. Fare clic su **Create**(Crea).
9. Fare clic su **Aggiorna** e quindi fare clic sulla tabella di route **RT-FWroute**.
10. Fare clic su **Subnet** e quindi su **Associa**.
11. Fare clic su **Rete virtuale** e quindi selezionare **VN-Spoke**.
12. In **Subnet** fare clic su **SN-Workload**.
13. Fare clic su **OK**.
14. Fare clic su **Route** e quindi su **Aggiungi**.
15. In **Nome route** immettere **FW-DG**.
16. In **Prefisso indirizzo** immettere **0.0.0.0/0**.
17. In **Tipo hop successivo** selezionare **Appliance virtuale**.

    Firewall di Azure è in effetti un servizio gestito, ma in questa situazione è possibile usare un'appliance virtuale.
1. In **Indirizzo hop successivo** immettere l'indirizzo IP privato per il firewall annotato in precedenza.
2. Fare clic su **OK**.


## <a name="configure-a-dnat-rule"></a>Configurare una regola DNAT

```azurepowershell-interactive
 $rgName  = "RG-DNAT-Test"
 $firewallName = "FW-DNAT-test"
 $publicip = type the Firewall public ip
 $newAddress = type the private IP address for the Srv-Workload virtual machine 
 
# Get Firewall
    $firewall = Get-AzureRmFirewall -ResourceGroupName $rgName -Name $firewallName
  # Create NAT rule
    $natRule = New-AzureRmFirewallNatRule -Name RL-01 -SourceAddress * -DestinationAddress $publicip -DestinationPort 3389 -Protocol TCP -TranslatedAddress $newAddress -TranslatedPort 3389
  # Create NAT rule collection
    $natRuleCollection = New-AzureRmFirewallNatRuleCollection -Name RC-DNAT-01 -Priority 200 -Rule $natRule
  # Add NAT Rule collection to firewall:
    $firewall.AddNatRuleCollection($natRuleCollection)
  # Save:
    $firewall | Set-AzureRmFirewall
```
## <a name="configure-a-network-rule"></a>Configurare una regola di rete

1. Aprire **RG-DNAT-Test** e fare clic sul firewall **FW-DNAT-test**.
1. Nella pagina **FW-DNAT-test** fare clic su **Regole** in **Impostazioni**.
2. Fare clic su **Aggiungi raccolta regole di rete**.

Configurare la regola usando la tabella seguente e quindi fare clic su **Aggiungi**:


|Parametro  |Valore  |
|---------|---------|
|NOME     |**RC-Net-01**|
|Priorità     |**200**|
|Azione     |**Consenti**|

In **Regole**:

|Parametro  |Impostazione  |
|---------|---------|
|NOME     |**RL-RDP**|
|Protocollo     |**TCP**|
|Indirizzi di origine     |*|
|Indirizzi di destinazione     |Indirizzo IP privato **Srv-Workload**|
|Porte di destinazione|**3389**|


## <a name="test-the-firewall"></a>Testare il firewall

1. Connettere un desktop remoto all'indirizzo IP pubblico del firewall. Dovrebbe essere stabilita una connessione alla macchina virtuale **Srv-Workload**.
3. Chiudere il desktop remoto.
4. Modificare l'azione della raccolta di regole di rete **RC-Net-01** impostandola su **Nega**.
5. Riprovare a connettersi all'indirizzo IP pubblico del firewall. Questa volta l'operazione dovrebbe avere esito negativo a causa della regola **Nega**.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile conservare le risorse del firewall per l'esercitazione successiva oppure, se non è più necessario, eliminare il gruppo di risorse **RG-DNAT-Test** per eliminare tutte le risorse correlate al firewall.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Configurare un ambiente di rete di test
> * Distribuire un firewall
> * Creare una route predefinita
> * Configurare una regola DNAT
> * Configurare una regola di rete
> * Testare il firewall

È possibile ora monitorare i log di Firewall di Azure.

> [!div class="nextstepaction"]
> [Esercitazione: Monitorare i log di Firewall di Azure](./tutorial-diagnostics.md)
