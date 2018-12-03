---
title: Che cos'è HSM dedicato? | Microsoft Docs
description: HSM dedicato di Azure offre capacità di archiviazione chiavi in Azure che soddisfano la certificazione FIPS 140-2 Livello 3
services: dedicated-hsm
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 11/26/2018
ms.author: barclayn
ms.openlocfilehash: 92d77ec886a0f37c28f5e3031a7e14f63299c8aa
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52427115"
---
# <a name="what-is-dedicated-hsm"></a>Che cos'è HSM dedicato?

HSM dedicato di Azure offre l'archiviazione chiavi crittografica in Azure che soddisfa i requisiti di sicurezza più rigorosi. HSM dedicato è la soluzione ideale per i clienti che richiedono dispositivi convalidati dalla certificazione FIPS 140-2 Livello 3 e il controllo completo ed esclusivo dell'appliance HSM. I dispositivi HSM vengono distribuiti a livello globale in diverse aree di Azure e il provisioning può essere facilmente effettuato come coppia di dispositivi, configurata per la disponibilità elevata. È anche possibile effettuare il provisioning dei moduli di protezione hardware (HSM) tra varie aree per garantire il failover a livello regionale. Microsoft fornisce il servizio di HSM dedicato grazie all'appliance [SafeNet Luna Network HSM 7 (Modello A790)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) di Gemalto. Questo dispositivo offre i livelli più elevati di prestazioni e opzioni di integrazione crittografica. Quando se ne effettua il provisioning, i moduli di protezione hardware vengono connessi direttamente alla rete virtuale di un cliente, ed è possibile accedervi anche dall'applicazione locale e con gli strumenti di gestione grazie alla configurazione della connettività VPN da sito a sito o da punto a sito. I clienti acquisiranno il software e la documentazione per configurare e gestire i dispositivi HSM dal portale di supporto di Gemalto.

## <a name="why-use-azure-dedicated-hsm"></a>Perché usare HSM dedicato di Azure?

### <a name="fips-140-2-level-3-compliance"></a>Conformità a FIPS 140-2 Livello 3

Molte organizzazioni rispondono a rigorose normative del settore che definiscono la necessità di soddisfare i requisiti della certificazione [FIPS 140-2 Livello 3](https://csrc.nist.gov/publications/detail/fips/140/2/final) da parte dell'archiviazione chiavi crittografica. Attualmente, il servizio Azure Key Vault multi-tenant di Microsoft fornisce solo la certificazione FIPS 140-2 Livello 2. HSM dedicato di Azure risponde a un'esigenza reale del settore dei servizi finanziari, degli enti pubblici e di altri che sono tenuti a soddisfare i requisiti della certificazione FIPS 140-2 Livello 3.

### <a name="single-tenant-devices"></a>Dispositivi a tenant singolo

Molti dei nostri clienti richiedono un tenant singolo per i dispositivi di archiviazione crittografica. Il servizio HSM dedicato di Azure permetterà di effettuare il provisioning di un dispositivo fisico da uno dei data center Microsoft distribuiti a livello globale. Quando sarà stato effettuato il provisioning del dispositivo, solo il cliente potrà accedervi.

### <a name="full-administrative-control"></a>Controllo amministrativo completo

Oltre ai dispositivi a tenant singolo, molti clienti richiedono il controllo amministrativo completo e l'accesso esclusivo per scopi amministrativi. Al termine del provisioning, solo il cliente potrà accedere al dispositivo a livello amministrativo o applicativo. Microsoft non avrà alcun controllo amministrativo dopo il primo accesso del cliente, che richiede la modifica della password. Da quel momento in poi, il cliente è un vero tenant singolo con il controllo amministrativo completo e capacità di gestione dell'applicazione. Microsoft offre comunque l'accesso a livello di monitoraggio (non un ruolo di amministratore) per la telemetria attraverso una connessione alla porta seriale che copre i monitoraggi dell'hardware, ad esempio temperatura, stato di alimentazione e integrità della ventola. Il cliente è libero di disabilitare questa opzione se necessario, ma poi non riceverà gli avvisi di integrità proattivi da Microsoft.

### <a name="high-performance"></a>Prestazioni elevate

Il dispositivo Gemalto è stato selezionato per questo servizio per l'ampia gamma di algoritmi di crittografia e la varietà di sistemi operativi supportati e l'ampio supporto di API. Il modello specifico distribuito offre prestazioni eccellenti con 10.000 operazioni al secondo per RSA-2048. Supporta 10 partizioni che possono essere usate per le istanze di applicazione univoche. Si tratta di un dispositivo a bassa latenza, capacità elevata e velocità effettiva elevata.

### <a name="unique-cloud-based-offering"></a>Offerta univoca basata sul cloud

Microsoft ha riconosciuto un'esigenza specifica nell'ambito di un set unico di clienti ed è l'unico provider di servizi cloud a offrire ai nuovi clienti un servizio HSM dedicato convalidato con certificazione FIPS 140-2 Livello 3 e un'integrazione di applicazioni basate sul cloud e locali di tale portata.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>HSM dedicato di Azure è la scelta giusta?

HSM dedicato di Azure è un servizio specializzato che soddisfa i requisiti univoci in un tipo specifico di organizzazione su larga scala. Di conseguenza, è previsto che la maggior parte dei clienti di Azure non rientri nel profilo d'uso per questo servizio. Molti troveranno che il servizio Azure Key Vault sia più appropriato e più conveniente. Per aiutare il cliente nella scelta della soluzione giusta ai propri requisiti sono stati identificati i criteri seguenti.

### <a name="best-fit"></a>Soluzione ottimale

Più adatta a scenari di trasferimento in modalità "lift-and-shift", che richiedono l'accesso diretto ed esclusivo ai dispositivi HSM. Tra gli esempi sono inclusi:

- La migrazione delle applicazioni dalle macchine locali alle macchine virtuali di Azure
- La migrazione delle applicazioni da Amazon AWS EC2 alle macchine virtuali di Microsoft Azure che usano il servizio AWS Cloud HSM Classic (Amazon non offre questo servizio ai nuovi clienti)
- L'esecuzione di software standard nelle macchine virtuali di Microsoft Azure, come Apache/Ngnix SSL Offload, Oracle TDE e ADCS

### <a name="not-a-fit"></a>Soluzione non idonea

I servizi cloud Microsoft che supportano la crittografia con chiavi gestite dal cliente (ad esempio Azure Information Protection, Crittografia dischi di Azure, Azure Data Lake Store, archiviazione di Azure, Azure SQL, Customer Key di Office 365) non sono integrati con HSM dedicato di Azure.

### <a name="it-depends"></a>Dipende

Molti scenari variano in base a una combinazione potenzialmente complessa di requisiti e compromessi. Un esempio è il requisito FIPS 140-2 Livello 3, che spesso è obbligatorio e, di conseguenza, HSM dedicato è attualmente l'unica opzione.  Se questi requisiti obbligatori non sono pertinenti, spesso si dovrà decidere tra Azure Key Vault e HSM dedicato in base alla valutazione di una combinazione di requisiti. Tra gli esempi sono inclusi:

- Nuovo codice in esecuzione nella macchina virtuale di Azure di un cliente
- TDE di SQL Server in una macchina virtuale di Azure
- Crittografia lato client di archiviazione di Azure
- Tecnologia Always Encrypted di SQL Server e database SQL di Azure

## <a name="next-steps"></a>Passaggi successivi

Considerata la natura altamente specializzata di questo servizio, è consigliabile assicurarsi di aver compreso alcuni dei concetti chiave disponibili in questa documentazione, oltre ai prezzi, al supporto e ai contratti di servizio. È a disposizione un'esercitazione che consente di semplificare il provisioning degli HSM in un ambiente di rete virtuale esistente. Anche le [Guide all'integrazione Gemalto](https://safenet.gemalto.com/partners/microsoft/) e alcune guide pratiche per decidere l'architettura di distribuzione sono un'ottima risorsa.

* [Disponibilità elevata](high-availability.md)
* [Sicurezza fisica](physical-security.md)
* [Rete](networking.md)
* [Supporto](supportability.md)
* [Monitoraggio](monitoring.md)
