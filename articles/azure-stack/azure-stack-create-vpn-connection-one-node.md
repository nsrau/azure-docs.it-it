---
title: Creare una connessione VPN da sito a sito tra due reti virtuali in diversi ambienti Azure Stack Development Kit | Documenti Microsoft
description: Procedure dettagliate che un amministratore del cloud utilizza per creare una connessione VPN da sito a sito tra due ambienti di Azure Stack Development Kit a nodo singolo.
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: scottnap
ms.openlocfilehash: fa2a940620e06521fa110fa13dcbc3050635a502
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-development-kit-environments"></a>Creare una connessione VPN da sito a sito tra due reti virtuali in diversi ambienti Azure Stack Development Kit
## <a name="overview"></a>Panoramica
In questo articolo viene illustrato come creare una connessione VPN da sito a sito tra due reti virtuali in due ambienti Azure Stack Development Kit separati. Quando si configura le connessioni, viene illustrato il funzionamento gateway VPN nello Stack di Azure.

### <a name="connection-diagram"></a>Diagramma di connessione
Nel diagramma seguente viene illustrato quale la configurazione della connessione deve essere simile al termine.

![Configurazione della connessione VPN da sito a sito](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>Prima di iniziare
Per completare la configurazione della connessione, assicurarsi di disporre gli elementi seguenti prima di iniziare:

* Due server che soddisfino i requisiti hardware di Azure Stack Development Kit, definiti per il [prerequisiti per la distribuzione di Azure Stack](azure-stack-deploy.md). Assicurarsi che i prerequisiti vengono visualizzati di [articolo](azure-stack-deploy.md) sono soddisfatti troppo.
* Il [Kit di sviluppo di Azure Stack](https://azure.microsoft.com/en-us/overview/azure-stack/try/) pacchetto di distribuzione.

## <a name="deploy-the-azure-stack-development-kit-environments"></a>Distribuire gli ambienti Azure Stack Development Kit
Per completare la configurazione della connessione, è necessario distribuire due ambienti Azure Stack Development Kit.
> [!NOTE] 
> Per ogni Kit di sviluppo dello Stack Azure da distribuire, seguire la [istruzioni di distribuzione](azure-stack-run-powershell-script.md). In questo articolo vengono chiamati gli ambienti Azure Stack Development Kit *POC1* e *POC2*.


## <a name="prepare-an-offer-on-poc1-and-poc2"></a>Preparare un'offerta POC1 e POC2
In POC1 e POC2, preparare un'offerta in modo che un utente può sottoscrivere l'offerta e distribuire le macchine virtuali. Per informazioni su come creare un'offerta, vedere [rendere disponibili agli utenti di Azure Stack macchine virtuali](azure-stack-tutorial-tenant-vm.md).

## <a name="review-and-complete-the-network-configuration-table"></a>Rivedere e completare la tabella di configurazione di rete
Nella tabella seguente viene riepilogata la configurazione di rete per entrambi gli ambienti Azure Stack Development Kit. Utilizzare la procedura che viene visualizzato dopo la tabella per aggiungere l'indirizzo esterno BGPNAT specifico per la rete.

**Tabella di configurazione di rete**
|   |POC1|POC2|
|---------|---------|---------|
|Nome di rete virtuale     |VNET-01|VNET-02 |
|Spazio degli indirizzi di rete virtuale |10.0.10.0/23|10.0.20.0/23|
|Nome della subnet     |Subnet-01|Subnet-02|
|Intervallo di indirizzi subnet|10.0.10.0/24 |10.0.20.0/24 |
|Subnet gateway     |10.0.11.0/24|10.0.21.0/24|
|Indirizzo BGPNAT esterno     |         |         |

> [!NOTE]
> Gli indirizzi IP BGPNAT esterni nell'ambiente di esempio sono 10.16.167.195 per POC1 e 10.16.169.131 per POC2. Utilizzare la procedura seguente per determinare gli indirizzi IP BGPNAT esterni per gli host di Azure Stack Development Kit e quindi aggiungerli alla tabella di configurazione di rete precedente.


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Ottenere l'indirizzo IP della scheda esterna della macchina virtuale NAT
1. Accedere al computer fisico dello Stack di Azure per POC1.
2. Modificare il codice di Powershell seguente per sostituire la password dell'amministratore e quindi eseguire il codice nell'host di prova:

   ```powershell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "AzS-bgpnat01" `
    -Password $Password
   ```
3. Aggiungere l'indirizzo IP per la tabella di configurazione di rete che viene visualizzato nella sezione precedente.

4. Ripetere questa procedura su POC2.

## <a name="create-the-network-resources-in-poc1"></a>Creare le risorse di rete in POC1
Ora possibile creare le risorse di rete POC1 necessarie per impostare il gateway. Le istruzioni seguenti viene illustrato come creare le risorse tramite il portale per gli utenti. È inoltre possibile utilizzare il codice di PowerShell per creare le risorse.

![Flusso di lavoro che viene utilizzato per creare le risorse](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>Accedi come tenant
Un amministratore del servizio può accedere con un tenant per testare i piani, offerte e sottoscrizioni che potrebbero utilizzare i propri tenant. Se si dispone già di uno, [creare un account tenant](azure-stack-add-new-user-aad.md) prima di accedere.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Creare la rete virtuale e la subnet della macchina virtuale
1. Utilizzare un account tenant per accedere al portale per gli utenti.
2. Nel portale per gli utenti, selezionare **New**.

    ![Crea nuova rete virtuale](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)

3. Passare a **Marketplace**, quindi selezionare **rete**.
4. Selezionare **Rete virtuale**.
5. Per **nome**, **lo spazio degli indirizzi**, **nome Subnet**, e **intervallo di indirizzi di Subnet**, utilizzare i valori visualizzati in precedenza nella rete tabella di configurazione.
6. In **sottoscrizione**, viene visualizzata la sottoscrizione creata in precedenza.
7. Per **gruppo di risorse**, è possibile creare un gruppo di risorse o se si dispone già di uno, selezionare **utilizzare esistente**.
8. Verificare la località predefinita.
9. Selezionare **Aggiungi al dashboard**.
10. Selezionare **Create**.

### <a name="create-the-gateway-subnet"></a>Creare la subnet del gateway
1. Nel dashboard, aprire la risorsa di rete virtuale 01 / rete virtuale creata in precedenza.
2. Nel pannello **Impostazioni** selezionare **Subnet**.
3. Per aggiungere una subnet del gateway per la rete virtuale, selezionare **Subnet del Gateway**.
   
    ![Aggiungi subnet gateway](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Per impostazione predefinita, il nome della subnet è **GatewaySubnet**.
   Subnet gateway sono speciali. Per funzionare correttamente, è necessario che utilizzino il *GatewaySubnet* nome.
5. In **intervallo di indirizzi**, verificare che l'indirizzo sia **10.0.11.0/24**.
6. Selezionare **OK** per creare la subnet del gateway.

### <a name="create-the-virtual-network-gateway"></a>Creare il gateway di rete virtuale
1. Nel portale di Azure, selezionare **New**. 
2. Passare a **Marketplace**, quindi selezionare **rete**.
3. Nell'elenco di risorse di rete, selezionare **gateway di rete virtuale**.
4. In **nome**, immettere **GW1**.
5. Selezionare il **rete virtuale** elemento per scegliere una rete virtuale.
   Selezionare **rete virtuale-01** dall'elenco.
6. Selezionare il **indirizzo IP pubblico** voce di menu. Quando il **scegliere l'indirizzo IP pubblico** blade si apre selezionare **Crea nuovo**.
7. In **nome**, immettere **GW1 PiP**, quindi selezionare **OK**.
8.  Per impostazione predefinita, per **tipo VPN**, **basato su Route** è selezionata.
    Mantenere il **basato su Route** tipo VPN.
9. Verificare che la **Sottoscrizione** e la **Località** siano corrette. È possibile aggiungere la risorsa al dashboard. Selezionare **Create**.

### <a name="create-the-local-network-gateway"></a>Creare il gateway di rete locale
L'implementazione di un *gateway di rete locale* in questa distribuzione di valutazione di Azure Stack è un po' diversa da quella di una distribuzione di Azure effettiva.

In una distribuzione di Azure, un gateway di rete locale rappresenta un dispositivo fisico locale (nel tenant), che consente di connettersi a un gateway di rete virtuale in Azure. In questa distribuzione di valutazione dello Stack di Azure, entrambe le estremità della connessione sono gateway di rete virtuale.

Un modo per considerare questo più in generale è che la risorsa del gateway di rete locale indica sempre il gateway remoto a altra estremità della connessione. A causa della modalità che è stato progettato il Kit di sviluppo dello Stack di Azure, è necessario fornire l'indirizzo IP della scheda di rete esterna durante la conversione di indirizzo di rete (NAT) VM di altri Azure Stack Development Kit come indirizzo IP pubblico del gateway di rete locale. È quindi possibile creare i mapping NAT nella VM NAT per assicurarsi che entrambe le estremità siano collegate correttamente.


### <a name="create-the-local-network-gateway-resource"></a>Creare la risorsa di gateway di rete locale
1. Accedere al computer fisico dello Stack di Azure per POC1.
2. Nel portale per gli utenti, selezionare **New**.
3. Passare a **Marketplace**, quindi selezionare **rete**.
4. Nell'elenco di risorse, selezionare **gateway di rete locale**.
5. In **nome**, immettere **POC2-GW**.
6. In **indirizzo IP**, immettere l'indirizzo esterno BGPNAT POC2. Questo indirizzo viene visualizzato in precedenza nella tabella di configurazione di rete.
7. In **spazio degli indirizzi**, per lo spazio degli indirizzi della rete virtuale POC2 creati in un secondo momento, immettere **10.0.20.0/23**.
8. Verificare che il **sottoscrizione**, **gruppo di risorse**, e **percorso** siano corrette e quindi selezionare **crea**.

### <a name="create-the-connection"></a>Creare la connessione
1. Nel portale per gli utenti, selezionare **New**.
2. Passare a **Marketplace**, quindi selezionare **rete**.
3. Nell'elenco di risorse, selezionare **connessione**.
4. Nel **nozioni di base** pannello impostazioni per il **tipo di connessione**selezionare **Site-to-site (IPSec)**.
5. Selezionare il **sottoscrizione**, **gruppo di risorse**, e **percorso**, quindi selezionare **OK**.
6. Nel **impostazioni** pannello seleziona **gateway di rete virtuale**e quindi selezionare **GW1**.
7. Selezionare **gateway di rete locale**, quindi selezionare **POC2-GW**.
8. In **nome connessione**, immettere **POC1 POC2**.
9. In **chiave condivisa (PSK)**, immettere **12345**, quindi selezionare **OK**.
10. Nel **riepilogo** pannello seleziona **OK**.

### <a name="create-a-vm"></a>Creare una macchina virtuale
Per convalidare i dati che passano attraverso la connessione VPN, è necessario inviare e ricevere dati in ogni Kit di sviluppo dello Stack di Azure le macchine virtuali. Creare una macchina virtuale in POC1 ora e quindi nella rete virtuale, inserirlo nella subnet VM.

1. Nel portale di Azure, selezionare **New**.
2. Passare a **Marketplace**, quindi selezionare **calcolo**.
3. Nell'elenco di immagini di macchina virtuale, selezionare il **Eval di Data Center di Windows Server 2016** immagine.
4. Nel **nozioni di base** pannello, in **nome**, immettere **VM01**.
5. Immettere un nome utente valido e una password. Utilizzare questo account per accedere alla macchina virtuale dopo la sua creazione.
6. Fornire un **sottoscrizione**, **gruppo di risorse**, e **percorso**, quindi selezionare **OK**.
7. Nel **dimensioni** pannello per questa istanza, selezionare una dimensione di macchina virtuale, quindi **selezionare**.
8. Nel **impostazioni** pannello, accettare le impostazioni predefinite. Verificare che il **rete virtuale-01** rete virtuale è stata selezionata. Verificare che la subnet è impostata su **10.0.10.0/24**. Selezionare **OK**.
9. Nel **riepilogo** pannello, rivedere le impostazioni e quindi selezionare **OK**.



## <a name="create-the-network-resources-in-poc2"></a>Creare le risorse di rete in POC2

Il passaggio successivo consiste nel creare le risorse di rete per POC2. Le istruzioni seguenti viene illustrato come creare le risorse tramite il portale per gli utenti.

### <a name="sign-in-as-a-tenant"></a>Accedi come tenant
Un amministratore del servizio può accedere con un tenant per testare i piani, offerte e sottoscrizioni che potrebbero utilizzare i propri tenant. Se si dispone già di uno, [creare un account tenant](azure-stack-add-new-user-aad.md) prima di accedere.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Creare la rete virtuale e la subnet della macchina virtuale

1. Accedi con un account del tenant.
2. Nel portale per gli utenti, selezionare **New**.
3. Passare a **Marketplace**, quindi selezionare **rete**.
4. Selezionare **Rete virtuale**.
5. Utilizzare le informazioni visualizzate in precedenza nella tabella di configurazione di rete per identificare i valori per il POC2 **nome**, **lo spazio degli indirizzi**, **nome Subnet**e **Intervallo di indirizzi di subnet**.
6. In **sottoscrizione**, viene visualizzata la sottoscrizione creata in precedenza.
7. Per **gruppo di risorse**, creare un nuovo gruppo di risorse o, se già presente, selezionare **utilizzare esistente**.
8. Verificare il valore predefinito **percorso**.
9. Selezionare **Aggiungi al dashboard**.
10. Selezionare **Create**.

### <a name="create-the-gateway-subnet"></a>Creare la subnet del gateway
1. Aprire la risorsa di rete virtuale creata (**rete virtuale-02**) dal dashboard.
2. Nel pannello **Impostazioni** selezionare **Subnet**.
3. Selezionare **subnet del Gateway** per aggiungere una subnet del gateway per la rete virtuale.
4. Il nome predefinito della subnet è **GatewaySubnet**.
   Questo nome specifico è necessario per il corretto funzionamento delle subnet del gateway.
5. Nel **intervallo di indirizzi** campo, verificare l'indirizzo sia **10.0.21.0/24**.
6. Selezionare **OK** per creare la subnet del gateway.

### <a name="create-the-virtual-network-gateway"></a>Creare il gateway di rete virtuale
1. Nel portale di Azure, selezionare **New**.  
2. Passare a **Marketplace**, quindi selezionare **rete**.
3. Nell'elenco di risorse di rete, selezionare **gateway di rete virtuale**.
4. In **nome**, immettere **GW2**.
5. Per scegliere una rete virtuale, selezionare **rete virtuale**. Selezionare quindi **rete virtuale-02** dall'elenco.
6. Selezionare **indirizzo IP pubblico**. Quando il **scegliere l'indirizzo IP pubblico** blade si apre selezionare **Crea nuovo**.
7. In **nome**, immettere **GW2 PiP**, quindi selezionare **OK**.
8. Per impostazione predefinita, per **tipo VPN**, **basato su Route** è selezionata.
    Mantenere il **basato su Route** tipo VPN.
9. Verificare che la **Sottoscrizione** e la **Località** siano corrette. È possibile aggiungere la risorsa al dashboard. Selezionare **Create**.

### <a name="create-the-local-network-gateway-resource"></a>Creare la risorsa di gateway di rete locale

1. Nel portale per gli utenti POC2, selezionare **New**. 
4. Passare a **Marketplace**, quindi selezionare **rete**.
5. Nell'elenco di risorse, selezionare **gateway di rete locale**.
6. In **nome**, immettere **POC1-GW**.
7. In **indirizzo IP**, immettere l'indirizzo esterno BGPNAT POC1 elencato in precedenza nella tabella di configurazione di rete.
8. In **spazio degli indirizzi**, da POC1, immettere il **10.0.10.0/23** spazio di indirizzi **rete virtuale-01**.
9. Verificare che il **sottoscrizione**, **gruppo di risorse**, e **percorso** siano corrette e quindi selezionare **crea**.

## <a name="create-the-connection"></a>Creare la connessione
1. Nel portale per gli utenti, selezionare **New**. 
2. Passare a **Marketplace**, quindi selezionare **rete**.
3. Nell'elenco di risorse, selezionare **connessione**.
4. Nel **base** pannello impostazioni per il **tipo di connessione**, scegliere **Site-to-site (IPSec)**.
5. Selezionare il **sottoscrizione**, **gruppo di risorse**, e **percorso**, quindi selezionare **OK**.
6. Nel **impostazioni** pannello seleziona **gateway di rete virtuale**e quindi selezionare **GW2**.
7. Selezionare **gateway di rete locale**, quindi selezionare **POC1-GW**.
8. In **nome connessione**, immettere **POC2 POC1**.
9. In **chiave condivisa (PSK)**, immettere **12345**. Se si sceglie un valore diverso, tenere presente che *deve* corrisponde al valore per la chiave condivisa che è stato creato in POC1. Selezionare **OK**.
10. Esaminare il **riepilogo** blade e quindi selezionare **OK**.

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale
Creare una macchina virtuale in POC2 ora e inserirlo nella subnet VM nella rete virtuale.

1. Nel portale di Azure, selezionare **New**.
2. Passare a **Marketplace**, quindi selezionare **calcolo**.
3. Nell'elenco di immagini di macchina virtuale, selezionare il **Eval di Data Center di Windows Server 2016** immagine.
4. Nel **nozioni di base** pannello per **nome**, immettere **VM02**.
5. Immettere un nome utente valido e una password. Utilizzare questo account per accedere alla macchina virtuale dopo la sua creazione.
6. Fornire un **sottoscrizione**, **gruppo di risorse**, e **percorso**, quindi selezionare **OK**.
7. Nel **dimensioni** pannello Seleziona dimensioni per l'istanza di una macchina virtuale e quindi selezionare **selezionare**.
8. Nel **impostazioni** pannello, è possibile accettare i valori predefiniti. Verificare che il **rete virtuale-02** rete virtuale sia selezionata e verificare che la subnet è impostata su **10.0.20.0/24**. Selezionare **OK**.
9. Esaminare le impostazioni nella **riepilogo** blade e quindi selezionare **OK**.

## <a name="configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal"></a>Configurare la macchina virtuale NAT su ogni Kit di sviluppo dello Stack di Azure per l'attraversamento di gateway
Poiché il Kit di sviluppo dello Stack di Azure è indipendente e isolate dalla rete in cui viene distribuito l'host fisico, il *esterno* rete VIP che sono connessi i gateway non è effettivamente esterno. Al contrario, la rete VIP è nascosto dietro a un router che esegue la conversione degli indirizzi di rete. 

Il router è una macchina virtuale di Windows Server, denominata *AzS bgpnat01*, che esegue il ruolo servizio Routing e accesso remoto (RRAS) nell'infrastruttura di Azure Stack Development Kit. Nella macchina virtuale AzS bgpnat01 per consentire la connessione VPN da sito a sito per connettersi a entrambe le estremità, è necessario configurare NAT. 

Per configurare la connessione VPN, è necessario creare una route statica di mappa NAT che esegue il mapping all'interfaccia esterna di una macchina virtuale BGPNAT all'indirizzo VIP del Pool di Gateway di bordo. Una route di mapping NAT statica è necessaria per ogni porta in una connessione VPN.

> [!NOTE]
> Questa configurazione è necessaria per solo negli ambienti Azure Stack Development Kit.
> 
> 

### <a name="configure-the-nat"></a>Configurare NAT
> [!IMPORTANT]
> È necessario completare questa procedura per *entrambi* ambienti Azure Stack Development Kit.

1. Determinare il **indirizzo IP interno** da utilizzare nello script di PowerShell seguente. Aprire il gateway di rete virtuale (GW1 e GW2) e quindi scegliere il **Panoramica** pannello, salvare il valore per il **indirizzo IP pubblico** per un uso successivo.
![Indirizzo IP interno](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)
2. Accedere al computer fisico dello Stack di Azure per POC1.
3. Copiare e modificare lo script di PowerShell seguente. Per configurare il protocollo NAT in ogni Kit di sviluppo dello Stack di Azure, eseguire lo script in un Windows PowerShell ISE con privilegi elevati. Nello script, aggiungere i valori per il *indirizzo esterno BGPNAT* e *indirizzo IP interno* segnaposto:

   ```powershell
   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

4. Ripetere questa procedura su POC2.

## <a name="test-the-connection"></a>Testare la connessione
Ora che viene stabilita la connessione site-to-site, è necessario convalidare che è possibile ottenere il traffico che passano attraverso di esso. Per convalidare, accedere a una delle macchine virtuali che è stato creato in un ambiente Azure Stack Development Kit. Quindi, eseguire il ping la macchina virtuale creata in altro ambiente. 

Per assicurarsi che si invia il traffico attraverso la connessione da sito a sito, verificare che si esegue il ping l'indirizzo IP diretto (DIP) della macchina virtuale nella subnet remota, non l'indirizzo VIP. A tale scopo, individuare l'indirizzo DIP a altra estremità della connessione. Salva l'indirizzo per un uso successivo.

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>Accedere al tenant di macchina virtuale in POC1
1. Accedere al computer fisico dello Stack di Azure per POC1 e quindi utilizzare un account tenant per accedere al portale per gli utenti.
2. Nella barra di spostamento a sinistra, selezionare **calcolo**.
3. Nell'elenco di macchine virtuali, trovare **VM01** creato in precedenza e selezionarlo.
4. Nel pannello per la macchina virtuale, fare clic su **Connetti**e quindi aprire il file VM01.rdp.
   
     ![Pulsante Connetti](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Accedere con l'account configurato al momento della creazione della macchina virtuale.
6. Aprire con privilegi elevati **Windows PowerShell** finestra.
7. Immettere **ipconfig /all**.
8. Nell'output, è possibile trovare il **indirizzo IPv4**e quindi salvare l'indirizzo per un uso successivo. Questo è l'indirizzo che eseguirà il ping da POC2. Nell'ambiente di esempio è **10.0.10.4**, ma quello effettivo potrebbe essere diverso. Devono rientrare il **10.0.10.0/24** subnet creato in precedenza.
9. Per creare una regola del firewall che consente la macchina virtuale rispondere ai ping, eseguire il comando PowerShell seguente:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>Accedere al tenant di macchina virtuale in POC2
1. Accedere al computer fisico dello Stack di Azure per POC2 e quindi utilizzare un account tenant per accedere al portale per gli utenti.
2. Nella barra di spostamento a sinistra, fare clic su **calcolo**.
3. Dall'elenco di macchine virtuali, trovare **VM02** creato in precedenza e selezionarlo.
4. Nel pannello della macchina virtuale fare clic su **Connetti**.
5. Accedere con l'account configurato al momento della creazione della macchina virtuale.
6. Aprire con privilegi elevati **Windows PowerShell** finestra.
7. Immettere **ipconfig /all**.
8. Dovrebbe essere un indirizzo IPv4 che rientra **10.0.20.0/24**. Nell'ambiente di esempio, l'indirizzo è **10.0.20.4**, ma l'indirizzo potrebbe essere diverso.
9. Per creare una regola del firewall che consente la macchina virtuale rispondere ai ping, eseguire il comando PowerShell seguente:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. Dalla macchina virtuale in POC2, eseguire il ping la macchina virtuale in POC1, tramite il tunnel. A tale scopo, eseguire il ping il DIP registrato da VM01.
   Nell'ambiente di esempio, si tratta di **10.0.10.4**, ma assicurarsi di ping dell'indirizzo a cui si è preso nota nell'ambiente lab. Verrà visualizzato un risultato simile al seguente:
   
    ![Ping ha esito positivo](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. Una risposta dalla macchina virtuale remota indica un test riuscito! È possibile chiudere la finestra della macchina virtuale. Per testare la connessione, è possibile provare altri tipi di trasferimenti di dati come una copia di file.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Visualizzazione delle statistiche di trasferimento dati attraverso la connessione del gateway
Se si desidera conoscere la quantità di dati passa attraverso la connessione site-to-site, queste informazioni sono disponibili sul **connessione** blade. Questo test è anche un altro modo per verificare che il ping che appena inviato è effettivamente passato tramite la connessione VPN.

1. Mentre è effettuato l'accesso alla macchina virtuale tenant in POC2, utilizzare l'account tenant per accedere al portale per gli utenti.
2. Passare a **tutte le risorse**, quindi selezionare il **POC2 POC1** connessione. **Connessioni** viene visualizzato.
4. Nel **connessione** blade, le statistiche per **dati** e **i dati in uscita** vengono visualizzati. Nella schermata seguente, con un numero elevato per il trasferimento di file aggiuntivi. Dovrebbe essere presente alcuni valori diversi da zero.
   
    ![Dati in ingresso e in uscita](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
