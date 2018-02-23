---
title: "Gruppi di disponibilità di SQL Server - Macchine virtuali di Azure - Prerequisiti | Microsoft Docs"
description: "Questa esercitazione illustra come configurare i prerequisiti per la creazione di un gruppo di disponibilità AlwaysOn di SQL Server nelle VM di Azure."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/09/2017
ms.author: mikeray
ms.openlocfilehash: 85ad53f0b7b4b14784bb0755ee22763d124e63ba
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="complete-the-prerequisites-for-creating-always-on-availability-groups-on-azure-virtual-machines"></a>Completare i prerequisiti per la creazione di gruppi di disponibilità AlwaysOn in Macchine virtuali di Azure

Questa esercitazione illustra come completare i prerequisiti per la creazione di un [gruppo di disponibilità AlwaysOn di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md). Dopo il completamento dei prerequisiti, sono presenti un controller di dominio, due VM di SQL Server e un server di controllo in un unico gruppo di risorse.

**Tempo stimato**: il completamento dei prerequisiti potrebbe richiedere due ore circa. Il tempo è dedicato principalmente alla creazione delle macchine virtuali.

Il diagramma seguente illustra le operazioni di compilazione nell'esercitazione.

![Gruppo di disponibilità](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Esaminare la documentazione relativa ai gruppi di disponibilità

L'esercitazione presuppone una conoscenza di base dei gruppi di disponibilità AlwaysOn di SQL Server. Se non si ha familiarità con questa tecnologia, vedere [Panoramica di gruppi di disponibilità AlwaysOn (SQL Server)](http://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Creare un account Azure
È necessario un account Azure. È possibile [aprire un account Azure gratuito](/pricing/free-trial/?WT.mc_id=A261C142F) o [attivare i benefici della sottoscrizione di Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
1. Accedere al [portale di Azure](http://portal.azure.com).
2. Fare clic su **+** per creare un nuovo oggetto nel portale.

   ![Nuovo oggetto](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. Digitare **gruppo di risorse** nella finestra di ricerca del **Marketplace**.

   ![Gruppo di risorse](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. Fare clic su **Gruppo di risorse**.
5. Fare clic su **Crea**.
6. Nel pannello **Gruppo di risorse** digitare un nome in **Nome gruppo di risorse**, ad esempio digitare **sql-ha-rg**.
7. Se si hanno più sottoscrizioni di Azure, verificare che la sottoscrizione sia la sottoscrizione di Azure in cui si vuole creare il gruppo di disponibilità.
8. Selezionare una località. La località corrisponde all'area di Azure in cui si vuole creare il gruppo di disponibilità. Per questa esercitazione tutte le risorse verranno compilate in un'unica località di Azure.
9. Verificare che l'opzione **Aggiungi al dashboard** sia selezionata. Questa impostazione facoltativa inserisce un collegamento per il gruppo di risorse nel dashboard del portale di Azure.

   ![Gruppo di risorse](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Fare clic su **Crea** per creare il gruppo di risorse.

Azure crea il gruppo di risorse e aggiunge un collegamento a quest'ultimo nel portale.

## <a name="create-the-network-and-subnets"></a>Creare la rete e le subnet
Il passaggio successivo prevede la creazione di reti e di subnet nel gruppo di risorse di Azure.

La soluzione usa una rete virtuale con due subnet. Per altre informazioni sulle reti in Azure, vedere [Panoramica di Rete virtuale](../../../virtual-network/virtual-networks-overview.md).

Per creare la rete virtuale:

1. Nel portale di Azure fare clic su **+ Aggiungi** nel gruppo di risorse specifico. Azure apre il pannello **Tutto** .

   ![Nuovo elemento](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Cercare **rete virtuale**.

     ![Cercare la rete virtuale](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Fare clic su **Rete virtuale**.
4. Nel pannello **Rete virtuale** fare clic sul modello di distribuzione **Resource Manager** e quindi su **Crea**.

    La tabella seguente descrive le impostazioni per la rete virtuale:

   | **Campo** | Valore |
   | --- | --- |
   | **Nome** |autoHAVNET |
   | **Spazio degli indirizzi** |10.33.0.0/24 |
   | **Nome della subnet** |Admin |
   | **Intervallo di indirizzi subnet** |10.33.0.0/29 |
   | **Sottoscrizione** |Specificare la sottoscrizione da usare. Se è disponibile una sola sottoscrizione, il valore di **Sottoscrizione** non è impostato. |
   | **Gruppo di risorse** |Scegliere **Utilizza esistente** e selezionare il nome del gruppo di risorse. |
   | **Posizione** |Specificare la località di Azure. |

   Lo spazio indirizzi e l'intervallo di indirizzi subnet possono essere diversi da quelli della tabella. A seconda della sottoscrizione, sul portale verrà suggerito uno spazio indirizzi disponibile e un intervallo di indirizzi subnet corrispondente. Se non è disponibile uno spazio indirizzi sufficiente, usare un'altra sottoscrizione.

   L'esempio usa il nome di subnet **Admin**. Questa subnet verrà usata per i controller di dominio.

5. Fare clic su **Crea**.

   ![Configurare la rete virtuale](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

In questo modo, si torna al dashboard del portale e Azure invia una notifica dopo la creazione della rete.

### <a name="create-a-second-subnet"></a>Creare una seconda subnet
La nuova rete virtuale dispone di una subnet, denominata **Admin**. I controller di dominio usano questa subnet. Le VM di SQL Server usano una seconda subnet denominata **SQL**. Per configurare questa subnet:

1. Nel dashboard fare clic sul gruppo di risorse creato, **SQL-HA-RG**. Trovare la rete nel gruppo di risorse in **Risorse**.

    Se **SQL-HA-RG** non è visibile, individuarlo facendo clic su **Gruppi di risorse** e filtrando in base al nome del gruppo di risorse.
2. Scegliere **autoHAVNET** dall'elenco di risorse. Azure apre il pannello di configurazione della rete.
3. Nel pannello della rete virtuale **autoHAVNET**, in **Impostazioni** fare clic su **Subnet**.

    Si noti la subnet già creata.

   ![Configurare la rete virtuale](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Creare una seconda subnet. Fare clic su **+ Subnet**.
6. Nel pannello **Aggiungi subnet** configurare la subnet digitando **sqlsubnet** in **Nome**. Azure specifica automaticamente un **Intervallo di indirizzi**valido. Verificare che questo intervallo di indirizzi includa almeno 10 indirizzi. In un ambiente di produzione potrebbero essere necessari più indirizzi.
7. Fare clic su **OK**.

    ![Configurare la rete virtuale](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

La tabella seguente riepiloga le impostazioni di configurazione della rete:

| **Campo** | Valore |
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

Prima di creare le macchine virtuali, è necessario creare i set di disponibilità. I set di disponibilità riducono il tempo di inattività per gli eventi di manutenzione sia pianificati che non pianificati. Un set di disponibilità di Azure è un gruppo logico di risorse che Azure inserisce in domini di errore e domini di aggiornamento fisici. Un dominio di errore assicura che i membri del set di disponibilità dispongano di risorse di alimentazione e di rete separate. Un dominio di aggiornamento assicura che i membri del set di disponibilità non vengano disattivati contemporaneamente per la manutenzione. Per altre informazioni, vedere [Gestire la disponibilità delle macchine virtuali](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Sono necessari due set di disponibilità, uno per i controller di dominio e il secondo per le VM di SQL Server.

Per creare un set di disponibilità, passare al gruppo di risorse e fare clic su **Aggiungi**. Filtrare i risultati digitando **Set di disponibilità**. Fare clic su **Set di disponibilità** nei risultati e quindi su **Crea**.

Configurare due set di disponibilità in base ai parametri riportati nella tabella seguente:

| **Campo** | Set di disponibilità del controller di dominio | Set di disponibilità di SQL Server |
| --- | --- | --- |
| **Nome** |adavailabilityset |sqlavailabilityset |
| **Gruppo di risorse** |Nome gruppo di risorse |Nome gruppo di risorse |
| **Domini di errore** |3 |3 |
| **Domini di aggiornamento** |5 |3 |

Dopo avere creato i set di disponibilità, tornare al gruppo di risorse nel portale di Azure.

## <a name="create-domain-controllers"></a>Creare controller di dominio
Dopo la creazione della rete, delle subnet, dei set di disponibilità e di un servizio di bilanciamento del carico con connessione Internet, è possibile creare le macchine virtuali per i controller di dominio.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Creare le macchine virtuali per i controller di dominio
Per creare e configurare i controller di dominio, tornare al gruppo di risorse **SQL-HA-RG** .

1. Fare clic su **Aggiungi**. Viene aperto il pannello **Tutto** .
2. Digitare **Windows Server 2016 Datacenter**.
3. Fare clic su **Windows Server 2016 Datacenter**. Nel pannello **Windows Server 2016 Datacenter** verificare che il modello di distribuzione sia **Resource Manager** e quindi fare clic su **Crea**. Azure apre il pannello **Crea macchina virtuale** .

Ripetere i passaggi precedenti per creare due macchine virtuali. Assegnare un nome alle due macchine virtuali:

* ad-primary-dc
* ad-secondary-dc

  > [!NOTE]
  > La macchina virtuale **ad-secondary-dc** per fornire disponibilità elevata per Active Directory Domain Services.
  >
  >

La tabella seguente descrive le impostazioni per queste due macchine:

| **Campo** | Valore |
| --- | --- |
| **Nome** |Primo controller di dominio: *ad-primary-dc*.</br>Secondo controller di dominio: *ad-secondary-dc*. |
| **Tipo di disco VM** |SSD |
| **Nome utente** |DomainAdmin |
| **Password** |Contoso!0000 |
| **Sottoscrizione** |*Sottoscrizione in uso* |
| **Gruppo di risorse** |Nome gruppo di risorse |
| **Posizione** |*Località corrente* |
| **Dimensione** |DS1_V2 |
| **Archiviazione** | **Usa dischi gestiti** - **Sì** |
| **Rete virtuale** |autoHAVNET |
| **Subnet** |admin |
| **Indirizzo IP pubblico** |*Lo stesso nome della VM* |
| **Gruppo di sicurezza di rete** |*Lo stesso nome della VM* |
| **Set di disponibilità** |adavailabilityset </br>**Domini di errore**:2</br>**Domini di aggiornamento**:2|
| **Diagnostica** |Attivato |
| **Account di archiviazione di diagnostica** |*Creato automaticamente* |

   >[!IMPORTANT]
   >È possibile inserire una VM in un set di disponibilità solo in fase di creazione. Non è possibile modificare il set di disponibilità dopo che la VM è stata creata. Vedere [Gestire la disponibilità delle macchine virtuali](../manage-availability.md).

Azure crea le macchine virtuali.

Dopo avere creato le macchine virtuali, configurare il controller di dominio.

### <a name="configure-the-domain-controller"></a>Configurare il controller di dominio
Nei passaggi seguenti configurare la macchina virtuale **ad-primary-dc** come controller di dominio per corp.contoso.com.

1. Nel portale aprire il gruppo di risorse **SQL-HA-RG** e selezionare la macchina virtuale **ad-primary-dc**. Nel pannello **ad-primary-dc** fare clic su **Connetti** per aprire un file RDP per l'accesso desktop remoto.

    ![Connettersi a una macchina virtuale](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Accedere con l'account amministratore (**\DomainAdmin**) e la password (**Contoso!0000**) configurati.
3. Per impostazione predefinita, verrà visualizzato il dashboard **Server Manager** .
4. Fare clic sul collegamento **Aggiungi ruoli e funzionalità** sul dashboard.

    ![Server Manager - Aggiungere ruoli](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Selezionare **Avanti** fino a visualizzare la sezione **Ruoli server**.
6. Selezionare i ruoli **Active Directory Domain Services** e **Server DNS**. Quando richiesto, aggiungere eventuali funzionalità aggiuntive necessarie per questi ruoli.

   > [!NOTE]
   > Windows visualizza un avviso per indicare che non è presente alcun indirizzo IP statico. Se si sta eseguendo il test della configurazione, fare clic su **Continua**. Per gli scenari di produzione, impostare l'indirizzo IP come statico nel portale di Azure o [usare PowerShell per impostare l'indirizzo IP statico della macchina virtuale del controller di dominio](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >
   >

    ![Finestra di dialogo Aggiungi ruoli](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Fare clic su **Avanti** fino a raggiungere la sezione **Conferma**. Selezionare la casella di controllo **Riavvia automaticamente il server di destinazione se necessario**.
8. Fare clic su **Installa**.
9. Dopo l'installazione delle funzionalità, tornare al dashboard **Server Manager** .
10. Selezionare la nuova opzione **Servizi di dominio di Active Directory** nel riquadro di sinistra.
11. Fare clic sul collegamento **Altro** sulla barra di avviso gialla.

    ![Finestra di dialogo Active Directory Domain Services nella macchina virtuale di Server DNS](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. Nella colonna **Azione** della finestra di dialogo **Dettagli attività tutti i server** fare clic su **Alza di livello il server a controller di dominio**.
13. Nella **Configurazione guidata Servizi di dominio di Active Directory**, usare i seguenti valori:

    | **Page** | Impostazione |
    | --- | --- |
    | **Configurazione distribuzione** |**Aggiungi una nuova foresta**<br/> **Nome di dominio radice** = corp.contoso.com |
    | **Opzioni controller di dominio** |**Password DSRM** = Contoso!0000<br/>**Conferma password** = Contoso!0000 |
14. Fare clic su **Avanti** per procedere nelle altre pagine della procedura guidata. Nella pagina **Controllo dei prerequisiti** verificare che venga visualizzato il seguente messaggio: **Tutti i controlli dei prerequisiti sono riusciti**. È possibile leggere tutti i messaggi di avviso applicabili o continuare con l'installazione.
15. Fare clic su **Installa**. La macchina virtuale **ad-primary-dc** viene riavviata automaticamente.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Annotare l'indirizzo IP del controller di dominio primario

Usare il controller di dominio primario per DNS. Annotare l'indirizzo IP del controller di dominio primario.

Un modo per ottenere l'indirizzo IP del controller di dominio primario è tramite il portale di Azure.

1. Nel portale di Azure aprire il gruppo di risorse.

2. Fare clic sul controller di dominio primario.

3. Nel pannello del controller di dominio primario fare clic su **Interfacce di rete**.

![Interfacce di rete](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Annotare l'indirizzo IP privato per questo server.

### <a name="configure-the-virtual-network-dns"></a>Configurare il DNS della rete virtuale
Dopo aver creato il primo controller di dominio e attivato il DNS sul primo server, configurare la rete virtuale per usare questo server come DNS.

1. Nel portale di Azure fare clic sulla rete virtuale.

2. In **Impostazioni** fare clic su **Server DNS**.

3. Fare clic su **Personalizzato** e digitare l'indirizzo IP privato del controller di dominio primario.

4. Fare clic su **Save**.

### <a name="configure-the-second-domain-controller"></a>Configurare il secondo controller di dominio
Dopo il riavvio del controller di dominio primario, è possibile configurare il secondo controller di dominio. Questo passaggio facoltativo serve a garantire una disponibilità elevata. Seguire questi passaggi per configurare il secondo controller di dominio:

1. Nel portale aprire il gruppo di risorse **SQL-HA-RG** e selezionare la macchina virtuale **ad-secondary-dc**. Nel pannello **ad-secondary-dc** fare clic su **Connetti** per aprire un file RDP per l'accesso desktop remoto.
2. Accedere alla VM usando l'account dell'amministratore (**BUILTIN\DomainAdmin**) e la password (**Contoso!0000**) configurati.
3. Sostituire l'indirizzo del server DNS preferito con l'indirizzo del controller di dominio.
4. In **Centro connessioni di rete e condivisione** scegliere l'interfaccia di rete.
   ![Interfaccia di rete](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Fare clic su **Proprietà**.
6. Selezionare **Protocollo Internet versione 4 (TCP/IPv4)** e fare clic su **Proprietà**.
7. Selezionare **Utilizza i seguenti indirizzi server DNS** e specificare l'indirizzo del controller di dominio primario in **Server DNS preferito**.
8. Fare clic su **OK** e quindi su **Chiudi** per eseguire il commit delle modifiche. A questo punto è possibile aggiungere la macchina virtuale a **corp.contoso.com**.

   >[!IMPORTANT]
   >Se si perde la connessione al desktop remoto dopo avere modificato l'impostazione DNS, passare al portale di Azure e riavviare la macchina virtuale.

9. Dal desktop remoto al controller di dominio secondario aprire il **dashboard di Server Manager**.
10. Fare clic sul collegamento **Aggiungi ruoli e funzionalità** sul dashboard.

    ![Server Manager - Aggiungere ruoli](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. Selezionare **Avanti** fino a visualizzare la sezione **Ruoli server**.
12. Selezionare i ruoli **Active Directory Domain Services** e **Server DNS**. Quando richiesto, aggiungere eventuali funzionalità aggiuntive necessarie per questi ruoli.
13. Dopo l'installazione delle funzionalità, tornare al dashboard **Server Manager** .
14. Selezionare la nuova opzione **Servizi di dominio di Active Directory** nel riquadro di sinistra.
15. Fare clic sul collegamento **Altro** sulla barra di avviso gialla.
16. Nella colonna **Azione** della finestra di dialogo **Dettagli attività tutti i server** fare clic su **Alza di livello il server a controller di dominio**.
17. In **Configurazione distribuzione** selezionare **Aggiungi un controller di dominio a un dominio esistente**.
   ![Configurazione distribuzione](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. Fare clic su **Seleziona**.
19. Connettersi usando l'account dell'amministratore (**CORP.CONTOSO.COM\domainadmin**) e la password (**Contoso!0000**).
20. In **Selezionare un dominio dalla foresta** fare clic sul dominio e quindi su **OK**.
21. In **Opzioni controller di dominio** usare i valori predefiniti e impostare una password DSRM.

   >[!NOTE]
   >Nella pagina **Opzioni DNS** è possibile che venga visualizzato un avviso indicante che non è possibile creare una delega per questo server DNS. È possibile ignorare questo avviso in ambienti non di produzione.
22. Fare clic su **Avanti** fino a quando non viene visualizzato il controllo **Prerequisiti**. Fare clic su **Installa**.

Quando il server completa le modifiche alla configurazione, riavviare il server.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Aggiungere l'indirizzo IP privato del secondo controller di dominio al server DNS della VPN

Nel portale di Azure, nella rete virtuale modificare il server DNS in modo da includere l'indirizzo IP del controller di dominio secondario. Ciò consente la ridondanza del servizio DNS.

### <a name=DomainAccounts></a> Configurare gli account di dominio

Nei passaggi successivi vengono configurati gli account Active Directory (AD). La tabella seguente illustra gli account:

| |Account di installazione<br/> |sqlserver-0 <br/>Account del servizio SQL Agent e SQL Server |sqlserver-1<br/>Account del servizio SQL Agent e SQL Server
| --- | --- | --- | ---
|**Nome** |Installa |SQLSvc1 | SQLSvc2
|**Utente SamAccountName** |Installa |SQLSvc1 | SQLSvc2

Usare i passaggi seguenti per creare ogni account.

1. Accedere alla macchina virtuale **ad-primary-dc**.
2. In **Server Manager** selezionare **Strumenti** e quindi fare clic su **Centro di amministrazione di Active Directory**.   
3. Selezionare **corp (local)** nel riquadro di sinistra.
4. Nel riquadro **Attività** a destra selezionare **Nuovo** e quindi fare clic su **Utente**.
   ![Centro di amministrazione di Active Directory](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Impostare una password complessa per ogni account.<br/> Per gli ambienti non di produzione impostare l'account utente senza scadenza.

5. Fare clic su **OK** per creare l'utente.
6. Ripetere i passaggi precedenti per ognuno dei tre account.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Concedere le autorizzazioni necessarie all'account di installazione
1. Nel **Centro di amministrazione di Active Directory** selezionare **corp (local)** nel riquadro di sinistra. Nel riquadro **Attività** a destra fare quindi clic su **Proprietà**.

    ![Proprietà utente CORP](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. Selezionare **Estensioni**, quindi fare clic sul pulsante **Avanzate** nella scheda **Sicurezza**.
3. Nella finestra di dialogo **Impostazioni avanzate di sicurezza per corp** fare clic su **Aggiungi**.
4. Fare clic su **Seleziona un'entità**, cercare **CORP\Install** e quindi fare clic su **OK**.
5. Selezionare la casella di controllo **Leggi tutte le proprietà**.

6. Selezionare la casella di controllo **Create Computer objects** (Crea oggetti computer).

     ![Autorizzazioni utente Corp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. Fare clic su **OK** e quindi di nuovo clic su **OK**. Chiudere la finestra delle proprietà di **corp**.

Dopo aver completato la configurazione di Active Directory e degli oggetti utente, creare due macchine virtuali di SQL Server e una macchina virtuale server di controllo. Aggiungere quindi tutte e tre le macchine virtuali al dominio.

## <a name="create-sql-server-vms"></a>Creare le macchine virtuali di SQL Server

Creare altre tre macchine virtuali. La soluzione richiede due macchine virtuali con istanze di SQL Server. Una terza macchina virtuale fungerà da controllo. Windows Server 2016 può usare un [cloud di controllo](http://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness); tuttavia, per uniformità con sistemi operativi precedenti, in questo documento si usa una macchina virtuale come controllo.  

Prima di procedere, considerare le seguenti decisioni di progettazione.

* **Archiviazione - Azure Managed Disks**

   Per l'archiviazione delle macchine virtuali, usare Azure Managed Disks. Microsoft consiglia Managed Disks per le macchine virtuali di SQL Server. Managed Disks gestisce automaticamente le risorse di archiviazione. Inoltre, quando le macchine virtuali con Managed Disks sono nello stesso set di disponibilità, Azure distribuisce le risorse di archiviazione in modo da garantire la ridondanza appropriata. Per altre informazioni, vedere [Panoramica di Azure Managed Disks](../managed-disks-overview.md). Per informazioni dettagliate sui dischi gestiti in un set di disponibilità, vedere [Usare Managed Disks per le macchine virtuali nel set di disponibilità](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Rete - Indirizzi IP privati nell'ambiente di produzione**

   Per le macchine virtuali, in questa esercitazione si usano indirizzi IP pubblici. In questo modo la connessione remota può avvenire direttamente alla macchina virtuale tramite internet, semplificando la procedura di configurazione. Negli ambienti di produzione Microsoft consiglia di usare solo indirizzi IP privati al fine di ridurre la superficie di vulnerabilità della risorsa della VM dell'istanza di SQL Server.

### <a name="create-and-configure-the-sql-server-vms"></a>Creare e configurare le VM di SQL Server
Creare successivamente tre VM, tra cui due VM di SQL Server e una VM per un nodo del cluster aggiuntivo. Per creare ogni VM, tornare al gruppo di risorse **SQL-HA-RG**, fare clic su **Aggiungi**, cercare l'elemento della raccolta appropriato, fare clic su **Macchina virtuale**, quindi su **Da raccolta**. Usare le informazioni nella tabella seguente per facilitare la creazione di VM:


| Page | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Selezionare l'elemento della raccolta appropriato |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise on Windows Server 2016** |**SQL Server 2016 SP1 Enterprise on Windows Server 2016** |
| **Elementi di base** |**Nome** = cluster-fsw<br/>**Nome utente** = DomainAdmin<br/>**Password** = Contoso!0000<br/>**Sottoscrizione** = sottoscrizione<br/>**Gruppo di risorse** = SQL-HA-RG<br/>**Posizione** = posizione di Azure |**Nome** = sqlserver-0<br/>**Nome utente** = DomainAdmin<br/>**Password** = Contoso!0000<br/>**Sottoscrizione** = sottoscrizione<br/>**Gruppo di risorse** = SQL-HA-RG<br/>**Posizione** = posizione di Azure |**Nome** = sqlserver-1<br/>**Nome utente** = DomainAdmin<br/>**Password** = Contoso!0000<br/>**Sottoscrizione** = sottoscrizione<br/>**Gruppo di risorse** = SQL-HA-RG<br/>**Posizione** = posizione di Azure |
| Configurazione della macchina virtuale - **Dimensioni** |**DIMENSIONI** = DS1\_V2 (1 vCPU, 3,5 GB) |**DIMENSIONI** = DS2\_V2 (2 vCPU, 7 GB)</br>Le dimensioni devono supportare l'archiviazione su unità SSD (Supporto disco Premium )) |**DIMENSIONI** = DS2\_V2 (2 vCPU, 7 GB) |
| Configurazione della macchina virtuale - **Impostazioni** |**Archiviazione**: Usa dischi gestiti.<br/>**Rete virtuale** = autoHAVNET<br/>**Subnet** = sqlsubnet(10.1.1.0/24)<br/>**Indirizzo IP pubblico** generato automaticamente.<br/>**Gruppo di sicurezza di rete** = nessuno<br/>**Monitoraggio e diagnostica** = abilitato<br/>**Account di archiviazione di diagnostica**: usare un account di archiviazione generato automaticamente<br/>**Set di disponibilità** = sqlAvailabilitySet<br/> |**Archiviazione**: Usa dischi gestiti.<br/>**Rete virtuale** = autoHAVNET<br/>**Subnet** = sqlsubnet(10.1.1.0/24)<br/>**Indirizzo IP pubblico** generato automaticamente.<br/>**Gruppo di sicurezza di rete** = nessuno<br/>**Monitoraggio e diagnostica** = abilitato<br/>**Account di archiviazione di diagnostica**: usare un account di archiviazione generato automaticamente<br/>**Set di disponibilità** = sqlAvailabilitySet<br/> |**Archiviazione**: Usa dischi gestiti.<br/>**Rete virtuale** = autoHAVNET<br/>**Subnet** = sqlsubnet(10.1.1.0/24)<br/>**Indirizzo IP pubblico** generato automaticamente.<br/>**Gruppo di sicurezza di rete** = nessuno<br/>**Monitoraggio e diagnostica** = abilitato<br/>**Account di archiviazione di diagnostica**: usare un account di archiviazione generato automaticamente<br/>**Set di disponibilità** = sqlAvailabilitySet<br/> |
| Configurazione della macchina virtuale - **Impostazioni SQL Server** |Non applicabile |**Connettività SQL** = privata (nella rete virtuale)<br/>**Porta** = 1433<br/>**Autenticazione SQL** = disabilitata<br/>**Configurazione dell'archiviazione** = generale<br/>**Applicazione automatica delle patch** = domenica alle 2:00<br/>**Backup automatizzato** = disabilitato</br>**Integrazione dell'insieme di credenziali delle chiavi di Azure** = Disabilitata |**Connettività SQL** = privata (nella rete virtuale)<br/>**Porta** = 1433<br/>**Autenticazione SQL** = disabilitata<br/>**Configurazione dell'archiviazione** = generale<br/>**Applicazione automatica delle patch** = domenica alle 2:00<br/>**Backup automatizzato** = disabilitato</br>**Integrazione dell'insieme di credenziali delle chiavi di Azure** = Disabilitata |

<br/>

> [!NOTE]
> Le dimensioni delle macchine virtuali qui suggerite sono pensate per il test dei gruppi di disponibilità nelle VM di Azure. Per ottenere prestazioni ottimali su carichi di lavoro di produzione, vedere le dimensioni e la configurazione consigliate per la macchina virtuale di SQL Server in [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

Dopo aver effettuato il provisioning completo delle tre macchine virtuali, sarà necessario aggiungerle al dominio **corp.contoso.com** e concedere alle macchine i diritti amministrativi di CORP\Install.

### <a name="joinDomain"></a>Aggiungere i server al dominio

A questo punto è possibile aggiungere la macchina virtuale a **corp.contoso.com**. Eseguire le operazioni seguenti per le macchine virtuali di SQL Server e per il server di controllo della condivisione file:

1. Connettersi in remoto alla macchina virtuale con **BUILTIN\DomainAdmin**.
2. In **Server Manager** fare clic su **Server locale**.
3. Fare clic sul collegamento **GRUPPO DI LAVORO**.
4. Nella sezione **Nome computer** fare clic su **Cambia**.
5. Selezionare la casella di controllo **Dominio** e digitare **corp.contoso.com** nella casella di testo. Fare clic su **OK**.
6. Nella finestra di dialogo popup **Sicurezza di Windows** specificare le credenziali per l'account amministratore di dominio predefinito (**CORP\DomainAdmin**) e la password (**Contoso!0000**).
7. uando viene visualizzato il messaggio di benvenuto nel dominio corp.contoso.com, fare clic su **OK**.
8. Fare clic su **Chiudi** e quindi su **Riavvia ora** nella finestra di dialogo popup.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Aggiungere l'utente Corp\Install come amministratore in ogni VM del cluster

Dopo l'avvio di ogni macchina virtuale come membro del dominio, aggiungere **CORP\Install** come membro del gruppo di amministratori locale.

1. Attendere il riavvio della VM, quindi avviare di nuovo il file RDP dal controller di dominio primario per accedere a **sqlserver-0** usando l'account **CORP\DomainAdmin**.
   >[!TIP]
   >Assicurarsi di accedere con l'account di amministratore di dominio. Nei passaggi precedenti è stato usato l'account amministratore BUILTIN. Ora che il server appartiene al dominio, usare l'account di dominio. Nella sessione RDP specificare *DOMINIO*\\*NOME UTENTE*.

2. In **Server Manager** selezionare **Strumenti**, quindi fare clic su **Gestione computer**.
3. Nella finestra **Gestione computer** espandere **Utenti e gruppi locali**, quindi selezionare **Gruppi**.
4. Fare doppio clic sul gruppo **Administrators** .
5. Nella finestra di dialogo **Proprietà Administrators** fare clic su pulsante **Aggiungi**.
6. Immettere l'utente **CORP\Install** e quindi fare clic su **OK**.
7. Fare clic su **OK** per chiudere la finestra di dialogo **Proprietà Administrators**.
8. Ripetere i passaggi precedenti in **sqlserver-1** e **cluster-fsw**.

### <a name="setServiceAccount"></a>Impostare gli account del servizio SQL Server

Impostare l'account del servizio SQL Server in ogni macchina virtuale di SQL Server. Usare gli account creati in fase di [configurazione degli account di dominio](#DomainAccounts).

1. Aprire **Gestione configurazione SQL Server**.
2. Fare clic con il pulsante destro del mouse sul servizio SQL Server e scegliere **Proprietà**.
3. Impostare l'account e la password.
4. Ripetere questi passaggi per l'altra VM di SQL Server.  

Per i gruppi di disponibilità di SQL Server, ogni VM di SQL Server deve essere eseguita come account di dominio.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Creare un accesso in ogni macchina virtuale di SQL Server per l'account di installazione

Usare l'account di installazione (CORP\install) per configurare il gruppo di disponibilità. Questo account deve essere membro del ruolo del server predefinito **sysadmin** in ogni VM di SQL Server. La procedura seguente crea un account di accesso per l'account di installazione:

1. Connettersi al server tramite Remote Desktop Protocol (RDP) usando l'account *\<MachineName\>\DomainAdmin*.

1. Aprire SQL Server Management Studio e connettersi all'istanza locale di SQL Server.

1. In **Esplora oggetti** fare clic su **Sicurezza**.

1. Fare clic con il pulsante destro del mouse su **Account di accesso**. Fare clic su **Nuovo account di accesso**.

1. In **Account di accesso - Nuovo** fare clic su **Cerca**.

1. Fare clic su **Località**.

1. Immettere le credenziali di rete dell'amministratore di dominio.

1. Usare l'account di installazione.

1. Impostare l'account di accesso come membro del ruolo del server predefinito **sysadmin**.

1. Fare clic su **OK**.

Ripetere i passaggi precedenti nell'altra VM di SQL Server.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Aggiungere le funzionalità del cluster di failover a entrambe le VM di SQL Server

Per aggiungere le funzionalità del cluster di failover, seguire questa procedura in entrambe le VM di SQL Server:

1. Connettersi alla macchina virtuale di SQL Server tramite Remote Desktop Protocol (RDP) usando l'account *CORP\install*. Aprire **Server Manager > Dashboard**.
2. Fare clic sul collegamento **Aggiungi ruoli e funzionalità** sul dashboard.

    ![Server Manager - Aggiungere ruoli](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. Selezionare **Avanti** fino a visualizzare la sezione **Funzionalità server**.
4. In **Funzionalità** selezionare **Clustering di failover**.
5. Aggiungere le altre funzionalità necessarie.
6. Fare clic su **Installa** per aggiungere le funzionalità.

Ripetere i passaggi nell'altra VM di SQL Server.

## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"> Configurare il firewall in ogni macchina virtuale di SQL Server

La soluzione richiede che le seguenti porte TCP siano aperte nel firewall:

- **VM di SQL Server**:<br/>
   Porta 1433 per un'istanza predefinita di SQL Server.
- **Probe di Azure Load Balancer:**<br/>
   Qualsiasi porta disponibile. Negli esempi si usa spesso 59999.
- **Endpoint del mirroring del database:** <br/>
   Qualsiasi porta disponibile. Negli esempi si usa spesso 5022.

Le porte del firewall devono essere aperte in entrambe le VM di SQL Server.

Il metodo per aprire le porte dipende dalla soluzione firewall in uso. La sezione successiva illustra come aprire le porte in Windows Firewall. Aprire le porte necessarie in ogni VM di SQL Server.

### <a name="open-a-tcp-port-in-the-firewall"></a>Aprire una porta TCP nel firewall

1. Nella schermata **Start** avviare **Windows Firewall con sicurezza avanzata** nella prima istanza di SQL Server.
2. Nel riquadro sinistro selezionare **Regole connessioni in entrata**. Nel riquadro di destra fare clic su **Nuova regola**.
3. Per **Tipo di regola** scegliere **Porta**.
4. Per la porta specificare **TCP** e digitare i numeri di porta appropriati. Vedere l'esempio seguente:

   ![Firewall SQL](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. Fare clic su **Avanti**.
6. Nella pagina **Azione** mantenere selezionata l'opzione **Consenti la connessione** e fare clic su **Avanti**.
7. Nella pagina **Profilo** accettare le impostazioni predefinite e fare clic su **Avanti**.
8. Nella pagina **Nome** specificare un nome per la regola, ad esempio **Probe per Azure LB** nella casella di testo **Nome**, quindi fare clic su **Fine**.

Ripetere questi passaggi per la seconda VM di SQL Server.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un gruppo di disponibilità AlwaysOn in Macchine virtuali di Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md)
