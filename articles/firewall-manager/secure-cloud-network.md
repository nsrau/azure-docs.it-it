---
title: "Esercitazione: Proteggere l'hub virtuale con Gestione firewall di Azure"
description: In questa esercitazione viene illustrato come proteggere l'hub virtuale con Gestione firewall di Azure usando il portale di Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 07/29/2020
ms.author: victorh
ms.openlocfilehash: 458ebe14e77c7b190a5c4cdd9b408396589d5d27
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420822"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-firewall-manager"></a>Esercitazione: Proteggere l'hub virtuale con Gestione firewall di Azure

Gestione firewall di Azure consente di creare hub virtuali protetti per proteggere il traffico di rete cloud destinato a indirizzi IP privati, a soluzioni PaaS di Azure e a Internet. Il routing del traffico verso il firewall è automatizzato, pertanto non è necessario creare route definite dall'utente.

![proteggere la rete cloud](media/secure-cloud-network/secure-cloud-network.png)

Gestione firewall supporta anche un'architettura di rete virtuale hub. Per un confronto delle architetture di tipo hub virtuale protetto e rete virtuale hub, vedere [Informazioni sulle opzioni disponibili per l'architettura di Gestione firewall di Azure](vhubs-and-vnets.md).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare la rete virtuale spoke
> * Creare un hub virtuale protetto
> * Connettere le reti virtuali hub-spoke
> * Instradare il traffico all'hub
> * Distribuire i server
> * Creare un criterio firewall e proteggere l'hub
> * Testare il firewall

## <a name="create-a-hub-and-spoke-architecture"></a>Creare un'architettura hub-spoke

Prima di tutto, creare reti virtuali spoke in cui poter collocare i server.

### <a name="create-two-spoke-virtual-networks-and-subnets"></a>Creare due reti virtuali spoke e le subnet

Le due reti virtuali includeranno ognuna un server del carico di lavoro e saranno protette tramite firewall.

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. In **Rete** selezionare **Rete virtuale**.
2. In **Sottoscrizione** selezionare la propria sottoscrizione.
1. Per **Gruppo di risorse**, selezionare **Crea nuovo**, digitare **fw-manager** come nome e selezionare **OK**.
2. Per **Nome**, digitare **Spoke-01**.
3. In **Area** selezionare **(Stati Uniti) Stati Uniti orientali**.
4. Selezionare **Avanti: Indirizzi IP**.
1. Per **Spazio indirizzi** digitare **10.1.0.0/16**.
3. Selezionare **Aggiungi subnet**.
4. Digitare **Workload-01-SN**.
5. Per **Intervallo di indirizzi della subnet** digitare **10.1.1.0/24**.
6. Selezionare **Aggiungi**.
1. Selezionare **Rivedi e crea**.
2. Selezionare **Crea**.

Ripetere questa procedura per creare un'altra rete virtuale simile:

Nome: **Spoke-02**<br>
Spazio degli indirizzi: **10.2.0.0/16**<br>
Nome subnet: **Workload-02-SN**<br>
Intervallo di indirizzi della subnet: **10.2.1.0/24**

### <a name="create-the-secured-virtual-hub"></a>Creare l'hub virtuale protetto

Creare l'hub virtuale protetto usando Gestione firewall.

1. Nella home page del portale di Azure selezionare **Tutti i servizi**.
2. Nella casella di ricerca digitare **Gestione firewall**, quindi selezionare **Gestione firewall**.
3. Nella pagina **Gestione firewall** selezionare **Visualizza hub virtuali protetti**.
4. Nella pagina **Gestione firewall | Hub virtuali protetti** selezionare **Crea nuovo hub virtuale protetto**.
5. Per **Gruppo di risorse**, selezionare **fw-manager**.
7. In **Area** selezionare **Stati Uniti orientali**.
1. Per **Nome hub virtuale protetto**, digitare **Hub-01**.
2. Per **Spazio indirizzi dell'hub**, digitare **10.0.0.0/16**.
3. Come nome della nuova rete WAN virtuale digitare **Vwan-01**.
4. Lasciare deselezionata la casella di controllo **Includere il gateway VPN per abilitare i partner di sicurezza affidabili**.
5. Selezionare **Avanti: Firewall di Azure**.
6. Accettare l'impostazione predefinita **Firewall di Azure** **Abilitato** e quindi selezionare **Avanti: Partner di sicurezza affidabile**.
7. Accettare l'impostazione predefinita **Partner di sicurezza affidabile** **Disabilitato** e selezionare **Avanti: Rivedi e crea**.
8. Selezionare **Create** (Crea). La distribuzione richiederà circa 30 minuti.

A questo punto è possibile ottenere l'indirizzo IP pubblico del firewall.

1. Al termine della distribuzione, nel portale di Azure selezionare **Tutti i servizi**.
1. Digitare **gestione firewall** e quindi selezionare **Gestione firewall**.
2. Selezionare **Hub virtuali protetti**.
3. Selezionare **hub-01**.
7. Selezionare **Configurazione IP pubblico**.
8. Prendere nota dell'indirizzo IP pubblico, che verrà usato in seguito.

### <a name="connect-the-hub-and-spoke-virtual-networks"></a>Connettere le reti virtuali hub-spoke

Eseguire ora il peering tra le reti virtuali hub-spoke.

1. Selezionare il gruppo di risorse **fw-manager**, quindi selezionare la rete WAN virtuale **Vwan-01**.
2. In **Connettività** selezionare **Connessioni rete virtuale**.
3. Selezionare **Aggiungi connessione**.
4. Per **Nome connessione**, digitare **hub-spoke-01**.
5. Per **Hub**, selezionare **Hub-01**.
6. Per **Gruppo di risorse**, selezionare **fw-manager**.
7. Per **Rete virtuale**, selezionare **Spoke-01**.
8. Selezionare **Crea**.

Ripetere la connessione alla rete virtuale **Spoke-02**: nome connessione - **hub-spoke-02**

### <a name="configure-the-hub-and-spoke-routing"></a>Configurare il routing hub-spoke

Nel portale di Azure aprire un'istanza di Cloud Shell ed eseguire il comando di Azure PowerShell seguente per configurare il routing hub-spoke necessario. La propagazione delle connessioni di spoke/succursali con peering deve essere impostata su **NONE**. In questo modo si impedisce la comunicazione any-to-any tra gli spoke e si indirizza invece il traffico al firewall usando la route predefinita.

```azurepowershell
$noneRouteTable = Get-AzVHubRouteTable -ResourceGroupName fw-manager `
                  -HubName hub-01 -Name noneRouteTable
$vnetConns = Get-AzVirtualHubVnetConnection -ResourceGroupName fw-manager `
             -ParentResourceName hub-01

$vnetConn = $vnetConns[0]
$vnetConn.RoutingConfiguration.PropagatedRouteTables.Ids = @($noneRouteTable)
$vnetConn.RoutingConfiguration.PropagatedRouteTables.Labels = @("none")
Update-AzVirtualHubVnetConnection -ResourceGroupName fw-manager `
   -ParentResourceName hub-01 -Name $vnetConn.Name `
   -RoutingConfiguration $vnetConn.RoutingConfiguration

$vnetConn = $vnetConns[1]
$vnetConn.RoutingConfiguration.PropagatedRouteTables.Ids = @($noneRouteTable)
$vnetConn.RoutingConfiguration.PropagatedRouteTables.Labels = @("none")
Update-AzVirtualHubVnetConnection -ResourceGroupName fw-manager `
   -ParentResourceName hub-01 -Name $vnetConn.Name -RoutingConfiguration $vnetConn.RoutingConfiguration
```

## <a name="deploy-the-servers"></a>Distribuire i server

1. Nel portale di Azure fare clic su **Crea una risorsa**.
2. Selezionare **Windows Server 2016 Datacenter** nell'elenco **Più comuni**.
3. Immettere i valori seguenti per la macchina virtuale:

   |Impostazione  |Valore  |
   |---------|---------|
   |Resource group     |**fw-manager**|
   |Nome macchina virtuale     |**Srv-workload-01**|
   |Region     |**(USA) Stati Uniti orientali**|
   |Nome utente amministratore     |digitare un nome utente|
   |Password     |digitare una password|

4. In **Regole porta in ingresso**, per **Porte in ingresso pubbliche**, selezionare **Nessuna**.
6. Accettare tutte le altre impostazioni predefinite e selezionare **Avanti: Dischi**.
7. Accettare tutte le impostazioni predefinite del disco e selezionare **Avanti: Rete**.
8. Selezionare **Spoke-01** per la rete virtuale e **Workload-01-SN** per la subnet.
9. In **IP pubblico** selezionare **Nessuno**.
11. Accettare tutte le altre impostazioni predefinite e selezionare **Avanti: Gestione**.
12. Selezionare **Disattivato** per disabilitare la diagnostica di avvio. Accettare tutte le altre impostazioni predefinite e selezionare **Rivedi e crea**.
13. Verificare le impostazioni nella pagina di riepilogo e quindi selezionare **Crea**.

Usare le informazioni della tabella seguente per configurare un'altra macchina virtuale denominata **Srv-Workload-02**. Il resto della configurazione è uguale a quella della macchina virtuale **Srv-workload-01**.

|Impostazione  |valore  |
|---------|---------|
|Rete virtuale|**Spoke-02**|
|Subnet|**Workload-02-SN**|

Dopo la distribuzione dei server, selezionare una risorsa server e in **Rete** prendere nota dell'indirizzo IP privato di ogni server.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Creare un criterio firewall e proteggere l'hub

Un criterio firewall definisce raccolte di regole per indirizzare il traffico su uno o più hub virtuali protetti. Sarà perciò necessario creare il criterio firewall e quindi proteggere l'hub.

1. In Gestione firewall selezionare **Visualizza criteri firewall di Azure**.
2. Selezionare **Crea criterio firewall di Azure**.
3. In **Dettagli criteri**, per **Nome** digitare **Policy-01** e per **Area** selezionare **Stati Uniti orientali**.
4. Selezionare **Avanti: Impostazioni DNS (anteprima)** .
1. Selezionare **Avanti: Regole**.
2. Nella scheda **Regole** selezionare **Aggiungi una raccolta regole**.
3. Nella pagina **Aggiungi una raccolta regole** digitare **App-RC-01** per **Nome**.
4. Per **Tipo di raccolta regole**, selezionare **Applicazione**.
5. In **Priorità** digitare **100**.
6. Verificare che **Azione raccolta regole** sia **Consenti**.
7. Come **Nome** della regola, digitare **Allow-msft**.
8. Per **Tipo di origine**, selezionare **Indirizzo IP**.
9. Per **Origine**, digitare **\*** .
10. Per **Protocollo**, digitare **http,https**.
11. Verificare che **Tipo di destinazione** sia impostato su **FQDN**.
12. Per **Destinazione**, digitare **\*.microsoft.com**.
13. Selezionare **Aggiungi**.

Aggiungere una regola DNAT in modo da poter connettere un desktop remoto alla macchina virtuale **Srv-Workload-01**.

1. Selezionare **Aggiungi una raccolta regole**.
2. Per **Nome** digitare **DNAT-rdp**.
3. Per **Tipo di raccolta regole**, selezionare **DNAT**.
4. In **Priorità** digitare **100**.
5. Per **Nome** della regola digitare **Allow-rdp**.
6. Per **Tipo di origine**, selezionare **Indirizzo IP**.
7. Per **Origine**, digitare **\*** .
8. In **Protocollo** selezionare **TCP**.
9. In **Porte di destinazione** digitare **3389**.
10. In **Tipo di destinazione** selezionare **Indirizzo IP**.
11. Per **Destinazione**, digitare l'indirizzo IP pubblico del firewall annotato in precedenza.
12. Per **Indirizzo convertito**, digitare l'indirizzo IP privato per la macchina virtuale **Srv-Workload-01** annotato in precedenza.
13. Per **Porta tradotta** digitare **3389**.
14. Selezionare **Aggiungi**.

Aggiungere una regola di rete in modo da poter connettere un desktop remoto da **Srv-Workload-01** a **Srv-Workload-02**.

1. Selezionare **Aggiungi una raccolta regole**.
2. Per **Nome**, digitare **vnet-rdp**.
3. In **Tipo di raccolta regole** selezionare **Rete**.
4. In **Priorità** digitare **100**.
5. Per **Nome** della regola, digitare **Allow-vnet**.
6. Per **Tipo di origine**, selezionare **Indirizzo IP**.
7. Per **Origine**, digitare **\*** .
8. In **Protocollo** selezionare **TCP**.
9. In **Porte di destinazione** digitare **3389**.
9. In **Tipo di destinazione** selezionare **Indirizzo IP**.
10. Per **Destinazione**, digitare l'indirizzo IP pubblico della macchina virtuale **Srv-Workload-02** annotato in precedenza.
11. Selezionare **Aggiungi**.
1. Selezionare **Avanti: Intelligence sulle minacce**.
2. Selezionare **Avanti: Hub**.
3. Nella scheda **Hub** selezionare **Associa hub virtuali**.
4. Selezionare **Hub-01** e quindi **Aggiungi**.
5. Selezionare **Rivedi e crea**.
6. Selezionare **Crea**.

Il completamento di questa operazione può richiedere circa cinque minuti.

## <a name="route-traffic-to-your-hub"></a>Instradare il traffico all'hub

A questo punto è necessario assicurarsi che il traffico di rete venga instradato attraverso il firewall.

1. In Gestione firewall selezionare **Hub virtuali protetti**.
2. Selezionare **Hub-01**.
3. In **Impostazioni** selezionare **Configurazione della sicurezza**.
4. In **Traffico Internet** selezionare **Firewall di Azure**.
5. In **Traffico privato** selezionare **Send via Azure Firewall** (Invia tramite Firewall di Azure).
10. Verificare che **Traffico Internet** per le connessioni **hub-spoke** sia impostato su **Protetto**.
11. Selezionare **Salva**.


## <a name="test-your-firewall"></a>Testare il firewall

Per testare le regole del firewall, connettere un desktop remoto usando l'indirizzo IP pubblico del firewall, che è convertito tramite NAT in **Srv-Workload-01**. Da qui verrà usato un browser per testare la regola dell'applicazione e connettere un desktop remoto a **Srv-Workload-02** per testare la regola di rete.

### <a name="test-the-application-rule"></a>Testare la regola dell'applicazione

A questo punto testare le regole del firewall per verificare che tutto funzioni come previsto.

1. Connettere un desktop remoto all'indirizzo IP pubblico del firewall, quindi accedere.

3. Aprire Internet Explorer e passare a https://www.microsoft.com.
4. Selezionare **OK** > **Close** negli avvisi di sicurezza di Internet Explorer.

   Verrà visualizzata la home page Microsoft.

5. Passare a https://www.google.com.

   Si verrà bloccati dal firewall.

A questo punto è stato verificato che la regola dell'applicazione del firewall funziona:

* È possibile passare al nome di dominio completo consentito ma non agli altri.

### <a name="test-the-network-rule"></a>Testare la regola di rete

Ora testare la regola di rete.

- Aprire un desktop remoto all'indirizzo IP privato di **Srv-Workload-02**.

   Un desktop remoto dovrebbe connettersi a **Srv-Workload-02**.

A questo punto è stato verificato che la regola di rete del firewall funziona:
* È possibile connettere un desktop remoto a un server che si trova in un'altra rete virtuale.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Vedere le informazioni sui partner di sicurezza affidabili](trusted-security-partners.md)
