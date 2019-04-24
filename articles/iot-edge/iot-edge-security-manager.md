---
title: Informazioni sulla protezione di dispositivi e software con il gestore sicurezza - Azure IoT Edge | Microsoft Docs
description: Gestisce la strategia di sicurezza dei dispositivi IoT Edge e l'integrità dei servizi di sicurezza.
services: iot-edge
keywords: sicurezza, elemento protetto, enclave, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 07/30/2018
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: bc441e2bbd36c8d078eb67aff48e58684a026289
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60444977"
---
# <a name="azure-iot-edge-security-manager"></a>Gestore sicurezza di Azure IoT Edge

Il gestore sicurezza di Azure IoT Edge è una tecnologia di sicurezza ben delimitata per la protezione del dispositivo IoT Edge e di tutti i relativi componenti tramite l'astrazione dell'hardware del processore di sicurezza. È il punto focale per la protezione avanzata e offre il punto di integrazione delle tecnologie per gli OEM (Original Equipment Manufacturer).

![Gestore sicurezza di Azure IoT Edge](media/edge-security-manager/iot-edge-security-manager.png)

L'obiettivo del gestore sicurezza di IoT Edge è difendere l'integrità del dispositivo IoT Edge e di tutte le operazioni software inerenti.  A tale scopo, esegue la transizione del trust dall'hardware della radice di attendibilità sottostante (se disponibile) per eseguire il bootstrap in modo sicuro del runtime di IoT Edge e continuare a monitorare l'integrità delle operazioni.  Il gestore sicurezza di IoT Edge è costituito da software che opera in combinazione con l'hardware del processore di sicurezza, se disponibile, per offrire le massime garanzie di sicurezza.  

Le responsabilità del gestore sicurezza di IoT Edge includono, in via esemplificativa:

* Eseguire il bootstrap sicuro e misurato del dispositivo Azure IoT Edge.
* Eseguire il provisioning delle identità del dispositivo e transizione del trust dove applicabile.
* Ospitare e proteggere i componenti del dispositivo di servizi cloud come il servizio Device Provisioning.
* Eseguire il provisioning in modo sicuro dei moduli di IoT Edge con identità univoche.
* Operare come gatekeeper della radice di attendibilità dei dispositivi hardware tramite servizi notary.
* Monitorare l'integrità delle operazioni di IoT Edge in fase di esecuzione.

Il gestore sicurezza di IoT Edge include tre componenti:

* Daemon di sicurezza di IoT Edge.
* Platform Abstraction Layer (PAL) del modulo di protezione hardware.
* Facoltativo ma consigliato, radice di attendibilità dell'hardware del processore o modulo di protezione hardware.

## <a name="the-iot-edge-security-daemon"></a>Daemon di sicurezza di IoT Edge

Il daemon di sicurezza di IoT Edge è il software responsabile per le operazioni logiche del gestore sicurezza di IoT Edge. È una parte significativa della base di elaborazione attendibile del dispositivo IoT Edge. 

### <a name="design-principles"></a>Principi di progettazione

Il daemon di sicurezza di IoT Edge segue due principi fondamentali: ottimizzare l'integrità operativa e ridurre al minimo il software bloat e la varianza.

#### <a name="maximize-operational-integrity"></a>Ottimizzare l'integrità operativa

Il daemon di sicurezza di IoT Edge opera con il più alto livello di integrità possibile all'interno delle funzionalità di difesa di qualsiasi hardware della radice di attendibilità. Con l'integrazione corretta, l'hardware della radice di attendibilità misura e monitora il daemon di sicurezza in modo statico e in fase di esecuzione per impedire la manomissione.

L'accesso fisico è sempre una minaccia per i dispositivi IoT. La radice di attendibilità dell'hardware svolge un ruolo importante nella difesa dell'integrità del daemon di sicurezza di IoT Edge.  La radice di attendibilità dell'hardware si presenta in due forme:

* Elementi sicuri per la protezione di informazioni riservate come segreti e chiavi di crittografia.
* Enclave sicuri per la protezione di segreti come le chiavi e di carichi di lavoro sensibili come la misurazione e la fatturazione.

Esistono due tipi di ambienti di esecuzione per l'uso della radice di attendibilità dell'hardware:

* L'ambiente di esecuzione standard o avanzato (REE, Rich Execution Environment), che si basa sull'uso di elementi sicuri per proteggere le informazioni riservate.
* L'ambiente di esecuzione affidabile (TEE, Trusted Execution Environment), che si basa sull'uso della tecnologia degli enclave sicuri per proteggere le informazioni riservate e offrire la protezione per l'esecuzione del software.

Per i dispositivi che usano gli enclave sicuri come radice di attendibilità dell'hardware, è previsto che la logica relativa alle informazioni riservate risieda all'interno del daemon di sicurezza di IoT Edge.  Le parti non riservate del daemon di sicurezza possono trovarsi all'esterno dell'ambiente TEE.  In ogni caso, è previsto che gli ODM (Original Design Manufacturer) e gli OEM (Original Equipment Manufacturer) estendano l'attendibilità dal proprio modulo di protezione hardware per misurare e difendere l'integrità del daemon di sicurezza di IoT Edge in fase di avvio e di esecuzione.

#### <a name="minimize-bloat-and-churn"></a>Ridurre al minimo il software bloat e la varianza

Un altro principio di base per il daemon di sicurezza di IoT Edge è la riduzione della varianza.  Per il massimo livello di attendibilità, il daemon di sicurezza di IoT Edge può essere strettamente associato alla radice di attendibilità dell'hardware del dispositivo e operare come codice nativo.  Per questi tipi di realizzazioni spesso il software del daemon viene aggiornato tramite i percorsi di aggiornamento sicuro della radice di attendibilità dell'hardware (in contrapposizione ai meccanismi di aggiornamento forniti dal sistema operativo). Tale operazione può risultare complessa a seconda dello specifico scenario hardware e di distribuzione.  Anche il rinnovo della sicurezza è una raccomandazione importante per i dispositivi IoT, è evidente che requisiti di aggiornamento eccessivi o payload di aggiornamento di grandi dimensioni possono espandere l'esposizione agli attacchi in diversi modi.  Gli esempi includono l'omissione di aggiornamenti per ottimizzare la disponibilità operativa o hardware della radice di attendibilità troppo vincolato per elaborare i payload di aggiornamento di grandi dimensioni.  Di conseguenza, la progettazione del daemon di sicurezza di IoT Edge è concisa per ridurre al minimo il footprint, e quindi la base di elaborazione attendibile, e i requisiti di aggiornamento.

### <a name="architecture-of-iot-edge-security-daemon"></a>Architettura del daemon di sicurezza di IoT Edge

![Daemon di sicurezza di Azure IoT Edge](media/edge-security-manager/iot-edge-security-daemon.png)

Il daemon di sicurezza di IoT Edge è progettato per trarre vantaggio da qualsiasi tecnologia disponibile per la radice di attendibilità dell'hardware per la protezione avanzata.  Consente anche la suddivisione tra un ambiente di esecuzione standard/avanzato (REE) e un ambiente di esecuzione attendibile (TEE), quando le tecnologie hardware offrono ambienti di quest'ultimo tipo. Le interfacce specifiche del ruolo consentono l'interazione tra i componenti principali per assicurare l'integrità del dispositivo IoT Edge e delle relative operazioni.

#### <a name="cloud-interface"></a>Interfaccia cloud

L'interfaccia cloud consente al daemon di sicurezza di IoT Edge di accedere a servizi cloud per la sicurezza dei dispositivi, come il rinnovo della sicurezza.  Ad esempio, il daemon di sicurezza di IoT Edge attualmente usa questa interfaccia per accedere al [servizio Device Provisioning (DPS)](https://docs.microsoft.com/azure/iot-dps/) dell'hub IoT di Azure per la gestione del ciclo di vita delle identità del dispositivo.  

#### <a name="management-api"></a>API di gestione

Il daemon di sicurezza di IoT Edge offre un'API di gestione, che viene chiamata dall'agente di IoT Edge durante la creazione, l'avvio, l'arresto o la rimozione di un modulo di Edge. Il daemon di sicurezza di IoT Edge archivia le "registrazioni" per tutti i moduli attivi. Queste registrazioni eseguono il mapping dell'identità di un modulo ad alcune proprietà del modulo. Alcuni esempi di queste proprietà sono l'identificatore di processo (PID) del processo in esecuzione nel contenitore o l'hash del contenuto del contenitore Docker.

Queste proprietà vengono usate dall'API del carico di lavoro (descritta di seguito) per attestare che il chiamante è autorizzato a eseguire un'azione.

L'API di gestione è un'API con privilegi, che può essere chiamata solo dall'agente di IoT Edge.  Poiché il daemon di sicurezza di IoT Edge esegue il bootstrap e avvia l'agente di IoT Edge, è in grado di creare una registrazione implicita per l'agente di IoT Edge, dopo aver attestato che l'agente di IoT Edge non è stato manomesso. Lo stesso processo di attestazione impiegato dall'API viene usato per limitare l'accesso all'API di gestione solo all'agente di IoT Edge.

#### <a name="container-api"></a>API del contenitore

Il daemon di sicurezza di IoT Edge offre l'interfaccia del contenitore per l'interazione con il sistema di contenitori in uso, ad esempio Moby e Docker per la creazione di istanze dei moduli.

#### <a name="workload-api"></a>API del carico di lavoro

L'API del carico di lavoro è un'API del daemon di sicurezza di IoT Edge accessibile a tutti i moduli, tra cui l'agente di IoT Edge. Fornisce una prova di identità, che può essere un certificato X509 o un token firmato del modulo di protezione hardware, e il bundle di attendibilità corrispondente per un modulo. Il bundle di attendibilità contiene i certificati della CA per tutti gli altri server che i moduli devono considerare attendibili.

Il daemon di sicurezza di IoT Edge usa un processo di attestazione per la protezione di questa API. Quando un modulo chiama questa API, il daemon di sicurezza di IoT Edge tenta di trovare una registrazione per l'identità. Se l'operazione riesce, usa le proprietà della registrazione per misurare il modulo. Se il risultato del processo di misurazione corrisponde alla registrazione, viene generato un nuovo certificato X509 o un nuovo token firmato del modulo di protezione hardware. I certificati della CA (bundle di attendibilità) corrispondenti vengono restituiti al modulo.  Il modulo usa questo certificato per connettersi all'hub IoT o ad altri moduli oppure per avviare un server. Quando il token firmato o il certificato sta per scadere, è responsabilità del modulo richiedere un nuovo certificato. 

### <a name="integration-and-maintenance"></a>Integrazione e manutenzione

Microsoft mantiene la base di codice principale per il [daemon di sicurezza di IoT Edge su GitHub](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Installazione e aggiornamenti

L'installazione e gli aggiornamenti del daemon di sicurezza di IoT Edge vengono gestiti tramite il sistema di gestione pacchetti del sistema operativo. I dispositivi IoT Edge con una radice di attendibilità dell'hardware devono fornire protezione avanzata aggiuntiva per l'integrità del daemon eseguendo la gestione del relativo ciclo di vita tramite i sistemi di gestione degli aggiornamenti e dell'avvio protetto.  I produttori di dispositivi possono esplorare queste opzioni in conformità con le funzionalità dei dispositivi corrispondenti.

#### <a name="versioning"></a>Controllo delle versioni

Il runtime di IoT Edge registra e restituisce la versione del daemon di sicurezza di IoT Edge. La versione è restituita tramite l'attributo *runtime.platform.version* della proprietà segnalata del modulo dell'agente di IoT Edge.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Platform Abstraction Layer (PAL) del modulo di protezione hardware

Il Platform Abstraction Layer del modulo di protezione hardware astrae tutto l'hardware della radice di attendibilità per isolare lo sviluppatore o l'utente di IoT Edge dalle relative complessità.  È costituito da una combinazione di API (Application Programming Interface) e procedure di comunicazioni tra domini, ad esempio per la comunicazione tra un ambiente di esecuzione standard e un enclave protetto.  L'implementazione effettiva del Platform Abstraction Layer del modulo di protezione hardware dipende dallo specifico hardware sicuro in uso. La sua esistenza consente di usare praticamente qualsiasi hardware di processore sicuro.

## <a name="secure-silicon-root-of-trust-hardware"></a>Hardware della radice di attendibilità per il processore sicuro

Un processore sicuro è necessario per ancorare l'attendibilità nell'hardware del dispositivo IoT Edge.  I processori sicuri possono essere di versi tipi, come Trusted Platform Module (TPM), embedded Secure Element (eSE), ARM TrustZone, Intel SGX e tecnologie per processori sicuri personalizzate.  L'uso di una radice di attendibilità con processori sicuri è fortemente consigliato, considerati i rischi associati alla possibilità di accesso fisico ai dispositivi IoT.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Integrazione e manutenzione del gestore sicurezza di IoT Edge

Il gestore sicurezza di IoT Edge ha come obiettivo di identificare e isolare i componenti che difendono la sicurezza e l'integrità della piattaforma Azure IoT Edge per la protezione avanzata personalizzata. Le terze parti, come i produttori di dispositivi, devono fare uso delle funzionalità di sicurezza personalizzate disponibili con l'hardware dei relativi dispositivi.  Vedere la sezione Passaggi successivi per collegamenti a esempi su come implementare la protezione avanzata del gestore sicurezza di Azure IoT con TPM (Trusted Platform Module) nelle piattaforme Linux e Windows. Questi esempi usano TPM software o virtuali, ma si applicano direttamente a dispositivi TPM discreti.  

## <a name="next-steps"></a>Passaggi successivi

Leggere il blog [Securing the intelligent edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/) (Protezione dei dispositivi perimetrali intelligenti).

Creare ed effettuare il provisioning di un [dispositivo IoT Edge con un TPM virtuale in una macchina virtuale Linux](how-to-auto-provision-simulated-device-linux.md).

Creare ed effettuare il provisioning di un [dispositivo IoT Edge con un TPM simulato in Windows](how-to-auto-provision-simulated-device-windows.md).
