---
title: 'Azure ExpressRoute: Esempi di configurazione di router'
description: Usare questi esempi di configurazione dell'interfaccia e del routing per i router Cisco IOS-XE e Juniper MX Series come esempi per l'uso di Azure ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: duau
ms.openlocfilehash: 3bc850f02884ae0547c2ecf56a46a57a4e66a752
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89397407"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Esempi di configurazione del router per l'impostazione e la gestione del routing
Questa pagina fornisce gli esempi di configurazione dell'interfaccia e del routing delle serie Cisco IOS-XE e Juniper MX per l'uso con Azure ExpressRoute.

> [!IMPORTANT]
> Gli esempi in questa pagina devono essere intesi solo come linee guida. È necessario collaborare con il team di vendita/tecnico del fornitore e il team di rete per trovare le configurazioni appropriate in base alle specifiche esigenze. Microsoft non offre supporto per i problemi relativi alle configurazioni elencate in questa pagina. Contattare il fornitore del dispositivo per assistenza.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Impostazioni di MTU e TCP MSS sulle interfacce del router
Il valore dell'unità massima di trasmissione (MTU) dell'interfaccia ExpressRoute è 1500, ovvero il valore predefinito di MTU tipico per un'interfaccia Ethernet su un router. A meno che il router non abbia un valore MTU diverso per impostazione predefinita, non è necessario specificare un valore sull'interfaccia del router.

A differenza di un Gateway VPN di Azure, non è necessario specificare il valore delle dimensioni segmento massime (MSS) del TCP per un circuito ExpressRoute.

Gli esempi di configurazione del router in questo articolo si applicano a tutti i peering. Per altri dettagli sul routing, vedere [Peering di ExpressRoute](expressroute-circuit-peerings.md) e [Requisiti per il routing di ExpressRoute](expressroute-routing.md).


## <a name="cisco-ios-xe-based-routers"></a>Router basati su Cisco IOS-XE
Gli esempi in questa sezione si applicano a qualsiasi router che esegue la famiglia di sistemi operativi IOS-XE.

### <a name="configure-interfaces-and-subinterfaces"></a>Configurazione di interfacce e sotto-interfacce
Sarà necessaria una sotto-interfaccia per peering in ogni router che si connette a Microsoft. Una sotto-interfaccia può essere identificata mediante un ID VLAN o una coppia in stack di ID VLAN e indirizzo IP.

**Definizione dell'interfaccia Dot1Q**

Questo esempio fornisce la definizione della sotto-interfaccia per una sotto-interfaccia secondario con un ID VLAN. L'ID VLAN è univoco per ogni peering. L'ultimo ottetto dell'indirizzo IPv4 sarà sempre un numero dispari.

```console
interface GigabitEthernet<Interface_Number>.<Number>
 encapsulation dot1Q <VLAN_ID>
 ip address <IPv4_Address><Subnet_Mask>
```

**Definizione dell'interfaccia QinQ**

Questo esempio fornisce la definizione della sotto-interfaccia per una sotto-interfaccia secondario con due ID VLAN. L'ID VLAN esterno (s-tag), se usato, rimane invariato in tutti i peering. L'ID VLAN interno (c-tag) è univoco per ogni peering. L'ultimo ottetto dell'indirizzo IPv4 sarà sempre un numero dispari.

```console
interface GigabitEthernet<Interface_Number>.<Number>
 encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
 ip address <IPv4_Address><Subnet_Mask>
```

### <a name="set-up-ebgp-sessions"></a>Configurazione delle sessioni eBGP
È necessario impostare una sessione BGP con Microsoft per ogni peering. Configurare una sessione BGP usando l'esempio seguente. Se l'indirizzo IPv4 usato per la sotto-interfaccia è a.b.c.d, allora l'indirizzo IP del router adiacente BGP (Microsoft) sarà a.b.c.d+1. L'ultimo ottetto dell'indirizzo IPv4 del router adiacente BGP sarà sempre un numero pari.

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
 neighbor <IP#2_used_by_Azure> activate
 exit-address-family
!
```

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Configurazione dei prefissi da pubblicare tramite la sessione BGP
Configurare il router per pubblicare i prefissi di selezione a Microsoft con l'esempio seguente.

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  network <Prefix_to_be_advertised> mask <Subnet_mask>
  neighbor <IP#2_used_by_Azure> activate
 exit-address-family
!
```

### <a name="route-maps"></a>Mappe di routing
Usare le mappe di routing e gli elenchi di prefissi per filtrare i prefissi propagati nella rete. Vedere l'esempio seguente e assicurarsi di avere configurato gli elenchi di prefissi appropriati.

```console
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
```

### <a name="configure-bfd"></a>Configurare BFD

Si configurerà BFD in due posizioni: una a livello di interfaccia e un'altra a livello BGP. Questo esempio è relativo all'interfaccia QinQ. 

```console
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
```


## <a name="juniper-mx-series-routers"></a>Router serie Juniper MX
Gli esempi in questa sezione si applicano a tutti i router serie Juniper MX.

### <a name="configure-interfaces-and-subinterfaces"></a>Configurazione di interfacce e sotto-interfacce

**Definizione dell'interfaccia Dot1Q**

Questo esempio fornisce la definizione della sotto-interfaccia per una sotto-interfaccia secondario con un ID VLAN. L'ID VLAN è univoco per ogni peering. L'ultimo ottetto dell'indirizzo IPv4 sarà sempre un numero dispari.

```console
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
```


**Definizione dell'interfaccia QinQ**

Questo esempio fornisce la definizione della sotto-interfaccia per una sotto-interfaccia secondario con due ID VLAN. L'ID VLAN esterno (s-tag), se usato, rimane invariato in tutti i peering. L'ID VLAN interno (c-tag) è univoco per ogni peering. L'ultimo ottetto dell'indirizzo IPv4 sarà sempre un numero dispari.

```console
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
```

### <a name="set-up-ebgp-sessions"></a>Configurazione delle sessioni eBGP
È necessario impostare una sessione BGP con Microsoft per ogni peering. Configurare una sessione BGP usando l'esempio seguente. Se l'indirizzo IPv4 usato per la sotto-interfaccia è a.b.c.d, allora l'indirizzo IP del router adiacente BGP (Microsoft) sarà a.b.c.d+1. L'ultimo ottetto dell'indirizzo IPv4 del router adiacente BGP sarà sempre un numero pari.

```console
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
```

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Configurazione dei prefissi da pubblicare tramite la sessione BGP
Configurare il router per pubblicare i prefissi di selezione a Microsoft con l'esempio seguente.

```console
    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
                route-filter; 
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
                export <Policy_Name>;
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="route-policies"></a>Criteri di routing
È possibile usare le mappe di routing e gli elenchi di prefissi per filtrare i prefissi propagati nella rete. Vedere l'esempio seguente e assicurarsi di avere configurato gli elenchi di prefissi appropriati.

```console
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
                export <Policy_Name>;
                import <MS_Prefixes_Inbound>;
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="configure-bfd"></a>Configurare BFD
Configurare BFD solo nella sezione del protocollo BGP.

```console
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
```

### <a name="configure-macsec"></a>Configurare MACSec
Per la configurazione di MACSec, la chiave di associazione della connettività (CAK) e il nome della chiave di associazione della connettività (CKN) devono corrispondere ai valori configurati tramite i comandi di PowerShell.

```console
    security {
        macsec {
            connectivity-association <Connectivity_Association_Name> {
                cipher-suite gcm-aes-xpn-128;
                security-mode static-cak;
                pre-shared-key {
                    ckn <Connectivity_Association_Key_Name>;
                    cak <Connectivity_Association_Key>; ## SECRET-DATA
                }
            }
            interfaces {
                <Interface_Number> {
                    connectivity-association <Connectivity_Association_Name>;
                }
            }
        }
    }
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md) .



