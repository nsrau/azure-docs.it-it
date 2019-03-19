---
title: Creare una connessione VPN site-to-site tra due reti virtuali in diversi ambienti di Azure Stack Development Kit | Microsoft Docs
description: Procedura dettagliata che usa un amministratore del cloud per creare una connessione VPN site-to-site tra due ambienti di Azure Stack Development Kit a nodo singolo.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: sethm
ms.reviewer: scottnap
ms.lastreviewed: 09/12/2018
ROBOTS: NOINDEX
ms.openlocfilehash: e038afe27718dcbd407e83b5c9190606e6b917c4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58004661"
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-development-kit-environments"></a>Creare una connessione VPN site-to-site tra due reti virtuali in diversi ambienti di Azure Stack Development Kit
## <a name="overview"></a>Panoramica
Questo articolo illustra come creare una connessione VPN site-to-site tra due reti virtuali in due ambienti Azure Stack Development Kit separati. Durante la configurazione di connessioni, si apprenderà come funzionano i gateway VPN di Azure Stack.

### <a name="connection-diagram"></a>Diagramma di connessione
Il diagramma seguente illustra la configurazione della connessione aspetto che avrà al termine.

![Configurazione della connessione VPN Site-to-site](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>Prima di iniziare
Per completare la configurazione della connessione, verificare di disporre gli elementi seguenti prima di iniziare:

* Due server e altri prerequisiti che soddisfano i requisiti hardware di Azure Stack Development Kit, come descritto in [Guida introduttiva: Valuta Azure Stack Development Kit](azure-stack-deploy-overview.md). 
* Il [Azure Stack Development Kit](https://azure.microsoft.com/overview/azure-stack/try/) pacchetto di distribuzione.

## <a name="deploy-the-azure-stack-development-kit-environments"></a>Distribuire gli ambienti Azure Stack Development Kit
Per completare la configurazione della connessione, è necessario distribuire due ambienti di Azure Stack Development Kit.
> [!NOTE] 
> Per ogni Azure Stack Development Kit che si distribuisce, seguire le [istruzioni per la distribuzione](azure-stack-run-powershell-script.md). In questo articolo, vengono chiamati ambienti Azure Stack Development Kit *POC1* e *POC2*.


## <a name="prepare-an-offer-on-poc1-and-poc2"></a>Preparare un'offerta in POC1 e POC2
In POC1 e POC2, preparare un'offerta in modo che un utente può sottoscrivere l'offerta e distribuire le macchine virtuali. Per informazioni su come creare un'offerta, vedere [rendere disponibili agli utenti di Azure Stack le macchine virtuali](azure-stack-tutorial-tenant-vm.md).

## <a name="review-and-complete-the-network-configuration-table"></a>Rivedere e completare la tabella di configurazione di rete
Nella tabella seguente viene riepilogata la configurazione di rete per entrambi gli ambienti Azure Stack Development Kit. Utilizzare la procedura visualizzata sotto la tabella per aggiungere l'indirizzo esterno BGPNAT specifico per la rete.

**Tabella di configurazione di rete**

|   |POC1|POC2|
|---------|---------|---------|
|Nome della rete virtuale     |VNET-01|VNET-02 |
|Spazio indirizzi della rete virtuale |10.0.10.0/23|10.0.20.0/23|
|Nome della subnet     |Subnet-01|Subnet-02|
|Intervallo di indirizzi subnet|10.0.10.0/24 |10.0.20.0/24 |
|Subnet gateway     |10.0.11.0/24|10.0.21.0/24|
|Indirizzo BGPNAT esterno     |         |         |

> [!NOTE]
> Gli indirizzi IP BGPNAT esterni nell'ambiente di esempio sono 10.16.167.195 per POC1 e 10.16.169.131 per POC2. Utilizzare la procedura seguente per determinare gli indirizzi IP BGPNAT esterni per gli host di Azure Stack Development Kit e quindi aggiungerli alla tabella di configurazione di rete precedente.


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Ottenere l'indirizzo IP della scheda esterna della macchina virtuale NAT
1. Accedere al computer fisico di Azure Stack per POC1.
2. Modificare il codice di Powershell seguente per sostituire la password dell'amministratore e quindi eseguire il codice nell'host POC:

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

4. Ripetere questa procedura in POC2.

## <a name="create-the-network-resources-in-poc1"></a>Creare le risorse di rete in POC1
È ora possibile creare le risorse di rete POC1 che è necessario impostare il gateway di. Le istruzioni seguenti illustrano come creare le risorse usando il portale utenti. È anche possibile usare codice di PowerShell per creare le risorse.

![Flusso di lavoro che viene usato per creare le risorse](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>Accedere come tenant
Un amministratore del servizio può accedere come tenant per testare i piani, offerte e sottoscrizioni che i tenant possono usare. Se non ne hai già uno, [creare un account tenant](azure-stack-add-new-user-aad.md) prima di accedere.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Creare la rete virtuale e la subnet della macchina virtuale
1. Usare un account tenant per accedere al portale per gli utenti.
2. Nel portale per gli utenti, selezionare **+ crea una risorsa**.
3. Passare a **Marketplace**, quindi selezionare **Networking**.
4. Selezionare **Rete virtuale**.
5. Per **Name**, **dello spazio di indirizzi**, **Subnet name**, e **intervallo di indirizzi Subnet**, usare i valori visualizzati in precedenza nella rete tabella di configurazione.
6. Nelle **sottoscrizione**, viene visualizzata la sottoscrizione creata in precedenza.
7. Per la **gruppo di risorse**, è possibile creare un gruppo di risorse o se già presente, selezionare **Usa esistente**.
8. Verificare la località predefinita.
9. Selezionare **Aggiungi al dashboard**.
10. Selezionare **Create**.

### <a name="create-the-gateway-subnet"></a>Creare la subnet del gateway
1. Nel dashboard aprire la risorsa di rete virtuale VNET-01 creato in precedenza.
2. Nel pannello **Impostazioni** selezionare **Subnet**.
3. Per aggiungere una subnet del gateway alla rete virtuale, selezionare **Subnet del Gateway**.
   
    ![Aggiungere la subnet del gateway](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Per impostazione predefinita, il nome della subnet è impostato **GatewaySubnet**.
   Subnet del gateway sono speciali. Per funzionare correttamente, devono usare la *GatewaySubnet* nome.
5. Nelle **intervallo indirizzi**, verificare che l'indirizzo sia **10.0.11.0/24**.
6. Selezionare **OK** per creare la subnet del gateway.

### <a name="create-the-virtual-network-gateway"></a>Creare il gateway di rete virtuale
1. Nel portale di Azure, selezionare **+ crea una risorsa**. 
2. Passare a **Marketplace**, quindi selezionare **Networking**.
3. Nell'elenco delle risorse di rete, selezionare **gateway di rete virtuale**.
4. Nelle **Name**, immettere **GW1**.
5. Selezionare il **rete virtuale** per scegliere una rete virtuale.
   Selezionare **VNET-01** dall'elenco.
6. Selezionare il **indirizzo IP pubblico** voce di menu. Quando la **Scegli indirizzo IP pubblico** verrà aperto il pannello selezionare **Crea nuovo**.
7. Nelle **Name**, immettere **GW1-PiP**, quindi selezionare **OK**.
8.  Per impostazione predefinita, per **tipo di VPN**, **basato su Route** sia selezionata.
    Mantenere il **basato su Route** tipo VPN.
9. Verificare che la **Sottoscrizione** e la **Località** siano corrette. È possibile aggiungere la risorsa al dashboard. Selezionare **Create**.

### <a name="create-the-local-network-gateway"></a>Creare il gateway di rete locale
L'implementazione di un *gateway di rete locale* in questa distribuzione di valutazione di Azure Stack è un po' diversa da quella di una distribuzione di Azure effettiva.

In una distribuzione di Azure, un gateway di rete locale rappresenta un dispositivo fisico locale (al livello di tenant), che consente di connettersi a un gateway di rete virtuale in Azure. In questa distribuzione di valutazione di Azure Stack, entrambe le estremità della connessione sono gateway di rete virtuale.

Un modo per pensare ciò più in generale è che la risorsa gateway di rete locale indica sempre il gateway remoto a altra estremità della connessione. A causa del modo che Azure Stack Development Kit è stato progettato, è necessario specificare l'indirizzo IP della scheda di rete esterni durante la conversione di indirizzo di rete (NAT) della macchina virtuale di altri Azure Stack Development Kit come l'indirizzo IP pubblico del gateway di rete locale. È quindi possibile creare i mapping NAT nella VM NAT per assicurarsi che entrambe le estremità siano collegate correttamente.


### <a name="create-the-local-network-gateway-resource"></a>Creare la risorsa gateway di rete locale
1. Accedere al computer fisico di Azure Stack per POC1.
2. Nel portale per gli utenti, selezionare **+ crea una risorsa**.
3. Passare a **Marketplace**, quindi selezionare **Networking**.
4. Nell'elenco delle risorse, selezionare **gateway di rete locale**.
5. Nelle **Name**, immettere **POC2-GW**.
6. Nelle **indirizzo IP**, immettere l'indirizzo esterno BGPNAT per POC2. Questo indirizzo viene visualizzato in precedenza nella tabella di configurazione di rete.
7. Nelle **spazio indirizzi**, per lo spazio degli indirizzi della rete virtuale POC2 che vengono creati successivamente, immettere **10.0.20.0/23**.
8. Verificare che il **abbonamento**, **gruppo di risorse**, e **posizione** siano corrette e quindi selezionare **Create**.

### <a name="create-the-connection"></a>Creare la connessione
1. Nel portale per gli utenti, selezionare **+ crea una risorsa**.
2. Passare a **Marketplace**, quindi selezionare **Networking**.
3. Nell'elenco delle risorse, selezionare **connessione**.
4. Nel **nozioni di base** pannello impostazioni per il **tipo di connessione**, selezionare **Site-to-site (IPSec)**.
5. Selezionare il **sottoscrizione**, **gruppo di risorse**, e **posizione**, quindi selezionare **OK**.
6. Nel **le impostazioni** blade, selezionare **gateway di rete virtuale**e quindi selezionare **GW1**.
7. Selezionare **gateway di rete locale**, quindi selezionare **POC2-GW**.
8. Nelle **nome connessione**, immettere **POC1-POC2**.
9. Nelle **chiave condivisa (PSK)**, immettere **12345**, quindi selezionare **OK**.
10. Nel **Summary** blade, selezionare **OK**.

### <a name="create-a-vm"></a>Creare una macchina virtuale
Per convalidare i dati che passano attraverso la connessione VPN, è necessario disporre di macchine virtuali per inviare e ricevere dati in ogni database di Azure Stack Development Kit. Creare ora una macchina virtuale in POC1 e quindi nella rete virtuale, inserirlo nella subnet VM.

1. Nel portale di Azure, selezionare **+ crea una risorsa**.
2. Passare a **Marketplace**, quindi selezionare **Compute**.
3. Nell'elenco di immagini di macchine virtuali, selezionare la **Windows Server 2016 Datacenter Eval** immagine.
4. Nel **nozioni di base** pannello nella **Name**, immettere **VM01**.
5. Immettere un nome utente e una password validi. Si usa questo account per accedere alla macchina virtuale dopo averla creata.
6. Fornire una **sottoscrizione**, **gruppo di risorse**, e **posizione**, quindi selezionare **OK**.
7. Nel **dimensioni** Pannello di tale istanza, selezionare una dimensione di macchina virtuale e quindi selezionare **selezionare**.
8. Nel **impostazioni** pannello, accettare le impostazioni predefinite. Verificare che il **VNET-01** sia selezionata la rete virtuale. Verificare che la subnet sia impostata su **10.0.10.0/24**. Selezionare **OK**.
9. Nel **Summary** blade, rivedere le impostazioni e quindi selezionare **OK**.



## <a name="create-the-network-resources-in-poc2"></a>Creare le risorse di rete in POC2

Il passaggio successivo consiste nel creare le risorse di rete per POC2. Le istruzioni seguenti illustrano come creare le risorse usando il portale utenti.

### <a name="sign-in-as-a-tenant"></a>Accedere come tenant
Un amministratore del servizio può accedere come tenant per testare i piani, offerte e sottoscrizioni che i tenant possono usare. Se non ne hai già uno, [creare un account tenant](azure-stack-add-new-user-aad.md) prima di accedere.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Creare la rete virtuale e la subnet della macchina virtuale

1. Accedere con un account tenant.
2. Nel portale per gli utenti, selezionare **+ crea una risorsa**.
3. Passare a **Marketplace**, quindi selezionare **Networking**.
4. Selezionare **Rete virtuale**.
5. Usare le informazioni visualizzate in precedenza nella tabella di configurazione di rete per identificare i valori per il POC2 **Name**, **dello spazio di indirizzi**, **nome Subnet**e **Intervallo di indirizzi subnet**.
6. Nelle **sottoscrizione**, viene visualizzata la sottoscrizione creata in precedenza.
7. Per la **gruppo di risorse**, creare un gruppo di risorse nuovo o, se è già presente, selezionare **Usa esistente**.
8. Verificare il valore predefinito **posizione**.
9. Selezionare **Aggiungi al dashboard**.
10. Selezionare **Create**.

### <a name="create-the-gateway-subnet"></a>Creare la subnet del gateway
1. Aprire la risorsa rete virtuale creata (**VNET-02**) dal dashboard.
2. Nel pannello **Impostazioni** selezionare **Subnet**.
3. Selezionare **subnet del Gateway** per aggiungere una subnet del gateway alla rete virtuale.
4. Il nome predefinito della subnet è **GatewaySubnet**.
   Questo nome specifico è necessario per il corretto funzionamento delle subnet del gateway.
5. Nel **intervallo indirizzi** campo, verificare l'indirizzo sia **10.0.21.0/24**.
6. Selezionare **OK** per creare la subnet del gateway.

### <a name="create-the-virtual-network-gateway"></a>Creare il gateway di rete virtuale
1. Nel portale di Azure, selezionare **+ crea una risorsa**.  
2. Passare a **Marketplace**, quindi selezionare **Networking**.
3. Nell'elenco delle risorse di rete, selezionare **gateway di rete virtuale**.
4. Nelle **Name**, immettere **GW2**.
5. Per scegliere una rete virtuale, selezionare **rete virtuale**. Quindi selezionare **VNET-02** dall'elenco.
6. Selezionare **Indirizzo IP pubblico**. Quando la **Scegli indirizzo IP pubblico** verrà aperto il pannello selezionare **Crea nuovo**.
7. Nelle **Name**, immettere **GW2-PiP**, quindi selezionare **OK**.
8. Per impostazione predefinita, per **tipo di VPN**, **basato su Route** sia selezionata.
    Mantenere il **basato su Route** tipo VPN.
9. Verificare che la **Sottoscrizione** e la **Località** siano corrette. È possibile aggiungere la risorsa al dashboard. Selezionare **Create**.

### <a name="create-the-local-network-gateway-resource"></a>Creare la risorsa gateway di rete locale

1. Nel portale per gli utenti POC2, selezionare **+ crea una risorsa**. 
4. Passare a **Marketplace**, quindi selezionare **Networking**.
5. Nell'elenco delle risorse, selezionare **gateway di rete locale**.
6. Nelle **Name**, immettere **POC1-GW**.
7. Nelle **indirizzo IP**, immettere l'indirizzo esterno BGPNAT per POC1 elencato in precedenza nella tabella di configurazione di rete.
8. Nelle **spazio degli indirizzi**, da POC1, immettere il **10.0.10.0/23** spazio di indirizzi **VNET-01**.
9. Verificare che il **abbonamento**, **gruppo di risorse**, e **posizione** siano corrette e quindi selezionare **Create**.

## <a name="create-the-connection"></a>Creare la connessione
1. Nel portale per gli utenti, selezionare **+ crea una risorsa**. 
2. Passare a **Marketplace**, quindi selezionare **Networking**.
3. Nell'elenco delle risorse, selezionare **connessione**.
4. Nel **base** pannello impostazioni per il **tipo di connessione**, scegliere **Site-to-site (IPSec)**.
5. Selezionare il **sottoscrizione**, **gruppo di risorse**, e **posizione**, quindi selezionare **OK**.
6. Nel **le impostazioni** blade, selezionare **gateway di rete virtuale**e quindi selezionare **GW2**.
7. Selezionare **gateway di rete locale**, quindi selezionare **POC1-GW**.
8. Nelle **nome connessione**, immettere **POC2-POC1**.
9. Nelle **chiave condivisa (PSK)**, immettere **12345**. Se si sceglie un valore diverso, tenere presente che l'it *necessario* corrisponde al valore della chiave condivisa creata in POC1. Selezionare **OK**.
10. Rivedere le **Summary** pannello e quindi selezionare **OK**.

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale
Creare una macchina virtuale in POC2, ora e inserirlo nella subnet VM nella rete virtuale.

1. Nel portale di Azure, selezionare **+ crea una risorsa**.
2. Passare a **Marketplace**, quindi selezionare **Compute**.
3. Nell'elenco di immagini di macchine virtuali, selezionare la **Windows Server 2016 Datacenter Eval** immagine.
4. Nel **nozioni di base** pannello per **Name**, immettere **VM02**.
5. Immettere un nome utente e una password validi. Utilizzare questo account per accedere alla macchina virtuale dopo averla creata.
6. Fornire una **sottoscrizione**, **gruppo di risorse**, e **posizione**, quindi selezionare **OK**.
7. Nel **dimensioni** blade, selezionare una macchina virtuale di dimensioni per questa istanza e quindi selezionare **seleziona**.
8. Nel **impostazioni** pannello, è possibile accettare i valori predefiniti. Verificare che il **VNET-02** sia selezionata la rete virtuale e verificare che la subnet sia impostata su **10.0.20.0/24**. Selezionare **OK**.
9. Verificare le impostazioni nella **Summary** pannello e quindi selezionare **OK**.

## <a name="configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal"></a>Configurare la macchina virtuale NAT in ogni Azure Stack Development Kit per l'attraversamento gateway
Perché Azure Stack Development Kit è indipendente e isolato dalla rete in cui viene distribuito l'host fisico, il *esterno* rete con indirizzi VIP che sono connessi i gateway non è veramente esterna. Al contrario, la rete VIP è nascosta dietro un router che esegue la conversione degli indirizzi di rete. 

Il router è una macchina virtuale di Windows Server, chiamata *AzS-bgpnat01*, che esegue il ruolo di Routing e accesso remoto (RRAS) nell'infrastruttura di Azure Stack Development Kit. È necessario configurare NAT nella macchina virtuale per consentire la connessione VPN site-to-site per connettersi a entrambe le estremità AzS-bgpnat01. 

Per configurare la connessione VPN, è necessario creare una route di mapping NAT statica che esegue il mapping di interfaccia esterna nella macchina virtuale BGPNAT all'indirizzo VIP del Pool di Gateway Edge. Una route di mapping NAT statica è necessaria per ogni porta in una connessione VPN.

> [!NOTE]
> Questa configurazione è necessaria solo per gli ambienti Azure Stack Development Kit.
> 
> 

### <a name="configure-the-nat"></a>Configurare la NAT
> [!IMPORTANT]
> È necessario completare questa procedura per *entrambi* ambienti Azure Stack Development Kit.

1. Determinare il **indirizzo IP interno** da usare nello script di PowerShell seguente. Apre il gateway di rete virtuale (GW1 e GW2) e quindi scegliere il **Panoramica** blade, salvare il valore per il **indirizzo IP pubblico** per un uso successivo.
![Indirizzo IP interno](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)
2. Accedere al computer fisico di Azure Stack per POC1.
3. Copiare e modificare lo script di PowerShell seguente. Per configurare la NAT in ogni database di Azure Stack Development Kit, eseguire lo script in un'istanza di PowerShell ISE Windows con privilegi elevati. Nello script, aggiungere i valori per il *indirizzi esterni BGPNAT* e *indirizzo IP interno* segnaposto:

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

4. Ripetere questa procedura in POC2.

## <a name="test-the-connection"></a>Testare la connessione
Ora che viene stabilita la connessione site-to-site, è necessario convalidare che è possibile ottenere il traffico passa attraverso di esso. Per convalidare, accedere a una delle macchine virtuali che è stato creato in entrambi gli ambienti Azure Stack Development Kit. Quindi, eseguire il ping di macchina virtuale creata in altro ambiente. 

Per garantire che si invia il traffico attraverso la connessione site-to-site, assicurarsi che il ping dell'indirizzo IP diretto (DIP) della macchina virtuale nella subnet remota, non l'indirizzo VIP. A tale scopo, trovare l'indirizzo DIP a altra estremità della connessione. Salvare l'indirizzo per un uso successivo.

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>Accedere al tenant di macchina virtuale in POC1
1. Accedi al computer fisico di Azure Stack per POC1 e quindi usare un account tenant per accedere al portale per gli utenti.
2. Nella barra di spostamento a sinistra, selezionare **calcolo**.
3. Nell'elenco delle macchine virtuali, individuare **VM01** creato in precedenza e quindi selezionarlo.
4. Nel pannello per la macchina virtuale, fare clic su **Connect**e quindi aprire il file VM01.rdp.
   
     ![Pulsante di connessione](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Accedere con l'account configurato al momento della creazione della macchina virtuale.
6. Aprire con privilegi elevati **Windows PowerShell** finestra.
7. Immettere **ipconfig /all**.
8. Nell'output trovare la **indirizzo IPv4**e quindi salvare l'indirizzo per un uso successivo. Questo è l'indirizzo a cui effettuerà il ping da POC2. Nell'ambiente di esempio è **10.0.10.4**, ma quello effettivo potrebbe essere diverso. Devono rientrare la **10.0.10.0/24** subnet creata in precedenza.
9. Per creare una regola firewall che consenta la macchina virtuale rispondere ai ping, eseguire il comando PowerShell seguente:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>Accedi per il macchina virtuale tenant in POC2
1. Accedi al computer fisico di Azure Stack per POC2 e quindi usare un account tenant per accedere al portale per gli utenti.
2. Nella barra di spostamento a sinistra, fare clic su **calcolo**.
3. Dall'elenco di macchine virtuali, individuare **VM02** creato in precedenza e quindi selezionarlo.
4. Nel pannello della macchina virtuale fare clic su **Connetti**.
5. Accedere con l'account configurato al momento della creazione della macchina virtuale.
6. Aprire con privilegi elevati **Windows PowerShell** finestra.
7. Immettere **ipconfig /all**.
8. Dovrebbe essere un indirizzo IPv4 compreso entro **10.0.20.0/24**. Nell'ambiente di esempio, l'indirizzo viene **10.0.20.4**, ma l'indirizzo potrebbe essere diverso.
9. Per creare una regola firewall che consenta la macchina virtuale rispondere ai ping, eseguire il comando PowerShell seguente:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. Dalla macchina virtuale in POC2, effettuare il ping di macchina virtuale in POC1, attraverso il tunnel. A tale scopo, eseguire il ping il DIP che è registrato da VM01.
   Nell'ambiente di esempio, si tratta **10.0.10.4**, ma è meglio il ping dell'indirizzo a cui si è preso nota nel lab. Si verrà visualizzato un risultato simile a quanto segue:
   
    ![Ping con esito positivo](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. Una risposta della macchina virtuale remota indica un test riuscito! È possibile chiudere la finestra della macchina virtuale. Per testare la connessione, è possibile provare altri tipi di trasferimenti di dati, ad esempio una copia del file.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Visualizzazione delle statistiche di trasferimento dati attraverso la connessione del gateway
Se si desidera sapere quanti dati vengono passati attraverso la connessione site-to-site, queste informazioni sono disponibili nel **connessione** pannello. Questo test è anche un altro modo per verificare che il ping che appena inviato sia passato effettivamente la connessione VPN.

1. Mentre è connessi alla macchina virtuale tenant in POC2, usare l'account tenant per accedere al portale per gli utenti.
2. Passare a **tutte le risorse**, quindi selezionare la **POC2-POC1** connessione. **Le connessioni** viene visualizzata.
4. Nel **connessione** blade, le statistiche per **dati nel** e **dati in uscita** vengono visualizzati. Nello screenshot seguente, i numeri di grandi dimensioni vengono attribuiti al trasferimento di file aggiuntivi. Dovrebbe essere presenti alcuni valori diversi da zero.
   
    ![Dati in ingresso e in uscita](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
