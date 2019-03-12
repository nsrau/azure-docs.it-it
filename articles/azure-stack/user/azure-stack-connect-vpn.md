---
title: Connettere Azure Stack ad Azure tramite VPN
description: Come connettere reti virtuali in Azure Stack per le reti virtuali in Azure tramite VPN.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: scottnap
ms.lastreviewed: 10/24/2018
ms.openlocfilehash: d2766416c84233281e55ff6f8be8519b7aadfb8c
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57763555"
---
# <a name="connect-azure-stack-to-azure-using-vpn"></a>Connettere Azure Stack ad Azure tramite VPN

*Si applica a: Sistemi integrati di Azure Stack*

Questo articolo illustra come creare una VPN site-to-site per connettere una rete virtuale in Azure Stack a una rete virtuale di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare la configurazione della connessione, assicurarsi che disporre dei seguenti elementi prima di iniziare:

* Azure Stack integrati distribuzione dei sistemi (a più nodi) che è direttamente connesso a internet. L'intervallo di indirizzi IP esterno deve essere raggiungibile direttamente dalla rete internet pubblica.
* Una sottoscrizione di Azure valida. Se non hai una sottoscrizione di Azure, è possibile creare un [qui account Azure gratuito](https://azure.microsoft.com/free/?b=17.06).

### <a name="vpn-connection-diagram"></a>Diagramma di connessione VPN

La figura seguente illustra la configurazione della connessione aspetto che avrà al termine:

![Configurazione della connessione VPN Site-to-site](media/azure-stack-connect-vpn/image2.png)

### <a name="network-configuration-example-values"></a>Valori di esempio di configurazione di rete

Tabella di esempi di configurazione di rete mostra i valori utilizzati per gli esempi in questo articolo. È possibile usare questi valori, oppure è possibile farvi riferimento per comprendere meglio gli esempi in questo articolo:

|   |Azure Stack|Azure|
|---------|---------|---------|
|Nome della rete virtuale     |Azs-VNet|AzureVNet |
|Spazio indirizzi della rete virtuale |10.1.0.0/16|10.100.0.0/16|
|Nome della subnet     |FrontEnd|FrontEnd|
|Intervallo di indirizzi subnet|10.1.0.0/24 |10.100.0.0/24 |
|Subnet gateway     |10.1.1.0/24|10.100.1.0/24|

## <a name="create-the-network-resources-in-azure"></a>Creare le risorse di rete in Azure

Innanzitutto, creare le risorse di rete per Azure. Le istruzioni seguenti illustrano come creare le risorse usando il [portale di Azure](https://portal.azure.com/).

### <a name="create-the-virtual-network-and-virtual-machine-vm-subnet"></a>Creare la rete virtuale e subnet per macchina virtuale (VM)

1. Accedi per il [portale di Azure](https://portal.azure.com/) usando l'account Azure.
2. Nel portale per gli utenti, selezionare **+ crea una risorsa**.
3. Passare a **Marketplace**, quindi selezionare **Networking**.
4. Selezionare **Rete virtuale**.
5. Usare le informazioni dalla tabella di configurazione di rete per individuare i valori per Azure **Name**, **dello spazio di indirizzi**, **Subnet name**, e **indirizzo della Subnet intervallo**.
6. Per la **gruppo di risorse**, creare un gruppo di risorse nuovo o, se è già presente, selezionare **Usa esistente**.
7. Selezionare il **posizione** della rete virtuale.  Se si usa i valori di esempio, selezionare **Stati Uniti orientali** oppure usare un'altra posizione.
8. Selezionare **Aggiungi al dashboard**.
9. Selezionare **Create**.

### <a name="create-the-gateway-subnet"></a>Creare la subnet del Gateway

1. Aprire la risorsa rete virtuale creata (**AzureVNet**) dal dashboard.
2. Nel **le impostazioni** sezione, selezionare **subnet**.
3. Selezionare **subnet del Gateway** per aggiungere una subnet del gateway alla rete virtuale.
4. Il nome predefinito della subnet è **GatewaySubnet**.

   >[!IMPORTANT]
   >Questo nome specifico è necessario per il corretto funzionamento delle subnet del gateway.

5. Nel **intervallo indirizzi** campo, verificare l'indirizzo sia **10.100.1.0/24**.
6. Selezionare **OK** per creare la subnet del gateway.

### <a name="create-the-virtual-network-gateway"></a>Creare il gateway di rete virtuale

1. Nel portale di Azure, selezionare **+ crea una risorsa**.  
2. Passare a **Marketplace**, quindi selezionare **Networking**.
3. Nell'elenco delle risorse di rete, selezionare **gateway di rete virtuale**.
4. Nelle **Name**, digitare **Azure-GW**.
5. Per scegliere una rete virtuale, selezionare **rete virtuale**. Quindi selezionare **AzureVnet** dall'elenco.
6. Selezionare **Indirizzo IP pubblico**. Quando la **Scegli indirizzo IP pubblico** verrà visualizzata la sezione, selezionare **Crea nuovo**.
7. Nelle **Name**, digitare **Azure-GW-PiP**e quindi selezionare **OK**.
8. Per impostazione predefinita, per **tipo di VPN**, **basato su Route** sia selezionata. Mantenere il **basato su Route** tipo VPN.
9. Verificare che la **Sottoscrizione** e la **Località** siano corrette. È possibile aggiungere la risorsa al dashboard. Selezionare **Create**.

### <a name="create-the-local-network-gateway-resource"></a>Creare la risorsa gateway di rete locale

1. Nel portale di Azure, selezionare **+ crea una risorsa**.
2. Passare a **Marketplace**, quindi selezionare **Networking**.
3. Nell'elenco delle risorse, selezionare **gateway di rete locale**.
4. Nelle **Name**, digitare **Azs-GW**.
5. Nelle **indirizzo IP**, digitare l'indirizzo IP pubblico per il Azure Stack di Gateway di rete virtuale che è elencato in precedenza nella tabella di configurazione di rete.
6. Nelle **spazio degli indirizzi**, da Azure Stack, digitare il **10.1.0.0/24** e **10.1.1.0/24** spazio degli indirizzi per **AzureVNet**.
7. Verificare che il **abbonamento**, **gruppo di risorse**, e **posizione** siano corrette e quindi selezionare **Create**.

## <a name="create-the-connection"></a>Creare la connessione

1. Nel portale per gli utenti, selezionare **+ crea una risorsa**.
2. Passare a **Marketplace**, quindi selezionare **Networking**.
3. Nell'elenco delle risorse, selezionare **connessione**.
4. Nel **base** sezione Impostazioni per il **tipo di connessione**, scegliere **Site-to-site (IPSec)**.
5. Selezionare il **sottoscrizione**, **gruppo di risorse**, e **posizione**, quindi selezionare **OK**.
6. Nel **le impostazioni** sezione, selezionare **gateway di rete virtuale**e quindi selezionare **Azure-GW**.
7. Selezionare **gateway di rete locale**, quindi selezionare **Azs-GW**.
8. Nelle **nome connessione**, digitare **Azure-Azs**.
9. Nelle **chiave condivisa (PSK)**, digitare **12345**, quindi selezionare **OK**.

   >[!NOTE]
   >Se si usa un valore diverso per la chiave condivisa, tenere presente che deve corrispondere il valore per la chiave condivisa che crea in altra estremità della connessione.

10. Rivedere le **Summary** sezione e quindi selezionare **OK**.

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Ora creare una macchina virtuale in Azure e inserirlo nella subnet VM nella rete virtuale.

1. Nel portale di Azure, selezionare **+ crea una risorsa**.
2. Passare a **Marketplace**, quindi selezionare **Compute**.
3. Nell'elenco di immagini di macchine virtuali, selezionare la **Windows Server 2016 Datacenter Eval** immagine.
4. Nel **nozioni di base** sezione, per **nome**, digitare **AzureVM**.
5. Digitare un nome utente valido e una password. Utilizzare questo account per accedere alla macchina virtuale dopo averla creata.
6. Fornire una **sottoscrizione**, **gruppo di risorse**, e **posizione**, quindi selezionare **OK**.
7. Nel **dimensioni** sezione, selezionare una dimensione di macchina virtuale per questa istanza e quindi selezionare **seleziona**.
8. Nel **impostazioni** sezione, è possibile usare le impostazioni predefinite. Prima di selezionare **OK**, verificare che:

   * Il **AzureVnet** sia selezionata la rete virtuale.
   * La subnet sia impostata su **10.100.0.0/24**.

   Selezionare **OK**.

9. Verificare le impostazioni nella **Summary** sezione e quindi selezionare **OK**.

## <a name="create-the-network-resources-in-azure-stack"></a>Creare le risorse di rete in Azure Stack

Successivamente, creare le risorse di rete in Azure Stack.

### <a name="sign-in-as-a-user"></a>Accedere con un nome utente

Un amministratore del servizio può accedere come utente per testare i piani, offerte e sottoscrizioni che potrebbero usare i propri utenti. Se non ne hai già uno, [creare un account utente](../azure-stack-add-new-user-aad.md) prima di accedere.

### <a name="create-the-virtual-network-and-a-vm-subnet"></a>Creare la rete virtuale e una subnet VM

1. Usare un account utente per accedere al portale per gli utenti.
2. Nel portale per gli utenti, selezionare **+ crea una risorsa**.

    ![Crea una nuova rete virtuale](media/azure-stack-connect-vpn/image3.png)

3. Passare a **Marketplace**, quindi selezionare **Networking**.
4. Selezionare **Rete virtuale**.
5. Per **Name**, **dello spazio di indirizzi**, **Subnet name**, e **intervallo di indirizzi Subnet**, utilizzare i valori di tabella di configurazione di rete.
6. Nelle **sottoscrizione**, viene visualizzata la sottoscrizione creata in precedenza.
7. Per la **gruppo di risorse**, è possibile creare un gruppo di risorse o se già presente, selezionare **Usa esistente**.
8. Verificare la località predefinita.
9. Selezionare **Aggiungi al dashboard**.
10. Selezionare **Create**.

### <a name="create-the-gateway-subnet"></a>Creare la subnet del gateway

1. Nel dashboard aprire la risorsa di rete virtuale Azs-VNet che è stato creato.
2. Nel **le impostazioni** sezione, selezionare **subnet**.
3. Per aggiungere una subnet del gateway alla rete virtuale, selezionare **Subnet del Gateway**.

    ![Aggiungere la subnet del gateway](media/azure-stack-connect-vpn/image4.png)

4. Per impostazione predefinita, il nome della subnet è impostato **GatewaySubnet**. Per la subnet del gateway funzionare correttamente, devono usare la **GatewaySubnet** nome.
5. Nelle **intervallo indirizzi**, verificare che l'indirizzo sia **10.1.1.0/24**.
6. Selezionare **OK** per creare la subnet del gateway.

### <a name="create-the-virtual-network-gateway"></a>Creare il gateway di rete virtuale

1. Nel portale di Azure Stack, selezionare **+ crea una risorsa**.
2. Passare a **Marketplace**, quindi selezionare **Networking**.
3. Nell'elenco delle risorse di rete, selezionare **gateway di rete virtuale**.
4. Nelle **Name**, digitare **Azs-GW**.
5. Selezionare il **rete virtuale** per scegliere una rete virtuale. Selezionare **Azs-VNet** dall'elenco.
6. Selezionare il **indirizzo IP pubblico** voce di menu. Quando la **Scegli indirizzo IP pubblico** verrà visualizzata la sezione, selezionare **Crea nuovo**.
7. Nella **Name**, digitare **Azs-GW-PiP**, quindi selezionare **OK**.
8. Per impostazione predefinita **basato su Route** sia selezionata per **tipo di VPN**. Mantenere il **basato su Route** tipo VPN.
9. Verificare che la **Sottoscrizione** e la **Località** siano corrette. È possibile aggiungere la risorsa al dashboard. Selezionare **Create**.

### <a name="create-the-local-network-gateway"></a>Creare il gateway di rete locale

Il concetto di una *gateway di rete locale* in Azure Stack è leggermente diverso in una distribuzione di Azure.

In una distribuzione di Azure, un gateway di rete locale rappresenta un dispositivo fisico locale (nel percorso utente) che si connette a un gateway di rete virtuale in Azure. In Azure Stack, tuttavia, entrambe le estremità della connessione sono gateway di rete virtuale.

Una descrizione più generica è che la risorsa gateway di rete locale indica sempre il gateway remoto a altra estremità della connessione.

### <a name="create-the-local-network-gateway-resource"></a>Creare la risorsa gateway di rete locale

1. Accedere al portale di Azure Stack.
2. Nel portale per gli utenti, selezionare **+ crea una risorsa**.
3. Passare a **Marketplace**, quindi selezionare **Networking**.
4. Nell'elenco delle risorse, selezionare **gateway di rete locale**.
5. Nelle **Name**, digitare **Azure-GW**.
6. Nelle **indirizzo IP**, digitare l'indirizzo IP pubblico del gateway di rete virtuale in Azure **Azure-GW-PiP**. Questo indirizzo viene visualizzato in precedenza nella tabella di configurazione di rete.
7. Nelle **spazio indirizzi**, per lo spazio degli indirizzi della rete virtuale di Azure che è stato creato, digitare **10.100.0.0/24** e **10.100.1.0/24**.
8. Verificare che il **abbonamento**, **gruppo di risorse**, e **posizione** valori siano corretti e quindi selezionare **Create**.

### <a name="create-the-connection"></a>Creare la connessione

1. Nel portale per gli utenti, selezionare **+ crea una risorsa**.
2. Passare a **Marketplace**, quindi selezionare **Networking**.
3. Nell'elenco delle risorse, selezionare **connessione**.
4. Nel **nozioni di base** sezione Impostazioni per il **tipo di connessione**, selezionare **Site-to-site (IPSec)**.
5. Selezionare il **sottoscrizione**, **gruppo di risorse**, e **posizione**, quindi selezionare **OK**.
6. Nel **le impostazioni** sezione, selezionare **gateway di rete virtuale**e quindi selezionare **Azs-GW**.
7. Selezionare **gateway di rete locale**, quindi selezionare **Azure-GW**.
8. Nelle **nome connessione**, digitare **Azs-Azure**.
9. Nelle **chiave condivisa (PSK)**, digitare **12345**, quindi selezionare **OK**.
10. Nel **Summary** sezione, selezionare **OK**.

### <a name="create-a-virtual-machine-vm"></a>Creare una macchina virtuale (VM)

Per verificare la connessione VPN, creare due macchine virtuali: uno in Azure e uno in Azure Stack. Dopo aver creato queste macchine virtuali, è possibile usare per inviare e ricevere dati attraverso il tunnel VPN.

1. Nel portale di Azure, selezionare **+ crea una risorsa**.
2. Passare a **Marketplace**, quindi selezionare **Compute**.
3. Nell'elenco di immagini di macchine virtuali, selezionare la **Windows Server 2016 Datacenter Eval** immagine.
4. Nel **nozioni di base** nella sezione **nome**, digitare **Azs-VM**.
5. Digitare un nome utente valido e una password. Si usa questo account per accedere alla macchina virtuale dopo averla creata.
6. Fornire una **sottoscrizione**, **gruppo di risorse**, e **posizione**, quindi selezionare **OK**.
7. Nel **dimensioni** della sezione, per questa istanza, seleziona una dimensione di macchina virtuale e quindi selezionare **seleziona**.
8. Nel **impostazioni** sezione, accettare le impostazioni predefinite. Assicurarsi che il **Azs-VNet** sia selezionata la rete virtuale. Verificare che la subnet sia impostata su **10.1.0.0/24**. Selezionare **OK**.
9. Nel **riepilogo** sezione, rivedere le impostazioni e quindi selezionare * OK * *.

## <a name="test-the-connection"></a>Testare la connessione

Dopo aver stabilita la connessione site-to-site, è necessario verificare che è possibile ottenere i dati che fluiscono in entrambe le direzioni. Il modo più semplice per testare la connessione è possibile eseguire un test di ping:

* Accedere alla macchina virtuale creata in Azure Stack e ping la macchina virtuale in Azure.
* Accedere alla macchina virtuale creata in Azure ed effettuare il ping di macchina virtuale in Azure Stack.

>[!NOTE]
>Per assicurarsi che si sta inviando traffico tramite la connessione site-to-site, il ping dell'indirizzo IP diretto (DIP) della macchina virtuale nella subnet remota, non l'indirizzo VIP.

### <a name="sign-in-to-the-user-vm-in-azure-stack"></a>Accedi all'utente della macchina virtuale in Azure Stack

1. Accedere al portale di Azure Stack.
2. Nella barra di spostamento a sinistra, selezionare **macchine virtuali**.
3. Nell'elenco delle macchine virtuali, individuare **Azs-VM** creato in precedenza e quindi selezionarlo.
4. Nella sezione relativa alla macchina virtuale, selezionare **Connect**e quindi aprire il file Azs-VM.rdp.

     ![Pulsante di connessione](media/azure-stack-connect-vpn/image17.png)

5. Accedere con l'account configurato al momento della creazione della macchina virtuale.
6. Aprire un prompt di Windows PowerShell con privilegi elevato.
7. Digitare **ipconfig /all**.
8. Nell'output trovare la **indirizzo IPv4**e quindi salvare l'indirizzo per un uso successivo. Questo è l'indirizzo che si esegue il ping di Azure. Nell'ambiente di esempio, l'indirizzo viene **10.1.0.4**, ma nell'ambiente in uso potrebbe essere diverso. Devono rientrare la **10.1.0.0/24** subnet creata in precedenza.
9. Per creare una regola firewall che consenta la macchina virtuale rispondere ai ping, eseguire il comando PowerShell seguente:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-azure"></a>Accedere al tenant di macchina virtuale in Azure

1. Accedere al portale di Azure.
2. Nella barra di spostamento a sinistra, selezionare **macchine virtuali**.
3. Dall'elenco di macchine virtuali, individuare **macchina virtuale di Azure** creato in precedenza e quindi selezionarlo.
4. Nella sezione relativa alla macchina virtuale, selezionare **Connect**.
5. Accedere con l'account configurato al momento della creazione della macchina virtuale.
6. Aprire con privilegi elevati **Windows PowerShell** finestra.
7. Digitare **ipconfig /all**.
8. Dovrebbe essere un indirizzo IPv4 compreso entro **10.100.0.0/24**. Nell'ambiente di esempio, l'indirizzo viene **10.100.0.4**, ma l'indirizzo potrebbe essere diverso.
9. Per creare una regola firewall che consenta la macchina virtuale rispondere ai ping, eseguire il comando PowerShell seguente:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. Dalla macchina virtuale in Azure, effettuare il ping di macchina virtuale in Azure Stack, attraverso il tunnel. A tale scopo, eseguire il ping il DIP registrata dal Azs-VM. Nell'ambiente di esempio, si tratta **10.1.0.4**, ma è meglio il ping dell'indirizzo a cui si è preso nota nel lab. Si verrà visualizzato un risultato simile alla schermata seguente:

    ![Ping con esito positivo](media/azure-stack-connect-vpn/image19b.png)

11. Una risposta della macchina virtuale remota indica un test ha esito positivo. È possibile chiudere la finestra della macchina virtuale.

È inoltre necessario eseguire il trasferimento dei dati più rigoroso test; Copia diverse dimensioni, ad esempio, i file in entrambe le direzioni.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Visualizzazione delle statistiche di trasferimento dati attraverso la connessione del gateway

Se si desidera sapere quanti dati vengono passati attraverso la connessione site-to-site, queste informazioni sono disponibili nel **connessione** sezione. Questo test è anche un altro modo per verificare che il ping che appena inviato sia passato effettivamente la connessione VPN.

1. Eseguito l'accesso alla macchina virtuale utente in Azure Stack, usare l'account utente per accedere al portale per gli utenti.
2. Passare a **tutte le risorse**, quindi selezionare la **Azs-Azure** connessione. **Le connessioni** viene visualizzata.
3. Nel **Connection** sezione, le statistiche per **i dati in** e **dati in uscita** vengono visualizzati. Nella schermata seguente, i numeri di grandi dimensioni vengono attribuiti al trasferimento di file aggiuntivi. Dovrebbe essere presenti alcuni valori diversi da zero.

    ![Dati in ingresso e in uscita](media/azure-stack-connect-vpn/Connection.png)

## <a name="next-steps"></a>Passaggi successivi

* [Distribuire le App in Azure e Azure Stack](azure-stack-solution-pipeline.md)
