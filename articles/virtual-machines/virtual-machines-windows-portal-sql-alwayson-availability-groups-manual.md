<properties
	pageTitle="Configurare manualmente il gruppo di disponibilità AlwaysOn in macchine virtuali di Azure con Resource Manager"
	description="Creare un gruppo di disponibilità AlwaysOn con le macchine virtuali di Azure. Questa esercitazione usa principalmente l'interfaccia utente e gli strumenti invece dello scripting."
	services="virtual-machines"
	documentationCenter="na"
	authors="MikeRayMSFT"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="06/15/2016"
	ms.author="MikeRayMSFT" />

# Configurare manualmente il gruppo di disponibilità AlwaysOn in macchine virtuali di Azure con Resource Manager

> [AZURE.SELECTOR]
- [Resource Manager: automatica](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Resource Manager: manuale](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Classica: interfaccia utente](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Classica: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

Questa esercitazione end-to-end mostra come implementare i gruppi di disponibilità di SQL Server nelle macchine virtuali di Azure Resource Manager.

Al termine dell'esercitazione la soluzione sarà composta dagli elementi seguenti:

- Una rete virtuale contenente due subnet, tra cui una subnet front-end e una back-end

- Due controller di dominio in un set di disponibilità con un dominio di Active Directory (AD)

- Due VM di SQL Server in un set di disponibilità distribuite nella subnet back-end e aggiunte al dominio di AD

- Un cluster WSFC a 3 nodi con il modello di quorum Maggioranza dei nodi

- Un servizio di bilanciamento del carico interno per fornire un indirizzo IP ai gruppi di disponibilità

- Un gruppo di disponibilità con due repliche con commit sincrono di un database di disponibilità

La figura seguente è una rappresentazione grafica della soluzione.

![Architettura per gruppo di disponibilità in Azure Resource Manager](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/00-EndstateSampleNoELB.png)

Si tratta di una possibile configurazione. È possibile, ad esempio, ridurre il numero di macchine virtuali per un gruppo di disponibilità con due repliche per risparmiare ore di calcolo in Azure, usando il controller di dominio come condivisione file di controllo del quorum in un cluster WSFC a 2 nodi. Questo metodo consente di ridurre di un'unità il numero di macchine virtuali rispetto alla configurazione precedente.

>[AZURE.NOTE] Il completamento di questa esercitazione richiede una quantità di tempo significativa. È anche possibile compilare automaticamente l'intera soluzione. Nel portale di Azure è disponibile una configurazione raccolta per i gruppi di disponibilità AlwaysOn con un listener. In questo modo, tutto il necessario per i gruppi di disponibilità viene configurato automaticamente. Per altre informazioni, vedere [Portale - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

[AZURE.INCLUDE [availability-group-template](../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

Nell’esercitazione si presuppongono le condizioni seguenti:

- Si dispone già di un account Azure.

- Si conosce già la modalità di provisioning di una macchina virtuale SQL Server dalla raccolta di macchine virtuali tramite l'interfaccia utente grafica. Per altre informazioni, vedere [Provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-windows-portal-sql-server-provision.md).

- Si ha già una conoscenza approfondita dei gruppi di disponibilità. Per altre informazioni, vedere [Gruppi di disponibilità AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

>[AZURE.NOTE] Se si è interessati all'uso di gruppi di disponibilità con SharePoint, vedere anche [Configurare gruppi di disponibilità AlwaysOn di SQL Server 2012 per SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).

## Creare un gruppo di risorse

1. Accedere al [portale di Azure](http://portal.azure.com). 

1. Fare clic su **+Nuovo** e quindi digitare **Gruppo di risorse** nella finestra di ricerca del **Marketplace**.

    ![Gruppo di risorse](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/01-resourcegroupsymbol.png)

1. Fare clic su **Gruppo di risorse**

    ![Nuovo gruppo di risorse](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/01-newresourcegroup.png)

1. Fare clic su **Crea**.

1. Nel pannello **Gruppo di risorse** digitare **SQL-HA-RG** in **Nome gruppo di risorse**

1. Se si hanno più sottoscrizioni di Azure, verificare che la sottoscrizione sia la sottoscrizione di Azure in cui si vuole creare il gruppo di disponibilità.

1. Selezionare una località. La località è la località di Azure in cui verrà eseguito il gruppo di disponibilità. Per questa esercitazione tutte le risorse verranno compilate in un'unica località di Azure.

1. Verificare che **Aggiungi al dashboard** sia selezionato. Questa impostazione facoltativa inserisce un collegamento per il gruppo di risorse nel dashboard del portale di Azure.

1. Fare clic su **Crea** per creare il gruppo di risorse.

Azure creerà il nuovo gruppo di risorse e aggiungerà un collegamento al gruppo di risorse nel portale.

## Creare reti e subnet

Il passaggio successivo prevede la creazione di reti e di subnet nel gruppo di risorse di Azure.

La soluzione usa una rete virtuale con due subnet. È consigliabile conoscere le nozioni di base sulle reti e il funzionamento delle reti in Azure. Per altre informazioni sulle reti in Azure, vedere [Panoramica di Rete virtuale](../virtual-network/virtual-networks-overview.md).

Per creare la rete virtuale:

1. Nel portale di Azure fare clic sul nuovo gruppo di risorse e su **+** per aggiungere un nuovo elemento al gruppo di risorse. Azure apre il pannello **Tutto**. 

    ![Nuovo elemento](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/02-newiteminrg.png)

1. Cercare **rete virtuale**.

    ![Cercare la rete virtuale](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/04-findvirtualnetwork.png)

1. Fare clic su **Rete virtuale**.

1. Nel pannello **Rete virtuale** fare clic sul modello di distribuzione **Resource Manager** e su **Crea**.

    ![Creare una rete virtuale](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/05-createvirtualnetwork.png)
 

 
1. Configurare la rete virtuale nel pannello **Crea rete virtuale**.

    La tabella seguente descrive le impostazioni per la rete virtuale.

    | **Campo** | Valore |
| ----- | ----- |
| **Nome** | autoHAVNET |
| **Spazio degli indirizzi** | 10\.0.0.0/16 |
| **Nome della subnet** | Subnet-1 |
| **Intervallo di indirizzi subnet** | 10\.0.0.0/24 |
| **Sottoscrizione** | Specificare la sottoscrizione da usare. Se è disponibile solo una sottoscrizione, questa opzione potrebbe essere vuota. |
| **Posizione** | Specificare la località di Azure in cui si distribuirà il gruppo di disponibilità |

    Si noti che lo spazio indirizzi e l'intervallo di indirizzi subnet possono essere diversi da quelli della tabella. A seconda della sottoscrizione Azure specificherà automaticamente uno spazio indirizzi disponibile e un intervallo di indirizzi subnet corrispondente. Se non è disponibile uno spazio indirizzi sufficiente, usare un'altra sottoscrizione.

1. Fare clic su **Crea**

    ![Configurare la rete virtuale](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/06-configurevirtualnetwork.png)

In questo modo si tornerà al dashboard del portale e Azure invierà una notifica una volta creata la rete.

### Creare la seconda subnet

A questo punto la rete virtuale contiene una subnet, denominata Subnet-1. I controller di dominio useranno questa subnet. Le istanze di SQL Server useranno una seconda subnet denominata **Subnet-2**. Per configurare Subnet-2

1. Nel dashboard fare clic sul gruppo di risorse creato, **SQL-HA-RG**. Trovare la rete nel gruppo di risorse in **Risorse**.

  Se **SQL-HA-RG** non è visibile, è possibile trovarlo facendo clic su **Gruppi di risorse** e filtrando in base al nome del gruppo di risorse creato.

1.  Fare clic su **autoHAVNET** nell'elenco di risorse per aprire il pannello di configurazione della rete.

1.  Nel rete virtuale **autoHAVNET** fare clic su **Tutte le impostazioni*.

1. Nel pannello **Impostazioni** fare clic su **Subnet**.

    Si noti la subnet già creata.

    ![Configurare la rete virtuale](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/07-addsubnet.png)

1. Creare una seconda subnet. Fare clic su **+ Subnet**.

    Nel pannello **Aggiungi subnet** configurare la subnet digitando **subnet-2** in **Nome**. Azure specificherà automaticamente un **Intervallo di indirizzi** valido. Verificare che questo intervallo di indirizzi includa almeno 10 indirizzi. In un ambiente di produzione potrebbero essere necessari più indirizzi.

1. Fare clic su **OK**.
 
![Configurare la rete virtuale](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/08-configuresubnet.png)
   
Ecco un riepilogo delle impostazioni di configurazione per la rete virtuale ed entrambe le subnet.

| **Campo** | Valore |
| ----- | ----- |
| **Nome** | **autoHAVNET** |
| **Spazio degli indirizzi** | Dipende dagli spazi indirizzi disponibili nella sottoscrizione. Un valore tipico è 10.0.0.0/16 |
| **Nome della subnet** | **Subnet-1** |
| **Intervallo di indirizzi subnet** | Dipende dagli intervalli di indirizzi disponibili nella sottoscrizione. Un valore tipico è 10.0.0.0/24 |
| **Nome della subnet** | **Subnet-2** |
| **Intervallo di indirizzi subnet** | Dipende dagli intervalli di indirizzi disponibili nella sottoscrizione. Un valore tipico è 10.0.1.0/24 |
| **Sottoscrizione** | Specificare la sottoscrizione da usare. |
| **Gruppo di risorse** | **SQL-HA-RG** |
| **Posizione** | Specificare la stessa località scelta per il gruppo di risorse. |

## Creare set di disponibilità

Prima di creare le macchine virtuali, è necessario creare i set di disponibilità. I set di disponibilità riducono il tempo di inattività per gli eventi di manutenzione pianificati o non pianificati. Un set di disponibilità di Azure è un gruppo logico di risorse che Azure inserisce in domini di errore e domini di aggiornamento fisici. Un dominio di errore assicura che i membri del set di disponibilità abbiano risorse di alimentazione e di rete separate. Un dominio di aggiornamento assicura che i membri del set di disponibilità non vengano disattivati per la manutenzione contemporaneamente. [Gestione della disponibilità delle macchine virtuali](virtual-machines-windows-manage-availability.md).

Saranno necessari due set di disponibilità: uno per i controller di dominio e il secondo per le istanze di SQL Server.

Per creare un set di disponibilità, andare al gruppo di risorse e fare clic su **Aggiungi**. Filtrare i risultati digitando **Set di disponibilità**. Fare clic su **Set di disponibilità** nei risultati. Fare clic su **Create**.

Configurare due set di disponibilità in base ai parametri nella tabella seguente.

| **Campo** | Set di disponibilità controller di dominio | Set di disponibilità SQL Server |
| ----- | ----- | ----- |
| **Nome** | adAvailablitySet | sqlAvailabilitySet|
| **Gruppo di risorse** | SQL-HA-RG | SQL-HA-RG |
| **Domini di errore** | 3 | 3 |
| **Domini di aggiornamento** | 5 | 3 |

Dopo avere creato i set di disponibilità, tornare al gruppo di risorse nel portale di Azure.

## Creare controller di dominio

A questo punto sono stati creati la rete, le subnet, i set di disponibilità e un servizio di bilanciamento del carico con connessione Internet. Ora è possibile creare le macchine virtuali per i controller di dominio.

### Creare le macchine virtuali per i controller di dominio

Per creare e configurare i controller di dominio, tornare al gruppo di risorse **SQL-HA-RG**.

1. Fare clic su Aggiungi. Viene aperto il pannello **Tutto**.

1. Digitare **Windows Server 2012 R2 Datacenter**.

1. Fare clic su **Server 2012 R2 Datacenter**. Nel pannello **Windows Server 2012 R2 Datacenter** verificare che il modello di distribuzione sia impostato su **Resource Manager** e fare clic su **Crea**. Azure apre il pannello **Crea macchina virtuale**.

Sarà necessario eseguire il processo due volte per creare due macchine virtuali. Assegnare un nome alle due macchine virtuali:

- ad-primary-dc
- ad-secondary-dc

 >[AZURE.NOTE] **ad-secondary-dc** è un componente facoltativo per fornire disponibilità elevata per i servizi di dominio di Active Directory.

La tabella seguente descrive le impostazioni per questi due computer.

| **Campo** | Valore 
| ----- | ---- 
| **Nome utente** | DomainAdmin
| **Password** | Contoso!000 |
| **Sottoscrizione** | *sottoscrizione in uso* |
| **Gruppo di risorse** | SQL-HA-RG |
| **Posizione** | *località corrente* 
| **Dimensione** | D1\_V2 (standard)
| **Tipo di archiviazione** | standard
| **Account di archiviazione** | *Creato automaticamente*
| **Rete virtuale** | autoHAVNET
| **Subnet** | subnet-1
| **Indirizzo IP pubblico** | *Lo stesso nome della VM*
| **Gruppo di sicurezza di rete** | *Lo stesso nome della VM*
| **Diagnostica** | Enabled
| **Account di archiviazione di diagnostica** | *Creato automaticamente*
| **Set di disponibilità** | adAvailabilitySet

>[AZURE.NOTE] Non è possibile modificare il set di disponibilità in una VM dopo averlo creato.

Azure creerà le macchine virtuali.

Dopo avere creato le macchine virtuali, configurare il controller di dominio.

### Configurare il controller di dominio

Nei passaggi seguenti configurare la macchina virtuale **ad-primary-dc** come controller di dominio per corp.contoso.com.

1. Nel portale aprire il gruppo di risorse **SQL-HA-RG** e selezionare il computer **ad-primary-dc**. Nel pannello **ad-primary-dc** fare clic su **Connetti** per aprire un file RDP per l'accesso desktop remoto.

	![Connettersi alla macchina virtuale](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/20-connectrdp.png)

1. Accedere con l'account amministratore (**\\DomainAdmin**) e la password (**Contoso!000**) configurati.

1. Per impostazione predefinita, verrà visualizzato il dashboard **Server Manager**.

1. Fare clic sul collegamento **Aggiungi ruoli e funzionalità** sul dashboard.

	![Aggiunta ruoli Esplora server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784623.png)

1. Selezionare **Avanti** fino a visualizzare la sezione **Ruoli server**.

1. Selezionare i ruoli **Servizi di dominio di Active Directory** e **Server DNS**. Quando richiesto, aggiungere eventuali funzionalità aggiuntive richieste da questi ruoli.

	>[AZURE.NOTE] Verrà visualizzato un avviso di convalida che indica che non esiste alcun indirizzo IP statico. Se si sta eseguendo il test della configurazione, fare clic su Continua. Per scenari di produzione impostare l'indirizzo IP come statico nel portale di Azure o [usare PowerShell per impostare l'indirizzo IP statico della macchina virtuale controller di dominio](./virtual-network/virtual-networks-reserved-private-ip.md).

	![Finestra di dialogo Aggiungi ruoli](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784624.png)

1. Fare clic su **Avanti** fino a raggiungere la sezione **Conferma**. Selezionare la casella di controllo **Riavvia automaticamente il server di destinazione se necessario**.

1. Fare clic su **Installa**.

1. Dopo l'installazione delle funzionalità, tornare al dashboard **Server Manager**.

1. Selezionare la nuova opzione **Servizi di dominio di Active Directory** nel riquadro di sinistra.

1. Fare clic sul collegamento **Altro** sulla barra di avviso gialla.

	![Finestra di dialogo Servizi di dominio di Active Directory nella macchina virtuale di Server DNS](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784625.png)

1. Nella colonna **Azione** della finestra di dialogo **Tutti i server - Dettagli attività** fare cli su **Alza di livello il server a controller di dominio**.

1. Nella **Configurazione guidata Servizi di dominio di Active Directory**, usare i seguenti valori:

| **Page** |Impostazione|
|---|---|
|**Configurazione distribuzione** |**Aggiungi una nuova foresta** = Selezionata<br/>**Nome di dominio radice** = corp.contoso.com|
|**Opzioni controller di dominio**|**Password DSRM** = Contoso!000<br/>**Conferma password** = Contoso!000|

1. Fare clic su **Avanti** per procedere nelle altre pagine della procedura guidata. Nella pagina **Controllo dei prerequisiti** verificare che venga visualizzato il seguente messaggio: **Tutti i controlli dei prerequisiti sono riusciti**. Si noti che è opportuno leggere tutti i messaggi di avviso applicabili, ma è possibile continuare con l'installazione.

1. Fare clic su **Installa**. La macchina virtuale **ad-primary-dc** si riavvierà automaticamente.

### Configurare il secondo controller di dominio

Dopo il riavvio del controller di dominio primario, è possibile configurare il secondo controller di dominio. Questo passaggio facoltativo serve a garantire una disponibilità elevata. Per completare questo passaggio, sarà necessario conoscere l'indirizzo IP privato per il controller di dominio. È possibile ottenerlo dal portale di Azure. Seguire questi passaggi per configurare il secondo controller di dominio.

1. Accedere di nuovo al computer **ad-primary-dc**. 

1. Aprire Desktop remoto e connettersi al controller di dominio secondario in base all'indirizzo IP. Se non si conosce l'indirizzo IP del controller di dominio secondario, andare al portale di Azure e controllare l'indirizzo assegnato all'interfaccia di rete per il controller di dominio secondario.

1. Sostituire l'indirizzo del server DNS preferito con l'indirizzo del controller di dominio.

1. Avviare il file RDP nel controller di dominio primario (**ad-primary-dc**) e accedere alla VM con l'account amministratore (**BUILTIN\\DomainAdmin**) e la password (**Contoso!000**) configurati.

1. Dal controller di dominio primario avviare una sessione di Desktop remoto in **ad-secondary-dc** usando l'indirizzo IP. Usare lo stesso account e la stessa password.

1. Dopo l'accesso, verrà visualizzato il dashboard **Server Manager**. Nel riquadro di sinistra fare clic su **Server locale**.

1. Selezionare il collegamento **Indirizzo IPv4 assegnato da DHCP - Compatibile IPv6**.

1. Nella finestra **Connessioni di rete** selezionare l'icona della rete.

	![Modificare il Server DNS preferito della macchina virtuale](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784629.png)

1. Sulla barra dei comandi fare clic su **Cambia impostazioni di connessione** (a seconda delle dimensioni della finestra, potrebbe essere necessario fare clic sulla doppia freccia destra per visualizzare questo comando).

1. Selezionare **Protocollo Internet versione 4 (TCP/IPv4)** e fare clic su Proprietà.

1. Selezionare Utilizza i seguenti indirizzi server DNS e specificare l'indirizzo del controller di dominio primario in **Server DNS preferito**.

1. L'indirizzo è quello assegnato a una VM nella subnet subnet-1 nella rete virtuale di Azure e la VM in questione è **ad-primary-dc**. Per verificare l'indirizzo IP di **ad-primary-dc** digitare **nslookup ad-primary-dc** al prompt dei comandi, come illustrato di seguito.

	![Usare NSLOOKUP per trovare l'indirizzo IP del controller di dominio](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC664954.png)

  >[AZURE.NOTE] Dopo avere impostato il DNS, è possibile perdere la sessione RDP al server membro. In questo caso, riavviare la VM dal portale di Azure.


1. Fare clic su **OK** e quindi su **Chiudi** per eseguire il commit delle modifiche. A questo punto è possibile aggiungere la macchina virtuale a **corp.contoso.com**.

1. Ripetere i passaggi seguiti per creare il primo controller di dominio, ma nella **Configurazione guidata Servizi di dominio Active Directory** usare i valori seguenti:

|Page|Impostazione|
|---|---|
|**Configurazione distribuzione**|**Aggiungi un controller di dominio a un dominio esistente** = selezionata<br/>**Radice** = corp.contoso.com|
|**Opzioni controller di dominio**|**Password DSRM** = Contoso!000<br/>**Conferma password** = Contoso!000|


### Configurare gli account di dominio

Nei seguenti passaggi vengono configurati gli account Active Directory (AD) per un uso successivo.

1. Accedere di nuovo al computer **ad-primary-dc**.

1. In **Server Manager** selezionare **Strumenti** e quindi fare clic su **Centro di amministrazione di Active Directory**.

	![Centro di amministrazione di Active Directory](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784626.png)

1. In **Centro di amministrazione di Active Directory** selezionare **corp (local)** dal riquadro di sinistra.

1. Nel riquadro di destra **Attività** selezionare **Nuovo**, quindi fare clic su **Utente**. Usare le seguenti impostazioni:

|Impostazione|Valore|
|---|---|
|**Nome**|Installa|
|**Utente SamAccountName**|Installa|
|**Password**|Contoso!000|
|**Conferma password**|Contoso!000|
|**Altre opzioni password**|Selezionato|
|**Nessuna scadenza password**|Selezionato|

1. Fare clic su **OK** per creare l’utente **Install**. Questo account verrà usato per configurare il cluster di failover e il gruppo di disponibilità.

1. Creare due utenti aggiuntivi con gli stessi passaggi: **CORP\\SQLSvc1** e **CORP\\SQLSvc2**. Questi account verranno usati per le istanze di SQL Server. È quindi necessario assegnare a **CORP\\Install** le autorizzazioni richieste per configurare WSCF (Windows Service Failover Clustering).

1. Nel **Centro di amministrazione di Active Directory** selezionare **corp (local)** nel riquadro di sinistra. Quindi, nel riquadro di destra **Attività** fare clic su **Proprietà**.

	![Proprietà utente CORP](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784627.png)

1. Selezionare **Estensioni**, quindi fare clic sul pulsante **Avanzate** nella scheda **Sicurezza**.

1. Nella finestra di dialogo **Impostazioni avanzate di sicurezza per corp** seguire questa procedura. Fare clic su **Aggiungi**.

1. Fare clic su **Seleziona un'entità**. Quindi cercare **CORP\\Install**. Fare clic su **OK**.

1. Selezionare le autorizzazioni **Leggi tutte le proprietà** e **Crea oggetti computer**.

	![Autorizzazioni utente Corp](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784628.png)

1. Fare clic su **OK** e quindi fare di nuovo clic su **OK**. Chiudere la finestra delle proprietà di corp.

Dopo avere completato la configurazione di Active Directory e degli oggetti utente, si procederà alla creazione di due VM di SQL Server e di una VM server di controllo che verranno aggiunte tutte e tre al dominio.

## Creare VM di SQL Server

###Creare e configurare le VM di SQL Server

Successivamente, creare tre VM, tra cui due VM di SQL Server e un nodo del cluster WSFC. Per creare ogni VM, tornare al gruppo di risorse **HA-AG-RG**, fare clic su **Aggiungi**, cercare l'elemento della raccolta appropriato, **Macchina virtuale** e quindi **Da raccolta**. Successivamente, usare i modelli indicati della seguente tabella per creare le macchine virtuali.

|Page|VM1|VM2|VM3|
|---|---|---|---|
|Selezionare l'elemento della raccolta appropriato|**Windows Server 2012 R2 Datacenter**|**SQL Server 2014 SP1 Enterprise in Windows Server 2012 R2**|**SQL Server 2014 SP1 Enterprise in Windows Server 2012 R2**|
| Configurazione della macchina virtuale - **Nozioni di base** | **Nome** = cluster-fsw<br/>**Nome utente** = DomainAdmin<br/>**Password** = Contoso!000<br/>**Sottoscrizione** = Sottoscrizione in uso<br/>**Gruppo di risorse** = SQL-HA-RG<br/>**Posizione** = Posizione di Azure | **Nome** = sqlserver-0<br/>**Nome utente** = DomainAdmin<br/>**Password** = Contoso!000<br/>**Sottoscrizione** = Sottoscrizione in uso<br/>**Gruppo di risorse** = SQL-HA-RG<br/>**Posizione** = Posizione di Azure | **Nome** = sqlserver-1<br/>**Nome utente** = DomainAdmin<br/>**Password** = Contoso!000<br/>**Sottoscrizione** = Sottoscrizione in uso<br/>**Gruppo di risorse** = SQL-HA-RG<br/>**Posizione** = Posizione di Azure |
|Configurazione della macchina virtuale - **Dimensioni** |DS1 (1 core, 3,5 GB di memoria)|**DIMENSIONI** = DS 2 (2 core, 7 GB di memoria)|**DIMENSIONI** = DS 2 (2 core, 7 GB di memoria)|
|Configurazione della macchina virtuale - **Impostazioni**|**Archiviazione** = Premium (SSD)<br/>**SUBNET DI RETE** = autoHAVNET<br/>**ACCOUNT DI ARCHIVIAZIONE** = Usare un account di archiviazione generato automaticamente<br/>**Subnet** = subnet-2(10.1.1.0/24)<br/>**Indirizzo IP pubblico** = Nessuno<br/>**Gruppo di sicurezza di rete** = Nessuno<br/>**Monitoraggio diagnostica** = Abilitato<br/>**Account di archiviazione di diagnostica** = Usare un account di archiviazione generato automaticamente<br/>**SET DI DISPONIBILITÀ** = sqlAvailabilitySet<br/>|**Archiviazione** = Premium (SSD)<br/>**SUBNET DI RETE** = autoHAVNET<br/>**ACCOUNT DI ARCHIVIAZIONE** = Usare un account di archiviazione generato automaticamente<br/>**Subnet** = subnet-2(10.1.1.0/24)<br/>**Indirizzo IP pubblico** = Nessuno<br/>**Gruppo di sicurezza di rete** = Nessuno<br/>**Monitoraggio diagnostica** = Abilitato<br/>**Account di archiviazione di diagnostica** = Usare un account di archiviazione generato automaticamente<br/>**SET DI DISPONIBILITÀ** = sqlAvailabilitySet<br/>|**Archiviazione** = Premium (SSD)<br/>**SUBNET DI RETE** = autoHAVNET<br/>**ACCOUNT DI ARCHIVIAZIONE** = Usare un account di archiviazione generato automaticamente<br/>**Subnet** = subnet-2(10.1.1.0/24)<br/>**Indirizzo IP pubblico** = Nessuno<br/>**Gruppo di sicurezza di rete** = Nessuno<br/>**Monitoraggio diagnostica** = Abilitato<br/>**Account di archiviazione di diagnostica** = Usare un account di archiviazione generato automaticamente<br/>**SET DI DISPONIBILITÀ** = sqlAvailabilitySet<br/>
|Configurazione della macchina virtuale - **Impostazioni SQL Server**|Non applicabile|**Connettività SQL** = Privata (all'interno della rete virtuale)<br/>**Porta** = 1433<br/>**Autenticazione SQL** = Disabilita<br/>**Configurazione dell'archiviazione** = Generale<br/>**Applicazione automatica delle patch** = Domenica alle 2:00<br/>**Backup automatizzato** = Disabilitato</br>**Integrazione dell'insieme di credenziali delle chiavi di Azure** = Disabilitata|**Connettività SQL** = Privata (all'interno della rete virtuale)<br/>**Porta** = 1433<br/>**Autenticazione SQL** = Disabilita<br/>**Configurazione dell'archiviazione** = Generale<br/>**Applicazione automatica delle patch** = Domenica alle 2:00<br/>**Backup automatizzato** = Disabilitato</br>**Integrazione dell'insieme di credenziali delle chiavi di Azure** = Disabilitata|

<br/>

>[AZURE.NOTE] La configurazione precedente suggerisce macchine virtuali di livello STANDARD, perché le macchine di livello BASIC non supportano gli endpoint con carico bilanciato necessari al listener del gruppo di disponibilità. Inoltre, le dimensioni delle macchine qui suggerite sono pensate per il test dei gruppi di disponibilità in VM di Azure. Per ottenere prestazioni ottimali su carichi di lavoro di produzione, vedere le dimensioni e la configurazione consigliate per la macchina SQL Server in [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-performance.md).



Una volta eseguito il provisioning completo delle tre macchine virtuali, sarà necessario aggiungerle al dominio **corp.contoso.com** e concedere a queste ultime i diritti amministrativi di CORP\\Install.

Per continuare, annotare l'indirizzo IP virtuale di Azure per ogni VM. Ottenere l'indirizzo IP per ogni server. Nel gruppo di risorse Azure SQL-HA-RG fare clic sulla risorsa **autohaVNET**. Il pannello **autohaVNET** mostrerà gli indirizzi IP per ogni computer della rete. Registrare gli indirizzi IP per i dispositivi seguenti:

| Ruolo VM | Dispositivo | Indirizzo IP
| ----- | ----- | -----
| Controller di dominio primario | ad-primary-dc |
| Controller di dominio secondario | ad-secondary-dc |
| Controllo di condivisione file cluster | cluster-fsw |
| SQL Server | sqlserver-0 | 
| SQL Server | sqlserver-1 | 

Questi indirizzi verranno usati per configurare il servizio DNS per ogni VM. A tale scopo, seguire questa procedura per ognuna delle tre macchine virtuali.


1. In primo luogo, modificare l'indirizzo del server DNS preferito per ogni server membro. 

1. Avviare il file RDP nel controller di dominio primario (**ad-primary-dc**) e accedere alla VM con l'account amministratore (**BUILTIN\\DomainAdmin**) e la password (**Contoso!000**) configurati.

1. Dal controller di dominio primario avviare una sessione di Desktop remoto in **sqlserver-0** usando l'indirizzo IP. Usare lo stesso account e la stessa password.

1. Dopo l'accesso, verrà visualizzato il dashboard **Server Manager**. Nel riquadro di sinistra fare clic su **Server locale**.

1. Selezionare il collegamento **Indirizzo IPv4 assegnato da DHCP - Compatibile IPv6**.

1. Nella finestra **Connessioni di rete** selezionare l'icona della rete.

	![Modificare il Server DNS preferito della macchina virtuale](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784629.png)

1. Sulla barra dei comandi fare clic su **Cambia impostazioni di connessione** (a seconda delle dimensioni della finestra, potrebbe essere necessario fare clic sulla doppia freccia destra per visualizzare questo comando).

1. Selezionare **Protocollo Internet versione 4 (TCP/IPv4)** e fare clic su Proprietà.

1. Selezionare Utilizza i seguenti indirizzi server DNS e specificare l'indirizzo del controller di dominio primario in **Server DNS preferito**.

1. L'indirizzo è quello assegnato a una VM nella subnet subnet-1 nella rete virtuale di Azure e la VM in questione è **ad-primary-dc**. Per verificare l'indirizzo IP di **ad-primary-dc** digitare **nslookup ad-primary-dc** al prompt dei comandi, come illustrato di seguito.

	![Usare NSLOOKUP per trovare l'indirizzo IP del controller di dominio](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC664954.png)

  >[AZURE.NOTE] Dopo avere impostato il DNS, è possibile perdere la sessione RDP al server membro. In questo caso, riavviare la VM dal portale di Azure.


1. Fare clic su **OK** e quindi su **Chiudi** per eseguire il commit delle modifiche. A questo punto è possibile aggiungere la macchina virtuale a **corp.contoso.com**.

1. Tornando alla finestra **Server locale**, fare clic sul collegamento **WORKGROUP**.

1. Nella sezione **Nome computer** fare clic su **Cambia**.

1. Selezionare la casella di controllo **Dominio** e digitare **corp.contoso.com** nella casella di testo. Fare clic su **OK**.

1. Nella finestra di dialogo popup **Sicurezza di Windows** specificare le credenziali per l'account amministratore di dominio predefinito (**CORP\\DomainAdmin**) e la password (**Contoso!000**).

1. uando viene visualizzato il messaggio di benvenuto nel dominio corp.contoso.com, fare clic su **OK**.

1. Fare clic su **Chiudi**, quindi fare clic su **Riavvia ora** nella finestra di dialogo popup.

1. Ripetere questi passaggi per il server di controllo di condivisione file e per ogni SQL Server.

### Aggiungere l'utente Corp\\Install come amministratore in ogni VM del cluster:

1. Attendere il riavvio della VM, quindi avviare di nuovo il file RDP dal controller di dominio primario per accedere a **sqlserver-0** usando l'account **BUILTIN\\DomainAdmin**.

1. In **Server Manager** selezionare **Strumenti**, quindi fare clic su **Gestione computer**.

	![Gestione computer](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784630.png)

1. Nella finestra **Gestione computer**, espandere **Utenti e gruppi locali**, quindi selezionare **Gruppi**.

1. Fare doppio clic sul gruppo **Administrators**.

1. Nella finestra di dialogo **Proprietà Administrators** fare clic su pulsante **Aggiungi**.

1. Immettere l'utente **CORP\\Install**, quindi fare clic su **OK**. Alla richiesta delle credenziali, usare l'account **DomainAdmin** con la password **Contoso! 000**.

1. Fare clic su **OK** per chiudere la finestra di dialogo **Proprietà Administrators**.

1. Ripetere i passaggi precedenti in **sqlserver-1** e **cluster-fsw**.

## Creare il cluster

### Aggiungere la funzionalità **Clustering di failover** a ogni VM del cluster.

1. Usare RDP per connettersi a **sqlserver-0**.

1. Nel dashboard **Server Manager** fare clic su **Aggiungi ruoli e funzionalità**.

1. In **Aggiunta guidata ruoli e funzionalità** fare clic su **Avanti** fino a visualizzare la pagina **Funzionalità**.

1. Selezionare **Clustering di failover**. Quando richiesto, aggiungere altre eventuali funzionalità dipendenti.

	![Aggiunta della funzionalità Clustering di failover alla macchina virtuale](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784631.png)

1. Fare clic su **Avanti**, quindi su **Installa** nella pagina **Conferma**.

1. Al termine dell'installazione della funzionalità **Clustering di failover** fare clic su **Chiudi**.

1. Disconnettersi dalla macchina virtuale.

1. Ripetere i passaggi di questa sezione in **sqlserver-1** e **cluster-fsw**.

Completato il provisioning, le macchine virtuali di SQL Server sono in esecuzione, ma vengono installate con SQL Server usando le opzioni predefinite.

### Creare il cluster WSFC

In questa sezione si crea il cluster WSFC in cui verrà ospitato il gruppo di disponibilità che verrà creato in un secondo momento. A questo punto sono state eseguite le operazioni indicate di seguito per ognuna delle tre macchine virtuali che verranno utilizzate nel cluster WSFC:

- Provisioning completo in Azure

- Aggiunta della macchina virtuale al dominio

- Aggiunta di **CORP\\Install** al gruppo Administrators locale

- Aggiunta della funzionalità Clustering di failover

Tutte queste operazioni sono prerequisiti in ognuna delle macchine virtuali prima che ne sia possibile l'aggiunta al cluster WSFC.

Si noti inoltre che la rete virtuale di Azure non si comporta nello stesso modo di una rete locale. È necessario creare il cluster nel seguente ordine:

1. Creare un cluster a nodo singolo in uno dei nodi (**sqlserver-0**).

1. Modificare l'indirizzo IP del cluster in un indirizzo IP non usato in **sqlsubnet**.

1. Portare il nome del cluster online.

1. Aggiungere gli altri nodi (**sqlserver-1** e **cluster-fsw**).

Seguire questa procedura per effettuare le seguenti attività e configurare completamente il cluster.

1. Avviare il file RDP per **sqlserver-0** e accedere con l'account di dominio **CORP\\Install**.

1. Nel dashboard **Server Manager** selezionare **Strumenti**, quindi fare clic su **Gestione cluster di failover**.

1. Nel riquadro di sinistra fare clic con il pulsante destro del mouse su **Gestione cluster di failover**, quindi scegliere **Crea un cluster**, come illustrato di seguito.

	![Creazione di un cluster](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784632.png)

1. Nella Creazione guidata Cluster creare un cluster a un nodo procedendo nelle pagine con le seguenti impostazioni:

|Page|Impostazioni|
|---|---|
|Prima di iniziare|Valori predefiniti|
|Selezione dei server|Digitare **sqlserver-0** in **Immettere il nome del server** e fare clic su **Aggiungi**|
|Avviso di convalida|Selezionare **No. Non è necessario il supporto di Microsoft per il cluster e pertanto non desidero eseguire i test di convalida. Facendo clic su Avanti verrà proseguita la creazione del cluster.**.|
|Punto di accesso per l'amministrazione del cluster|Digitare **Cluster1** in **Nome cluster**|
|Conferma|Usare le impostazioni predefinite a meno a meno che non si usino spazi di archiviazione. Vedere la nota che segue questa tabella.|

>[AZURE.NOTE] Se si usa la funzionalità [Spazi di archiviazione](https://technet.microsoft.com/library/hh831739), che raggruppa più dischi in pool di archiviazione, è necessario deselezionare la casella di controllo **Aggiungi tutte le risorse di archiviazione idonee al cluster** nella pagina **Conferma**. Se non si deseleziona questa opzione, i dischi virtuali vengono disconnessi durante il processo di clustering. Di conseguenza, non vengono visualizzati in Gestione disco o in Esplora risorse fino a quando gli spazi di archiviazione non vengono rimossi dal cluster e ricollegati tramite PowerShell.

Ora che il cluster è stato creato, verificare la configurazione e aggiungere i nodi rimanenti.

1. Nel riquadro centrale scorrere verso il basso fino alla sezione **Risorse principali del cluster** ed espandere i dettagli **Nome: Cluster1**. Lo stato visualizzato di entrambe le risorse **Nome** e **Indirizzo IP** deve essere **Operazione non riuscita**. La risorsa Indirizzo IP non può essere portata online in quanto al cluster è assegnato lo stesso indirizzo IP della macchina, vale a dire che si tratta di un indirizzo duplicato.

1. Fare clic con il pulsante destro del mouse sulla risorsa **Indirizzo IP** non riuscita, quindi fare clic su **Proprietà**.

	![Proprietà del cluster](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784633.png)

1. Selezionare **Indirizzo IP statico** e specificare un indirizzo disponibile da subnet-2 nella casella di testo Indirizzo. Fare quindi clic su **OK**.

1. Nella sezione **Risorse principali del cluster** fare clic con il pulsante destro del mouse su **Nome: Cluster1** e scegliere **Porta online**. Attendere finché entrambe le risorse non sono online Quando la risorsa del nome cluster torna online, il server del controller di dominio viene aggiornato con un nuovo account del computer Active Directory. L'account di Active Directory verrà utilizzato per seguire il servizio del cluster del gruppo di disponibilità in un secondo momento.

1. Infine, si aggiungono i rimanenti nodi al cluster Nella struttura del browser fare clic con il pulsante destro del mouse su **Cluster1.corp.contoso.com** e scegliere **Aggiungi nodo**, come mostrato di seguito.

	![Aggiungere un nodo al cluster](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784634.png)

1. In **Aggiunta guidata nodi** fare clic su **Avanti**. Nella pagina **Selezione dei server** aggiungere **sqlserver-1** e **cluster-fsw** all'elenco, digitando il nome del server in **Immettere il nome del server** e quindi facendo clic su **Aggiungi**. Al termine dell'operazione, scegliere **Avanti**.

1. Nella pagina **Avviso di convalida** fare clic su **No** (in uno scenario di produzione è necessario eseguire i test di convalida). Quindi fare clic su **Avanti**.

1. Nella pagina **Conferma** fare clic su **Avanti** per aggiungere i nodi.

	>[AZURE.WARNING] Se si usa la funzionalità [Spazi di archiviazione](https://technet.microsoft.com/library/hh831739), che raggruppa più dischi in pool di archiviazione, è necessario deselezionare la casella di controllo **Aggiungi tutte le risorse di archiviazione idonee al cluster**. Se non si deseleziona questa opzione, i dischi virtuali vengono disconnessi durante il processo di clustering. Di conseguenza, non vengono visualizzati in Gestione disco o in Esplora risorse fino a quando gli spazi di archiviazione non vengono rimossi dal cluster e ricollegati tramite PowerShell.

1. Una volta aggiunti i nodi al cluster, scegliere **Fine**. A questo punto, Gestione cluster di failover visualizza il cluster con i tre nodi elencati nel contenitore **Nodi**.

1. Disconnettersi dalla sessione desktop remoto.

## Configurare gruppi di disponibilità

In questa sezione verranno effettuate le azioni seguenti sia in **sqlserver-0** che in **sqlserver-1**:

- Aggiungere **CORP\\Install** come ruolo sysadmin all'istanza predefinita di SQL Server

- Aprire il firewall per l'accesso remoto a SQL Server per il processo di SQL Server e la porta probe

- Abilitare la funzionalità Gruppi di disponibilità

- Impostare l'account del servizio SQL Server rispettivamente su **CORP\\SQLSvc1** e **CORP\\SQLSvc2**

Queste azioni possono essere eseguite in qualsiasi ordine. Tuttavia, i passaggi riportati di seguito verranno illustrati in ordine. Seguire i passaggi sia per **sqlserver-0** che per **sqlserver-1**:

### Aggiungere l'account di installazione come ruolo predefinito del server sysadmin in ogni istanza di SQL Server

1. Disconnettersi dalla sessione desktop remoto, se non è ancora stata eseguita questa operazione.

1. Avviare i file RDP per **sqlserver-0** e **sqlserver-1** e accedere come **BUILTIN\\DomainAdmin**.

1. Avviare **SQL Server Management Studio** e aggiungere **CORP\\Install** come ruolo **sysadmin** all'istanza predefinita di SQL Server. In **Esplora oggetti** fare clic con il pulsante destro del mouse su **Account di accesso** e fare clic su **Nuovo account di accesso**.

1. Digitare **CORP\\Install** in **Nome account di accesso**.

1. Nella pagina **Ruoli del server** selezionare **sysadmin**. Fare quindi clic su **OK**. Una volta creato l'account di accesso, è possibile visualizzarlo espandendo **Account di accesso** in **Esplora oggetti**.

### Aprire il firewall per l'accesso remoto a SQL Server e la porta probe in ogni istanza di SQL Server

Questa soluzione richiede due regole del firewall in ogni istanza di SQL Server. La prima regola fornisce l'accesso in ingresso a SQL Server, la seconda fornisce l'accesso in ingresso per il servizio di bilanciamento del carico e il listener.

1. Successivamente, si crea una regola firewall per SQL Server. Dalla schermata **Start** avviare **Windows Firewall con sicurezza avanzata**.

1. Nel riquadro sinistro selezionare **Regole connessioni in entrata**. Nel riquadro di destra fare clic su **Nuova regola**.

1. Nella pagina **Tipo di regola** selezionare **Programma**, quindi fare clic su **Avanti**.

1. Nella pagina **Programma** selezionare **Percorso programma** e digitare **%ProgramFiles%\\Microsoft SQL Server\\MSSQL12.MSSQLSERVER\\MSSQL\\Binn\\sqlservr.exe** nella casella di testo (se si stanno seguendo le istruzioni ma si usa SQL Server 2012, la directory di SQL Server è **MSSQL11.MSSQLSERVER**). Quindi fare clic su **Avanti**.

1. Nella pagina **Azione** mantenere selezionata l'opzione **Consenti la connessione** e fare clic su **Avanti**.

1. Nella pagina **Profilo** accettare le impostazioni predefinite e fare clic su **Avanti**.

1. Nella pagina **Nome** specificare un nome per la regola, ad esempio **SQL Server (Regola di programma)** nella casella di testo **Nome** quindi fare clic su **Fine**.

1. Creare una regola del firewall in ingresso aggiuntiva per la porta probe. Si tratta di una regola in ingresso alla porta TCP 59999, ai fini di questa esercitazione. Assegnare alla regola il nome **Listener SQL Server**.

Completare tutti i passaggi in entrambe le istanze di SQL Server.

### Abilitare la funzionalità Gruppi di disponibilità in ogni istanza di SQL Server.

Eseguire questi passaggi in entrambe le istanze di SQL Server.

1. Successivamente, abilitare la funzionalità **Gruppi di disponibilità AlwaysOn**. Dalla schermata **Start** avviare **Gestione configurazione SQL Server**.

1. Nella struttura del browser fare clic su **Servizi di SQL**, fare clic con il pulsante destro del mouse sul servizio **SQL Server (MSSQLSERVER)**, quindi scegliere **Proprietà**.

1. Fare clic sulla scheda **Disponibilità elevata AlwaysOn**, selezionare **Abilita gruppi di disponibilità AlwaysOn**, come mostrato di seguito, quindi scegliere **Applica**. Fare clic su **OK** nella finestra di dialogo popup e non chiudere ancora la finestra delle proprietà. Verrà riavviato il servizio SQL Server dopo la modifica dell'account del servizio.

	![Abilitare Gruppi di disponibilità AlwaysOn in Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665520.gif)

### Impostare l'account del servizio SQL Server in ogni istanza di SQL Server

Eseguire questi passaggi in entrambe le istanze di SQL Server.

1. Successivamente, si modifica l'account del servizio SQL Server. Fare clic sulla scheda **Accesso**, digitare **CORP\\SQLSvc1** (per **sqlserver-0**) o **CORP\\SQLSvc2** (per **sqlserver-1**) in **Nome account**, inserire e confermare la password, quindi fare clic su **OK**.

1. Nella finestra popup fare clic su **Sì** per riavviare il servizio SQL Server. Dopo il riavvio del servizio SQL Server, vengono applicate le modifiche apportate nella finestra delle proprietà.

1. Disconnettersi dalle macchine virtuali.

### Creare il gruppo di disponibilità.

A questo punto, è possibile procedere con la configurazione di un gruppo di disponibilità. Di seguito è riportata una descrizione delle azioni da eseguire:

- Creare un nuovo database (**MyDB1**) in **sqlserver-0**

- Eseguire sia un backup completo, sia un backup del log delle transazioni del database.

- Ripristinare i backup completi e del log in **sqlserver-1** con l'opzione **NORECOVERY**

- Creare il gruppo di disponibilità (**AG1**) con commit sincrono, failover automatico e repliche secondarie leggibili.

### Creare il database MyDB1 in sqlserver-0:

1. Disconnettersi dalle sessioni desktop remoto per **sqlserver-0** e **sqlserver-1**, se non è ancora stata eseguita questa operazione.

1. Avviare il file RDP per **sqlserver-0** e accedere come **CORP\\Install**.

1. In **Esplora file**, creare una directory denominata **backup**. Questa directory verrà utilizzata per eseguire il backup e il ripristino del database.

1. Fare clic con il pulsante destro del mouse sulla nuova directory, puntare a **Condividi con**, quindi scegliere **Utenti specifici**, come mostrato di seguito.

	![Creare una cartella di backup](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665521.gif)

1. Aggiungere **CORP\\SQLSvc1** e assegnargli l'autorizzazione **Lettura/Scrittura**, quindi aggiungere **CORP\\SQLSvc2** e assegnargli l'autorizzazione **Lettura/Scrittura** come mostrato di seguito, quindi fare clic su **Condividi**. Una volta completato il processo di condivisione file, fare clic su **Fatto**.

	![Concedere le autorizzazioni per la cartella di backup](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665522.gif)

1. Successivamente, si crea il database. Dal menu **Start** avviare **SQL Server Management Studio**, quindi fare clic su **Connetti** per connettersi all'istanza predefinita di SQL Server.

1. In **Esplora oggetti** fare clic con il pulsante destro del mouse **Database** e scegliere **Nuovo database**.

1. In **Nome database** digitare **MyDB1**, quindi fare clic su **OK**.

### Eseguire un backup completo di MyDB1 e ripristinarlo in sqlserver-1:

1. Successivamente, si esegue un backup completo del database. In **Esplora oggetti** espandere **Database**, quindi fare clic con il pulsante destro del mouse su **MyDB1**, puntare ad **Attività** e fare clic su **Backup**.

1. Nella **sezione Source** mantenere **Tipo di backup** impostato su **Completo**. Nella **Destinazione** fare clic su **Rimuovi** per rimuovere il percorso predefinito per il file di backup.

1. Nella sezione **Destinazione** fare clic su **Aggiungi**.

1. Nella casella di testo **Nome file** digitare **\\\sqlserver-0\\backup\\MyDB1.bak**. Successivamente, fare clic su **OK**, quindi fare di nuovo clic su **OK** per completare l'operazione di backup del database. Una volta completata l'operazione di backup, fare di nuovo clic su **OK** per chiudere la finestra di dialogo.

1. Successivamente, si esegue un backup del log delle transazioni del database. In **Esplora oggetti** espandere **Database**, quindi fare clic con il pulsante destro del mouse su **MyDB1**, scegliere **Attività** e fare clic su **Backup**.

1. In **Tipo di backup** selezionare **Log delle transazioni**. Mantenere il percorso file **di destinazione** impostato su quello specificato in precedenza e fare clic su **OK**. Una volta completata l'operazione di backup, fare di nuovo clic su **OK**.

1. Si procede quindi al ripristino del backup completo e del log delle transazioni in **sqlserver-1**. Avviare il file RDP per **sqlserver-1** e accedere come **CORP\\Install**. Lasciare aperta la sessione desktop remoto per **sqlserver-0**.

1. Dal menu **Start** avviare **SQL Server Management Studio**, quindi fare clic su **Connetti** per connettersi all'istanza predefinita di SQL Server.

1. In **Esplora oggetti** fare clic con il pulsante destro del mouse su **Ripristina database** e scegliere **Ripristina database**.

1. Nella sezione **Origine** selezionare **Dispositivo** e fare clic sul pulsante **…**dei puntini di sospensione.

1. In **Seleziona dispositivi di backup**, fare clic su **Aggiungi**.

1. Nel percorso di file di Backup digitare **\\\sqlserver-0\\backup**, quindi fare clic su Aggiorna, selezionare MyDB1.bak, quindi fare clic su OK e nuovamente su OK. A questo punto vengono visualizzati il backup completo e il backup del log nel riquadro.

1. Andare alla pagina Opzioni, selezionare RIPRISTINO CON NORECOVERY nello stato del ripristino e fare clic su OK per ripristinare il database. Una volta completata l'operazione di ripristino, fare clic su OK.

### Creare il gruppo di disponibilità:

1. Tornare alla sessione desktop remoto per **sqlserver-0**. In **Esplora oggetti** in SSMS fare clic con il pulsante destro del mouse su **Disponibilità elevata AlwaysOn**, quindi scegliere **reazione guidata Gruppo di disponibilità**, come mostrato di seguito.

	![Avviare la creazione guidata nuovo gruppo di disponibilità](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665523.gif)

1. Nella pagina **Introduzione** fare clic su **Avanti**. Nella pagina **Specifica nome del gruppo di disponibilità** digitare **AG1** in **Nome gruppo di disponibilità**, quindi fare di nuovo clic su **Avanti**.

	![Creazione guidata nuovo gruppo di disponibilità: specificare il nome di gruppo di disponibilità](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665524.gif)

1. Nella pagina **Seleziona database** selezionare **MyDB1** e fare clic su **Avanti**. Il database soddisfa i prerequisiti per un gruppo di disponibilità in quanto è stato eseguito almeno un backup completo sulla replica primaria usata.

	![Creazione guidata nuovo gruppo di disponibilità: selezionare i database](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665525.gif)

1. Nella pagina **Specifica repliche** fare clic su **Aggiungi replica**.

	![Creazione guidata nuovo gruppo di disponibilità: specificare le repliche](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665526.png)

1. Viene visualizzata la finestra di dialogo **Connetti al server**. Digitare **sqlserver-1** in **Nome server**, quindi fare clic su **Connetti**.

	![Creazione guidata nuovo gruppo di disponibilità: connettersi al server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665527.png)

1. A questo punto, nella pagina **Specifica repliche** viene visualizzato **sqlserver-1** elencato in **Repliche di disponibilità**. Configurare le repliche come mostrato di seguito: Al termine dell'operazione, scegliere **Avanti**.

	![Creazione guidata nuovo gruppo di disponibilità: specificare le repliche (complete)](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665528.png)

1. Nella pagina **Seleziona sincronizzazione dati iniziale** selezionare **Solo join** e fare clic su **Avanti**. La sincronizzazione dei dati è già stata eseguita manualmente al momento dell'esecuzione dei backup completi e di transazione in **sqlserver-0** e i dati sono stati ripristinati in **sqlserver-1**. È invece possibile scegliere di non eseguire le operazioni di backup e ripristino nel database in uso e selezionare **Completo** per consentire l'esecuzione automatica della sincronizzazione dati tramite la Creazione guidata Gruppo di disponibilità. Tuttavia, non è un'operazione consigliata per database di grandi dimensioni presenti in alcune organizzazioni.

	![Creazione guidata nuovo gruppo di disponibilità: selezionare la sincronizzazione dati iniziale](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665529.png)

1. Nella pagina **Convalida** fare clic su **Avanti**. L'aspetto di questa pagina dovrebbe essere simile a quanto riportato di seguito. È presente un avviso per la configurazione del listener in quanto non è stato configurato un listener del gruppo di disponibilità. È possibile ignorare questo avviso, poiché l'esercitazione non configura alcun listener. In questa esercitazione il listener verrà creato più avanti. Per i dettagli su come configurare un listener, vedere [Configurare un servizio di bilanciamento del carico interno per un gruppo di disponibilità AlwaysOn in Azure](virtual-machines-windows-portal-sql-alwayson-int-listener.md).

	![Creazione guidata nuovo gruppo di disponibilità: convalida](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665530.gif)

1. Nella pagina **Riepilogo** fare clic su **Fine**, quindi attendere il completamento della configurazione del nuovo gruppo di disponibilità tramite la procedura guidata. Per visualizzare lo stato dettagliato è possibile fare clic su **Ulteriori dettagli** nella pagina **Avanzamento**. Al termine della procedura guidata, controllare la pagina **Risultati** per verificare la corretta creazione del gruppo di disponibilità, come mostrato di seguito, quindi fare clic su **Chiudi** per uscire dalla procedura guidata.

	![Creazione guidata nuovo gruppo di disponibilità: risultati](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665531.png)

1. In **Esplora oggetti** espandere **Disponibilità elevata AlwaysOn**, quindi espandere **Gruppi di disponibilità**. A questo punto viene visualizzato il nuovo gruppo di disponibilità in questo contenitore. Fare clic con il pulsante destro del mouse su **AG1 (Primary)** e selezionare **Mostra dashboard**.

	![Mostrare dashboard gruppo di disponibilità](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665532.png)

1. L'aspetto del **Dashboard AlwaysOn** dovrebbe essere simile a quello riportato di seguito. È possibile visualizzare le repliche, la modalità di failover di ciascuna replica e lo stato di sincronizzazione.

	![Dashboard gruppo di disponibilità](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665533.png)

1. Tornare a **Server Manager**, selezionare **Strumenti**, quindi avviare **Gestione cluster di failover**.

1. Espandere **Cluster1.corp.contoso.com**, quindi espandere **Servizi e applicazioni**. Selezionare **Ruoli** e notare che è stato creato il ruolo del gruppo di disponibilità **AG1** . Si noti che AG1 non dispone di indirizzi IP da cui i client del database possono connettersi al gruppo di disponibilità, poiché non è stato configurato un listener. È possibile connettersi direttamente al nodo primario per le operazioni di lettura/scrittura e al nodo secondario per le query di sola lettura.

	![Gruppo di disponibilità in Gestione cluster di failover](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665534.png)

>[AZURE.WARNING] Non tentare di eseguire il failover del gruppo di disponibilità da Gestione cluster di failover. È consigliabile eseguire tutte le operazioni di failover nel **Dashboard AlwaysOn** in SSMS. Per altre informazioni, vedere le [restrizioni relative all'uso di Gestione cluster di failover WSFC con i gruppi di disponibilità](https://msdn.microsoft.com/library/ff929171.aspx).

## Configurare il servizio di bilanciamento del carico interno

Per connettersi direttamente al gruppo di disponibilità, è necessario configurare un servizio di bilanciamento del carico interno in Azure e quindi creare il listener nel cluster. Questa sezione offre una panoramica generale di tali passaggi. Per istruzioni dettagliate, vedere [Configurare un servizio di bilanciamento del carico interno per un gruppo di disponibilità AlwaysOn in Azure](virtual-machines-windows-portal-sql-alwayson-int-listener.md).

### Creare il servizio di bilanciamento del carico in Azure

1. Nel portale di Azure andare a **SQL-HA-RG** e fare clic su **+ Aggiungi**.

1. Cercare **Servizio di bilanciamento del carico**. Scegliere il servizio di bilanciamento del carico pubblicato da Microsoft e fare clic su **Crea**.

1. Configurare i parametri seguenti per il servizio di bilanciamento del carico.

| Impostazione | Campo |
| --- | ---
| **Nome** | sqlLB
| **Schema** | Interno
| **Rete virtuale** | autoHAVNET
| **Subnet** | subnet-2. Si tratta dell'indirizzo IP che si imposterà per il listener nella risorsa cluster.  
| **Assegnazione indirizzi IP** | Static
| **Indirizzo IP** | Usare un indirizzo disponibile da subnet-2.
| **Sottoscrizione** | Usare la stessa sottoscrizione di tutte le altre risorse di questa soluzione.
| **Posizione** | Usare la stessa località di tutte le altre risorse di questa soluzione.

Fare clic su **Create**.

Configurare le impostazioni seguenti per il servizio di bilanciamento del carico:

| Impostazione | Campo |
| --- | ---|
| **Pool back-end** Nome | sqlLBBE 
| **SQLLBBE Set di disponibilità** | sqlAvailabilitySet
| **SQLLBBE Macchine virtuali** | sqlserver-0, sqlserver-1
| **SQLLBBE Usato da** | SQLAlwaysOnEndPointListener
| **Probe** Nome | SQLAlwaysOnEndPointProbe
| **Protocollo probe** | TCP
| **Porta probe** | 59999\. Si noti che è possibile usare qualsiasi porta non usata.
| **Intervallo probe** | 5
| **Probe Soglia di non integrità** | 2
| **Probe Usato da** | SQLAlwaysOnEndPointListener
| **Regole di bilanciamento del carico** Nome | SQLAlwaysOnEndPointListener
| **Regole di bilanciamento del carico Protocollo** | TCP
| **Regole di bilanciamento del carico Porta** | 1433\. Si noti che si usa questa porta perché è quella predefinita di SQL Server.
| **Regole di bilanciamento del carico Porta** | 1433\. Si noti che si usa questa porta perché è quella predefinita di SQL Server.
| **Regole di bilanciamento del carico Porta back-end** | 1433
| **Regole di bilanciamento del carico Probe** | SQLAlwaysOnEndPointProbe
| **Regole di bilanciamento del carico Salvataggio permanente sessione** | None
| **Regole di bilanciamento del carico Timeout inattività** | 4
| **Regole di bilanciamento del carico IP mobile (Direct Server Return)** | Enabled

>[AZURE.NOTE] È necessario abilitare Direct Server Return nelle regole di bilanciamento del carico al momento della creazione.

Dopo avere configurato il servizio di bilanciamento del carico, configurare il listener nel cluster di failover.

### Configurare il servizio di bilanciamento del carico nel cluster di failover

A questo punto è necessario configurare il listener del gruppo di disponibilità AlwaysOn nel cluster di failover.

1. Usare RDP per connettersi a SQL Server da ad-primary-dc a sqlserver-0.

1. In Gestione cluster di failover prendere nota del nome della rete di cluster. Per determinare il nome della rete di cluster in **Gestione cluster di failover**, fare clic su **Reti** nel riquadro sinistro. Si userà questo nome nella variabile `$ClusterNetworkName` nello script di PowerShell.

1. In Gestione cluster di failover espandere il nome del cluster e fare clic su **Ruoli**.

1. In **Ruoli** fare clic con il pulsante destro del mouse sul nome del gruppo di disponibilità e quindi scegliere**Aggiungi risorsa** > **Punto di accesso client**.

1. In **Nome** digitare **aglistener**. Fare clic su **Avanti** due volte e quindi su **Fine**. Non portare il listener o la risorsa in linea a questo punto.

1. Scegliere la scheda**Risorse**, quindi espandere il punto di accesso Client appena creato. Fare clic con il pulsante destro del mouse sulla risorsa IP e scegliere Proprietà. Prendere nota del nome dell'indirizzo IP. Si userà questo nome nella variabile `$IPResourceName` nello script di PowerShell.

1. In **Indirizzo IP** fare clic su **Indirizzo IP statico** e impostare l'indirizzo IP statico sullo stesso indirizzo usato nel portale di Azure per il servizio di bilanciamento del carico **sqlLB**. Si noti che si userà questo stesso indirizzo IP nella variabile `$ILBIP` nello script di PowerShell. Abilitare NetBIOS per questo indirizzo e fare clic su OK.

1. Nel nodo del cluster che attualmente ospita la replica primaria, aprire un'istanza di PowerShell ISE con privilegi elevati e incollare i comandi seguenti in un nuovo script.

        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = "<X.X.X.X>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    
1. Aggiornare le variabili ed eseguire lo script di PowerShell per configurare l'indirizzo IP e la porta per il nuovo listener.

1. In **Gestione cluster di failover** fare clic con il pulsante destro del mouse sulla risorsa gruppo di disponibilità e scegliere **Proprietà**. Nella scheda **Dipendenze** impostare il gruppo di risorse come dipendente dal nome della rete del listener.

1. Impostare la proprietà Porta di attesa su 1433. A questo scopo, aprire SQL Server Management Studio, fare clic con il pulsante destro del mouse sul listener del gruppo di disponibilità e scegliere Proprietà. Impostare **Porta** su 1433.

1. A questo punto è possibile [portare il listener online](virtual-machines-windows-portal-sql-alwayson-int-listener.md#2-bring-the-listener-online).

### Testare la connessione al listener

Per testare la connessione:

1. Usare RDP per connettersi all'istanza di SQL Server che non è proprietaria della replica.

1. Usare l'utilità sqlcmd per testare la connessione. Lo script seguente, ad esempio, stabilisce una connessione sqlcmd alla replica primaria tramite il listener con l'autenticazione di Windows:

        sqlcmd -S "<listenerName>" -E



## Passaggi successivi

Per altre informazioni sull'uso di SQL Server in Azure, vedere [SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0622_2016-->