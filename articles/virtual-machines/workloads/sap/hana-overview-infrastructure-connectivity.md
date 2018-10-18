---
title: Infrastruttura e connettività a SAP HANA in Azure (istanze grandi) | Microsoft Docs
description: Configurare l'infrastruttura di connettività necessaria per l'uso di SAP HANA in Azure (istanze grandi).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c61dffc6fd9d0c65f5e925daebdf2a02cfb5d6ba
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161357"
---
# <a name="sap-hana-large-instances-deployment"></a>Distribuzione di SAP HANA (istanze Large) 

Dopo aver finalizzato l'acquisto di SAP HANA in Azure (istanze Large) con il team Microsoft dedicato agli account aziendali, è necessario specificare le informazioni seguenti per distribuire le unità di istanze Large di HANA:

- Nome del cliente
- Informazioni di contatto aziendali (inclusi indirizzo e-mail e numero di telefono)
- Informazioni di contatto tecniche (inclusi indirizzo e-mail e numero di telefono)
- Informazioni di contatto di rete tecniche (inclusi indirizzo e-mail e numero di telefono)
- Area di distribuzione di Azure: Stati Uniti occidentali, Stati Uniti orientali, Australia orientale, Australia sud-orientale, Europa occidentale e Europa settentrionale a partire da luglio 2017
- Confermare l'SKU (configurazione) di SAP HANA in Azure (istanze grandi)
- Come già illustrato in dettaglio nel documento di panoramica e architettura per le istanze Large di HANA, per ogni area di Azure in cui viene distribuito è necessario specificare:
    - Intervallo di indirizzi IP da /29 per le connessioni ER-P2P che connettono reti virtuali di Azure a istanze Large di HANA
    - Blocco CIDR da /24 usato per il pool di indirizzi IP per il server di istanze Large di HANA
- Valori dell'intervallo di indirizzi IP usato nell'attributo dello spazio di indirizzi della rete virtuale di ogni rete virtuale che si connetterà alle istanze Large di HANA
- Dati per ogni sistema di istanze HANA di grandi dimensioni:
  - Nome host da usare, preferibilmente con un nome di dominio completo
  - Indirizzo IP da usare per l'unità di istanze Large di HANA non compresa nell'intervallo del pool di indirizzi IP del server, tenendo presente che i primi 30 indirizzi IP nell'intervallo del pool di indirizzi IP del server sono riservati per uso interno nelle istanze Large di HANA
  - Nome SID di SAP HANA per l'istanza di SAP HANA (obbligatorio per creare i volumi disco richiesti legati a SAP HANA). Il nome SID di HANA è necessario per la creazione di autorizzazioni per sidadm nei volumi NFS, che dovranno essere associate all'unità di istanze Large di HANA. Viene usato anche come uno dei componenti del nome dei volumi disco che devono essere montati. Se si vuole eseguire più di un'istanza HANA sull'unità, è necessario elencare più nomi SID di HANA, ognuno dei quali ottiene un set separato di volumi assegnati.
  - L'ID gruppo dell'utente sidadm nel sistema operativo Linux OS è necessario per creare i volumi disco correlati a SAP HANA. L'installazione di SAP HANA crea in genere il gruppo sapsys con l'ID gruppo 1001 e l'utente sidadm fa parte di tale gruppo
  - L'ID utente dell'utente sidadm nel sistema operativo Linux OS è necessario per creare i volumi disco correlati a SAP HANA. Se sull'unità vengono eseguite più istanze HANA, è necessario elencare tutti gli utenti <sid>adm 
- ID sottoscrizione di Azure per la sottoscrizione di Azure a cui SAP HANA in Azure (istanze Large) verrà connesso direttamente. L'ID sottoscrizione fa riferimento alla sottoscrizione di Azure che verrà addebitata con le unità di istanze Large di HANA.

Dopo aver specificato tali informazioni, Microsoft effettua il provisioning di SAP HANA in Azure (istanze Large) e restituisce le informazioni necessarie per collegare le reti virtuali di Azure a istanze Large di HANA e per accedere alle relative unità.

Prima di leggere questo articolo, acquisire familiarità con [termini comuni di istanze Large di HANA](hana-know-terms.md) e lo [SKU di istanze Large di HANA](hana-available-skus.md).

Usare la sequenza seguente per connettersi a istanze Large di HANA dopo la distribuzione da parte di Microsoft:

1. [Connessione di macchine virtuali di Azure a istanze Large di HANA](hana-connect-azure-vm-large-instances.md)
2. [Connessione di una rete virtuale a ExpressRoute per istanze Large di HANA](hana-connect-vnet-express-route.md)
3. [Requisiti di rete aggiuntivi (facoltativo)](hana-additional-network-requirements.md)

**Passaggi successivi**

- Vedere [Connessione di macchine virtuali di Azure a istanze Large di HANA](hana-connect-azure-vm-large-instances.md).
- Vedere [Connessione di una rete virtuale a ExpressRoute per istanze Large di HANA](hana-connect-vnet-express-route.md).