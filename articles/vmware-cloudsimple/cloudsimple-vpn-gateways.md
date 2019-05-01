---
title: Gateway VPN nella soluzione di VMware da CloudSimple - Azure
description: Scopri CloudSimple site-to-site VPN e i concetti VPN point-to-site
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fa7730247ddc5f30c3d21a32421a6c55ec4ef72e
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64872756"
---
# <a name="vpn-gateways-overview"></a>Panoramica del gateway VPN

Un gateway VPN consente di inviare traffico crittografato tra una rete di area CloudSimple in un percorso locale o un computer sulla rete Internet pubblica.  Ogni area può avere un solo gateway VPN. ma è possibile creare più connessioni allo stesso gateway VPN. Quando si creano più connessioni allo stesso gateway VPN, tutti i tunnel VPN condividono la larghezza di banda disponibile per il gateway.

CloudSimple offre due tipi di gateway VPN:

* Gateway VPN Site-to-site
* Gateway VPN da punto a sito

## <a name="site-to-site-vpn-gateway"></a>Gateway VPN da sito a sito

Un gateway VPN site-to-site consente di inviare traffico crittografato tra una rete di area CloudSimple e un data center locale. Usare questa connessione per definire l'intervallo di subnet/CIDR, per il traffico di rete tra la rete locale e la rete di area CloudSimple.

Il gateway VPN consente di utilizzare i servizi in locale nel cloud privato e dei servizi sul cloud privato, dalla rete locale.  CloudSimple fornisce un server VPN basata su criteri per stabilire una connessione dalla rete locale.

Casi d'uso per la rete VPN site-to-site includono:

* Accessibilità di vCenter cloud privato da qualsiasi workstation della rete locale.
* Utilizzo di Active Directory in locale come origine delle identità di vCenter.
* Pratico trasferimento dei modelli di macchina virtuale, le immagini ISO e altri file provenienti dalle risorse locali al server vCenter al cloud privato.
* Accessibilità dei carichi di lavoro in esecuzione sul cloud privato dalla rete locale.

### <a name="cryptographic-parameters"></a>Parametri di crittografici

Una connessione VPN site-to-site Usa i parametri di crittografia predefinito seguente per stabilire una connessione sicura.  Quando si crea una connessione dal dispositivo VPN locale, i parametri devono corrispondere.

Una connessione VPN site-to-site Usa i parametri di crittografia predefinito seguente per stabilire una connessione sicura.  Quando si crea una connessione dal dispositivo VPN locale, usare uno qualsiasi dei seguenti parametri supportati dal gateway VPN da sito locale.

#### <a name="phase-1-proposals"></a>Fase 1 proposte

| Parametro | Proposta 1 | Proposta 2 | Proposta di 3 |
|-----------|------------|------------|------------|
| Versione IKE | IKEv1 | IKEv1 | IKEv1 |
| Crittografia | AES 128 | AES 256 | AES 256 |
| Algoritmo hash| SHA 256 | SHA 256 | SHA 1 |
| Gruppo Diffie-Hellman (DH gruppo) | 1 | 1 | 1 |
| Durata | 28.800 secondi | 28.800 secondi | 28.800 secondi |
| Dimensioni dei dati | 4 GB | 4 GB | 4 GB |


#### <a name="phase-2-proposals"></a>Fase 2 proposte 

| Parametro | Proposta 1 | Proposta 2 | Proposta di 3 |
|-----------|------------|------------|------------|
| Crittografia | AES 128 | AES 256 | AES 256 |
| Algoritmo hash| SHA 256 | SHA 256 | SHA 1 |
| Perfect Forward Secrecy gruppo (PFS) | Nessuna | Nessuna | Nessuna |
| Durata | 1.800 secondi | 1.800 secondi | 1.800 secondi |
| Dimensioni dei dati | 4 GB | 4 GB | 4 GB |

## <a name="point-to-site-vpn-gateway"></a>Gateway VPN da punto a sito

Una VPN da punto a sito consente di inviare traffico crittografato tra una rete di area CloudSimple e un computer client.  VPN da punto a sito è il modo più semplice per accedere alla rete del cloud privato, tra cui il vCenter del cloud privato e un carico di lavoro macchine virtuali.  Se ci si connette al cloud privato in modalità remota, usare la connettività VPN da punto a sito.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare il gateway VPN](https://docs.azure.cloudsimple.com/vpn-gateway/)