---
title: Terminologia usata con il servizio Device provisioning in hub Azure Microsoft Docs
description: Descrive la terminologia comune usata con il servizio Device provisioning (DPS) e l'hub Internet
author: wesmc7777
ms.author: wesmc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: b9fc37c6589cdd0bc6a5cdce7b7ebebe2c6e9a85
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531611"
---
# <a name="iot-hub-device-provisioning-service-dps-terminology"></a>Terminologia del servizio Device provisioning in hub Internet (DPS)

Il servizio Device Provisioning in hub IoT è un servizio helper per l'hub IoT che consente di configurare il provisioning completamente automatico dei dispositivi in un hub IoT specifico. Con il servizio Device provisioning è possibile effettuare il [provisioning di milioni di](about-iot-dps.md#provisioning-process) dispositivi in modo sicuro e scalabile.

Il provisioning dei dispositivi è un processo in due parti. La prima parte consiste nello stabilire la connessione iniziale tra il dispositivo e la soluzione Internet delle cose mediante la *registrazione* del dispositivo. La seconda parte consiste nell'applicare la *configurazione* corretta al dispositivo in base ai requisiti specifici della soluzione. Una volta completati entrambi questi passaggi, il *provisioning* del dispositivo può considerarsi completato. Il servizio Device Provisioning consente di automatizzare entrambi i passaggi per semplificare l'esperienza di provisioning dei dispositivi.

Questo articolo fornisce una panoramica dei concetti relativi al provisioning in relazione alla gestione del *servizio*. L'articolo è di particolare interesse per le persone coinvolte nella [fase di impostazione cloud](about-iot-dps.md#cloud-setup-step) in preparazione alla distribuzione di un dispositivo.

## <a name="service-operations-endpoint"></a>Endpoint delle operazioni del servizio

L'endpoint delle operazioni del servizio è l'endpoint per la gestione delle impostazioni del servizio e la manutenzione dell'elenco di registrazione. Questo endpoint viene usato solo dall'amministratore del servizio e non dai dispositivi.

## <a name="device-provisioning-endpoint"></a>Endpoint di provisioning dei dispositivi

L'endpoint di provisioning dei dispositivi è il singolo endpoint che tutti i dispositivi usano per il provisioning automatico. L'URL è lo stesso per tutte le istanze del servizio di provisioning, per eliminare la necessità di sovrascrivere la memoria dei dispositivi con nuove informazioni di connessione in scenari di supply chain. L'ambito ID garantisce l'isolamento dei tenant.

## <a name="linked-iot-hubs"></a>Hub IoT collegati

Il servizio Device Provisioning può effettuare il provisioning dei dispositivi solo negli hub IoT che sono stati collegati. Il collegamento di un hub IoT a un'istanza del servizio Device Provisioning fornisce al servizio le autorizzazioni di lettura/scrittura nel registro dei dispositivi dell'hub IoT. Con il collegamento, il servizio Device Provisioning può registrare un ID dispositivo e impostare la configurazione iniziale nel dispositivo gemello. Gli hub IoT collegati possono trovarsi in qualsiasi area di Azure. È possibile collegare hub di altre sottoscrizioni al servizio di provisioning.


## <a name="allocation-policy"></a>Criteri di allocazione

Impostazione a livello di servizio che determina in che modo il servizio Device Provisioning assegna i dispositivi a un hub IoT. Sono supportati tre criteri di allocazione:

* **Distribuzione ponderata uniforme**: gli hub IoT collegati hanno le stesse probabilità di essere scelti per il provisioning dei dispositivi. Si tratta dell'impostazione predefinita. Se si esegue il provisioning dei dispositivi in un solo hub IoT, è possibile mantenere questa impostazione.

* **Latenza minima**: il provisioning dei dispositivi viene effettuato in un hub IoT con latenza minima per il dispositivo. Se più hub IoT collegati forniscono la stessa latenza minima, il servizio di provisioning suddivide i dispositivi tra gli hub

* **Configurazione statica tramite l'elenco di registrazione**: la specifica dell'hub IoT desiderato nell'elenco di registrazione ha la priorità rispetto ai criteri di allocazione a livello di servizio.

* **Personalizzata (usare la funzione di Azure)**: i criteri di allocazione personalizzati offrono un maggiore controllo sulla modalità di assegnazione dei dispositivi a un hub. A tale scopo viene usato un codice personalizzato in una funzione di Azure per assegnare dispositivi a un hub IoT. Il servizio Device provisioning chiama il codice della funzione di Azure fornendo tutte le informazioni rilevanti sul dispositivo e la registrazione al codice. Viene eseguito il codice della funzione, che restituisce le informazioni dell'hub IoT usate per il provisioning del dispositivo.

## <a name="enrollment"></a>Registrazione

Una registrazione è un record di dispositivi o gruppi di dispositivi che possono registrarsi tramite il provisioning automatico. Il record di registrazione contiene informazioni sul dispositivo o sul gruppo di dispositivi, tra cui:
- il [meccanismo di attestazione](#attestation-mechanism) usato dal dispositivo
- la configurazione iniziale facoltativa desiderata
- l'hub IoT desiderato
- l'ID dispositivo desiderato

Il servizio Device Provisioning supporta due tipi di registrazione:

### <a name="enrollment-group"></a>Gruppo di registrazione

Un gruppo di registrazione è un gruppo di dispositivi che condividono un meccanismo di attestazione specifico. I gruppi di registrazione supportano sia X. 509 sia simmetrico. Tutti i dispositivi del gruppo di registrazione X. 509 presentano certificati X. 509 che sono stati firmati dalla stessa autorità di certificazione radice o intermedia (CA). Ogni dispositivo nel gruppo di registrazione di chiavi simmetriche presenta token SAS derivati dalla chiave simmetrica del gruppo. Il nome del gruppo di registrazione e il nome di certificato devono essere alfanumerici, in caratteri minuscoli e possono contenere segni meno.

> [!TIP]
> È consigliabile usare un gruppo di registrazione per un numero elevato di dispositivi che condividono una configurazione iniziale desiderata o per i dispositivi destinati allo stesso tenant.

### <a name="individual-enrollment"></a>Registrazione individuale

Una registrazione individuale è una voce per un singolo dispositivo che esegue la registrazione. Le registrazioni individuali possono usare certificati foglia X.509 o token di firma di accesso condiviso (in un TPM fisico o virtuale) come meccanismo di attestazione. L'ID di registrazione in una singola registrazione è alfanumerico, con caratteri minuscoli e può contenere segni meno. In caso di registrazione individuale è possibile specificare l'ID dispositivo hub IoT desiderato.

> [!TIP]
> È consigliabile usare le registrazioni individuali per i dispositivi che richiedono configurazioni iniziali univoche oppure per i dispositivi che possono autenticare solo usando token di firma di accesso condiviso tramite l'attestazione TPM.


## <a name="attestation-mechanism"></a>Meccanismo di attestazione

Un meccanismo di attestazione è il metodo usato per confermare l'identità di un dispositivo. Il meccanismo di attestazione viene configurato in una voce di registrazione e indica al servizio di provisioning il metodo da usare per la verifica dell'identità di un dispositivo durante la registrazione.

> [!NOTE]
> L'hub IoT usa lo "schema di autenticazione" per un concetto simile in tale servizio.

Il servizio Device Provisioning supporta i seguenti tipi di attestazione:
* **Certificati X.509** basati sul flusso di autenticazione del certificato X.509 standard. Per altre informazioni, vedere [attestazione X. 509](concepts-x509-attestation.md).
* **Trusted Platform Module (TPM)** basato su una richiesta di verifica nonce, che usa lo standard TPM relativo alle chiavi per presentare un token di firma di accesso condiviso (SAS). Questa operazione non richiede un TPM fisico sul dispositivo, ma il servizio prevede di attestare l'uso della chiave di verifica dell'autenticità per ogni [specifica TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/). Per ulteriori informazioni, vedere [attestazione TPM](concepts-tpm-attestation.md).
* **Chiave simmetrica** basata sui [token di sicurezza](../iot-hub/iot-hub-devguide-security.md#security-tokens)della firma di accesso condiviso, che includono una firma con hash e una scadenza incorporata. Per altre informazioni, vedere [Attestazione con chiave simmetrica](concepts-symmetric-key-attestation.md).


## <a name="hardware-security-module"></a>Modulo di protezione hardware

Il modulo di protezione hardware viene usato per l'archiviazione sicura, basata su hardware dei segreti dei dispositivi ed è il tipo di archiviazione dei segreti più sicuro. Nel modulo di protezione hardware è possibile archiviare sia i certificati X.509 che i token di firma di accesso condiviso. I moduli di protezione hardware possono essere usati con entrambi i meccanismi di attestazione supportati dal servizio di provisioning.

> [!TIP]
> È consigliabile usare un modulo di protezione hardware con i dispositivi per archiviare in modo sicuro i segreti nei dispositivi.

I segreti dei dispositivi possono essere archiviati anche nel software (memoria), ma si tratta di un tipo di archiviazione meno sicuro rispetto a un modulo di protezione hardware.



## <a name="id-scope"></a>Ambito ID

L'ambito ID viene assegnato a un servizio Device Provisioning quando viene creato dall'utente ed è usato per identificare in modo univoco il servizio di provisioning specifico usato dal dispositivo per la registrazione. L'ambito ID viene generato dal servizio e non è modificabile, per garantire l'univocità.

> [!NOTE]
> L'univocità è importante per le operazioni di distribuzione con esecuzione prolungata e gli scenari di fusione e acquisizione.


## <a name="registration"></a>Registrazione

Una registrazione è il record di un dispositivo che esegue correttamente la registrazione/il provisioning in un hub IoT tramite il servizio Device Provisioning. I record di registrazione vengono creati automaticamente. Possono essere eliminati, ma non aggiornati.


## <a name="registration-id"></a>ID di registrazione

L'ID registrazione viene usato per identificare in modo univoco una registrazione del dispositivo con il servizio Device provisioning. L'ID dispositivo deve essere univoco nell'[ambito ID](#id-scope) del servizio di provisioning. Ogni dispositivo deve avere un ID di registrazione. L'ID registrazione è alfanumerico, senza distinzione tra maiuscole e minuscole e può contenere caratteri speciali, tra cui due punti, punto, carattere di sottolineatura e trattino

* Nel caso di un TPM, l'ID di registrazione viene fornito dal TPM stesso.
* Nel caso dell'attestazione basata su X.509, l'ID di registrazione viene fornito come nome soggetto del certificato.

## <a name="device-id"></a>ID dispositivo

L'ID dispositivo è l'ID visualizzato nell'hub IoT. È possibile, ma non obbligatorio, impostare l'ID dispositivo desiderato nella voce di registrazione. L'impostazione dell'ID dispositivo desiderato è supportata solo nelle registrazioni singole. Se l'ID dispositivo desiderato non viene specificato nell'elenco di registrazione, l'ID di registrazione viene usato come ID dispositivo durante la registrazione del dispositivo. Vedere altre informazioni sugli [ID dispositivo nell'hub IoT](../iot-hub/iot-hub-devguide-identity-registry.md).



## <a name="operations"></a>Operazioni

Le operazioni rappresentano l'unità di fatturazione del servizio Device Provisioning. Un'operazione si riferisce al corretto completamento di un'istruzione nel servizio. Le operazioni includono le registrazioni e le ripetizioni delle registrazioni dei dispositivi, oltre alle modifiche sul lato del servizio, come l'aggiunta di voci all'elenco di registrazione e l'aggiornamento delle voci dell'elenco di registrazione.
