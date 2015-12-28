<properties 
   pageTitle="Come creare gruppi di sicurezza di rete in modalità classica mediante l'interfaccia della riga di comando di Azure | Microsoft Azure"
   description="Informazioni su come creare e distribuire gruppi di sicurezza di rete in modalità classica mediante l'interfaccia della riga di comando di Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# Come creare gruppi di sicurezza di rete (in modalità classica) nell'interfaccia della riga di comando di Azure

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]In questo articolo viene illustrato il modello di distribuzione classica. È inoltre possibile [creare gruppi di sicurezza di rete nel modello di distribuzione di Gestione risorse](virtual-networks-create-nsg-arm-cli.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

I comandi di esempio dell'interfaccia della riga di comando di Azure riportati di seguito prevedono un ambiente semplice già creato in base allo scenario precedente. Se si desidera eseguire i comandi illustrati in questo documento, creare prima di tutto l'ambiente di testing [creando una rete virtuale](virtual-networks-create-vnet-classic-cli).

## Come creare il gruppo di sicurezza di rete per la subnet front-end
Per creare un gruppo di sicurezza di rete denominato *NSG-FrontEnd* in base allo scenario precedente, seguire questa procedura.

1. Se l'interfaccia della riga di comando di Azure non è mai stata usata, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](xplat-cli-install.md) e seguire le istruzioni fino al punto in cui si selezionano l'account e la sottoscrizione di Azure.

2. Eseguire il comando **azure config mode** per passare alla modalità classica, come illustrato di seguito.

		azure config mode asm

	Output previsto:

		info:    New mode is asm

3. Eseguire il comando **azure network nsg create** per creare un gruppo di sicurezza di rete.

		azure network nsg create -l uswest -n NSG-FrontEnd

	Output previsto:

		info:    Executing command network nsg create
		info:    Creating a network security group "NSG-FrontEnd"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Name                            : NSG-FrontEnd
		data:    Location                        : West US
		data:    Security group rules:
		data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
		ity  Default
		data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
		---  -------
		data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
		     true   
		data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
		     true   
		data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
		     true   
		data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
		     true   
		data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
		     true   
		data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
		     true   
		info:    network nsg create command OK

	Parametri:

	- **-l (o --location)**. Area di Azure in cui verrà creato il nuovo gruppo di sicurezza di rete. Per questo scenario, *westus*.
	- **-n (o --name)**. Nome per il nuovo gruppo di sicurezza di rete. Per questo scenario, *NSG-FrontEnd*.

4. Eseguire il comando **azure network nsg rule create** per creare una regola che consenta l'accesso alla porta 3389 (RDP) da Internet.

		azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389

	Output previsto:

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Creating a network security rule "rdp-rule"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Name                            : rdp-rule
		data:    Source address prefix           : INTERNET
		data:    Source Port                     : *
		data:    Destination address prefix      : *
		data:    Destination Port                : 3389
		data:    Protocol                        : TCP
		data:    Type                            : Inbound
		data:    Action                          : Allow
		data:    Priority                        : 100
		info:    network nsg rule create command OK

	Parametri:

	- **-a (o --nsg-name)**. Nome del gruppo di sicurezza di rete in cui verrà creata la regola. Per questo scenario, *NSG-FrontEnd*.
	- **-n (o --name)**. Nome per la nuova regola. Per questo scenario, *rdp-rule*.
	- **-c (o --action)**. Livello di accesso per la regola (Deny o Allow).
	- **-p (o --protocol)**. Protocollo (Tcp, Udp o *) per la regola.
	- **-r (o --type)**. Direzione di connessione (Inbound o Outbound).
	- **-y (o --priority)**. Priorità per la regola.
	- **-f (o --source-address-prefix)**. Prefisso dell'indirizzo di origine in CIDR o con tag predefiniti.
	- **-o (o --source-port-range)**. Porta o intervallo di porte di origine.
	- **-e (o --destination-address-prefix)**. Prefisso dell'indirizzo di destinazione in CIDR o con tag predefiniti.
	- **-u (o --destination-port-range)**. Porta o intervallo di porte di destinazione.	

5. Eseguire il comando **azure network nsg rule create** per creare una regola che consenta l'accesso alla porta 80 (HTTP) da Internet.

		azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80

	Output previsto:

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Creating a network security rule "web-rule"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Name                            : web-rule
		data:    Source address prefix           : INTERNET
		data:    Source Port                     : *
		data:    Destination address prefix      : *
		data:    Destination Port                : 80
		data:    Protocol                        : TCP
		data:    Type                            : Inbound
		data:    Action                          : Allow
		data:    Priority                        : 200
		info:    network nsg rule create command OK

6. Eseguire il comando **azure network nsg subnet add** per collegare il gruppo di sicurezza di rete alla subnet front-end.

		azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd 

	Output previsto:

		info:    Executing command network nsg subnet add
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Looking up the subnet "FrontEnd"
		info:    Looking up network configuration
		info:    Creating a network security group "NSG-FrontEnd"
		info:    network nsg subnet add command OK

## Come creare il gruppo di sicurezza di rete per la subnet back-end
Per creare un gruppo di sicurezza di rete denominato *NSG-BackEnd* in base allo scenario precedente, seguire questa procedura.

3. Eseguire il comando **azure network nsg create** per creare un gruppo di sicurezza di rete.

		azure network nsg create -l uswest -n NSG-BackEnd

	Output previsto:

		info:    Executing command network nsg create
		info:    Creating a network security group "NSG-BackEnd"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Name                            : NSG-BackEnd
		data:    Location                        : West US
		data:    Security group rules:
		data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
		ity  Default
		data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
		---  -------
		data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
		     true   
		data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
		     true   
		data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
		     true   
		data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
		     true   
		data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
		     true   
		data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
		     true   
		info:    network nsg create command OK

	Parametri:

	- **-l (o --location)**. Area di Azure in cui verrà creato il nuovo gruppo di sicurezza di rete. Per questo scenario, *westus*.
	- **-n (o --name)**. Nome per il nuovo gruppo di sicurezza di rete. Per questo scenario, *NSG-FrontEnd*.

4. Eseguire il comando **azure network nsg rule create** per creare una regola che consenta l'accesso alla porta 1433 (SQL) dalla subnet front-end.

		azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433

	Output previsto:

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-BackEnd"
		info:    Creating a network security rule "sql-rule"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Name                            : sql-rule
		data:    Source address prefix           : 192.168.1.0/24
		data:    Source Port                     : *
		data:    Destination address prefix      : *
		data:    Destination Port                : 1433
		data:    Protocol                        : TCP
		data:    Type                            : Inbound
		data:    Action                          : Allow
		data:    Priority                        : 100
		info:    network nsg rule create command OK


5. Eseguire il comando **azure network nsg rule create** per creare una regola che neghi l'accesso a Internet.

		azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80

	Output previsto:

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-BackEnd"
		info:    Creating a network security rule "web-rule"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Name                            : web-rule
		data:    Source address prefix           : *
		data:    Source Port                     : *
		data:    Destination address prefix      : INTERNET
		data:    Destination Port                : 80
		data:    Protocol                        : TCP
		data:    Type                            : Outbound
		data:    Action                          : Deny
		data:    Priority                        : 200
		info:    network nsg rule create command OK

6. Eseguire il comando **azure network nsg subnet add** per collegare il gruppo di sicurezza di rete alla subnet back-end.

		azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd 

	Output previsto:

		info:    Executing command network nsg subnet add
		info:    Looking up the network security group "NSG-BackEndX"
		info:    Looking up the subnet "BackEnd"
		info:    Looking up network configuration
		info:    Creating a network security group "NSG-BackEndX"
		info:    network nsg subnet add command OK

<!---HONumber=AcomDC_1217_2015-->