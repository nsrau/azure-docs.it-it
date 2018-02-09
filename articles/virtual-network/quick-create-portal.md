---
title: Creare una rete virtuale in Azure - Portale | Microsoft Docs
description: Apprendere a creare rapidamente una rete virtuale usando il portale di Azure. Una rete virtuale consente a molti tipi di risorse di Azure di comunicare privatamente tra loro.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 264dc38383b9adad70325f7fb7802b1dcf2da1c0
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Creare una rete virtuale usando il portale di Azure

Questo articolo illustra come creare una rete virtuale. Dopo aver creato una rete virtuale, si distribuiranno nella rete due macchine virtuali affinché comunichino privatamente tra loro.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="log-in-to-azure"></a>Accedere ad Azure 

Accedere al portale di Azure all'indirizzo http://portal.azure.com.

## <a name="create-a-virtual-network"></a>Crea rete virtuale

1. Fare clic su **+ Nuovo** nell'angolo superiore sinistro del portale di Azure.

2. Selezionare **Rete** e quindi **Rete virtuale**.

3. Come illustrato nell'immagine seguente, immettere *myVirtualNetwork* come **Nome**, *myResourceGroup* come **Gruppo di risorse**, selezionare un valore per **Posizione** e la propria **Sottoscrizione**, accettare le impostazioni predefinite rimanenti e quindi fare clic su **Crea**. 

    ![Immettere le informazioni di base sulla rete virtuale](./media/quick-create-portal/virtual-network.png)

    Lo **Spazio indirizzi** è specificato nella notazione CIDR. Una rete virtuale contiene zero o più subnet. Lo **Spazio indirizzi** della subnet predefinita di 10.0.0.0/24 usa l'intero intervallo indirizzi della rete virtuale, pertanto non è possibile creare un'altra subnet all'interno della rete virtuale usando l'intervallo e lo spazio indirizzi predefiniti. L'intervallo indirizzi specificato include gli indirizzi IP 10.0.0.0-10.0.0.254. Sono disponibili solo gli indirizzi 10.0.0.4-10.0.0.254 poiché Azure riserva i primi quattro indirizzi (0-3) e l'ultimo indirizzo di ogni subnet. Gli indirizzi IP disponibili sono assegnati alle risorse distribuite all'interno di una rete virtuale.

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Una rete virtuale consente a vari tipi di risorse di Azure di comunicare privatamente tra loro. Uno dei tipi di risorsa che è possibile distribuire in una rete virtuale è una macchina virtuale. Creare due macchine virtuali nella rete virtuale per poter convalidare e comprendere il funzionamento delle comunicazioni tra macchine virtuali in una rete virtuale in un passaggio successivo.

1. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.

2. Selezionare **Calcolo** e quindi **Windows Server 2016 Datacenter**.

3. Immettere le informazioni relative alla macchina virtuale illustrate nell'immagine seguente. I valori immessi per **Nome utente** e **Password** vengono usati per accedere alla macchina virtuale in un passaggio successivo. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). Selezionare la propria **Sottoscrizione**, scegliere di usare il gruppo di risorse *myResourceGroup* esistente e verificare che il valore selezionato per **Posizione** sia lo stesso in cui è stata creata la rete virtuale. Al termine fare clic su **OK**.

    ![Immettere le informazioni di base relative a una macchina virtuale](./media/quick-create-portal/virtual-machine-basics.png)

4. Selezionare una dimensione per la macchina virtuale e quindi fare clic su **Seleziona**. Per visualizzare altre dimensioni, selezionare **Visualizza tutto** o modificare il filtro **Supported disk type** (Tipo di disco supportato). Le dimensioni visualizzate potrebbero essere diverse da quelle riportate nell'esempio seguente: 

    ![Selezionare una dimensione per la macchina virtuale](./media/quick-create-portal/virtual-machine-size.png)

5. In **Impostazioni** *myVirtualNetwork* dovrebbe essere già selezionato per **Rete virtuale**, ma in caso contrario fare clic su **Rete virtuale** e quindi selezionare *myVirtualNetwork*. Lasciare selezionata l'opzione *default* per **Subnet** e quindi fare clic su **OK**.

    ![Selezionare una rete virtuale](./media/quick-create-portal/virtual-machine-network-settings.png)

6. Nella pagina **Riepilogo** fare clic su **Crea** per avviare la distribuzione della macchina virtuale. 

7. La creazione della macchina virtuale richiede alcuni minuti. Dopo la creazione, la macchina virtuale viene aggiunta al dashboard del portale di Azure e viene aperto automaticamente il riepilogo macchine virtuali. Fare clic su **Rete**.

    ![Informazioni relative alla rete delle macchine virtuali](./media/quick-create-portal/virtual-machine-networking.png)

    Si noterà che l'indirizzo **IP privato** è *10.0.0.4*. Nel passaggio 5, in **Impostazioni**, è stata selezionata la rete virtuale *myVirtualNetwork* ed è stata accettata la subnet denominata *default* come **Subnet**. Quando è stata [creata la rete virtuale](#create-a-virtual-network), è stato accettato il valore predefinito di 10.0.0.0/24 per l'**Intervallo indirizzi** della subnet. Il server DHCP di Azure assegna alla macchina virtuale il primo indirizzo disponibile per la subnet selezionata. Azure riserva i primi quattro indirizzi (0-3) e l'ultimo indirizzo di ogni subnet, pertanto 10.0.0.4 è il primo indirizzo disponibile per la subnet.

    L'indirizzo **IP pubblico** assegnato è diverso dall'indirizzo assegnato alla macchina virtuale. Per impostazione predefinita, Azure assegna a ogni macchina virtuale un indirizzo IP pubblico e instradabile su Internet. L'indirizzo IP pubblico viene assegnato alla macchina virtuale da un [pool di indirizzi assegnato a ogni area di Azure](https://www.microsoft.com/download/details.aspx?id=41653). Azure sa quale indirizzo IP pubblico viene assegnato a una macchina virtuale, mentre il sistema operativo in esecuzione in una macchina virtuale non è a conoscenza di eventuali indirizzi IP pubblici assegnati.

8. Completare nuovamente i passaggi 1-7, ma nel passaggio 3 assegnare il nome *myVm2* alla macchina virtuale. 

9. Dopo aver creato la macchina virtuale, fare clic su **Rete**, come nel passaggio 7. Si noterà che l'indirizzo **IP privato** è *10.0.0.5*. Azure aveva già assegnato il primo indirizzo utilizzabile di *10.0.0.4* nella subnet alla macchina virtuale *myVm1*, pertanto ha assegnato *10.0.0.5* alla macchina virtuale  *myVm2* poiché era il successivo indirizzo disponibile nella subnet.

## <a name="connect-to-a-virtual-machine"></a>Connettersi a una macchina virtuale

1. Connettersi in remoto alla macchina virtuale *myVm1*. Nella parte superiore del portale di Azure immettere *myVm1*. Fare clic su **myVm1** quando viene visualizzato nei risultati della ricerca. Scegliere il pulsante **Connetti** .

    ![Panoramica delle macchine virtuali](./media/quick-create-portal/virtual-machine-overview.png)


2. Dopo aver fatto clic sul pulsante **Connetti**, viene creato e scaricato nel computer un file Remote Desktop Protocol con estensione rdp.  
3. Aprire il file con estensione rdp scaricato. Se richiesto, fare clic su **Connetti**. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale e quindi fare clic su **OK**. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Fare clic su **Sì** o **Continua** per procedere con la connessione.

## <a name="validate-communication"></a>Convalidare la comunicazione

Il tentativo di eseguire il ping a una macchina virtuale di Windows ha esito negativo poiché per impostazione predefinita il ping non è consentito in Windows Firewall. Per consentire il ping per *myVm1*, immettere il comando seguente da un prompt dei comandi:

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

Per convalidare la comunicazione con *myVm2*, immettere il comando seguente da un prompt dei comandi nella macchina virtuale *myVm1*. Specificare le credenziali usate durante la creazione della macchina virtuale e quindi completare la connessione:

```
mstsc /v:myVm2
```

La connessione desktop remoto ha esito positivo poiché a entrambe le macchine virtuali sono stati assegnati indirizzi IP privati dalla subnet *default* e poiché per impostazione predefinita il desktop remoto è consentito in Windows Firewall. È possibile connettersi a *myVm2* tramite il nome host poiché Azure garantisce automaticamente la risoluzione dei nomi DNS per tutti gli host all'interno di una rete virtuale. Da un prompt dei comandi eseguire il ping a *myVm1* da *myVm2*.

```
ping myvm1
```

Il ping ha esito positivo poiché è stato consentito in Windows Firewall nella macchina virtuale *myVm1* in un passaggio precedente. Per verificare le comunicazioni verso l'esterno su Internet, immettere il comando seguente:

```
ping bing.com
```

Si riceveranno quattro risposte da bing.com. Per impostazione predefinita, tutte le macchine virtuali in una rete virtuale possono comunicare verso l'esterno su Internet.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse e il relativo contenuto. Nella parte superiore del portale di Azure immettere *myResourceGroup*. Fare clic su **myResourceGroup** quando viene visualizzato nei risultati della ricerca. Fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata distribuita una rete virtuale predefinita con una subnet e due macchine virtuali. Per informazioni su come creare una rete virtuale personalizzata con più subnet ed eseguire attività di gestione di base, procedere all'esercitazione per la creazione e la gestione di una rete virtuale personalizzata.


> [!div class="nextstepaction"]
> [Create a custom virtual network and manage it](virtual-networks-create-vnet-arm-pportal.md#portal) (Creare e gestire una rete virtuale personalizzata)