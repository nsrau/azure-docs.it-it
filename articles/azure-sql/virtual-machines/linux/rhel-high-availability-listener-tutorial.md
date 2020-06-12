---
title: Configurare il listener del gruppo di disponibilità per SQL Server nelle macchine virtuali RHEL in Azure - Macchine virtuali Linux | Microsoft Docs
description: Informazioni su come configurare un listener del gruppo di disponibilità in SQL Server nelle macchine virtuali RHEL in Azure
ms.service: virtual-machines-linux
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 03/11/2020
ms.openlocfilehash: f60cb3f28c57d6df4a309a7630d078c593d75410
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84343762"
---
# <a name="tutorial-configure-an-availability-group-listener-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Esercitazione: Configurare un listener del gruppo di disponibilità per SQL Server nelle macchine virtuali RHEL in Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!NOTE]
> L'esercitazione presentata è disponibile in **anteprima pubblica**. 
>
> In questa esercitazione viene usato SQL Server 2017 con RHEL 7.6, ma è possibile usare SQL Server 2019 in RHEL 7 o RHEL 8 per configurare la disponibilità elevata. I comandi per configurare le risorse del gruppo di disponibilità sono cambiati in RHEL 8 ed è quindi opportuno vedere l'articolo [Creare risorse del gruppo di disponibilità](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) e le risorse di RHEL 8 per altre informazioni sui comandi corretti.

Questa esercitazione illustra i passaggi per creare un listener del gruppo di disponibilità per i server SQL nelle macchine virtuali RHEL in Azure. Si apprenderà come:

> [!div class="checklist"]
> - Creare un servizio di bilanciamento del carico nel portale di Azure
> - Creare un pool back-end per il servizio di bilanciamento del carico
> - Creare un probe per il servizio di bilanciamento del carico
> - Impostare le regole di bilanciamento del carico
> - Creare la risorsa servizio di bilanciamento del carico nel cluster
> - Creare il listener del gruppo di disponibilità
> - Testare la connessione al listener
> - Test di un failover

## <a name="prerequisite"></a>Prerequisito

Aver completato l'[Esercitazione: Configurare i gruppi di disponibilità per SQL Server nelle macchine virtuali RHEL in Azure](rhel-high-availability-stonith-tutorial.md)

## <a name="create-the-load-balancer-in-the-azure-portal"></a>Creare il servizio di bilanciamento del carico nel portale di Azure

Le istruzioni seguenti illustrano i passaggi da 1 a 4 della sezione [Creare e configurare il servizio di bilanciamento del carico nel portale di Azure](../windows/availability-group-load-balancer-portal-configure.md#create-and-configure-the-load-balancer-in-the-azure-portal) dell'articolo [Bilanciamento del carico - Portale di Azure](../windows/availability-group-load-balancer-portal-configure.md).

### <a name="create-the-load-balancer"></a>Creare il servizio di bilanciamento del carico

1. Nel portale di Azure aprire il gruppo di risorse contenente le macchine virtuali di SQL Server. 

2. Nel gruppo di risorse fare clic su **Aggiungi**.

3. Nei risultati della ricerca cercare il **bilanciamento del carico** e selezionare **Servizio di bilanciamento del carico**, pubblicato da **Microsoft**.

4. Nel pannello **Servizio di bilanciamento del carico** fare clic su**Crea**.

5. Bella finestra di dialogo **Crea servizio di bilanciamento del carico** configurare il servizio di bilanciamento del carico come segue:

   | Impostazione | valore |
   | --- | --- |
   | **Nome** |Nome che rappresenta il servizio di bilanciamento del carico. Ad esempio **sqlLB**. |
   | **Tipo** |**Interno** |
   | **Rete virtuale** |La rete virtuale predefinita creata deve essere denominata **VM1VNET**. |
   | **Subnet** |Selezionare la subnet in cui si trovano le istanze di SQL Server. Il valore predefinito deve essere **VM1Subnet**.|
   | **Assegnazione indirizzi IP** |**Statico** |
   | **Indirizzo IP privato** |Usare l'indirizzo IP `virtualip` creato nel cluster. |
   | **Sottoscrizione** |Usare la sottoscrizione usata per il gruppo di risorse. |
   | **Gruppo di risorse** |Selezionare il gruppo di risorse in cui si trovano le istanze di SQL Server. |
   | **Posizione** |Selezionare il percorso di Azure in cui si trovano le istanze di SQL Server. |

### <a name="configure-the-back-end-pool"></a>Configurare il pool back-end
In Azure il pool di indirizzi back-end è chiamato *pool back-end*. In questo caso, il pool back-end è costituito dagli indirizzi delle tre istanze di SQL Server nel gruppo di disponibilità. 

1. Nel gruppo di risorse fare clic sul servizio di bilanciamento del carico creato. 

2. In **Impostazioni** fare clic su **Pool back-end**.

3. In **Pool back-end** fare clic su **Aggiungi** per creare un pool di indirizzi back-end. 

4. In **Aggiungi pool back-end**, in **Nome** digitare un nome per il pool back-end.

5. In **Associato a** selezionare **Macchina virtuale**. 

6. Selezionare ogni macchina virtuale nell'ambiente e associare l'indirizzo IP appropriato a ogni selezione.

    :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-backend-pool.png" alt-text="Aggiungi pool back-end":::

7. Scegliere **Aggiungi**. 

### <a name="create-a-probe"></a>Creare un probe

Il probe definisce come Azure deve verificare quali istanze di SQL Server sono attualmente proprietarie del listener del gruppo di disponibilità. Azure esamina il servizio in base all'indirizzo IP su una porta definita quando si crea il probe.

1. Nel pannello **Impostazioni** del servizio di bilanciamento del carico fare clic su **Probe integrità**. 

2. Nel pannello **Probe integrità** fare clic su **Aggiungi**.

3. Configurare il probe nel pannello **Aggiungi probe** . Usare i valori seguenti per configurare il probe.

   | Impostazione | valore |
   | --- | --- |
   | **Nome** |Nome che rappresenta il probe. Ad esempio **SQLAlwaysOnEndPointProbe**. |
   | **Protocollo** |**TCP** |
   | **Porta** |È possibile usare qualsiasi porta disponibile. Ad esempio *59999*. |
   | **Interval** |*5* |
   | **Soglia non integra** |*2* |

4.  Fare clic su **OK**. 

5. Accedere a tutte le macchine virtuali e aprire la porta del probe usando i comandi seguenti:

    ```bash
    sudo firewall-cmd --zone=public --add-port=59999/tcp --permanent
    sudo firewall-cmd --reload
    ```

Azure crea il probe e lo usa per verificare quale istanza di SQL Server possieda il listener per il gruppo di disponibilità.

### <a name="set-the-load-balancing-rules"></a>Impostare le regole di bilanciamento del carico

Le regole di bilanciamento del carico consentono di configurare il modo in cui il servizio di bilanciamento del carico instrada il traffico alle istanze di SQL Server. Per questo servizio di bilanciamento del carico abilitare Direct Server Return perché solo una per volta delle tre istanze di SQL Server è proprietaria della risorsa listener del gruppo di disponibilità.

1. Nel pannello **Impostazioni** del servizio di bilanciamento del carico fare clic su **Regole di bilanciamento del carico**. 

2. Nel pannello **Regole di bilanciamento del carico** fare clic su **Aggiungi**.

3. Nel pannello **Aggiungi regola di bilanciamento del carico** configurare la regola di bilanciamento del carico. Usare le seguenti impostazioni: 

   | Impostazione | valore |
   | --- | --- |
   | **Nome** |Nome che rappresenta la regola di bilanciamento del carico. Ad esempio **SQLAlwaysOnEndPointListener**. |
   | **Protocollo** |**TCP** |
   | **Porta** |*1433* |
   | **Porta back-end** |*1433*. Questo valore verrà ignorato perché questa regola usa **IP mobile (Direct Server Return)** . |
   | **Probe** |Usare il nome del probe creato per questo servizio di bilanciamento del carico. |
   | **Persistenza della sessione** |**Nessuno** |
   | **Timeout di inattività (minuti)** |*4* |
   | **IP mobile (Direct Server Return)** |**Enabled** |

   :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-load-balancing-rule.png" alt-text="Aggiungi regola di bilanciamento del carico":::

4. Fare clic su **OK**. 
5. Azure configura la regola di bilanciamento del carico. Ora il servizio di bilanciamento del carico è configurato per instradare il traffico all'istanza di SQL Server che ospita il listener per il gruppo di disponibilità. 

A questo punto il gruppo di risorse dispone di un servizio di bilanciamento del carico, che si connette a tutte le macchine virtuali SQL Server. Il servizio di bilanciamento del carico contiene anche un indirizzo IP per il listener del gruppo di disponibilità SQL Server Always On in modo che tutte le macchine virtuali possano rispondere alle richieste per i gruppi di disponibilità.

## <a name="create-the-load-balancer-resource-in-the-cluster"></a>Creare la risorsa servizio di bilanciamento del carico nel cluster

1. Accedere alla macchina virtuale primaria. È necessario creare la risorsa per abilitare la porta per il probe del servizio di bilanciamento del carico di Azure (59999 in questo esempio). Eseguire il comando seguente:

    ```bash
    sudo pcs resource create azure_load_balancer azure-lb port=59999
    ```

1. Creare un gruppo di risorse contenente la risorsa `virtualip` e `azure_load_balancer`:

    ```bash
    sudo pcs resource group add virtualip_group azure_load_balancer virtualip
    ```

### <a name="add-constraints"></a>Aggiungere i vincoli

1. Per garantire che la risorsa indirizzo IP e la risorsa gruppo di disponibilità del servizio di bilanciamento del carico di Azure siano in esecuzione nello stesso nodo, è necessario configurare un vincolo di condivisione del percorso. Eseguire il comando seguente:

    ```bash
    sudo pcs constraint colocation add azure_load_balancer ag_cluster-master INFINITY with-rsc-role=Master
    ```
1. Creare un vincolo di ordinamento per garantire che la risorsa gruppo di disponibilità sia operativa prima dell'indirizzo IP del servizio di bilanciamento del carico di Azure. Mentre il vincolo di condivisione del percorso implica un vincolo di ordinamento, questo lo applica.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start azure_load_balancer
    ```

1. Per verificare i vincoli, eseguire questo comando:

    ```bash
    sudo pcs constraint list --full
    ```

    Dovrebbe venire visualizzato l'output seguente.

    ```output
    Location Constraints:
    Ordering Constraints:
      promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
      promote ag_cluster-master then start azure_load_balancer (kind:Mandatory) (id:order-ag_cluster-master-azure_load_balancer-mandatory)
    Colocation Constraints:
      virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
      azure_load_balancer with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-azure_load_balancer-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

## <a name="create-the-availability-group-listener"></a>Creare il listener del gruppo di disponibilità

1. Nel nodo primario eseguire il comando seguente in SQLCMD o SSMS:

    - Sostituire l'indirizzo IP usato di seguito con l'indirizzo IP `virtualip`.

    ```sql
    ALTER AVAILABILITY
    GROUP [ag1] ADD LISTENER 'ag1-listener' (
            WITH IP(('10.0.0.7'    ,'255.255.255.0'))
                ,PORT = 1433
            );
    GO
    ```

1. Accedere a ogni nodo della macchina virtuale. Usare il comando seguente per aprire il file hosts e configurare la risoluzione dei nomi host per `ag1-listener` in ogni macchina virtuale.

    ```
    sudo vi /etc/hosts
    ```

    Nell'editor **vi** immettere `i` per inserire il testo e, in una riga vuota, aggiungere l'indirizzo IP di `ag1-listener`. Aggiungere quindi `ag1-listener` dopo uno spazio accanto all'indirizzo IP.

    ```output
    <IP of ag1-listener> ag1-listener
    ```

    Per uscire dall'editor **vi**, premere prima il tasto **ESC** e quindi immettere il comando `:wq` per eseguire la scrittura del file e uscire. Eseguire questa operazione in ogni nodo.

## <a name="test-the-listener-and-a-failover"></a>Testare il listener e un failover

### <a name="test-logging-in-to-sql-server-using-the-availability-group-listener"></a>Testare l'accesso a SQL Server usando il listener del gruppo di disponibilità

1. Usare SQLCMD per accedere al nodo primario di SQL Server usando il nome del listener del gruppo di disponibilità:

    - Usare un account di accesso creato in precedenza e sostituire `<YourPassword>` con la password corretta. Nell'esempio seguente viene usato l'account di accesso `sa` creato con SQL Server.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Verificare il nome del server a cui si è connessi. Eseguire questo comando in SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    L'output mostrerà il nodo primario corrente. Corrisponderà a `VM1` se non è mai stato testato un failover.

    Uscire dalla sessione di SQL Server digitando il comando `exit`.

### <a name="test-a-failover"></a>Testare un failover

1. Eseguire il comando seguente per procedere al failover manuale della replica primaria in `<VM2>` o in un'altra replica. Sostituire `<VM2>` con il valore del nome del server.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Se si controllano i vincoli, si noterà che ne è stato aggiunto un altro a seguito del failover manuale:

    ```bash
    sudo pcs constraint list --full
    ```

    Si noterà che è stato aggiunto un vincolo con l'ID `cli-prefer-ag_cluster-master`.

1. Rimuovere il vincolo con l'ID `cli-prefer-ag_cluster-master` usando il comando seguente:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Controllare le risorse cluster usando il comando `sudo pcs resource`. Si noterà che l'istanza primaria è ora `<VM2>`.

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
        Masters: [ <VM2> ]
        Slaves: [ <VM1> <VM3> ]
    Resource Group: virtualip_group
        azure_load_balancer        (ocf::heartbeat:azure-lb):      Started <VM2>
        virtualip  (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

1. Usare SQLCMD per accedere alla replica primaria usando il nome del listener:

    - Usare un account di accesso creato in precedenza e sostituire `<YourPassword>` con la password corretta. Nell'esempio seguente viene usato l'account di accesso `sa` creato con SQL Server.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
     ```

1. Verificare il server a cui si è connessi. Eseguire questo comando in SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    Si noterà che si è ora connessi alla macchina virtuale di cui è stato eseguito il failover.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui servizi di bilanciamento del carico in Azure, vedere:

> [!div class="nextstepaction"]
> [Configurare un servizio di bilanciamento del carico per un gruppo di disponibilità in SQL Server in macchine virtuali di Azure](../windows/availability-group-load-balancer-portal-configure.md)
