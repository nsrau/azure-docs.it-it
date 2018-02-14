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
ms.openlocfilehash: 1d057a4df43cf25e6817672d198207d9a50e462e
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="how-to-unprovision-devices-enrolled-by-your-provisioning-service"></a>Come annullare il provisioning di dispositivi registrati tramite il servizio di provisioning

È possibile che sia necessario annullare il provisioning di dispositivi precedentemente sottoposti a provisioning tramite il servizio di servizio Device Provisioning. È possibile, ad esempio, che un dispositivo debba essere venduto o spostato in un hub IoT diverso o che sia stato smarrito, rubato o danneggiato. 

In generale, l'annullamento del provisioning di un dispositivo si articola in due passaggi:

1. Revocare al dispositivo l'accesso al servizio di provisioning. A seconda che si intenda revocare l'accesso in modo temporaneo o permanente o, in presenza del meccanismo di attestazione X.509, si intenda revocarlo sulla gerarchia dei gruppi di registrazione esistenti, è possibile disabilitare o eliminare una voce di registrazione. 
 
   - Per informazioni su come revocare ai dispositivi l'accesso tramite il portale, vedere [Revocare l'accesso ai dispositivi](how-to-revoke-device-access-portal.md).
   - Per informazioni su come revocare ai dispositivi l'accesso a livello di programmazione tramite uno degli SDK del servizio Device Provisioning, vedere [Gestire le registrazioni dei dispositivi con SDK di servizi](how-to-manage-enrollments-sdks.md).

2. Disabilitare o eliminare la voce del dispositivo nel registro delle identità relativo all'hub IoT in cui è stato effettuato il provisioning. Per altre informazioni, vedere [Gestire le identità dei dispositivi](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) nella documentazione di hub IoT di Azure. 

I passaggi esatti per annullare il provisioning di un dispositivo dipendono dal meccanismo di attestazione del dispositivo e dalla relativa voce di registrazione applicabile con il servizio di provisioning.

## <a name="individual-enrollments"></a>Registrazioni singole
Per i dispositivi che usano l'attestazione TPM o X.509 con un certificato foglia, il provisioning viene effettuato tramite una voce di registrazione singola. 

Per annullare il provisioning di un dispositivo associato a una registrazione singola: 
1. Per i dispositivi che usano l'attestazione TPM, eliminare la voce di registrazione singola per revocare in modo permanente l'accesso del dispositivo al servizio di provisioning oppure disabilitare la voce per revocarne temporaneamente l'accesso. Per i dispositivi che usano l'attestazione X.509, è possibile eliminare o disabilitare la voce. Tenere tuttavia presente che, se si elimina una registrazione singola per un dispositivo che usa l'attestazione X.509 ed è presente un gruppo di registrazioni abilitato per un certificato di firma nella catena di certificati del dispositivo, il dispositivo potrà effettuare di nuovo la registrazione. Per questi dispositivi, può essere più sicuro disabilitare la voce di registrazione. In questo modo, è possibile impedire al dispositivo di effettuare di nuovo la registrazione, indipendentemente dalla presenza o meno di un gruppo di registrazioni abilitato per uno dei certificati di firma.
2. Disabilitare o eliminare la voce del dispositivo nel registro delle identità dell'hub IoT in cui è stato effettuato il provisioning. 


## <a name="enrollment-groups"></a>Gruppi di registrazioni
Con l'attestazione X.509, il provisioning dei dispositivi può essere effettuato anche tramite un gruppo di registrazione. I gruppi di registrazione sono configurati con un certificato di firma, che può essere un certificato intermedio o CA radice, e autorizzano l'accesso al servizio di provisioning solo ai dispositivi che dispongono di tale certificato nella rispettiva catena di certificati. Per altre informazioni sulla registrazione dei gruppi e dei certificati X.509 con il servizio di provisioning, vedere [Certificati X.509](concepts-security.md#x509-certificates). 

Per visualizzare l'elenco dei dispositivi di cui è stato effettuato il provisioning tramite un gruppo di registrazione, è possibile visualizzare i dettagli del gruppo di registrazione. In questo modo, è possibile capire facilmente in quale hub IoT è stato effettuato il provisioning di ogni dispositivo. Per visualizzare l'elenco dei dispositivi: 

1. Accedere al portale di Azure e fare clic su **Tutte le risorse** nel menu a sinistra.
2. Fare clic sul servizio di provisioning nell'elenco di risorse.
3. Nel servizio di provisioning, fare clic su **Manage enrollments** (Gestisci registrazioni), quindi selezionare la scheda **Enrollment Groups** (Gruppi di registrazione).
4. Fare clic sul gruppo di registrazione per aprirlo.

   ![Visualizzare la voce del gruppo di registrazione nel portale](./media/how-to-unprovision-devices/view-enrollment-group.png)

Con i gruppi di registrazione sono possibili due scenari:

- Per annullare il provisioning di tutti i dispositivi sottoposti a provisioning tramite un gruppo di registrazioni:
  1. Disabilitare il gruppo di registrazioni per impedirne il certificato di firma. 
  2. Usare l'elenco dei dispositivi sottoposti a provisioning per il gruppo di registrazioni per disabilitare o eliminare ogni dispositivo dal registro delle identità del rispettivo hub IoT. 
  3. Dopo aver disabilitato o eliminato tutti i dispositivi dai rispettivi hub IoT, è possibile facoltativamente eliminare anche il gruppo di registrazione. Tenere presente tuttavia che, se si elimina il gruppo di registrazione ma è presente un gruppo di registrazione abilitato per un certificato di firma che si trova a un livello superiore nella catena dei certificati di uno o più dispositivi, è possibile eseguire di nuovo la registrazione di tali dispositivi. 
- Per annullare il provisioning di un singolo dispositivo da un gruppo di registrazioni:
  1. Creare una registrazione singola disabilitata per il certificato (dispositivo) foglia. In questo modo, viene revocato l'accesso al servizio di provisioning solo per quel dispositivo e non per gli altri dispositivi che dispongono nella propria catena del certificato di firma del gruppo di registrazione. Non eliminare la registrazione individuale disabilitata relativa al dispositivo, poiché questa operazione consentirebbe di eseguire nuovamente la registrazione del dispositivo tramite il gruppo di registrazione. 
  2. Usare l'elenco dei dispositivi sottoposti a provisioning per il gruppo di registrazioni per trovare l'hub IoT in cui è stato effettuato il provisioning del dispositivo e disabilitarlo o eliminarlo dal registro delle identità dell'hub. 
  
  










