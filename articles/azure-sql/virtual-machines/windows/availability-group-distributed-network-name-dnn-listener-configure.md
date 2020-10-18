---
title: Configurare il listener DNN per il gruppo di disponibilità
description: Informazioni su come configurare un listener DNN (Distributed Network Name) per sostituire il listener del nome di rete virtuale (VNN) e instradare il traffico al gruppo di disponibilità Always On SQL Server nella macchina virtuale di Azure.
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
ms.date: 10/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: abfcd6a13bc5e8ad262fe47111eb680ad00a34df
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168982"
---
# <a name="configure-a-dnn-listener-for-an-availability-group"></a>Configurare un listener DNN per un gruppo di disponibilità
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Con SQL Server nelle macchine virtuali di Azure, il nome di rete distribuita (DNN) instrada il traffico alla risorsa cluster appropriata. Fornisce un modo più semplice per connettersi a un gruppo di disponibilità Always On (AG) rispetto al listener del nome di rete virtuale (VNN), senza la necessità di un Azure Load Balancer. 

Questo articolo illustra come configurare un listener DNN per sostituire il listener VNN e instradare il traffico al gruppo di disponibilità con SQL Server in macchine virtuali di Azure per la disponibilità elevata e il ripristino di emergenza (HADR). 

La funzionalità listener DNN è attualmente disponibile solo a partire da SQL Server 2019 CU8 in Windows Server 2016 e versioni successive. 

Per un'opzione di connettività alternativa, prendere in considerazione un [listener VNN e Azure Load Balancer](availability-group-vnn-azure-load-balancer-configure.md) . 

## <a name="overview"></a>Panoramica

Un listener DNN (Distributed Network Name) sostituisce il listener del gruppo di disponibilità VNN (Virtual Network Name) tradizionale quando viene usato con [gruppi di disponibilità always on nelle vm SQL Server](availability-group-overview.md). Questa operazione nega la necessità di un Azure Load Balancer di instradare il traffico, semplificando la distribuzione, la manutenzione e il miglioramento del failover. 

Usare il listener DNN per sostituire un listener VNN esistente o in alternativa utilizzarlo insieme a un listener VNN esistente, in modo che il gruppo di disponibilità disponga di due punti di connessione distinti, uno usando il nome del listener VNN (e la porta se non predefinita) e uno usando il nome del listener DNN e la porta. 

## <a name="prerequisites"></a>Prerequisiti

Per poter completare la procedura descritta in questo articolo, è necessario disporre di:

- SQL Server 2019 in CU8 o versioni successive, in Windows Server 2016 e versioni successive
- Si è deciso che il nome di rete distribuita è l' [opzione di connettività appropriata per la soluzione HADR](hadr-cluster-best-practices.md#connectivity).
- Configurazione del [gruppo di disponibilità always on](availability-group-overview.md). 
- Installata la versione più recente di [PowerShell](/powershell/azure/install-az-ps). 


## <a name="create-script"></a>Crea script

Usare PowerShell per creare la risorsa nome di rete distribuita (DNN) e associarla al gruppo di disponibilità. 

A tale scopo, seguire questa procedura: 

1. Aprire un editor di testo, come Blocco note. 
1. Copiare e incollare lo script seguente: 

   ```powershell
   param (
      [Parameter(Mandatory=$true)][string]$Ag,
      [Parameter(Mandatory=$true)][string]$Dns,
      [Parameter(Mandatory=$true)][string]$Port
   )
   
   Write-Host "Add a DNN listener for availability group $Ag with DNS name $Dns and port $Port"
   
   $ErrorActionPreference = "Stop"
   
   # create the DNN resource with the port as the resource name
   Add-ClusterResource -Name $Port -ResourceType "Distributed Network Name" -Group $Ag 
   
   # set the DNS name of the DNN resource
   Get-ClusterResource -Name $Port | Set-ClusterParameter -Name DnsName -Value $Dns 
   
   # start the DNN resource
   Start-ClusterResource -Name $Port
   
   
   $Dep = Get-ClusterResourceDependency -Resource $Ag
   if ( $Dep.DependencyExpression -match '\s*\((.*)\)\s*' )
   {
   $DepStr = "$($Matches.1) or [$Port]"
   }
   else
   {
   $DepStr = "[$Port]"
   }
   
   Write-Host "$DepStr"
   
   # add the Dependency from availability group resource to the DNN resource
   Set-ClusterResourceDependency -Resource $Ag -Dependency "$DepStr"
   
   
   #bounce the AG resource
   Stop-ClusterResource -Name $Ag
   Start-ClusterResource -Name $Ag
   ```

1. Salvare lo script come `.ps1` file, ad esempio `add_dnn_listener.ps1` . 


## <a name="execute-script"></a>Esegui script

Per creare il listener DNN, eseguire lo script passando i parametri per il nome del gruppo di disponibilità, il nome del listener e la porta. 

Ad esempio, supponendo il nome di un gruppo di disponibilità `ag1` , il nome del listener `dnnlsnr` e la porta del listener come `6789` , attenersi alla procedura seguente: 

1. Aprire uno strumento dell'interfaccia della riga di comando, ad esempio prompt dei comandi o PowerShell. 
1. Passare al percorso in cui è stato salvato lo `.ps1` script, ad esempio c:\Documenti. 
1. Eseguire lo script: ```add_dnn_listener.ps1 <ag name> <listener-name> <listener port>``` . Ad esempio: 

   ```console
   c:\Documents> add_dnn_listener.ps1 ag1 dnnlsnr 6789
   ```

## <a name="verify-listener"></a>Verifica listener

Usare SQL Server Management Studio o Transact-SQL per confermare che il listener DNN è stato creato correttamente. 

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

Espandere listener del **gruppo di disponibilità** in [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) per visualizzare il listener DNN: 

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-in-ssms.png" alt-text="Visualizzare il listener DNN nei listener del gruppo di disponibilità in SQL Server Management Studio (SSMS)":::

### <a name="transact-sql"></a>Transact-SQL

Usare Transact-SQL per visualizzare lo stato del listener DNN: 

```sql
SELECT * FROM SYS.AVAILABILITY_GROUP_LISTENERS
```

`1`Il valore per `is_distributed_network_name` indica che il listener è un listener DNN (Distributed Network Name): 

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-tsql.png" alt-text="Visualizzare il listener DNN nei listener del gruppo di disponibilità in SQL Server Management Studio (SSMS)":::


## <a name="update-connection-string"></a>Aggiornare la stringa di connessione

Aggiornare le stringhe di connessione per le applicazioni in modo che si connettano al listener DNN. Per garantire una connettività rapida al failover, aggiungere `MultiSubnetFailover=True` alla stringa di connessione se il client SQL lo supporta. 

## <a name="test-failover"></a>Failover di test

Failover di test del gruppo di disponibilità per garantire la funzionalità. 

Per eseguire il test del failover, attenersi alla procedura seguente: 

1. Connettersi al listener DNN o a una delle repliche usando [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 
1. Espandere **Always on gruppo di disponibilità** nel **Esplora oggetti**. 
1. Fare clic con il pulsante destro del mouse sul gruppo di disponibilità e scegliere **failover** per aprire la **procedura guidata failover**. 
1. Seguire le istruzioni per scegliere una destinazione di failover e non eseguire il gruppo di disponibilità su una replica secondaria. 
1. Verificare che il database sia in uno stato sincronizzato nella nuova replica primaria. 
1. Opzionale Eseguire il failback alla replica primaria originale o a un'altra replica secondaria. 

## <a name="test-connectivity"></a>Testare la connettività

Testare la connettività al listener DNN con i seguenti passaggi:

1. Aprire [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
1. Connettersi al listener DNN. 
1. Aprire una nuova finestra query e verificare la replica a cui si è connessi eseguendo `SELECT @@SERVERNAME` . 
1. Eseguire il failover del gruppo di disponibilità su un'altra replica.
1. Dopo un periodo di tempo ragionevole, eseguire `SELECT @@SERVERNAME` per confermare che il gruppo di disponibilità è ora ospitato in un'altra replica. 


## <a name="limitations"></a>Limitazioni

- Attualmente, un listener DNN per un gruppo di disponibilità è supportato solo per SQL Server 2019 CU8 e versioni successive in Windows Server 2016 e versioni successive. 
- Quando si lavora con altre funzionalità di SQL Server e un gruppo di disponibilità con un DNN, è possibile che si verifichino ulteriori considerazioni. Per altre informazioni, vedere [AG con interoperabilità DNN](availability-group-dnn-interoperability.md). 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità di SQL Server HADR in Azure, vedere [gruppi di disponibilità](availability-group-overview.md) e istanza del cluster di [failover](failover-cluster-instance-overview.md). È anche possibile apprendere le [procedure](hadr-cluster-best-practices.md) consigliate per la configurazione dell'ambiente per la disponibilità elevata e il ripristino di emergenza. 


