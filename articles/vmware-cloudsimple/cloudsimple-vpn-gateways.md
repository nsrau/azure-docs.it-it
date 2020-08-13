---
title: Soluzione VMware di Azure di CloudSimple-gateway VPN
description: Informazioni sui gateway VPN da sito a sito e da punto a sito di CloudSimple, che vengono usati per inviare il traffico crittografato tra un'area di CloudSimple e altre risorse.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e013bb96990a8f3a0ef7d3a58529b200919e276
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88140633"
---
# <a name="vpn-gateways-overview"></a>Panoramica di gateway VPN

Un gateway VPN viene usato per inviare il traffico crittografato tra una rete di area CloudSimple in una posizione locale o un computer tramite la rete Internet pubblica.  Ogni area può avere un solo gateway VPN, che può supportare più connessioni. Quando si creano più connessioni allo stesso gateway VPN, tutti i tunnel VPN condividono la larghezza di banda disponibile per il gateway.

CloudSimple fornisce due tipi di gateway VPN:

* Gateway VPN da sito a sito
* Gateway VPN da punto a sito

## <a name="site-to-site-vpn-gateway"></a>Gateway VPN da sito a sito

Un gateway VPN da sito a sito viene usato per inviare il traffico crittografato tra una rete di area CloudSimple e un Data Center locale. Usare questa connessione per definire l'intervallo di subnet/CIDR per il traffico di rete tra la rete locale e la rete dell'area CloudSimple.

Il gateway VPN consente di utilizzare i servizi locali nel cloud privato e i servizi nel cloud privato dalla rete locale.  CloudSimple fornisce un server VPN basato su criteri per stabilire la connessione dalla rete locale.

Casi d'uso per VPN da sito a sito:

* Accessibilità del cloud privato vCenter da qualsiasi workstation nella rete locale.
* Uso del Active Directory locale come origine di identità vCenter.
* Trasferimento comodo di modelli di VM, ISOs e altri file dalle risorse locali al cloud privato vCenter.
* Accessibilità dei carichi di lavoro in esecuzione nel cloud privato dalla rete locale.

![Topologia di connessione VPN da sito a sito](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Parametri crittografici

Una connessione VPN da sito a sito usa i parametri crittografici predefiniti seguenti per stabilire una connessione protetta.  Quando si crea una connessione dal dispositivo VPN locale, usare uno dei parametri seguenti che sono supportati dal gateway VPN locale.

#### <a name="phase-1-proposals"></a>Proposte della fase 1

| Parametro | Proposta 1 | Proposta 2 | Proposta 3 |
|-----------|------------|------------|------------|
| Versione IKE | IKEv1 | IKEv1 | IKEv1 |
| Crittografia | AES 128 | AES 256 | AES 256 |
| Algoritmo hash| SHA 256 | SHA 256 | SHA 1 |
| Gruppo Diffie Hellman (gruppo DH) | 2 | 2 | 2 |
| Durata | 28.800 secondi | 28.800 secondi | 28.800 secondi |
| Dimensioni dei dati | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>Proposte della fase 2

| Parametro | Proposta 1 | Proposta 2 | Proposta 3 |
|-----------|------------|------------|------------|
| Crittografia | AES 128 | AES 256 | AES 256 |
| Algoritmo hash| SHA 256 | SHA 256 | SHA 1 |
| Gruppo della segretezza diretta perfetto (gruppo PFS) | nessuno | nessuno | nessuno |
| Durata | 1.800 secondi | 1.800 secondi | 1.800 secondi |
| Dimensioni dei dati | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> Impostare la pressione TCP MSS su 1200 sul dispositivo VPN. In alternativa, se i dispositivi VPN non supportano il blocco MSS, è invece possibile impostare il MTU sull'interfaccia del tunnel su 1240 byte.

## <a name="point-to-site-vpn-gateway"></a>Gateway VPN da punto a sito

Una VPN da punto a sito viene usata per inviare il traffico crittografato tra una rete dell'area CloudSimple e un computer client.  La VPN da punto a sito è il modo più semplice per accedere alla rete cloud privata, incluse le VM vCenter e del carico di lavoro del cloud privato.  Usare la connettività VPN da punto a sito se ci si connette al cloud privato in remoto.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare il gateway VPN](vpn-gateway.md)
