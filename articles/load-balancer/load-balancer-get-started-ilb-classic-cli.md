<properties
   pageTitle="Creare un servizio di bilanciamento del carico interno usando l’interfaccia della riga di comando di Azure nel modello di distribuzione classica | Microsoft Azure"
   description="Informazioni su come creare un servizio di bilanciamento del carico interno usando l’interfaccia della riga di comando di Azure nel modello di distribuzione classica"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="sewhee" />

# Introduzione alla creazione di un servizio di bilanciamento del carico interno (classico) tramite l’interfaccia di riga di comando di Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] Informazioni su come [eseguire questa procedura con il modello di Resource Manager](load-balancer-get-started-ilb-arm-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


## Per creare un set con servizio di bilanciamento del carico interno per le macchine virtuali

Per creare un set con servizio di bilanciamento del carico e i server che invieranno il traffico a esso, è necessario eseguire le operazioni seguenti:

1. Creare un'istanza della funzionalità di bilanciamento del carico interno che sarà l'endpoint del traffico in ingresso da configurare con carico bilanciato tra i server di un set con carico bilanciato.

1. Aggiungere gli endpoint corrispondenti alle macchine virtuali che riceveranno il traffico in ingresso.

1. Configurare i server che invieranno il traffico con carico bilanciato all'indirizzo IP virtuale (indirizzo VIP) dell'istanza del bilanciamento del carico interno.

## Procedura dettagliata sulla creazione di un servizio di bilanciamento del carico interno tramite CLI

In questa guida viene illustrato come creare un servizio di bilanciamento del carico interno in base allo scenario precedente.

1. Se l'interfaccia della riga di comando di Azure non è mai stata usata, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../../articles/xplat-cli-install.md) e seguire le istruzioni fino al punto in cui si selezionano l'account e la sottoscrizione di Azure.

2. Eseguire il comando **azure config mode** per passare alla modalità classica, come illustrato di seguito.

		azure config mode asm

	Output previsto:

		info:    New mode is asm


## Creazione dell’endpoint e del set del servizio di bilanciamento del carico

Lo scenario presuppone le macchine virtuali "DB1" e "DB2" in un servizio cloud denominato "mytestcloud". Entrambe le macchine virtuali utilizzano una rete virtuale denominata "my testvnet" con subnet "subnet-1".

In questa guida verrà creato un set del servizio di bilanciamento del carico interno utilizzando la porta 1433 come porta privata e la porta 1433 come porta locale.

Si tratta di uno scenario comune in cui si dispone di macchine virtuali di SQL nel back-end che utilizzano un servizio di bilanciamento del carico interno per garantire che i server di database non siano esposti direttamente tramite un indirizzo IP pubblico.


### Passaggio 1

Creare un set di bilanciamento del carico interno utilizzando `azure network service internal-load-balancer add`.

	 azure service internal-load-balancer add -r mytestcloud -n ilbset -t subnet-1 -a 192.168.2.7

Parametri utilizzati:

**- r** -nome del servizio cloud<BR> **- n** -nome del servizio di bilanciamento del carico interno<BR> **-t** -nome della subnet (stessa subnet per le macchine virtuali che verranno aggiunti al servizio di bilanciamento del carico interno)<BR> **-** (facoltativo) aggiungere un indirizzo IP privato statico<BR>

Per ulteriori informazioni, vedere `azure service internal-load-balancer --help`.

È possibile controllare le proprietà del servizio di bilanciamento del carico interno utilizzando il comando `azure service internal-load-balancer list` *nome del servizio cloud*.

Qui di seguito un esempio di output:

	azure service internal-load-balancer list my-testcloud
	info:    Executing command service internal-load-balancer list
	+ Getting cloud service deployment
	data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
	data:    ------  -------  ----------  -----------------------------
	data:    ilbset  Private  subnet-1    192.168.2.7
	info:    service internal-load-balancer list command OK


## Passaggio 2

Configurare il set di bilanciamento del carico interno quando si aggiunge il primo endpoint. Associare l'endpoint, la macchina virtuale e la porta probe per il set di bilanciamento del carico interno in questo passaggio.

	azure vm endpoint create db1 1433 -k 1433 tcp -t 1433 -r tcp -e 300 -f 600 -i ilbset

Parametri utilizzati:

**-k** - porta della macchina virtuale locale<BR> **-t** - porta probe<BR> **- r** - protocollo probe<BR> **-e** -intervallo di probe in secondi<BR> **-f** - intervallo di timeout in secondi <BR> **-i** -nome del servizio di bilanciamento del carico interno <BR>


## Passaggio 3

Verificare la configurazione del servizio di bilanciamento del carico utilizzando `azure vm show` *nome macchina virtuale*.

	azure vm show DB1

L'output sarà:

		azure vm show DB1
	info:    Executing command vm show
	+ Getting virtual machines
	data:    DNSName "mytestcloud.cloudapp.net"
	data:    Location "East US 2"
	data:    VMName "DB1"
	data:    IPAddress "192.168.2.4"
	data:    InstanceStatus "ReadyRole"
	data:    InstanceSize "Standard_D1"
	data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
	data:    OSDisk hostCaching "ReadWrite"
	data:    OSDisk name "db1-DB1-0-201511120457370846"
	data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
	data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
	data:    OSDisk operatingSystem "Windows"
	data:    OSDisk iOType "Standard"
	data:    ReservedIPName ""
	data:    VirtualIPAddresses 0 address "137.116.64.107"
	data:    VirtualIPAddresses 0 name "db1ContractContract"
	data:    VirtualIPAddresses 0 isDnsProgrammed true
	data:    VirtualIPAddresses 1 address "192.168.2.7"
	data:    VirtualIPAddresses 1 name "ilbset"
	data:    Network Endpoints 0 localPort 5986
	data:    Network Endpoints 0 name "PowerShell"
	data:    Network Endpoints 0 port 5986
	data:    Network Endpoints 0 protocol "tcp"
	data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"
	data:    Network Endpoints 0 enableDirectServerReturn false
	data:    Network Endpoints 1 localPort 3389
	data:    Network Endpoints 1 name "Remote Desktop"
	data:    Network Endpoints 1 port 60173
	data:    Network Endpoints 1 protocol "tcp"
	data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
	data:    Network Endpoints 1 enableDirectServerReturn false
	data:    Network Endpoints 2 localPort 1433
	data:    Network Endpoints 2 name "tcp-1433-1433"
	data:    Network Endpoints 2 port 1433
	data:    Network Endpoints 2 loadBalancerProbe port 1433
	data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
	data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
	data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
	data:    Network Endpoints 2 protocol "tcp"
	data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
	data:    Network Endpoints 2 enableDirectServerReturn false
	data:    Network Endpoints 2 loadBalancerName "ilbset"
	info:    vm show command OK


## Creare un endpoint di desktop remoto per una macchina virtuale

È possibile creare un endpoint di desktop remoto per inoltrare il traffico di rete da una porta pubblica a una porta locale per una macchina virtuale specifica utilizzando `azure vm endpoint create`.

	azure vm endpoint create web1 54580 -k 3389


## Rimuovere la macchina virtuale dal servizio di bilanciamento del carico

È possibile rimuovere una macchina virtuale da un set di bilanciamento del carico interno impostato eliminando l'endpoint associato. Una volta rimosso l'endpoint, la macchina virtuale non appartiene più al set del servizio di bilanciamento del carico.

 Utilizzando l'esempio precedente, è possibile rimuovere l'endpoint creato per la macchina virtuale "DB1" dal servizio di bilanciamento del carico interno "ilbset" utilizzando il comando `azure vm endpoint delete`.

	azure vm endpoint delete DB1 tcp-1433-1433


Per ulteriori informazioni, vedere `azure vm endpoint --help`.


## Passaggi successivi

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico utilizzando l’affinità dell’IP di origine](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0824_2016-->