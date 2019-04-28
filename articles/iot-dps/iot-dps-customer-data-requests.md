---
title: Funzionalità di richiesta dati dei clienti
author: dominicbetts
ms.author: dobett
manager: timlt
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 5dd027c886e8102e77ddefe93817daee0e1ec29b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626435"
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

* Per eliminare le informazioni di registrazione per un singolo dispositivo è possibile usare [Device Enrollment - Delete](/rest/api/iot-dps/deleteindividualenrollment/deleteindividualenrollment) (Registrazione dispositivo - Elimina).
* Per eliminare le informazioni di registrazione per un gruppo di dispositivi è possibile usare [Device Enrollment group - Delete](/rest/api/iot-dps/deleteenrollmentgroup/deleteenrollmentgroup) (Registrazione gruppo di dispositivi - Elimina).
* Per eliminare le informazioni sui dispositivi che sono stati sottoposti a provisioning, è possibile usare [Registration State - Delete Registration State](/rest/api/iot-dps/deletedeviceregistrationstate/deletedeviceregistrationstate) (Stato di registrazione - Elimina stato di registrazione).

## <a name="exporting-customer-data"></a>Esportazione di dati del cliente

Il servizio di provisioning di dispositivi archivia le registrazioni e i record di registrazione. Le registrazioni contengono informazioni sui dispositivi sui quali è possibile eseguire il provisioning e i record di registrazione mostrano i dispositivi che sono già stati sottoposti al processo di provisioning.

Gli amministratori del tenant possono visualizzare le registrazioni e i record di registrazione nel portale di Azure ed esportarle tramite operazioni di copia e incolla.

Per altre informazioni su come gestire le registrazioni, vedere l'argomento relativo a [come gestire le registrazioni dei dispositivi](how-to-manage-enrollments.md).

È anche possibile eseguire le operazioni di esportazione per le registrazioni e i record di registrazione usando le API REST:

* Per esportare le informazioni di registrazione per un singolo dispositivo è possibile usare [Device Enrollment - Get](/rest/api/iot-dps/getindividualenrollment/getindividualenrollment) (Registrazione dispositivo - Ottieni).
* Per esportare le informazioni di registrazione per un gruppo di dispositivi è possibile usare [Device Enrollment group - Get](/rest/api/iot-dps/getenrollmentgroup/getenrollmentgroup) (Registrazione gruppo di dispositivi - Ottieni).
* Per esportare le informazioni sui dispositivi che sono già stati sottoposti a provisioning, è possibile usare [Registration State - Get Registration State](/rest/api/iot-dps/getdeviceregistrationstate/getdeviceregistrationstate) (Stato di registrazione - Ottieni stato di registrazione).

> [!NOTE]
> Quando si usano i servizi enterprise di Microsoft, Microsoft genera alcune informazioni note come log generati dal sistema. Alcuni log generati dal sistema del servizio di provisioning di dispositivi non sono accessibili o esportabili dagli amministratori del tenant. Questi log costituiscono azioni pratiche eseguite nel servizio e i dati diagnostici relativi ai singoli dispositivi.

## <a name="links-to-additional-documentation"></a>Collegamenti a documentazione aggiuntiva

La documentazione completa per le API per il provisioning di dispositivi si trova in [https://docs.microsoft.com/rest/api/iot-dps](https://docs.microsoft.com/rest/api/iot-dps).

[Funzionalità di richiesta dati dei clienti](../iot-hub/iot-hub-customer-data-requests.md) hub IoT.