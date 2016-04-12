<properties
   pageTitle="Esempi di configurazione di router ExpressRoute | Microsoft Azure"
   description="In questa pagina vengono forniti esempi di configurazione di router per router Cisco e Juniper."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/16/2016"
   ms.author="cherylmc"/>

# Esempi di configurazione del router per l'impostazione e la gestione NAT

Questa pagina fornisce gli esempi di configurazione NAT per i router Cisco ASA e Juniper SRX. Devono essere intesi come esempi a solo scopo informativo e non devono essere usati per altri scopi. È possibile rivolgersi al fornitore per ottenere le configurazioni appropriate per la rete in uso.

>[AZURE.IMPORTANT] Gli esempi inclusi in questa pagina devono essere intesi solo come linee guida. È necessario collaborare con il team di vendita/tecnico del fornitore e il team di rete per ottenere le configurazioni appropriate in base alle specifiche esigenze. Microsoft non offre supporto per i problemi relativi alle configurazioni elencate in questa pagina. È necessario contattare il fornitore del dispositivo per assistenza.

Gli esempi di configurazione di router riportati di seguito si applicano al peering pubblico di Azure e al peering Microsoft. Non è necessario configurare NAT per il peering privato di Azure. Per altre informazioni sul routing, vedere [Peering di ExpressRoute](expressroute-circuit-peerings.md) e [Requisiti NAT di ExpressRoute](expressroute-nat.md).

**Nota:** è necessario usare pool di IP NAT separati per la connettività a Internet ed ExpressRoute. L'uso dello stesso pool di IP NAT a livello di Internet ed ExpressRoute comporterà un routing asimmetrico e la perdita di connettività.

## Firewall Cisco ASA

### Configurazione PAT per il traffico dalla rete del cliente a Microsoft

    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>
    
    
    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem-range-1
      network-object <IP> <Subnet-Mask>
    
    object-group network on-prem-range-2
      network-object <IP> <Subnet-Mask>
    
    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2
    
    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range

### Configurazione PAT per il traffico da Microsoft alla rete del cliente

#### Interfacce e Direzione:
	Source Interface (where the traffic enters the ASA): inside
	Destination Interface (where the traffic exits the ASA): outside

#### Configurazione:
Pool NAT:

	object network outbound-PAT
		host <NAT-IP>

Server di destinazione:

	object network Customer-Network
		network-object <IP> <Subnet-Mask>

Gruppo dell’oggetto per gli indirizzi IP del cliente

	object-group network MSFT-Network-1
		network-object <MSFT-IP> <Subnet-Mask>
	
	object-group network MSFT-PAT-Networks
		network-object object MSFT-Network-1

Comandi NAT:

	nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network


## Router serie Juniper SRX 

### 1\. Creare interfacce Ethernet ridondanti per il cluster

	interfaces {
	    reth0 {
	        description "To Internal Network";
	        vlan-tagging;
	        redundant-ether-options {
	            redundancy-group 1;
	        }
	        unit 100 {
	            vlan-id 100;
	            family inet {
	                address <IP-Address/Subnet-mask>;
	            }
	        }
	    }
	    reth1 {
	        description "To Microsoft via Edge Router";
	        vlan-tagging;
	        redundant-ether-options {
	            redundancy-group 2;
	        }
	        unit 100 {
	            description "To Microsoft via Edge Router";
	            vlan-id 100;
	            family inet {
	                address <IP-Address/Subnet-mask>;
	            }
	        }
	    }
	}


### 2\. Creare due aree di sicurezza

 - Area attendibile per la rete interna e area non attendibile per la rete esterna esposta ai router perimetrali
 - Assegnare le interfacce appropriate alle aree
 - Abilitare i servizi nelle interfacce


	security { zones { security-zone Trust { host-inbound-traffic { system-services { ping; } protocols { bgp; } } interfaces { reth0.100; } } security-zone Untrust { host-inbound-traffic { system-services { ping; } protocols { bgp; } } interfaces { reth1.100; } } } }


### 3\. Creare criteri di sicurezza tra aree
 
	security {
	    policies {
	        from-zone Trust to-zone Untrust {
	            policy allow-any {
	                match {
	                    source-address any;
	                    destination-address any;
	                    application any;
	                }
	                then {
	                    permit;
	                }
	            }
	        }
	        from-zone Untrust to-zone Trust {
	            policy allow-any {
	                match {
	                    source-address any;
	                    destination-address any;
	                    application any;
	                }
	                then {
	                    permit;
	                }
	            }
	        }
	    }
	}


### 4\. Configurare i criteri NAT
 - Creare due pool NAT. Uno verrà usato per il traffico NAT in uscita verso Microsoft e l'altro per il traffico da Microsoft al cliente.
 - Creare regole NAT il traffico corrispondente

		security {
		    nat {
		        source {
		            pool SNAT-To-ExpressRoute {
		                routing-instance {
		                    External-ExpressRoute;
		                }
		                address {
		                    <NAT-IP-address/Subnet-mask>;
		                }
		            }
		            pool SNAT-From-ExpressRoute {
		                routing-instance {
		                    Internal;
		                }
		                address {
		                    <NAT-IP-address/Subnet-mask>;
		                }
		            }
		            rule-set Outbound_NAT {
		                from routing-instance Internal;
		                to routing-instance External-ExpressRoute;
		                rule SNAT-Out {
		                    match {
		                        source-address 0.0.0.0/0;
		                    }
		                    then {
		                        source-nat {
		                            pool {
		                                SNAT-To-ExpressRoute;
		                            }
		                        }
		                    }
		                }
		            }
		            rule-set Inbound-NAT {
		                from routing-instance External-ExpressRoute;
		                to routing-instance Internal;
		                rule SNAT-In {
		                    match {
		                        source-address 0.0.0.0/0;
		                    }
		                    then {
		                        source-nat {
		                            pool {
		                                SNAT-From-ExpressRoute;
		                            }
		                        }
		                    }
		                }
		            }
		        }
		    }
		}


### 5\. Configurare BGP per pubblicare prefissi selettivi in ciascuna direzione

Fare riferimento agli esempi riportati nella pagina [Esempi di configurazione del routing](expressroute-config-samples-routing.md).

### 6\. Creare criteri

	routing-options {
	    	      autonomous-system <Customer-ASN>;
	}
	policy-options {
	    prefix-list Microsoft-Prefixes {
	        <IP-Address/Subnet-Mask;
	        <IP-Address/Subnet-Mask;
	    }
	    prefix-list private-ranges {
	        10.0.0.0/8;
	        172.16.0.0/12;
	        192.168.0.0/16;
	        100.64.0.0/10;
	    }
	    policy-statement Advertise-NAT-Pools {
	        from {
	            protocol static;
	            route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
	        }
	        then accept;
	    }
	    policy-statement Accept-from-Microsoft {
	        term 1 {
	            from {
	                instance External-ExpressRoute;
	                prefix-list-filter Microsoft-Prefixes orlonger;
	            }
	            then accept;
	        }
	        term deny {
	            then reject;
	        }
	    }
	    policy-statement Accept-from-Internal {
	        term no-private {
	            from {
	                instance Internal;
	                prefix-list-filter private-ranges orlonger;
	            }
	            then reject;
	        }
	        term bgp {
	            from {
	                instance Internal;
	                protocol bgp;
	            }
	            then accept;
	        }
	        term deny {
	            then reject;
	        }
	    }
	}
	routing-instances {
	    Internal {
	        instance-type virtual-router;
	        interface reth0.100;
	        routing-options {
	            static {
	                route <NAT-Pool-IP-Address/Subnet-mask> discard;
	            }
	            instance-import Accept-from-Microsoft;
	        }
	        protocols {
	            bgp {
	                group customer {
	                    export <Advertise-NAT-Pools>;
	                    peer-as <Customer-ASN-1>;
	                    neighbor <BGP-Neighbor-IP-Address>;
	                }
	            }
	        }
	    }
	    External-ExpressRoute {
	        instance-type virtual-router;
	        interface reth1.100;
	        routing-options {
	            static {
	                route <NAT-Pool-IP-Address/Subnet-mask> discard;
	            }
	            instance-import Accept-from-Internal;
	        }
	        protocols {
	            bgp {
	                group edge-router {
	                    export <Advertise-NAT-Pools>;
	                    peer-as <Customer-Public-ASN>;
	                    neighbor <BGP-Neighbor-IP-Address>;
	                }
	            }
	        }
	    }
	}

## Passaggi successivi

Per altre informazioni, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).

<!---HONumber=AcomDC_0323_2016-->