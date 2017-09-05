---
title: Creare la prima rete virtuale di Azure | Microsoft Docs
description: Informazioni su come creare una rete virtuale di Azure, connettere alla rete due macchine virtuali e connettersi alle macchine virtuali.
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2016
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: e653764d7cb514d50b44fadd0cc5963dd404d99e
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---

# <a name="create-your-first-virtual-network"></a>Creare la prima rete virtuale

Informazioni su come creare una rete virtuale con due subnet, creare due macchine virtuali (VM, Virtual Machine) e connettere ciascuna di esse a una delle subnet, come illustrato nella figura seguente:

![Diagramma della rete virtuale](./media/virtual-network-get-started-vnet-subnet/vnet-diagram.png)

Una rete virtuale di Azure (VNet) è una rappresentazione della propria rete personalizzata nel cloud. È possibile controllare le impostazioni della rete di Azure network e definire blocchi di indirizzi DHCP, impostazioni DNS, criteri di sicurezza e routing. Per altre informazioni sui concetti relativi alle reti virtuali, vedere l'articolo [Panoramica delle reti virtuali](virtual-networks-overview.md). Per creare le risorse mostrate nella figura, seguire questa procedura:

1. [Creare una rete virtuale con due subnet](#create-vnet)
2. [Creare due VM, ciascuna con una scheda di interfaccia di rete ](#create-vms) e associare un gruppo di sicurezza di rete a ogni scheda di interfaccia di rete
3. [Eseguire la connessione da e verso le VM](#connect-to-from-vms)
4. [Eliminare tutte le risorse](#delete-resources). Alcune delle risorse create in questo esercizio comportano spese durante l'esecuzione del provisioning. Per ridurre al minimo i costi, dopo aver completato l'esercizio verificare di completare i passaggi di questa sezione per eliminare le risorse create.

Dopo aver completato i passaggi descritti in questo articolo, si avrà una conoscenza di base dei possibili usi di una rete virtuale. Sono inoltre indicati alcuni passaggi successivi che consentono di acquisire altre informazioni sull'uso delle reti virtuali a un livello più profondo.

## <a name="create-vnet"></a>Creare una rete virtuale con due subnet

Per creare una rete virtuale con due subnet, seguire questa procedura. Per controllare il flusso del traffico tra le subnet vengono in genere usate subnet differenti.

1. Accedere al [Portale di Azure](<https://portal.azure.com>). Se non si ha già di un account, è possibile iscriversi per ottenere una [versione di valutazione gratuita della durata di un mese](https://azure.microsoft.com/free). 
2. Nel riquadro **Preferiti** del portale fare clic su **Nuovo**.
3. Nel pannello **Nuovo** fare clic su **Rete**. Nel pannello **Rete** fare clic su **Rete virtuale**, come illustrato nella figura seguente:

    ![Diagramma della rete virtuale](./media/virtual-network-get-started-vnet-subnet/virtual-network.png)

4.  Nel pannello **Rete virtuale** lasciare selezionato *Resource Manager* come modello di distribuzione e fare clic su **Crea**.
5.  Nel pannello **Crea rete virtuale** visualizzato immettere i valori seguenti e quindi fare clic su **Crea**:

    |**Impostazione**|**Valore**|**Dettagli**|
    |---|---|---|
    |**Nome**|*MyVNet*|Il nome deve essere univoco all'interno del gruppo di risorse.|
    |**Spazio degli indirizzi**|*10.0.0.0/16*|È possibile specificare uno spazio degli indirizzi a piacere usando la notazione CIDR.|
    |**Nome della subnet**|*Front-end*|Il nome della subnet deve essere univoco all'interno della rete virtuale.|
    |**Intervallo di indirizzi subnet**|*10.0.0.0/24*| L'intervallo specificato deve essere presente all'interno dello spazio degli indirizzi definito per la rete virtuale.|
    |**Sottoscrizione**|*[Sottoscrizione in uso]*|Selezionare una sottoscrizione in cui creare la rete virtuale. Una rete virtuale può essere presente all'interno di una sola sottoscrizione.|
    |**Gruppo di risorse**|**Crea nuovo:** *MyRG*|Creare un gruppo di risorse. Il nome del gruppo di risorse deve essere univoco all'interno della sottoscrizione selezionata. Per altre informazioni sui gruppi di risorse, vedere l'articolo [Panoramica di Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups).|
    |**Posizione**|*Stati Uniti occidentali*| In genere viene selezionata la posizione più vicina alla posizione fisica.|

    La creazione della rete virtuale richiede qualche secondo. Dopo questa operazione, viene visualizzato il dashboard del portale di Azure.

6. Dopo aver creato la rete virtuale, nel riquadro **Preferiti** del portale di Azure fare clic su **Tutte le risorse**. Nel pannello **Tutte le risorse** fare clic sulla rete virtuale **MyVNet**. Se nella sottoscrizione selezionata sono già presenti delle risorse, è possibile immettere *MyVNet* nella casella **Filtra per nome** per accedere facilmente alla rete virtuale.
7. Viene visualizzato il pannello **MyVNet** con informazioni sulla rete virtuale, come illustrato nella figura seguente:

    ![Diagramma della rete virtuale](./media/virtual-network-get-started-vnet-subnet/myvnet.png)

8. Come illustrato nella figura precedente, fare clic su **Subnet** per visualizzare l'elenco delle subnet incluse nella rete virtuale. L'unica subnet presente è **Front-end**, creata al passaggio 5.
9. Nel pannello MyVNet - Subnet fare clic su **+ Subnet** per creare una subnet con le informazioni seguenti e quindi fare clic su **OK** per procedere alla creazione:

    |**Impostazione**|**Valore**|**Dettagli**|
    |---|---|---|
    |**Nome**|*Back-end*|Il nome deve essere univoco all'interno della rete virtuale.|
    |**Intervallo di indirizzi**|*10.0.1.0/24*|L'intervallo specificato deve essere presente all'interno dello spazio degli indirizzi definito per la rete virtuale.|
    |**Gruppo di sicurezza di rete** e **Tabella di route**|*Nessuno* (impostazione predefinita)|I gruppi di sicurezza di rete sono descritti più avanti in questo articolo. Per altre informazioni sulle route definite dall'utente, vedere l'articolo [Route definite dall'utente](virtual-networks-udr-overview.md).|

10. Dopo aver aggiunto la nuova subnet alla rete virtuale, è possibile chiudere il pannello **MyVNet - Subnet** e quindi il pannello **Tutte le risorse**.

## <a name="create-vms"></a>Creare macchine virtuali

Dopo aver creato la rete virtuale e le subnet, è possibile creare le VM. Ai fini di questo esercizio, entrambe le VM eseguono il sistema operativo Windows Server. Possono tuttavia eseguire qualsiasi sistema operativo supportato da Azure, incluse diverse distribuzioni di Linux.

### <a name="create-web-server-vm"></a>Creare la VM del server Web

Per creare la VM del server Web, seguire questa procedura:

1. Nel riquadro Preferiti del portale di Azure fare clic su **Nuovo**, **Calcolo** e quindi su **Windows Server 2016 Datacenter**.
2. Nel pannello **Windows Server 2016 Datacenter** fare clic su **Crea**.
3. Nel pannello **Informazioni di base** visualizzato immettere o selezionare i valori seguenti e quindi fare clic su **OK**:

    |**Impostazione**| **Valore**|**Dettagli**|
    |---|---|---|
    |**Nome**|*MyWebServer*|Questa VM funge da server Web a cui si connettono le risorse Internet.|
    |**Tipo di disco VM**|*SSD*|
    |**Nome utente**|*A scelta dell'utente*|
    |**Password e Conferma password**|*A scelta dell'utente*|
    | **Sottoscrizione**|*<Your subscription>*|La sottoscrizione deve essere quella selezionata al passaggio 5 della sezione [Creare una rete virtuale con due subnet](#create-vnet) di questo articolo. La rete virtuale a cui viene connessa una VM deve essere presente nella stessa sottoscrizione della VM.|
    |**Gruppo di risorse**|**Usare il gruppo di risorse esistente:** Selezionare *MyRG*|Anche se in questo esercizio viene usato lo stesso gruppo di risorse definito per la rete virtuale, non è necessario che le risorse siano presenti nello stesso gruppo.|
    |**Posizione**|*Stati Uniti occidentali*|La posizione deve essere quella selezionata al passaggio 5 della sezione [Creare una rete virtuale con due subnet](#create-vnet) di questo articolo. Le VM e le reti virtuali a cui queste si connettono devono trovarsi nella stessa posizione.|

4. Nel pannello **Scegli una dimensione** fare clic su *DS1_V2 Standard* e quindi su **Seleziona**. Per un elenco di tutte le dimensioni di VM Windows supportate da Azure, vedere l'articolo [Dimensioni per le macchine virtuali Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
5. Nel pannello **Impostazioni** immettere o selezionare i valori seguenti e quindi fare clic su **OK**:

    |**Impostazione**|**Valore**|**Dettagli**|
    |---|---|---|
    |**Archiviazione: Usa dischi gestiti**|*Sì*||
    |**Rete virtuale**| Selezionare *MyVNet*|È possibile selezionare qualsiasi rete virtuale presente nella stessa posizione della VM che si sta creando. Per altre informazioni sulle reti virtuali e sulle subnet, vedere l'articolo [Reti virtuali](virtual-networks-overview.md).|
    |**Subnet**|Selezionare *Front-end*|È possibile selezionare qualsiasi subnet presente all'interno della rete virtuale.|
    |**Indirizzo IP pubblico**|Accettare il valore predefinito|Un indirizzo IP pubblico consente di connettersi alla VM da Internet. Per altre informazioni sugli indirizzi IP pubblici, vedere l'articolo [Indirizzi IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).|
    |**Gruppo di sicurezza di rete (firewall)**|Accettare il valore predefinito|Fare clic sul gruppo di sicurezza di rete predefinito **(nuovo) MyWebServer-nsg** creato dal portale per visualizzarne le impostazioni. Nel pannello **Crea gruppo di sicurezza di rete** visualizzato è presente una regola in ingresso che consente il traffico TCP/3389 (RDP) da qualsiasi indirizzo IP di origine.|
    |**Tutti gli altri valori**|Accettare i valori predefiniti|Per altre informazioni sulle impostazioni rimanenti, vedere l'articolo [Panoramica delle macchine virtuali](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|

    I gruppi di sicurezza di rete consentono di creare regole in ingresso e in uscita per il tipo di traffico di rete in ingresso e in uscita dalla VM. Per impostazione predefinita, tutto il traffico in ingresso alla VM viene rifiutato. È possibile aggiungere altre regole in ingresso per TCP/80 (HTTP) e TCP/443 (HTTPS) per un server Web di produzione. Non sono presenti regole per il traffico in uscita perché tutto il traffico in uscita è consentito per impostazione predefinita. È possibile aggiungere o rimuovere regole per controllare il traffico in base ai propri criteri. Per altre informazioni sui gruppi di sicurezza di rete, vedere l'articolo [Gruppi di sicurezza di rete](virtual-networks-nsg.md).

6.  Esaminare le impostazioni nel pannello **Riepilogo** e quindi fare clic su **OK** per creare la VM. Dopo la creazione della VM, nel dashboard del portale viene visualizzato un riquadro relativo allo stato. La creazione può richiedere alcuni minuti. Non è necessario attenderne il completamento. Mentre la creazione della VM è in corso, è possibile procedere con il passaggio successivo.

### <a name="create-database-server-vm"></a>Creare la VM del server di database

Per creare la VM del server di database, seguire questa procedura:

1.  Nel riquadro Preferiti fare clic su **Nuovo**, **Calcolo** e quindi su **Windows Server 2016 Datacenter**.
2.  Nel pannello **Windows Server 2016 Datacenter** fare clic su **Crea**.
3.  Nel pannello **Informazioni di base** immettere o selezionare i valori seguenti e quindi fare clic su **OK**:

    |**Impostazione**|**Valore**|**Dettagli**|
    |---|---|---|
    |**Nome**|*MyDBServer*|Questa VM funge da server di database a cui si connette il server Web. Le risorse Internet, invece, non possono connettersi a questa VM.|
    |**Tipo di disco VM**|*SSD*||
    |**Nome utente**|A scelta dell'utente||
    |**Password e Conferma password**|A scelta dell'utente||
    |**Sottoscrizione**|<Your subscription>|La sottoscrizione deve essere quella selezionata al passaggio 5 della sezione [Creare una rete virtuale con due subnet](#create-vnet) di questo articolo.|
    |**Gruppo di risorse**|**Usare il gruppo di risorse esistente:** Selezionare *MyRG*|Anche se in questo esercizio viene usato lo stesso gruppo di risorse definito per la rete virtuale, non è necessario che le risorse siano presenti nello stesso gruppo.|
    |**Posizione**|*Stati Uniti occidentali*|La posizione deve essere quella selezionata al passaggio 5 della sezione [Creare una rete virtuale con due subnet](#create-vnet) di questo articolo.|

4.  Nel pannello **Scegli una dimensione** fare clic su *DS1_V2 Standard* e quindi su **Seleziona**.
5.  Nel pannello **Impostazioni** immettere o selezionare i valori seguenti e quindi fare clic su **OK**:

    |**Impostazione**|**Valore**|**Dettagli**|
    |----|----|---|
    |**Archiviazione: Usa dischi gestiti**|*Sì*||
    |**Rete virtuale**|Selezionare *MyVNet*|È possibile selezionare qualsiasi rete virtuale presente nella stessa posizione della VM che si sta creando.|
    |**Subnet**|Selezionare *Back-end* facendo clic sulla casella **Subnet** e quindi selezionando **Back-end** nel pannello **Scegliere una subnet**|È possibile selezionare qualsiasi subnet presente all'interno della rete virtuale.|
    |**Indirizzo IP pubblico**|Nessuno: fare clic sull'indirizzo predefinito e quindi su **Nessuno** nel pannello **Scegli indirizzo IP pubblico**|Senza un indirizzo IP pubblico, è possibile connettersi alla VM soltanto da un'altra VM connessa alla stessa rete virtuale. Non è possibile connettersi alla VM direttamente da Internet.|
    |**Gruppo di sicurezza di rete (firewall)**|Accettare il valore predefinito| Questo gruppo di sicurezza di rete ha la stessa regola in ingresso predefinita del gruppo di sicurezza di rete creato per la VM MyWebServer. È possibile aggiungere un'altra regola in ingresso per TCP/1433 (MS SQL) per un server di database. Non sono presenti regole per il traffico in uscita perché tutto il traffico in uscita è consentito per impostazione predefinita. È possibile aggiungere o rimuovere regole per controllare il traffico in base ai propri criteri.|
    |**Tutti gli altri valori**|Accettare i valori predefiniti||

6.  Esaminare le impostazioni nel pannello **Riepilogo** e quindi fare clic su **OK** per creare la VM. Dopo la creazione della VM, nel dashboard del portale viene visualizzato un riquadro relativo allo stato. La creazione può richiedere alcuni minuti. Non è necessario attenderne il completamento. Mentre la creazione della VM è in corso, è possibile procedere con il passaggio successivo.

## <a name="review"></a>Esaminare le risorse

Anche se sono state create una rete virtuale e due VM, il portale di Azure ha creato automaticamente diverse risorse aggiuntive nel gruppo di risorse MyRG. Esaminare il contenuto del gruppo di risorse MyRG seguendo questa procedura:

1. Nel riquadro **Preferiti** fare clic su **Altri servizi**.
2. Nel riquadro **Altri servizi** digitare *Gruppi di risorse* nella casella contenente la parola *Filtro*. Quando **Gruppi di risorse** viene visualizzato nell'elenco filtrato, selezionarlo.
3. Nel riquadro **Gruppi di risorse** fare clic sul gruppo di risorse *MyRG*. Se nella sottoscrizione sono presenti diversi gruppi di risorse, è possibile digitare *MyRG* nella casella contenente il testo *Filtra per nome* per accedere rapidamente al gruppo di risorse MyRG.
4.  Nel pannello **MyRG** è possibile notare che il gruppo di risorse contiene 12 risorse, come illustrato nella figura seguente:

    ![Contenuto del gruppo di risorse](./media/virtual-network-get-started-vnet-subnet/resource-group-contents.png)

Per altre informazioni, vedere gli articoli introduttivi relativi a [macchine virtuali](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [dischi](../virtual-machines/windows/about-disks-and-vhds.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [account di archiviazione](../storage/common/storage-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json). È possibile visualizzare i due gruppi di sicurezza di rete creati automaticamente dal portale. È inoltre possibile notare che il portale ha creato due risorse scheda di interfaccia di rete. Una scheda di interfaccia di rete consente a una VM di connettersi ad altre risorse attraverso la rete virtuale. Per altre informazioni, vedere l'articolo [Interfacce di rete](virtual-network-network-interface.md). Il portale ha creato anche una risorsa indirizzo IP pubblico. Un indirizzo IP pubblico consiste in una singola impostazione per una risorsa indirizzo IP pubblico. Per altre informazioni sugli indirizzi IP pubblici, vedere l'articolo [Indirizzi IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).

## <a name="connect-to-from-vms"></a>Connettersi alle VM

Dopo aver creato la rete virtuale e le due VM, è possibile connettersi alle VM seguendo la procedura descritta nelle sezioni seguenti:

### <a name="connect-from-internet"></a>Connettersi alla VM del server Web da Internet

Per connettersi alla VM del server Web da Internet, seguire questa procedura:

1. Nel portale aprire il gruppo di risorse MyRG seguendo la procedura descritta nella sezione [Esaminare le risorse](#review) di questo articolo.
2. Nel pannello **MyRG** fare clic sulla VM **MyWebServer**.
3. Nel pannello **MyWebServer** fare clic su **Connetti**, come illustrato nella figura seguente:

    ![Connettersi alla VM del server Web](./media/virtual-network-get-started-vnet-subnet/webserver.png)

4. Consentire al browser di scaricare il file *MyWebServer.rdp* e aprirlo.
5. Se viene visualizzata una finestra di dialogo in cui si informa che il server di pubblicazione della connessione remota non può essere verificato, fare clic su **Connetti**.
6. Al momento dell'immissione delle credenziali, verificare di usare il nome utente e la password specificati al passaggio 3 della sezione [Creare la VM del server Web](#create-web-server-vm) di questo articolo. Se nella casella **Sicurezza di Windows** visualizzata non sono riportate le credenziali corrette, può essere necessario fare clic su **Altre scelte** e quindi su **Usa un account diverso** e specificare il nome utente e la password corretti. Fare clic su **OK** per connettersi alla VM.
7. Se viene visualizzata la finestra di dialogo **Connessione desktop remoto** in cui si informa che l'identità del computer remoto non può essere verificata, fare clic su **Sì**.
8. A questo punto è stata effettuata la connessione alla VM MyWebServer da Internet. Lasciare aperta la connessione del desktop remoto per completare i passaggi della sezione successiva.

La connessione remota è all'indirizzo IP pubblico assegnato alla risorsa indirizzo IP pubblico creata dal portale al passaggio 5 della sezione [Creare una rete virtuale con due subnet](#create-vnet) di questo articolo. La connessione è consentita perché la regola predefinita creata nel gruppo di sicurezza di rete **MyWebServer-nsg** ha permesso l'ingresso tramite TCP/3389 (RDP) alla VM da qualsiasi indirizzo IP di origine. Se si prova a connettersi alla VM tramite qualsiasi altra porta, la connessione ha esito negativo, a meno che non si aggiungano altre regole in ingresso al gruppo di sicurezza di rete per abilitare le altre porte.

>[!NOTE]
>Se al gruppo di sicurezza di rete vengono aggiunte altre regole in ingresso, verificare che nel firewall di Windows siano aperte le stesse porte. In caso contrario, la connessione ha esito negativo.
>

### <a name="connect-to-internet"></a>Connettersi a Internet dalla VM del server Web

Per connettersi in uscita a Internet dalla VM del server Web, seguire questa procedura:

1. Se non è già disponibile una connessione remota aperta alla VM MyWebServer, crearne una seguendo la procedura descritta nella sezione [Connettersi alla VM del server Web da Internet](#connect-from-internet) di questo articolo.
2. Aprire Internet Explorer dal desktop di Windows. Nella finestra di dialogo **Configura Internet Explorer 11** fare clic su **Non usare le impostazioni consigliate** e quindi su **OK**. È opportuno accettare le impostazioni consigliate per un server di produzione.
3. Nella barra degli indirizzi di Internet Explorer digitare [bing.com](http:www.bing.com). Se viene visualizzata una finestra di dialogo di Internet Explorer, fare clic su **Aggiungi**, quindi di nuovo su **Aggiungi** nella finestra di dialogo **Siti attendibili** e infine fare clic su **Chiudi**. Ripetere questa procedura per qualsiasi altra finestra di dialogo di Internet Explorer.
4. Nella pagina di ricerca di Bing immettere *whatsmyipaddress* e quindi fare clic sul pulsante con la lente di ingrandimento. Bing restituisce l'indirizzo IP pubblico assegnato alla risorsa indirizzo IP pubblico creata dal portale al momento della creazione della VM. Se si esaminano le impostazioni della risorsa **MyWebServer-ip**, si noterà che l'indirizzo IP è uguale a quello assegnato alla risorsa indirizzo IP pubblico, come illustrato nella figura seguente. L'indirizzo IP assegnato alla VM, tuttavia, è diverso.

    ![Connettersi alla VM del server Web](./media/virtual-network-get-started-vnet-subnet/webserver-pip.png)

5.  Lasciare aperta la connessione del desktop remoto per completare i passaggi della sezione successiva.

È possibile connettersi a Internet dalla VM perché la connettività in uscita da quest'ultima è consentita per impostazione predefinita. È possibile limitare la connettività in uscita tramite l'applicazione di regole di aggiunta al gruppo di sicurezza di rete applicato alla scheda di interfaccia di rete, alla subnet a cui la scheda di interfaccia di rete è connessa o a entrambi.

Se la VM viene impostata sullo stato arrestato (deallocato) mediante il portale, l'indirizzo IP pubblico può cambiare. Se è necessario che l'indirizzo IP pubblico rimanga sempre invariato, è possibile usare per l'indirizzo IP il metodo di allocazione statica anziché quello di allocazione dinamica (impostazione predefinita). Per altre informazioni sulle differenze tra i due metodi di allocazione, vedere l'articolo [Tipi di indirizzi IP e metodi di allocazione](virtual-network-ip-addresses-overview-arm.md).

### <a name="webserver-to-dbserver"></a>Connettersi alla VM del server di database dalla VM del server Web

Per connettersi alla VM del server di database dalla VM del server Web, seguire questa procedura:

1. Se non è già disponibile una connessione remota aperta alla VM MyWebServer, crearne una seguendo la procedura descritta nella sezione [Connettersi alla VM del server Web da Internet](#connect-from-internet) di questo articolo.
2. Fare clic sul pulsante Start nell'angolo inferiore sinistro del desktop di Windows e quindi iniziare a digitare *desktop remoto*. Quando nel menu Start viene visualizzata l'opzione **Connessione desktop remoto**, selezionarla.
3. Nella finestra di dialogo **Connessione desktop remoto** immettere *MyDBServer* come nome del computer e quindi fare clic su **Connetti**.
4. Immettere il nome utente e la password specificati al passaggio 3 della sezione [Creare la VM del server di database](#create-database-server-vm) di questo articolo e quindi fare clic su **OK**.
5. Se viene visualizzata una finestra di dialogo in cui si informa che l'identità del computer remoto non può essere verificata, fare clic su **Sì**.
6. Lasciare aperta la connessione del desktop remoto a entrambi i server per completare i passaggi della sezione successiva.

È possibile connettersi alla VM del server di database dalla VM del server Web per i motivi seguenti:

- Le connessioni TCP/3389 in ingresso sono abilitate per qualsiasi IP di origine nel gruppo di sicurezza di rete predefinito creato al passaggio 5 della sezione [Creare la VM del server di database](#create-database-server-vm) di questo articolo.
- È possibile avviare la connessione dalla VM del server Web, che è connessa alla stessa rete virtuale della VM del server di database. Per connettersi a una VM a cui non è assegnato un indirizzo IP pubblico, è necessario connettersi da un'altra VM connessa alla stessa rete virtuale, anche se la VM è connessa a una subnet diversa.
- Anche se le VM sono connesse a subnet diverse, Azure crea route predefinite che abilitano la connettività tra le subnet. È tuttavia possibile sostituire le route predefinite e crearne di proprie. Per altre informazioni sul routing in Azure, vedere l'articolo [Route definite dall'utente](virtual-networks-udr-overview.md).

Se si prova ad avviare una connessione remota alla VM del server di database da Internet, come descritto nella sezione [Connettersi alla VM del server Web da Internet](#connect-from-internet) di questo articolo, si noterà che l'opzione **Connetti** è disattivata. L'opzione Connetti è disattivata perché alla VM non è assegnato un indirizzo IP pubblico: questa situazione rende impossibili le connessioni in entrata alla VM da Internet.

### <a name="connect-to-the-internet-from-the-database-server-vm"></a>Connettersi a Internet dalla VM del server di database

Per connettersi in uscita a Internet dalla VM del server di database, seguire questa procedura:

1. Se non è già disponibile una connessione remota aperta alla VM MyDBServer dalla VM MyWebServer, seguire la procedura descritta nella sezione [Connettersi alla VM del server di database dalla VM del server Web](#webserver-to-dbserver) di questo articolo.
2. Dal desktop di Windows della VM MyDBServer aprire Internet Explorer e selezionare le opzioni delle finestre di dialogo come indicato ai passaggi 2 e 3 della sezione [Connettersi a Internet dalla VM del server Web](#connect-to-internet) di questo articolo.
3. Nella barra degli indirizzi immettere [bing.com](http:www.bing.com).
4. Fare clic su **Aggiungi** nella finestra di Internet Explorer visualizzata, quindi fare clic su **Aggiungi**  e scegliere **Chiudi** nella finestra di dialogo **Siti attendibili**. Seguire questa procedura nelle altre finestre di dialogo eventualmente visualizzate.
5. Nella pagina di ricerca di Bing immettere *whatsmyipaddress* e quindi fare clic sul pulsante con la lente di ingrandimento. Bing restituisce l'indirizzo IP pubblico attualmente assegnato alla VM dall'infrastruttura di Azure. 6. Chiudere la connessione remota alla VM MyDBServer dalla VM MyWebServer e quindi chiudere la connessione remota alla VM MyWebServer.

La connessione in uscita a Internet è consentita perché tutto il traffico in uscita è consentito per impostazione predefinita, anche se alla VM MyDBServer non è assegnata una risorsa indirizzo IP pubblico. Tutte le VM sono in grado di connettersi in uscita a Internet per impostazione predefinita, anche se alla VM non è assegnata una risorsa indirizzo IP pubblico. Non è tuttavia possibile connettersi all'indirizzo IP pubblico da Internet, come invece avviene per la VM MyWebServer a cui è assegnata una risorsa indirizzo IP pubblico.

## <a name="delete-resources"></a>Eliminare tutte le risorse

Per eliminare tutte le risorse create nell'esecuzione dell'esercizio, seguire questa procedura:

1. Per visualizzare il gruppo di risorse MyRG creato in questo esercizio, completare i passaggi da 1 a 3 della procedura descritta nella sezione [Esaminare le risorse](#review) di questo articolo. Esaminare di nuovo le risorse presenti nel gruppo di risorse. Se è stato creato il gruppo di risorse MyRG, come descritto nei passaggi precedenti, verranno visualizzate le 12 risorse mostrate nella figura relativa al passaggio 4.
2. Nel pannello MyRG fare clic sul pulsante **Elimina**.
3. Il portale richiede di digitare il nome del gruppo di risorse per confermare che si desidera effettivamente procedere all'eliminazione. Se sono presenti risorse diverse da quelle mostrate nel passaggio 4 della sezione [Esaminare le risorse](#review) di questo articolo, fare clic su **Annulla**. Se sono visualizzate soltanto le 12 risorse create come parte di questo esercizio, digitare *MyRG* come nome del gruppo di risorse e quindi fare clic su **Elimina**. L'eliminazione di un gruppo di risorse determina l'eliminazione di tutte le risorse in esso contenute. È quindi consigliabile verificare sempre il contenuto di un gruppo prima di eliminarlo. Il portale elimina tutte le risorse contenute nel gruppo di risorse e quindi elimina il gruppo. Questo processo richiede alcuni minuti.

## <a name="next-steps"></a>Passaggi successivi

In questo esercizio sono state create una rete virtuale e due VM. Durante la creazione delle VM sono state specificate alcune impostazioni personalizzate e sono state accettate diverse impostazioni predefinite. Prima di distribuire reti e macchine virtuali nell'ambiente di produzione, è consigliabile leggere gli articoli seguenti per verificare di conoscere tutte le impostazioni disponibili:

- [Reti virtuali](virtual-networks-overview.md)
- [Indirizzi IP pubblici](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- [Interfacce di rete](virtual-network-network-interface.md)
- [Gruppi di sicurezza di rete](virtual-networks-nsg.md)
- [Macchine virtuali](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

