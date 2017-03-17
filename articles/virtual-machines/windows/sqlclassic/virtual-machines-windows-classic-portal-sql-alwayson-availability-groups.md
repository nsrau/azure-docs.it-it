---
title: "Configurare gruppi di disponibilità AlwaysOn in macchine virtuali di Azure (distribuzione classica) | Microsoft Docs"
description: "Creare un gruppo di disponibilità AlwaysOn con le macchine virtuali di Azure. Questa esercitazione usa principalmente l&quot;interfaccia utente e gli strumenti invece dello scripting."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 8d48a3d2-79f8-4ab0-9327-2f30ee0b2ff1
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/22/2016
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: c1a1c7d2fd56e20d30cf0468de2d7d6c2935ef3e
ms.lasthandoff: 03/07/2017


---
# <a name="configure-always-on-availability-group-in-azure-vm-classic"></a>Configurare gruppi di disponibilità AlwaysOn in macchine virtuali di Azure (distribuzione classica)
> [!div class="op_single_selector"]
> * [Resource Manager: modello](../sql/virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
> * [Resource Manager: manuale](../sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
> * [Classica: interfaccia utente](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
> * [Classica: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)
> 
> 

<br/>

> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.

Questa esercitazione end-to-end illustra come implementare i gruppi di disponibilità tramite SQL Server AlwaysOn in esecuzione in macchine virtuali di Azure.

Al termine dell'esercitazione la soluzione SQL Server AlwaysOn in Azure sarà composta dagli elementi seguenti:

* Una rete virtuale contenente più subnet, tra cui una subnet front-end e una back-end
* Un controller di dominio con un dominio di Active Directory (AD)
* Due macchine virtuali di SQL Server distribuite nella subnet di back-end e aggiunte al dominio AD
* Un cluster WSFC a 3 nodi con il modello di quorum Maggioranza dei nodi
* Un gruppo di disponibilità con due repliche con commit sincrono di un database di disponibilità

La figura seguente è una rappresentazione grafica della soluzione.

![Architettura di laboratorio di test per gruppo di disponibilità in Azure](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC791912.png)

Si tratta di una possibile configurazione. È possibile, ad esempio, ridurre il numero di macchine virtuali per un gruppo di disponibilità con due repliche per risparmiare ore di calcolo in Azure, usando il controller di dominio come condivisione file di controllo del quorum in un cluster WSFC a 2 nodi. Questo metodo consente di ridurre di un'unità il numero di macchine virtuali rispetto alla configurazione precedente.

Nell’esercitazione si presuppongono le condizioni seguenti:

* Si dispone già di un account Azure.
* La procedura per il provisioning di una macchina virtuale SQL Server classica dalla raccolta di macchine virtuali tramite l'interfaccia utente grafica è già nota.
* Si ha già una conoscenza approfondita dei gruppi di disponibilità AlwaysOn. Per altre informazioni, vedere [Gruppi di disponibilità AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Se si è interessati all'uso di gruppi di disponibilità AlwaysOn con SharePoint, vedere anche [Configurare gruppi di disponibilità AlwaysOn di SQL Server 2012 per SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).
> 
> 

## <a name="create-the-virtual-network-and-domain-controller-server"></a>Creare la rete virtuale e il server del controller di dominio
Si inizia con un nuovo account di prova di Azure. Una volta terminata la configurazione dell'account, viene visualizzata la schermata iniziale del portale di Azure classico.

1. Fare clic sul pulsante **Nuovo** pulsante nell'angolo inferiore sinistro della pagina, come mostrato di seguito.
   
    ![Fare clic su Nuovo nel portale](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665511.gif)
2. Fare clic su **Servizi di rete**, quindi su **Rete virtuale** e su **Creazione personalizzata**, come indicato di seguito.
   
    ![Crea rete virtuale](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665512.gif)
3. Nella finestra **CREA RETE VIRTUALE** creare una nuova rete virtuale avanzando nelle pagine con le impostazioni riportate di seguito. 
   
   | Page | Impostazioni |
   | --- | --- |
   | Dettagli della rete virtuale |**NOME = ContosoNET**<br/>**AREA = Stati Uniti occidentali** |
   | Server DNS e connettività VPN |Nessuno |
   | Spazi di indirizzi della rete virtuale |Le impostazioni sono mostrate nella schermata riportata di seguito:  ![Creare una rete virtuale](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784620.png) |
4. Successivamente, si crea la macchina virtuale verrà utilizzata come controller di dominio (DC). Fare di nuovo clic su **Nuovo**, **Calcola**, **Macchina virtuale**, quindi su **Da raccolta**, come indicato di seguito.
   
    ![Creare una macchina virtuale](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784621.png)
5. Nella finestra di dialogo **CREA MACCHINA VIRTUALE** configurare una nuova macchina virtuale avanzando nelle pagine con le impostazioni riportate di seguito. 
   
   | Page | Impostazioni |
   | --- | --- |
   | Selezionare il sistema operativo della macchina virtuale |Windows Server 2012 R2 Datacenter |
   | Configurazione macchina virtuale |**DATA DI RILASCIO VERSIONE** = (più recente)<br/>**NOME MACCHINA VIRTUALE** = ContosoDC<br/>**PIANO** = STANDARD<br/>**DIMENSIONI** = A2 (2 core)<br/>**NUOVO NOME UTENTE** = AzureAdmin<br/>**NUOVA PASSWORD** = Contoso!000<br/>**CONFERMA** = Contoso!000 |
   | Configurazione macchina virtuale |**SERVIZIO CLOUD** = creare un nuovo servizio cloud<br/>**NOME DNS DEL SERVIZIO CLOUD** = nome univoco del servizio cloud<br/>**NOME DNS** = nome univoco (ad esempio: ContosoDC123)<br/>**AREA/GRUPPO DI AFFINITÀ/RETE VIRTUALE** = ContosoNET<br/>**SUNET RETE VIRTUALE** = Back(10.10.2.0/24)<br/>**ACCOUNT DI ARCHIVIAZIONE**: usare un account di archiviazione generato automaticamente<br/>**SET DI DISPONIBILITÀ** = (Nessuno) |
   | Opzioni macchina virtuale |Valori predefiniti |

Una volta terminata la configurazione della nuova macchina virtuale, attendere l’esecuzione del relativo provisioning. Il completamento di questo processo richiede del tempo e, se si fa clic sulla scheda **Macchina virtuale** nel portale di Azure classico, è possibile visualizzare il passaggio degli stati di ContosoDC da **Starting (Provisioning)** ad **Arrestato**, **Avvio in corso**, **Running (Provisioning)** e infine **In esecuzione**.

A questo punto, il provisioning del server del controller di dominio è completato. Si configurerà quindi il dominio di Active Directory nel server del controller di dominio.

## <a name="configure-the-domain-controller"></a>Configurare il controller di dominio
Nei seguenti passaggi viene configurata la macchina virtuale ContosoDC come controller di dominio per corp.contoso.com.

1. Nel portale selezionare la macchina **ContosoDC** . Nella scheda **Dashboard** fare clic su **Connetti** per aprire un file RDP per l'accesso desktop remoto.
   
    ![Connettersi alla macchina virtuale](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784622.png)
2. Accedere con l'account amministratore (**\AzureAdmin**) e la password (**Contoso!000**) configurati.
3. Per impostazione predefinita, verrà visualizzato il dashboard **Server Manager** .
4. Fare clic sul collegamento **Aggiungi ruoli e funzionalità** sul dashboard.
   
    ![Aggiunta ruoli Esplora server](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784623.png)
5. Selezionare **Avanti** fino a visualizzare la sezione **Ruoli server**.
6. Selezionare i ruoli **Active Directory Domain Services** e **Server DNS**. Quando richiesto, aggiungere eventuali funzionalità aggiuntive richieste da questi ruoli.
   
   > [!NOTE]
   > Verrà visualizzato un avviso di convalida che indica che non esiste alcun indirizzo IP statico. Se si sta eseguendo il test della configurazione, fare clic su Continua. Per scenari di produzione, [usare PowerShell per impostare l'indirizzo IP statico della macchina virtuale controller di dominio](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   > 
   > 
   
    ![Finestra di dialogo Aggiungi ruoli](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784624.png)
7. Fare clic su **Avanti** fino a raggiungere la sezione **Conferma**. Selezionare la casella di controllo **Riavvia automaticamente il server di destinazione se necessario** .
8. Fare clic su **Installa**.
9. Dopo l'installazione delle funzionalità, tornare al dashboard **Server Manager** .
10. Selezionare la nuova opzione **Servizi di dominio di Active Directory** nel riquadro di sinistra.
11. Fare clic sul collegamento **Altro** sulla barra di avviso gialla.
    
     ![Finestra di dialogo Servizi di dominio di Active Directory nella macchina virtuale di Server DNS](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784625.png)
12. Nella colonna **Azione** della finestra di dialogo **Dettagli attività tutti i server** fare clic su **Alza di livello il server a controller di dominio**.
13. Nella **Configurazione guidata Servizi di dominio di Active Directory**, usare i seguenti valori:
    
    | Page | Impostazione |
    | --- | --- |
    | Configurazione distribuzione |**Aggiungi una nuova foresta** = selezionata<br/>**Nome di dominio radice** = corp.contoso.com |
    | Opzioni controller di dominio |**Password** = Contoso!000<br/>**Conferma password** = Contoso!000 |
14. Fare clic su **Avanti** per procedere nelle altre pagine della procedura guidata. Nella pagina **Controllo dei prerequisiti** verificare che venga visualizzato il seguente messaggio: **Tutti i controlli dei prerequisiti sono riusciti**. Si noti che è opportuno leggere tutti i messaggi di avviso applicabili, ma è possibile continuare con l'installazione.
15. Fare clic su **Installa**. La macchina virtuale **ContosoDC** si riavvierà automaticamente.

## <a name="configure-domain-accounts"></a>Configurare gli account di dominio
Nei seguenti passaggi vengono configurati gli account Active Directory (AD) per un uso successivo.

1. Accedere di nuovo alla macchina virtuale **ContosoDC** .
2. In **Server Manager** selezionare **Strumenti** e quindi fare clic su **Centro di amministrazione di Active Directory**.
   
    ![Centro di amministrazione di Active Directory](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784626.png)
3. Nel pannello **Centro di amministrazione di Active Directory** select **corp (local)** dal riquadro di sinistra.
4. Nel riquadro **Attività** a destra selezionare **Nuovo** e quindi fare clic su **Utente**. Usare le seguenti impostazioni:
   
   | Impostazione | Valore |
   | --- | --- |
   | **Nome** |Installa |
   | **Utente SamAccountName** |Installa |
   | **Password** |Contoso!000 |
   | **Conferma password** |Contoso!000 |
   | **Altre opzioni password** |Selezionato |
   | **Nessuna scadenza password** |Selezionato |
5. Fare clic su **OK** per creare l'utente **Install**. Questo account verrà usato per configurare il cluster di failover e il gruppo di disponibilità.
6. Creare due utenti aggiuntivi con gli stessi passaggi: **CORP\SQLSvc1** e **CORP\SQLSvc2**. Questi account verranno usati per le istanze di SQL Server. Successivamente, è necessario assegnare a **CORP\Install** le autorizzazioni necessarie per configurare WSCF (Windows Service Failover Clustering).
7. Nel **Centro di amministrazione di Active Directory** selezionare **corp (local)** nel riquadro di sinistra. Nel riquadro **Attività** a destra fare quindi clic su **Proprietà**.
   
    ![Proprietà utente CORP](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784627.png)
8. Selezionare **Estensioni**, quindi fare clic sul pulsante **Avanzate** nella scheda **Sicurezza**.
9. Nella finestra di dialogo **Impostazioni avanzate di sicurezza per corp** seguire questa procedura. Fare clic su **Aggiungi**.
10. Fare clic su **Seleziona un'entità**. Cercare quindi **CORP\Install**. Fare clic su **OK**.
11. Selezionare le autorizzazioni **Leggi tutte le proprietà** e **Create Computer objects** (Crea oggetti computer).
    
     ![Autorizzazioni utente Corp](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784628.png)
12. Fare clic su **OK** e quindi di nuovo clic su **OK**. Chiudere la finestra delle proprietà di corp.

Dopo aver terminato la configurazione di Active Directory e degli oggetti utente, si procederà alla creazione di tre macchine virtuali di SQL Server che verranno aggiunte al dominio.

## <a name="create-the-sql-server-vms"></a>Creare le macchine virtuali di SQL Server
Successivamente, creare tre macchine virtuali, tra cui un nodo del cluster WSFC e due macchine virtuali SQL Server. Per creare ciascuna delle macchine virtuali, tornare al portale di Azure classico, fare clic su **Nuovo**, **Calcolo**, **Macchina virtuale**, quindi **Da raccolta**. Successivamente, usare i modelli indicati della seguente tabella per creare le macchine virtuali.

| Page | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Selezionare il sistema operativo della macchina virtuale |**Windows Server 2012 R2 Datacenter** |**SQL Server 2014 RTM Enterprise** |**SQL Server 2014 RTM Enterprise** |
| Configurazione macchina virtuale |**DATA DI RILASCIO VERSIONE** = (più recente)<br/>**NOME MACCHINA VIRTUALE** = ContosoWSFCNode<br/>**PIANO** = STANDARD<br/>**DIMENSIONI** = A2 (2 core)<br/>**NUOVO NOME UTENTE** = AzureAdmin<br/>**NUOVA PASSWORD** = Contoso!000<br/>**CONFERMA** = Contoso!000 |**DATA DI RILASCIO VERSIONE** = (più recente)<br/>**NOME MACCHINA VIRTUALE** = ContosoSQL1<br/>**PIANO** = STANDARD<br/>**DIMENSIONI** = A3 (4 core)<br/>**NUOVO NOME UTENTE** = AzureAdmin<br/>**NUOVA PASSWORD** = Contoso!000<br/>**CONFERMA** = Contoso!000 |**DATA DI RILASCIO VERSIONE** = (più recente)<br/>**NOME MACCHINA VIRTUALE** = ContosoSQL2<br/>**PIANO** = STANDARD<br/>**DIMENSIONI** = A3 (4 core)<br/>**NUOVO NOME UTENTE** = AzureAdmin<br/>**NUOVA PASSWORD** = Contoso!000<br/>**CONFERMA** = Contoso!000 |
| Configurazione macchina virtuale |**SERVIZIO CLOUD** = nome DNS univoco del servizio cloud creato in precedenza (ad esempio: ContosoDC123)<br/>**AREA/GRUPPO DI AFFINITÀ/RETE VIRTUALE** = ContosoNET<br/>**SUNET RETE VIRTUALE** = Back(10.10.2.0/24)<br/>**ACCOUNT DI ARCHIVIAZIONE**: usare un account di archiviazione generato automaticamente<br/>**SETDI DISPONIBILITÀ** = creare un set di disponibilità<br/>**NOME SET DI DISPONIBILITÀ** = SQLHADR |**SERVIZIO CLOUD** = nome DNS univoco del servizio cloud creato in precedenza (ad esempio: ContosoDC123)<br/>**AREA/GRUPPO DI AFFINITÀ/RETE VIRTUALE** = ContosoNET<br/>**SUNET RETE VIRTUALE** = Back(10.10.2.0/24)<br/>**ACCOUNT DI ARCHIVIAZIONE**: usare un account di archiviazione generato automaticamente<br/>**SET DI DISPONIBILITÀ** = SQLHADR (è anche possibile configurare il set di disponibilità dopo aver creato la macchina. Tutte e tre le macchine virtuali devono essere assegnate al set di disponibilità SQLHADR. |**SERVIZIO CLOUD** = nome DNS univoco del servizio cloud creato in precedenza (ad esempio: ContosoDC123)<br/>**AREA/GRUPPO DI AFFINITÀ/RETE VIRTUALE** = ContosoNET<br/>**SUNET RETE VIRTUALE** = Back(10.10.2.0/24)<br/>**ACCOUNT DI ARCHIVIAZIONE**: usare un account di archiviazione generato automaticamente<br/>**SET DI DISPONIBILITÀ** = SQLHADR (è anche possibile configurare il set di disponibilità dopo aver creato la macchina. Tutte e tre le macchine virtuali devono essere assegnate al set di disponibilità SQLHADR. |
| Opzioni macchina virtuale |Valori predefiniti |Valori predefiniti |Valori predefiniti |

<br/>

> [!NOTE]
> La configurazione precedente suggerisce macchine virtuali di livello STANDARD, poiché le macchine di livello BASIC non supportano gli endpoint con carico bilanciato necessari per creare in seguito listener del gruppo di disponibilità. Inoltre, le dimensioni delle macchine qui suggerite sono pensate per il test dei gruppi di disponibilità all'interno di macchine virtuali di Azure. Per ottenere prestazioni ottimali su carichi di lavoro di produzione, vedere le dimensioni e la configurazione consigliate per la macchina SQL Server in [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](../sql/virtual-machines-windows-sql-performance.md).
> 
> 

Dopo aver eseguito il provisioning completo delle tre macchine virtuali, sarà necessario aggiungerle al dominio **corp.contoso.com** e concedere alle macchine i diritti amministrativi di CORP\Install. A tale scopo, seguire questa procedura per ognuna delle tre macchine virtuali.

1. In primo luogo, modificare l'indirizzo del server DNS preferito. Per iniziare, scaricare il file desktop remoto (RDP) di ciascuna macchina virtuale nella directory locale selezionando la macchina virtuale nell'elenco e facendo clic sul pulsante **Connetti** . Per selezionare una macchina virtuale, fare clic su un punto qualsiasi ma nella prima cella della riga, come mostrato di seguito.
   
    ![Scaricare il file con estensione RDP](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664953.jpg)
2. Avviare il file RDP scaricato e accedere alla macchina virtuale con l'account amministratore (**BUILTIN\AzureAdmin**) e la password (**Contoso!000**).
3. Dopo l'accesso, verrà visualizzato il dashboard **Server Manager** . Nel riquadro di sinistra fare clic su **Server locale** .
4. Selezionare il collegamento **Indirizzo IPv4 assegnato da DHCP - Compatibile IPv6** .
5. Nella finestra **Connessioni di rete** selezionare l'icona della rete.
   
    ![Modificare il Server DNS preferito della macchina virtuale](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784629.png)
6. Sulla barra dei comandi fare clic su **Cambia impostazioni di connessione** (a seconda delle dimensioni della finestra, potrebbe essere necessario fare clic sulla doppia freccia destra per visualizzare questo comando).
7. Selezionare **Protocollo Internet versione 4 (TCP/IPv4)** e fare clic su Proprietà.
8. Selezionare Usa i seguenti indirizzi server DNS e specificare **10.10.2.4** in **Server DNS preferito**.
9. L'indirizzo **10.10.2.4** è quello assegnato a una macchina virtuale nella subnet 10.10.2.0/24 in una rete virtuale di Azure e la macchina virtuale in questione è **ContosoDC**. Per verificare l'indirizzo IP di **ContosoDC** digitare **nslookup contosodc** nel prompt dei comandi, come mostrato di seguito.
   
    ![Usare NSLOOKUP per trovare l'indirizzo IP del controller di dominio](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664954.jpg)
10. Fare clic su **OK** e su **Chiudi** per eseguire il commit delle modifiche. A questo punto è possibile aggiungere la macchina virtuale a **corp.contoso.com**.
11. Tornando alla finestra **Server locale**, fare clic sul collegamento **WORKGROUP**.
12. Nella sezione **Nome computer** fare clic su **Cambia**.
13. Selezionare la casella di controllo **Dominio** e digitare **corp.contoso.com** nella casella di testo. Fare clic su **OK**.
14. Nella finestra di dialogo popup **Sicurezza di Windows** specificare le credenziali per l'account amministratore di dominio predefinito (**CORP\AzureAdmin**) e la password (**Contoso!000**).
15. uando viene visualizzato il messaggio di benvenuto nel dominio corp.contoso.com, fare clic su **OK**.
16. Fare clic su **Chiudi** e quindi su **Riavvia ora** nella finestra di dialogo popup.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-vm"></a>Aggiungere l'utente Corp\Install come amministratore in ciascuna macchina virtuale:
1. Attendere fino al riavvio della macchina virtuale, quindi avviare di nuovo il file RDP per accedere alla macchina virtuale con l'account **BUILTIN\AzureAdmin**.
2. In **Server Manager** selezionare **Strumenti**, quindi fare clic su **Gestione computer**.
   
    ![Gestione computer](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784630.png)
3. Nella finestra **Gestione computer** espandere **Utenti e gruppi locali**, quindi selezionare **Gruppi**.
4. Fare doppio clic sul gruppo **Administrators** .
5. Nella finestra di dialogo **Proprietà Administrators** fare clic su pulsante **Aggiungi**.
6. Immettere l'utente **CORP\Install** e quindi fare clic su **OK**. Alla richiesta delle credenziali, usare l'account **AzureAdmin** con la password **Contoso!000**.
7. Fare clic su **OK** per chiudere la finestra di dialogo **Proprietà Administrators**.

### <a name="add-the-failover-clustering-feature-to-each-vm"></a>Aggiungere la funzionalità **Clustering di failover** a ogni macchina virtuale.
1. Nel dashboard **Server Manager** fare clic su **Aggiungi ruoli e funzionalità**.
2. In **Aggiunta guidata ruoli e funzionalità** fare clic su **Avanti** fino a visualizzare la pagina **Funzionalità**.
3. Selezionare **Clustering di failover**. Quando richiesto, aggiungere altre eventuali funzionalità dipendenti.
   
    ![Aggiunta della funzionalità Clustering di failover alla macchina virtuale](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784631.png)
4. Fare clic su **Avanti**, quindi su **Installa** nella pagina **Conferma**.
5. Al termine dell'installazione della funzionalità **Clustering di failover** fare clic su **Chiudi**.
6. Disconnettersi dalla macchina virtuale.
7. Ripetere i passaggi di questa sezione per tutti e tre i server, ovvero **ContosoWSFCNode**, **ContosoSQL1** e **ContosoSQL2**.

Completato il provisioning, le macchine virtuali di SQL Server sono in esecuzione, ma vengono installate con SQL Server usando le opzioni predefinite.

## <a name="create-the-wsfc-cluster"></a>Creare il cluster WSFC
In questa sezione si crea il cluster WSFC in cui verrà ospitato il gruppo di disponibilità che verrà creato in un secondo momento. A questo punto sono state eseguite le operazioni indicate di seguito per ognuna delle tre macchine virtuali che verranno utilizzate nel cluster WSFC:

* Provisioning completo in Azure
* Aggiunta della macchina virtuale al dominio
* Aggiunta di **CORP\Install** al gruppo Administrators locale
* Aggiunta della funzionalità Clustering di failover

Tutte queste operazioni sono prerequisiti in ognuna delle macchine virtuali prima che ne sia possibile l'aggiunta al cluster WSFC.

Si noti inoltre che la rete virtuale di Azure non si comporta nello stesso modo di una rete locale. È necessario creare il cluster nel seguente ordine:

1. Creare un cluster a nodo singolo in uno dei nodi (**ContosoSQL1**).
2. Modificare l'indirizzo IP del cluster in un indirizzo IP non usato (**10.10.2.101**).
3. Portare il nome del cluster online.
4. Aggiungere gli altri nodi (**ContosoSQL2** e **ContosoWSFCNode**).

Seguire questa procedura per effettuare le seguenti attività e configurare completamente il cluster.

1. Avviare il file RDP per **ContosoSQL1** e accedere con l'account di dominio **CORP\Install**.
2. Nel dashboard **Server Manager** selezionare **Strumenti** e quindi fare clic su **Gestione cluster di failover**.
3. Nel riquadro di sinistra fare clic con il pulsante destro del mouse su **Gestione cluster di failover** e quindi scegliere **Crea cluster**, come illustrato di seguito.
   
    ![Creazione di un cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784632.png)
4. Nella Creazione guidata Cluster creare un cluster a un nodo procedendo nelle pagine con le seguenti impostazioni:
   
   | Page | Impostazioni |
   | --- | --- |
   | Prima di iniziare |Valori predefiniti |
   | Selezione dei server |Digitare **ContosoSQL1** in **Immettere il nome del server** e fare clic su **Aggiungi** |
   | Avviso di convalida |Selezionare **No. Non è necessario il supporto di Microsoft per il cluster e pertanto non desidero eseguire i test di convalida. Facendo clic su Avanti verrà proseguita la creazione del cluster**. |
   | Punto di accesso per l'amministrazione del cluster |Digitare **Cluster1** in **Nome cluster** |
   | Conferma |Usare le impostazioni predefinite a meno a meno che non si usino spazi di archiviazione. Vedere la nota che segue questa tabella. |
   
   > [!WARNING]
   > Se si usa la funzionalità [Spazi di archiviazione](https://technet.microsoft.com/library/hh831739), che raggruppa più dischi in pool di archiviazione, è necessario deselezionare la casella di controllo **Aggiungi tutte le risorse di archiviazione idonee al cluster** nella pagina **Conferma**. Se non si deseleziona questa opzione, i dischi virtuali vengono disconnessi durante il processo di clustering. Di conseguenza, non vengono visualizzati in Gestione disco o in Esplora risorse fino a quando gli spazi di archiviazione non vengono rimossi dal cluster e ricollegati tramite PowerShell.
   > 
   > 
5. Nel riquadro di sinistra espandere **Gestione cluster di failover**, quindi fare clic su **Cluster1.corp.contoso.com**.
6. Nel riquadro centrale scorrere verso il basso fino alla sezione **Risorse principali del cluster** ed espandere i dettagli **Nome: Cluster1**. Lo stato visualizzato di entrambe le risorse **Nome** e **Indirizzo IP** deve essere **Operazione non riuscita**. La risorsa Indirizzo IP non può essere portata online in quanto al cluster è assegnato lo stesso indirizzo IP della macchina, vale a dire che si tratta di un indirizzo duplicato.
7. Fare clic con il pulsante destro del mouse sulla risorsa **Indirizzo IP** non riuscita, quindi scegliere **Proprietà**.
   
    ![Proprietà del cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784633.png)
8. Selezionare **Indirizzo IP statico** e specificare **10.10.2.101** nella casella di testo Indirizzo. Fare quindi clic su **OK**.
9. Nella sezione **Risorse principali del cluster** fare clic con il pulsante destro del mouse su **Nome: Cluster1** e scegliere **Porta online**. Attendere finché entrambe le risorse non sono online Quando la risorsa del nome cluster torna online, il server del controller di dominio viene aggiornato con un nuovo account del computer Active Directory. L'account di Active Directory verrà utilizzato per seguire il servizio del cluster del gruppo di disponibilità in un secondo momento.
10. Infine, si aggiungono i rimanenti nodi al cluster Nella struttura del browser fare clic con il pulsante destro del mouse su **Cluster1.corp.contoso.com** e scegliere **Aggiungi nodo**, come mostrato di seguito.
    
     ![Aggiungere un nodo al cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784634.png)
11. In **Aggiunta guidata nodi** fare clic su **Avanti**. Nella pagina **Selezione dei server** aggiungere **ContosoSQL2** e **ContosoWSFCNode** all'elenco digitando il nome del server in **Immettere il nome del server** e facendo clic su **Aggiungi**. Al termine dell'operazione, scegliere **Avanti**.
12. Nella pagina **Avviso di convalida** fare clic su **No**. In uno scenario di produzione è necessario eseguire i test di convalida. Quindi fare clic su **Next**.
13. Nella pagina **Conferma** fare clic su **Avanti** per aggiungere i nodi.
    
    > [!WARNING]
    > Se si usa la funzionalità [Spazi di archiviazione](https://technet.microsoft.com/library/hh831739), che raggruppa più dischi in pool di archiviazione, è necessario deselezionare la casella di controllo **Aggiungi tutte le risorse di archiviazione idonee al cluster** . Se non si deseleziona questa opzione, i dischi virtuali vengono disconnessi durante il processo di clustering. Di conseguenza, non vengono visualizzati in Gestione disco o in Esplora risorse fino a quando gli spazi di archiviazione non vengono rimossi dal cluster e ricollegati tramite PowerShell.
    > 
    > 
14. Una volta aggiunti i nodi al cluster, scegliere **Fine**. A questo punto, Gestione cluster di failover visualizza il cluster con i tre nodi elencati nel contenitore **Nodi** .
15. Disconnettersi dalla sessione desktop remoto.

## <a name="prepare-the-sql-server-instances-for-availability-group"></a>Preparare le istanze di SQL Server per il gruppo di disponibilità
In questa sezione verranno effettuate le azioni seguenti sia in **ContosoSQL1** che in **contosoSQL2**:

* Aggiungere un account di accesso per **NT AUTHORITY\System** con le autorizzazioni necessarie impostate all'istanza predefinita di SQL Server
* Aggiungere **CORP\Install** come ruolo sysadmin all'istanza predefinita di SQL Server
* Aprire il firewall per l'accesso remoto di SQL Server
* Abilitare la funzionalità Gruppi di disponibilità AlwaysOn
* Impostare l'account del servizio SQL Server rispettivamente su **CORP\SQLSvc1** e **CORP\SQLSvc2**

Queste azioni possono essere eseguite in qualsiasi ordine. Tuttavia, i passaggi riportati di seguito verranno illustrati in ordine. Seguire questa procedura sia per **ContosoSQL1**, sia per **ContosoSQL2**:

1. Disconnettersi dalla sessione desktop remoto, se non è ancora stata eseguita questa operazione.
2. Avviare i file RDP per **ContosoSQL1** e **ContosoSQL2** e accedere come **BUILTIN\AzureAdmin**.
3. Innanzitutto, aggiungere **NT AUTHORITY\System** agli account di accesso di SQL Server con le autorizzazioni necessarie. Avviare **SQL Server Management Studio**.
4. Fare clic su **Connetti** per connettersi all'istanza predefinita di SQL Server.
5. In **Esplora oggetti**, espandere **Sicurezza**, quindi espandere **Account di accesso**.
6. Fare clic con il pulsante destro del mouse sull'account di accesso **NT AUTHORITY\System** e scegliere **Proprietà**.
7. Nella pagina **Entità a protezione diretta** per il server locale selezionare **Concedi** per le autorizzazioni seguenti e scegliere **OK**.
   
   * Alterare eventuali gruppi di disponibilità
   * Connettersi a SQL
   * Visualizzare lo stato del server
8. Successivamente, aggiungere **CORP\Install** come ruolo **sysadmin** all'istanza predefinita di SQL Server. In **Esplora oggetti** fare clic con il pulsante destro del mouse su **Account di accesso** e scegliere **Nuovo account di accesso**.
9. Digitare **CORP\Install** in **Nome account di accesso**.
10. Nella pagina **Ruoli del server** selezionare **sysadmin**. Fare quindi clic su **OK**. Una volta creato l'account di accesso, è possibile visualizzarlo espandendo **Account di accesso** in **Esplora oggetti**.
11. Successivamente, si crea una regola firewall per SQL Server. Dalla schermata **Start** avviare **Windows Firewall con sicurezza avanzata**.
12. Nel riquadro sinistro selezionare **Regole connessioni in entrata**. Nel riquadro di destra fare clic su **Nuova regola**.
13. Nella pagina **Tipo di regola** selezionare **Programma**, quindi fare clic su **Avanti**.
14. Nella pagina **Programma** selezionare **Percorso programma** e digitare **%ProgramFiles%\Microsoft SQL Server\MSSQL12.MSSQLSERVER\MSSQL\Binn\sqlservr.exe** nella casella di testo. Se si seguono queste istruzioni, ma si usa SQL Server 2012, la directory di SQL Server è **MSSQL11.MSSQLSERVER**. Quindi fare clic su **Next**.
15. Nella pagina **Azione** mantenere selezionata l'opzione **Consenti la connessione** e fare clic su **Avanti**.
16. Nella pagina **Profilo** accettare le impostazioni predefinite e fare clic su **Avanti**.
17. Nella pagina **Nome** specificare un nome per la regola, ad esempio **SQL Server (Regola di programma)** nella casella di testo **Nome**, quindi fare clic su **Fine**.
18. Successivamente, abilitare la funzionalità **Gruppi di disponibilità AlwaysOn** . Dalla schermata **Start** avviare **Gestione configurazione SQL Server**.
19. Nella struttura del browser fare clic su **Servizi di SQL Server**, fare clic con il pulsante destro del mouse sul servizio **SQL Server (MSSQLSERVER)**, quindi scegliere **Proprietà**.
20. Fare clic sulla scheda **Disponibilità elevata AlwaysOn**, selezionare **Abilita gruppi di disponibilità AlwaysOn**, quindi scegliere **Applica**, come mostrato di seguito. Fare clic su **OK** nella finestra di dialogo popup e non chiudere ancora la finestra delle proprietà. Verrà riavviato il servizio SQL Server dopo la modifica dell'account del servizio.
    
     ![Abilita gruppi di disponibilità AlwaysOn](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665520.gif)
21. Successivamente, si modifica l'account del servizio SQL Server. Fare clic sulla scheda **Accesso**, digitare **CORP\SQLSvc1** (per **ContosoSQL1**) o **CORP\SQLSvc2** (per **ContosoSQL2**) in **Nome account**, inserire e confermare la password, quindi fare clic su **OK**.
22. Nella finestra popup fare clic su **Sì** per riavviare il servizio SQL Server. Dopo il riavvio del servizio SQL Server, vengono applicate le modifiche apportate nella finestra delle proprietà.
23. Disconnettersi dalle macchine virtuali.

## <a name="create-the-availability-group"></a>Creare il gruppo di disponibilità.
A questo punto, è possibile procedere con la configurazione di un gruppo di disponibilità. Di seguito è riportata una descrizione delle azioni da eseguire:

* Creare un nuovo database (**MyDB1**) in **ContosoSQL1**
* Eseguire sia un backup completo, sia un backup del log delle transazioni del database.
* Ripristinare i backup completi e del log in **ContosoSQL2** con l'opzione **NORECOVERY**
* Creare il gruppo di disponibilità (**AG1**) con commit sincrono, failover automatico e repliche secondarie leggibili.

### <a name="create-the-mydb1-database-on-contososql1"></a>Creare il database MyDB1 in ContosoSQL1:
1. Disconnettersi dalle sessioni desktop remoto per **ContosoSQL1** e **ContosoSQL2**, se non è ancora stata eseguita questa operazione.
2. Avviare il file RDP per **ContosoSQL1** e accedere come **CORP\Install**.
3. In **Esplora file** creare in **C:\** una directory denominata **backup**. Questa directory verrà utilizzata per eseguire il backup e il ripristino del database.
4. Fare clic con il pulsante destro del mouse sulla nuova directory, scegliere **Condividi con**, quindi fare clic su **Utenti specifici** come mostrato di seguito.
   
    ![Creare una cartella di backup](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665521.gif)
5. Aggiungere **CORP\SQLSvc1** e assegnarvi l'autorizzazione **Lettura/Scrittura**, quindi aggiungere **CORP\SQLSvc2** e assegnarvi l'autorizzazione **Lettura** come mostrato di seguito, quindi fare clic su **Condividi**. Una volta completato il processo di condivisione file, fare clic su **Fatto**.
   
    ![Concedere le autorizzazioni per la cartella di backup](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665522.gif)
6. Successivamente, si crea il database. Dal menu **Start** avviare **SQL Server Management Studio**, quindi fare clic su **Connetti** per connettersi all'istanza predefinita di SQL Server.
7. In **Esplora oggetti** fare clic con il pulsante destro del mouse **Database** e scegliere **Nuovo database**.
8. In **Nome database** digitare **MyDB1**, quindi fare clic su **OK**.

### <a name="take-a-full-backup-of-mydb1-and-restore-it-on-contososql2"></a>Eseguire un backup completo di MyDB1 e ripristinarlo in ContosoSQL2:
1. Successivamente, si esegue un backup completo del database. In **Esplora oggetti** espandere **Database**, quindi fare clic con il pulsante destro del mouse su **MyDB1**, puntare ad **Attività** e fare clic su **Backup**.
2. Nella sezione **Origine** mantenere **Tipo backup** impostato su **Completo**. Nella sezione **Destinazione** fare clic su **Rimuovi** per rimuovere il percorso predefinito del file di backup.
3. Nella sezione **Destinazione** fare clic su **Aggiungi**.
4. Nella casella di testo **Nome file** digitare **\\\ContosoSQL1\backup\MyDB1.bak**. Successivamente, fare clic su **OK**, quindi fare di nuovo clic su **OK** per completare l'operazione di backup del database. Una volta completata l'operazione di backup, fare di nuovo clic su **OK** per chiudere la finestra di dialogo.
5. Successivamente, si esegue un backup del log delle transazioni del database. In **Esplora oggetti** espandere **Database**, quindi fare clic con il pulsante destro del mouse su **MyDB1**, puntare ad **Attività** e fare clic su **Backup**.
6. In **Tipo backup** selezionare **Log delle transazioni**. In **Destinazione** lasciare il percorso file impostato su quello specificato in precedenza e fare clic su **OK**. Una volta completata l'operazione di backup, fare di nuovo clic su **OK** .
7. Si procede quindi al ripristino del backup completo e del log delle transazioni in **ContosoSQL2**. Avviare il file RDP per **ContosoSQL2** e accedere come **CORP\Install**. Lasciare aperta la sessione desktop remoto per **ContosoSQL1** .
8. Dal menu **Start** avviare **SQL Server Management Studio**, quindi fare clic su **Connetti** per connettersi all'istanza predefinita di SQL Server.
9. In **Esplora oggetti** fare clic con il pulsante destro del mouse su **Database** e scegliere **Ripristina database**.
10. Nella sezione **Origine** selezionare **Dispositivo** e fare clic sul pulsante **…** .
11. In **Seleziona dispositivi di backup**, fare clic su **Aggiungi**.
12. Nel percorso di file di Backup, digitare \\ContosoSQL1\backup, quindi fare clic su Aggiorna, selezionare MyDB1.bak, quindi fare clic su OK e nuovamente su OK. A questo punto vengono visualizzati il backup completo e il backup del log nel riquadro.
13. Andare alla pagina Opzioni, selezionare RIPRISTINO CON NORECOVERY nello stato del ripristino e fare clic su OK per ripristinare il database. Una volta completata l'operazione di ripristino, fare clic su OK.

### <a name="create-the-availability-group"></a>Creare il gruppo di disponibilità:
1. Tornare alla sessione desktop remoto per **ContosoSQL1**. In **Esplora oggetti** in SSMS fare clic con il pulsante destro del mouse su **Disponibilità elevata AlwaysOn**, quindi fare clic su **Creazione guidata nuovo gruppo di disponibilità**, come mostrato di seguito.
   
    ![Avviare la creazione guidata nuovo gruppo di disponibilità](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665523.gif)
2. Nella pagina **Introduzione** fare clic su **Avanti**. Nella pagina **Specifica nome del gruppo di disponibilità** digitare **AG1** in **Nome gruppo di disponibilità**, quindi fare di nuovo clic su **Avanti**.
   
    ![Creazione guidata nuovo gruppo di disponibilità: specificare il nome di gruppo di disponibilità](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665524.gif)
3. Nella pagina **Seleziona database** selezionare **MyDB1** e fare clic su **Avanti**. Il database soddisfa i prerequisiti per un gruppo di disponibilità in quanto è stato eseguito almeno un backup completo sulla replica primaria usata.
   
    ![Creazione guidata nuovo gruppo di disponibilità: selezionare i database](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665525.gif)
4. Nella pagina **Specifica repliche** fare clic su **Aggiungi replica**.
   
    ![Creazione guidata nuovo gruppo di disponibilità: specificare le repliche](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665526.gif)
5. Viene visualizzata la finestra di dialogo **Connetti al server** . Digitare **ContosoSQL2** in **Nome server**, quindi fare clic su **Connetti**.
   
    ![Creazione guidata nuovo gruppo di disponibilità: connettersi al server](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665527.gif)
6. A questo punto, nella pagina **Specifica repliche** viene visualizzato **ContosoSQL2** elencato in **Repliche disponibili**. Configurare le repliche come mostrato di seguito: Al termine dell'operazione, scegliere **Avanti**.
   
    ![Creazione guidata nuovo gruppo di disponibilità: specificare le repliche (complete)](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665528.gif)
7. Nella pagina **Seleziona sincronizzazione dati iniziale** selezionare **Solo join** e fare clic su **Avanti**. La sincronizzazione dei dati è già stata eseguita manualmente al momento dell'esecuzione dei backup completi e di transazione in **ContosoSQL1** e i dati sono stati ripristinati in **ContosoSQL2**. È invece possibile scegliere di non eseguire le operazioni di backup e ripristino nel database in uso e selezionare **Completo** per consentire l'esecuzione automatica della sincronizzazione dati tramite la Creazione guidata Gruppo di disponibilità. Tuttavia, non è un'operazione consigliata per database di grandi dimensioni presenti in alcune organizzazioni.
   
    ![Creazione guidata nuovo gruppo di disponibilità: selezionare la sincronizzazione dati iniziale](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665529.gif)
8. Nella pagina **Convalida** fare clic su **Avanti**. L'aspetto di questa pagina dovrebbe essere simile a quanto riportato di seguito. È presente un avviso per la configurazione del listener in quanto non è stato configurato un listener del gruppo di disponibilità. È possibile ignorare questo avviso, poiché l'esercitazione non configura alcun listener. Per configurare il listener dopo il completamento dell'esercitazione, vedere [Configurare un listener ILB per gruppi di disponibilità AlwaysOn in Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).
   
    ![Creazione guidata nuovo gruppo di disponibilità: convalida](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665530.gif)
9. Nella pagina **Riepilogo** fare clic su **Fine**, quindi attendere il completamento della configurazione del nuovo gruppo di disponibilità tramite la procedura guidata. Per visualizzare lo stato dettagliato è possibile fare clic su **Altri dettagli** nella pagina **Stato**. Al termine della procedura guidata, controllare la pagina **Risultati** per verificare la corretta creazione del gruppo di disponibilità, come mostrato di seguito, quindi fare clic su **Chiudi** per uscire dalla procedura guidata.
   
    ![Creazione guidata nuovo gruppo di disponibilità: risultati](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665531.gif)
10. In **Esplora oggetti** espandere **Disponibilità elevata AlwaysOn**, quindi espandere **Gruppi di disponibilità**. A questo punto viene visualizzato il nuovo gruppo di disponibilità in questo contenitore. Fare clic con il pulsante destro del mouse su **AG1 (Primary)** e selezionare **Show Dashboard** (Mostra dashboard).
    
     ![Mostrare dashboard gruppo di disponibilità](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665532.gif)
11. L'aspetto del **Dashboard AlwaysOn** sarà simile a quello riportato di seguito. È possibile visualizzare le repliche, la modalità di failover di ciascuna replica e lo stato di sincronizzazione.
    
     ![Dashboard gruppo di disponibilità](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665533.gif)
12. Tornare a **Server Manager**, selezionare **Strumenti**, quindi avviare **Gestione cluster di failover**.
13. Espandere **Cluster1.corp.contoso.com**, quindi espandere **Servizi e applicazioni**. Selezionare **Ruoli**. Si noti che è stato creato il ruolo del gruppo di disponibilità **AG1**. Si noti che AG1 non dispone di indirizzi IP da cui i client del database possono connettersi al gruppo di disponibilità, poiché non è stato configurato un listener. È possibile connettersi direttamente al nodo primario per le operazioni di lettura/scrittura e al nodo secondario per le query di sola lettura.
    
     ![Gruppo di disponibilità in Gestione cluster di failover](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665534.gif)

> [!WARNING]
> Non tentare di eseguire il failover del gruppo di disponibilità da Gestione cluster di failover. È consigliabile eseguire tutte le operazioni di failover nel **Dashboard AlwaysOn** in SSMS. Per altre informazioni, vedere le [restrizioni relative all'uso di Gestione cluster di failover WSFC con i gruppi di disponibilità](https://msdn.microsoft.com/library/ff929171.aspx).
> 
> 

## <a name="next-steps"></a>Passaggi successivi
SQL Server AlwaysOn è stato correttamente implementato mediante la creazione di un gruppo di disponibilità in Azure. Per configurare un listener per questo gruppo di disponibilità, vedere [Configurare un listener ILB per gruppi di disponibilità AlwaysOn in Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

Per altre informazioni sull'uso di SQL Server in Azure, vedere [SQL Server in Macchine virtuali di Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).


