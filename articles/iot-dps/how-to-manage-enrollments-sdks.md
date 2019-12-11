---
title: Gestire le registrazioni dei dispositivi con gli SDK di Azure DPS
description: Come gestire le registrazioni dei dispositivi nel servizio Device provisioning in hub Internet (DPS) tramite gli SDK del servizio
author: robinsh
ms.author: robinsh
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 5cb0e25ec70956e66f7b867f0d0b9473160fc3ad
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975075"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Come gestire le registrazioni dei dispositivi con gli SDK del servizio Device Provisioning di Azure
La *registrazione dei dispositivi* crea un record di un singolo dispositivo o di un gruppo di dispositivi registrato in un dato punto con il servizio Device Provisioning. Il record di registrazione contiene la configurazione iniziale desiderata per i dispositivi come parte della registrazione, incluso l'hub IoT desiderato. In questo articolo viene illustrato come gestire le registrazioni dei dispositivi per il servizio di provisioning a livello di programmazione tramite gli SDK del servizio di provisioning di Azure IoT.  Gli SDK sono disponibili su GitHub nello stesso repository degli SDK di Azure IoT.

## <a name="prerequisites"></a>Prerequisiti
* Ottenere la stringa di connessione da un'istanza del servizio Device Provisioning.
* Ottenere gli elementi di sicurezza dei dispositivi per il [meccanismo di attestazione](concepts-security.md#attestation-mechanism) in uso:
    * [**TPM (Trusted Platform Module)** ](/azure/iot-dps/concepts-security#trusted-platform-module):
        * Registrazione singola: ID registrazione e chiave di verifica dell'autenticità del TPM da un dispositivo fisico o da un simulatore TPM.
        * Il gruppo di registrazioni non è valido per l'attestazione TPM.
    * [**X.509**](/azure/iot-dps/concepts-security):
        * Registrazione singola: [certificato foglia](/azure/iot-dps/concepts-security) dal dispositivo fisico o dall'emulatore [DICE](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) dell'SDK.
        * Gruppo di registrazioni: [certificato della CA/radice](/azure/iot-dps/concepts-security#root-certificate) o [certificato intermedio](/azure/iot-dps/concepts-security#intermediate-certificate), usato per generare il certificato del dispositivo in un dispositivo fisico.  Può anche essere generato dall'emulatore DICE dell'SDK.
* Le chiamate API possono essere diverse a seconda dei diversi linguaggi. Per i dettagli fare riferimento agli esempi disponibili su GitHub:
   * [Esempi client del servizio di provisioning per Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [Esempi client del servizio di provisioning per Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [Esempi client del servizio di provisioning per .NET](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Creare una registrazione dei dispositivi
Esistono due modi per registrare i dispositivi con il servizio di provisioning:

* Un **gruppo di registrazioni** è una voce relativa a un gruppo di dispositivi che condividono un meccanismo di attestazione comune dei certificati X.509, firmato mediante il [certificato radice](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) o il [certificato intermedio](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate). È consigliabile usare un gruppo di registrazione per un numero elevato di dispositivi che condividono una configurazione iniziale desiderata o per i dispositivi destinati allo stesso tenant. Si noti che è possibile registrare solo i dispositivi che usano il meccanismo di attestazione X.509 come *gruppi di registrazione*. 

    È possibile creare un gruppo di registrazioni con gli SDK attenendosi al flusso di lavoro seguente:

    1. Per il gruppo di registrazioni, il meccanismo di attestazione usa il certificato X.509.  Chiamare l'API dell'SDK del servizio ```X509Attestation.createFromRootCertificate``` con il certificato radice per creare l'attestazione per la registrazione.  Il certificato X.509 viene fornito in un file con estensione pem o sotto forma di stringa.
    1. Creare una nuova variabile ```EnrollmentGroup``` tramite il valore del parametro ```attestation``` creato e un valore univoco del parametro ```enrollmentGroupId```.  Facoltativamente, è possibile impostare parametri quali, ad esempio, ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    2. Chiamare l'API dell'SDK del servizio ```createOrUpdateEnrollmentGroup``` nell'applicazione back-end tramite ```EnrollmentGroup``` per creare un gruppo di registrazioni.

* Una **registrazione individuale** è una voce per un singolo dispositivo che esegue la registrazione. Le registrazioni singole possono usare certificati X.509 o token di firma di accesso condiviso (in un TPM fisico o virtuale) come meccanismo di attestazione. È consigliabile usare le registrazioni singole per i dispositivi che richiedono configurazioni iniziali univoche oppure per i dispositivi che possono usare solo token di firma di accesso condiviso tramite TPM o TPM virtuale come meccanismo di attestazione. In caso di registrazione individuale è possibile specificare l'ID dispositivo hub IoT desiderato.

    È possibile creare una registrazione singola con gli SDK attenendosi al flusso di lavoro seguente:
    
    1. Scegliere il meccanismo per ```attestation```, che può essere TPM o X.509.
        1. **TPM**: usando la chiave di verifica dell'autenticità da un dispositivo fisico o da simulatore TPM come input, è possibile chiamare l'API dell'SDK del servizio ```TpmAttestation``` per creare l'attestazione per la registrazione. 
        2. **X.509**: usando il certificato client come input, è possibile chiamare l'API dell'SDK del servizio ```X509Attestation.createFromClientCertificate``` per creare l'attestazione per la registrazione.
    2. Creare una nuova variabile ```IndividualEnrollment``` usando il valore del parametro ```attestation``` creato e un valore univoco del parametro ```registrationId``` come input, disponibile nel dispositivo o generato dal simulatore TPM.  Facoltativamente, è possibile impostare parametri quali, ad esempio, ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    3. Chiamare l'API dell'SDK del servizio ```createOrUpdateIndividualEnrollment``` nell'applicazione back-end tramite ```IndividualEnrollment``` per creare una registrazione singola.

Dopo avere creato una registrazione, il servizio Device Provisioning restituirà un risultato di registrazione. Questo flusso di lavoro viene illustrato negli esempi [citati in precedenza](#prerequisites).

## <a name="update-an-enrollment-entry"></a>Aggiornare una voce di registrazione

Dopo aver creato una voce di registrazione, potrebbe essere necessario aggiornare la registrazione.  I potenziali scenari includono l'aggiornamento della proprietà desiderata, l'aggiornamento del metodo di attestazione o la revoca dell'accesso al dispositivo.  Sono disponibili diverse API per la registrazione singola e di gruppo, ma esiste alcuna distinzione per il meccanismo di attestazione.

È possibile aggiornare una voce di registrazione attenendosi al flusso di lavoro seguente:
* **Registrazione singola**:
    1. Ottenere la registrazione più recente dal servizio di provisioning prima con l'API dell'SDK del servizio ```getIndividualEnrollment```.
    2. Modificare il parametro della registrazione più recente, se necessario. 
    3. Tramite la registrazione più recente chiamare l'API dell'SDK del servizio ```createOrUpdateIndividualEnrollment``` per aggiornare la voce di registrazione.
* **Registrazione di gruppo**:
    1. Ottenere la registrazione più recente dal servizio di provisioning prima con l'API dell'SDK del servizio ```getEnrollmentGroup```.
    2. Modificare il parametro della registrazione più recente, se necessario.
    3. Tramite la registrazione più recente chiamare l'API dell'SDK del servizio ```createOrUpdateEnrollmentGroup``` per aggiornare la voce di registrazione.

Questo flusso di lavoro viene illustrato negli esempi [citati in precedenza](#prerequisites).

## <a name="remove-an-enrollment-entry"></a>Rimuovere una voce di registrazione

* La **registrazione singola** può essere eliminata chiamando l'API dell'SDK del servizio ```deleteIndividualEnrollment``` tramite ```registrationId```.
* La **registrazione di gruppo** può essere eliminata chiamando l'API dell'SDK del servizio ```deleteEnrollmentGroup``` tramite ```enrollmentGroupId```.

Questo flusso di lavoro viene illustrato negli esempi [citati in precedenza](#prerequisites).

## <a name="bulk-operation-on-individual-enrollments"></a>Operazione in blocco sulle registrazioni singole

È possibile eseguire un'operazione in blocco per creare, aggiornare o rimuovere più iscrizioni singole attenendosi al flusso di lavoro seguente:

1. Creare una variabile che contiene più ```IndividualEnrollment```.  L'implementazione di questa variabile è diversa per ciascun linguaggio.  Esaminare l'esempio di operazione in blocco disponibile su GitHub per informazioni dettagliate.
2. Chiamare l'API dell'SDK del servizio ```runBulkOperation``` con un valore per ```BulkOperationMode``` per l'operazione desiderata e la variabile in uso per le registrazioni singole. Sono supportate quattro modalità: create, update, updateIfMatchEtag e delete.

Dopo aver eseguito correttamente un'operazione, il servizio Device Provisioning restituirà un risultato per l'operazione in blocco.

Questo flusso di lavoro viene illustrato negli esempi [citati in precedenza](#prerequisites).
