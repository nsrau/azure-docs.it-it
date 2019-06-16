---
title: Supporto - Modulo HSM dedicato di Azure | Microsoft Docs
description: Opzioni di supporto e aree di responsabilità per il modulo HSM dedicato di Azure in diversi scenari
services: dedicated-hsm
author: johndaw
manager: barbkess
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/27/2019
ms.author: barclayn
ms.openlocfilehash: 8341e9057b07fbb72957eddc342ae875e4591993
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60912264"
---
# <a name="azure-dedicated-hsm-supportability"></a>Supporto di HSM dedicato di Azure

Il servizio HSM dedicato di Azure offre un dispositivo fisico ad uso esclusivo del cliente, con controllo amministrativo completo e piena responsabilità di gestione. Il dispositivo reso disponibile è un [Gemalto SafeNet Luna Network HSM 7 modello A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Dopo il provisioning al cliente, Microsoft non avrà alcun accesso amministrativo, oltre a una connessione alla porta seriale come ruolo di monitoraggio.  In mancanza di accesso, Microsoft non può avere alcuna responsabilità a livello di manutenzione del software o amministrazione di sistema. Di conseguenza, i clienti sono responsabili delle attività operative più comuni.
I clienti sono interamente responsabili per le applicazioni che usano i moduli di protezione hardware e dovrebbero collaborare con Gemalto per ottenere supporto o assistenza basata su consulenza. Vista la portata della proprietà del cliente in termini di igiene operativa, Microsoft non può offrire alcun tipo di garanzia di disponibilità elevata per questo servizio. È responsabilità del cliente assicurarsi che le proprie applicazioni siano configurate correttamente per ottenere la disponibilità elevata. Microsoft monitorerà e gestirà l'integrità del dispositivo e la connettività di rete.

## <a name="getting-support"></a>Ottenere assistenza

Supporto tecnico per moduli di protezione hardware dedicato è frutto della collaborazione tra Microsoft e Gemalto. Eventuali problemi hardware o problemi di percorso di rete verranno risolto da Microsoft e nulla a che fare con il modulo di protezione effettivo, ad esempio configurazione, software, lo sviluppo dell'applicazione e del firmware, verrà risolti da Gemalto. Questo modello di supporto garantisce la strada al supporto più efficace. In caso di dubbio con un problema specifico, generare una richiesta di supporto con Microsoft e si garantirà che si viene indirizzati in modo appropriato. Mantenere il coinvolgimento in tutti gli scenari di supporto tecnico Microsoft e cercano di ottenere la migliore esperienza di supporto per i clienti.

## <a name="gemalto-support"></a>Supporto Gemalto

I clienti che usano il servizio modulo di protezione hardware dedicati qualificarsi per il supporto da Gemalto in base alla loro Plus piano di supporto. Ciò richiede un processo di registrazione usando il portale di supporto Gemalto. Verranno fornite un ID cliente e le istruzioni per questo elemento come parte di engagement iniziale con Microsoft per ottenere l'accesso al servizio modulo di protezione hardware dedicati. Il meccanismo per ottenere supporto da Gemalto è tramite il [portale di assistenza clienti](https://supportportal.gemalto.com/csm/).
Un aspetto essenziale di nota è che Gemalto fornirà tutti i software e la documentazione necessari per usare il modulo di protezione hardware (ad esempio, il software di accesso client e gli SDK) tramite download nel portale di supporto del cliente.

### <a name="software-components"></a>Componenti software

Nella configurazione dei dispositivi HSM vengono usati diversi componenti software:

* Software client
* SDK
* Strumenti

### <a name="guidance"></a>Materiale sussidiario

Gemalto rende disponibile materiale sussidiario relativo all'amministrazione e alla configurazione tramite il [portale di assistenza clienti](https://supportportal.gemalto.com/csm/). Una volta effettuato l'accesso usando un ID cliente valido, questi documenti sono disponibili per il download. Gemalto fornisce inoltre una serie di guide all'integrazione per assistere i clienti con scenari diversi e integrazioni di software. Per altre informazioni, vedere il [sito partner di Gemalto per Microsoft](https://safenet.gemalto.com/partners/microsoft/).

### <a name="support"></a>Supporto

Qualsiasi problema a livello di software o domanda relativa all'uso del modulo di protezione hardware come parte del servizio HSM dedicato deve essere riferito direttamente al supporto Gemalto. Tutti i componenti software elencati in precedenza e tutte le configurazioni personalizzate di moduli di protezione hardware post-provisioning saranno gestiti da Gemalto. Per altre informazioni, vedere il [portale di assistenza clienti di Gemalto](https://supportportal.gemalto.com/csm/).

### <a name="consulting-services"></a>Servizi di consulenza

Per assistenza nella progettazione, lo sviluppo e la distribuzione di applicazioni personalizzate che usano il modulo di protezione hardware, contattare il rappresentante Gemalto.

## <a name="microsoft-support"></a>Supporto tecnico Microsoft

Microsoft garantisce dispositivi fisici di modulo di protezione hardware rete accessibile e lo stato operativo per l'utilizzo esclusivo di un singolo cliente. I clienti sono responsabili di configurazione, amministrazione e gestione del dispositivo. Le responsabilità di Microsoft includono:

* Assicurarsi che il dispositivo disponga di alimentazione e raffreddamento
* Mantenere lo stato operativo del modulo di protezione hardware (ad esempio, scenari di risoluzione dei problemi)
* Il dispositivo sia accessibile in rete.

I problemi seguenti devono essere segnalati a Microsoft:

* Guasti dei componenti
* Guasto completo del dispositivo
* Problemi di accesso alla rete
* Problemi di provisioning e deprovisioning.

Microsoft ha accesso fisico alla porta seriale del dispositivo tramite un ruolo di monitoraggio (vale a dire, un ruolo non amministrativo) che abilita la telemetria di base sull'integrità.  In questo modo Microsoft può fornire la notifica tempestiva di problemi al cliente, a meno che il cliente non scelga di limitare il privilegio. 

### <a name="provisioning-and-decommissioning"></a>Provisioning e rimozione delle autorizzazioni

Dopo aver ottenuto una registrazione approvata per il servizio HSM dedicato, il cliente sarà in grado di creare risorse HSM, attualmente tramite PowerShell o l'interfaccia della riga di comando e non dal portale di Azure. La risorsa attraversa un processo di allocazione in cui viene eseguito il mapping di un dispositivo fisico in un'area specificata a una rete virtuale del cliente predefinita. Una volta visibile sulla rete virtuale, il cliente può accedere al dispositivo e configurarlo ulteriormente in base ai requisiti. I clienti accedono ai moduli di protezione hardware dedicati usando strumenti e software client Gemalto. Il processo di creazione delle risorse è supportato da Microsoft. Il processo di configurazione personalizzata e altri aspetti sono supportati da Gemalto. Vedere la sezione precedente sul supporto Gemalto. Quando un cliente smette di utilizzare un modulo di protezione hardware, deve reimpostarlo (impostarlo su zero) per garantire la non persistenza dei dati. Il processo di reimpostazione del dispositivo rimuove tutti i dati e la configurazione personalizzata. Microsoft dealloca il dispositivo e lo restituisce al pool in uno stato originario. Ciò significa che quando il dispositivo viene restituito al pool non vi è alcuna traccia dell'attività del cliente precedente. 

### <a name="hardware-issues"></a>Problemi hardware

Il dispositivo HSM ha alimentatori e ventole ridondanti e sostituibili.  Tuttavia, la rimozione di unità della ventola causerà sempre un evento di manomissione. Se si verifica il guasto di un componente, Microsoft seguirà il processo più appropriato per risolvere il problema a livello di componente in modo da causare un'interruzione minima e il minimo rischio per la disponibilità del servizio ai clienti.
Qualsiasi guasto più grave comporterà la sostituzione con un nuovo dispositivo dal pool libero. Il cliente include semplicemente il nuovo dispositivo nella coppia a disponibilità elevata esistente in modo da sincronizzarlo e tornare allo stato operativo completo. I dispositivi contenenti dati del modulo di protezione hardware verranno rimossi e distrutti all'interno del data center. Solo lo chassis verrà restituito a Gemalto per il riciclo.


### <a name="networking-issues"></a>Problemi di rete

In caso di problemi di accesso di rete al dispositivo HSM, i clienti devono contattare il supporto tecnico Microsoft. Un semplice test per l'accesso di rete consiste nell'usare SSH per connettersi al dispositivo HSM. In caso di errore, contattare il supporto tecnico Microsoft.

## <a name="service-level-expectations-for-support"></a>Aspettative del livello di servizio per il supporto

Per i livelli di servizio del supporto tecnico Microsoft, vedere il [piano di supporto per Azure](https://azure.microsoft.com/support/plans/).
Per i livelli di servizio del supporto Gemalto, vedere [Gemalto Support Essentials](https://azure.microsoft.com/support/plans/) (Informazioni di base sul supporto Gemalto).

## <a name="next-steps"></a>Passaggi successivi

Prima di eseguire il provisioning di dispositivi o di iniziare la progettazione o distribuzione di applicazioni, è consigliabile aver ben compreso tutti i concetti chiave.

* [Architettura di distribuzione](deployment-architecture.md)
* [Disponibilità elevata](high-availability.md)
* [Sicurezza fisica](physical-security.md)
* [Rete](networking.md)

