---
title: "Più indirizzi IP per le macchine virtuali - Portale | Documentazione Microsoft"
description: "Informazioni su come assegnare più indirizzi IP a una macchina virtuale usando il portale di Azure | Resource Manager."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 7ce83952f0f16e01a4837ce2155571d223d7b551
ms.openlocfilehash: b1a2549e0f04dbc00a47a57ecc888ca36339c646


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Assegnare più indirizzi IP alle macchine virtuali usando il portale di Azure

> [!div class="op_single_selector"]
> * [Portale](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](virtual-network-multiple-ip-addresses-cli.md)
>

Una macchina virtuale di Azure può essere associata a una o più interfacce di rete. A ogni scheda di interfaccia di rete possono essere assegnati uno o più indirizzi IP pubblici o privati, statici e dinamici. L'assegnazione di più indirizzi IP a una VM consente di:

* Ospitare più siti Web o servizi con indirizzi IP e certificati SSL diversi in un singolo server.
* Fungere da appliance virtuale di rete, ad esempio un firewall o un servizio di bilanciamento del carico.
* Aggiungere qualsiasi indirizzo IP per qualsiasi scheda di interfaccia di rete a un pool back-end di Azure Load Balancer. In passato, era possibile aggiungere a un pool di back-end solo gli indirizzi IP primari per la scheda di interfaccia di rete primaria. Per altre informazioni su come bilanciare il carico di più configurazioni IP, leggere l'articolo [Load balancing multiple IP configurations](../load-balancer/load-balancer-multiple-ip.md) (Bilanciamento del carico di più configurazioni IP).

Ogni scheda di interfaccia di rete collegata a una macchina virtuale dispone di una o più configurazioni IP associate. A ogni configurazione viene assegnato un indirizzo IP privato statico o dinamico. Ogni configurazione può anche avere una risorsa di indirizzo IP pubblico associata. Una risorsa indirizzo IP pubblico dispone di un indirizzo IP dinamico o statico assegnato. Per altre informazioni sugli indirizzi IP in Azure, leggere l'articolo sugli [indirizzi IP in Azure](virtual-network-ip-addresses-overview-arm.md).

Questo articolo illustra come usare il portale di Azure per assegnare più indirizzi IP a una VM creata tramite il modello di distribuzione Azure Resource Manager. Non è possibile a assegnare più indirizzi IP alle risorse create tramite il modello di distribuzione classica. Per altre informazioni sui modelli di distribuzione di Azure, leggere l'articolo [Understand Azure deployment models](../resource-manager-deployment-model.md) (Informazioni sui modelli di distribuzione di Azure).

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="scenario"></a>Scenario
Una macchina virtuale con una singola scheda di interfaccia di rete viene creata e collegata a una rete virtuale. La macchina virtuale richiede tre diversi indirizzi IP *privati* e due indirizzi IP *pubblici*. Gli indirizzi IP vengono assegnati alle configurazioni IP seguenti:

* **IPConfig-1:** assegna un indirizzo IP privato *dinamico* (predefinito) e un indirizzo IP pubblico *statico*.
* **IPConfig-2:** assegna un indirizzo IP privato *statico* e un indirizzo IP pubblico *statico*.
* **IPConfig-3:** assegna un indirizzo IP privato *dinamico* e nessun indirizzo IP pubblico.
  
    ![Più indirizzi IP](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

Le configurazioni IP vengono associate alla scheda di interfaccia di rete al momento della creazione della stessa, mentre la scheda di interfaccia di rete viene collegata alla macchina virtuale al momento della creazione della macchina virtuale. I tipi di indirizzi IP usati per lo scenario sono a scopo illustrativo. È possibile assegnare qualsiasi tipo di assegnazione e indirizzo IP desiderato.

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Creare una macchina virtuale con più indirizzi IP

Se si desidera creare una VM con più indirizzi IP, è necessario crearla tramite PowerShell o l'interfaccia della riga di comando di Azure. Per informazioni su come procedere, fare clic sulle opzioni relative a PowerShell o all'interfaccia della riga di comando nella parte superiore di questo articolo. È possibile creare una VM con un singolo indirizzo IP privato statico e, facoltativamente, un singolo indirizzo IP pubblico tramite il portale, seguendo i passaggi indicati negli articoli [Creare la prima macchina virtuale Windows nel portale di Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md) o [Creare una VM Linux in Azure usando il portale](../virtual-machines/virtual-machines-linux-quick-create-portal.md). Dopo aver creato la VM, è possibile cambiare i tipi di indirizzo IP e aggiungere altri indirizzi IP mediante il portale seguendo i passaggi indicati nella sezione [Aggiungere indirizzi IP a una VM](#add) di questo articolo.

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>Aggiungere indirizzi IP a una macchina virtuale

È possibile aggiungere indirizzi IP privati e pubblici a una scheda di interfaccia di rete esistente completando la procedura seguente. Gli esempi delle sezioni seguenti presuppongono che si disponga già di una VM con le tre configurazioni IP descritte nello [scenario](#Scenario) di questo articolo, ma questa condizione non è indispensabile.

> [!NOTE]
> Sebbene questo articolo assegna tutte le configurazioni IP a una singola scheda di interfaccia di rete, è possibile anche assegnare più configurazioni IP a qualsiasi scheda di interfaccia di rete in una macchina virtuale. Per informazioni su come creare una macchina virtuale con più schede di interfacce di rete, leggere l'articolo [Creare una macchina virtuale con più schede di interfaccia di rete](virtual-network-deploy-multinic-arm-ps.md).

### <a name="a-namecoreaddacore-steps"></a><a name="coreadd"></a>Passaggi di base

1. Registrarsi all'anteprima inviando un messaggio di posta elettronica a [IP multipli](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con l'ID sottoscrizione e l'uso previsto. Non completare i passaggi rimanenti:
    - Finché non si riceve un messaggio di posta elettronica che comunica che si è stati accettati nell'anteprima.
    - Senza seguire le istruzioni nel messaggio di posta elettronica ricevuto.
2. Passare al portale di Azure all'indirizzo https://portal.azure.com e, se necessario, eseguire l'accesso.
3. Nel portale fare clic su **Altri servizi** > digitare *macchine virtuali* nella casella del filtro e quindi fare clic su **Macchine virtuali**.
4. Nel pannello **Macchine virtuali** scegliere la VM a cui aggiungere indirizzi IP. Fare clic su **Interfacce di rete** nel pannello della macchina virtuale visualizzato e quindi selezionare l'interfaccia di rete a cui aggiungere l'indirizzo IP. Nell'esempio illustrato nella figura seguente è selezionata la scheda di rete denominata *myNIC* dalla VM denominata *myVM*:

    ![Interfaccia di rete](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

5. Nel pannello visualizzato per la scheda di rete selezionata fare clic su **Configurazioni IP**, come illustrato nell'immagine seguente:

    ![Configurazioni IP](./media/virtual-network-multiple-ip-addresses-portal/figure2.png)

Completare i passaggi in una delle sezioni che seguono, in base al tipo di indirizzo IP che si vuole aggiungere.

### <a name="add-a-private-ip-address"></a>**Aggiungere un indirizzo IP privato**

Completare i passaggi seguenti per aggiungere un nuovo indirizzo IP privato:

1. Completare i passaggi della sezione [Passaggi di base](#coreadd) di questo articolo.
2. Fare clic su **Aggiungi**. Nel pannello **Aggiungi configurazione IP** che viene visualizzato creare una configurazione IP denominata *IPConfig-4* con *10.0.0.7* come indirizzo IP privato *Statico* quindi fare clic su **OK**, come illustrato nell'immagine seguente:

    ![Aggiungere un indirizzo IP privato](./media/virtual-network-multiple-ip-addresses-portal/figure3.png)

    > [!NOTE]
    > Quando si aggiunge un indirizzo IP statico, è necessario specificare un indirizzo valido e inutilizzato nella subnet a cui la scheda di rete è connessa. Se l'indirizzo selezionato non è disponibile, il portale mostra una X per l'indirizzo IP e sarà necessario selezionare un altro indirizzo.

    Se si preferisce che il **Metodo di allocazione** dell'indirizzo IP privato sia *Dinamico*, selezionare l'opzione e non sarà necessario specificare un indirizzo IP.
3. Dopo aver fatto clic su OK, il pannello si chiude e la nuova configurazione IP compare nell'elenco, come illustrato nell'immagine seguente:

    ![Configurazioni IP](./media/virtual-network-multiple-ip-addresses-portal/figure4.png)

    Fare clic su **OK** per chiudere il pannello **Aggiungi configurazione IP**.
4. È possibile fare clic su **Aggiungi** per aggiungere altre configurazioni IP o chiudere tutti i pannelli aperti per completare l'aggiunta degli indirizzi IP.
5. Aggiungere gli indirizzi IP privati al sistema operativo della VM completando i passaggi per il proprio sistema operativo indicati nella sezione [Aggiungere indirizzi IP al sistema operativo di una VM](#os-config) di questo articolo.

### <a name="add-a-public-ip-address"></a>Aggiungere un indirizzo IP pubblico

Per aggiungere un indirizzo IP pubblico è necessario associare una risorsa indirizzo IP pubblico a una configurazione IP nuova o esistente.

> [!NOTE]
> Per gli indirizzi IP pubblici è prevista una tariffa nominale. Per altre informazioni sui prezzi degli indirizzi IP, vedere la pagina [Prezzi per gli indirizzi IP](https://azure.microsoft.com/pricing/details/ip-addresses) . È previsto un limite per il numero di indirizzi IP pubblici che possono essere usati in una sottoscrizione. Per altre informazioni sui limiti, vedere l'articolo [Limiti di Azure](../azure-subscription-service-limits.md#networking-limits).
> 

### <a name="a-namecreate-public-ipacreate-a-public-ip-address-resource"></a><a name="create-public-ip"></a>Creare una risorsa indirizzo IP pubblico

Un indirizzo IP pubblico consiste in una singola impostazione per una risorsa indirizzo IP pubblico. Se si dispone di una risorsa indirizzo IP pubblico che non è attualmente associata a una configurazione IP e la si vuole associare a una configurazione IP, ignorare i passaggi seguenti e completare quelli della sezione appropriata fra quelle riportate di seguito. Se non si dispone di una risorsa indirizzo IP pubblico, completare la procedura seguente per crearne una:

1. Passare al portale di Azure all'indirizzo https://portal.azure.com e, se necessario, eseguire l'accesso.
3. Nel portale fare clic su **Nuovo** > **Rete** > **Indirizzo IP pubblico**.
4. Nel pannello **Crea indirizzo IP pubblico** che viene visualizzato immettere un **Nome**, selezionare un tipo di **Assegnazione indirizzi IP**, una **Sottoscrizione**, un **Gruppo di risorse** e un **Percorso**, quindi fare clic su **Crea**, come illustrato nell'immagine seguente:

    ![Creare una risorsa indirizzo IP pubblico](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Completare i passaggi in una delle sezioni seguenti per associare la risorsa indirizzo IP pubblico a una configurazione IP.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Associare la risorsa indirizzo IP pubblico a una nuova configurazione IP

1. Completare i passaggi della sezione [Passaggi di base](#coreadd) di questo articolo.
2. Fare clic su **Aggiungi**. Nel pannello **Aggiungi configurazione IP** che viene visualizzato creare una configurazione IP denominata *IPConfig-4*. Attivare l'opzione **Indirizzo IP pubblico** e selezionare una risorsa indirizzo IP pubblico esistente e disponibile dal pannello **Scegli indirizzo IP pubblico**, come mostrato nell'immagine seguente:

    ![Nuova configurazione IP](./media/virtual-network-multiple-ip-addresses-portal/figure6.png)

    Dopo aver selezionato la risorsa indirizzo IP pubblico, fare clic su **OK** e il pannello verrà chiuso. Se non si dispone di un indirizzo IP pubblico esistente, è possibile crearne uno completando la procedura descritta nella sezione [Creare una risorsa indirizzo IP pubblico](#create-public-ip) di questo articolo. 

3. Rivedere la nuova configurazione IP, come mostrato nell'immagine seguente:

    ![Configurazioni IP](./media/virtual-network-multiple-ip-addresses-portal/figure7.png)

    > [!NOTE]
    > Anche se non è stato fatto in modo esplicito, un indirizzo IP privato è stato comunque assegnato alla configurazione IP, in quanto tutte le configurazioni IP devono avere un indirizzo IP privato.
    >

4. È possibile fare clic su **Aggiungi** per aggiungere altre configurazioni IP o chiudere tutti i pannelli aperti per completare l'aggiunta degli indirizzi IP.
5. Aggiungere l'indirizzo IP privato al sistema operativo della VM completando i passaggi relativi al sistema operativo indicati nella sezione [Aggiungere indirizzi IP al sistema operativo di una VM](#os-config) di questo articolo. Non aggiungere l'indirizzo IP pubblico al sistema operativo.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Associare la risorsa indirizzo IP pubblico a una configurazione IP esistente

1. Completare i passaggi della sezione [Passaggi di base](#coreadd) di questo articolo.
2. Selezionare la configurazione IP a cui aggiungere la risorsa indirizzo IP pubblico, abilitare l'indirizzo IP pubblico e selezionare una risorsa indirizzo IP pubblico esistente e disponibile. Nell'esempio illustrato nella figura seguente la risorsa indirizzo IP pubblico *myPublicIp3* è associata a *IPConfig-3*.

    ![Configurazione IP esistente](./media/virtual-network-multiple-ip-addresses-portal/figure8.png)

    Dopo aver selezionato la risorsa indirizzo IP pubblico, fare clic su **Salva** e i pannelli verranno chiusi. Se non si dispone di un indirizzo IP pubblico esistente, è possibile crearne uno completando la procedura descritta nella sezione [Creare una risorsa indirizzo IP pubblico](#create-public-ip) di questo articolo.

3. Rivedere la nuova configurazione IP, come mostrato nell'immagine seguente:

    ![Configurazioni IP](./media/virtual-network-multiple-ip-addresses-portal/figure9.png)

4. È possibile fare clic su **Aggiungi** per aggiungere altre configurazioni IP o chiudere tutti i pannelli aperti per completare l'aggiunta degli indirizzi IP. Non aggiungere l'indirizzo IP pubblico al sistema operativo.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]



<!--HONumber=Dec16_HO2-->


