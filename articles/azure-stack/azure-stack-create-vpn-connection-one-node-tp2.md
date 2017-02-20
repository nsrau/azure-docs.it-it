---
title: Creare una connessione VPN da sito a sito tra due reti virtuali in ambienti diversi del modello di verifica di Azure Stack | Documentazione Microsoft
description: Procedura dettagliata che consente a un amministratore cloud di creare una connessione VPN da sito a sito tra due ambienti del modello di verifica a un nodo in TP2.
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
ms.date: 02/08/2017
ms.author: scottnap
translationtype: Human Translation
ms.sourcegitcommit: 5104c7996de9dc0597e65be31c28a9aaa1243a90
ms.openlocfilehash: d324290caf5b5a085a2daf67e541c295dffda732


---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-poc-environments"></a>Creare una connessione VPN da sito a sito tra due reti virtuali in ambienti diversi del modello di verifica di Azure Stack
## <a name="overview"></a>Panoramica
Questo articolo illustra come creare una connessione VPN da sito a sito tra due reti virtuali in due ambienti separati del modello di verifica (PoC) di Azure Stack. Configurando le connessioni si apprenderà il funzionamento dei gateway VPN in Azure Stack.

> [!NOTE]
> Questo documento si applica in particolare al modello di verifica TP2 di Azure Stack.
> 
> 

### <a name="connection-diagram"></a>Diagramma di connessione
Il diagramma seguente illustra l'aspetto che dovrebbe avere la configurazione al termine della procedura:

![](media/azure-stack-create-vpn-connection-one-node-tp2/image1.png)

### <a name="before-you-begin"></a>Prima di iniziare
Per completare la configurazione, assicurarsi di avere a disposizione quanto segue prima di iniziare:

* Due server che soddisfano i requisiti hardware PoC di Azure Stack e gli altri prerequisiti definiti in [Azure Stack Deployment Prerequisites](azure-stack-deploy.md) (Prerequisiti per la distribuzione di Azure Stack).
* Pacchetto di distribuzione Azure Stack Technical Preview 2.

## <a name="deploy-the-poc-environments"></a>Distribuire gli ambienti del modello di verifica
Per completare questa configurazione si distribuiranno due ambienti del modello di verifica di Azure Stack.

* Per ogni modello di verifica da distribuire, è possibile seguire le istruzioni di distribuzione dettagliate contenute nell'articolo [Distribuire Azure Stack POC](azure-stack-run-powershell-script.md).
  In questo documento si fa riferimento in modo generico agli ambienti del modello di verifica con i nomi *POC1* e *POC2*.

## <a name="configure-quotas-for-compute-network-and-storage"></a>Configurare le quote per il calcolo, la rete e l'archiviazione
In primo luogo, è necessario configurare le *quote* per il calcolo, la rete e l'archiviazione. Tali servizi possono essere associati a un *piano* e quindi a un'*offerta* che i tenant possono sottoscrivere.

> [!NOTE]
> È necessario eseguire questa procedura per ogni ambiente del modello di verifica di Azure Stack.
> 
> 

La creazione di quote per i servizi è diversa rispetto a TP1. La procedura per creare le quote in TP2 è disponibile alla pagina <http://aka.ms/mas-create-quotas>. Per questo esercizio è possibile accettare i valori predefiniti per tutte le impostazioni delle quote.

## <a name="create-a-plan-and-offer"></a>Creare un piano e un'offerta
I [piani](azure-stack-key-features.md) sono raggruppamenti di uno o più servizi. Il provider può creare i piani da offrire ai tenant. A loro volta, i tenant sottoscrivono le offerte per usare i piani e i servizi inclusi.

> [!NOTE]
> È necessario eseguire questa procedura per ogni ambiente del modello di verifica di Azure Stack.
> 
> 

1. Prima di tutto, creare un piano. A tale scopo, è possibile seguire la procedura riportata nell'articolo [Creare un piano in Azure Stack](azure-stack-create-plan.md).
2. Creare un'offerta seguendo la procedura descritta in [Creare un'offerta in Azure Stack](azure-stack-create-offer.md).
3. Accedere al portale come amministratore tenant e [sottoscrivere l'offerta creata](azure-stack-subscribe-plan-provision-vm.md).

## <a name="create-the-network-resources-in-poc-1"></a>Creare le risorse di rete in POC1
Ora si creeranno le risorse effettive necessarie per impostare la configurazione. La procedura seguente descrive le operazioni da eseguire. Queste istruzioni illustrano come creare risorse con il portale, ma è possibile ottenere lo stesso risultato usando PowerShell.

![](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="log-in-as-a-tenant"></a>Accedere come tenant
Un amministratore del servizio può accedere come tenant per testare i piani, le offerte e le sottoscrizioni che i tenant possono usare. Se non è già disponibile, [creare un account tenant](azure-stack-add-new-user-aad.md) prima di accedere.

### <a name="create-the-virtual-network--vm-subnet"></a>Creare la rete virtuale e la subnet della macchina virtuale
1. Accedere usando un account tenant.
2. Nel portale di Azure fare clic su **Nuovo**.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)
3. Selezionare **Rete** dal menu Marketplace.
4. Fare clic sulla voce di menu **Rete virtuale**.
5. Fare clic su **Crea** nella parte inferiore del pannello di descrizione della risorsa. Immettere i valori seguenti nei campi appropriati in base a questa tabella.
   
   | **Campo** | **Valore** |
   | --- | --- |
   | Nome |vnet-01 |
   | Spazio degli indirizzi |10.0.10.0/23 |
   | Nome della subnet |subnet-01 |
   | Intervallo di indirizzi subnet |10.0.10.0/24 |
6. La sottoscrizione creata in precedenza verrà popolata nel campo **Sottoscrizione**.
7. Per quanto riguarda il gruppo di risorse, è possibile crearne uno nuovo o selezionare **Usa esistente**, se è già disponibile un gruppo di risorse.
8. Verificare la località predefinita.
9. Fare clic su **Crea**.

### <a name="create-the-gateway-subnet"></a>Creare la subnet del gateway
1. Aprire la risorsa rete virtuale appena creata (vnet-01) dal dashboard.
2. Nel pannello Impostazioni selezionare **Subnet**.
3. Fare clic su **Subnet del gateway** per aggiungere una subnet del gateway alla rete virtuale.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)
4. Il nome predefinito della subnet è **GatewaySubnet**.
   Questo nome specifico è necessario per il corretto funzionamento delle subnet del gateway.
5. Nel campo **Intervallo di indirizzi** digitare **10.0.11.0/24**.
6. Fare clic su **Crea** per creare la subnet del gateway.

### <a name="create-the-virtual-network-gateway"></a>Creare il gateway di rete virtuale
1. Nel portale di Azure fare clic su **Nuovo**.
   
2. Selezionare **Rete** dal menu Marketplace.
3. Selezionare **Gateway di rete virtuale** dall'elenco di risorse di rete.
4. Esaminare la descrizione e fare clic su **Crea**.
5. Nel campo **Nome** digitare **GW1**.
6. Fare clic su **Rete virtuale** per scegliere una rete virtuale.
   Selezionare **vnet-01** dall'elenco.
7. Fare clic sulla voce di menu **Indirizzo IP pubblico**. Quando viene visualizzato il pannello **Scegli indirizzo IP pubblico**, fare clic su **Crea nuovo**.
8. Nel campo **Nome** digitare **GW1-PiP** e fare clic su **OK**.
9. Per **Tipo di gateway** deve essere selezionata l'opzione **VPN** per impostazione predefinita. Mantenere questa impostazione.
10. Per **Tipo VPN** deve essere selezionata l'opzione **Basato su route** per impostazione predefinita.
    Mantenere questa impostazione.
11. Verificare che la **Sottoscrizione** e la **Località** siano corrette. Volendo, è possibile aggiungere la risorsa al dashboard. Fare clic su **Crea**.

### <a name="create-the-local-network-gateway"></a>Creare il gateway di rete locale
L'implementazione di un *gateway di rete locale* in questa distribuzione di valutazione di Azure Stack è un po' diversa da quella di una distribuzione di Azure effettiva.

Come in Azure, è necessario il concetto di gateway di rete locale. Tuttavia, in una distribuzione di Azure il gateway di rete locale è un dispositivo fisico locale, al livello di tenant, usato per connettersi a un gateway di rete virtuale in Azure. In questa distribuzione di valutazione di Azure Stack, invece, entrambe le estremità della connessione sono gateway di rete virtuali.

Più in generale, la risorsa gateway di rete locale deve sempre indicare il gateway remoto all'altra estremità della connessione. Il modello di verifica è progettato in modo che sia necessario specificare l'indirizzo IP della scheda di rete esterna nella macchina virtuale NAT dell'altro modello di verifica come indirizzo IP pubblico del gateway di rete locale. Si creeranno quindi i mapping NAT alla macchina virtuale NAT per assicurarsi che entrambe le estremità siano collegate correttamente.


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Ottenere l'indirizzo IP della scheda esterna della macchina virtuale NAT
1. Accedere al computer fisico di Azure Stack per POC2.
2. Premere [tasto WINDOWS]+R per aprire il menu **Esegui**, digitare **mstsc** e premere INVIO.
3. Nel campo **Computer** digitare il nome **MAS-BGPNAT01** e fare clic su  **Connetti**.
4. Fare clic sul menu Start, quindi fare clic con il pulsante destro del mouse su **Windows PowerShell** e scegliere **Esegui come amministratore**.
5. Digitare **ipconfig /all**.
6. Trovare la scheda Ethernet connessa alla rete locale e prendere nota dell'indirizzo IPv4 associato a tale scheda. Nell'ambiente di esempio è **10.16.167.195**, ma quello effettivo sarà diverso.
7. Registrare tale indirizzo. Verrà usato come indirizzo IP pubblico della risorsa gateway di rete locale creata in POC1.

### <a name="create-the-local-network-gateway-resource"></a>Creare la risorsa gateway di rete locale
1. Accedere al computer fisico di Azure Stack per POC1.
2. Nel campo **Computer** digitare il nome **MAS-CON01** e fare clic su **Connetti**.
3. Nel portale di Azure fare clic su **Nuovo**.
   
4. Selezionare **Rete** dal menu Marketplace.
5. Selezionare **Gateway di rete locale** dall'elenco di risorse.
6. Nel campo **Nome** digitare **POC2-GW**.
7. Non si conosce ancora l'indirizzo IP dell'altro gateway, ma sarà possibile tornare a modificarlo in seguito. Per il momento, digitare **10.16.167.195** nel campo **Indirizzo IP**.
8. Nel campo **Spazio di indirizzi** digitare lo spazio degli indirizzi della rete virtuale che verrà creata in POC2, ovvero **10.0.20.0/23**. Digitare quindi tale valore.
9. Verificare che la **Sottoscrizione**, il **Gruppo di risorse** e la **Località** siano corretti e fare clic su **Crea**.

### <a name="create-the-connection"></a>Creare la connessione
1. Nel portale di Azure fare clic su **Nuovo**.
   
2. Selezionare **Rete** dal menu Marketplace.
3. Selezionare **Connessione** dall'elenco di risorse.
4. Nel pannello delle impostazioni **Basic** scegliere **Da sito a sito (IPSec)** come **Tipo di connessione**.
5. Selezionare la **Sottoscrizione**, il **Gruppo di risorse** e la **Località** e fare clic su **OK**.
6. Nel pannello **Impostazioni** scegliere il **gateway di rete virtuale** (**GW1**) creato in precedenza.
7. Scegliere il **gateway di rete locale** (**POC2-GW**) creato in precedenza.
8. Nel campo **Nome connessione** digitare **POC1-POC2**.
9. Nel campo **Chiave condivisa (PSK)** digitare **12345** e fare clic su **OK**.

### <a name="create-a-vm"></a>Creare una macchina virtuale
Per convalidare i dati che passano attraverso la connessione VPN, è necessario che le macchine virtuali inviino e ricevano dati in ogni modello di verifica. Creare una macchina virtuale in POC1 e inserirla nella subnet VM della rete virtuale.

1. Nel portale di Azure fare clic su **Nuovo**.
   
2. Selezionare **Macchine virtuali** dal menu Marketplace.
3. Nell'elenco di immagini di macchine virtuali, selezionare l'immagine **Windows Server 2012 R2 Datacenter**.
4. Nel pannello **Informazioni di base** digitare **VM01** nel campo **Nome**.
5. Immettere un nome utente e una password validi. Questo account verrà usato per accedere alla macchina virtuale, dopo averla creata.
6. Specificare la **Sottoscrizione**, il **Gruppo di risorse** e la **Località** e fare clic su **OK**.
7. Nel pannello **Dimensione** scegliere una dimensione di macchina virtuale per questa istanza e quindi fare clic su **Seleziona**.
8. Nel pannello **Impostazioni** è possibile accettare le impostazioni predefinite. È sufficiente assicurarsi che la rete virtuale selezionata sia **vnet-01** e che la subnet sia impostata su **10.0.10.0/24**. Fare clic su **OK**.
9. Esaminare le impostazioni nel pannello **Riepilogo** e fare clic su **OK**.

## <a name="create-the-network-resources-in-poc-2"></a>Creare le risorse di rete in POC2
### <a name="log-in-as-a-tenant"></a>Accedere come tenant
Un amministratore del servizio può accedere come tenant per testare i piani, le offerte e le sottoscrizioni che i tenant possono usare. Se non è già disponibile, [creare un account tenant](azure-stack-add-new-user-aad.md) prima di accedere.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Creare la rete virtuale e la subnet della macchina virtuale
1. Accedere usando un account tenant.
2. Nel portale di Azure fare clic su **Nuovo**.
   
3. Selezionare **Rete** dal menu Marketplace.
4. Fare clic su **Rete virtuale** nel menu.
5. Fare clic su **Crea** nella parte inferiore del pannello di descrizione della risorsa. Immettere i valori seguenti nei campi appropriati in base a questa tabella.
   
   | **Campo** | **Valore** |
   | --- | --- |
   | Nome |vnet-02 |
   | Spazio degli indirizzi |10.0.20.0/23 |
   | Nome della subnet |subnet-02 |
   | Intervallo di indirizzi subnet |10.0.20.0/24 |
6. La sottoscrizione creata in precedenza verrà popolata nel campo **Sottoscrizione**.
7. Per il gruppo di risorse, è possibile crearne uno nuovo o selezionare **Usa esistente**, se è già disponibile un gruppo di risorse.
8. Verificare la **località** predefinita. Volendo, è possibile aggiungere la rete virtuale al dashboard per accedervi più facilmente.
9. Fare clic su **Create**(Crea).

### <a name="create-the-gateway-subnet"></a>Creare la subnet del gateway
1. Aprire la risorsa rete virtuale appena creata (**vnet-02**) dal dashboard.
2. Nel pannello **Impostazioni** selezionare **Subnet**.
3. Fare clic su **Subnet del gateway** per aggiungere una subnet del gateway alla rete virtuale.
   
4. Il nome predefinito della subnet è **GatewaySubnet**.
   Questo nome specifico è necessario per il corretto funzionamento delle subnet del gateway.
5. Nel campo **Intervallo di indirizzi** digitare **10.0.20.0/24**.
6. Fare clic su **Crea** per creare la subnet del gateway.

### <a name="create-the-virtual-network-gateway"></a>Creare il gateway di rete virtuale
1. Nel portale di Azure fare clic su **Nuovo**.
   
2. Selezionare **Rete** dal menu Marketplace.
3. Selezionare **Gateway di rete virtuale** dall'elenco di risorse di rete.
4. Esaminare la descrizione e fare clic su **Crea**.
5. Nel campo **Nome** digitare **GW2**.
6. Fare clic su **Rete virtuale** per scegliere una rete virtuale.
   Selezionare **vnet-02** dall'elenco.
7. Fare clic su **Indirizzo IP pubblico**. Quando viene visualizzato il pannello **Scegli indirizzo IP pubblico**, fare clic su **Crea nuovo**.
8. Nel campo **Nome** digitare **GW2-PiP** e fare clic su **OK**.
9. Per **Tipo di gateway** deve essere selezionata l'opzione **VPN** per impostazione predefinita. Mantenere questa impostazione.
10. Per **Tipo VPN** deve essere selezionata l'opzione **Basato su route** per impostazione predefinita.
    Mantenere questa impostazione.
11. Verificare che la **Sottoscrizione** e la **Località** siano corrette. Volendo, è possibile aggiungere la risorsa al dashboard. Fare clic su **Crea**.

### <a name="create-the-local-network-gateway"></a>Creare il gateway di rete locale
#### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Ottenere l'indirizzo IP della scheda esterna della macchina virtuale NAT
1. Accedere al computer fisico di Azure Stack per POC1.
2. Tenere premuto [tasto WINDOWS]+R per aprire il menu **Esegui**, digitare **mstsc** e premere INVIO.
3. Nel campo **Computer** digitare il nome **MAS-BGPNAT01** e fare clic su  **Connetti**.
4. Fare clic sul menu Start, quindi fare clic con il pulsante destro del mouse su **Windows PowerShell** e scegliere **Esegui come amministratore**.
5. Digitare **ipconfig /all**.
6. Trovare la scheda Ethernet connessa alla rete locale e prendere nota dell'indirizzo IPv4 associato a tale scheda. Nell'ambiente di esempio è **10.16.169.131**, ma quello effettivo sarà diverso.
7. Registrare tale indirizzo. Verrà usato successivamente come indirizzo IP pubblico della risorsa gateway di rete locale creata in POC1.

#### <a name="create-the-local-network-gateway-resource"></a>Creare la risorsa gateway di rete locale
1. Accedere al computer fisico di Azure Stack per POC2.
2. Nel campo **Computer** digitare il nome **MAS-CON01** e fare clic su **Connetti**.
3. Nel portale di Azure fare clic su **Nuovo**.
   
4. Selezionare **Rete** dal menu Marketplace.
5. Selezionare **Gateway di rete locale** dall'elenco di risorse.
6. Nel campo **Nome** digitare **POC1-GW**.
7. A questo punto è necessario l'indirizzo IP pubblico registrato per il gateway di rete virtuale in POC1. Digitare **10.16.169.131** nel campo **Indirizzo IP**.
8. Nel campo **Spazio di indirizzi** digitare lo spazio degli indirizzi di **vnet-01** da POC1, ovvero **10.0.0.0/16**.
9. Verificare che la **Sottoscrizione**, il **Gruppo di risorse** e la **Località** siano corretti e fare clic su **Crea**.

## <a name="create-the-connection"></a>Creare la connessione
1. Nel portale di Azure fare clic su **Nuovo**.
   
2. Selezionare **Rete** dal menu Marketplace.
3. Selezionare **Connessione** dall'elenco di risorse.
4. Nel pannello delle impostazioni **Basic** scegliere **Da sito a sito (IPSec)** come **Tipo di connessione**.
5. Selezionare la **Sottoscrizione**, il **Gruppo di risorse** e la **Località** e fare clic su **OK**.
6. Nel pannello **Impostazioni** scegliere il **gateway di rete virtuale** (**GW1**) creato in precedenza.
7. Scegliere il **gateway di rete locale** (**POC1-GW**) creato in precedenza.
8. Nel campo **Nome connessione** digitare **POC2-POC1**.
9. Nel campo **Chiave condivisa (PSK)** digitare **12345**. Se si sceglie un valore diverso, tenere presente che DEVE corrispondere al valore della chiave condivisa creata in POC1. Fare clic su **OK**.

## <a name="create-a-vm"></a>Creare una macchina virtuale
Creare una macchina virtuale in POC1 e inserirla nella subnet VM della rete virtuale.

1. Nel portale di Azure fare clic su **Nuovo**.
   
2. Selezionare **Macchine virtuali** dal menu Marketplace.
3. Nell'elenco di immagini di macchine virtuali, selezionare l'immagine **Windows Server 2012 R2 Datacenter**.
4. Nel pannello **Informazioni di base** digitare **VM02** nel campo **Nome**.
5. Immettere un nome utente e una password validi. Questo account verrà usato per accedere alla macchina virtuale, dopo averla creata.
6. Specificare la **Sottoscrizione**, il **Gruppo di risorse** e la **Località** e fare clic su **OK**.
7. Nel pannello **Dimensione** scegliere una dimensione di macchina virtuale per questa istanza e quindi fare clic su **Seleziona**.
8. Nel pannello Impostazioni è possibile accettare le impostazioni predefinite. È sufficiente assicurarsi che la rete virtuale selezionata sia **vnet-02** e che la subnet sia impostata su **20.0.0.0/24**. Fare clic su **OK**.
9. Esaminare le impostazioni nel pannello **Riepilogo** e fare clic su **OK**.

## <a name="configure-the-nat-vm-in-each-poc-for-gateway-traversal"></a>Configurare la macchina virtuale NAT in ogni modello di verifica per l'attraversamento gateway
Dato che il modello di verifica è progettato per essere indipendente e isolato dalla rete in cui viene distribuito l'host fisico, la rete dell'indirizzo VIP "esterna" a cui sono connessi i gateway non è veramente esterna, ma è nascosta dietro un router che esegue la Network Address Translation (NAT). Il router è in realtà una macchina virtuale Windows Server (**MAS-BGPNAT01**) che esegue il ruolo Servizio Routing e Accesso remoto (RRAS) nell'infrastruttura del modello di verifica. Per consentire alla connessione VPN da sito a sito di connettersi a entrambe le estremità, è necessario configurare la NAT nella macchina virtuale MAS-BGPNAT01. A tale scopo, è necessario creare un mapping NAT statico che esegue il mapping dell'interfaccia esterna della macchina virtuale BGPNAT all'indirizzo VIP del gateway del pool di server perimetrali per le porte necessarie per una connessione VPN.

> [!NOTE]
> Questa configurazione è necessaria solo per ambienti del modello di verifica.
> 
> 

### <a name="configure-nat"></a>Configurare la NAT
È necessario seguire questa procedura in ENTRAMBI gli ambienti del modello di verifica.

1. Accedere al computer fisico di Azure Stack per POC1.
2. Tenere premuto [tasto WINDOWS]+R per aprire il menu **Esegui**, digitare **mstsc** e premere **INVIO**.
3. Nel campo **Computer** digitare il nome **MAS-BGPNAT01** e fare clic su **Connetti**.
4. Fare clic sul menu Start, quindi fare clic con il pulsante destro del mouse su **Windows PowerShell** e scegliere **Esegui come amministratore**.
5. Digitare **ipconfig /all**.
6. Trovare la scheda Ethernet connessa alla rete locale e prendere nota dell'indirizzo IPv4 associato a tale scheda. Nell'ambiente di esempio è **10.16.169.131**, contrassegnato con un cerchio rosso nella figura seguente, ma quello effettivo sarà diverso.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image16.png)
7. Digitare il comando di PowerShell seguente per definire l'indirizzo NAT esterno per le porte con autenticazione IKE. Ricordarsi di sostituire l'indirizzo IP con quello corrispondente all'ambiente usato.
   
       Add-NetNatExternalAddress -NatName BGPNAT -IPAddress 10.16.169.131 -PortStart 499 -PortEnd 501
8. Creare quindi un mapping NAT statico per eseguire il mapping dell'indirizzo esterno all'indirizzo IP pubblico del gateway, per associare la porta 500 ISAKMP per la fase 1 del tunnel IPSec.
   
        Add-NetNatStaticMapping -NatName BGPNAT -Protocol UDP -ExternalIPAddress 10.16.169.131 -InternalIPAddress 192.168.102.1 -ExternalPort 500 -InternalPort 500
> [!NOTE] 
> Il parametro `-InternalAddress` in questo caso è l'indirizzo IP pubblico del gateway di rete virtuale creato in precedenza.  Per trovare il valore dell'indirizzo IP pubblico, vedere le proprietà del pannello Gateway di rete virtuale.       

9. Infine, è necessario configurare l'attraversamento NAT che usa la porta 4500 per stabilire il tunnel IPSec completo nei dispositivi NAT.
   
        Add-NetNatStaticMapping -NatName BGPNAT -Protocol UDP -ExternalIPAddress 10.16.169.131 -InternalIPAddress 192.168.102.1 -ExternalPort 4500 -InternalPort 4500
> [!NOTE] 
> Il parametro `-InternalAddress` in questo caso è l'indirizzo IP pubblico del gateway di rete virtuale creato in precedenza.  Per trovare il valore dell'indirizzo IP pubblico, vedere le proprietà del pannello Gateway di rete virtuale.       

10. Ripetere i passaggi da 1 a 9 in POC2.

## <a name="test-the-connection"></a>Testare la connessione
Ora che è stata stabilita la connessione da sito a sito, è necessario verificare che il flusso del traffico possa attraversarla. A tale scopo, è sufficiente accedere a una delle macchine virtuali create in uno degli ambienti del modello di verifica ed effettuare il ping della macchina virtuale creata nell'altro ambiente. Per assicurarsi che il traffico attraversi la connessione da sito a sito, effettuare il ping dell'indirizzo IP diretto (DIP) della macchina virtuale nella subnet remota, non dell'indirizzo VIP. A tale scopo, è necessario trovare e prendere nota dell'indirizzo all'altra estremità della connessione.

### <a name="log-in-to-the-tenant-vm-in-poc1"></a>Accedere alla macchina virtuale tenant in POC1
1. Accedere al computer fisico di Azure Stack per POC1 e accedere al portale con un account tenant.
2. Nel riquadro di spostamento a sinistra fare clic su **Macchine virtuali**.
3. Trovare la **VM01** creata in precedenza nell'elenco di macchine virtuali e selezionarla.
4. Nel pannello della macchina virtuale fare clic su **Connetti**.
   
     ![](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Aprire un prompt dei comandi dall'interno della macchina virtuale e digitare **ipconfig /all**.
6. Trovare l'**indirizzo IPv4** nell'output e prenderne nota. Si tratta dell'indirizzo a cui si effettuerà il ping da POC2. Nell'ambiente di esempio è **10.0.10.4**, ma quello effettivo potrebbe essere diverso. Deve tuttavia rientrare nell'intervallo della subnet **10.0.10.0/24** creata in precedenza.

### <a name="log-in-to-the-tenant-vm-in-poc2"></a>Accedere alla macchina virtuale tenant in POC2
1. Accedere al computer fisico di Azure Stack per POC2 e accedere al portale con un account tenant.
2. Nel riquadro di spostamento a sinistra fare clic su **Macchine virtuali**.
3. Trovare la **VM02** creata in precedenza nell'elenco di macchine virtuali e selezionarla.
4. Nel pannello della macchina virtuale fare clic su **Connetti**.
   
5. Aprire un prompt dei comandi dall'interno della macchina virtuale e digitare **ipconfig /all**.
6. Verrà visualizzato un indirizzo IPv4 compreso nell'intervallo 10.0.20.0/24. Nell'ambiente di esempio è 10.0.20.4, ma quello effettivo potrebbe essere diverso.
7. A questo punto, dalla macchina virtuale in POC2 si effettua il ping della macchina virtuale in POC1, attraverso il tunnel. A tale scopo, effettuare il ping dell'indirizzo IP diretto registrato da VM01.
   Nell'ambiente di esempio è 10.0.10.4, ma occorre assicurarsi di effettuare il ping dell'indirizzo di cui si è preso nota nel lab. Verrà visualizzato un risultato simile al seguente:
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
8. Se la macchina virtuale remota risponde, il test ha avuto esito positivo. È possibile chiudere la finestra di connessione della macchina virtuale. È anche possibile provare a eseguire altri trasferimenti di dati, ad esempio la copia di un file, per testare la connessione.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Visualizzazione delle statistiche di trasferimento dati attraverso la connessione del gateway
Le informazioni relative alla quantità di dati che passa attraverso la connessione da sito a sito sono disponibili nel pannello Connessione. Questo test offre anche il modo di verificare che il ping appena inviato sia passato effettivamente per la connessione VPN.

1. Mentre si è ancora connessi alla macchina virtuale tenant in POC2, accedere al **portale del modello di verifica di Microsoft Azure Stack** con l'account tenant.
2. Fare clic sulla voce di menu **Esplora** e selezionare **Connessioni**.
3. Fare clic sulla connessione **POC2-POC1** nell'elenco.
4. Nel pannello Connessione vengono visualizzate le statistiche dei **Dati in entrata** e dei **Dati in uscita**. Nello screenshot seguente sono riportati valori molto più elevati di quanto possa generare un semplice ping. Sono dovuti ad alcuni trasferimenti di file aggiuntivi. Dovrebbero essere presenti valori diversi da zero.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)




<!--HONumber=Feb17_HO2-->


