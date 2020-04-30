---
title: 'Azure ExpressRoute: esempi di configurazione del router'
description: In questa pagina vengono forniti esempi di configurazione di router per router Cisco e Juniper.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: osamaz
ms.openlocfilehash: 3603bc45b920dc62eb8bf6f2eb8557f98e21638e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024813"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Esempi di configurazione del router per l'impostazione e la gestione del routing
Questa pagina fornisce esempi di configurazione dell'interfaccia e del routing per i router Cisco IOS-XE e Juniper MX Series quando si lavora con Azure ExpressRoute.

> [!IMPORTANT]
> Gli esempi in questa pagina sono puramente per le linee guida. È necessario collaborare con il team di vendita/tecnico del fornitore e il team di rete per trovare le configurazioni appropriate per soddisfare le esigenze specifiche. Microsoft non supporterà i problemi correlati alle configurazioni elencate in questa pagina. Contattare il fornitore del dispositivo per individuare eventuali problemi di supporto.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Impostazioni di MTU e TCP MSS sulle interfacce del router
L'unità di trasmissione massima (MTU) per l'interfaccia ExpressRoute è 1500, che corrisponde al valore MTU predefinito tipico per un'interfaccia Ethernet su un router. A meno che il router non abbia un valore MTU diverso per impostazione predefinita, non è necessario specificare un valore sull'interfaccia del router.

A differenza di un gateway VPN di Azure, non è necessario specificare la dimensione del segmento massimo TCP (MSS) per un circuito ExpressRoute.

Gli esempi di configurazione del router in questo articolo si applicano a tutti i peering. Per altri dettagli sul routing, vedere [Peering di ExpressRoute](expressroute-circuit-peerings.md) e [Requisiti per il routing di ExpressRoute](expressroute-routing.md).


## <a name="cisco-ios-xe-based-routers"></a>Router basati su Cisco IOS-XE
Gli esempi in questa sezione si applicano a qualsiasi router che esegue la famiglia di sistemi operativi IOS-XE.

### <a name="configure-interfaces-and-subinterfaces"></a>Configurare interfacce e sottointerfacce
È necessaria una sottointerfaccia per peering in ogni router che si connette a Microsoft. Una sottointerfaccia può essere identificata con un ID VLAN o una coppia in pila di ID VLAN e un indirizzo IP.

**Definizione dell'interfaccia Dot1Q**

Questo esempio fornisce la definizione di sottointerfaccia per una sottointerfaccia con un singolo ID VLAN. L'ID VLAN è univoco per ogni peering. L'ultimo ottetto dell'indirizzo IPv4 sarà sempre un numero dispari.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Definizione dell'interfaccia QinQ**

Questo esempio fornisce la definizione di sottointerfaccia per una sottointerfaccia con due ID VLAN. L'ID VLAN esterno (s-Tag), se usato, rimane invariato in tutti i peering. L'ID VLAN interno (c-tag) è univoco per ogni peering. L'ultimo ottetto dell'indirizzo IPv4 sarà sempre un numero dispari.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="set-up-ebgp-sessions"></a>Configurare sessioni eBGP
È necessario configurare una sessione BGP con Microsoft per ogni peering. Configurare una sessione BGP usando l'esempio seguente. Se l'indirizzo IPv4 usato per la sottointerfaccia era a. b. c. d, l'indirizzo IP di BGP Neighbor (Microsoft) sarà a. b. c. d + 1. L'ultimo ottetto dell'indirizzo IPv4 del router adiacente BGP sarà sempre un numero pari.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Configurare i prefissi da annunciare tramite la sessione BGP
Configurare il router per annunciare i prefissi Select a Microsoft usando l'esempio seguente.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="route-maps"></a>Mappe di routing
Usare mappe di route e elenchi di prefisso per filtrare i prefissi propagati nella rete. Vedere l'esempio seguente e assicurarsi di avere configurato gli elenchi di prefisso appropriati.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !

### <a name="configure-bfd"></a>Configurare BFD

Si configurerà BFD in due posizioni: una a livello di interfaccia e un'altra a livello BGP. Questo esempio è relativo all'interfaccia QinQ. 

    interface GigabitEthernet<Interface_Number>.<Number>
     bfd interval 300 min_rx 300 multiplier 3
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> fall-over bfd
     exit-address-family
    !


## <a name="juniper-mx-series-routers"></a>Router serie Juniper MX
Gli esempi in questa sezione si applicano a qualsiasi router della serie Juniper MX.

### <a name="configure-interfaces-and-subinterfaces"></a>Configurare interfacce e sottointerfacce

**Definizione dell'interfaccia Dot1Q**

Questo esempio fornisce la definizione di sottointerfaccia per una sottointerfaccia con un singolo ID VLAN. L'ID VLAN è univoco per ogni peering. L'ultimo ottetto dell'indirizzo IPv4 sarà sempre un numero dispari.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


**Definizione dell'interfaccia QinQ**

Questo esempio fornisce la definizione di sottointerfaccia per una sottointerfaccia con due ID VLAN. L'ID VLAN esterno (s-Tag), se usato, rimane invariato in tutti i peering. L'ID VLAN interno (c-tag) è univoco per ogni peering. L'ultimo ottetto dell'indirizzo IPv4 sarà sempre un numero dispari.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="set-up-ebgp-sessions"></a>Configurare sessioni eBGP
È necessario configurare una sessione BGP con Microsoft per ogni peering. Configurare una sessione BGP usando l'esempio seguente. Se l'indirizzo IPv4 usato per la sottointerfaccia era a. b. c. d, l'indirizzo IP di BGP Neighbor (Microsoft) sarà a. b. c. d + 1. L'ultimo ottetto dell'indirizzo IPv4 del router adiacente BGP sarà sempre un numero pari.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Configurare i prefissi da annunciare tramite la sessione BGP
Configurare il router per annunciare i prefissi Select a Microsoft usando l'esempio seguente.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter 
    <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="route-policies"></a>Criteri di route
Per filtrare i prefissi propagati nella rete, è possibile usare mappe di route e elenchi di prefissi. Vedere l'esempio seguente e assicurarsi di avere configurato gli elenchi di prefisso appropriati.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
                prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="configure-bfd"></a>Configurare BFD
Configurare BFD solo nella sezione protocollo BGP.

    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
                bfd-liveness-detection {
                       minimum-interval 3000;
                       multiplier 3;
                }
            }                               
        }                                   
    }


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md) .



