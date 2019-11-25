---
title: Informazioni sulla protezione di dispositivi e software con il gestore sicurezza - Azure IoT Edge | Microsoft Docs
description: Gestisce la strategia di sicurezza dei dispositivi IoT Edge e l'integrità dei servizi di sicurezza.
services: iot-edge
keywords: sicurezza, elemento protetto, enclave, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 90cb7cf0a30ea0ebfe00454288de25ddf6e58d52
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457524"
---
# <a name="azure-iot-edge-security-manager"></a>Gestore sicurezza di Azure IoT Edge

Il gestore sicurezza di Azure IoT Edge è una tecnologia di sicurezza ben delimitata per la protezione del dispositivo IoT Edge e di tutti i relativi componenti tramite l'astrazione dell'hardware del processore di sicurezza. It is the focal point for security hardening and provides technology integration point to original equipment manufacturers (OEM).

![Gestore sicurezza di Azure IoT Edge](media/edge-security-manager/iot-edge-security-manager.png)

L'obiettivo del gestore sicurezza di IoT Edge è difendere l'integrità del dispositivo IoT Edge e di tutte le operazioni software inerenti. The security manager transitions trust from underlying hardware root of trust hardware (if available) to bootstrap the IoT Edge runtime and monitor ongoing operations.  Il gestore sicurezza di IoT Edge è costituito da software che opera in combinazione con l'hardware del processore di sicurezza, se disponibile, per offrire le massime garanzie di sicurezza.  

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

The IoT Edge security daemon is responsible for the logical operations of IoT Edge security manager. It represents a significant portion of the trusted computing base of the IoT Edge device. 

### <a name="design-principles"></a>Principi di progettazione

Il daemon di sicurezza di IoT Edge segue due principi fondamentali: ottimizzare l'integrità operativa e ridurre al minimo il software bloat e la varianza.

#### <a name="maximize-operational-integrity"></a>Ottimizzare l'integrità operativa

The IoT Edge security daemon operates with the highest integrity possible within the defense capability of any given root of trust hardware. Con l'integrazione corretta, l'hardware della radice di attendibilità misura e monitora il daemon di sicurezza in modo statico e in fase di esecuzione per impedire la manomissione.

L'accesso fisico è sempre una minaccia per i dispositivi IoT. La radice di attendibilità dell'hardware svolge un ruolo importante nella difesa dell'integrità del daemon di sicurezza di IoT Edge.  Hardware root of trust come in two varieties:

* Elementi sicuri per la protezione di informazioni riservate come segreti e chiavi di crittografia.
* Enclave sicuri per la protezione di segreti come le chiavi e di carichi di lavoro sensibili come la misurazione e la fatturazione.

Two kinds of execution environments exist to use hardware root of trust:

* The standard or rich execution environment (REE) that relies on the use of secure elements to protect sensitive information.
* The trusted execution environment (TEE) that relies on the use of secure enclave technology to protect sensitive information and offer protection to software execution.

For devices using secure enclaves as hardware root of trust, sensitive logic within IoT Edge security daemon should be inside the enclave.  Non-sensitive portions of the security daemon can be outside of the TEE.  In any case, original design manufacturers (ODM) and original equipment manufacturers (OEM) should extend trust from their HSM to measure and defend the integrity of the IoT Edge security daemon at boot and runtime.

#### <a name="minimize-bloat-and-churn"></a>Ridurre al minimo il software bloat e la varianza

Un altro principio di base per il daemon di sicurezza di IoT Edge è la riduzione della varianza.  Per il massimo livello di attendibilità, il daemon di sicurezza di IoT Edge può essere strettamente associato alla radice di attendibilità dell'hardware del dispositivo e operare come codice nativo.  It's common for these types of realizations to update the daemon software through the hardware root of trust's secure update paths (as opposed to OS provided update mechanisms), which can be challenging in some scenarios.  While security renewal is recommended for IoT devices, excessive update requirements or large update payloads can expand the threat surface in many ways.  Gli esempi includono l'omissione di aggiornamenti per ottimizzare la disponibilità operativa o hardware della radice di attendibilità troppo vincolato per elaborare i payload di aggiornamento di grandi dimensioni.  As such, the design of IoT Edge security daemon is concise to keep the footprint and trusted computing base small and to minimize update requirements.

### <a name="architecture-of-iot-edge-security-daemon"></a>Architettura del daemon di sicurezza di IoT Edge

![Daemon di sicurezza di Azure IoT Edge](media/edge-security-manager/iot-edge-security-daemon.png)

The IoT Edge security daemon takes advantage of any available hardware root of trust technology for security hardening.  It also allows for split-world operation between a standard/rich execution environment (REE) and a trusted execution environment (TEE) when hardware technologies offer trusted execution environments. Role-specific interfaces enable the major components of IoT Edge to assure the integrity of the IoT Edge device and its operations.

#### <a name="cloud-interface"></a>Interfaccia cloud

The cloud interface allows the IoT Edge security daemon to access cloud services such as cloud compliments to device security like security renewal.  For example, the IoT Edge security daemon currently uses this interface to access the Azure IoT Hub [Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/) for device identity lifecycle management.  

#### <a name="management-api"></a>API Gestione

IoT Edge security daemon offers a management API, which is called by the IoT Edge agent when creating/starting/stopping/removing an IoT Edge module. The security daemon stores “registrations” for all active modules. Queste registrazioni eseguono il mapping dell'identità di un modulo ad alcune proprietà del modulo. Alcuni esempi di queste proprietà sono l'identificatore di processo (PID) del processo in esecuzione nel contenitore o l'hash del contenuto del contenitore Docker.

These properties are used by the workload API (described below) to verify that the caller is authorized to perform an action.

The management API is a privileged API, callable only from the IoT Edge agent.  Poiché il daemon di sicurezza di IoT Edge esegue il bootstrap e avvia l'agente di IoT Edge, è in grado di creare una registrazione implicita per l'agente di IoT Edge, dopo aver attestato che l'agente di IoT Edge non è stato manomesso. The same attestation process that the workload API uses also restricts access to the management API to only the IoT Edge agent.

#### <a name="container-api"></a>API del contenitore

The container API interacts with the container system in use for module management, like Moby or Docker.

#### <a name="workload-api"></a>API del carico di lavoro

The workload API is accessible to all modules. It provides proof of identity, either as an HSM rooted signed token or an X509 certificate, and the corresponding trust bundle to a module. Il bundle di attendibilità contiene i certificati della CA per tutti gli altri server che i moduli devono considerare attendibili.

The IoT Edge security daemon uses an attestation process to guard this API. When a module calls this API, the security daemon attempts to find a registration for the identity. Se l'operazione riesce, usa le proprietà della registrazione per misurare il modulo. If the result of the measurement process matches the registration, a new proof of identity is generated. I certificati della CA (bundle di attendibilità) corrispondenti vengono restituiti al modulo.  Il modulo usa questo certificato per connettersi all'hub IoT o ad altri moduli oppure per avviare un server. When the signed token or certificate nears expiration, it's the responsibility of the module to request a new certificate. 

### <a name="integration-and-maintenance"></a>Integrazione e manutenzione

Microsoft mantiene la base di codice principale per il [daemon di sicurezza di IoT Edge su GitHub](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Installazione e aggiornamenti

L'installazione e gli aggiornamenti del daemon di sicurezza di IoT Edge vengono gestiti tramite il sistema di gestione pacchetti del sistema operativo. I dispositivi IoT Edge con una radice di attendibilità dell'hardware devono fornire protezione avanzata aggiuntiva per l'integrità del daemon eseguendo la gestione del relativo ciclo di vita tramite i sistemi di gestione degli aggiornamenti e dell'avvio protetto. Device makers should explore these avenues based on their respective device capabilities.

#### <a name="versioning"></a>Controllo delle versioni

Il runtime di IoT Edge registra e restituisce la versione del daemon di sicurezza di IoT Edge. La versione è restituita tramite l'attributo *runtime.platform.version* della proprietà segnalata del modulo dell'agente di IoT Edge.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Platform Abstraction Layer (PAL) del modulo di protezione hardware

Il Platform Abstraction Layer del modulo di protezione hardware astrae tutto l'hardware della radice di attendibilità per isolare lo sviluppatore o l'utente di IoT Edge dalle relative complessità.  It includes a combination of application programming interface (API) and trans-domain communication procedures, for example communication between a standard execution environment and a secure enclave.  L'implementazione effettiva del Platform Abstraction Layer del modulo di protezione hardware dipende dallo specifico hardware sicuro in uso. La sua esistenza consente di usare praticamente qualsiasi hardware di processore sicuro.

## <a name="secure-silicon-root-of-trust-hardware"></a>Hardware della radice di attendibilità per il processore sicuro

Un processore sicuro è necessario per ancorare l'attendibilità nell'hardware del dispositivo IoT Edge.  I processori sicuri possono essere di versi tipi, come Trusted Platform Module (TPM), embedded Secure Element (eSE), ARM TrustZone, Intel SGX e tecnologie per processori sicuri personalizzate.  The use of secure silicon root of trust in devices is recommended given the threats associated with physical accessibility of IoT devices.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Integrazione e manutenzione del gestore sicurezza di IoT Edge

Il gestore sicurezza di IoT Edge ha come obiettivo di identificare e isolare i componenti che difendono la sicurezza e l'integrità della piattaforma Azure IoT Edge per la protezione avanzata personalizzata. Le terze parti, come i produttori di dispositivi, devono fare uso delle funzionalità di sicurezza personalizzate disponibili con l'hardware dei relativi dispositivi.  Vedere la sezione Passaggi successivi per collegamenti a esempi su come implementare la protezione avanzata del gestore sicurezza di Azure IoT con TPM (Trusted Platform Module) nelle piattaforme Linux e Windows. Questi esempi usano TPM software o virtuali, ma si applicano direttamente a dispositivi TPM discreti.  

## <a name="next-steps"></a>Passaggi successivi

Leggere il blog [Securing the intelligent edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/) (Protezione dei dispositivi perimetrali intelligenti).

Creare ed effettuare il provisioning di un [dispositivo IoT Edge con un TPM virtuale in una macchina virtuale Linux](how-to-auto-provision-simulated-device-linux.md).

Creare ed effettuare il provisioning di un [dispositivo IoT Edge con un TPM simulato in Windows](how-to-auto-provision-simulated-device-windows.md).
