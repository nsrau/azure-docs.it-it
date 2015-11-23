<properties 
   pageTitle="Introduzione alla creazione del servizio di bilanciamento del carico Internet nel modello di distribuzione classica tramite l’interfaccia della riga di comando di Azure | Microsoft Azure"
   description="Informazioni su come creare un servizio di bilanciamento del carico Internet nel modello di distribuzione classica mediante l'interfaccia della riga di comando di Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/06/2015"
   ms.author="joaoma" />

# Introduzione alla creazione del servizio di bilanciamento del carico Internet (classico) nell’interfaccia della riga di comando di Azure CLI

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]In questo articolo viene illustrato il modello di distribuzione classica. Vedere [Informazioni su come creare un servizio di bilanciamento del carico Internet in Gestione risorse di Azure](load-balancer-get-started-internet-arm-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## Procedura dettagliata sulla creazione di un servizio di bilanciamento del carico Internet tramite CLI

In questa guida viene illustrato come creare un servizio di bilanciamento del carico Internet in base allo scenario precedente.

1. Se l'interfaccia della riga di comando di Azure non è mai stata usata, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](xplat-cli.md) e seguire le istruzioni fino al punto in cui si selezionano l'account e la sottoscrizione di Azure.

2. Eseguire il comando **azure config mode** per passare alla modalità classica, come illustrato di seguito.

		azure config mode asm

	Output previsto:

		info:    New mode is asm


## Creazione dell’endpoint e del set del servizio di bilanciamento del carico 

Lo scenario presuppone che le macchine virtuali "web1" e "web2" sono state create. In questa guida verrà creato un set del servizio di bilanciamento del carico utilizzando la porta 80 come porta pubblica e 80 come porta locale. Una porta probe è inoltre stata configurata sulla porta 80 e il set del servizio di bilanciamento del carico è stato chiamato "lbset"


### Passaggio 1 

Creare il primo endpoint e il set del servizio di bilanciamento del carico utilizzando `azure network vm endpoint create` per la macchina virtuale "web1"

	azure network endpoint create web1 80 -k 80 -o tcp -t 80 -b lbset 


## Passaggio 2 

Aggiungere una seconda macchina virtuale "web2" al set del servizio di bilanciamento del carico.

	azure network endpoint create web2 80 -k 80 -o tcp -t 80 -b lbset

## Passaggio 3 

Verificare la configurazione del servizio di bilanciamento del carico utilizzando `azure vm show`

	azure vm show web1

L'output sarà:

	data:    DNSName "contoso.cloudapp.net"
	data:    Location "East US"
	data:    VMName "web1"
	data:    IPAddress "10.0.0.5"
	data:    InstanceStatus "ReadyRole"
	data:    InstanceSize "Standard_D1"
	data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
	6-en.us-127GB.vhd"
	data:    OSDisk hostCaching "ReadWrite"
	data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
	data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
	/vhds/joaomatest-web1-2015-09-25.vhd"
	data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
	r-2012-R2-20150916-en.us-127GB.vhd"
	data:    OSDisk operatingSystem "Windows"
	data:    OSDisk iOType "Standard"
	data:    ReservedIPName ""
	data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
	data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
	data:    VirtualIPAddresses 0 isDnsProgrammed true
	data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
	data:    Network Endpoints 0 localPort 80
	data:    Network Endpoints 0 name "tcp-80-80"
	data:    Network Endpoints 0 port 80
	data:    Network Endpoints 0 loadBalancerProbe port 80
	data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
	data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
	data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
	data:    Network Endpoints 0 protocol "tcp"
	data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
	data:    Network Endpoints 0 enableDirectServerReturn false
	data:    Network Endpoints 1 localPort 5986
	data:    Network Endpoints 1 name "PowerShell"
	data:    Network Endpoints 1 port 5986
	data:    Network Endpoints 1 protocol "tcp"
	data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
	data:    Network Endpoints 1 enableDirectServerReturn false
	data:    Network Endpoints 2 localPort 3389
	data:    Network Endpoints 2 name "Remote Desktop"
	data:    Network Endpoints 2 port 58081
	info:    vm show command OK

## Creare un endpoint di desktop remoto per una macchina virtuale

È possibile creare un endpoint di desktop remoto per inoltrare il traffico di rete da una porta pubblica a una porta locale per una macchina virtuale specifica utilizzando `azure vm endpoint create`.

	azure vm endpoint create web1 54580 -k 3389 


## Rimuovere la macchina virtuale dal servizio di bilanciamento del carico

È necessario eliminare l'endpoint associato al set del servizio di bilanciamento del carico impostato dalla macchina virtuale. Una volta rimosso l'endpoint, la macchina virtuale non appartiene più al set del servizio di bilanciamento del carico.

 Utilizzando l'esempio precedente, è possibile rimuovere l'endpoint creato per la macchina virtuale "web1" dal servizio di bilanciamento del carico "lbset" utilizzando il comando `azure vm endpoint delete`.

	azure vm endpoint delete web1 tcp-80-80


>[AZURE.NOTE]È possibile esplorare più opzioni per gestire gli endpoint utilizzando il comando `azure vm endpoint --help`


## Passaggi successivi

[Introduzione alla configurazione del bilanciamento del carico interno](load-balancer-internal-getstarted.md)

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)

 

<!---HONumber=Nov15_HO3-->