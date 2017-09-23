---
title: Esempi di configurazione di router ExpressRoute | Microsoft Docs
description: In questa pagina vengono forniti esempi di configurazione di router per router Cisco e Juniper.
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: 564826bc-017a-4683-a385-37c9fa814948
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 032e584dc5abf59e9e3e8d80673b402f1fbf721b
ms.contentlocale: it-it
ms.lasthandoff: 05/15/2017

---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Esempi di configurazione del router per l'impostazione e la gestione del routing
Questa pagina fornisce gli esempi di configurazione dell'interfaccia e del routing per i router Cisco IOS-XE e Juniper MX. Devono essere intesi come esempi a solo scopo informativo e non devono essere usati per altri scopi. È possibile rivolgersi al fornitore per ottenere le configurazioni appropriate per la rete in uso. 

> [!IMPORTANT]
> Gli esempi inclusi in questa pagina devono essere intesi solo come linee guida. È necessario collaborare con il team di vendita/tecnico del fornitore e il team di rete per ottenere le configurazioni appropriate in base alle specifiche esigenze. Microsoft non offre supporto per i problemi relativi alle configurazioni elencate in questa pagina. È necessario contattare il fornitore del dispositivo per assistenza.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Impostazioni di MTU e TCP MSS sulle interfacce del router
* Il valore MTU dell'interfaccia ExpressRoute è 1500, ovvero il valore predefinito di MTU tipico per un'interfaccia Ethernet su un router. A meno che il router non abbia un valore MTU diverso per impostazione predefinita, non è necessario specificare un valore sull'interfaccia del router.
* A differenza di un Gateway VPN di Azure, non è necessario specificare il valore MSS TCP per un circuito ExpressRoute.

Gli esempi di configurazione del router riportati di seguito si applicano a tutti i peering. Per altri dettagli sul routing, vedere [Peering di ExpressRoute](expressroute-circuit-peerings.md) e [Requisiti per il routing di ExpressRoute](expressroute-routing.md).


## <a name="cisco-ios-xe-based-routers"></a>Router basati su Cisco IOS-XE
Gli esempi in questa sezione si applicano a qualsiasi router che esegue la famiglia di sistemi operativi IOS-XE.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Configurazione di interfacce e sotto-interfacce
Sarà necessaria una sotto-interfaccia per peering in ogni router connesso a Microsoft. Una sotto-interfaccia può essere identificata mediante un ID VLAN o una coppia in stack di ID VLAN e indirizzo IP.

**Definizione dell'interfaccia Dot1Q**

Questo esempio fornisce la definizione della sotto-interfaccia per una sotto-interfaccia secondario con un ID VLAN. L'ID VLAN è univoco per ogni peering. L'ultimo ottetto dell'indirizzo IPv4 sarà sempre un numero dispari.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Definizione dell'interfaccia QinQ**

Questo esempio fornisce la definizione della sotto-interfaccia per una sotto-interfaccia con due ID VLAN. L'ID VLAN esterno (s-tag), se usato, rimane invariato in tutti i peering. L'ID VLAN interno (c-tag) è univoco per ogni peering. L'ultimo ottetto dell'indirizzo IPv4 sarà sempre un numero dispari.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. Impostazione delle sessioni eBGP
È necessario impostare una sessione BGP con Microsoft per ogni peering. L'esempio seguente consente di configurare una sessione BGP con Microsoft. Se l'indirizzo IPv4 usato per la sotto-interfaccia è a.b.c.d, l'indirizzo IP del router adiacente BGP (Microsoft) sarà a.b.c.d+1. L'ultimo ottetto dell'indirizzo IPv4 del router adiacente BGP sarà sempre un numero pari.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Impostazione dei prefissi da pubblicare tramite la sessione BGP
È possibile configurare il router per pubblicare i prefissi di selezione a Microsoft. Per eseguire questa operazione, usare l'esempio riportato di seguito.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. Mappe di routing
È possibile usare le mappe di routing e gli elenchi di prefissi per filtrare i prefissi propagati nella rete. È possibile usare l'esempio riportato di seguito per completare l'attività. Assicurarsi di avere impostato gli elenchi di prefissi appropriati.

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


## <a name="juniper-mx-series-routers"></a>Router serie Juniper MX
Gli esempi in questa sezione si applicano a tutti i router serie Juniper MX.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Configurazione di interfacce e sotto-interfacce

**Definizione dell'interfaccia Dot1Q**

Questo esempio fornisce la definizione della sotto-interfaccia per una sotto-interfaccia secondario con un ID VLAN. L'ID VLAN è univoco per ogni peering. L'ultimo ottetto dell'indirizzo IPv4 sarà sempre un numero dispari.

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

Questo esempio fornisce la definizione della sotto-interfaccia per una sotto-interfaccia con due ID VLAN. L'ID VLAN esterno (s-tag), se usato, rimane invariato in tutti i peering. L'ID VLAN interno (c-tag) è univoco per ogni peering. L'ultimo ottetto dell'indirizzo IPv4 sarà sempre un numero dispari.

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

### <a name="2-setting-up-ebgp-sessions"></a>2. Impostazione delle sessioni eBGP
È necessario impostare una sessione BGP con Microsoft per ogni peering. L'esempio seguente consente di configurare una sessione BGP con Microsoft. Se l'indirizzo IPv4 usato per la sotto-interfaccia è a.b.c.d, l'indirizzo IP del router adiacente BGP (Microsoft) sarà a.b.c.d+1. L'ultimo ottetto dell'indirizzo IPv4 del router adiacente BGP sarà sempre un numero pari.

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

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Impostazione dei prefissi da pubblicare tramite la sessione BGP
È possibile configurare il router per pubblicare i prefissi di selezione a Microsoft. Per eseguire questa operazione, usare l'esempio riportato di seguito.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
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


### <a name="4-route-maps"></a>4. Mappe di routing
È possibile usare le mappe di routing e gli elenchi di prefissi per filtrare i prefissi propagati nella rete. È possibile usare l'esempio riportato di seguito per completare l'attività. Assicurarsi di avere impostato gli elenchi di prefissi appropriati.

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

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md) .


