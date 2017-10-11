---
title: "Creare un listener per i gruppi di disponibilità di SQL Server nelle macchine virtuali di Azure | Microsoft Docs"
description: "Istruzioni dettagliate per creare un listener per un gruppo di disponibilità Always On per SQL Server nelle macchine virtuali di Azure"
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/01/2017
ms.author: mikeray
ms.openlocfilehash: 09fed7e785708d4afe64905de973becc188181d7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="configure-a-load-balancer-for-an-always-on-availability-group-in-azure"></a>Configurare un servizio di bilanciamento del carico interno per un gruppo di disponibilità Always On in Azure
In questo articolo viene illustrato come creare un servizio di bilanciamento del carico per un gruppo di disponibilità SQL Server Always On nelle macchine virtuali in esecuzione con Azure Resource Manager. Un gruppo di disponibilità richiede un servizio di bilanciamento del carico quando le istanze di SQL Server sono in macchine virtuali di Azure. Il servizio di bilanciamento del carico archivia l'indirizzo IP per il listener del gruppo di disponibilità. Se un gruppo di disponibilità si estende su più aree, è necessario un servizio di bilanciamento del carico per ogni area.

Per completare questa attività, è necessario disporre di un gruppo di disponibilità di SQL Server distribuito nelle macchine virtuali di Azure in esecuzione con Resource Manager. Entrambe le macchine virtuali di SQL Server devono appartenere allo stesso set di disponibilità. È possibile usare il [modello di Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) per creare automaticamente il gruppo di disponibilità in Resource Manager. Questo modello crea automaticamente un servizio di bilanciamento del carico interno. 

Se si preferisce, è possibile [configurare manualmente un gruppo di disponibilità](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Per questo articolo è necessario che i gruppi di disponibilità siano già configurati.  

Gli argomenti correlati includono:

* [Configurare i gruppi di disponibilità Always On in una macchina virtuale di Azure, GUI](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
* [Configurare una connessione da VNet a VNet tramite Azure Resource Manager e PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Completando questo articolo si creerà e configurerà un servizio di bilanciamento del carico nel portale di Azure. Al termine del processo, si configurerà il cluster per usare l'indirizzo IP dal servizio di bilanciamento del carico per il listener del gruppo di disponibilità.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Creare e configurare il servizio di bilanciamento del carico nel portale di Azure
In questa parte dell'attività eseguire questi passaggi:

1. Nel portale di Azure creare il servizio di bilanciamento del carico e configurare l'indirizzo IP.
2. Configurare il pool back-end.
3. Creare il probe. 
4. Impostare le regole di bilanciamento del carico.

> [!NOTE]
> Se le istanze di SQL Server si trova in diverse aree e gruppi di risorse, eseguire ogni passaggio due volte, una volta per ogni gruppo di risorse.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Passaggio1: Creare il servizio di bilanciamento del carico e configurare l'indirizzo IP
Per prima cosa creare il servizio di bilanciamento del carico. 

1. Nel portale di Azure aprire il gruppo di risorse contenente le macchine virtuali di SQL Server. 

2. Nel gruppo di risorse fare clic su **Aggiungi**.

3. Nei risultati della ricerca cercare il **bilanciamento del carico** e selezionare **Servizio di bilanciamento del carico**, pubblicato da **Microsoft**.

4. Nel pannello **Servizio di bilanciamento del carico** fare clic su**Crea**.

5. Bella finestra di dialogo **Crea servizio di bilanciamento del carico** configurare il servizio di bilanciamento del carico come segue:

   | Impostazione | Valore |
   | --- | --- |
   | **Nome** |Nome che rappresenta il servizio di bilanciamento del carico. Ad esempio **sqlLB**. |
   | **Tipo** |**Interno**: la maggior parte delle implementazioni usano un servizio di bilanciamento del carico interno, che consente alle applicazioni all'interno della stessa rete virtuale di connettersi al gruppo di disponibilità.  </br> **Esterno**: consente alle applicazioni di connettersi al gruppo di disponibilità tramite una connessione Internet pubblica. |
   | **Rete virtuale** |Selezionare la rete virtuale in cui si trovano le istanze di SQL Server. |
   | **Subnet** |Selezionare la subnet in cui si trovano le istanze di SQL Server. |
   | **Assegnazione indirizzi IP** |**Statico** |
   | **Indirizzo IP privato** |Specificare un indirizzo IP disponibile della subnet. che verrà usato quando si creerà un listener nel cluster. Più avanti in questo articolo si userà questo indirizzo per la variabile `$ILBIP` in uno script di PowerShell. |
   | **Sottoscrizione** |Se si hanno più sottoscrizioni, può essere visualizzato questo campo. Selezionare la sottoscrizione da associare a questa risorsa. In genere è la stessa sottoscrizione di tutte le risorse del gruppo di disponibilità. |
   | **Gruppo di risorse** |Selezionare il gruppo di risorse in cui si trovano le istanze di SQL Server. |
   | **Posizione** |Selezionare il percorso di Azure in cui si trovano le istanze di SQL Server. |

6. Fare clic su **Crea**. 

Azure crea il servizio di bilanciamento del carico. Il servizio di bilanciamento del carico appartiene a una rete, a una subnet, a un gruppo di risorse e a una località specifici. Quando Azure termina l'attività, verificare le impostazioni del servizio di bilanciamento del carico in Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Passaggio 2: Configurare il pool back-end
In Azure il pool di indirizzi back-end è chiamato *pool back-end*. In questo caso, il pool back-end è costituito dagli indirizzi delle due istanze di SQL Server nel gruppo di disponibilità. 

1. Nel gruppo di risorse fare clic sul servizio di bilanciamento del carico creato. 

2. In **Impostazioni** fare clic su **Pool back-end**.

3. In **Pool back-end** fare clic su **Aggiungi** per creare un pool di indirizzi back-end. 

4. In **Aggiungi pool back-end**, in **Nome** digitare un nome per il pool back-end.

5. In **Macchine virtuali** fare clic su **Aggiungi una macchina virtuale**. 

6. In **Scegli macchine virtuali** fare clic su **Scegli un set di disponibilità** e specificare il set di disponibilità a cui appartengono le macchine virtuali di SQL Server.

7. Dopo aver scelto il set di disponibilità, fare clic su **Scegli le macchine virtuali**, selezionare le due macchine virtuali che ospitano le istanze di SQL Server nel gruppo di disponibilità e quindi fare clic su **Seleziona**. 

8. Fare clic su **OK** per chiudere i pannelli per **Scegliere le macchine virtuali** e **Aggiungi pool back-end**. 

Azure aggiorna le impostazioni per il pool di indirizzi back-end. Il set di disponibilità ora include un pool di due istanze di SQL Server.

### <a name="step-3-create-a-probe"></a>Passaggio 3: Creare un probe
Il probe definisce come Azure deve verificare quali istanze di SQL Server sono attualmente proprietarie del listener del gruppo di disponibilità. Azure esamina il servizio in base all'indirizzo IP su una porta definita quando si crea il probe.

1. Nel pannello **Impostazioni** del servizio di bilanciamento del carico fare clic su **Probe integrità**. 

2. Nel pannello **Probe integrità** fare clic su **Aggiungi**.

3. Configurare il probe nel pannello **Aggiungi probe** . Usare i valori seguenti per configurare il probe.

   | Impostazione | Valore |
   | --- | --- |
   | **Nome** |Nome che rappresenta il probe. Ad esempio **SQLAlwaysOnEndPointProbe**. |
   | **Protocollo** |**TCP** |
   | **Porta** |È possibile usare qualsiasi porta disponibile. Ad esempio *59999*. |
   | **Interval** |*5* |
   | **Soglia non integra** |*2* |

4.  Fare clic su **OK**. 

> [!NOTE]
> Verificare che la porta specificata sia aperta nel firewall di entrambe le istanze di SQL Server. Per entrambe le istanze è necessaria una regola in ingresso per la porta TCP usata. Per altre informazioni, vedere [Aggiungere o modificare una regola del firewall](http://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

Azure crea il probe e lo usa per verificare quale istanza di SQL Server possieda il listener per il gruppo di disponibilità.

### <a name="step-4-set-the-load-balancing-rules"></a>Passaggio 4: Impostare le regole di bilanciamento del carico
Le regole di bilanciamento del carico determinano come il servizio di bilanciamento del carico instrada il traffico alle istanze di SQL Server. Per questo servizio di bilanciamento del carico abilitare Direct Server Return perché solo una per volta delle due istanze di SQL Server è proprietaria della risorsa listener del gruppo di disponibilità.

1. Nel pannello **Impostazioni** del servizio di bilanciamento del carico fare clic su **Regole di bilanciamento del carico**. 

2. Nel pannello **Regole di bilanciamento del carico** fare clic su **Aggiungi**.

3. Nel pannello **Aggiungi regola di bilanciamento del carico** configurare la regola di bilanciamento del carico. Usare le seguenti impostazioni: 

   | Impostazione | Valore |
   | --- | --- |
   | **Nome** |Nome che rappresenta la regola di bilanciamento del carico. Ad esempio **SQLAlwaysOnEndPointListener**. |
   | **Protocollo** |**TCP** |
   | **Porta** |*1433* |
   | **Porta back-end** |*1433*. Questo valore verrà ignorato perché questa regola usa **IP mobile (Direct Server Return)**. |
   | **Probe** |Usare il nome del probe creato per questo servizio di bilanciamento del carico. |
   | **Persistenza della sessione** |**Nessuno** |
   | **Timeout di inattività (minuti)** |*4* |
   | **IP mobile (Direct Server Return)** |**Enabled** |

   > [!NOTE]
   > Potrebbe essere necessario scorrere il pannello verso il basso per visualizzare tutte le impostazioni.
   > 

4. Fare clic su **OK**. 
5. Azure configura la regola di bilanciamento del carico. Ora il servizio di bilanciamento del carico è configurato per instradare il traffico all'istanza di SQL Server che ospita il listener per il gruppo di disponibilità. 

A questo punto il gruppo di risorse dispone di un servizio di bilanciamento del carico, che si connette a entrambi i computer SQL Server. Il servizio di bilanciamento del carico contiene anche un indirizzo IP per il listener del gruppo di disponibilità SQL Server Always On in modo che entrambi i computer possano rispondere alle richieste per i gruppi di disponibilità.

> [!NOTE]
> Se le istanze di SQL Server si trovano in due aree separate, ripetere i passaggi nell'altra area. Ogni area richiede un servizio di bilanciamento del carico. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configurare il cluster per usare l'indirizzo IP del servizio di bilanciamento del carico
Il passaggio successivo consiste nel configurare il listener nel cluster e nel portare il listener online. Eseguire le operazioni seguenti: 

1. Creare il listener del gruppo di disponibilità nel cluster di failover. 

2. Portare online il listener.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Passaggio 5: Creare il listener del gruppo di disponibilità nel cluster di failover
In questo passaggio si creerà manualmente il listener del gruppo di disponibilità in Gestione cluster di failover e SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>Verificare la configurazione del listener

Se le risorse del cluster e le dipendenze sono configurate correttamente, dovrebbe essere visualizzato il listener in SQL Server Management Studio. Per impostare la porta del listener, procedere come segue:

1. Avviare SQL Server Management Studio e connettersi alla replica primaria.

2. Passare a **Disponibilità elevata AlwaysOn** > **Gruppi di disponibilità** > **Listener gruppo di disponibilità**.  
    Viene visualizzato il nome del listener creato in Gestione Cluster di Failover. 

3. Fare clic con il pulsante destro del mouse sul nome del listener e quindi su **Proprietà**.

4. Nella casella **Porta** specificare il numero di porta per il listener del gruppo di disponibilità usando il valore $EndpointPort usato in precedenza, l'impostazione predefinita era 1433, quindi fare clic su **OK**.

Ora si ha un gruppo di disponibilità nelle macchine virtuali di Azure in esecuzione in modalità Resource Manager. 

## <a name="test-the-connection-to-the-listener"></a>Testare la connessione al listener
Verificare la connessione effettuando le operazioni seguenti:

1. Usare RDP per connettersi a un'istanza di SQL Server che si trova nella stessa rete virtuale, ma non è proprietaria della replica. Può trattarsi dell'altra istanza di SQL Server nel cluster.

2. Usare l'utilità **sqlcmd** per testare la connessione. Lo script seguente, ad esempio, stabilisce una connessione **sqlcmd** alla replica primaria tramite il listener con l'autenticazione di Windows:
   
        sqlcmd -S <listenerName> -E

La connessione SQLCMD si connette automaticamente all'istanza di SQL Server che ospita la replica primaria. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Creare un indirizzo IP per un gruppo di disponibilità aggiuntivo

Ogni gruppo di disponibilità usa un listener diverso. Ogni listener ha un proprio indirizzo IP. Usare lo stesso bilanciamento del carico per contenere l'indirizzo IP per altri listener. Dopo aver creato un gruppo di disponibilità, aggiungere l'indirizzo IP al bilanciamento del carico e quindi configurare il listener.

Per aggiungere un indirizzo IP a un bilanciamento del carico con il portale di Azure, completare i passaggi seguenti:

1. Nel portale di Azure aprire il gruppo di risorse che contiene il bilanciamento del carico e fare clic sul bilanciamento del carico. 

2. In **IMPOSTAZIONI** fare clic su **Pool di indirizzi IP front-end** e quindi su **Aggiungi**. 

3. In **Aggiungi indirizzo IP front-end** assegnare un nome per il front-end. 

4. Verificare che **Rete virtuale** e **Subnet** siano le stesse delle istanze di SQL Server.

5. Impostare l'indirizzo IP per il listener. 
   
   >[!TIP]
   >È possibile impostare l'indirizzo IP come statico e digitare un indirizzo che non sia attualmente utilizzato nella subnet. In alternativa è possibile impostare l'indirizzo IP come dinamico e salvare il nuovo pool di indirizzi IP front-end. In questo caso il portale di Azure assegna automaticamente un indirizzo IP disponibile al pool. È quindi possibile riaprire il pool di indirizzi IP front-end e modificare l'assegnazione in statico. 

6. Salvare l'indirizzo IP per il listener. 

7. Aggiungere un probe integrità usando le impostazioni seguenti:

   |Impostazione |Valore
   |:-----|:----
   |**Nome** |Un nome per identificare il probe.
   |**Protocollo** |TCP
   |**Porta** |Una porta TCP non usata che deve essere disponibile in tutte le macchine virtuali. Non può essere usata per altri scopi. Due listener non possono usare la stessa porta probe. 
   |**Interval** |L'intervallo di tempo tra i tentativi di probe. Usare il valore predefinito (5).
   |**Soglia non integra** |Il numero di soglie consecutive che devono essere superate prima che una macchina virtuale venga considerata non integra.

8. Fare clic su **OK** per salvare il probe. 

9. Creare una regola di bilanciamento del carico. Fare clic su **Regole di bilanciamento del carico** e quindi fare clic su **Aggiungi**.

10. Configurare la nuova regola di bilanciamento del carico con le seguenti impostazioni:

   |Impostazione |Valore
   |:-----|:----
   |**Nome** |Un nome per identificare la regola di bilanciamento del carico. 
   |**Indirizzo IP front-end IP** |Selezionare l'indirizzo IP che è stato creato. 
   |**Protocollo** |TCP
   |**Porta** |Usare la porta che viene utilizzata dalle istanze di SQL Server. Un'istanza predefinita utilizza la porta 1433, a meno che non venga modificata. 
   |**Porta back-end** |Usare lo stesso valore di **Porta**.
   |**Pool back-end** |Il pool che contiene le macchine virtuali con le istanze di SQL Server. 
   |**Probe di integrità** |Scegliere il probe che è stato creato.
   |**Persistenza della sessione** |Nessuno
   |**Timeout di inattività (minuti)** |Valore predefinito (4)
   |**IP mobile (Direct Server Return)** | Attivato

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Configurare il gruppo di disponibilità affinché usi il nuovo indirizzo IP

Per completare la configurazione del cluster, ripetere i passaggi eseguiti per la creazione del primo gruppo di disponibilità. Ovvero configurare il [cluster affinché usi il nuovo indirizzo IP](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Dopo aver aggiunto un indirizzo IP al listener, configurare il gruppo di disponibilità aggiuntivo effettuando le operazioni seguenti: 

1. Verificare che la porta probe per il nuovo indirizzo IP sia aperta in entrambe le macchine virtuali di SQL Server. 

2. [In Cluster Manager aggiungere il punto di accesso client](#addcap).

3. [Configurare la risorsa IP per il gruppo di disponibilità](#congroup).

   >[!IMPORTANT]
   >Quando si crea l'indirizzo IP, usare l'indirizzo IP che è stato aggiunto al bilanciamento del carico.  

4. [Rendere la risorsa del gruppo di disponibilità di SQL Server dipendente dal punto di accesso client](#dependencyGroup).

5. [Rendere la risorsa del punto di accesso client dipendente dall'indirizzo IP](#listname).
 
6. [Impostare i parametri del cluster in PowerShell](#setparam).

Dopo avere configurato il gruppo di disponibilità per usare il nuovo indirizzo IP, configurare la connessione al listener. 

## <a name="next-steps"></a>Passaggi successivi

- [Configurare un gruppo di disponibilità SQL Server AlwaysOn nelle macchine virtuali di Azure](virtual-machines-windows-portal-sql-availability-group-dr.md)
