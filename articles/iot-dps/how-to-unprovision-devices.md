---
title: Come annullare il provisioning di dispositivi registrati con il servizio Device Provisioning in hub IoT di Azure | Microsoft Docs
description: Come annullare il provisioning di dispositivi registrati tramite il servizio Device Provisioning nel portale di Azure
services: iot-dps
keywords: 
author: JimacoMS
ms.author: v-jamebr
ms.date: 01/08/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 83ed72c0f2eb342c372ef97e5443d60eab1e2174
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2018
---
# <a name="how-to-unprovision-devices-enrolled-by-your-provisioning-service"></a>Come annullare il provisioning di dispositivi registrati tramite il servizio di provisioning

È possibile che sia necessario annullare il provisioning di dispositivi precedentemente sottoposti a provisioning tramite il servizio di servizio Device Provisioning. È possibile, ad esempio, che un dispositivo debba essere venduto o spostato in un hub IoT diverso o che sia stato smarrito, rubato o danneggiato. 

In generale, l'annullamento del provisioning di un dispositivo si articola in due passaggi:

1. Revocare al dispositivo l'accesso al servizio di provisioning. A seconda che si intenda revocare l'accesso in modo temporaneo o permanente o, in presenza del meccanismo di attestazione X.509, si intenda revocarlo sulla gerarchia dei gruppi di registrazione esistenti, è possibile disabilitare o eliminare una voce di registrazione. 
 
   - Per informazioni su come revocare ai dispositivi l'accesso tramite il portale, vedere [Revocare l'accesso ai dispositivi](how-to-revoke-device-access-portal.md).
   - Per informazioni su come revocare ai dispositivi l'accesso a livello di programmazione tramite uno degli SDK del servizio Device Provisioning, vedere [Gestire le registrazioni dei dispositivi con SDK di servizi](how-to-manage-enrollments-sdks.md).

2. Disabilitare o eliminare la voce del dispositivo nel registro delle identità relativo all'hub IoT in cui è stato effettuato il provisioning. Per altre informazioni, vedere [Gestire le identità dei dispositivi](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) nella documentazione di hub IoT di Azure. 

L'esatta procedura da seguire per annullare il provisioning di un dispositivo dipende dal meccanismo di attestazione usato.

Per i dispositivi che usano l'attenzione TPM o X.509 con un certificato foglia autofirmato (senza catena di certificati), il provisioning viene effettuato tramite una singola voce di registrazione. Per questi dispositivi, è possibile eliminare la voce corrispondente in modo da revocarne l'accesso al servizio di provisioning in modo permanente oppure disabilitare la voce corrispondente in modo revocarne l'accesso in modo temporaneo, e quindi procedere eliminando o disabilitando il dispositivo nel registro delle identità relativo all'hub IoT in cui è stato effettuato il provisioning.

Con l'attestazione X.509, il provisioning dei dispositivi può essere effettuato anche tramite un gruppo di registrazione. I gruppi di registrazione sono configurati con un certificato di firma, che può essere un certificato intermedio o CA radice, e autorizzano l'accesso al servizio di provisioning solo ai dispositivi che dispongono di tale certificato nella rispettiva catena di certificati. Per altre informazioni sulla registrazione dei gruppi e dei certificati X.509 con il servizio di provisioning, vedere [Certificati X.509](concepts-security.md#x509-certificates). 

Per visualizzare l'elenco dei dispositivi di cui è stato effettuato il provisioning tramite un gruppo di registrazione, è possibile visualizzare i dettagli del gruppo di registrazione. In questo modo, è possibile capire facilmente in quale hub IoT è stato effettuato il provisioning di ogni dispositivo. Per visualizzare l'elenco dei dispositivi: 

1. Accedere al portale di Azure e fare clic su **Tutte le risorse** nel menu a sinistra.
2. Fare clic sul servizio di provisioning nell'elenco di risorse.
3. Nel servizio di provisioning, fare clic su **Manage enrollments** (Gestisci registrazioni), quindi selezionare la scheda **Enrollment Groups** (Gruppi di registrazione).
4. Fare clic sul gruppo di registrazione per aprirlo.

   ![Visualizzare la voce del gruppo di registrazione nel portale](./media/how-to-unprovision-devices/view-enrollment-group.png)

Con i gruppi di registrazione sono possibili due scenari:

- Per annullare il provisioning di tutti i dispositivi sottoposti a provisioning tramite un gruppo di registrazione, è necessario prima disabilitare il gruppo di registrazione e inserire il relativo certificato di firma nell'elenco degli elementi non consentiti. Usando l'elenco dei dispositivi sottoposti a provisioning nel gruppo di registrazione, è quindi possibile disabilitare o eliminare ogni dispositivo dal registro delle identità del rispettivo hub IoT. Dopo aver disabilitato o eliminato tutti i dispositivi dai rispettivi hub IoT, è possibile facoltativamente eliminare anche il gruppo di registrazione. Tenere presente tuttavia che, se si elimina il gruppo di registrazione ma è presente un gruppo di registrazione abilitato per un certificato di firma che si trova a un livello superiore nella catena dei certificati di uno o più dispositivi, è possibile eseguire di nuovo la registrazione di tali dispositivi. 
- Per annullare il provisioning di un singolo dispositivo da un gruppo di registrazione, è necessario prima creare una registrazione individuale disabilitata per il relativo certificato foglia (dispositivo). In questo modo, viene revocato l'accesso al servizio di provisioning solo per quel dispositivo e non per gli altri dispositivi che dispongono nella propria catena del certificato di firma del gruppo di registrazione. Usando l'elenco dei dispositivi sottoposti a provisioning disponibile nei dettagli del gruppo di registrazione, è quindi possibile trovare l'hub IoT in cui è stato effettuato il provisioning del dispositivo e disabilitarlo o eliminarlo dal registro delle entità dell'hub. Non eliminare la registrazione individuale disabilitata relativa al dispositivo, poiché questa operazione consentirebbe di eseguire nuovamente la registrazione del dispositivo tramite il gruppo di registrazione. 










