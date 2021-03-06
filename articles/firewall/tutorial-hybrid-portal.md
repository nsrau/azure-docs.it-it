---
title: 'Esercitazione: Distribuire e configurare Firewall di Azure in una rete ibrida con il portale di Azure'
description: Questa esercitazione mostra come distribuire e configurare Firewall di Azure con il portale di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 11/17/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 78e934a90b8d4e8feccf18a5cada3ec4920e1642
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94734454"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-the-azure-portal"></a>Esercitazione: Distribuire e configurare Firewall di Azure in una rete ibrida con il portale di Azure

Quando si connette la rete locale a una rete virtuale di Azure per creare una rete ibrida, la possibilità di controllare l'accesso alle risorse di rete di Azure è una parte importante di un piano di sicurezza complessivo.

È possibile usare il Firewall di Azure per controllare l'accesso alla rete in una rete ibrida usando le regole che definiscono il traffico di rete consentito e negato.

Per questa esercitazione vengono create tre reti virtuali:

- **VNet-Hub**: in questa rete virtuale si trova la rete virtuale.
- **VNet-Spoke**: la rete virtuale spoke rappresenta il carico di lavoro che si trova in Azure.
- **VNet-Onprem**: rappresenta una rete locale. In una distribuzione reale la connessione può essere effettuata tramite una connessione VPN o ExpressRoute. Per semplicità, questa esercitazione usa una connessione gateway VPN e per rappresentare una rete locale viene usata una rete virtuale ubicata in Azure.

![Firewall in una rete ibrida](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Dichiarare le variabili
> * Creare la rete virtuale dell'hub del firewall
> * Creare la rete virtuale spoke
> * Creare la rete virtuale locale
> * Configurare e distribuire il firewall
> * Creare e connettere i gateway VPN
> * Eseguire il peering tra le reti virtuali dell'hub e spoke
> * Creare le route
> * Creare le macchine virtuali
> * Testare il firewall

Se si vuole completare la procedura con Azure PowerShell, vedere [Distribuire e configurare Firewall di Azure in una rete ibrida con Azure PowerShell](tutorial-hybrid-ps.md).

## <a name="prerequisites"></a>Prerequisiti

Una rete ibrida usa il modello di architettura hub-spoke per instradare il traffico tra le reti virtuali di Azure e le reti locali. L'architettura hub-spoke presenta i requisiti seguenti:

- Impostare **AllowGatewayTransit** quando si esegue il peering dell'hub di rete virtuale con lo spoke di rete virtuale. Nell'architettura di rete hub-spoke il transito tramite gateway consente alle reti virtuali spoke di condividere il gateway VPN nell'hub invece di distribuire gateway VPN in ogni rete virtuale spoke. 

   Inoltre, le route alle reti virtuali connesse al gateway o alle reti locali si propagheranno automaticamente alle tabelle di routing per le reti virtuali con peering che usano il transito tramite gateway. Per altre informazioni, vedere [Configurare il transito tramite gateway VPN per il peering di reti virtuali](../vpn-gateway/vpn-gateway-peering-gateway-transit.md).

- Impostare **UseRemoteGateways** quando si esegue il peering dello spoke di rete virtuale con l'hub di rete virtuale. Se è impostata l'opzione **UseRemoteGateways** ed è impostata anche l'opzione **AllowGatewayTransit** nel peering remoto, la rete virtuale spoke usa i gateway della rete virtuale remota per il transito.
- Per instradare il traffico della subnet spoke attraverso il firewall dell'hub, è possibile usare una route definita dall'utente che punti al firewall con l'opzione **Propagazione route del gateway di rete virtuale** disabilitata. L'opzione **Propagazione route del gateway di rete virtuale** disabilitata impedisce la distribuzione delle route alle subnet spoke. Evita anche che le route apprese entrino in conflitto con la route definita dall'utente. Per mantenere abilitata l'opzione **Propagazione route del gateway di rete virtuale**, assicurarsi di definire route specifiche per il firewall per sostituire quelle pubblicate in locale su BGP.
- Configurare una route definita dall'utente nella subnet del gateway dell'hub che punti all'indirizzo IP del firewall come hop successivo per le reti spoke. Non è richiesta alcuna route definita dall'utente nella subnet di Firewall di Azure, dal momento che le route vengono apprese dal protocollo BGP.

Vedere la sezione [Creare route](#create-the-routes) in questa esercitazione per vedere come vengono create le route.

>[!NOTE]
>Connettività diretta al Firewall di Azure. Se AzureFirewallSubnet apprende una route predefinita alla rete locale tramite BGP è necessario sostituirla con una route UDR 0.0.0.0/0 con il valore **NextHopType** impostato come **Internet** per mantenere connettività diretta a Internet.
>
>È possibile configurare Firewall di Azure per supportare il tunneling forzato. Per altre informazioni, vedere [Tunneling forzato di Firewall di Azure](forced-tunneling.md).

>[!NOTE]
>Il traffico tra reti virtuali direttamente con peering viene instradato direttamente anche se una route definita dall'utente punta al firewall di Azure come gateway predefinito. Per inviare il traffico da subnet a subnet al firewall in questo scenario, una route definita dall'utente deve contenere il prefisso di rete subnet di destinazione in modo esplicito su entrambe le subnet.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-the-firewall-hub-virtual-network"></a>Creare la rete virtuale dell'hub del firewall

Prima di tutto, creare un gruppo di risorse per contenere tutte le risorse per questa esercitazione:

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Nella home page del portale di Azure selezionare **Gruppi di risorse** > **Aggiungi**.
3. In **Sottoscrizione** selezionare la propria sottoscrizione.
1. In **Nome gruppo di risorse** digitare **FW-Hybrid-Test**.
2. In **Area** selezionare **(Stati Uniti) Stati Uniti orientali**. Tutte le risorse create in seguito devono trovarsi nella stessa località.
3. Selezionare **Rivedi e crea**.
4. Selezionare **Crea**.

A questo punto, creare la rete virtuale:

> [!NOTE]
> La dimensione della subnet AzureFirewallSubnet è /26. Per altre informazioni sulle dimensioni delle subnet, vedere le [domande frequenti su Firewall di Azure](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. In **Rete** selezionare **Rete virtuale**.
1. Selezionare **Create** (Crea).
1. In **Gruppo di risorse** selezionare **FW-Hybrid-Test**.
1. In **Nome** digitare **VNet-hub**.
1. Selezionare **Avanti: Indirizzi IP**.
1. Per **Spazio indirizzi IPv4**, eliminare l'indirizzo predefinito e digitare **10.5.0.0/16**.
1. In **Nome subnet** selezionare **Aggiungi subnet**.
1. Per **Nome subnet** immettere **AzureFirewallSubnet**. Il firewall si troverà in questa subnet e il nome della subnet **deve** essere AzureFirewallSubnet.
1. Per **Intervallo di indirizzi subnet**, digitare **10.5.0.0/26**. 
1. Selezionare **Aggiungi**.
1. Selezionare **Rivedi e crea**.
1. Selezionare **Create** (Crea).

## <a name="create-the-spoke-virtual-network"></a>Creare la rete virtuale spoke

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. In **Rete** selezionare **Rete virtuale**.
7. In **Gruppo di risorse** selezionare **FW-Hybrid-Test**.
1. In **Nome** digitare **VNet-Spoke**.
2. In **Area** selezionare **(Stati Uniti) Stati Uniti orientali**.
3. Selezionare **Avanti: Indirizzi IP**.
4. Per **Spazio indirizzi IPv4**, eliminare l'indirizzo predefinito e digitare **10.6.0.0/16**.
6. In **Nome subnet** selezionare **Aggiungi subnet**.
7. Per **Nome subnet**, digitare **SN-Workload**.
8. Per **Intervallo di indirizzi subnet**, digitare **10.6.0.0/24**. 
9. Selezionare **Aggiungi**.
10. Selezionare **Rivedi e crea**.
11. Selezionare **Create** (Crea).

## <a name="create-the-on-premises-virtual-network"></a>Creare la rete virtuale locale

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. In **Rete** selezionare **Rete virtuale**.
7. In **Gruppo di risorse** selezionare **FW-Hybrid-Test**.
1. In **Nome** digitare **VNet-OnPrem**.
2. In **Area** selezionare **(Stati Uniti) Stati Uniti orientali**.
3. Selezionare **Avanti: Indirizzi IP**
4. Per **Spazio indirizzi IPv4**, eliminare l'indirizzo predefinito e digitare **192.168.0.0/16**.
5. In **Nome subnet** selezionare **Aggiungi subnet**.
7. Per **Nome subnet**, digitare **SN-Corp**.
8. Per **Intervallo di indirizzi subnet**, digitare **192.168.1.0/24**. 
9. Selezionare **Aggiungi**.
10. Selezionare **Rivedi e crea**.
11. Selezionare **Create** (Crea).

A questo punto creare una seconda subnet per il gateway.

1. Nella pagina **VNet-Onprem** selezionare **Subnet**.
2. Selezionare **+Subnet**.
3. In **Nome** digitare **GatewaySubnet**.
4. Per **Intervallo di indirizzi subnet** digitare **192.168.2.0/24**.
5. Selezionare **OK**.

## <a name="configure-and-deploy-the-firewall"></a>Configurare e distribuire il firewall

A questo punto distribuire il firewall nella rete virtuale dell'hub del firewall.

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. Nella colonna di sinistra selezionare **Rete**, quindi cercare e selezionare **Firewall**.
4. Nella pagina **Crea un firewall** usare la tabella seguente per configurare il firewall:

   |Impostazione  |valore  |
   |---------|---------|
   |Subscription     |\<your subscription\>|
   |Resource group     |**FW-Hybrid-Test** |
   |Nome     |**AzFW01**|
   |Area     |**Stati Uniti orientali**|
   |Scegliere una rete virtuale     |**Use existing** (Usa esistente):<br> **VNet-hub**|
   |Indirizzo IP pubblico     |Aggiungi nuovo: <br>**fw-pip**. |

5. Selezionare **Rivedi e crea**.
6. Controllare il riepilogo e quindi selezionare **Crea** per creare il firewall.

   La distribuzione richiede alcuni minuti.
7. Al termine della distribuzione, passare al gruppo di risorse **FW-Hybrid-Test** e selezionare il firewall **AzFW01**.
8. Annotare l'indirizzo IP privato. Sarà necessario più avanti per la creazione della route predefinita.

### <a name="configure-network-rules"></a>Configurare le regole di rete

Aggiungere innanzitutto una regola di rete per consentire il traffico Web.

1. Nella pagina **AzFW01** selezionare **Regole**.
2. Selezionare la scheda **Raccolta regole di rete**.
3. Selezionare **Aggiungi raccolta regole di rete**.
4. In **Nome** digitare **RCNet01**.
5. In **Priorità** digitare **100**.
6. In **Azione** selezionare **Consenti**.
6. In **Regole** digitare **AllowWeb** in **Nome**.
7. In **Protocollo** selezionare **TCP**.
8. In **Tipo di origine** selezionare **Indirizzo IP**.
9. In **Origine** digitare **192.168.1.0/24**.
10. Per **Tipo di destinazione** selezionare **Indirizzo IP**.
11. In **Indirizzo di destinazione** digitare **10.6.0.0/16**
12. In **Porte di destinazione** digitare **80**.

A questo punto aggiungere una regola per consentire il traffico RDP.

Nella seconda riga della regola digitare le informazioni seguenti:

1. In **Nome** digitare **AllowRDP**.
2. In **Protocollo** selezionare **TCP**.
3. In **Tipo di origine** selezionare **Indirizzo IP**.
4. In **Origine** digitare **192.168.1.0/24**.
5. Per **Tipo di destinazione** selezionare **Indirizzo IP**.
6. In **Indirizzo di destinazione** digitare **10.6.0.0/16**
7. In **Porte di destinazione** digitare **3389**.
8. Selezionare **Aggiungi**.

## <a name="create-and-connect-the-vpn-gateways"></a>Creare e connettere i gateway VPN

Le reti virtuali dell'hub e locale sono connesse tramite gateway VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Creare un gateway VPN per la rete virtuale dell'hub

Creare ora un gateway VPN per la rete virtuale dell'hub. Le configurazioni da rete a rete richiedono un tipo di VpnType RouteBased. La creazione di un gateway VPN spesso richiede anche più di 45 minuti, a seconda della SKU del gateway VPN selezionata.

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. Nella casella di testo di ricerca digitare **gateway di rete virtuale**.
3. Selezionare **Gateway di rete virtuale** e selezionare **Crea**.
4. In **Nome** digitare **GW-hub**.
5. In **Area** selezionare la stessa area usata in precedenza.
6. In **Tipo di gateway** selezionare **VPN**.
7. In **Tipo VPN** selezionare **Basato su route**.
8. In **SKU** selezionare **Basic**.
9. In **Rete virtuale** selezionare **VNet-hub**.
10. In **Indirizzo IP pubblico** selezionare **Crea nuovo** e digitare **VNet-hub-GW-pip** per il nome.
11. Accettare tutte le altre impostazioni predefinite e quindi selezionare **Rivedi e crea**.
12. Esaminare la configurazione e quindi selezionare **Crea**.

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Creare un gateway VPN per la rete virtuale locale

Creare ora un gateway VPN per la rete virtuale locale. Le configurazioni da rete a rete richiedono un tipo di VpnType RouteBased. La creazione di un gateway VPN spesso richiede anche più di 45 minuti, a seconda della SKU del gateway VPN selezionata.

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. Nella casella di testo di ricerca digitare **gateway di rete virtuale** e premere **INVIO**.
3. Selezionare **Gateway di rete virtuale** e selezionare **Crea**.
4. In **Nome** digitare **GW-Onprem**.
5. In **Area** selezionare la stessa area usata in precedenza.
6. In **Tipo di gateway** selezionare **VPN**.
7. In **Tipo VPN** selezionare **Basato su route**.
8. In **SKU** selezionare **Basic**.
9. In **Rete virtuale**, selezionare **VNet-Onprem**.
10. In **Indirizzo IP pubblico** selezionare **Crea nuovo** e digitare **VNet-Onprem-GW-pip** per il nome.
11. Accettare tutte le altre impostazioni predefinite e quindi selezionare **Rivedi e crea**.
12. Esaminare la configurazione e quindi selezionare **Crea**.

### <a name="create-the-vpn-connections"></a>Creare le connessioni VPN

A questo punto è possibile creare le connessioni VPN tra gateway locali e hub.

In questo passaggio si crea la connessione dalla rete virtuale dell'hub alla rete virtuale locale. Verrà visualizzata una chiave condivisa a cui si fa riferimento negli esempi. È possibile utilizzare i propri valori specifici per la chiave condivisa. L'importante è che la chiave condivisa corrisponda per entrambe le configurazioni. Il completamento della creazione di una connessione può richiedere un po' di tempo.

1. Aprire il gruppo di risorse **FW-Hybrid-Test** e selezionare il gateway **GW-hub**.
2. Selezionare **Connessioni** nella colonna di sinistra.
3. Selezionare **Aggiungi**.
4. Per il nome della connessione digitare **Hub-to-Onprem**.
5. Selezionare **Da rete virtuale a rete virtuale** in **Tipo di connessione**.
6. In **Secondo gateway di rete virtuale** selezionare **GW-Onprem**.
7. In **Chiave condivisa (PSK)** digitare **AzureA1b2C3**.
8. Selezionare **OK**.

Creare la connessione dalla rete virtuale locale alla rete virtuale dell'hub. Questo passaggio è simile a quello precedente, con la differenza che viene creata la connessione da VNet-Onprem a VNet-hub. Assicurarsi che le chiavi condivise corrispondano. Dopo alcuni minuti la connessione verrà stabilita.

1. Aprire il gruppo di risorse **FW-Hybrid-Test** e selezionare il gateway **GW-Onprem**.
2. Selezionare **Connessioni** nella colonna di sinistra.
3. Selezionare **Aggiungi**.
4. Per il nome della connessione digitare **Onprem-to-Hub**.
5. Selezionare **Da rete virtuale a rete virtuale** in **Tipo di connessione**.
6. In **Secondo gateway di rete virtuale** selezionare **GW-hub**.
7. In **Chiave condivisa (PSK)** digitare **AzureA1b2C3**.
8. Selezionare **OK**.


#### <a name="verify-the-connection"></a>Verificare la connessione

Dopo circa cinque minuti lo stato di entrambe le connessioni dovrebbe essere **Connesso**.

![Connessioni del gateway](media/tutorial-hybrid-portal/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Eseguire il peering tra le reti virtuali dell'hub e spoke

Eseguire ora il peering tra le reti virtuali dell'hub e spoke.

1. Aprire il gruppo di risorse **FW-Hybrid-Test** e selezionare la rete virtuale **VNet-hub**.
2. Nella colonna di sinistra selezionare **Peering**.
3. Selezionare **Aggiungi**.
4. In **Questa rete virtuale**:
 
   
   |Nome impostazione  |valore  |
   |---------|---------|
   |Nome del collegamento di peering| HubtoSpoke|
   |Traffico verso la rete virtuale remota|   Consenti (impostazione predefinita)      |
   |Traffico inoltrato dalla rete virtuale remota    |   Consenti (impostazione predefinita)      |
   |Gateway di rete virtuale     |  Usa il gateway di questa rete virtuale       |
    
5. In **Rete virtuale remota**:

   |Nome impostazione  |valore  |
   |---------|---------|
   |Nome del collegamento di peering | SpoketoHub|
   |Modello di distribuzione della rete virtuale| Resource Manager|
   |Subscription|\<your subscription\>|
   |Rete virtuale| VNet-Spoke
   |Traffico verso la rete virtuale remota     |   Consenti (impostazione predefinita)      |
   |Traffico inoltrato dalla rete virtuale remota    |   Consenti (impostazione predefinita)      |
   |Gateway di rete virtuale     |  Usa il gateway della rete virtuale remota       |

5. Selezionare **Aggiungi**.

   :::image type="content" source="media/tutorial-hybrid-portal/firewall-peering.png" alt-text="Peering di rete virtuale":::

## <a name="create-the-routes"></a>Creare le route

Creare quindi due route:

- Una route dalla subnet del gateway dell'hub alla subnet spoke attraverso l'indirizzo IP del firewall
- Una route predefinita dalla subnet spoke attraverso l'indirizzo IP del firewall

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. Nella casella di testo di ricerca digitare **tabella di route** e premere **INVIO**.
3. Selezionare **Tabella di route**.
4. Selezionare **Crea**.
6. Selezionare **FW-Hybrid-Test** come gruppo di risorse.
8. In **Area** selezionare la stessa località usata in precedenza.
1. Per il nome digitare **UDR-Hub-Spoke**.
9. Selezionare **Rivedi e crea**.
10. Selezionare **Crea**.
11. Al termine della creazione della tabella di route, selezionarla per aprire la pagina corrispondente.
12. Selezionare **Route** nella colonna di sinistra.
13. Selezionare **Aggiungi**.
14. Come nome della route digitare **ToSpoke**.
15. Per il prefisso dell'indirizzo digitare **10.6.0.0/16**.
16. Come tipo hop successivo selezionare **Appliance virtuale**.
17. Come indirizzo hop successivo digitare l'indirizzo IP privato del firewall annotato in precedenza.
18. Selezionare **OK**.

A questo punto associare la route alla subnet.

1. Nella pagina **UDR-Hub-Spoke - Route** selezionare **Subnet**.
2. Selezionare **Associa**.
3. In **Rete virtuale** selezionare **VNet-hub**.
1. In **Subnet** selezionare **GatewaySubnet**.
2. Selezionare **OK**.

A questo punto creare la route predefinita dalla subnet spoke.

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. Nella casella di testo di ricerca digitare **tabella di route** e premere **INVIO**.
3. Selezionare **Tabella di route**.
5. Selezionare **Crea**.
7. Selezionare **FW-Hybrid-Test** come gruppo di risorse.
8. In **Area** selezionare la stessa località usata in precedenza.
1. Per il nome digitare **UDR-DG**.
4. Per **Propaga route del gateway** selezionare **No**.
5. Selezionare **Rivedi e crea**.
6. Selezionare **Crea**.
7. Al termine della creazione della tabella di route, selezionarla per aprire la pagina corrispondente.
8. Selezionare **Route** nella colonna di sinistra.
9. Selezionare **Aggiungi**.
10. Come nome della route digitare **ToHub**.
11. Per il prefisso dell'indirizzo digitare **0.0.0.0/0**.
12. Come tipo hop successivo selezionare **Appliance virtuale**.
13. Come indirizzo hop successivo digitare l'indirizzo IP privato del firewall annotato in precedenza.
14. Selezionare **OK**.

A questo punto associare la route alla subnet.

1. Nella pagina **UDR-DG - Route** selezionare **Subnet**.
2. Selezionare **Associa**.
3. In **Rete virtuale** selezionare **VNet-spoke**.
1. In **Subnet** selezionare **SN-Workload**.
2. Selezionare **OK**.

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare ora le macchine virtuali locali e per il carico di lavoro spoke e posizionarle nelle subnet appropriate.

### <a name="create-the-workload-virtual-machine"></a>Creare la macchina virtuale per il carico di lavoro

Creare una macchina virtuale nella rete virtuale spoke, con IIS in esecuzione e senza indirizzo IP pubblico.

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. In **Più comuni** selezionare **Windows Server 2016 Datacenter**.
3. Immettere i valori seguenti per la macchina virtuale:
    - **Gruppo di risorse**: selezionare **FW-Hybrid-Test**.
    - **Nome macchina virtuale**: *VM-Spoke-01*.
    - **Area**: la stessa area usata in precedenza.
    - **Nome utente**: \<type a user name\>.
    - **Password**: \<type a password\>
4. In **Porte in ingresso pubbliche** selezionare **Consenti porte selezionate** e quindi selezionare **HTTP (80)** e **RDP (3389)**
4. Selezionare **Next:Disks**.
5. Accettare le impostazioni predefinite e selezionare **Next: Rete**.
6. Selezionare **VNet-Spoke** per la rete virtuale. La subnet è **SN-Workload**.
7. In **IP pubblico** selezionare **Nessuno**. 
9. Selezionare **Next:Management**.
10. In **Diagnostica di avvio** selezionare **Disabilita**.
11. Selezionare **Rivedi e crea**, esaminare le impostazioni nella pagina di riepilogo e quindi selezionare **Crea**.

### <a name="install-iis"></a>Installare IIS

1. Nel portale di Azure aprire Cloud Shell e assicurarsi che sia impostato su **PowerShell**.
2. Eseguire questo comando per installare IIS nella macchina virtuale e, se necessario, modificare il percorso:

   ```azurepowershell-interactive
   Set-AzVMExtension `
           -ResourceGroupName FW-Hybrid-Test `
           -ExtensionName IIS `
           -VMName VM-Spoke-01 `
           -Publisher Microsoft.Compute `
           -ExtensionType CustomScriptExtension `
           -TypeHandlerVersion 1.4 `
           -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell      Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
           -Location EastUS
   ```

### <a name="create-the-on-premises-virtual-machine"></a>Creare la macchina virtuale locale

Si tratta di una macchina virtuale usata per connettersi all'indirizzo IP pubblico usando Desktop remoto. Da qui, ci si connette al server locale attraverso il firewall.

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. In **Più comuni** selezionare **Windows Server 2016 Datacenter**.
3. Immettere i valori seguenti per la macchina virtuale:
    - **Gruppo di risorse**: selezionare Usa esistente e quindi **FW-Hybrid-Test**.
    - **Nome della macchina virtuale**: - *VM-Onprem*.
    - **Area**: la stessa area usata in precedenza.
    - **Nome utente**: \<type a user name\>.
    - **Password**: \<type a user password\>.
7. In **Porte in ingresso pubbliche** selezionare **Consenti porte selezionate** e quindi selezionare **RDP (3389)** .
4. Selezionare **Next:Disks**.
5. Accettare le impostazioni predefinite e selezionare **Next:Networking**.
6. Selezionare **VNet-Onprem** per la rete virtuale. La subnet è **SN-Corp**.
8. Selezionare **Next:Management**.
10. In **Diagnostica di avvio** selezionare **Disabilita**.
10. Selezionare **Rivedi e crea**, esaminare le impostazioni nella pagina di riepilogo e quindi selezionare **Crea**.

## <a name="test-the-firewall"></a>Testare il firewall

1. Innanzitutto, prendere nota dell'indirizzo IP privato per la macchina virtuale **VM-spoke-01**.

2. Dal portale di Azure connettersi alla macchina virtuale **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Aprire un Web browser in **VM-Onprem** e andare a http://\<VM-spoke-01 private IP\>.

   Viene visualizzata la pagina **VM-spoke-01**: ![Pagina Web VM-Spoke-01](media/tutorial-hybrid-portal/VM-Spoke-01-web.png)

4. Nella macchina virtuale **VM-Onprem** aprire un desktop remoto per **VM-spoke-01** all'indirizzo IP privato.

   La connessione dovrebbe avere esito positivo e dovrebbe essere possibile eseguire l'accesso.

A questo punto si è verificato che le regole del firewall funzionano:

<!---- You can ping the server on the spoke VNet.--->
- È possibile esplorare il Web server nella rete virtuale spoke.
- È possibile connettersi al server nella rete virtuale spoke con RDP.

Modificare quindi l'azione della raccolta di regole di rete del firewall impostandola su **Nega** per verificare che le regole del firewall funzionino come previsto.

1. Selezionare il firewall **AzFW01**.
2. Selezionare **Regole**.
3. Selezionare la scheda **Raccolta regole di rete** e quindi la raccolta di regole **RCNet01**.
4. In **Azione** selezionare **Nega**.
5. Selezionare **Salva**.

Chiudere eventuali desktop remoti esistenti prima di testare le regole modificate. A questo punto rieseguire i test, che dovrebbero avere tutti esito negativo.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile conservare le risorse del firewall per l'esercitazione successiva oppure, se non è più necessario, eliminare il gruppo di risorse **FW-Hybrid-Test** per eliminare tutte le risorse correlate al firewall.

## <a name="next-steps"></a>Passaggi successivi

È possibile ora monitorare i log di Firewall di Azure.

> [!div class="nextstepaction"]
> [Esercitazione: monitorare i log del Firewall di Azure](./firewall-diagnostics.md)