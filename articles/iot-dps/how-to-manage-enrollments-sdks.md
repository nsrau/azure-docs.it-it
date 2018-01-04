---
title: Gestire le registrazioni dei dispositivi con gli SDK servizio di Provisioning del dispositivo di Azure | Documenti Microsoft
description: Come gestire le registrazioni dei dispositivi in IoT Hub dispositivo servizio di Provisioning con il SDK del servizio
services: iot-dps
keywords: 
author: yzhong94
ms.author: yizhon
ms.date: 12/01/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: arjmands
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 82da49924e71a38ca557f244f2830e1da45826b1
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Come gestire le registrazioni dei dispositivi con gli SDK servizio di Provisioning del dispositivo di Azure
Oggetto *la registrazione del dispositivo* crea un record di un singolo dispositivo o un gruppo di dispositivi a un certo punto può registrare con il servizio di Provisioning di dispositivi. Il record di registrazione contiene la configurazione iniziale desiderata per i dispositivi come parte della registrazione, incluso l'hub IoT desiderato. In questo articolo viene illustrato come gestire le registrazioni dei dispositivi per il servizio di provisioning a livello di programmazione utilizzando Azure IoT Provisioning del servizio SDK.  il SDK sono disponibili su GitHub nello stesso repository come IoT di Azure SDK.

## <a name="samples"></a>Esempi
Questo articolo vengono descritti i concetti di alto livelli per la gestione delle registrazioni dei dispositivi per il servizio di provisioning a livello di programmazione utilizzando Azure IoT Provisioning del servizio SDK.  Chiamate API esatte potrebbero essere diverse a causa delle differenze di lingua.  Vedere gli esempi che vengono forniti in GitHub per informazioni dettagliate:
* [Esempi di Client del servizio di Provisioning Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
* [Esempi di Provisioning Client del servizio di Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)

## <a name="prerequisites"></a>Prerequisiti
* Stringa di connessione da un'istanza del servizio di Provisioning di dispositivi
* Elementi di sicurezza di dispositivi:
    * [**TPM**](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-security):
        * Registrazione di singoli: ID di registrazione e la chiave di verifica dell'autenticità TPM da un dispositivo fisico o da TPM simulatore.
        * Gruppo di registrazione non è applicabile per l'attestazione TPM.
    * [**X. 509**](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-security):
        * Registrazione di singoli: il [certificato foglia](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-security#leaf-certificate) dal dispositivo fisico o dall'emulatore di lancio dei dadi.
        * Gruppo di registrazione: il [certificato radice](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-security#root-certificate) o [certificato intermedio](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-security#intermediate-certificate), utilizzato per produrre certificato del dispositivo in un dispositivo fisico.  Può anche essere generato dall'emulatore di lancio dei dadi.

## <a name="create-a-device-enrollment"></a>Creare una registrazione dei dispositivi

Esistono due modi per registrare i dispositivi con il servizio di provisioning:

* Un **gruppo registrazione** è una voce per un gruppo di dispositivi che condividono un meccanismo di attestazione comuni dei certificati x. 509, firmato dal [certificato radice](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-security#root-certificate) o [certificato intermedio ](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-security#intermediate-certificate). È consigliabile usare un gruppo di registrazione per un numero elevato di dispositivi che condividono una configurazione iniziale desiderata o per i dispositivi destinati allo stesso tenant. Si noti che è possibile registrare solo i dispositivi che usano il meccanismo di attestazione X.509 come *gruppi di registrazione*. 

    È possibile creare un gruppo di registrazione con il SDK di questo flusso di lavoro seguente:

    1. Per il gruppo di registrazione, il meccanismo di attestazione Usa certificato x. 509.  Chiamare l'API del servizio SDK ```X509Attestation.createFromRootCertificate``` con il certificato radice per creare l'attestazione per la registrazione.  Certificato x. 509 viene fornito in un file PEM o sotto forma di stringa.
    1. Creare un nuovo ```EnrollmentGroup``` utilizzando variabili di ```attestation``` creato e univoco ```enrollmentGroupId```.  Facoltativamente, è possibile impostare parametri come ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    2. Chiamare l'API del servizio SDK ```createOrUpdateEnrollmentGroup``` nell'applicazione back-end con ```EnrollmentGroup``` per creare un gruppo di registrazione.

* Una **registrazione individuale** è una voce per un singolo dispositivo che esegue la registrazione. Le registrazioni individuali possono usare certificati X.509 o token di firma di accesso condiviso (in un TPM reale o virtuale) come meccanismo di attestazione. È consigliabile usare le registrazioni individuali per i dispositivi che richiedono configurazioni iniziali univoche oppure per i dispositivi che possono usare solo token di firma di accesso condiviso tramite TPM o TPM virtuale come meccanismo di attestazione. In caso di registrazione individuale è possibile specificare l'ID dispositivo hub IoT desiderato.

    È possibile creare una registrazione singoli con il SDK di questo flusso di lavoro seguente:
    
    1. Scegliere il ```attestation``` meccanismo, che può essere TPM o x. 509.
        1. **TPM**: usando la chiave di verifica dell'autenticità da un dispositivo fisico o da simulatore TPM come input, è possibile chiamare l'API del servizio SDK ```TpmAttestation``` per creare l'attestazione per la registrazione. 
        2. **X. 509**: utilizzando il certificato client come input, è possibile chiamare l'API del servizio SDK ```X509Attestation.createFromClientCertificate``` per creare l'attestazione per la registrazione.
    2. Creare un nuovo ```IndividualEnrollment``` variabile con il ```attestation``` creato e univoco ```registrationId``` come input, che nel dispositivo o generati dal simulatore TPM.  Facoltativamente, è possibile impostare parametri come ```Device ID```, ```IoTHubHostName```, ```ProvisioningStatus```.
    3. Chiamare l'API del servizio SDK ```createOrUpdateIndividualEnrollment``` nell'applicazione back-end con ```IndividualEnrollment``` per creare una registrazione singoli.

Dopo avere creato una registrazione, il servizio di Provisioning di dispositivi restituirà un risultato di registrazione.

Questo flusso di lavoro viene illustrata la [esempi](#samples).

## <a name="update-an-enrollment-entry"></a>Aggiornare una voce di registrazione

Dopo aver creato una voce di registrazione, si desidera aggiornare la registrazione.  Potenziali scenari includono l'aggiornamento della proprietà desiderata, aggiornando il metodo di attestazione o revoca dell'accesso al dispositivo.  Sono disponibili diverse API per la registrazione di singoli e registrazione di gruppo, ma nessuna distinzione per il meccanismo di attestazione.

È possibile aggiornare una voce di registrazione seguenti questo flusso di lavoro:
* **Registrazione di singoli**:
    1. Ottenere la registrazione più recente dal servizio di provisioning prima con API SDK del servizio ```getIndividualEnrollment```.
    2. Modificare il parametro della registrazione più recente in base alle esigenze. 
    3. Tramite la registrazione più recente, chiamare l'API SDK del servizio ```createOrUpdateIndividualEnrollment``` per aggiornare la voce di registrazione.
* **Registrazione di gruppo**:
    1. Ottenere la registrazione più recente dal servizio di provisioning prima con API SDK del servizio ```getEnrollmentGroup```.
    2. Modificare il parametro della registrazione più recente in base alle esigenze.
    3. Tramite la registrazione più recente, chiamare l'API SDK del servizio ```createOrUpdateEnrollmentGroup``` per aggiornare la voce di registrazione.

Questo flusso di lavoro viene illustrata la [esempi](#samples).

## <a name="remove-an-enrollment-entry"></a>Rimuovere una voce di registrazione

* **Registrazione di singoli** può essere eliminato chiamando l'API del servizio SDK ```deleteIndividualEnrollment``` utilizzando ```registrationId```.
* **Registrazione di gruppo** può essere eliminato chiamando l'API del servizio SDK ```deleteEnrollmentGroup``` utilizzando ```enrollmentGroupId```.

Questo flusso di lavoro viene illustrata la [esempi](#samples).

## <a name="bulk-operation-on-individual-enrollments"></a>Operazione BULK sulle registrazioni dei singoli

È possibile eseguire l'operazione bulk per creare, aggiornare o rimuovere più iscrizioni singoli seguenti questo flusso di lavoro:

1. Creare una variabile che contiene più ```IndividualEnrollment```.  Implementazione di questa variabile è diverso per ogni lingua.  Esaminare l'esempio di operazione bulk su GitHub per informazioni dettagliate.
2. Chiamare l'API del servizio SDK ```runBulkOperation``` con un ```BulkOperationMode``` per l'operazione desiderata e la variabile per le registrazioni dei singoli. Sono supportate quattro modalità: creare, aggiornare, updateIfMatchEtag ed eliminare.

Dopo aver correttamente eseguito un'operazione, il servizio di Provisioning di dispositivi restituirà un risultato dell'operazione bulk.

Questo flusso di lavoro viene illustrata la [esempi](#samples).
