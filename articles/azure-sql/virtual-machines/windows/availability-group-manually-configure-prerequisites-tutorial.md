---
title: 'Esercitazione: prerequisiti per un gruppo di disponibilità'
description: Questa esercitazione illustra come configurare i prerequisiti per la creazione di un SQL Server Always On gruppo di disponibilità nelle macchine virtuali di Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 278e5feb327c1376b7644050f414f680334d5c50
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91263233"
---
# <a name="prerequisites-for-creating-always-on-availability-groups-on-sql-server-on-azure-virtual-machines"></a>Prerequisiti per la creazione di Gruppi di disponibilità Always On in SQL Server in macchine virtuali di Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questa esercitazione illustra come completare i prerequisiti per la creazione di un [SQL Server Always on gruppo di disponibilità in macchine virtuali (VM) di Azure](availability-group-manually-configure-tutorial.md). Una volta completati i prerequisiti, saranno disponibili un controller di dominio, due SQL Server macchine virtuali e un server di controllo del mirroring in un singolo gruppo di risorse.

**Tempo stimato**: il completamento dei prerequisiti potrebbe richiedere un paio d'ore. Il tempo è dedicato principalmente alla creazione delle macchine virtuali.

Il diagramma seguente illustra le operazioni di compilazione nell'esercitazione.

![gruppo di disponibilità](./media/availability-group-manually-configure-prerequisites-tutorial-/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Esaminare la documentazione relativa ai gruppi di disponibilità

L'esercitazione presuppone una conoscenza di base dei gruppi di disponibilità AlwaysOn di SQL Server. Se non si ha familiarità con questa tecnologia, vedere [Panoramica di gruppi di disponibilità AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Creare un account Azure

È necessario un account Azure. È possibile [aprire un account Azure gratuito](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic) o [attivare i benefici della sottoscrizione di Visual Studio](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **+** per creare un nuovo oggetto nel portale.

   ![Nuovo oggetto](./media/availability-group-manually-configure-prerequisites-tutorial-/01-portalplus.png)

3. Digitare **gruppo di risorse** nella finestra di ricerca del **Marketplace**.

   ![Resource group](./media/availability-group-manually-configure-prerequisites-tutorial-/01-resourcegroupsymbol.png)

4. Selezionare **gruppo di risorse**.
5. Selezionare **Crea**.
6. In **Nome gruppo di risorse** digitare un nome per il gruppo di risorse. ad esempio digitare **sql-ha-rg**.
7. Se si hanno più sottoscrizioni di Azure, verificare che la sottoscrizione sia la sottoscrizione di Azure in cui si vuole creare il gruppo di disponibilità.
8. Selezionare una località. La località corrisponde all'area di Azure in cui si vuole creare il gruppo di disponibilità. Questo articolo illustra la creazione di tutte le risorse in un'unica località di Azure.
9. Verificare che l'opzione **Aggiungi al dashboard** sia selezionata. Questa impostazione facoltativa inserisce un collegamento per il gruppo di risorse nel dashboard del portale di Azure.

   ![Resource group](./media/availability-group-manually-configure-prerequisites-tutorial-/01-resourcegroup.png)

10. Selezionare **Crea** per creare il gruppo di risorse.

Azure crea il gruppo di risorse e aggiunge un collegamento a quest'ultimo nel portale.

## <a name="create-the-network-and-subnets"></a>Creare la rete e le subnet

Il passaggio successivo prevede la creazione di reti e di subnet nel gruppo di risorse di Azure.

La soluzione usa una rete virtuale con due subnet. Per altre informazioni sulle reti in Azure, vedere [Panoramica di Rete virtuale](../../../virtual-network/virtual-networks-overview.md).

Per creare la rete virtuale nel portale di Azure:

1. Nel gruppo di risorse selezionare **+ Aggiungi**. 

   ![Nuovo elemento](./media/availability-group-manually-configure-prerequisites-tutorial-/02-newiteminrg.png)
2. Cercare **rete virtuale**.

     ![Cercare la rete virtuale](./media/availability-group-manually-configure-prerequisites-tutorial-/04-findvirtualnetwork.png)
3. Selezionare **Rete virtuale**.
4. Nella **rete virtuale**selezionare il modello di distribuzione **Gestione risorse** e quindi selezionare **Crea**.

    La tabella seguente descrive le impostazioni per la rete virtuale:

   | **Campo** | valore |
   | --- | --- |
   | **Nome** |autoHAVNET |
   | **Spazio degli indirizzi** |10.33.0.0/24 |
   | **Nome della subnet** |Amministrativi |
   | **Intervallo di indirizzi subnet** |10.33.0.0/29 |
   | **Sottoscrizione** |Specificare la sottoscrizione da usare. Se è disponibile una sola sottoscrizione, il valore di **Sottoscrizione** non è impostato. |
   | **Gruppo di risorse** |Scegliere **Utilizza esistente** e selezionare il nome del gruppo di risorse. |
   | **Posizione** |Specificare la località di Azure. |

   Lo spazio indirizzi e l'intervallo di indirizzi subnet possono essere diversi da quelli della tabella. A seconda della sottoscrizione, sul portale verrà suggerito uno spazio indirizzi disponibile e un intervallo di indirizzi subnet corrispondente. Se non è disponibile uno spazio indirizzi sufficiente, usare un'altra sottoscrizione.

   L'esempio usa il nome di subnet **Admin**. Questa subnet verrà usata per i controller di dominio.

5. Selezionare **Crea**.

   ![Configurare la rete virtuale](./media/availability-group-manually-configure-prerequisites-tutorial-/06-configurevirtualnetwork.png)

In questo modo, si torna al dashboard del portale e Azure invia una notifica dopo la creazione della rete.

### <a name="create-a-second-subnet"></a>Creare una seconda subnet

La nuova rete virtuale dispone di una subnet, denominata **Admin**. I controller di dominio usano questa subnet. Le VM di SQL Server usano una seconda subnet denominata **SQL**. Per configurare questa subnet:

1. Nel Dashboard selezionare il gruppo di risorse creato, **SQL-ha-RG**. Trovare la rete nel gruppo di risorse in **Risorse**.

    Se **SQL-ha-RG** non è visibile, trovarlo selezionando **gruppi di risorse** e filtrando in base al nome del gruppo di risorse.

2. Selezionare **AUTOhavnt** nell'elenco di risorse. 
3. Nella rete virtuale **autoHAVNET**, in **Impostazioni** selezionare **Subnets**.

    Si noti la subnet già creata.

   ![Configurare la rete virtuale](./media/availability-group-manually-configure-prerequisites-tutorial-/07-addsubnet.png)

5. Per creare una seconda subnet, selezionare **+ subnet**.
6. In **Aggiungi subnet** configurare la subnet digitando **sqlsubnet** in **Nome**. Azure specifica automaticamente un **Intervallo di indirizzi**valido. Verificare che questo intervallo di indirizzi includa almeno 10 indirizzi. In un ambiente di produzione potrebbero essere necessari più indirizzi.
7. Selezionare **OK**.

    ![Configurare la rete virtuale](./media/availability-group-manually-configure-prerequisites-tutorial-/08-configuresubnet.png)

La tabella seguente riepiloga le impostazioni di configurazione della rete:

| **Campo** | valore |
| --- | --- |
| **Nome** |**autoHAVNET** |
| **Spazio degli indirizzi** |Questo valore dipende dagli spazi indirizzi disponibili nella sottoscrizione. Un valore tipico è 10.0.0.0/16. |
| **Nome della subnet** |**admin** |
| **Intervallo di indirizzi subnet** |Questo valore dipende dagli intervalli di indirizzi disponibili nella sottoscrizione. Un valore tipico è 10.0.0.0/24 |
| **Nome della subnet** |**sqlsubnet** |
| **Intervallo di indirizzi subnet** |Questo valore dipende dagli intervalli di indirizzi disponibili nella sottoscrizione. Un valore tipico è 10.0.1.0/24 |
| **Sottoscrizione** |Specificare la sottoscrizione da usare. |
| **Gruppo di risorse** |**SQL-HA-RG** |
| **Posizione** |Specificare la stessa località scelta per il gruppo di risorse. |

## <a name="create-availability-sets"></a>Creare set di disponibilità

Prima di creare le macchine virtuali, è necessario creare i set di disponibilità. I set di disponibilità riducono il tempo di inattività per gli eventi di manutenzione sia pianificati che non pianificati. Un set di disponibilità di Azure è un gruppo logico di risorse che Azure inserisce in domini di errore e domini di aggiornamento fisici. Un dominio di errore assicura che i membri del set di disponibilità dispongano di risorse di alimentazione e di rete separate. Un dominio di aggiornamento assicura che i membri del set di disponibilità non vengano disattivati contemporaneamente per la manutenzione. Per altre informazioni, vedere [Gestire la disponibilità delle macchine virtuali](../../../virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Sono necessari due set di disponibilità, uno per i controller di dominio e il secondo per le VM di SQL Server.

Per creare un set di disponibilità, passare al gruppo di risorse e selezionare **Aggiungi**. Filtrare i risultati digitando **Set di disponibilità**. Selezionare **set di disponibilità** nei risultati, quindi selezionare **Crea**.

Configurare due set di disponibilità in base ai parametri riportati nella tabella seguente:

| **Campo** | Set di disponibilità del controller di dominio | Set di disponibilità di SQL Server |
| --- | --- | --- |
| **Nome** |adavailabilityset |sqlavailabilityset |
| **Gruppo di risorse** |Nome gruppo di risorse |Nome gruppo di risorse |
| **Domini di errore** |3 |3 |
| **Domini di aggiornamento** |5 |3 |

Dopo avere creato i set di disponibilità, tornare al gruppo di risorse nel portale di Azure.

## <a name="create-domain-controllers"></a>Creare controller di dominio

Dopo la creazione della rete, delle subnet e dei set di disponibilità, è possibile creare le macchine virtuali per i controller di dominio.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Creare le macchine virtuali per i controller di dominio

Per creare e configurare i controller di dominio, tornare al gruppo di risorse **SQL-HA-RG** .

1. Selezionare **Aggiungi**. 
2. Digitare **Windows Server 2016 Datacenter**.
3. Selezionare **Windows Server 2016 datacenter**. In **Windows Server 2016 datacenter**verificare che il modello di distribuzione sia **Gestione risorse**, quindi selezionare **Crea**. 

Ripetere i passaggi precedenti per creare due macchine virtuali. Assegnare un nome alle due macchine virtuali:

* ad-primary-dc
* ad-secondary-dc

  > [!NOTE]
  > La macchina virtuale **ad-secondary-dc** per fornire disponibilità elevata per Active Directory Domain Services.
  >

La tabella seguente descrive le impostazioni per queste due macchine:

| **Campo** | valore |
| --- | --- |
| **Nome** |Primo controller di dominio: *ad-primary-dc*.</br>Secondo controller di dominio: *ad-secondary-dc*. |
| **Tipo di disco VM** |SSD |
| **Nome utente** |DomainAdmin |
| **Password** |Contoso!0000 |
| **Sottoscrizione** |*Sottoscrizione in uso* |
| **Gruppo di risorse** |Nome gruppo di risorse |
| **Posizione** |*Località corrente* |
| **Dimensione** |DS1_V2 |
| **Storage** | **Usa dischi gestiti** - **Sì** |
| **Rete virtuale** |autoHAVNET |
| **Subnet** |admin |
| **Indirizzo IP pubblico** |*Lo stesso nome della VM* |
| **Gruppo di sicurezza di rete** |*Lo stesso nome della VM* |
| **Set di disponibilità** |adavailabilityset </br>**Domini di errore**:2 </br>**Domini di aggiornamento**:2|
| **Diagnostica** |Attivato |
| **Account di archiviazione di diagnostica** |*Creato automaticamente* |

   >[!IMPORTANT]
   >È possibile inserire una VM in un set di disponibilità solo in fase di creazione. Non è possibile modificare il set di disponibilità dopo che la VM è stata creata. Vedere [Gestire la disponibilità delle macchine virtuali](../../../virtual-machines/linux/manage-availability.md).

Azure crea le macchine virtuali.

Dopo avere creato le macchine virtuali, configurare il controller di dominio.

### <a name="configure-the-domain-controller"></a>Configurare il controller di dominio

Nei passaggi seguenti configurare la macchina virtuale **ad-primary-dc** come controller di dominio per corp.contoso.com.

1. Nel portale aprire il gruppo di risorse **SQL-HA-RG** e selezionare la macchina virtuale **ad-primary-dc**. In **ad-Primary-DC**selezionare **Connetti** per aprire un file RDP per l'accesso desktop remoto.

    ![Connettersi a una macchina virtuale](./media/availability-group-manually-configure-prerequisites-tutorial-/20-connectrdp.png)

2. Accedere con l'account amministratore ( **\DomainAdmin**) e la password (**Contoso!0000**) configurati.
3. Per impostazione predefinita, verrà visualizzato il dashboard **Server Manager** .
4. Selezionare il collegamento **Aggiungi ruoli e funzionalità** nel dashboard.

    ![Server Manager - Aggiungere ruoli](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)

5. Selezionare **Avanti** fino a visualizzare la sezione **Ruoli server**.
6. Selezionare i ruoli **Active Directory Domain Services** e **Server DNS**. Quando richiesto, aggiungere eventuali funzionalità aggiuntive necessarie per questi ruoli.

   > [!NOTE]
   > Windows visualizza un avviso per indicare che non è presente alcun indirizzo IP statico. Se si sta testando la configurazione, selezionare **continue (continua**). Per gli scenari di produzione, impostare l'indirizzo IP come statico nel portale di Azure o [usare PowerShell per impostare l'indirizzo IP statico della macchina virtuale del controller di dominio](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >

    ![Finestra di dialogo Aggiungi ruoli](./media/availability-group-manually-configure-prerequisites-tutorial-/23-addroles.png)

7. Selezionare **Avanti** fino a raggiungere la sezione **conferma** . Selezionare la casella di controllo **Riavvia automaticamente il server di destinazione se necessario**.
8. Selezionare **Installa**.
9. Dopo l'installazione delle funzionalità, tornare al dashboard **Server Manager** .
10. Selezionare la nuova opzione **Servizi di dominio di Active Directory** nel riquadro di sinistra.
11. Selezionare il collegamento **altro** sulla barra di avviso gialla.

    ![Finestra di dialogo Active Directory Domain Services nella macchina virtuale di Server DNS](./media/availability-group-manually-configure-prerequisites-tutorial-/24-addsmore.png)
    
12. Nella colonna **azione** della finestra di dialogo **Dettagli attività tutti i server** Selezionare **innalza di livello il server a controller di dominio**.
13. Nella **Configurazione guidata Servizi di dominio di Active Directory**, usare i seguenti valori:

    | **Page** | Impostazione |
    | --- | --- |
    | **Configurazione distribuzione** |**Aggiungi una nuova foresta**<br/> **Nome di dominio radice** = corp.contoso.com |
    | **Opzioni controller di dominio** |**Password DSRM** = Contoso!0000<br/>**Conferma password** = Contoso!0000 |

14. Selezionare **Avanti** per esaminare le altre pagine della procedura guidata. Nella pagina **Controllo dei prerequisiti** verificare che venga visualizzato il messaggio seguente: **Tutti i controlli dei prerequisiti sono riusciti**. È possibile leggere tutti i messaggi di avviso applicabili o continuare con l'installazione.
15. Selezionare **Installa**. La macchina virtuale **ad-primary-dc** viene riavviata automaticamente.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Annotare l'indirizzo IP del controller di dominio primario

Usare il controller di dominio primario per DNS. Annotare l'indirizzo IP del controller di dominio primario.

Un modo per ottenere l'indirizzo IP del controller di dominio primario è tramite il portale di Azure.

1. Nel portale di Azure aprire il gruppo di risorse.

2. Selezionare il controller di dominio primario.

3. Nel controller di dominio primario selezionare **interfacce di rete**.

![Interfacce di rete](./media/availability-group-manually-configure-prerequisites-tutorial-/25-primarydcip.png)

Annotare l'indirizzo IP privato per questo server.

### <a name="configure-the-virtual-network-dns"></a>Configurare il DNS della rete virtuale

Dopo aver creato il primo controller di dominio e attivato il DNS sul primo server, configurare la rete virtuale per usare questo server come DNS.

1. Nella portale di Azure selezionare nella rete virtuale.

2. In **Impostazioni**selezionare **server DNS**.

3. Selezionare **personalizzato**e digitare l'indirizzo IP privato del controller di dominio primario.

4. Selezionare **Salva**.

### <a name="configure-the-second-domain-controller"></a>Configurare il secondo controller di dominio

Dopo il riavvio del controller di dominio primario, è possibile configurare il secondo controller di dominio. Questo passaggio facoltativo serve a garantire una disponibilità elevata. Seguire questi passaggi per configurare il secondo controller di dominio:

1. Nel portale aprire il gruppo di risorse **SQL-HA-RG** e selezionare la macchina virtuale **ad-secondary-dc**. In **ad-Secondary-DC**selezionare **Connetti** per aprire un file RDP per l'accesso desktop remoto.
2. Accedere alla VM usando l'account dell'amministratore (**BUILTIN\DomainAdmin**) e la password (**Contoso!0000**) configurati.
3. Sostituire l'indirizzo del server DNS preferito con l'indirizzo del controller di dominio.
4. In **Centro rete e condivisione**selezionare l'interfaccia di rete.

   ![Interfaccia di rete](./media/availability-group-manually-configure-prerequisites-tutorial-/26-networkinterface.png)

5. Selezionare **Proprietà**.
6. Selezionare **protocollo Internet versione 4 (TCP/IPv4)** e quindi selezionare **Proprietà**.
7. Selezionare **Usa i seguenti indirizzi server DNS** e quindi specificare l'indirizzo del controller di dominio primario nel **server DNS preferito**.
8. Fare clic su **OK**e quindi su **Chiudi** per eseguire il commit delle modifiche. A questo punto è possibile aggiungere la macchina virtuale a **corp.contoso.com**.

   >[!IMPORTANT]
   >Se si perde la connessione al desktop remoto dopo avere modificato l'impostazione DNS, passare al portale di Azure e riavviare la macchina virtuale.

9. Dal desktop remoto al controller di dominio secondario aprire il **dashboard di Server Manager**.
10. Selezionare il collegamento **Aggiungi ruoli e funzionalità** nel dashboard.

    ![Server Manager - Aggiungere ruoli](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)
11. Selezionare **Avanti** fino a visualizzare la sezione **Ruoli server**.
12. Selezionare i ruoli **Active Directory Domain Services** e **Server DNS**. Quando richiesto, aggiungere eventuali funzionalità aggiuntive necessarie per questi ruoli.
13. Dopo l'installazione delle funzionalità, tornare al dashboard **Server Manager** .
14. Selezionare la nuova opzione **Servizi di dominio di Active Directory** nel riquadro di sinistra.
15. Selezionare il collegamento **altro** sulla barra di avviso gialla.
16. Nella colonna **azione** della finestra di dialogo **Dettagli attività tutti i server** Selezionare **innalza di livello il server a controller di dominio**.
17. In **Configurazione distribuzione** selezionare **Aggiungi un controller di dominio a un dominio esistente**.

    ![Configurazione della distribuzione](./media/availability-group-manually-configure-prerequisites-tutorial-/28-deploymentconfig.png)

18. Fare clic su **Seleziona**.
19. Connettersi usando l'account dell'amministratore (**CORP.CONTOSO.COM\domainadmin**) e la password (**Contoso!0000**).
20. In **selezionare un dominio dalla foresta**scegliere il dominio e quindi fare clic su **OK**.
21. In **Opzioni controller di dominio** usare i valori predefiniti e impostare una password DSRM.

    >[!NOTE]
    >Nella pagina **Opzioni DNS** è possibile che venga visualizzato un avviso indicante che non è possibile creare una delega per questo server DNS. È possibile ignorare questo avviso in ambienti non di produzione.
    >

22. Selezionare **Avanti** finché la finestra di dialogo non raggiunge il controllo dei **prerequisiti** . Quindi selezionare **Installa**.

Quando il server completa le modifiche alla configurazione, riavviare il server.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Aggiungere l'indirizzo IP privato al secondo controller di dominio al server DNS VPN

Nel portale di Azure, nella rete virtuale modificare il server DNS in modo da includere l'indirizzo IP del controller di dominio secondario. Questa impostazione consente la ridondanza del servizio DNS.

### <a name="configure-the-domain-accounts"></a><a name="DomainAccounts"></a> Configurare gli account di dominio

Nei passaggi successivi vengono configurati gli account Active Directory (AD). La tabella seguente illustra gli account:

| |Account di installazione<br/> |sqlserver-0 <br/>Account del servizio SQL Agent e SQL Server |sqlserver-1<br/>Account del servizio SQL Agent e SQL Server
| --- | --- | --- | ---
|**Nome** |Installazione |SQLSvc1 | SQLSvc2
|**Utente SamAccountName** |Installazione |SQLSvc1 | SQLSvc2

Usare i passaggi seguenti per creare ogni account.

1. Accedere alla macchina virtuale **ad-primary-dc**.
2. In **Server Manager**selezionare **strumenti**, quindi **centro di amministrazione di Active Directory**.   
3. Selezionare **corp (local)** nel riquadro di sinistra.
4. Nel riquadro **attività** a destra selezionare **nuovo**e quindi selezionare **utente**.

   ![Centro di amministrazione di Active Directory](./media/availability-group-manually-configure-prerequisites-tutorial-/29-addcnewuser.png)

   >[!TIP]
   >Impostare una password complessa per ogni account.<br/> Per gli ambienti non di produzione impostare l'account utente senza scadenza.
   >

5. Selezionare **OK** per creare l'utente.
6. Ripetere i passaggi precedenti per ognuno dei tre account.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Concedere le autorizzazioni necessarie all'account di installazione

1. Nel **Centro di amministrazione di Active Directory** selezionare **corp (local)** nel riquadro di sinistra. Nel riquadro **attività** a destra selezionare **Proprietà**.

    ![Proprietà utente CORP](./media/availability-group-manually-configure-prerequisites-tutorial-/31-addcproperties.png)

2. Selezionare **estensioni**, quindi fare clic sul pulsante **Avanzate** nella scheda **sicurezza** .
3. Nella finestra di dialogo **impostazioni di sicurezza avanzate per Corp** selezionare **Aggiungi**.
4. Fare clic su **selezionare un'entità**, cercare **CORP\Install**, quindi selezionare **OK**.
5. Selezionare la casella di controllo **Leggi tutte le proprietà**.

6. Selezionare la casella di controllo **Create Computer objects** (Crea oggetti computer).

     ![Autorizzazioni utente Corp](./media/availability-group-manually-configure-prerequisites-tutorial-/33-addpermissions.png)

7. Selezionare **OK** e quindi di nuovo **OK**. Chiudere la finestra delle proprietà di **corp**.

Dopo aver completato la configurazione di Active Directory e degli oggetti utente, creare due macchine virtuali di SQL Server e una macchina virtuale server di controllo. Aggiungere quindi tutte e tre le macchine virtuali al dominio.

## <a name="create-sql-server-vms"></a>Creare le macchine virtuali di SQL Server

Creare altre tre macchine virtuali. La soluzione richiede due macchine virtuali con istanze di SQL Server. Una terza macchina virtuale fungerà da controllo. Windows Server 2016 può usare un [cloud](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)di controllo. Tuttavia, per coerenza con i sistemi operativi precedenti, in questo articolo viene usata una macchina virtuale per un server di controllo del mirroring.  

Prima di procedere, considerare le seguenti decisioni di progettazione.

* **Archiviazione - Azure Managed Disks**

   Per l'archiviazione delle macchine virtuali, usare Azure Managed Disks. Microsoft consiglia Managed Disks per le macchine virtuali di SQL Server. Managed Disks gestisce automaticamente le risorse di archiviazione. Inoltre, quando le macchine virtuali con Managed Disks sono nello stesso set di disponibilità, Azure distribuisce le risorse di archiviazione in modo da garantire la ridondanza appropriata. Per altre informazioni, vedere [Panoramica di Azure Managed Disks](../../../virtual-machines/managed-disks-overview.md). Per informazioni dettagliate sui dischi gestiti in un set di disponibilità, vedere [Usare Managed Disks per le macchine virtuali nel set di disponibilità](../../../virtual-machines/linux/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Rete - Indirizzi IP privati nell'ambiente di produzione**

   Per le macchine virtuali, in questa esercitazione si usano indirizzi IP pubblici. Un indirizzo IP pubblico consente la connessione remota direttamente alla macchina virtuale tramite Internet e semplifica le operazioni di configurazione. Negli ambienti di produzione Microsoft consiglia di usare solo indirizzi IP privati al fine di ridurre la superficie di vulnerabilità della risorsa della VM dell'istanza di SQL Server.

### <a name="create-and-configure-the-sql-server-vms"></a>Creare e configurare le VM di SQL Server

Successivamente, creare tre macchine virtuali, due SQL Server VM e una VM per un nodo del cluster aggiuntivo. Per creare ogni VM, tornare al gruppo di risorse **SQL-ha-RG** , quindi selezionare **Aggiungi**. Cercare l'elemento della raccolta appropriato, selezionare **macchina virtuale**e quindi selezionare **da raccolta**. Usare le informazioni nella tabella seguente per facilitare la creazione di VM:


| Pagina | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Selezionare l'elemento della raccolta appropriato |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise on Windows Server 2016** |**SQL Server 2016 SP1 Enterprise on Windows Server 2016** |
| **Elementi di base** |**Nome** = cluster-fsw<br/>**Nome utente** = DomainAdmin<br/>**Password** = Contoso!0000<br/>**Sottoscrizione** = sottoscrizione<br/>**Gruppo di risorse** = SQL-HA-RG<br/>**Località** = località di Azure |**Nome** = sqlserver-0<br/>**Nome utente** = DomainAdmin<br/>**Password** = Contoso!0000<br/>**Sottoscrizione** = sottoscrizione<br/>**Gruppo di risorse** = SQL-HA-RG<br/>**Località** = località di Azure |**Nome** = sqlserver-1<br/>**Nome utente** = DomainAdmin<br/>**Password** = Contoso!0000<br/>**Sottoscrizione** = sottoscrizione<br/>**Gruppo di risorse** = SQL-HA-RG<br/>**Località** = località di Azure |
| Configurazione della macchina virtuale - **Dimensioni** |**DIMENSIONI** = DS1\_V2 (1 vCPU, 3,5 GB) |**DIMENSIONI** = DS2\_V2 (2 vCPU, 7 GB)</br>Le dimensioni devono supportare l'archiviazione su unità SSD (Supporto disco Premium )) |**DIMENSIONI** = DS2\_V2 (2 vCPU, 7 GB) |
| Configurazione della macchina virtuale - **Impostazioni** |**Storage** (Archiviazione): Usare dischi gestiti.<br/>**Rete virtuale** = autoHAVNET<br/>**Subnet** = sqlsubnet(10.1.1.0/24)<br/>**Indirizzo IP pubblico** generato automaticamente.<br/>**Gruppo di sicurezza di rete** = nessuno<br/>**Monitoraggio e diagnostica** = abilitato<br/>**Account di archiviazione di diagnostica**: usare un account di archiviazione generato automaticamente<br/>**Set di disponibilità** = sqlAvailabilitySet<br/> |**Storage** (Archiviazione): Usare dischi gestiti.<br/>**Rete virtuale** = autoHAVNET<br/>**Subnet** = sqlsubnet(10.1.1.0/24)<br/>**Indirizzo IP pubblico** generato automaticamente.<br/>**Gruppo di sicurezza di rete** = nessuno<br/>**Monitoraggio e diagnostica** = abilitato<br/>**Account di archiviazione di diagnostica**: usare un account di archiviazione generato automaticamente<br/>**Set di disponibilità** = sqlAvailabilitySet<br/> |**Storage** (Archiviazione): Usare dischi gestiti.<br/>**Rete virtuale** = autoHAVNET<br/>**Subnet** = sqlsubnet(10.1.1.0/24)<br/>**Indirizzo IP pubblico** generato automaticamente.<br/>**Gruppo di sicurezza di rete** = nessuno<br/>**Monitoraggio e diagnostica** = abilitato<br/>**Account di archiviazione di diagnostica**: usare un account di archiviazione generato automaticamente<br/>**Set di disponibilità** = sqlAvailabilitySet<br/> |
| Configurazione della macchina virtuale - **Impostazioni SQL Server** |Non applicabile |**Connettività SQL** = privata (nella rete virtuale)<br/>**Porta** = 1433<br/>**Autenticazione SQL** = disabilitata<br/>**Configurazione dell'archiviazione** = generale<br/>**Applicazione automatica delle patch** = domenica alle 2:00<br/>**Backup automatizzato** = disabilitato</br>**Integrazione dell'insieme di credenziali delle chiavi di Azure** = Disabilitata |**Connettività SQL** = privata (nella rete virtuale)<br/>**Porta** = 1433<br/>**Autenticazione SQL** = disabilitata<br/>**Configurazione dell'archiviazione** = generale<br/>**Applicazione automatica delle patch** = domenica alle 2:00<br/>**Backup automatizzato** = disabilitato</br>**Integrazione dell'insieme di credenziali delle chiavi di Azure** = Disabilitata |

<br/>

> [!NOTE]
> Le dimensioni dei computer suggerite qui sono destinate al test dei gruppi di disponibilità nelle macchine virtuali di Azure. Per prestazioni ottimali sui carichi di lavoro di produzione, vedere le indicazioni per SQL Server dimensioni e la configurazione dei computer in procedure consigliate per le [prestazioni per SQL Server in macchine virtuali di Azure](performance-guidelines-best-practices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>

Dopo aver effettuato il provisioning completo delle tre macchine virtuali, sarà necessario aggiungerle al dominio **corp.contoso.com** e concedere alle macchine i diritti amministrativi di CORP\Install.

### <a name="join-the-servers-to-the-domain"></a><a name="joinDomain"></a>Aggiungere i server al dominio

A questo punto è possibile aggiungere la macchina virtuale a **corp.contoso.com**. Eseguire questa procedura per le macchine virtuali di SQL Server e per il server di controllo della condivisione file:

1. Connettersi in remoto alla macchina virtuale con **BUILTIN\DomainAdmin**.
2. In **Server Manager** selezionare **Server locale**.
3. Selezionare il collegamento del **gruppo** di lavoro.
4. Nella sezione **nome computer** selezionare **Cambia**.
5. Selezionare la casella di controllo **Dominio** e digitare **corp.contoso.com** nella casella di testo. Selezionare **OK**.
6. Nella finestra di dialogo popup **Sicurezza di Windows** specificare le credenziali per l'account amministratore di dominio predefinito (**CORP\DomainAdmin**) e la password (**Contoso!0000**).
7. Quando viene visualizzato il messaggio "Benvenuto nel dominio corp.contoso.com", fare clic su **OK**.
8. Selezionare **Chiudi**, quindi fare clic su **Riavvia ora** nella finestra di dialogo popup.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Aggiungere l'utente Corp\Install come amministratore in ogni VM del cluster

Dopo l'avvio di ogni macchina virtuale come membro del dominio, aggiungere **CORP\Install** come membro del gruppo di amministratori locale.

1. Attendere il riavvio della VM, quindi avviare di nuovo il file RDP dal controller di dominio primario per accedere a **sqlserver-0** usando l'account **CORP\DomainAdmin**.

   >[!TIP]
   >Assicurarsi di accedere con l'account di amministratore di dominio. Nei passaggi precedenti è stato usato l'account amministratore BUILTIN. Ora che il server appartiene al dominio, usare l'account di dominio. Nella sessione RDP specificare *DOMINIO*\\*NOME UTENTE*.
   >

2. In **Server Manager**selezionare **strumenti**e quindi **Gestione computer**.
3. Nella finestra **Gestione computer** espandere **Utenti e gruppi locali**, quindi selezionare **Gruppi**.
4. Fare doppio clic sul gruppo **Administrators** .
5. Nella finestra di dialogo **Proprietà amministratori** selezionare il pulsante **Aggiungi** .
6. Immettere l'utente **CORP\Install**, quindi fare clic su **OK**.
7. Selezionare **OK** per chiudere la finestra di dialogo **Proprietà amministratore** .
8. Ripetere i passaggi precedenti in **sqlserver-1** e **cluster-fsw**.

### <a name="set-the-sql-server-service-accounts"></a><a name="setServiceAccount"></a>Impostare gli account del servizio SQL Server

Impostare l'account del servizio SQL Server in ogni macchina virtuale di SQL Server. Usare gli account creati in fase di configurazione degli account di dominio.

1. Aprire **Gestione configurazione SQL Server**.
2. Fare clic con il pulsante destro del mouse sul servizio SQL Server, quindi scegliere **Proprietà**.
3. Impostare l'account e la password.
4. Ripetere questi passaggi per l'altra VM di SQL Server.  

Per i gruppi di disponibilità di SQL Server, ogni VM di SQL Server deve essere eseguita come account di dominio.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Creare un accesso in ogni macchina virtuale di SQL Server per l'account di installazione

Usare l'account di installazione (CORP\install) per configurare il gruppo di disponibilità. Questo account deve essere membro del ruolo del server predefinito **sysadmin** in ogni VM di SQL Server. La procedura seguente crea un account di accesso per l'account di installazione:

1. Connettersi al server tramite Remote Desktop Protocol (RDP) usando l'account *\<MachineName\>\DomainAdmin*.

1. Aprire SQL Server Management Studio e connettersi all'istanza locale di SQL Server.

1. In **Esplora oggetti**selezionare **sicurezza**.

1. Fare clic con il pulsante destro del mouse su **Account di accesso**. Selezionare **nuovo account di accesso**.

1. In **Account di accesso - Nuovo** selezionare **Cerca**.

1. Selezionare **percorsi**.

1. Immettere le credenziali di rete dell'amministratore di dominio.

1. Usare l'account di installazione.

1. Impostare l'account di accesso come membro del ruolo del server predefinito **sysadmin**.

1. Selezionare **OK**.

Ripetere i passaggi precedenti nell'altra VM di SQL Server.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Aggiungere le funzionalità del cluster di failover a entrambe le VM di SQL Server

Per aggiungere le funzionalità del cluster di failover, seguire questa procedura in entrambe le VM di SQL Server:

1. Connettersi alla macchina virtuale di SQL Server tramite Remote Desktop Protocol (RDP) usando l'account *CORP\install*. Aprire **Server Manager > Dashboard**.
2. Selezionare il collegamento **Aggiungi ruoli e funzionalità** nel dashboard.

    ![Server Manager - Aggiungere ruoli](./media/availability-group-manually-configure-prerequisites-tutorial-/22-addfeatures.png)

3. Selezionare **Avanti** fino a visualizzare la sezione **Funzionalità server**.
4. In **Funzionalità** selezionare **Clustering di failover**.
5. Aggiungere le altre funzionalità necessarie.
6. Selezionare **Installa** per aggiungere le funzionalità.

Ripetere i passaggi nell'altra VM di SQL Server.

  >[!NOTE]
  > È ora possibile automatizzare questo passaggio, insieme all'unione di macchine virtuali di SQL Server al cluster di failover, con l'[interfaccia della riga di comando di Azure della VM di SQL](availability-group-az-cli-configure.md) e i [modelli di avvio rapido di Azure](availability-group-quickstart-template-configure.md).
  >


## <a name="configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"></a> Configurare il firewall in ogni macchina virtuale di SQL Server

La soluzione richiede che le seguenti porte TCP siano aperte nel firewall:

- **SQL Server VM**: porta 1433 per un'istanza predefinita di SQL Server.
- **Probe** del servizio di bilanciamento del carico di Azure: Qualsiasi porta disponibile. Negli esempi si usa spesso 59999.
- **Endpoint del mirroring del database:** Qualsiasi porta disponibile. Negli esempi si usa spesso 5022.

Le porte del firewall devono essere aperte in entrambe le VM di SQL Server.

Il metodo per aprire le porte dipende dalla soluzione firewall in uso. La sezione successiva illustra come aprire le porte in Windows Firewall. Aprire le porte necessarie in ogni VM di SQL Server.

### <a name="open-a-tcp-port-in-the-firewall"></a>Aprire una porta TCP nel firewall

1. Nella schermata **Start** avviare **Windows Firewall con sicurezza avanzata** nella prima istanza di SQL Server.
2. Nel riquadro sinistro selezionare **Regole connessioni in entrata**. Nel riquadro destro selezionare **nuova regola**.
3. Per **Tipo di regola** scegliere **Porta**.
4. Per la porta specificare **TCP** e digitare i numeri di porta appropriati. Vedere l'esempio seguente:

   ![Firewall SQL](./media/availability-group-manually-configure-prerequisites-tutorial-/35-tcpports.png)

5. Selezionare **Avanti**.
6. Nella pagina **azione** lasciare selezionata **l'opzione Consenti la connessione** e quindi fare clic su **Avanti**.
7. Nella pagina **profilo** accettare le impostazioni predefinite e quindi fare clic su **Avanti**.
8. Nella pagina **nome** specificare un nome di regola, ad esempio **Probe lb di Azure**, nella casella di testo **nome** e quindi fare clic su **fine**.

Ripetere questi passaggi per la seconda VM di SQL Server.

## <a name="configure-system-account-permissions"></a>Configurare le autorizzazioni dell'account di sistema

Per creare un account per l'account di sistema e concedere le autorizzazioni appropriate, completare questa procedura in ogni istanza di SQL Server:

1. Creare un account per `[NT AUTHORITY\SYSTEM]` in ogni istanza di SQL Server. Lo script seguente crea questo account:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Concedere le autorizzazioni seguenti a `[NT AUTHORITY\SYSTEM]` in ogni istanza di SQL Server:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   Lo script seguente concede queste autorizzazioni:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

## <a name="next-steps"></a>Passaggi successivi

* [Creare un gruppo di disponibilità SQL Server Always On in macchine virtuali di Azure](availability-group-manually-configure-tutorial.md)
