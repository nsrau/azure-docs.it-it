---
title: Configurare VNN con Azure Load Balancer per HADR
description: Informazioni su come configurare un servizio di bilanciamento del carico di Azure per instradare il traffico al nome di rete virtuale (VNN) per il gruppo di disponibilità o l'istanza del cluster di failover con SQL Server in macchine virtuali di Azure per la disponibilità elevata e il ripristino di emergenza (HADR).
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 10ff324e85082a4a5911e2c949744e7df1d9ad0b
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85965623"
---
# <a name="configure-vnn-with-azure-load-balancer-sql-server-on-azure-vms"></a>Configurare VNN con Azure Load Balancer (SQL Server in macchine virtuali di Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Nelle macchine virtuali di Azure, i cluster usano un servizio di bilanciamento del carico per contenere un indirizzo IP che deve trovarsi in un nodo del cluster alla volta. In questa soluzione, il servizio di bilanciamento del carico include l'indirizzo IP per il nome di rete virtuale (VNN) usato dalla risorsa in cluster in Azure. 

Questo articolo illustra come configurare un servizio di bilanciamento del carico usando il servizio Azure Load Balancer. Il servizio di bilanciamento del carico instraderà il traffico verso il [listener del gruppo di disponibilità (AG)](availability-group-overview.md) o le [istanze del cluster di failover (failover)](failover-cluster-instance-overview.md) con SQL Server nelle VM di Azure per la disponibilità elevata e il ripristino di emergenza (HADR). 



## <a name="prerequisites"></a>Prerequisiti

Per poter completare la procedura descritta in questo articolo, è necessario disporre di:

- Si è deciso che Azure Load Balancer è l' [opzione di connettività appropriata per la soluzione HADR](hadr-cluster-best-practices.md#connectivity).
- Configurare il [listener del gruppo di disponibilità](availability-group-overview.md) o le [istanze del cluster di failover](failover-cluster-instance-overview.md). 
- Installata la versione più recente di [PowerShell](/powershell/azure/install-az-ps?view=azps-4.2.0). 


## <a name="create-load-balancer"></a>Creare un servizio di bilanciamento del carico

Usare il [portale di Azure](https://portal.azure.com) per creare il servizio di bilanciamento del carico:

1. Nel portale di Azure passare al gruppo di risorse contenente le macchine virtuali.

1. Selezionare **Aggiungi**. Cerca **Load Balancer**in Azure Marketplace. Selezionare **Bilanciamento del carico**.

1. Selezionare **Crea**.

1. Configurare il servizio di bilanciamento del carico usando i valori seguenti:

   - **Sottoscrizione** La sottoscrizione di Azure.
   - **Gruppo di risorse**: il gruppo di risorse contenente le macchine virtuali.
   - **Name**: un nome che identifichi il servizio di bilanciamento del carico.
   - **Area**: la località di Azure contenente le macchine virtuali.
   - **Tipo**: pubblico o privato. Un servizio di bilanciamento del carico privato è accessibile dalla rete virtuale. La maggior parte delle applicazioni Azure può usare un servizio di bilanciamento del carico privato. Se l'applicazione deve accedere a SQL Server direttamente tramite Internet, usare un servizio di bilanciamento del carico pubblico.
   - **SKU**: Standard.
   - **Rete virtuale**: la stessa rete delle macchine virtuali.
   - **Assegnazione indirizzo IP**: statico. 
   - **Indirizzo IP privato**: l'indirizzo IP assegnato alla risorsa di rete del cluster.

   La figura seguente mostra l'interfaccia utente della pagina **Crea servizio di bilanciamento del carico**:

   ![Configurare il servizio di bilanciamento del carico](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

## <a name="configure-backend-pool"></a>Configurare il pool back-end

1. Tornare al gruppo di risorse di Azure contenente le macchine virtuali e individuare il nuovo servizio di bilanciamento del carico. Potrebbe essere necessario aggiornare la visualizzazione del gruppo di risorse. Selezionare l'istanza di Load Balancer.

1. Selezionare **Pool back-end** e quindi **Aggiungi**.

1. Associare il pool back-end con il set di disponibilità contenente le macchine virtuali.

1. In **Configurazioni IP della rete di destinazione** selezionare **MACCHINA VIRTUALE** e scegliere le macchine virtuali che parteciperanno come nodi del cluster. Assicurarsi di includere tutte le macchine virtuali che ospiteranno l'istanza FCI o il gruppo di disponibilità.

1. Selezionare **OK** per creare il pool back-end.

## <a name="configure-health-probe"></a>Configurare il probe di integrità

1. Nel riquadro bilanciamento del carico selezionare **Probe di integrità**.

1. Selezionare **Aggiungi**.

1. Nel riquadro **Aggiungi Probe** di integrità <span id="probe"> </span> impostare i parametri del probe di integrità seguenti:

   - **Name**: un nome per il probe di integrità.
   - **Protocollo**: TCP.
   - **Port**: la porta creata nel firewall per il probe di integrità [durante la preparazione della macchina virtuale](failover-cluster-instance-prepare-vm.md#uninstall-sql-server-1). In questo articolo, l'esempio usa la porta TCP `59999`.
   - **Intervallo**: 5 secondi.
   - **Soglia non integra**: 2 errori consecutivi.

1. Selezionare **OK**.

## <a name="set-load-balancing-rules"></a>Impostare le regole di bilanciamento del carico

1. Nel riquadro bilanciamento del carico selezionare regole di **bilanciamento del carico**.

1. Selezionare **Aggiungi**.

1. Impostare i parametri della regola di bilanciamento del carico:

   - **Nome**: nome per le regole di bilanciamento del carico.
   - **Indirizzo IP**front-end: indirizzo IP per il SQL Server FCI o la risorsa di rete in cluster del listener AG.
   - **Port**: SQL Server porta TCP. La porta predefinita dell'istanza è 1433.
   - **Porta back-end**: la stessa porta del valore della **porta** quando si Abilita l' **indirizzo IP mobile (Direct Server Return)**.
   - **Pool back-end**: il nome del pool back-end configurato in precedenza.
   - **Probe di integrità**: il probe di integrità configurato in precedenza.
   - **Salvataggio permanente sessione**: No.
   - **Timeout di inattività (minuti)** : 4.
   - **IP mobile (Direct Server Return)** : abilitato.

1. Selezionare **OK**.

## <a name="configure-cluster-probe"></a>Configurare il probe del cluster

Impostare il parametro della porta probe del cluster in PowerShell.

Per impostare tale parametro, aggiornare le variabili nello script seguente con i valori dell'ambiente in uso. Rimuovere le parentesi acute (`<` e `>`) dallo script.

```powershell
$ClusterNetworkName = "<Cluster Network Name>"
$IPResourceName = "<SQL Server FCI / AG Listener IP Address Resource Name>" 
$ILBIP = "<n.n.n.n>" 
[int]$ProbePort = <nnnnn>

Import-Module FailoverClusters

Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
```

Nella tabella seguente vengono descritti i valori che è necessario aggiornare:


|**Valore**|**Descrizione**|
|---------|---------|
|`Cluster Network Name`| Nome del cluster di failover di Windows Server per la rete. In **Gestione cluster di failover** > **Reti** fare clic con il pulsante destro del mouse sulla rete e scegliere **Proprietà**. Il valore corretto è riportato in **Nome** nella scheda **Generale**.|
|`SQL Server FCI/AG listener IP Address Resource Name`|Nome della risorsa per l'indirizzo IP del listener di SQL Server FCI o del gruppo di disponibilità. In **Gestione cluster di failover** > **Ruoli** fare clic con il pulsante destro del mouse sulla risorsa indirizzo IP sotto **Nome server** nel ruolo dell'istanza del cluster di failover di SQL Server e quindi scegliere **Proprietà**. Il valore corretto è riportato in **Nome** nella scheda **Generale**.|
|`ILBIP`|Indirizzo IP del servizio di bilanciamento del carico interno (ILB). Questo indirizzo viene configurato nel portale di Azure come indirizzo front-end di ILB. Questo è anche l'indirizzo IP dell'istanza del cluster di failover SQL Server. È riportato in **Gestione cluster di failover** nella stessa pagina delle proprietà in cui si trova `<SQL Server FCI/AG listener IP Address Resource Name>`.|
|`nnnnn`|Porta Probe configurata nel Probe di integrità del servizio di bilanciamento del carico. È valida qualsiasi porta TCP inutilizzata.|
|SubnetMask| Subnet mask per il parametro del cluster. Deve essere l'indirizzo di trasmissione IP TCP: `255.255.255.255` .| 


Dopo aver configurato il probe del cluster, è possibile visualizzare tutti i parametri del cluster in PowerShell. Eseguire questo script:

```powershell
Get-ClusterResource $IPResourceName | Get-ClusterParameter
```


## <a name="test-failover"></a>Failover di test


Failover di test della risorsa in cluster per convalidare le funzionalità del cluster. 

# <a name="failover-cluster-instance"></a>[Istanza del cluster di failover](#tab/fci)

Eseguire questa procedura:

1. Connettersi a uno dei nodi del cluster SQL Server usando RDP.
1. Aprire **Gestione cluster di failover**. Selezionare **Ruoli**. Rilevare quale nodo è proprietario del ruolo dell'istanza del cluster di failover di SQL Server.
1. Fare clic con il pulsante destro del mouse sul ruolo dell'istanza del cluster di failover di SQL Server. 
1. Selezionare **Sposta** e quindi selezionare **Miglior nodo possibile**.

In **Gestione cluster di failover** viene visualizzato il ruolo e le relative risorse passano alla modalità offline. Le risorse vengono quindi spostate e portate di nuovo online nell'altro nodo.



# <a name="ag-listener"></a>[Listener del gruppo di disponibilità](#tab/ag)

Eseguire questa procedura:

1. Aprire [SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms) e connettersi al listener del gruppo di disponibilità. 

1. Espandere **Always on gruppo di disponibilità** nel **Esplora oggetti**. 
1. Fare clic con il pulsante destro del mouse sul gruppo di disponibilità e scegliere **failover**. 
1. Seguire le istruzioni della procedura guidata per eseguire il failover del gruppo di disponibilità su una replica secondaria. 

Il failover ha esito positivo quando le repliche passano i ruoli e sono sincronizzate. 

---

## <a name="test-connectivity"></a>Testare la connettività

Per testare la connettività, accedere a un'altra macchina virtuale nella stessa rete virtuale. Aprire **SQL Server Management Studio** e connettersi al nome dell'istanza FCI di SQL Server o al listener del gruppo di disponibilità.

>[!NOTE]
>Se necessario, è possibile [scaricare SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità di SQL Server HADR in Azure, vedere [gruppi di disponibilità](availability-group-overview.md) e istanza del cluster di [failover](failover-cluster-instance-overview.md). È anche possibile apprendere le [procedure](hadr-cluster-best-practices.md) consigliate per la configurazione dell'ambiente per la disponibilità elevata e il ripristino di emergenza. 



