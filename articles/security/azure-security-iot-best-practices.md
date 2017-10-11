---
title: Procedure consigliate per la sicurezza di Internet delle cose | Documentazione Microsoft
description: L'articolo fornisce un elenco completo di procedure consigliate per la sicurezza di Internet of Things e indicazioni generali.
services: security
documentationcenter: na
author: TomShinder
manager: StevenPo
editor: TomSh
ms.assetid: 2d5598c5-4c30-481d-b8f4-51ee024ea9a7
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: yurid
ms.openlocfilehash: 8efc0053458e338ac1afe98d9ce970c1d5cbfa81
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="internet-of-things-security-best-practices"></a>Procedure consigliate per la sicurezza di Internet delle cose
La protezione dell'infrastruttura di Internet delle cose (IoT) è un'impresa considerevole per tutte le persone coinvolte nelle soluzioni IoT. Dato il numero di dispositivi coinvolti e la natura distribuita di questi dispositivi, l'impatto di un evento di sicurezza che può compromettere milioni di dispositivi IoT non è da sottovalutare perché le conseguenze possono essere enormi.

Per questo motivo, la sicurezza IoT richiede un approccio approfondito. I dati devono essere sicuri nel cloud e quando vengono spostati su reti private e pubbliche. Devono essere in uso metodi per effettuare il provisioning sicuro dei dispositivi IoT stessi. Ogni livello, dal dispositivo, alla rete e al back-end cloud, richiede garanzie di sicurezza affidabili.

Le procedure consigliate IoT possono essere classificate nel modo seguente:

* Produttore o integratore di hardware IoT
* Sviluppatore di soluzioni IoT
* Distributore di soluzioni IoT
* Operatore di soluzioni IoT

Questo articolo riepiloga le [procedure consigliate per la sicurezza di Internet delle cose](../iot-suite/iot-security-best-practices.md). Per informazioni più dettagliate, vedere tale articolo.

## <a name="iot-hardware-manufacturer-or-integrator"></a>Produttore o integratore di hardware IoT
Si invitano i produttori e gli integratori di hardware IoT a seguire le procedure consigliate riportate più avanti:

* **Impostare l'hardware sui requisiti minimi**: la progettazione dell'hardware deve includere esclusivamente le funzionalità minime necessarie per il funzionamento. 
* **Realizzare hardware a prova di manomissione**: meccanismi integrati per il rilevamento di manomissioni fisiche dell'hardware, ad esempio l'apertura del coperchio del dispositivo, la rimozione di una parte del dispositivo e così via. 
* **Creare un hardware sicuro**: se il [costo del venduto](https://en.wikipedia.org/wiki/Cost_of_goods_sold) lo consente, creare funzionalità di sicurezza, ad esempio l'archiviazione protetta e crittografata e la funzionalità di avvio basata sul modulo TPM (Trusted Platform Module).
* **Implementare aggiornamenti sicuri**: l'aggiornamento del firmware durante il ciclo di vita del dispositivo è inevitabile.

## <a name="iot-solution-developer"></a>Sviluppatore di soluzioni IoT
Si invitano gli sviluppatori di soluzioni IoT a seguire le procedure consigliate riportate più avanti:

* **Applicare una metodologia di sviluppo software protetta**: lo sviluppo di software protetti richiede una riflessione totale riguardo alla sicurezza dall'inizio del progetto fino all'implementazione, al test e alla distribuzione.
* **Scegliere software open source con attenzione**: il software open source consente di sviluppare soluzioni in modo rapido.
* **Eseguire l'integrazione con attenzione**: molti dei problemi di sicurezza del software intervengono al confine tra librerie e API. 

## <a name="iot-solution-deployer"></a>Distributore di soluzioni IoT
Si invitano i distributori di soluzioni IoT a seguire le procedure consigliate riportate più avanti:

* **Distribuire l'hardware in modo sicuro**: le distribuzioni IoT potrebbero richiedere che l'hardware da distribuire si trovi in posizioni non protette, ad esempio spazi pubblici o posizioni non controllate.
* **Proteggere le chiavi di autenticazione**: durante la distribuzione, ogni dispositivo richiede gli ID dei dispositivi e le chiavi di autenticazione associate generate dal servizio cloud. Conservare fisicamente queste chiavi al sicuro anche dopo la distribuzione. Qualsiasi chiave compromessa può essere usata da un dispositivo non autorizzato per passare come dispositivo esistente.

## <a name="iot-solution-operator"></a>Operatore di soluzioni IoT
Si invitano gli operatori di soluzioni IoT a seguire le procedure consigliate riportate più avanti:

* **Aggiornare i sistemi regolarmente**: assicurarsi che tutti i sistemi operativi e i driver dei dispositivi vengano aggiornati alle versioni più recenti. 
* **Proteggere i sistemi da attività dannose**: se il sistema operativo lo consente, aggiungere funzionalità antivirus e anti-malware recenti su ogni sistema operativo del dispositivo. 
* **Effettuare controlli regolari**: controllare la presenza di problemi di sicurezza all'infrastruttura IoT è un fattore chiave durante la risposta agli incidenti di sicurezza.
* **Proteggere fisicamente l'infrastruttura IoT**: gli attacchi più dannosi per la sicurezza dell'infrastruttura IoT vengono lanciati tramite l'accesso fisico ai dispositivi.
* **Proteggere le credenziali del cloud**: le credenziali per l'autenticazione nel cloud usate per la configurazione e il funzionamento di una distribuzione IoT costituiscono probabilmente il modo più semplice per accedere e compromettere un sistema IoT. 

