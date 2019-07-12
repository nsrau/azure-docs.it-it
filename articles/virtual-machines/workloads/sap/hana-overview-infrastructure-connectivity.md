---
title: Infrastruttura e connettività a SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Configurare l'infrastruttura di connettività necessaria per l'uso di SAP HANA in Azure (istanze Large).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05bd09d3ab05f3ce426126e5629523fba087dad9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707318"
---
# <a name="sap-hana-large-instances-deployment"></a>Distribuzione di SAP HANA (istanze Large) 

Questo articolo presuppone che sia stato effettuato l'acquisto di SAP HANA in Azure (istanze Large) da Microsoft. Prima di leggere questo articolo, vedere le informazioni di base relative a [termini comuni per le istanze Large di HANA](hana-know-terms.md) e [SKU di istanze Large di HANA](hana-available-skus.md).


Per la distribuzione di unità di istanze Large di HANA, Microsoft richiede le informazioni seguenti:

- Nome del cliente.
- Informazioni di contatto aziendali (inclusi indirizzo e-mail e numero di telefono).
- Informazioni di contatto per questioni tecniche (inclusi indirizzo e-mail e numero di telefono).
- Informazioni di contatto per questioni di rete tecniche (inclusi indirizzo e-mail e numero di telefono).
- Area di distribuzione di Azure (ad esempio, Stati Uniti occidentali, Australia orientale o Europa settentrionale).
- SKU (configurazione) di SAP HANA in Azure (istanze Large).
- Per ogni area di distribuzione di Azure:
    - Intervallo di indirizzi IP /29 per le connessioni ER-P2P che connettono le reti virtuali di Azure a istanze Large di HANA.
    - Blocco CIDR /24 usato per il pool di indirizzi IP del server di istanze Large di HANA.
- Valori dell'intervallo di indirizzi IP usati nell'attributo dello spazio di indirizzi di ogni rete virtuale di Azure che si connette alle istanze Large di HANA.
- Dati per ogni sistema di istanze Large di HANA:
  - Nome host desiderato, preferibilmente con un nome di dominio completo.
  - Indirizzo IP desiderato per l'unità di istanze Large di HANA al di fuori dell'intervallo di indirizzi del pool di indirizzi IP del server. I primi 30 indirizzi IP nell'intervallo del pool di indirizzi IP del server sono riservati per l'uso interno nelle istanze Large di HANA.
  - Nome SID di SAP HANA per l'istanza di SAP HANA (obbligatorio per creare i volumi disco richiesti legati a SAP HANA). Microsoft richiede il SID HANA per la creazione di autorizzazioni per sidadm nei volumi NFS. Questi volumi si collegano all'unità di istanze Large di HANA. Il SID HANA viene usato anche come uno dei componenti del nome dei volumi del disco che vengono montati. Se si vuole eseguire più di un'istanza HANA nell'unità, elencare più SID HANA, ognuno dei quali ottiene un set separato di volumi assegnati.
  - Nel sistema operativo Linux, l'utente sidadm ha un ID gruppo. Questo ID è necessario per creare i volumi disco richiesti correlati a SAP HANA. L'installazione di SAP HANA crea in genere il gruppo sapsys con un ID gruppo 1001. L'utente sidadm fa parte di tale gruppo.
  - Nel sistema operativo Linux, l'utente sidadm ha un ID utente. Questo ID è necessario per creare i volumi disco richiesti correlati a SAP HANA. Se nell'unità vengono eseguite più istanze HANA, elencare tutti gli utenti sidadm. 
- ID della sottoscrizione di Azure a cui verranno connesse direttamente le istanze Large di SAP HANA in Azure. Questo ID sottoscrizione fa riferimento alla sottoscrizione di Azure su cui verranno addebitati i costi delle unità o delle unità di istanze Large di HANA.

Dopo che sono state fornite le informazioni indicate in precedenza, Microsoft effettua il provisioning di SAP HANA in Azure (istanze Large). Microsoft invia le informazioni per collegare le reti virtuali di Azure alle istanze Large di HANA. È anche possibile accedere alle unità di istanze Large di HANA.

Usare la sequenza seguente per connettersi alle istanze Large di HANA dopo la distribuzione da parte di Microsoft:

1. [Connessione di macchine virtuali di Azure a istanze Large di HANA](hana-connect-azure-vm-large-instances.md)
2. [Connessione di una rete virtuale a ExpressRoute per istanze Large di HANA](hana-connect-vnet-express-route.md)
3. [Requisiti di rete aggiuntivi (facoltativo)](hana-additional-network-requirements.md)

