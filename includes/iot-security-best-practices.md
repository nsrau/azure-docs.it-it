---
title: File di inclusione
description: File di inclusione
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 2138eed9975abe804442c476d19b5b7229685362
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180262"
---
# <a name="security-best-practices-for-internet-of-things-iot"></a>Procedure consigliate per la sicurezza di Internet delle cose (IoT)

Per proteggere un'infrastruttura IoT (Internet of Things, Internet delle cose) è richiesta una strategia di sicurezza rigorosa e approfondita. Questa strategia richiede la protezione dei dati nel cloud, dell'integrità dei dati in transito sulla rete internet pubblica e il provisioning sicuro dei dispositivi. Ogni livello crea maggiori garanzie di sicurezza dell'infrastruttura complessiva.

## <a name="secure-an-iot-infrastructure"></a>Proteggere un'infrastruttura IoT

Questa strategia di sicurezza può essere sviluppata e implementata con la partecipazione attiva dei vari attori coinvolti nella produzione, nello sviluppo e nella distribuzione di dispositivi e infrastrutture IoT. Di seguito è riportata una descrizione dettagliata degli attori.

* **Produttore/integratore di hardware IoT**: Questi attori sono in genere, i produttori di hardware IoT da distribuire, integratori di assemblaggio di hardware da produttori diversi, oppure di fornitori per la distribuzione IoT prodotta o integrata da altri fornitori.

* **Sviluppatore di soluzioni IoT**: Lo sviluppo di una soluzione IoT viene in genere eseguito da uno sviluppatore di soluzioni. Uno sviluppatore può far parte di un team interno o un integratore di sistemi (SI) specializzato in questa attività. Lo sviluppatore di soluzioni IoT può sviluppare vari componenti della soluzione IoT partendo da zero, integrare vari componenti COTS oppure open source, o adottare un Solution Accelerator che necessita di un adattamento minore.

* **Distributore di soluzioni IoT**: Dopo aver sviluppata una soluzione IoT, deve essere distribuita nel campo. Questo processo implica la distribuzione dell'hardware, l'interconnessione dei dispositivi e la distribuzione di soluzioni su dispositivi hardware o nel cloud.

* **Operatore di soluzioni IoT**: Dopo aver distribuita la soluzione IoT, richiede operazioni a lungo termine, il monitoraggio, aggiornamenti e manutenzione. Queste operazioni possono essere eseguite da un team interno che comprende esperti di tecnologie informatiche, team per le operazioni hardware e team di manutenzione, nonché specialisti di dominio che monitorano il corretto funzionamento dell'intera infrastruttura IoT.

Le sezioni che seguono descrivono le procedure consigliate per ognuno di questi attori per aiutare a sviluppare, distribuire e gestire un'infrastruttura IoT protetta.

## <a name="iot-hardware-manufacturerintegrator"></a>Produttore/integratore di hardware IoT

Di seguito sono riportate le pratiche ottimali per i produttori di hardware IoT e gli integratori di hardware.

* **Impostare l'hardware sui requisiti minimi**: La progettazione di hardware deve includere le funzionalità minime necessarie per il funzionamento di hardware e nient'altro. Ad esempio, è possibile includere porte USB solo se necessario al funzionamento del dispositivo. Queste funzionalità aggiuntive espongono il dispositivo a vettori di attacchi indesiderati, che sarebbe opportuno evitare.

* **Realizzare hardware a prova di manomissione**: Compilare meccanismi per rilevare eventuali manomissioni fisiche, ad esempio l'apertura del coperchio del dispositivo o la rimozione di una parte del dispositivo. Questi segnali di manomissione possono essere inseriti nel flusso dei dati caricati nel cloud, al fine di segnalare tali eventi agli operatori.

* **Creare un hardware sicuro**: Se COGS lo consente, creare funzionalità di sicurezza, ad esempio archiviazione protetta e crittografata o funzionalità di avvio basate su modulo TPM (Trusted Platform). Queste funzionalità rendono i dispositivi più sicuri e aiutano a proteggere l'intera infrastruttura IoT.

* **Implementare aggiornamenti sicuri**: Aggiornamenti del firmware durante il ciclo di vita del dispositivo sono inevitabili. La creazione di dispositivi con percorsi protetti per gli aggiornamenti e crittografia protetta delle versioni del firmware consentirà al dispositivo di rimanere protetto durante e dopo gli aggiornamenti.

## <a name="iot-solution-developer"></a>Sviluppatore di soluzioni IoT

Di seguito sono riportate le pratiche ottimali per gli sviluppatori di soluzioni IoT:

* **Applicare una metodologia di sviluppo software sicuro**: Lo sviluppo di software protetti richiede sin-riguardo alla sicurezza, dall'inizio del progetto fino alla relativa implementazione, test e alla distribuzione. Le scelte di piattaforme, linguaggi e strumenti è influenzata dalla metodologia. Microsoft Security Development Lifecycle fornisce un approccio dettagliato per la creazione di software protetti.

* **Scegliere software open source con attenzione**: Software open source consente di sviluppare rapidamente soluzioni. Quando si sceglie il software open source, valutare il livello di attività della community per ogni componente open source. Una community attiva garantisce il supporto del software e la possibilità di rilevare e risolvere i problemi. Al contrario, un software open source incomprensibile e inattivo non verrebbe supportato e i problemi non verrebbero probabilmente rilevati.

* **Integrare con cautela**: Molti software sicurezza intervengono al confine tra librerie e API. Le funzionalità non necessarie per la distribuzione in corso potrebbero essere ancora disponibili tramite un livello di API. Per garantire la sicurezza complessiva, assicurarsi di controllare tutte le interfacce dei componenti integrati per rilevare eventuali difetti di protezione.

## <a name="iot-solution-deployer"></a>Distributore di soluzioni IoT

Di seguito sono riportate le pratiche ottimali per i distributori di soluzioni IoT:

* **Distribuire l'hardware in modo sicuro**: le distribuzioni IoT potrebbero richiedere che l'hardware da distribuire si trovi in posizioni non protette, ad esempio spazi pubblici o posizioni non controllate. In questi casi, assicurarsi che la distribuzione dell'hardware garantisca la massima protezione dalle manomissioni. Se l'hardware dispone di porte USB o di altro tipo, assicurarsi che questi elementi siano protetti. Molti vettori di attacco possono usarle come punto di ingresso.

* **Proteggere le chiavi di autenticazione**: Durante la distribuzione, ogni dispositivo richiede gli ID dispositivo e le chiavi di autenticazione associate generate dal servizio cloud. Conservare fisicamente queste chiavi al sicuro anche dopo la distribuzione. Qualsiasi chiave compromessa può essere usata da un dispositivo non autorizzato per passare come dispositivo esistente.

## <a name="iot-solution-operator"></a>Operatore di soluzioni IoT

Di seguito sono riportate le pratiche ottimali per gli operatori di soluzioni IoT:

* **Mantenere aggiornato il sistema**: Assicurarsi che tutti i driver di dispositivo e sistemi operativi dei dispositivi vengano aggiornati alle versioni più recenti. Se si attiva la funzionalità di aggiornamento automatico su Windows 10 (IoT o altri SKU), Microsoft mantiene il sistema aggiornato, garantendo un sistema operativo protetto per i dispositivi IoT. Mantenere aggiornati gli altri sistemi operativi (ad esempio Linux) aiuta a garantirne la protezione anche dagli attacchi dannosi.

* **Proteggersi da attività dannose**: Se il sistema operativo lo consente, installare le funzionalità antivirus e antimalware più recenti in ogni sistema operativo del dispositivo. In questo modo è possibile mitigare le minacce più esterne. È possibile proteggere i sistemi operativi più recenti dalle minacce eseguendo i passaggi appropriati.

* **Controllare spesso**: Il controllo dell'infrastruttura IoT per i problemi correlati alla sicurezza è chiave durante la risposta agli eventi imprevisti della sicurezza. La maggior parte dei sistemi operativi offre la registrazione integrata degli eventi che è opportuno esaminare frequentemente per assicurarsi che non si verifichino violazioni della protezione. Le informazioni di controllo possono essere inviate come flusso dati di telemetria separati al servizio cloud per l'analisi.

* **Proteggere fisicamente l'infrastruttura IoT**: Gli attacchi più dannosi della protezione dell'infrastruttura IoT vengono lanciati tramite l'accesso fisico ai dispositivi. Un'importante procedura di sicurezza è la protezione contro l'uso non autorizzato di porte USB e altri accessi fisici. Un'operazione fondamentale per rilevare eventuali violazioni è la registrazione degli accessi fisici, come l'uso delle porte USB. Anche in questo caso, Windows 10 (IoT e altri SKU) offre la registrazione dettagliata di questi eventi.

* **Proteggere le credenziali del cloud**: Le credenziali di autenticazione cloud utilizzate per la configurazione e funzionamento di una distribuzione IoT costituiscono probabilmente il modo più semplice per accedere e compromettere un sistema IoT. Proteggere le credenziali modificando frequentemente la password ed evitare di usarle sui computer pubblici.

Le capacità dei vari dispositivi IoT variano. Alcuni dispositivi potrebbero essere computer dotati di sistemi operativi desktop tradizionali, mentre altri dispositivi potrebbero disporre di sistemi operativi molto leggeri. Le migliori pratiche per la protezione descritte in precedenza possono essere applicate a questi dispositivi in modo diverso. Se specificato, è necessario attenersi alle procedure aggiuntive per la sicurezza e la distribuzione fornite dai produttori dei dispositivi.

Alcuni dispositivi legacy e limitati potrebbero non essere stati progettati in modo specifico per la distribuzione IoT. Questi dispositivi potrebbero non essere in grado di crittografare i dati, stabilire connessioni a Internet, o fornire un controllo avanzato. In questi casi, l'uso di un gateway moderno e sicuro sul campo può aggregare i dati dai dispositivi legacy al fine di garantire la protezione necessaria per la connessione di questi dispositivi a Internet. I gateway sul campo offrono l'autenticazione protetta, la negoziazione per le sessioni crittografate, la ricezione di comandi dal cloud e molte altre funzionalità di sicurezza.