---
title: Funzionalità di richiesta dati dei clienti
author: dominicbetts
ms.author: dobett
manager: timlt
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: d6355926c8fac62b01c36d28265842b1233ce213
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38666944"
---
# <a name="summary-of-customer-data-request-features"></a>Riepilogo delle funzionalità di richiesta dati dei clienti

Il servizio Device Provisioning in hub IoT di Azure è un servizio API REST basato su cloud orientato ai clienti aziendali che attiva il provisioning completamente automatico dei dispositivi su hub IoT con una protezione che inizia dal dispositivo e termina nel cloud.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Ai singoli dispositivi viene assegnato un ID registrazione e un ID dispositivo da un amministratore del tenant. I dati relativi e provenienti da questi dispositivi sono basati su questi ID. Microsoft non gestisce alcuna informazione e non dispone dell'accesso ai dati che permetterebbero la correlazione tra questi dispositivi e un individuo.

Molti dei dispositivi gestiti nel servizio di provisioning di dispositivi non sono dispositivi personali, ad esempio un termostato da ufficio o un robot industriale. I clienti possono, tuttavia, decidere di rendere personalmente identificabili alcuni dispositivi e, a loro discrezione, possono gestire i propri asset o i propri metodi di verifica dell'inventario che collegano i dispositivi agli individui. Il servizio di provisioning di dispositivi gestisce e archivia tutti i dati associati dispositivi come se fossero dati personali.

Gli amministratori del tenant possono usare il portale di Azure o le API REST del servizio per soddisfare le richieste di informazioni mediante l'esportazione o l'eliminazione dei dati associati a un ID dispositivo o un ID registrazione.

> [!NOTE]
> I dispositivi che sono stati sottoposti a provisioning nell'hub IoT tramite il servizio di provisioning di dispositivi presentano dati aggiuntivi archiviati nel servizio hub IoT. Vedere la [documentazione di riferimento dell'hub IoT](../iot-hub/iot-hub-customer-data-requests.md) per effettuare una richiesta completa per un determinato dispositivo.

## <a name="deleting-customer-data"></a>Eliminazione di dati del cliente

Il servizio di provisioning di dispositivi archivia le registrazioni e i record di registrazione. Le registrazioni contengono informazioni sui dispositivi sui quali è possibile eseguire il provisioning e i record di registrazione mostrano i dispositivi che sono già stati sottoposti al processo di provisioning.

Gli amministratori del tenant possono rimuovere le registrazioni dal portale di Azure e in questo modo vengono rimossi anche tutti i record di registrazione associati.

Per altre informazioni, vedere l'argomento relativo a [come gestire le registrazioni dei dispositivi](how-to-manage-enrollments.md).

È anche possibile eseguire le operazioni di eliminazione per le registrazioni e i record di registrazione usando le API REST:

* Per eliminare le informazioni di registrazione per un singolo dispositivo è possibile usare [Device Enrollment - Delete](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollment/delete) (Registrazione dispositivo - Elimina).
* Per eliminare le informazioni di registrazione per un gruppo di dispositivi è possibile usare [Device Enrollment group - Delete](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollmentgroup/delete) (Registrazione gruppo di dispositivi - Elimina).
* Per eliminare le informazioni sui dispositivi che sono stati sottoposti a provisioning, è possibile usare [Registration State - Delete Registration State](https://docs.microsoft.com/rest/api/iot-dps/registrationstate/deleteregistrationstate) (Stato di registrazione - Elimina stato di registrazione).

## <a name="exporting-customer-data"></a>Esportazione di dati del cliente

Il servizio di provisioning di dispositivi archivia le registrazioni e i record di registrazione. Le registrazioni contengono informazioni sui dispositivi sui quali è possibile eseguire il provisioning e i record di registrazione mostrano i dispositivi che sono già stati sottoposti al processo di provisioning.

Gli amministratori del tenant possono visualizzare le registrazioni e i record di registrazione nel portale di Azure ed esportarle tramite operazioni di copia e incolla.

Per altre informazioni su come gestire le registrazioni, vedere l'argomento relativo a [come gestire le registrazioni dei dispositivi](how-to-manage-enrollments.md).

È anche possibile eseguire le operazioni di esportazione per le registrazioni e i record di registrazione usando le API REST:

* Per esportare le informazioni di registrazione per un singolo dispositivo è possibile usare [Device Enrollment - Get](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollment/get) (Registrazione dispositivo - Ottieni).
* Per esportare le informazioni di registrazione per un gruppo di dispositivi è possibile usare [Device Enrollment group - Get](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollmentgroup/get) (Registrazione gruppo di dispositivi - Ottieni).
* Per esportare le informazioni sui dispositivi che sono già stati sottoposti a provisioning, è possibile usare [Registration State - Get Registration State](https://docs.microsoft.com/rest/api/iot-dps/registrationstate/getregistrationstate) (Stato di registrazione - Ottieni stato di registrazione).

> [!NOTE]
> Quando si usano i servizi enterprise di Microsoft, Microsoft genera alcune informazioni note come log generati dal sistema. Alcuni log generati dal sistema del servizio di provisioning di dispositivi non sono accessibili o esportabili dagli amministratori del tenant. Questi log costituiscono azioni pratiche eseguite nel servizio e i dati diagnostici relativi ai singoli dispositivi.

## <a name="links-to-additional-documentation"></a>Collegamenti a documentazione aggiuntiva

La documentazione completa per le API per il provisioning di dispositivi si trova in [https://docs.microsoft.com/rest/api/iot-dps](https://docs.microsoft.com/rest/api/iot-dps).

[Funzionalità di richiesta dati dei clienti](../iot-hub/iot-hub-customer-data-requests.md) hub IoT.