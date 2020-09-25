---
title: Configurare il nome di rete distribuita (DNN)
description: Informazioni su come configurare un nome di rete distribuita (DNN) per instradare il traffico al SQL Server nell'istanza del cluster di failover di macchine virtuali (FCI) di Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ce3261eca8697ae1fabc07785353a4e845dadeba
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317005"
---
# <a name="configure-a-distributed-network-name-for-an-fci"></a>Configurare un nome di rete distribuita per un'istanza FCI 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Nelle macchine virtuali di Azure viene usato il nome di rete distribuita (DNN) per instradare il traffico alla risorsa cluster appropriata. Fornisce un modo più semplice per connettersi all'istanza del cluster di failover di SQL Server (FCI) rispetto al nome della rete virtuale (VNN), senza la necessità di Azure Load Balancer. Questa funzionalità è attualmente disponibile in anteprima ed è disponibile solo per SQL Server 2019 CU2 e versioni successive e Windows Server 2016 e versioni successive. 

Questo articolo illustra come configurare un DNN per instradare il traffico verso il failover con SQL Server in macchine virtuali di Azure per la disponibilità elevata e il ripristino di emergenza (HADR). 

## <a name="prerequisites"></a>Prerequisiti

Per poter completare la procedura descritta in questo articolo, è necessario disporre di:

- Si è deciso che il nome di rete distribuita è l' [opzione di connettività appropriata per la soluzione HADR](hadr-cluster-best-practices.md#connectivity).
- Configurazione delle [istanze del cluster di failover](failover-cluster-instance-overview.md). 
- Installata la versione più recente di [PowerShell](/powershell/azure/install-az-ps). 

## <a name="create-dnn-resource"></a>Creare una risorsa DNN 

La risorsa DNN viene creata nello stesso gruppo di cluster del SQL Server FCI. Usare PowerShell per creare la risorsa DNN all'interno del gruppo cluster FCI. 

Il comando di PowerShell seguente aggiunge una risorsa DNN al gruppo di cluster FCI di SQL Server con un nome di risorsa `<dnnResourceName>` . Il nome della risorsa viene usato per identificare in modo univoco una risorsa. È possibile utilizzarne uno che ha senso per l'utente ed è univoco nel cluster. Il tipo di risorsa deve essere `Distributed Network Name` . 

Il `-Group` valore deve corrispondere al nome del gruppo di cluster che corrisponde alla SQL Server FCI in cui si desidera aggiungere il nome della rete distribuita. Per un'istanza predefinita, il formato tipico è `SQL Server (MSSQLSERVER)` . 


```powershell
Add-ClusterResource -Name <dnnResourceName> `
-ResourceType "Distributed Network Name" -Group "<WSFC role of SQL server instance>"
```

Ad esempio, per creare la risorsa DNN `dnn-demo` per un SQL Server FCI predefinito, usare il comando di PowerShell seguente:

```powershell
Add-ClusterResource -Name dnn-demo `
-ResourceType "Distributed Network Name" -Group "SQL Server (MSSQLSERVER)"

```

## <a name="set-cluster-dnn-dns-name"></a>Imposta il nome DNS del cluster DNN

Impostare il nome DNS per la risorsa DNN nel cluster. Il cluster usa quindi questo valore per instradare il traffico al nodo che ospita attualmente la SQL Server FCI. 
 
I client usano il nome DNS per connettersi alla SQL Server FCI. È possibile scegliere un valore univoco. In alternativa, se si dispone già di un'istanza del cluster di failover esistente e non si desidera aggiornare le stringhe di connessione client, è possibile configurare DNN in modo da utilizzare il VNN corrente già utilizzato dai client. A tale scopo, è necessario [rinominare il VNN](#rename-the-vnn) prima di impostare DNN in DNS.


Usare questo comando per impostare il nome DNS per il DNN: 

```powershell
Get-ClusterResource -Name <dnnResourceName> | `
Set-ClusterParameter -Name DnsName -Value <DNSName>
```

Il `DNSName` valore è quello usato dai client per connettersi all'istanza del cluster di failover di SQL Server. Per consentire ai client di connettersi a, ad esempio `FCIDNN` , usare il comando di PowerShell seguente:

```powershell
Get-ClusterResource -Name dnn-demo | `
Set-ClusterParameter -Name DnsName -Value FCIDNN
```

I client verranno ora inseriti `FCIDNN` nella stringa di connessione per la connessione al SQL Server FCI. 

   > [!WARNING]
   > Non eliminare il nome della rete virtuale (VNN) corrente perché è un componente necessario dell'infrastruttura FCI. 


### <a name="rename-the-vnn"></a>Rinominare il VNN 

Se si dispone di un nome di rete virtuale esistente e si desidera che i client continuino a utilizzare questo valore per connettersi alla SQL Server FCI, è necessario rinominare il VNN corrente in un valore segnaposto. Dopo la ridenominazione del VNN corrente, è possibile impostare il valore del nome DNS per DNN su VNN. 

Si applicano alcune restrizioni per rinominare VNN. Per ulteriori informazioni, vedere [ridenominazione di un'istanza FCI](/sql/sql-server/failover-clusters/install/rename-a-sql-server-failover-cluster-instance).

Se l'uso del VNN corrente non è necessario per l'azienda, ignorare questa sezione. Dopo aver rinominato il VNN, [impostare il nome DNS DNN del cluster](#set-cluster-dnn-dns-name). 

   
## <a name="set-dnn-resource-online"></a>Imposta la risorsa DNN online

Dopo che la risorsa DNN è stata denominata in modo appropriato e il valore del nome DNS è stato impostato nel cluster, usare PowerShell per impostare la risorsa DNN online nel cluster: 

```powershell
Start-ClusterResource -Name <dnnResourceName>
```

Ad esempio, per avviare la risorsa DNN `dnn-demo` , usare il comando di PowerShell seguente: 

```powershell
Start-ClusterResource -Name dnn-demo
```

## <a name="configure-possible-owners"></a>Configurare possibili proprietari

Per impostazione predefinita, il cluster associa il nome DNS DNN a tutti i nodi del cluster. Tuttavia, i nodi del cluster che non fanno parte dell'istanza FCI di SQL Server devono essere esclusi dall'elenco di DNN possibili proprietari. 

Per aggiornare i proprietari possibili, attenersi alla procedura seguente:

1. Passare alla risorsa DNN in Gestione cluster di failover. 
1. Fare clic con il pulsante destro del mouse sulla risorsa DNN e scegliere **Proprietà**. 
   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/fci-dnn-properties.png" alt-text="Menu di scelta rapida per la risorsa DNN, con il comando Properties evidenziato.":::
1. Deselezionare la casella di controllo per tutti i nodi che non fanno parte dell'istanza del cluster di failover. L'elenco dei possibili proprietari per la risorsa DNN deve corrispondere all'elenco dei possibili proprietari per la risorsa dell'istanza di SQL Server. Ad esempio, supponendo che data3 non partecipi all'istanza FCI, l'immagine seguente è un esempio di rimozione di data3 dall'elenco dei possibili proprietari per la risorsa DNN: 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/clear-check-for-nodes-not-in-fci.png" alt-text="Deselezionare la casella di controllo accanto ai nodi che non fanno parte dell'istanza FCI per i possibili proprietari della risorsa DNN":::

1. Selezionare **OK** per salvare le impostazioni. 


## <a name="restart-sql-server-instance"></a>Riavvia SQL Server istanza 

Utilizzare Gestione cluster di failover per riavviare l'istanza di SQL Server. Seguire questa procedura:

1. Passare alla risorsa SQL Server in Gestione cluster di failover.
1. Fare clic con il pulsante destro del mouse sulla risorsa SQL Server e portarla offline. 
1. Quando tutte le risorse associate sono offline, fare clic con il pulsante destro del mouse sulla risorsa SQL Server e riportarla online. 

## <a name="update-connection-string"></a>Aggiornare la stringa di connessione

Per garantire una connettività rapida al failover, aggiungere `MultiSubnetFailover=True` alla stringa di connessione se la versione del client SQL è precedente a 4.6.1. 

Inoltre, se DNN non usa il VNN originale, i client SQL che si connettono al SQL Server FCI dovranno aggiornare la stringa di connessione al nome DNS DNN. Per evitare questo requisito, è possibile aggiornare il valore del nome DNS in modo che sia il nome del VNN. Tuttavia, è necessario [sostituire prima il VNN esistente con un segnaposto](#rename-the-vnn) . 

## <a name="test-failover"></a>Failover di test

Failover di test della risorsa in cluster per convalidare le funzionalità del cluster. 

Per eseguire il test del failover, attenersi alla procedura seguente: 

1. Connettersi a uno dei nodi del cluster SQL Server usando RDP.
1. Aprire **Gestione cluster di failover**. Selezionare **Ruoli**. Rilevare quale nodo è proprietario del ruolo dell'istanza del cluster di failover di SQL Server.
1. Fare clic con il pulsante destro del mouse sul ruolo dell'istanza del cluster di failover di SQL Server. 
1. Selezionare **Sposta** e quindi selezionare **Miglior nodo possibile**.

In **Gestione cluster di failover** viene visualizzato il ruolo e le relative risorse passano alla modalità offline. Le risorse vengono quindi spostate e portate di nuovo online nell'altro nodo.

## <a name="test-connectivity"></a>Testare la connettività

Per testare la connettività, accedere a un'altra macchina virtuale nella stessa rete virtuale. Aprire **SQL Server Management Studio** e connettersi all'istanza del cluster di failover di SQL Server usando il nome DNS DNN.

Se necessario, è possibile [scaricare SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="avoid-ip-conflict"></a>Evitare conflitti IP

Si tratta di un passaggio facoltativo per evitare che l'indirizzo IP virtuale (VIP) usato dalla risorsa FCI venga assegnato a un'altra risorsa in Azure come duplicato. 

Sebbene i clienti usino ora DNN per connettersi alla SQL Server FCI, il nome della rete virtuale (VNN) e l'indirizzo IP virtuale non possono essere eliminati perché sono componenti necessari dell'infrastruttura FCI. Tuttavia, poiché non è più disponibile un servizio di bilanciamento del carico che riserva l'indirizzo IP virtuale in Azure, esiste il rischio che a un'altra risorsa nella rete virtuale venga assegnato lo stesso indirizzo IP dell'indirizzo IP virtuale usato dall'istanza FCI. Questo può causare un problema di conflitto IP duplicato. 

Configurare un indirizzo APIPA o una scheda di rete dedicata per riservare l'indirizzo IP. 

### <a name="apipa-address"></a>Indirizzo APIPA

Per evitare di utilizzare indirizzi IP duplicati, configurare un indirizzo APIPA (noto anche come indirizzo locale del collegamento). A tale scopo, eseguire il comando seguente:

```powershell
Get-ClusterResource "virtual IP address" | Set-ClusterParameter 
    –Multiple @{"Address”=”169.254.1.1”;”SubnetMask”=”255.255.0.0”;"OverrideAddressMatch"=1;”EnableDhcp”=0}
```

In questo comando "indirizzo IP virtuale" è il nome della risorsa dell'indirizzo VIP in cluster e "169.254.1.1" è l'indirizzo APIPA scelto per l'indirizzo VIP. Scegliere l'indirizzo più adatto alla propria attività. Impostare `OverrideAddressMatch=1` per consentire l'uso dell'indirizzo IP in qualsiasi rete, incluso lo spazio degli indirizzi APIPA. 

### <a name="dedicated-network-adapter"></a>Scheda di rete dedicata

In alternativa, configurare una scheda di rete in Azure per riservare l'indirizzo IP usato dalla risorsa indirizzo IP virtuale. Tuttavia, in questo modo viene utilizzato l'indirizzo nello spazio degli indirizzi della subnet e si verifica un sovraccarico aggiuntivo che garantisce che la scheda di rete non venga utilizzata per altri scopi.

## <a name="limitations"></a>Limitazioni

- Attualmente, DNN è supportato solo per SQL Server 2019 CU2 e versioni successive in Windows Server 2016. 
- Attualmente, un DNN è supportato solo per le istanze del cluster di failover con SQL Server nelle VM di Azure. Usare il nome della rete virtuale con Azure Load Balancer per i listener del gruppo di disponibilità.
- Quando si lavora con altre funzionalità di SQL Server e un'istanza FCI con una DNN, è possibile che si verifichino ulteriori considerazioni. Per altre informazioni, vedere [FCI con interoperabilità DNN](failover-cluster-instance-dnn-interoperability.md). 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità di SQL Server HADR in Azure, vedere [gruppi di disponibilità](availability-group-overview.md) e istanza del cluster di [failover](failover-cluster-instance-overview.md). È anche possibile apprendere le [procedure](hadr-cluster-best-practices.md) consigliate per la configurazione dell'ambiente per la disponibilità elevata e il ripristino di emergenza. 

Potrebbero essere necessari requisiti di configurazione aggiuntivi per alcune funzionalità di SQL Server specifiche se utilizzate con DNN e FCI. Per altre informazioni, vedere [FCI con interoperabilità DNN](failover-cluster-instance-dnn-interoperability.md) . 

