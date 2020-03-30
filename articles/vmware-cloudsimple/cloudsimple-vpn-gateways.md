---
title: Soluzione Azure VMware di CloudSimple - Gateway VPN
description: Scopri di più sui concetti di CloudSimple Site-to-Site VPN e Point-to-Site VPN
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 662fa342b3a18f726b418c496ff3fda937445301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024858"
---
# <a name="vpn-gateways-overview"></a>Panoramica dei gateway VPN

Un gateway VPN viene usato per inviare traffico crittografato tra una rete di aree CloudSimple in una posizione locale o un computer tramite Internet pubblico.  Ogni area può avere un gateway VPN, che può supportare più connessioni. Quando si creano più connessioni allo stesso gateway VPN, tutti i tunnel VPN condividono la larghezza di banda disponibile per il gateway.

CloudSimple fornisce due tipi di gateway VPN:

* Gateway VPN da sito a sito
* Gateway VPN da punto a sito

## <a name="site-to-site-vpn-gateway"></a>Gateway VPN da sito a sito

Un gateway VPN da sito a sito viene utilizzato per inviare traffico crittografato tra una rete di aree CloudSimple e un data center locale. Utilizzare questa connessione per definire l'intervallo subnet/CIDR per il traffico di rete tra la rete locale e la rete di aree CloudSimple.

Il gateway VPN consente di utilizzare i servizi locali nel cloud privato e i servizi nel cloud privato dalla rete locale.  CloudSimple fornisce un server VPN basato su policy per stabilire la connessione dalla rete locale.

Casi d'uso per la VPN da sito a sito:

* Accessibilità del tuo Private Cloud vCenter da qualsiasi workstation nella rete locale.
* Utilizzo di Active Directory locale come origine di identità vCenter.
* Trasferimento pratico di modelli di macchine virtuali, ISO e altri file dalle risorse locali al vCenter del cloud privato.
* Accessibilità dei carichi di lavoro in esecuzione nel cloud privato dalla rete locale.

![Topologia di connessione VPN da sito a sito](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Parametri crittografici

Una connessione VPN da sito a sito utilizza i seguenti parametri di crittografia predefiniti per stabilire una connessione sicura.  Quando si crea una connessione dal dispositivo VPN locale, utilizzare uno dei parametri seguenti supportati dal gateway VPN locale.

#### <a name="phase-1-proposals"></a>Proposte della fase 1

| Parametro | Proposta 1 | Proposta 2 | Proposta 3 |
|-----------|------------|------------|------------|
| Versione IKE | IKEv1 | IKEv1 | IKEv1 |
| Crittografia | AES 128 | AES 256 | AES 256 |
| Algoritmo hashHash Algorithm| SHA 256 | SHA 256 | SHA 1 |
| Gruppo Diffie Hellman (Gruppo DH) | 2 | 2 | 2 |
| Durata | 28.800 secondi | 28.800 secondi | 28.800 secondi |
| Dimensioni dei dati | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>Proposte della fase 2

| Parametro | Proposta 1 | Proposta 2 | Proposta 3 |
|-----------|------------|------------|------------|
| Crittografia | AES 128 | AES 256 | AES 256 |
| Algoritmo hashHash Algorithm| SHA 256 | SHA 256 | SHA 1 |
| Perfect Forward Secrecy Group (PFS Group) | nessuno | nessuno | nessuno |
| Durata | 1.800 secondi | 1.800 secondi | 1.800 secondi |
| Dimensioni dei dati | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> Impostare TCP MSS Clamping su 1200 sul dispositivo VPN. In alternativa, se i dispositivi VPN non supportano il bloccaggio MSS, in alternativa è possibile impostare l'MTU sull'interfaccia del tunnel su 1240 byte.

## <a name="point-to-site-vpn-gateway"></a>Gateway VPN da punto a sito

Una VPN da punto a sito viene utilizzata per inviare traffico crittografato tra una rete di aree CloudSimple e un computer client.  La VPN da punto a sito è il modo più semplice per accedere alla rete cloud privata, inclusi vCenter del cloud privato e le macchine virtuali del carico di lavoro.  Utilizzare la connettività VPN da punto a sito se ci si connette al cloud privato in remoto.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare il gateway VPN](vpn-gateway.md)
