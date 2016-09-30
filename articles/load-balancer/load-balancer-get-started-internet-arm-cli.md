<properties 
   pageTitle="Creare un servizio di bilanciamento del carico Internet in Gestione risorse mediante l'interfaccia della riga di comando di Azure | Microsoft Azure"
   description="Informazioni su come creare un servizio di bilanciamento del carico Internet in Gestione risorse mediante l'interfaccia della riga di comando di Azure."
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# Introduzione alla creazione di un servizio di bilanciamento del carico interno tramite l’interfaccia di riga di comando di Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] Questo articolo illustra il modello di distribuzione Gestione risorse. Vedere [Informazioni su come creare un servizio di bilanciamento del carico Internet tramite la distribuzione classica](load-balancer-get-started-internet-classic-portal.md)


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## Distribuzione della soluzione tramite l'interfaccia della riga di comando di Azure

La procedura seguente illustra come creare un servizio di bilanciamento del carico Internet usando Azure Resource Manager con l'interfaccia della riga di comando di Azure. Con Azure Resource Manager ogni risorsa viene creata e configurata singolarmente e quindi integrata per creare una risorsa.

Creare e configurare gli oggetti seguenti per distribuire un servizio di bilanciamento del carico:

- Configurazione di IP front-end: contiene gli indirizzi IP pubblici per il traffico di rete in ingresso.
- Pool di indirizzi back-end: contiene interfacce di rete (NIC) per le macchine virtuali per la ricezione di traffico di rete dal servizio di bilanciamento del carico.
- Regole di bilanciamento del carico: contengono regole per il mapping di una porta pubblica nel servizio di bilanciamento del carico alle porte nel pool di indirizzi back-end.
- Regole NAT in ingresso: contengono regole per il mapping di una porta pubblica nel servizio di bilanciamento del carico a una porta per una macchina virtuale specifica nel pool di indirizzi back-end.
- Probe: contengono probe di integrità usati per verificare la disponibilità di istanze di macchine virtuali nel pool di indirizzi back-end.

Per altre informazioni, vedere [Supporto di Gestione risorse di Azure per il servizio di bilanciamento del carico](load-balancer-arm.md).

## Configurare l'interfaccia della riga di comando per l'uso di Resource Manager

1. Se l'interfaccia della riga di comando di Azure non è mai stata usata, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../../articles/xplat-cli-install.md) e seguire le istruzioni fino al punto in cui si selezionano l'account e la sottoscrizione di Azure.

2. Eseguire il comando **azure config mode** per passare alla modalità Gestione risorse, come illustrato di seguito.

        azure config mode arm

    Output previsto:

        info:    New mode is arm

## Creare una rete virtuale e un indirizzo IP pubblico per il pool di indirizzi IP front-end

1. Creare una rete virtuale (VNet) denominata *NRPVnet* nella località Stati Uniti orientali usando un gruppo di risorse denominato *NRPRG*.

        azure network vnet create NRPRG NRPVnet eastUS -a 10.0.0.0/16

    Creare una subnet denominata *NRPVnetSubnet* con un blocco CIDR di 10.0.0.0/24 in *NRPVnet*.

        azure network vnet subnet create NRPRG NRPVnet NRPVnetSubnet -a 10.0.0.0/24

2. Creare un indirizzo IP pubblico denominato *NRPPublicIP* che dovrà essere usato da un pool di indirizzi IP front-end con nome DNS *loadbalancernrp.eastus.cloudapp.azure.com*. Il comando seguente usa il tipo di allocazione statica e un timeout di inattività pari a 4 minuti.

        azure network public-ip create -g NRPRG -n NRPPublicIP -l eastus -d loadbalancernrp -a static -i 4

    >[AZURE.IMPORTANT] Il servizio di bilanciamento del carico userà l'etichetta di dominio dell'indirizzo IP pubblico come nome di dominio completo. Questa è una differenza rispetto alla distribuzione classica, che usa il servizio cloud come nome di dominio completo del servizio di bilanciamento del carico. In questo esempio il nome di dominio completo è *loadbalancernrp.eastus.cloudapp.azure.com*.

## Creare un servizio di bilanciamento del carico

Il comando seguente crea un servizio di bilanciamento del carico denominato *NRPlb* nel gruppo di risorse *NRPRG* nella località *Stati Uniti orientali* di Azure.

    azure network lb create NRPRG NRPlb eastus

## Creare un pool di indirizzi IP front-end e un pool di indirizzi back-end

Questo esempio crea il pool di indirizzi IP front-end che riceve il traffico in ingresso per il servizio di bilanciamento del carico e il pool di indirizzi IP back-end a cui il pool front-end invia il traffico di rete sottoposto a bilanciamento del carico.

1. Creare un pool di indirizzi IP front-end che associa l'indirizzo IP pubblico creato nel passaggio precedente e il servizio di bilanciamento del carico.

        azure network lb frontend-ip create nrpRG NRPlb NRPfrontendpool -i nrppublicip

2. Configurare un pool di indirizzi back-end usato per ricevere il traffico in ingresso dal pool di indirizzi IP front-end.

        azure network lb address-pool create NRPRG NRPlb NRPbackendpool

## Creare regole di bilanciamento del carico, regole NAT e probe

Questo esempio crea gli elementi seguenti.

- Regola NAT per la conversione di tutto il traffico in ingresso sulla porta 21 alla porta 22<sup>1</sup>
- Regola NAT per la conversione di tutto il traffico in ingresso sulla porta 23 alla porta 22.
- Regola del servizio di bilanciamento del carico per il bilanciamento di tutto il traffico in ingresso sulla porta 80 verso la porta 80 negli indirizzi nel pool back-end.
- Regola probe per il controllo dello stato di integrità in una pagina denominata *HealthProbe.aspx*.

<sup>1</sup> Le regole NAT vengono associate a un'istanza di macchina virtuale specifica dietro al servizio di bilanciamento del carico. Il traffico di rete in arrivo sulla porta 21 viene inviato a una specifica macchina virtuale sulla porta 22 associata a questa regola NAT. È necessario specificare un protocollo (UDP o TCP) per una regola NAT. Entrambi i protocolli non possono essere assegnati alla stessa porta.

1. Creare le regole NAT.

        azure network lb inbound-nat-rule create -g nrprg -l nrplb -n ssh1 -p tcp -f 21 -b 22
        azure network lb inbound-nat-rule create -g nrprg -l nrplb -n ssh2 -p tcp -f 23 -b 22

    Parametri
    * **-g**: nome del gruppo di risorse.
    * **-l**: nome del servizio di bilanciamento del carico.
    * **-n**: nome della risorsa che indica se si tratta di una regola NAT, una regola probe o una regola del servizio di bilanciamento del carico.
    * **-p**: protocollo. Può essere TCP o UDP.
    * **-f**: porta front-end da usare. Il comando probe usa -f per definire il percorso probe
    * **-b**: porta back-end da usare

2. Creare una regola del servizio di bilanciamento del carico.

        azure network lb rule create nrprg nrplb lbrule -p tcp -f 80 -b 80 -t NRPfrontendpool -o NRPbackendpool

3. Creare un probe di integrità.

        azure network lb probe create -g nrprg -l nrplb -n healthprobe -p "http" -o 80 -f healthprobe.aspx -i 15 -c 4

    Parametri
    * **-g**: gruppo di risorse
    * **-l**: nome del set di bilanciamento del carico
    * **-n**: nome del probe di integrità
    * **-p**: protocollo usato dal probe di integrità
    * **-i**: intervallo di probe in secondi
    * **-c**: numero di controlli

4. Verificare le impostazioni.

        azure network lb show nrprg nrplb

    Output previsto:

        info:    Executing command network lb show
        + Looking up the load balancer "nrplb"
        + Looking up the public ip "NRPPublicIP"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb
        data:    Name                            : nrplb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : eastus
        data:    Provisioning State              : Succeeded
        data:    Frontend IP configurations:
        data:      Name                          : NRPfrontendpool
        data:      Provisioning state            : Succeeded
        data:      Public IP address id          : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/publicIPAddresses/NRPPublicIP
        data:      Public IP allocation method   : Static
        data:      Public IP address             : 40.114.13.145
        data:
        data:    Backend address pools:
        data:      Name                          : NRPbackendpool
        data:      Provisioning state            : Succeeded
        data:
        data:    Load balancing rules:
        data:      Name                          : HTTP
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 80
        data:      Backend port                  : 80
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:      Backend address pool          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:
        data:    Inbound NAT rules:
        data:      Name                          : ssh1
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 21
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:      Name                          : ssh2
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 23
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:    Probes:
        data:      Name                          : healthprobe
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Http
        data:      Port                          : 80
        data:      Interval in seconds           : 15
        data:      Number of probes              : 4
        data:
        info:    network lb show command OK

## Creare NIC

È necessario creare schede di interfaccia di rete (NIC) o modificare quelle esistenti e associarle a regole NAT, regole del servizio di bilanciamento del carico e probe.

1. Creare una NIC denominata *lb-nic1-be* e associarla alla regola NAT *rdp1* e quindi al pool di indirizzi back-end *NRPbackendpool*.

        azure network nic create -g nrprg -n lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

    Parametri

    * **-g**: nome del gruppo di risorse.
    * **-n**: nome della risorsa NIC.
    * **--subnet-name**: nome della subnet.
    * **--subnet-vnet-name**: nome della rete virtuale.
    * **-d**: ID del pool di risorse back-end. Inizia con /subscription/{subscriptionID/resourcegroups/<resourcegroup-name>/providers/Microsoft.Network/loadbalancers/<load-balancer-name>/backendaddresspools/<name-of-the-backend-pool>
    * **-e**: ID della regola NAT da associare alla risorsa NIC. Inizia con /subscriptions/####################################/resourceGroups/<resourcegroup-name>/providers/Microsoft.Network/loadBalancers/<load-balancer-name>/inboundNatRules/<nat-rule-name>

    Output previsto:

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. Creare una NIC denominata *lb-nic2-be* e associarla alla regola NAT *rdp2* e quindi al pool di indirizzi back-end *NRPbackendpool*.

        azure network nic create -g nrprg -n lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

3. Creare una macchina virtuale denominata *web1* e associarla alla NIC denominata *lb-nic1-be*. Un account di archiviazione denominato *web1nrp* è stato creato prima dell'esecuzione del comando seguente.

        azure vm create --resource-group nrprg --name web1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] Le macchine virtuali in un servizio di bilanciamento del carico devono trovarsi nello stesso set di disponibilità. Usare `azure availset create` per creare un set di disponibilità.

    L'output dovrebbe essere simile al seguente:

        info:    Executing command vm create
        + Looking up the VM "web1"
        Enter username: azureuser
        Enter password for azureuser: *********
        Confirm password: *********
        info:    Using the VM Size "Standard_A1"
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account web1nrp
        + Looking up the availability set "nrp-avset"
        info:    Found an Availability set "nrp-avset"
        + Looking up the NIC "lb-nic1-be"
        info:    Found an existing NIC "lb-nic1-be"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet" in the NIC "lb-nic1-be"
        info:    This is a NIC without publicIP configured
        + Creating VM "web1"
        info:    vm create command OK

    >[AZURE.NOTE] Il messaggio informativo **This is a NIC without publicIP configured** è un comportamento previsto, perché la NIC creata per il servizio di bilanciamento del carico si connette a Internet tramite l'indirizzo IP pubblico del servizio di bilanciamento del carico.

    Poiché la NIC *lb-nic1-be* è associata alla regola NAT *rdp1*, è possibile connettersi a *web1* mediante RDP tramite la porta 3441 nel servizio di bilanciamento del carico.

4. Creare una macchina virtuale denominata *web2* e associarla alla NIC denominata *lb-nic2-be*. Un account di archiviazione denominato *web1nrp* è stato creato prima dell'esecuzione del comando seguente.

        azure vm create --resource-group nrprg --name web2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## Aggiornare un bilanciamento del carico esistente

È possibile aggiungere regole che fanno riferimento a un servizio di bilanciamento del carico esistente. Nell'esempio seguente una nuova regola di bilanciamento del carico viene aggiunta a un servizio di bilanciamento del carico **NRPlb** esistente

    azure network lb rule create -g nrprg -l nrplb -n lbrule2 -p tcp -f 8080 -b 8051 -t frontendnrppool -o NRPbackendpool

Parametri

* **-g**: nome del gruppo di risorse.
* **-l**: nome del servizio di bilanciamento del carico.
* **-n**: nome della regola di bilanciamento del carico
* **-p**: protocollo
* **-f**: porta front-end
* **-b**: porta back-end
* **-t**: nome del pool front-end
* **-b**: nome del pool back-end

## Eliminare un servizio di bilanciamento del carico

Per rimuovere un servizio di bilanciamento del carico, usare il comando seguente:

    azure network lb delete -g nrprg -n nrplb

Dove **nrprg** è il gruppo di risorse e **nrplb** è il nome del servizio di bilanciamento del carico.

## Passaggi successivi

[Introduzione alla configurazione del bilanciamento del carico interno](load-balancer-get-started-ilb-arm-cli.md)

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)

<!----HONumber=AcomDC_0914_2016-->
