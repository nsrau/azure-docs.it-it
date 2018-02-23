---
title: Esempio di configurazione per la connessione di dispositivi Cisco ASA a gateway VPN di Azure | Microsoft Docs
description: Questo articolo contiene un esempio di configurazione per la connessione di dispositivi Cisco ASA ai gateway VPN di Azure.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.openlocfilehash: fbe22b70b4fe3463ffc7b0e9a7ebd683f681117d
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Configurazione di esempio: dispositivo Cisco ASA (IKEv2/senza BGP)
Questo articolo fornisce configurazioni di esempio per la connessione di dispositivi Cisco ASA (Adaptive Security Appliance) a gateway VPN di Azure. L'esempio si applica a dispositivi Cisco ASA che eseguono IKEv2 senza il protocollo BGP (Border Gateway Protocol). 

## <a name="device-at-a-glance"></a>Informazioni sul dispositivo

|                        |                                   |
| ---                    | ---                               |
| Fornitore del dispositivo          | Cisco                             |
| Modello del dispositivo           | ASA                               |
| Versione finale         | 8.4 e versioni successive                     |
| Modello testato           | ASA 5505                          |
| Versione testata         | 9.2                               |
| Versione IKE            | IKEv2                             |
| BGP                    | No                                 |
| Tipo di gateway VPN di Azure | Gateway VPN basato su route           |
|                        |                                   |

> [!NOTE]
> La configurazione di esempio connette un dispositivo Cisco ASA a un gateway VPN di Azure **basato su route**. La connessione usa criteri IPsec/IKE personalizzati con l'opzione **UsePolicyBasedTrafficSelectors**, come descritto in [questo articolo](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> L'esempio richiede che i dispositivi ASA usino i criteri **IKEv2** con le configurazioni basate sull'elenco di accesso, non su VTI. Per verificare che i criteri IKEv2 siano supportati nei dispositivi VPN, vedere le specifiche del fornitore dei dispositivi.


## <a name="vpn-device-requirements"></a>Requisiti del dispositivo VPN
I gateway VPN di Azure usano le suite di protocolli IPsec/IKE standard per stabilire la connessione tramite tunnel VPN da sito a sito (S2S). Per i parametri di protocollo IPsec/IKE dettagliati e gli algoritmi di crittografia predefinita per i gateway VPN di Azure, vedere [Informazioni sui dispositivi VPN e sui parametri IPsec/IKE per connessioni del Gateway VPN da sito a sito](vpn-gateway-about-vpn-devices.md).

> [!NOTE]
> È possibile specificare facoltativamente l'esatta combinazione di algoritmi di crittografia e di complessità della chiave per una connessione specifica, come descritto in [Informazioni sui requisiti di crittografia e i gateway VPN di Azure](vpn-gateway-about-compliance-crypto.md). Se si seleziona una combinazione specifica di complessità delle chiavi e algoritmi di crittografia, assicurarsi di usare le specifiche corrispondenti nei dispositivi VPN.

## <a name="single-vpn-tunnel"></a>Tunnel per VPN unico
Questa configurazione è costituita da un singolo tunnel VPN S2S tra un gateway VPN di Azure e il dispositivo VPN locale. È possibile configurare [BGP nel tunnel VPN locale](#bgp).

![Tunnel per VPN S2S unico](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Per istruzioni dettagliate per creare le configurazioni di Azure, vedere [Single VPN tunnel setup](vpn-gateway-3rdparty-device-config-overview.md#singletunnel) (Configurazione del tunnel VPN singolo).

### <a name="virtual-network-and-vpn-gateway-information"></a>Informazioni sulla rete virtuale e sul gateway di rete VPN
Questa sezione include i parametri per l'esempio.

| **Parametro**                | **Valore**                    |
| ---                          | ---                          |
| Prefissi di indirizzi di rete virtuale        | 10.11.0.0/16<br>10.12.0.0/16 |
| Indirizzo IP del gateway VPN di Azure         | Azure_Gateway_Public_IP      |
| Prefissi di indirizzi locali | 10.51.0.0/16<br>10.52.0.0/16 |
| Indirizzo IP del dispositivo VPN locale    | OnPrem_Device_Public_IP     |
| * ASN BGP di rete virtuale                | 65010                        |
| * Indirizzo IP del peer BGP di Azure           | 10.12.255.30                 |
| * ASN BGP locale         | 65050                        |
| * Indirizzo IP del peer BGP locale     | 10.52.255.254                |
|                              |                              |

\* Parametro facoltativo solo per BGP.

### <a name="ipsecike-policy-and-parameters"></a>Parametri e criteri IPsec/IKE
La tabella seguente include gli algoritmi di IPsec/IKE e i parametri usati nell'esempio. Per verificare gli algoritmi supportati per i modelli del dispositivo VPN e le versioni del firmware, vedere le specifiche del dispositivo VPN.

| **IPsec/IKEv2**  | **Valore**                            |
| ---              | ---                                  |
| Crittografia IKEv2 | AES256                               |
| Integrità IKEv2  | SHA384                               |
| Gruppo DH         | DHGroup24                            |
| * Crittografia IPsec | AES256                               |
| * Integrità IPsec  | SHA1                                 |
| Gruppo PFS        | PFS24                                |
| Durata associazione di sicurezza in modalità rapida   | 7.200 secondi                         |
| Selettore di traffico | UsePolicyBasedTrafficSelectors $True |
| Chiave precondivisa   | PreSharedKey                         |
|                  |                                      |

\*In alcuni dispositivi il valore di integrità IPsec deve essere null se l'algoritmo di crittografia IPsec è AES-GCM.

### <a name="asa-device-support"></a>Supporto di dispositivi ASA

* Il supporto per IKEv2 richiede ASA 8.4 e versioni successive.

* Il supporto per il gruppo DH e il gruppo PFS oltre al gruppo 5 richiede ASA versione 9.

* Il supporto per la crittografia IPsec con AES-GCM e l'integrità IPsec con SHA-256, SHA-384 e SHA-512 richiede ASA versione 9. Questo requisito di supporto si applica ai dispositivi ASA più recenti. Alla data di pubblicazione, i modelli ASA 5505 5510, 5520, 5540, 5550 e 5580 non supportano questi algoritmi. Per verificare gli algoritmi supportati per i modelli del dispositivo VPN e le versioni del firmware, vedere le specifiche del dispositivo VPN.


### <a name="sample-device-configuration"></a>Configurazione di esempio del dispositivo
Lo script seguente è un esempio basato sulla configurazione e sui parametri elencati in precedenza. La configurazione del tunnel VPN S2S è costituita dai seguenti elementi:

1. Interfacce e route
2. Elenchi di accesso
3. Criteri IKE e parametri (fase 1 o in modalità principale)
4. Criteri IKE e parametri (fase 2 o in modalità rapida)
5. Altri parametri, ad esempio fissaggio TCP MSS

> [!IMPORTANT]
> Prima di usare lo script di esempio, completare questa procedura. Sostituire i valori segnaposto nello script con le impostazioni del dispositivo per la configurazione.

* Specificare la configurazione dell'interfaccia per le interfacce interne ed esterne.
* Identificare le route per le reti esterne/pubbliche e interne/private.
* Verificare che tutti i nomi e i numeri dei criteri siano univoci nel dispositivo.
* Verificare che gli algoritmi di crittografia siano supportati nel dispositivo.
* Sostituire i **valori segnaposto** seguenti con i valori effettivi per la configurazione:
  - Nome interfaccia esterna: **outside**
  - **Azure_Gateway_Public_IP**
  - **OnPrem_Device_Public_IP**
  - IKE: **Pre_Shared_Key**
  - Nomi per i gateway di rete virtuale e locale: **VNetName** e **LNGName**
  - **Prefissi** degli indirizzi di rete virtuale e locale
  - **Netmask** appropriate

#### <a name="sample-script"></a>Script di esempio

```
! Sample ASA configuration for connecting to Azure VPN gateway
!
! Tested hardware: ASA 5505
! Tested version:  ASA version 9.2(4)
!
! Replace the following place holders with your actual values:
!   - Interface names - default are "outside" and "inside"
!   - <Azure_Gateway_Public_IP>
!   - <OnPrem_Device_Public_IP>
!   - <Pre_Shared_Key>
!   - <VNetName>*
!   - <LNGName>* ==> LocalNetworkGateway - the Azure resource that represents the
!     on-premises network, specifies network prefixes, device public IP, BGP info, etc.
!   - <PrivateIPAddress> ==> Replace it with a private IP address if applicable
!   - <Netmask> ==> Replace it with appropriate netmasks
!   - <Nexthop> ==> Replace it with the actual nexthop IP address
!
! (*) Must be unique names in the device configuration
!
! ==> Interface & route configurations
!
!     > <OnPrem_Device_Public_IP> address on the outside interface or vlan
!     > <PrivateIPAddress> on the inside interface or vlan; e.g., 10.51.0.1/24
!     > Route to connect to <Azure_Gateway_Public_IP> address
!
!     > Example:
!
!       interface Ethernet0/0
!        switchport access vlan 2
!       exit
!
!       interface vlan 1
!        nameif inside
!        security-level 100
!        ip address <PrivateIPAddress> <Netmask>
!       exit
!
!       interface vlan 2
!        nameif outside
!        security-level 0
!        ip address <OnPrem_Device_Public_IP> <Netmask>
!       exit
!
!       route outside 0.0.0.0 0.0.0.0 <NextHop IP> 1
!
! ==> Access lists
!
!     > Most firewall devices deny all traffic by default. Create access lists to
!       (1) Allow S2S VPN tunnels between the ASA and the Azure gateway public IP address
!       (2) Construct traffic selectors as part of IPsec policy or proposal
!
access-list outside_access_in extended permit ip host <Azure_Gateway_Public_IP> host <OnPrem_Device_Public_IP>
!
!     > Object group that consists of all VNet prefixes (e.g., 10.11.0.0/16 &
!       10.12.0.0/16)
!
object-group network Azure-<VNetName>
 description Azure virtual network <VNetName> prefixes
 network-object 10.11.0.0 255.255.0.0
 network-object 10.12.0.0 255.255.0.0
exit
!
!     > Object group that corresponding to the <LNGName> prefixes.
!       E.g., 10.51.0.0/16 and 10.52.0.0/16. Note that LNG = "local network gateway".
!       In Azure network resource, a local network gateway defines the on-premises
!       network properties (address prefixes, VPN device IP, BGP ASN, etc.)
!
object-group network <LNGName>
 description On-Premises network <LNGName> prefixes
 network-object 10.51.0.0 255.255.0.0
 network-object 10.52.0.0 255.255.0.0
exit
!
!     > Specify the access-list between the Azure VNet and your on-premises network.
!       This access list defines the IPsec SA traffic selectors.
!
access-list Azure-<VNetName>-acl extended permit ip object-group <LNGName> object-group Azure-<VNetName>
!
!     > No NAT required between the on-premises network and Azure VNet
!
nat (inside,outside) source static <LNGName> <LNGName> destination static Azure-<VNetName> Azure-<VNetName>
!
! ==> IKEv2 configuration
!
!     > General IKEv2 configuration - enable IKEv2 for VPN
!
group-policy DfltGrpPolicy attributes
 vpn-tunnel-protocol ikev1 ikev2
exit
!
crypto isakmp identity address
crypto ikev2 enable outside
!
!     > Define IKEv2 Phase 1/Main Mode policy
!       - Make sure the policy number is not used
!       - integrity and prf must be the same
!       - DH group 14 and above require ASA version 9.x.
!
crypto ikev2 policy 1
 encryption       aes-256
 integrity        sha384
 prf              sha384
 group            24
 lifetime seconds 86400
exit
!
!     > Set connection type and pre-shared key
!
tunnel-group <Azure_Gateway_Public_IP> type ipsec-l2l
tunnel-group <Azure_Gateway_Public_IP> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <Pre_Shared_Key> 
 ikev2 local-authentication  pre-shared-key <Pre_Shared_Key> 
exit
!
! ==> IPsec configuration
!
!     > IKEv2 Phase 2/Quick Mode proposal
!       - AES-GCM and SHA-2 requires ASA version 9.x on newer ASA models. ASA
!         5505, 5510, 5520, 5540, 5550, 5580 are not supported.
!       - ESP integrity must be null if AES-GCM is configured as ESP encryption
!
crypto ipsec ikev2 ipsec-proposal AES-256
 protocol esp encryption aes-256
 protocol esp integrity  sha-1
exit
!
!     > Set access list & traffic selectors, PFS, IPsec protposal, SA lifetime
!       - This sample uses "Azure-<VNetName>-map" as the crypto map name
!       - ASA supports only one crypto map per interface, if you already have
!         an existing crypto map assigned to your outside interface, you must use
!         the same crypto map name, but with a different sequence number for
!         this policy
!       - "match address" policy uses the access-list "Azure-<VNetName>-acl" defined 
!         previously
!       - "ipsec-proposal" uses the proposal "AES-256" defined previously 
!       - PFS groups 14 and beyond requires ASA version 9.x.
!
crypto map Azure-<VNetName>-map 1 match address Azure-<VNetName>-acl
crypto map Azure-<VNetName>-map 1 set pfs group24
crypto map Azure-<VNetName>-map 1 set peer <Azure_Gateway_Public_IP>
crypto map Azure-<VNetName>-map 1 set ikev2 ipsec-proposal AES-256
crypto map Azure-<VNetName>-map 1 set security-association lifetime seconds 7200
crypto map Azure-<VNetName>-map interface outside
!
! ==> Set TCP MSS to 1350
!
sysopt connection tcpmss 1350
!
```

## <a name="simple-debugging-commands"></a>Semplici comandi di debug

Usare i comandi ASA seguenti a scopo di debug:

* Mostrare l'associazione di sicurezza IPsec o IKE:
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* Attivare la modalità debug:
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    I comandi `debug` possono generare output significativo nella console.

* Mostrare le configurazioni correnti nel dispositivo:
    ```
    show run
    ```
    Usare `show` comandi secondari per elencare parti specifiche della configurazione, ad esempio:
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>Passaggi successivi
Per configurare connessioni cross-premise e da rete virtuale a rete virtuale di tipo attivo/attivo, vedere [Configurare gateway VPN di tipo attivi/attivo](vpn-gateway-activeactive-rm-powershell.md).
