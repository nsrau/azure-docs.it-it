---
title: "Configurare un gruppo di disponibilità AlwaysOn in macchine virtuali di Azure (distribuzione classica) | Microsoft Docs"
description: "Creare un gruppo di disponibilità AlwaysOn con le macchine virtuali di Azure. Questa esercitazione usa principalmente l'interfaccia utente e gli strumenti invece dello scripting."
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
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: b360fe9f28eeb9b10c82fce729165b1b572ac3c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-always-on-availability-group-in-azure-virtual-machines-classic"></a>Configurare un gruppo di disponibilità AlwaysOn in macchine virtuali di Azure (distribuzione classica)
> [!div class="op_single_selector"]
> * [Classica: interfaccia utente](../classic/portal-sql-alwayson-availability-groups.md)
> * [Classica: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Prima di iniziare, considerare che ora è possibile completare questa attività nel modello Azure Resource Manager. Per le nuove distribuzioni è consigliabile usare il modello Azure Resource Manager. Vedere [Panoramica sui gruppi di disponibilità AlwaysOn di SQL Server in macchine virtuali di Azure](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT] 
> Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Azure offre due diversi modelli di distribuzione per creare e usare le risorse, ovvero [Resource Manager e la distribuzione classica](../../../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra come usare il modello di distribuzione classico. 

Per completare questa attività con il modello Azure Resource Manager, vedere [Panoramica sui gruppi di disponibilità AlwaysOn di SQL Server in macchine virtuali di Azure](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

Questa esercitazione end-to-end illustra come implementare i gruppi di disponibilità tramite SQL Server AlwaysOn in esecuzione in macchine virtuali di Azure.

Al termine dell'esercitazione la soluzione SQL Server AlwaysOn in Azure sarà composta dagli elementi seguenti:

* Una rete virtuale contenente più subnet, tra cui una subnet front-end e una back-end
* Un controller di dominio con un dominio di Active Directory (Azure AD)
* Due macchine virtuali che eseguono SQL Server e che sono distribuite alla subnet back-end e aggiunte al dominio di Azure AD
* Un cluster di failover a tre nodi con il modello di quorum Maggioranza dei nodi
* Un gruppo di disponibilità che ha due repliche di commit sincrono di un database di disponibilità

La figura seguente è una rappresentazione grafica della soluzione.

![Architettura di lab di test per gruppi di disponibilità in Azure](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC791912.png)

Si tratta di una possibile configurazione. È possibile, ad esempio, ridurre il numero di macchine virtuali per un gruppo di disponibilità con due repliche al fine di risparmiare ore di calcolo in Azure usando il controller di dominio come condivisione file di controllo del quorum in un cluster a 2 nodi. Questo metodo consente di ridurre di un'unità il numero di macchine virtuali rispetto alla configurazione precedente.

Nell’esercitazione si presuppongono le condizioni seguenti:

* Si dispone già di un account Azure.
* La procedura per il provisioning di una macchina virtuale classica che esegue SQL Server dalla raccolta di macchine virtuali tramite l'interfaccia utente grafica è già nota.
* Si dispone già una conoscenza approfondita dei gruppi di disponibilità AlwaysOn. Per altre informazioni, vedere [Gruppi di disponibilità AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Se si è interessati all'uso di gruppi di disponibilità AlwaysOn con SharePoint, vedere anche [Configurare gruppi di disponibilità AlwaysOn di SQL Server 2012 per SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).
> 
> 

## <a name="create-the-virtual-network-and-domain-controller-server"></a>Creare la rete virtuale e il server del controller di dominio
Si inizia con un nuovo account di prova di Azure. Dopo la configurazione dell'account, viene visualizzata la schermata iniziale del portale di Azure classico.

1. Fare clic sul pulsante **Nuovo** pulsante nell'angolo sinistro della parte inferiore della pagina, come illustrato nella schermata seguente.
   
    ![Fare clic su Nuovo nel portale](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665511.gif)
2. Fare clic su **Servizi di rete** > **Rete virtuale** > **Creazione personalizzata**, come illustrata nella schermata seguente.
   
    ![Crea rete virtuale](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665512.gif)
3. Nella finestra di dialogo **CREA RETE VIRTUALE** creare una nuova rete virtuale avanzando nelle pagine e usando le impostazioni nella tabella seguente. 
   
   | Page | Impostazioni |
   | --- | --- |
   | Dettagli della rete virtuale |**NOME = ContosoNET**<br/>**AREA = Stati Uniti occidentali** |
   | Server DNS e connettività VPN |Nessuno |
   | Spazi di indirizzi della rete virtuale |Le impostazioni vengono illustrate nella schermata seguente: ![Crea rete virtuale](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784620.png) |
4. Creare la macchina virtuale che verrà usata come controller di dominio. Fare clic su **Nuovo** > **Calcola** > **Macchina virtuale** > **Da raccolta**, come illustrata nella schermata seguente.
   
    ![Creare una macchina virtuale](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784621.png)
5. Nella finestra di dialogo **CREA MACCHINA VIRTUALE** configurare una nuova macchina virtuale avanzando nelle pagine con le impostazioni seguenti. 
   
   | Page | Impostazioni |
   | --- | --- |
   | Selezionare il sistema operativo della macchina virtuale |Windows Server 2012 R2 Datacenter |
   | Configurazione macchina virtuale |**DATA DI RILASCIO VERSIONE** = (più recente)<br/>**NOME MACCHINA VIRTUALE** = ContosoDC<br/>**PIANO** = STANDARD<br/>**DIMENSIONI** = A2 (2 core)<br/>**NUOVO NOME UTENTE** = AzureAdmin<br/>**NUOVA PASSWORD** = Contoso!000<br/>**CONFERMA** = Contoso!000 |
   | Configurazione macchina virtuale |**SERVIZIO CLOUD** = creare un nuovo servizio cloud<br/>**NOME DNS DEL SERVIZIO CLOUD** = nome univoco del servizio cloud<br/>**NOME DNS** = nome univoco (ad esempio: ContosoDC123)<br/>**AREA/GRUPPO DI AFFINITÀ/RETE VIRTUALE** = ContosoNET<br/>**SUNET RETE VIRTUALE** = Back(10.10.2.0/24)<br/>**ACCOUNT DI ARCHIVIAZIONE**: usare un account di archiviazione generato automaticamente<br/>**SET DI DISPONIBILITÀ** = (Nessuno) |
   | Opzioni macchina virtuale |Valori predefiniti |

Al termine della configurazione della nuova macchina virtuale, attendere l'esecuzione del relativo provisioning. L'esecuzione di questo processo richiede tempo. Se si fa clic sulla scheda **Macchina virtuale** nel portale di Azure classico, è possibile visualizzare il passaggio degli stati di ContosoDC da **Starting (Provisioning)** ad **Arrestato**, **Avvio in corso**, **Running (Provisioning)** e infine **In esecuzione**.

A questo punto, il provisioning del server del controller di dominio è completato. Si configurerà quindi il dominio di Active Directory nel server del controller di dominio.

## <a name="configure-the-domain-controller"></a>Configurare il controller di dominio
Nei seguenti passaggi viene configurata la macchina virtuale ContosoDC come controller di dominio per corp.contoso.com.

1. Nel portale selezionare la macchina **ContosoDC** . Nella scheda **Dashboard** fare clic su **Connetti** per aprire un file RDP per l'accesso desktop remoto.
   
    ![Connettersi alla macchina virtuale](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784622.png)
2. Accedere con l'account amministratore (**\AzureAdmin**) e la password (**Contoso!000**) configurati.
3. Per impostazione predefinita, verrà visualizzato il dashboard **Server Manager** .
4. Fare clic sul collegamento **Aggiungi ruoli e funzionalità** sul dashboard.
   
    ![Aggiunta ruoli Esplora server](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784623.png)
5. Fare clic su **Avanti** fino a visualizzare la sezione **Ruoli server**.
6. Selezionare i ruoli **Active Directory Domain Services** e **Server DNS**. Quando richiesto, aggiungere altre funzionalità richieste da questi ruoli.
   
   > [!NOTE]
   > Verrà visualizzato un avviso di convalida che indica che non esiste alcun indirizzo IP statico. Se si esegue il test della configurazione, fare clic su **Continua**. Per scenari di produzione, [usare PowerShell per impostare l'indirizzo IP statico della macchina virtuale controller di dominio](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   > 
   > 
   
    ![Finestra di dialogo Aggiungi ruoli](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784624.png)
7. Fare clic su **Avanti** fino a raggiungere la sezione **Conferma**. Selezionare la casella di controllo **Riavvia automaticamente il server di destinazione se necessario**.
8. Fare clic su **Installa**.
9. Dopo aver installato le funzionalità, tornare al dashboard **Server Manager**.
10. Selezionare la nuova opzione **Servizi di dominio di Active Directory** nel riquadro a sinistra.
11. Fare clic sul collegamento **Altro** sulla barra di avviso gialla.
    
     ![Finestra di dialogo Servizi di dominio di Active Directory nella macchina virtuale del server DNS](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784625.png)
12. Nella colonna **Azione** della finestra di dialogo **Dettagli attività tutti i server** fare clic su **Alza di livello il server a controller di dominio**.
13. Nella **Configurazione guidata Servizi di dominio di Active Directory**, usare i seguenti valori:
    
    | Page | Impostazione |
    | --- | --- |
    | Configurazione distribuzione |**Aggiungi una nuova foresta** = selezionata<br/>**Nome di dominio radice** = corp.contoso.com |
    | Opzioni controller di dominio |**Password** = Contoso!000<br/>**Conferma password** = Contoso!000 |
14. Fare clic su **Avanti** per procedere nelle altre pagine della procedura guidata. Nella pagina **Controllo dei prerequisiti** verificare che venga visualizzato il seguente messaggio: **Tutti i controlli dei prerequisiti sono riusciti**. Si noti che è opportuno leggere tutti i messaggi di avviso applicabili, ma che è possibile continuare con l'installazione.
15. Fare clic su **Installa**. La macchina virtuale **ContosoDC** si riavvierà automaticamente.

## <a name="configure-domain-accounts"></a>Configurare gli account di dominio
Nei seguenti passaggi vengono configurati gli account Active Directory per un uso successivo.

1. Accedere nuovamente alla macchina **ContosoDC**.
2. In **Server Manager** fare clic su **Strumenti** > **Centro di amministrazione di Active Directory**.
   
    ![Centro di amministrazione di Active Directory](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784626.png)
3. Nel **Centro di amministrazione di Active Directory** selezionare **corp (local)** nel riquadro di sinistra.
4. Nel riquadro **Attività** a destra fare clic su **Nuovo** > **Utente**. Usare le seguenti impostazioni:
   
   | Impostazione | Valore |
   | --- | --- |
   | **Nome** |Installa |
   | **Utente SamAccountName** |Installa |
   | **Password** |Contoso!000 |
   | **Conferma password** |Contoso!000 |
   | **Altre opzioni password** |Selezionato |
   | **Nessuna scadenza password** |Selezionato |
5. Fare clic su **OK** per creare l'utente **Install**. Questo account verrà usato per configurare il cluster di failover e il gruppo di disponibilità.
6. Creare due utenti aggiuntivi, ovvero **CORP\SQLSvc1** e **CORP\SQLSvc2**, con gli stessi passaggi. Tali account verranno usati per le istanze di SQL Server. A questo punto è necessario assegnare a **CORP\Install** le autorizzazioni necessarie per configurare il clustering di failover di Windows.
7. Nel **Centro di amministrazione di Active Directory** fare clic su **corp (local)** nel riquadro a sinistra. Nel riquadro **Attività** fare clic su **Proprietà**.
   
    ![Proprietà utente CORP](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784627.png)
8. Selezionare **Estensioni**, quindi fare clic sul pulsante **Avanzate** nella scheda **Sicurezza**.
9. Nella finestra di dialogo **Impostazioni avanzate di sicurezza per corp** fare clic su **Aggiungi**.
10. Fare clic su **Seleziona un'entità**, cercare **CORP\Install** e quindi fare clic su **OK**.
11. Selezionare le autorizzazioni **Leggi tutte le proprietà** e **Create Computer objects** (Crea oggetti computer).
    
     ![Autorizzazioni utente Corp](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784628.png)
12. Fare clic su **OK** e quindi di nuovo clic su **OK**. Chiudere la finestra delle proprietà di corp.

Al termine della configurazione di Active Directory e degli oggetti utente, si procederà alla creazione di tre macchine virtuali di SQL Server che verranno aggiunte al dominio.

## <a name="create-the-sql-server-virtual-machines"></a>Creare le macchine virtuali di SQL Server
Creare tre macchine virtuali, una per un nodo del cluster e due per SQL Server. Per creare ogni macchina virtuale, tornare al portale di Azure classico, fare clic su **Nuovo** > **Calcola** > **Macchina virtuale** > **Da raccolta**. Usare quindi i modelli indicati nella tabella seguente tabella per creare le macchine virtuali.

| Page | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Selezionare il sistema operativo della macchina virtuale |**Windows Server 2012 R2 Datacenter** |**SQL Server 2014 RTM Enterprise** |**SQL Server 2014 RTM Enterprise** |
| Configurazione macchina virtuale |**DATA DI RILASCIO VERSIONE** = (più recente)<br/>**NOME MACCHINA VIRTUALE** = ContosoWSFCNode<br/>**PIANO** = STANDARD<br/>**DIMENSIONI** = A2 (2 core)<br/>**NUOVO NOME UTENTE** = AzureAdmin<br/>**NUOVA PASSWORD** = Contoso!000<br/>**CONFERMA** = Contoso!000 |**DATA DI RILASCIO VERSIONE** = (più recente)<br/>**NOME MACCHINA VIRTUALE** = ContosoSQL1<br/>**PIANO** = STANDARD<br/>**DIMENSIONI** = A3 (4 core)<br/>**NUOVO NOME UTENTE** = AzureAdmin<br/>**NUOVA PASSWORD** = Contoso!000<br/>**CONFERMA** = Contoso!000 |**DATA DI RILASCIO VERSIONE** = (più recente)<br/>**NOME MACCHINA VIRTUALE** = ContosoSQL2<br/>**PIANO** = STANDARD<br/>**DIMENSIONI** = A3 (4 core)<br/>**NUOVO NOME UTENTE** = AzureAdmin<br/>**NUOVA PASSWORD** = Contoso!000<br/>**CONFERMA** = Contoso!000 |
| Configurazione macchina virtuale |**SERVIZIO CLOUD** = nome DNS univoco del servizio cloud creato in precedenza (ad esempio: ContosoDC123)<br/>**AREA/GRUPPO DI AFFINITÀ/RETE VIRTUALE** = ContosoNET<br/>**SUNET RETE VIRTUALE** = Back(10.10.2.0/24)<br/>**ACCOUNT DI ARCHIVIAZIONE**: usare un account di archiviazione generato automaticamente<br/>**SETDI DISPONIBILITÀ** = creare un set di disponibilità<br/>**NOME SET DI DISPONIBILITÀ** = SQLHADR |**SERVIZIO CLOUD** = nome DNS univoco del servizio cloud creato in precedenza (ad esempio: ContosoDC123)<br/>**AREA/GRUPPO DI AFFINITÀ/RETE VIRTUALE** = ContosoNET<br/>**SUNET RETE VIRTUALE** = Back(10.10.2.0/24)<br/>**ACCOUNT DI ARCHIVIAZIONE**: usare un account di archiviazione generato automaticamente<br/>**SET DI DISPONIBILITÀ** = SQLHADR (è anche possibile configurare il set di disponibilità dopo aver creato la macchina. Tutte e tre le macchine virtuali devono essere assegnate al set di disponibilità SQLHADR. |**SERVIZIO CLOUD** = nome DNS univoco del servizio cloud creato in precedenza (ad esempio: ContosoDC123)<br/>**AREA/GRUPPO DI AFFINITÀ/RETE VIRTUALE** = ContosoNET<br/>**SUNET RETE VIRTUALE** = Back(10.10.2.0/24)<br/>**ACCOUNT DI ARCHIVIAZIONE**: usare un account di archiviazione generato automaticamente<br/>**SET DI DISPONIBILITÀ** = SQLHADR (è anche possibile configurare il set di disponibilità dopo aver creato la macchina. Tutte e tre le macchine virtuali devono essere assegnate al set di disponibilità SQLHADR. |
| Opzioni macchina virtuale |Valori predefiniti |Valori predefiniti |Valori predefiniti |

<br/>

> [!NOTE]
> La configurazione precedente suggerisce macchine virtuali di livello STANDARD poiché le macchine di livello BASIC non supportano gli endpoint con carico bilanciato necessari per creare in seguito listener del gruppo di disponibilità. Le dimensioni delle macchine suggerite sono pensate inoltre per il test dei gruppi di disponibilità all'interno di macchine virtuali di Azure. Per ottenere prestazioni ottimali su carichi di lavoro di produzione, vedere le dimensioni e la configurazione consigliate per la macchina SQL Server in [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](../sql/virtual-machines-windows-sql-performance.md).
> 
> 

Dopo aver effettuato il provisioning completo delle tre macchine virtuali, è necessario aggiungerle al dominio **corp.contoso.com** e concedere alle macchine i diritti amministrativi di CORP\Install. A tale scopo, seguire questa procedura per ognuna delle tre macchine virtuali.

1. In primo luogo, modificare l'indirizzo del server DNS preferito. Selezionare la macchina virtuale nell'elenco per scaricare il file RDP relativo e quindi fare clic sul pulsante **Connetti**. Per selezionare una macchina virtuale, fare clic su un punto qualsiasi, ma nella prima cella della riga, come illustrato nella figura seguente.
   
    ![Scaricare il file RDP](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664953.jpg)
2. Aprire il file RDP scaricato e accedere alla macchina virtuale con l'account amministratore (**BUILTIN\AzureAdmin**) e la password (**Contoso!000**) configurati.
3. Dopo l'accesso, verrà visualizzato il dashboard **Server Manager**. Nel riquadro di sinistra fare clic su **Server locale** .
4. Fare clic sul collegamento **Indirizzo IPv4 assegnato da DHCP - Compatibile IPv6** .
5. Nella finestra di dialogo **Connessioni di rete** fare clic sull'icona della rete.
   
    ![Modificare il server DNS preferito della macchina virtuale](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784629.png)
6. Sulla barra dei comandi fare clic su **Cambia impostazioni di connessione**. A seconda delle dimensioni della finestra, potrebbe essere necessario fare clic sulla doppia freccia destra per visualizzare questo comando.
7. Selezionare **Protocollo Internet versione 4 (TCP/IPv4)** e quindi fare clic su **Proprietà**.
8. Selezionare **Utilizza i seguenti indirizzi server DNS** e quindi specificare **10.10.2.4** in **Server DNS preferito**.
9. L'indirizzo **10.10.2.4** è quello assegnato a una macchina virtuale nella subnet 10.10.2.0/24 in una rete virtuale di Azure e la macchina virtuale è **ContosoDC**. Per verificare l'indirizzo IP di **ContosoDC**, digitare **nslookup contosodc** nella finestra del prompt dei comandi, come illustrato nella schermata seguente.
   
    ![Usare NSLOOKUP per trovare l'indirizzo IP per il controller di dominio](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664954.jpg)
10. Fare clic su **OK** > **Chiudi** per salvare le modifiche. A questo punto è possibile aggiungere la macchina virtuale a **corp.contoso.com**.
11. Tornando alla finestra **Server locale**, fare clic sul collegamento **WORKGROUP**.
12. Nella sezione **Nome computer** fare clic su **Cambia**.
13. Selezionare la casella di controllo **Dominio**, digitare **corp.contoso.com** nella casella di testo e quindi fare clic su **OK**.
14. Nella finestra di dialogo **Sicurezza di Windows** specificare le credenziali per l'account amministratore di dominio predefinito (**CORP\AzureAdmin**) e la password (**Contoso!000**).
15. uando viene visualizzato il messaggio di benvenuto nel dominio corp.contoso.com, fare clic su **OK**.
16. Fare clic su **Chiudi** > **Riavvia ora** nella finestra di dialogo.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-virtual-machine"></a>Aggiungere l'utente Corp\Install come amministratore in ogni macchina virtuale
1. Attendere fino al riavvio della macchina virtuale e quindi aprire di nuovo il file RDP per accedere alla macchina virtuale con l'account **BUILTIN\AzureAdmin**.
2. In **Server Manager** fare clic su **Strumenti** > **Gestione computer**.
   
    ![Gestione computer](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784630.png)
3. Nella finestra di dialogo **Gestione computer** espandere **Utenti e gruppi locali** e quindi fare clic su **Gruppi**.
4. Fare doppio clic sul gruppo **Administrators** .
5. Nella finestra di dialogo **Proprietà Administrators** fare clic su pulsante **Aggiungi**.
6. Immettere l'utente **CORP\Install** e quindi fare clic su **OK**. Alla richiesta delle credenziali, usare l'account **AzureAdmin** con la password **Contoso!000**.
7. Fare clic su **OK** per chiudere la finestra di dialogo **Proprietà Administrators**.

### <a name="add-the-failover-clustering-feature-to-each-virtual-machine"></a>Aggiungere la funzionalità Clustering di failover a ogni macchina virtuale
1. Nel dashboard **Server Manager** fare clic su **Aggiungi ruoli e funzionalità**.
2. In **Aggiunta guidata ruoli e funzionalità** fare clic su **Avanti** fino a visualizzare la pagina **Funzionalità**.
3. Selezionare **Clustering di failover**. Quando richiesto, aggiungere altre funzionalità dipendenti.
   
    ![Aggiungere la funzionalità Clustering di failover alla macchina virtuale](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784631.png)
4. Fare clic su **Avanti**, quindi su **Installa** nella pagina **Conferma**.
5. Al termine dell'installazione della funzionalità **Clustering di failover** fare clic su **Chiudi**.
6. Disconnettersi dalla macchina virtuale.
7. Ripetere i passaggi di questa sezione per tutti i tre server, ovvero **ContosoWSFCNode**, **ContosoSQL1** e **ContosoSQL2**.

Completato il provisioning, le macchine virtuali di SQL Server sono in esecuzione, ma ognuna è stata installata con le opzioni predefinite di SQL Server.

## <a name="create-the-failover-cluster"></a>Creare il cluster di failover
In questa sezione si crea il cluster di failover che ospiterà il gruppo di disponibilità creato in seguito. A questo punto dovrebbero essere state completate le operazioni indicate di seguito per ognuna delle tre macchine virtuali che verranno usate nel cluster di failover:

* Provisioning completo in Azure
* Aggiunta della macchina virtuale al dominio
* Aggiunta di **CORP\Install** al gruppo Administrators locale
* Aggiunta della funzionalità Clustering di failover

Per ogni macchina virtuale, tutte queste operazioni sono prerequisiti che devono essere soddisfatti prima di aggiungere la macchina virtuale al cluster di failover.

Si noti inoltre che la rete virtuale di Azure non si comporta nello stesso modo di una rete locale. È necessario creare il cluster nel seguente ordine:

1. Creare un cluster a nodo singolo in uno dei nodi (**ContosoSQL1**).
2. Modificare l'indirizzo IP del cluster in un indirizzo IP non usato (**10.10.2.101**).
3. Portare il nome del cluster online.
4. Aggiungere gli altri nodi (**ContosoSQL2** e **ContosoWSFCNode**).

Seguire questa procedura per completare le attività per configurare completamente il cluster.

1. Aprire il file RDP per **ContosoSQL1** e accedere con l'account di dominio **CORP\Install**.
2. Nel dashborad **Server Manager** fare clic su **Strumenti** > **Gestione cluster di failover**.
3. Nel riquadro a sinistra fare clic con il pulsante destro del mouse su **Gestione cluster di failover** e quindi scegliere **Crea cluster**, come illustrato nella schermata seguente.
   
    ![Creare cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784632.png)
4. Nella Creazione guidata Cluster creare un cluster a un nodo procedendo nelle pagine con le impostazioni della tabella seguente:
   
   | Page | Impostazioni |
   | --- | --- |
   | Prima di iniziare |Valori predefiniti |
   | Selezione dei server |Digitare **ContosoSQL1** in **Immettere il nome del server** e fare clic su **Aggiungi** |
   | Avviso di convalida |Selezionare **No. Non è necessario il supporto di Microsoft per il cluster e pertanto non desidero eseguire i test di convalida. Facendo clic su Avanti verrà proseguita la creazione del cluster**. |
   | Punto di accesso per l'amministrazione del cluster |Digitare **Cluster1** in **Nome cluster** |
   | Conferma |Usare le impostazioni predefinite a meno a meno che non si usino spazi di archiviazione. Vedere l'avviso successivo alla tabella. |
   
   > [!WARNING]
   > Se si usa la funzionalità [Spazi di archiviazione](https://technet.microsoft.com/library/hh831739), che raggruppa più dischi in pool di archiviazione, è necessario deselezionare la casella di controllo **Aggiungi tutte le risorse di archiviazione idonee al cluster** nella pagina **Conferma**. Se non si deseleziona questa opzione, i dischi virtuali vengono disconnessi durante il processo di clustering e di conseguenza non vengono visualizzati in Gestione disco o in Esplora risorse fino a quando gli spazi di archiviazione non vengono rimossi dal cluster e ricollegati tramite PowerShell.
   > 
   > 
5. Nel riquadro a sinistra espandere **Gestione cluster di failover** e quindi fare clic su **Cluster1.corp.contoso.com**.
6. Nel riquadro centrale scorrere verso il basso fino alla sezione **Risorse principali del cluster** ed espandere i dettagli **Nome: Cluster1**. Lo stato visualizzato di entrambe le risorse **Nome** e **Indirizzo IP** deve essere **Operazione non riuscita**. La risorsa Indirizzo IP non può essere portata online poiché al cluster è assegnato lo stesso indirizzo IP della macchina, ovvero si tratta di un indirizzo duplicato.
7. Fare clic con il pulsante destro del mouse sulla risorsa **Indirizzo IP** non riuscita, quindi scegliere **Proprietà**.
   
    ![Proprietà del cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784633.png)
8. Selezionare **Indirizzo IP statico**, specificare **10.10.2.101** nella casella di testo **Indirizzo** e quindi fare clic su **OK**.
9. Nella sezione **Risorse principali del cluster** fare clic con il pulsante destro del mouse su **Nome: Cluster1** e quindi scegliere **Porta online**. Attendere finché entrambe le risorse non siano online. Quando la risorsa del nome cluster torna online, il server del controller di dominio viene aggiornato con un nuovo account del computer Active Directory. Tale account verrà usato per eseguire il servizio del cluster del gruppo di disponibilità in un secondo momento.
10. Aggiungere i rimanenti nodi al cluster. Nella struttura del browser fare clic con il pulsante destro del mouse su **Cluster1.corp.contoso.com** e quindi scegliere **Aggiungi nodo** come illustrato nella schermata seguente.
    
     ![Aggiungere un nodo al cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784634.png)
11. In **Aggiunta guidata nodi** fare clic su **Avanti**. Nella pagina **Selezione dei server** aggiungere **ContosoSQL2** e **ContosoWSFCNode** all'elenco digitando il nome del server in **Immettere il nome del server** e quindi facendo clic su **Aggiungi**. Al termine dell'operazione, scegliere **Avanti**.
12. Nella pagina **Avviso di convalida** fare clic su **No**. In uno scenario di produzione è necessario tuttavia eseguire i test di convalida. Quindi fare clic su **Next**.
13. Nella pagina **Conferma** fare clic su **Avanti** per aggiungere i nodi.
    
    > [!WARNING]
    > Se si usa la funzionalità [Spazi di archiviazione](https://technet.microsoft.com/library/hh831739), che raggruppa più dischi in pool di archiviazione, è necessario deselezionare la casella di controllo **Aggiungi tutte le risorse di archiviazione idonee al cluster** . Se non si deseleziona questa opzione, i dischi virtuali vengono disconnessi durante il processo di clustering Di conseguenza, non vengono visualizzati in Gestione disco o in Esplora risorse fino a quando gli spazi di archiviazione non vengono rimossi dal cluster e ricollegati tramite PowerShell.
    > 
    > 
14. Dopo aver aggiunto i nodi al cluster, fare clic su **Fine**. A questo punto, Gestione cluster di failover visualizza il cluster con i tre nodi elencati nel contenitore **Nodi** .
15. Disconnettersi dalla sessione desktop remoto.

## <a name="prepare-the-sql-server-instances-for-availability-groups"></a>Preparare le istanze di SQL Server per i gruppi di disponibilità
In questa sezione verranno effettuate le azioni seguenti sia in **ContosoSQL1** che in **contosoSQL2**:

* Aggiungere un account di accesso per **NT AUTHORITY\System** con le autorizzazioni necessarie impostate all'istanza predefinita di SQL Server.
* Aggiungere **CORP\Install** come ruolo sysadmin all'istanza predefinita di SQL Server.
* Aprire il firewall per l'accesso remoto di SQL Server.
* Abilitare la funzionalità Gruppi di disponibilità AlwaysOn.
* Impostare l'account del servizio SQL Server su **CORP\SQLSvc1** e **CORP\SQLSvc2** rispettivamente.

Queste azioni possono essere eseguite in qualsiasi ordine. I passaggi seguenti vengono tuttavia presentati in ordine. Seguire questa procedura sia per **ContosoSQL1**, sia per **ContosoSQL2**:

1. Disconnettersi dalla sessione desktop remoto, se non è ancora stata eseguita questa operazione.
2. Aprire i file RDP per **ContosoSQL1** e **ContosoSQL2** e accedere come **BUILTIN\AzureAdmin**.
3. Aggiungere prima **NT AUTHORITY\System** agli account di accesso di SQL Server con le autorizzazioni necessarie. Aprire **SQL Server Management Studio**.
4. Fare clic su **Connetti** per connettersi all'istanza predefinita di SQL Server.
5. In **Esplora oggetti**, espandere **Sicurezza**, quindi espandere **Account di accesso**.
6. Fare clic con il pulsante destro del mouse sull'account di accesso **NT AUTHORITY\System** e quindi scegliere **Proprietà**.
7. Nella pagina **Entità a protezione diretta** per il server locale selezionare **Concedi** per le autorizzazioni seguenti e quindi fare clic su **OK**.
   
   * Alterare eventuali gruppi di disponibilità
   * Connettersi a SQL
   * Visualizzare lo stato del server
8. Aggiungere **CORP\Install** come ruolo **sysadmin** all'istanza di SQL Server predefinita. In **Esplora oggetti** fare clic con il pulsante destro del mouse su **Account di accesso** e quindi scegliere **Nuovo account di accesso**.
9. Digitare **CORP\Install** in **Nome account di accesso**.
10. Nella pagina **Ruoli server** fare clic su **sysadmin** e quindi fare clic su **OK**. Dopo aver creato l'account di accesso, è possibile visualizzarlo espandendo **Account di accesso** in **Esplora oggetti**.
11. Per creare una regola del firewall per SQL Server, nella schermata **Start** aprire **Windows Firewall con sicurezza avanzata**.
12. Nel riquadro sinistro selezionare **Regole connessioni in entrata**. Nel riquadro a destra fare clic su **Nuova regola**.
13. Nella pagina **Tipo di regola** fare clic su **Programma** > **Avanti**.
14. Nella pagina **Programmi** selezionare **Percorso programma**, digitare **%ProgramFiles%\Microsoft SQL Server\MSSQL12.MSSQLSERVER\MSSQL\Binn\sqlservr.exe** nella casella di testo e quindi fare clic su **Avanti**. Se si seguono queste istruzioni, ma si usa SQL Server 2012, la directory di SQL Server è **MSSQL11. MSSQLSERVER**.
15. Nella pagina **Azione** mantenere selezionata l'opzione **Consenti la connessione** e quindi fare clic su **Avanti**.
16. Nella pagina **Profilo** accettare le impostazioni predefinite e quindi fare clic su **Avanti**.
17. Nella pagina **Nome** specificare un nome per la regola, ad esempio **SQL Server (Regola di programma)** nella casella di testo **Nome** e quindi fare clic su **Fine**.
18. Per abilitare la funzionalità **Gruppi di disponibilità AlwaysOn**, nella schermata **Start** aprire **Gestione configurazione SQL Server**.
19. Nella struttura del browser fare clic su **Servizi di SQL Server**, fare clic con il pulsante destro del mouse sul servizio **SQL Server (MSSQLSERVER)** e quindi scegliere **Proprietà**.
20. Fare clic sulla scheda **Disponibilità elevata AlwaysOn**, selezionare **Abilita gruppi di disponibilità AlwaysOn**, come illustrato nella schermata seguente, e quindi fare clic su **Applica**. Fare clic su **OK** nella finestra di dialogo e non chiudere la finestra di dialogo **Proprietà**. Verrà riavviato il servizio SQL Server dopo la modifica dell'account del servizio.
    
     ![Abilita gruppi di disponibilità AlwaysOn](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665520.gif)
21. Per modificare l'account del servizio SQL Server, fare clic sulla scheda **Accesso**, digitare **CORP\SQLSvc1** (per **ContosoSQL1**) o **CORP\SQLSvc2** (per **ContosoSQL2**) in **Nome account**, inserire e confermare la password e quindi fare clic su **OK**.
22. Nella finestra di dialogo visualizzata fare clic su **Sì** per riavviare il servizio SQL Server. Dopo il riavvio del servizio SQL Server, vengono applicate le modifiche apportate nella finestra di dialogo **Proprietà**.
23. Disconnettersi dalle macchine virtuali.

## <a name="create-the-availability-group"></a>Creare il gruppo di disponibilità
A questo punto, è possibile procedere con la configurazione di un gruppo di disponibilità. Di seguito è riportata una descrizione delle azioni da eseguire:

* Creare un nuovo database (**MyDB1**) in **ContosoSQL1**.
* Eseguire un backup completo e un backup del log delle transazioni del database.
* Ripristinare i backup completi e del log in **ContosoSQL2** con l'opzione **NORECOVERY**.
* Creare il gruppo di disponibilità (**AG1**) con commit sincrono, failover automatico e repliche secondarie leggibili.

### <a name="create-the-mydb1-database-on-contososql1"></a>Creare il database MyDB1 in ContosoSQL1
1. Disconnettersi dalle sessioni desktop remoto per **ContosoSQL1** e **ContosoSQL2**, se questa operazione non è ancora stata eseguita.
2. Aprire il file RDP per **ContosoSQL1** e accedere come **CORP\Install**.
3. In **Esplora file** creare in **C:\\** una directory denominata **backup** che verrà usata per eseguire il backup e il ripristino del database.
4. Fare clic con il pulsante destro del mouse sulla nuova directory, scegliere **Condividi con** e quindi fare clic su **Utenti specifici** come illustrato nella schermata seguente.
   
    ![Creare una cartella di backup](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665521.gif)
5. Aggiungere **CORP\SQLSvc1** e assegnarvi l'autorizzazione **Lettura/Scrittura**. Aggiungere **CORP\SQLSvc2**, assegnarvi l'autorizzazione **Lettura**, come illustrato nella schermata seguente e quindi fare clic su **Condividi**. Al termine del processo di condivisione file, fare clic su **Fatto**.
   
    ![Concedere le autorizzazioni per la cartella di backup](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665522.gif)
6. Per creare il database, dal menu **Start** aprire **SQL Server Management Studio** e quindi fare clic su **Connetti** per connettersi all'istanza di SQL Server predefinita.
7. In **Esplora oggetti** fare clic con il pulsante destro del mouse su **Database** e quindi fare clic su **Nuovo database**.
8. In **Nome database** digitare **MyDB1** e quindi fare clic su **OK**.

### <a name="make-a-full-backup-of-mydb1-and-restore-it-on-contososql2"></a>Eseguire un backup completo di MyDB1 e ripristinarlo in ContosoSQL2
1. Per eseguire un backup completo del database, in **Esplora oggetti** espandere **Database**, fare clic con il pulsante destro del mouse su **MyDB1**, scegliere **Attività** e quindi fare clic su **Backup**.
2. Nella sezione **Origine** mantenere **Tipo backup** impostato su **Completo**. Nella sezione **Destinazione** fare clic su **Rimuovi** per rimuovere il percorso predefinito del file di backup.
3. Nella sezione **Destinazione** fare clic su **Aggiungi**.
4. Nella casella di testo **Nome file** digitare **\\ContosoSQL1\backup\MyDB1.bak**, fare clic su **OK** e quindi fare clic di nuovo su **OK** per eseguire il backup del database. Al termine dell'operazione di backup, fare clic di nuovo su **OK** per chiudere la finestra di dialogo.
5. Per eseguire un backup del log delle transazioni, in **Esplora oggetti** espandere **Database**, fare clic con il pulsante destro del mouse su **MyDB1**, scegliere **Attività** e quindi fare clic su **Backup**.
6. In **Tipo backup** selezionare **Log delle transazioni**. In **Destinazione** lasciare il percorso file impostato su quello specificato in precedenza e quindi fare clic su **OK**. Al termine dell'operazione di backup, fare clic di nuovo su **OK**.
7. Per ripristinare i backup completo e del log delle transazioni in **ContosoSQL2**, aprire il file RDP per **ContosoSQL2** e accedere come **CORP\Install**. Lasciare aperta la sessione desktop remoto per **ContosoSQL1** .
8. Dal menu **Start** aprire **SQL Server Management Studio** e quindi fare clic su **Connetti** per connettersi all'istanza di SQL Server predefinita.
9. In **Esplora oggetti** fare clic con il pulsante destro del mouse su **Database** e quindi scegliere **Ripristina database**.
10. Nella sezione **Origine** selezionare **Dispositivo** e fare clic sul pulsante con i puntini di sospensione **…**. .
11. In **Seleziona dispositivi di backup**, fare clic su **Aggiungi**.
12. In **Percorso file di backup** digitare **\\ContosoSQL1\backup**, fare clic su **Aggiorna**, selezionare **MyDB1.bak**, fare clic su **OK** e quindi fare clic di nuovo su **OK**. A questo punto vengono visualizzati il backup completo e il backup del log nel riquadro **Set di backup da ripristinare**.
13. Andare alla pagina **Opzioni**, selezionare **RIPRISTINO CON NORECOVERY** in **Stato di ripristino** e quindi fare clic su **OK** per ripristinare il database. Al termine dell'operazione di ripristino, fare clic su **OK**.

### <a name="create-the-availability-group"></a>Creare il gruppo di disponibilità
1. Tornare alla sessione desktop remoto per **ContosoSQL1**. In **Esplora oggetti** in SQL Server Management Studio fare doppio clic su **Disponibilità elevata AlwaysOn** e quindi fare clic su **Creazione guidata gruppo di disponibilità**, come illustrato nella schermata seguente.
   
    ![Avviare la Creazione guidata del nuovo gruppo di disponibilità](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665523.gif)
2. Nella pagina **Introduzione** fare clic su **Avanti**. Nella pagina **Specifica nome del gruppo di disponibilità** digitare **AG1** in **Nome gruppo di disponibilità** e quindi fare di nuovo clic su **Avanti**.
   
    ![Creazione guidata del nuovo gruppo di disponibilità: specifica del nome del gruppo di disponibilità](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665524.gif)
3. Nella pagina **Seleziona database** selezionare **MyDB1** e quindi fare clic su **Avanti**. Il database soddisfa i prerequisiti per un gruppo di disponibilità in quanto è stato eseguito almeno un backup completo sulla replica primaria usata.
   
    ![Creazione guidata del nuovo gruppo di disponibilità: selezione dei database](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665525.gif)
4. Nella pagina **Specifica repliche** fare clic su **Aggiungi replica**.
   
    ![Creazione guidata del nuovo gruppo di disponibilità: specifica delle repliche](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665526.gif)
5. Nelle finestra di dialogo **Connetti al server** digitare **ContosoSQL2** in **Nome server** e quindi fare clic su **Connetti**.
   
    ![Creazione guidata del nuovo gruppo di disponibilità: connessione al server](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665527.gif)
6. Nella pagina **Specifica repliche** viene visualizzato **ContosoSQL2** nell'elenco **Repliche disponibili**. Configurare le repliche come illustrato nella schermata seguente. Al termine dell'operazione, scegliere **Avanti**.
   
    ![Creazione guidata del nuovo gruppo di disponibilità: specifica delle repliche (completata)](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665528.gif)
7. Nella pagina **Seleziona sincronizzazione dati iniziale** selezionare **Solo join** e quindi fare clic su **Avanti**. La sincronizzazione dei dati è già stata eseguita manualmente al momento dell'esecuzione dei backup completi e di transazione in **ContosoSQL1** e del ripristino dei dati in **ContosoSQL2**. È possibile scegliere di non eseguire le operazioni di backup e ripristino nel database in uso e selezionare **Completo** per consentire l'esecuzione automatica della sincronizzazione dati tramite la Creazione guidata gruppo di disponibilità. Questa opzione, tuttavia, non è consigliata per i database di dimensioni molto grandi presenti in alcune organizzazioni.
   
    ![Creazione guidata del nuovo gruppo di disponibilità: selezione della sincronizzazione dei dati iniziale](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665529.gif)
8. Nella pagina **Convalida** fare clic su **Avanti**. La pagina dovrebbe essere simile alla schermata seguente. È presente un avviso per la configurazione del listener in quanto non è stato configurato un listener del gruppo di disponibilità. È possibile ignorare questo avviso, poiché l'esercitazione non configura alcun listener. Per configurare il listener dopo il completamento dell'esercitazione, vedere [Configurare un listener ILB per gruppi di disponibilità AlwaysOn in Azure](../classic/ps-sql-int-listener.md).
   
    ![Creazione guidata del nuovo gruppo di disponibilità: convalida](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665530.gif)
9. Nella pagina **Riepilogo** fare clic su **Fine** e quindi attendere il completamento della configurazione del nuovo gruppo di disponibilità tramite la procedura guidata. Per visualizzare lo stato di avanzamento dettagliato, fare clic su **Altri dettagli** nella pagina **Stato**. Al termine della procedura guidata, controllare la pagina **Risultati** per verificare la corretta creazione del gruppo di disponibilità, come illustrato nella schermata seguente, e quindi fare clic su **Chiudi** per uscire dalla procedura guidata.
   
    ![Creazione guidata del nuovo gruppo di disponibilità: risultati](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665531.gif)
10. In **Esplora oggetti** espandere **Disponibilità elevata AlwaysOn** e quindi espandere **Gruppi di disponibilità**. A questo punto viene visualizzato il nuovo gruppo di disponibilità in questo contenitore. Fare clic con il pulsante destro del mouse su **AG1 (Primary)** (AG1 (Primario)) e selezionare **Show Dashboard** (Mostra dashboard).
    
     ![Visualizzare il dashboard del gruppo di disponibilità](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665532.gif)
11. L'aspetto di **Dashboard** deve essere analogo a quello illustrato nella schermata seguente. È possibile visualizzare le repliche, la modalità di failover di ogni replica e lo stato di sincronizzazione.
    
     ![Dashboard del gruppo di disponibilità](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665533.gif)
12. Tornare a **Server Manager**, fare clic su **Strumenti** e quindi aprire **Gestione cluster di failover**.
13. Espandere **Cluster1.corp.contoso.com**, quindi espandere **Servizi e applicazioni**. Selezionare **Ruoli**. Si noti che è stato creato il ruolo del gruppo di disponibilità **AG1**. Si noti che AG1 non dispone di alcun indirizzo IP da cui i client del database possono connettersi al gruppo di disponibilità poiché non è stato configurato un listener. È possibile connettersi direttamente al nodo primario per le operazioni di lettura/scrittura e al nodo secondario per le query di sola lettura.
    
     ![Gruppo di disponibilità in Gestione cluster di failover](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665534.gif)

> [!WARNING]
> Non tentare di eseguire il failover del gruppo di disponibilità da Gestione cluster di failover. Tutte le operazioni di failover devono essere eseguite in **Dashboard AlwaysOn** in SQL Server Management Studio. Per altre informazioni, vedere [Restrictions on Using The Failover Cluster Manager with Availability Groups](https://msdn.microsoft.com/library/ff929171.aspx) (Limitazioni sull'uso di Gestione cluster di failover con i gruppi di disponibilità).
> 
> 

## <a name="next-steps"></a>Passaggi successivi
SQL Server AlwaysOn è stato correttamente implementato mediante la creazione di un gruppo di disponibilità in Azure. Per configurare un listener per questo gruppo di disponibilità, vedere [Configurare un listener ILB per gruppi di disponibilità Always On in Azure](../classic/ps-sql-int-listener.md).

Per altre informazioni sull'uso di SQL Server in Azure, vedere [SQL Server in Macchine virtuali di Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

