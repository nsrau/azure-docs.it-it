---
title: Connettersi dello Stack di Azure ad Azure tramite VPN
description: Come connettere reti virtuali in Azure Stack alle reti virtuali in Azure tramite VPN.
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: victorh
ms.openlocfilehash: c06eb0bb44bdfeab956e9b5051786b5bc631acf5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-azure-stack-to-azure-using-vpn"></a>Connettersi dello Stack di Azure ad Azure tramite VPN

*Si applica a: Azure Stack integrate di sistemi*

In questo articolo viene illustrato come creare una VPN da sito a sito per connettere una rete virtuale nello Stack di Azure a una rete virtuale in Azure.

### <a name="connection-diagram"></a>Diagramma di connessione
Nel diagramma seguente viene illustrato quale la configurazione della connessione deve essere simile al termine:

![Configurazione della connessione VPN da sito a sito](media/azure-stack-connect-vpn/image2.png)

### <a name="before-you-begin"></a>Prima di iniziare
Per completare la configurazione della connessione, accertarsi di avere i seguenti elementi prima di iniziare:

* Uno Stack di Azure integrata la distribuzione di sistemi (a più nodi) che è direttamente connesso a Internet. Ciò significa che l'intervallo di indirizzo IP pubblico esterno deve essere raggiungibile direttamente dalla rete Internet pubblica.
* Una sottoscrizione Azure valida.  Se non si dispone di una sottoscrizione di Azure, è possibile creare un [senza account di Azure](https://azure.microsoft.com/free/?b=17.06).

## <a name="network-example-values-table"></a>Tabella dei valori di esempio rete
La tabella di valori di esempio rete vengono illustrati i valori di esempio utilizzati in questo articolo. È possibile utilizzare questi valori o è possibile fare riferimento a essi per comprendere meglio gli esempi in questo articolo.

**Tabella dei valori di esempio rete**
|   |Azure Stack|Azure|
|---------|---------|---------|
|Nome di rete virtuale     |Azs della rete virtuale|AzureVNet |
|Spazio degli indirizzi di rete virtuale |10.1.0.0/16|10.100.0.0/16|
|Nome della subnet     |FrontEnd|FrontEnd|
|Intervallo di indirizzi subnet|10.1.0.0/24 |10.100.0.0/24 |
|Subnet gateway     |10.1.1.0/24|10.100.1.0/24|

## <a name="create-the-network-resources-in-azure"></a>Creare le risorse di rete in Azure

Prima creare le risorse di rete per Azure. Le istruzioni seguenti viene illustrato come creare le risorse tramite il [portale di Azure](http://portal.azure.com/).

### <a name="create-the-virtual-network-and-vm-subnet"></a>Creare la rete virtuale e la subnet della macchina virtuale

1. Accedi al [portale di Azure](http://portal.azure.com/) utilizzando l'account di Azure.
2. Nel portale per gli utenti, selezionare **New**.
3. Passare a **Marketplace**, quindi selezionare **rete**.
4. Selezionare **rete virtuale**.
5. Utilizzare le informazioni dalla tabella di configurazione di rete per individuare i valori per Azure **nome**, **lo spazio degli indirizzi**, **nome Subnet**, e **indirizzo di Subnet intervallo**.
6. Per **gruppo di risorse**, creare un nuovo gruppo di risorse o, se già presente, selezionare **utilizzare esistente**.
7. Selezionare il **percorso** di una rete virtuale.  Se si utilizzano i valori di esempio, selezionare **Stati Uniti orientali** o utilizzare un altro percorso, se si preferisce.
8. Selezionare **Aggiungi al dashboard**.
9. Selezionare **Crea**.

### <a name="create-the-gateway-subnet"></a>Creare la subnet del gateway
1. Aprire la risorsa di rete virtuale creata (**AzureVNet**) dal dashboard.
2. Nel **impostazioni** selezionare **subnet**.
3. Selezionare **subnet del Gateway** per aggiungere una subnet del gateway per la rete virtuale.
4. Il nome predefinito della subnet è **GatewaySubnet**.
   Questo nome specifico è necessario per il corretto funzionamento delle subnet del gateway.
5. Nel **intervallo di indirizzi** campo, verificare l'indirizzo sia **10.100.0.0/24**.
6. Selezionare **OK** per creare la subnet del gateway.

### <a name="create-the-virtual-network-gateway"></a>Creare il gateway di rete virtuale
1. Nel portale di Azure, selezionare **New**.  
2. Passare a **Marketplace**, quindi selezionare **rete**.
3. Nell'elenco di risorse di rete, selezionare **gateway di rete virtuale**.
4. In **nome**, tipo **Azure-GW**.
5. Per scegliere una rete virtuale, selezionare **rete virtuale**. Selezionare quindi **AzureVnet** dall'elenco.
6. Selezionare **indirizzo IP pubblico**. Quando il **scegliere l'indirizzo IP pubblico** verrà visualizzata la sezione, selezionare **Crea nuovo**.
7. In **nome**, tipo **Azure-GW-PiP**, quindi selezionare **OK**.
8. Per impostazione predefinita, per **tipo VPN**, **basato su Route** è selezionata.
    Mantenere il **basato su Route** tipo VPN.
9. Verificare che la **Sottoscrizione** e la **Località** siano corrette. È possibile aggiungere la risorsa al dashboard. Selezionare **Crea**.

### <a name="create-the-local-network-gateway-resource"></a>Creare la risorsa di gateway di rete locale

1. Nel portale di Azure, selezionare **New**. 
4. Passare a **Marketplace**, quindi selezionare **rete**.
5. Nell'elenco di risorse, selezionare **gateway di rete locale**.
6. In **nome**, tipo **Azs-GW**.
7. In **indirizzo IP**, digitare l'indirizzo IP pubblico per il Azure Stack Gateway della rete virtuale che è elencato in precedenza nella tabella di configurazione di rete.
8. In **spazio degli indirizzi**, dallo Stack di Azure, digitare il **10.0.10.0/23** spazio di indirizzi **AzureVNet**.
9. Verificare che il **sottoscrizione**, **gruppo di risorse**, e **percorso** siano corrette e quindi selezionare **crea**.

## <a name="create-the-connection"></a>Creare la connessione
1. Nel portale per gli utenti, selezionare **New**. 
2. Passare a **Marketplace**, quindi selezionare **rete**.
3. Nell'elenco di risorse, selezionare **connessione**.
4. Nel **base** sezione Impostazioni per il **tipo di connessione**, scegliere **Site-to-site (IPSec)**.
5. Selezionare il **sottoscrizione**, **gruppo di risorse**, e **percorso**, quindi selezionare **OK**.
6. Nel **impostazioni** sezione, selezionare **gateway di rete virtuale**, quindi selezionare **Azure-GW**.
7. Selezionare **gateway di rete locale**, quindi selezionare **Azs-GW**.
8. In **nome connessione**, tipo **Azure Azs**.
9. In **chiave condivisa (PSK)**, tipo **12345**. Se si sceglie un valore diverso, tenere presente che *deve* corrisponde al valore per la chiave condivisa che crei a altra estremità della connessione. Selezionare **OK**.
10. Esaminare il **riepilogo** sezione e quindi selezionare **OK**.

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale
Creare una macchina virtuale in Azure a questo punto e inserirlo nella subnet VM nella rete virtuale.

1. Nel portale di Azure, selezionare **New**.
2. Passare a **Marketplace**, quindi selezionare **calcolo**.
3. Nell'elenco di immagini di macchina virtuale, selezionare il **Eval di Data Center di Windows Server 2016** immagine.
4. Nel **nozioni di base** sezione per **nome**, tipo **AzureVM**.
5. Digitare un nome utente valido e una password. Utilizzare questo account per accedere alla macchina virtuale dopo la sua creazione.
6. Fornire un **sottoscrizione**, **gruppo di risorse**, e **percorso**, quindi selezionare **OK**.
7. Nel **dimensioni** sezione, selezionare una dimensione di macchina virtuale per questa istanza, quindi **selezionare**.
8. Nel **impostazioni** sezione, è possibile accettare i valori predefiniti. Assicurarsi che il **AzureVnet** rete virtuale sia selezionata e verificare che la subnet è impostata su **10.0.20.0/24**. Selezionare **OK**.
9. Esaminare le impostazioni nella **riepilogo** sezione e quindi selezionare **OK**.

## <a name="create-the-network-resources-in-azure-stack"></a>Creare le risorse di rete nello Stack di Azure
Creare le risorse di rete nello Stack di Azure.

### <a name="sign-in-as-a-user"></a>Accedi con un account utente
Un amministratore del servizio può accedere come un utente per testare i piani, offerte e sottoscrizioni che potrebbero utilizzare i propri utenti. Se si dispone già di uno, [creare un account utente](azure-stack-add-new-user-aad.md) prima di accedere.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Creare la rete virtuale e la subnet della macchina virtuale
1. Utilizzare un account utente per accedere al portale per gli utenti.
2. Nel portale per gli utenti, selezionare **New**.

    ![Crea nuova rete virtuale](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)

3. Passare a **Marketplace**, quindi selezionare **rete**.
4. Selezionare **rete virtuale**.
5. Per **nome**, **lo spazio degli indirizzi**, **nome Subnet**, e **intervallo di indirizzi di Subnet**, utilizzare i valori di tabella di configurazione di rete.
6. In **sottoscrizione**, viene visualizzata la sottoscrizione creata in precedenza.
7. Per **gruppo di risorse**, è possibile creare un gruppo di risorse o se si dispone già di uno, selezionare **utilizzare esistente**.
8. Verificare la località predefinita.
9. Selezionare **Aggiungi al dashboard**.
10. Selezionare **Crea**.

### <a name="create-the-gateway-subnet"></a>Creare la subnet del gateway
1. Nel dashboard, aprire la risorsa di rete virtuale Azs della rete virtuale creata.
2. Nel **impostazioni** selezionare **subnet**.
3. Per aggiungere una subnet del gateway per la rete virtuale, selezionare **Subnet del Gateway**.
   
    ![Aggiungi subnet gateway](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Per impostazione predefinita, il nome della subnet è **GatewaySubnet**.
   Subnet gateway sono speciali. Per funzionare correttamente, è necessario che utilizzino il *GatewaySubnet* nome.
5. In **intervallo di indirizzi**, verificare che l'indirizzo sia **10.1.1.0/24**.
6. Selezionare **OK** per creare la subnet del gateway.

### <a name="create-the-virtual-network-gateway"></a>Creare il gateway di rete virtuale
1. Nel portale di Azure Stack, selezionare **New**. 
2. Passare a **Marketplace**, quindi selezionare **rete**.
3. Nell'elenco di risorse di rete, selezionare **gateway di rete virtuale**.
4. In **nome**, tipo **Azs-GW**.
5. Selezionare il **rete virtuale** elemento per scegliere una rete virtuale.
   Selezionare **Azs-VNet** dall'elenco.
6. Selezionare il **indirizzo IP pubblico** voce di menu. Quando il **scegliere l'indirizzo IP pubblico** verrà visualizzata la sezione, selezionare **Crea nuovo**.
7. In **nome**, tipo **Azs-GW-PiP**, quindi selezionare **OK**.
8.  Per impostazione predefinita, per **tipo VPN**, **basato su Route** è selezionata.
    Mantenere il **basato su Route** tipo VPN.
9. Verificare che la **Sottoscrizione** e la **Località** siano corrette. È possibile aggiungere la risorsa al dashboard. Selezionare **Crea**.

### <a name="create-the-local-network-gateway"></a>Creare il gateway di rete locale
La nozione di un *gateway di rete locale* nello Stack di Azure è leggermente diverso in una distribuzione di Azure.

In una distribuzione di Azure, un gateway di rete locale rappresenta un dispositivo fisico locale (in corrispondenza della posizione di utente), che consente di connettersi a un gateway di rete virtuale in Azure. Nello Stack di Azure, entrambe le estremità della connessione sono gateway di rete virtuale.

Un modo per considerare questo più in generale è che la risorsa del gateway di rete locale indica sempre il gateway remoto a altra estremità della connessione. 

### <a name="create-the-local-network-gateway-resource"></a>Creare la risorsa di gateway di rete locale
1. Accedere al portale di Azure Stack.
2. Nel portale per gli utenti, selezionare **New**.
3. Passare a **Marketplace**, quindi selezionare **rete**.
4. Nell'elenco di risorse, selezionare **gateway di rete locale**.
5. In **nome**, tipo **Azure-GW**.
6. In **indirizzo IP**, digitare l'indirizzo IP pubblico per il gateway di rete virtuale in Azure **Azure-GW-PiP**. Questo indirizzo viene visualizzato in precedenza nella tabella di configurazione di rete.
7. In **spazio degli indirizzi**, per lo spazio degli indirizzi della rete virtuale di Azure creata, digitare **10.0.20.0/23**.
8. Verificare che il **sottoscrizione**, **gruppo di risorse**, e **percorso** siano corrette e quindi selezionare **crea**.

### <a name="create-the-connection"></a>Creare la connessione
1. Nel portale per gli utenti, selezionare **New**.
2. Passare a **Marketplace**, quindi selezionare **rete**.
3. Nell'elenco di risorse, selezionare **connessione**.
4. Nel **nozioni di base** sezione Impostazioni per il **tipo di connessione**selezionare **Site-to-site (IPSec)**.
5. Selezionare il **sottoscrizione**, **gruppo di risorse**, e **percorso**, quindi selezionare **OK**.
6. Nel **impostazioni** sezione, selezionare **gateway di rete virtuale**, quindi selezionare **Azs-GW**.
7. Selezionare **gateway di rete locale**, quindi selezionare **Azure-GW**.
8. In **nome connessione**, tipo **Azs Azure**.
9. In **chiave condivisa (PSK)**, tipo **12345**, quindi selezionare **OK**.
10. Nel **riepilogo** selezionare **OK**.

### <a name="create-a-vm"></a>Creare una macchina virtuale
Per convalidare i dati che passano attraverso la connessione VPN, è necessario creare macchine virtuali a ogni estremità per inviare e ricevere dati tramite il tunnel VPN. 

1. Nel portale di Azure, selezionare **New**.
2. Passare a **Marketplace**, quindi selezionare **calcolo**.
3. Nell'elenco di immagini di macchina virtuale, selezionare il **Eval di Data Center di Windows Server 2016** immagine.
4. Nel **nozioni di base** nella sezione **nome**, tipo **Azs-VM**.
5. Digitare un nome utente valido e una password. Utilizzare questo account per accedere alla macchina virtuale dopo la sua creazione.
6. Fornire un **sottoscrizione**, **gruppo di risorse**, e **percorso**, quindi selezionare **OK**.
7. Nel **dimensioni** sezione, per questa istanza, seleziona una dimensione di macchina virtuale e quindi selezionare **selezionare**.
8. Nel **impostazioni** sezione, accettare le impostazioni predefinite. Assicurarsi che il **Azs-VNet** rete virtuale è stata selezionata. Verificare che la subnet è impostata su **10.1.0.0/24**. Selezionare **OK**.
9. Nel **riepilogo** sezione, verificare le impostazioni e quindi selezionare **OK**.


## <a name="test-the-connection"></a>Testare la connessione
Ora che viene stabilita la connessione site-to-site, è necessario convalidare che è possibile ottenere il traffico che passano attraverso di esso. Per convalidare, accedere a una delle macchine virtuali che è stato creato nello Stack di Azure. Quindi, eseguire il ping la macchina virtuale creata in Azure. 

Per assicurarsi che si invia il traffico attraverso la connessione site-to-site, ping dell'indirizzo IP diretto (DIP) della macchina virtuale nella subnet remota, non l'indirizzo VIP. A tale scopo, individuare l'indirizzo DIP a altra estremità della connessione. Salva l'indirizzo per un uso successivo.

### <a name="sign-in-to-the-user-vm-in-azure-stack"></a>Accedi all'utente di macchina virtuale in Azure Stack
1. Accedere al portale di Azure Stack.
2. Nella barra di spostamento a sinistra, selezionare **macchine virtuali**.
3. Nell'elenco di macchine virtuali, trovare **Azs-VM** creato in precedenza e selezionarlo.
4. Nella sezione per la macchina virtuale, fare clic su **Connetti**e quindi aprire il file Azs VM.rdp.
   
     ![Pulsante Connetti](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Accedere con l'account configurato al momento della creazione della macchina virtuale.
6. Aprire con privilegi elevati **Windows PowerShell** finestra.
7. Digitare **ipconfig /all**.
8. Nell'output, è possibile trovare il **indirizzo IPv4**e quindi salvare l'indirizzo per un uso successivo. Questo è l'indirizzo che eseguirà il ping da Azure. Nell'ambiente di esempio è **10.0.10.4**, ma quello effettivo potrebbe essere diverso. Devono rientrare il **10.0.10.0/24** subnet creato in precedenza.
9. Per creare una regola del firewall che consente la macchina virtuale rispondere ai ping, eseguire il comando PowerShell seguente:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-azure"></a>Accedere al tenant di macchina virtuale in Azure
1. Accedere al portale di Azure.
2. Nella barra di spostamento a sinistra, fare clic su **macchine virtuali**.
3. Dall'elenco di macchine virtuali, trovare **macchina virtuale di Azure** creato in precedenza e selezionarlo.
4. Nella sezione per la macchina virtuale, fare clic su **Connetti**.
5. Accedere con l'account configurato al momento della creazione della macchina virtuale.
6. Aprire con privilegi elevati **Windows PowerShell** finestra.
7. Digitare **ipconfig /all**.
8. Dovrebbe essere un indirizzo IPv4 che rientra **10.0.20.0/24**. Nell'ambiente di esempio, l'indirizzo è **10.0.20.4**, ma l'indirizzo potrebbe essere diverso.
9. Per creare una regola del firewall che consente la macchina virtuale rispondere ai ping, eseguire il comando PowerShell seguente:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. Dalla macchina virtuale in Azure, effettuare il ping la macchina virtuale nello Stack di Azure, tramite il tunnel. A tale scopo, eseguire il ping il DIP registrato da Azs-VM.
   Nell'ambiente di esempio, si tratta di **10.0.10.4**, ma assicurarsi di ping dell'indirizzo a cui si è preso nota nell'ambiente lab. Verrà visualizzato un risultato simile nella schermata seguente:
   
    ![Ping ha esito positivo](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. Una risposta dalla macchina virtuale remota indica un test riuscito! È possibile chiudere la finestra della macchina virtuale. Per testare la connessione, è possibile provare altri tipi di trasferimenti di dati come una copia di file.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Visualizzazione delle statistiche di trasferimento dati attraverso la connessione del gateway
Se si desidera conoscere la quantità di dati passa attraverso la connessione site-to-site, queste informazioni sono disponibili sul **connessione** sezione. Questo test è anche un altro modo per verificare che il ping che appena inviato è effettivamente passato tramite la connessione VPN.

1. Mentre è effettuato l'accesso alla macchina virtuale utente nello Stack di Azure, utilizzare l'account utente per accedere al portale per gli utenti.
2. Passare a **tutte le risorse**, quindi selezionare il **Azs Azure** connessione. **Connessioni** viene visualizzato.
4. Nel **connessione** sezione, le statistiche per **dati** e **i dati in uscita** vengono visualizzati. Nella schermata seguente, con un numero elevato per il trasferimento di file aggiuntivi. Dovrebbe essere presente alcuni valori diversi da zero.
   
    ![Dati in e out](media/azure-stack-connect-vpn/Connection.png)

## <a name="next-steps"></a>Passaggi successivi

[Distribuire le App in Azure e Azure Stack](azure-stack-solution-pipeline.md)