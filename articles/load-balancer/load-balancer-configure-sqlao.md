<properties 
   pageTitle="Configurare il bilanciamento del carico per SQL AlwaysOn | Microsoft Azure"
   description="Configurare il bilanciamento del carico per usare sempre SQL AlwaysOn e sfruttare PowerShell per creare il bilanciamento del carico per l'implementazione SQL"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="sewhee" />

# Configurare il bilanciamento del carico per SQL AlwaysOn

I gruppi di disponibilità AlwaysOn di SQL Server ora possono essere eseguiti con ILB. Un gruppo di disponibilità è la soluzione principale di SQL Server per il ripristino di emergenza e la disponibilità elevata. Il listener del gruppo di disponibilità consente alle applicazioni client di connettersi facilmente alla replica primaria, indipendentemente dal numero di repliche nella configurazione.

Il nome del listener (DNS) viene associato a un indirizzo IP con carico bilanciato e il bilanciamento del carico di Azure indirizza il traffico in ingresso solo al server primario nel set di repliche.


È possibile usare il supporto ILB per gli endpoint di SQL Server AlwaysOn (listener). Ora si ha il controllo dell'accessibilità del listener e si può scegliere l'indirizzo IP con carico bilanciato da una subnet specifica nella rete virtuale.

Usando ILB sul listener, l'endpoint SQL Server (ad esempio, Server=tcp:ListenerName,1433;Database=DatabaseName) è accessibile solo per:

Servizi e macchine virtuali negli stessi servizi di rete virtuale e macchine virtuali da servizi di rete locali connessi e macchine virtuali da reti virtuali interconnesse

![ILB\_SQLAO\_NewPic](./media/load-balancer-configure-sqlao/sqlao1.jpg)


Il bilanciamento del carico interno può essere configurato solo tramite PowerShell.


## Aggiungere al servizio il bilanciamento del carico interno 

### Passaggio 1

Nell'esempio seguente, verrà configurata una rete virtuale che contiene una subnet denominata "Subnet-1":

	Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc

### Passaggio 2

Aggiungere gli endpoint con carico bilanciato per ILB in ogni macchina virtuale

	Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –
	DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

 	Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

Nell'esempio precedente, si dispone di 2 macchine virtuali denominate "sqlsvc1" e "sqlsvc2" in esecuzione nel servizio cloud "Sqlsvc". Dopo la creazione di ILB con l'opzione "DirectServerReturn", verranno aggiunti gli endpoint con carico bilanciato all’ILB per consentire a SQL di configurare i listener per i gruppi di disponibilità.

Per altre informazioni su come creare un gruppo di disponibilità AlwaysOn di SQL, vedere la [raccolta del portale](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx).



## Vedere anche

[Introduzione alla configurazione del bilanciamento del carico Internet](load-balancer-get-started-internet-arm-ps.md)

[Introduzione alla configurazione del bilanciamento del carico interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurare una modalità di distribuzione del bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=AcomDC_0824_2016-->