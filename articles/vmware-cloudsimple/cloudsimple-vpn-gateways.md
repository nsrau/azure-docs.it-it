---
title: Gateway VPN nella soluzione VMware di CloudSimple-Azure
description: Informazioni sulla VPN da sito a sito di CloudSimple e i concetti relativi alla VPN da punto a sito
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 47d61f80cae926965dd71342980302c2b3045c52
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689638"
---
# <a name="vpn-gateways-overview"></a>Panoramica di gateway VPN

Un gateway VPN viene usato per inviare il traffico crittografato tra una rete di area CloudSimple in una posizione locale o un computer tramite la rete Internet pubblica.  Ogni area può avere un solo gateway VPN. ma è possibile creare più connessioni allo stesso gateway VPN. Quando si creano più connessioni allo stesso gateway VPN, tutti i tunnel VPN condividono la larghezza di banda disponibile per il gateway.

CloudSimple fornisce due tipi di gateway VPN:

* Gateway VPN da sito a sito
* Gateway VPN da punto a sito

## <a name="site-to-site-vpn-gateway"></a>Gateway VPN da sito a sito

Un gateway VPN da sito a sito viene usato per inviare il traffico crittografato tra una rete di area CloudSimple e un Data Center locale. Usare questa connessione per definire l'intervallo di subnet/CIDR per la comunicazione tra la rete locale e la rete dell'area CloudSimple.

Il gateway VPN consente di utilizzare i servizi locali nel cloud privato e i servizi nel cloud privato, dalla rete locale.  CloudSimple fornisce un server VPN basato su criteri per stabilire una connessione dalla rete locale.

I casi di utilizzo per la VPN da sito a sito includono:

* Accessibilità del cloud privato vCenter da qualsiasi workstation nella rete locale.
* Uso del Active Directory locale come origine di identità vCenter.
* Trasferimento comodo di modelli di VM, ISOs e altri file dalle risorse locali al cloud privato vCenter.
* Accessibilità dei carichi di lavoro in esecuzione nel cloud privato dalla rete locale.

![Topologia di connessione VPN da sito a sito](media/cloudsimple-site-to-site-vpn-connection.png)

> [!IMPORTANT]
> È necessario bloccare la MSS TCP a 1078 byte o inferiore. In alternativa, se i dispositivi VPN non supportano il blocco MSS, è invece possibile impostare il MTU sull'interfaccia del tunnel su 1118 byte. 

### <a name="cryptographic-parameters"></a>Parametri crittografici

Una connessione VPN da sito a sito usa i parametri crittografici predefiniti seguenti per stabilire una connessione protetta.  Quando si crea una connessione dal dispositivo VPN locale, usare uno dei parametri seguenti supportati dal gateway VPN locale.

#### <a name="phase-1-proposals"></a>Proposte della fase 1

| Parametro                       | Proposta 1     | Proposta 2     | Proposta 3     |
|---------------------------------|----------------|----------------|----------------|
| Versione IKE                     | IKEv1          | IKEv1          | IKEv1          |
| Crittografia                      | AES 128        | AES 256        | AES 256        |
| Algoritmo hash                  | SHA 256        | SHA 256        | SHA 1          |
| Gruppo Diffie Hellman (gruppo DH) | 2              | 2              | 2              |
| Durata                       | 28.800 secondi | 28.800 secondi | 28.800 secondi |
| Dimensioni dei dati                       | 4 GB           | 4 GB           | 4 GB           |
| Rilevamento peer inattivo       | Disabilitato/disattivato   | Disabilitato/disattivato   | Disabilitato/disattivato   |


#### <a name="phase-2-proposals"></a>Proposte della fase 2 

| Parametro                                 | Proposta 1    | Proposta 2    | Proposta 3    |
|-------------------------------------------|---------------|---------------|---------------|
| Crittografia                                | AES 128       | AES 256       | AES 256       |
| Algoritmo hash                            | SHA 256       | SHA 256       | SHA 1         |
| Gruppo della segretezza diretta perfetto (gruppo PFS) | Nessuna          | Nessuna          | Nessuna          |
| Durata                                 | 1\.800 secondi | 1\.800 secondi | 1\.800 secondi |
| Dimensioni dei dati                                 | 4 GB          | 4 GB          | 4 GB          |

## <a name="point-to-site-vpn-gateway"></a>Gateway VPN da punto a sito

Una VPN da punto a sito viene usata per inviare il traffico crittografato tra una rete dell'area CloudSimple e un computer client.  La VPN da punto a sito è il modo più semplice per accedere alla rete cloud privata, incluse le VM vCenter e del carico di lavoro del cloud privato.  Usare la connettività VPN da punto a sito se ci si connette al cloud privato in remoto.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare il gateway VPN](https://docs.azure.cloudsimple.com/vpn-gateway/)