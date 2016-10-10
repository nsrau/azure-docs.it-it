<properties
   pageTitle="Creare un servizio di bilanciamento del carico interno con l'interfaccia della riga di comando di Resource Manager | Microsoft Azure"
   description="Informazioni su come creare un servizio di bilanciamento del carico interno con l'interfaccia della riga di comando di Resource Manager"
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

# Creare un servizio di bilanciamento del carico interno tramite l'interfaccia della riga di comando di Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](load-balancer-get-started-ilb-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## Distribuire la soluzione tramite l'interfaccia della riga di comando di Azure

La procedura seguente illustra come creare un servizio di bilanciamento del carico Internet usando Azure Resource Manager con l'interfaccia della riga di comando di Azure. Con Azure Resource Manager ogni risorsa viene creata e configurata singolarmente e quindi integrata per creare una risorsa.

È necessario creare e configurare gli oggetti seguenti per distribuire un servizio di bilanciamento del carico.

- **Configurazione di IP front-end**: contiene gli indirizzi IP pubblici per il traffico di rete in ingresso
- **Pool di indirizzi back-end**: contiene interfacce di rete (NIC) per le macchine virtuali per la ricezione di traffico di rete dal servizio di bilanciamento del carico
- **Regole di bilanciamento del carico**: contiene le regole che eseguono il mapping di una porta pubblica sul servizio di bilanciamento del carico alla porta nel pool di indirizzi back-end
- **Regole NAT in ingresso**: contiene le regole che eseguono il mapping di una porta pubblica sul servizio di bilanciamento del carico a una porta per una macchina virtuale specifica nel pool di indirizzi back-end
- **Probe**: contengono probe di integrità usati per verificare la disponibilità di istanze di macchine virtuali nel pool di indirizzi back-end

Per altre informazioni, vedere [Supporto di Azure Resource Manager per Load Balancer](load-balancer-arm.md).

## Configurare l'interfaccia della riga di comando per l'uso di Resource Manager

1. Se l'interfaccia della riga di comando di Azure non è mai stata usata, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../../articles/xplat-cli-install.md). Seguire le istruzioni fino al punto in cui si seleziona l'account Azure e la sottoscrizione.

2. Eseguire il comando **azure config mode** per passare alla modalità Resource Manager, come illustrato di seguito:

        azure config mode arm

    Output previsto:

        info:    New mode is arm

## Per creare un servizio di bilanciamento del carico interno, attenersi alla procedura dettagliata descritta di seguito

1. Accedere ad Azure.

        azure login

    Quando richiesto, immettere le credenziali di Azure.

2. Attivare la modalità Azure Resource Manager per gli strumenti di comando.

        azure config mode arm

## Creare un gruppo di risorse

Tutte le risorse in Azure Resource Manager vengono associate a un gruppo di risorse. Se non è ancora stato fatto, creare un gruppo di risorse.

    azure group create <resource group name> <location>

## Creare un set di bilanciamento del carico interno

1. Creare un bilanciamento del carico interno

    Nello scenario seguente, viene creato un gruppo di risorse denominato nrprg viene creato nell'area Stati Uniti orientali.

        azure network lb create -n nrprg -l eastus

    >[AZURE.NOTE] Tutte le risorse per un servizio di bilanciamento del carico interno, ad esempio reti virtuali e subnet della rete virtuale, devono essere nello stesso gruppo di risorse e nella stessa area.

2. Creare un indirizzo IP di front-end per il servizio di bilanciamento del carico interno.

    L'indirizzo IP usato deve essere compreso nell'intervallo della subnet della rete virtuale.

        azure network lb frontend-ip create -g nrprg -l ilbset -n feilb -a 10.0.0.7 -e nrpvnetsubnet -m nrpvnet

    Parametri utilizzati:

    * **-g**: gruppo di risorse
    * **-l**: nome del set di bilanciamento del carico interno
    * **-n**: nome dell'IP front-end
    * **-a**: indirizzo IP privato all'interno dell'intervallo di subnet
    * **-e**: nome della subnet
    * **-m**: nome della rete virtuale

3. Creare un pool di indirizzi back-end.

        azure network lb address-pool create -g nrprg -l ilbset -n beilb

    Parametri utilizzati:

    * **-g**: gruppo di risorse
    * **-l**: nome del set di bilanciamento del carico interno
    * **-n**: nome del pool di indirizzi back-end

    Dopo aver definito un indirizzo IP front-end e un pool di indirizzi back-end, è possibile creare regole del servizio di bilanciamento del carico, regole NAT in ingresso e probe di integrità personalizzati.

4. Creare una regola del servizio di bilanciamento del carico per il servizio di bilanciamento del carico interno.

    Attenendosi alla procedura precedente, il comando crea una regola del servizio di bilanciamento del carico in ascolto sulla porta 1433 nel pool front-end e invia traffico di rete con bilanciamento del carico al pool di indirizzi back-end usando ancora la porta 1433.

        azure network lb rule create -g nrprg -l ilbset -n ilbrule -p tcp -f 1433 -b 1433 -t feilb -o beilb

    Parametri utilizzati:

    * **-g**: gruppo di risorse
    * **-l**: nome del set di bilanciamento del carico interno
    * **-n**: nome della regola di bilanciamento del carico
    * **-p**: protocollo usato per la regola
    * **-f**: porta in attesa del traffico di rete in ingresso nel front-end del bilanciamento del carico
    * **-b**: porta che riceve il traffico di rete nel pool di indirizzi back-end

5. Creare regole NAT in ingresso.

    Le regole NAT in ingresso vengono usate per creare endpoint in un servizio di bilanciamento del carico che viene inviato a un'istanza di macchina virtuale specifica. Nei passaggi precedenti sono state create due regole NAT per il desktop remoto.

        azure network lb inbound-nat-rule create -g nrprg -l ilbset -n NATrule1 -p TCP -f 5432 -b 3389

        azure network lb inbound-nat-rule create -g nrprg -l ilbset -n NATrule2 -p TCP -f 5433 -b 3389

    Parametri utilizzati:

    * **-g**: gruppo di risorse
    * **-l**: nome del set di bilanciamento del carico interno
    * **-n**: nome della regola NAT in ingresso
    * **-p**: protocollo usato per la regola
    * **-f**: porta in attesa del traffico di rete in ingresso nel front-end del bilanciamento del carico
    * **-b**: porta che riceve il traffico di rete nel pool di indirizzi back-end

5. Creare probe di integrità per il servizio di bilanciamento del carico.

    Un probe di integrità controlla tutte le istanze di una macchina virtuale per assicurarsi che possano inviare il traffico di rete. L'istanza della macchina virtuale con controlli di probe falliti non viene rimossa dal servizio di bilanciamento del carico fino a quando non è nuovamente online e il controllo dei probe ne certifica l'integrità.

        azure network lb probe create -g nrprg -l ilbset -n ilbprobe -p tcp -i 300 -c 4

    Parametri utilizzati:

    * **-g**: gruppo di risorse
    * **-l**: nome del set di bilanciamento del carico interno
    * **-n**: nome del probe di integrità
    * **-p**: protocollo usato dal probe di integrità
    * **-i**: intervallo di probe in secondi
    * **-c**: numero di controlli


    >[AZURE.NOTE] La piattaforma Microsoft Azure usa un indirizzo IPv4 statico e instradabile pubblicamente per un'ampia gamma di scenari di amministrazione. L'indirizzo IP è 168.63.129.16. Questo indirizzo IP non deve essere bloccato da alcun firewall, perché potrebbe causare un comportamento imprevisto. Per quanto riguarda il bilanciamento del carico interno di Azure, questo indirizzo IP viene usato da probe di monitoraggio del servizio di bilanciamento del carico per determinare lo stato di integrità delle macchine virtuali in un set con carico bilanciato. Se si usa un gruppo di sicurezza di rete per limitare il traffico alle macchine virtuali di Azure in un set con carico bilanciato internamente o lo si applica a una subnet di rete virtuale, assicurarsi di aggiungere una regola di sicurezza di rete per consentire il traffico dall'indirizzo 168.63.129.16.

## Creare NIC

È necessario creare schede di interfaccia di rete (NIC) o modificare quelle esistenti e associarle a regole NAT, regole del servizio di bilanciamento del carico e probe.

1. Creare una NIC denominata *lb-nic1-be* e associarla alla regola NAT *rdp1* e al pool di indirizzi back-end *beilb*.

        azure network nic create -g nrprg -n lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

    Parametri

    * **-g**: nome del gruppo di risorse
    * **-n**: nome della risorsa NIC
    * **--subnet-name**: nome della subnet
    * **--subnet-vnet-name**: nome della rete virtuale
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

2. Creare una NIC denominata *lb-nic2-be* e associarla alla regola NAT *rdp2* e al pool di indirizzi back-end *beilb*.

        azure network nic create -g nrprg -n lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

3. Creare una macchina virtuale denominata *DB1* e associarla alla NIC denominata *lb-nic1-be*. Un account di archiviazione denominato *web1nrp* è stato creato prima dell'esecuzione del comando seguente:

        azure vm create --resource-group nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] Le macchine virtuali in un servizio di bilanciamento del carico devono trovarsi nello stesso set di disponibilità. Usare `azure availset create` per creare un set di disponibilità.

4. Creare una macchina virtuale (VM) denominata *DB2* e associarla alla NIC denominata *lb-nic2-be*. Un account di archiviazione denominato *web1nrp* è stato creato prima dell'esecuzione del comando seguente.

        azure vm create --resource-group nrprg --name DB2 --location eastus --vnet-    name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## Eliminare un servizio di bilanciamento del carico

Per rimuovere un servizio di bilanciamento del carico, usare il comando seguente:

    azure network lb delete -g nrprg -n ilbset

In questo esempio, **nrprg** rappresenta il gruppo di risorse e **ilbset** è il nome del servizio di bilanciamento del carico interno.


## Passaggi successivi

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico usando l'affinità dell'IP di origine](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0928_2016-->