---
title: Come gestire l'accesso al dispositivo per il servizio di Provisioning di Azure IoT Hub dispositivo | Documenti Microsoft
description: Come revocare l'accesso ai dispositivi al servizio dei punti di distribuzione nel portale di Azure
services: iot-dps
keywords: 
author: JimacoMS
ms.author: v-jamebr
ms.date: 12/22/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 7985fba68ef2c6f651c64756f8c534928b573de5
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/23/2017
---
# <a name="how-to-revoke-device-access-to-your-provisioning-service-in-the-azure-portal"></a>Come revocare l'accesso dei dispositivi per il servizio di provisioning nel portale di Azure

Corretta gestione delle credenziali del dispositivo è fondamentale per i sistemi di alto profilo come soluzioni IoT. Una procedura consigliata per tali sistemi è predisporre un piano chiaro di come revocare l'accesso per i dispositivi nei casi in cui le credenziali, se un token di firma di accesso condiviso o un certificato x. 509, potrebbero essere compromessa. In questo articolo viene descritto come revocare l'accesso dei dispositivi durante il passaggio di provisioning.

Per informazioni sulla revoca dispositivo l'accesso a un hub IoT dopo che il dispositivo è stato eseguito il provisioning. vedere [disabilitare dispositivi](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices).

> [!NOTE] 
> Tenere presente i criteri di ripetizione dei dispositivi che è revocato l'accesso. Ad esempio, un dispositivo con un criterio di ripetizione infinito continuamente potrebbe provare a registrare con il servizio di provisioning, risorse del servizio e probabilmente influenzando le prestazioni.

## <a name="blacklist-devices-with-an-individual-enrollment-entry"></a>Blacklist dispositivi con una voce di registrazione singoli

Le registrazioni dei singoli si applicano a un singolo dispositivo e può utilizzare i certificati x. 509 o token di firma di accesso condiviso (in un modulo TPM reali o virtuali) come meccanismo di attestazione. (Dispositivi che utilizzano i token di firma di accesso condiviso, come il meccanismo di attestazione può solo eseguire il provisioning tramite una registrazione di singoli). Per disattivare un dispositivo che dispone di una registrazione singoli, è possibile disabilitare o eliminare la voce di registrazione: 

- Per disattivare temporaneamente il dispositivo, è possibile disabilitare la voce di registrazione. 

    1. Accedere al portale di Azure e fare clic su **tutte le risorse** dal menu a sinistra.
    2. Selezionare il servizio di provisioning che si desidera disattivare il dispositivo nell'elenco di risorse.
    3. Nel servizio di provisioning, fare clic su **Manage enrollments** (Gestisci registrazioni), quindi selezionare la scheda **Individual Enrollments** (Registrazioni singole).
    4. Fare clic sulla voce di registrazione per il dispositivo che si desidera disattivare per aprirlo. 
    5. Fare clic su **disabilitare** nella parte inferiore della voce di elenco di registrazione, quindi fare clic su **salvare**.  

        ![Disabilitare la voce di registrazione singoli nel portale di](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)
    
- Per disattivare definitivamente il dispositivo, è possibile eliminare la voce di registrazione.

    1. Accedere al portale di Azure e fare clic su **tutte le risorse** dal menu a sinistra.
    2. Selezionare il servizio di provisioning che si desidera disattivare il dispositivo nell'elenco di risorse.
    3. Nel servizio di provisioning, fare clic su **Manage enrollments** (Gestisci registrazioni), quindi selezionare la scheda **Individual Enrollments** (Registrazioni singole).
    4. Selezionare la casella accanto alla voce di registrazione per il dispositivo che si desidera disattivare. 
    5. Fare clic su **eliminare** nella parte superiore della finestra, quindi fare clic su **Sì** per confermare che si desidera rimuovere la registrazione. 

        ![Eliminare la voce di registrazione singoli nel portale](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)
    
    6. Una volta completato l'azione, verranno visualizzati la voce rimossa dall'elenco delle registrazioni dei singoli.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-using-an-enrollment-group"></a>Disattivare un intermedio x. 509 o un certificato CA radice utilizzando un gruppo di registrazione

Certificati x. 509 in genere vengono disposte in una catena di certificati attendibili. Se viene compromesso un certificato in qualsiasi fase in una catena, trust viene interrotta e il certificato deve essere disattivato per evitare che i dispositivi a valle in catena che contiene tale certificato per il servizio di Provisioning del dispositivo in corso il provisioning. Per ulteriori informazioni sui certificati x. 509 e come vengono usati con il servizio di provisioning, vedere [certificati x. 509](./concepts-security.md#x509-certificates). 

Un gruppo di registrazione è una voce per i dispositivi che condividono un meccanismo di attestazione comuni dei certificati x. 509 firmato dallo stesso intermedia o CA radice. La voce di registrazione gruppo è configurata con il certificato x. 509 associato alla CA radice o intermedia, nonché dei valori di configurazione, come lo stato di disponibilità di una copia e di connessione dell'hub IoT, che vengono condivisi dai dispositivi con tale certificato nel certificato catena. Per disattivare il certificato, è possibile disabilitare o eliminare il gruppo di registrazione:

- Per disattivare temporaneamente il certificato, è possibile disabilitare il gruppo di registrazione. 

    1. Accedere al portale di Azure e fare clic su **tutte le risorse** dal menu a sinistra.
    2. Selezionare il servizio di provisioning che si desidera disattivare il certificato di firma nell'elenco di risorse.
    3. Nel servizio di provisioning, fare clic su **Manage enrollments** (Gestisci registrazioni), quindi selezionare la scheda **Enrollment Groups** (Gruppi di registrazione).
    4. Fare clic sul gruppo di registrazione per il certificato che si desidera disattivare per aprirlo.
    5. Fare clic su **Modifica gruppo** in alto a sinistra della voce del gruppo di registrazione.
    6. Per disabilitare la voce di registrazione, selezionare **disabilitare** sul **abilitare voce** passare, quindi fare clic su **salvare**.  

        ![Disabilitare la voce di registrazione gruppo nel portale di](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
- Per disattivare definitivamente il certificato, è possibile eliminare il gruppo di registrazione.

    1. Accedere al portale di Azure e fare clic su **tutte le risorse** dal menu a sinistra.
    2. Selezionare il servizio di provisioning che si desidera disattivare il dispositivo nell'elenco di risorse.
    3. Nel servizio di provisioning, fare clic su **Manage enrollments** (Gestisci registrazioni), quindi selezionare la scheda **Enrollment Groups** (Gruppi di registrazione).
    4. Selezionare la casella accanto al gruppo di registrazione per il certificato che si desidera disattivare. 
    5. Fare clic su **eliminare** nella parte superiore della finestra, quindi fare clic su **Sì** per confermare che si desidera rimuovere il gruppo di registrazione. 

        ![Eliminare una voce di registrazione gruppo nel portale di](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

    6. Una volta completato l'azione, verranno visualizzati la voce rimossa dall'elenco dei gruppi di registrazione.  

> [!NOTE]
> Se si elimina un gruppo di registrazione di un certificato, i dispositivi che dispongono di tale certificato nella catena di certificati relativi potrebbero ancora essere in grado di registrare se un gruppo di registrazione abilitata per il certificato radice o un altro certificato intermedio più in alto nel certificato esiste una catena.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Blacklist dispositivi specifici in un gruppo di registrazione

Per autenticare i dispositivi che implementano il meccanismo di attestazione x. 509 utilizzano catena di certificati del dispositivo e la chiave privata. Quando un dispositivo si connette e si esegue l'autenticazione con il servizio di Provisioning di dispositivi, il servizio cerca innanzitutto una registrazione singoli corrispondenti credenziali del dispositivo prima di eseguire ricerche di gruppi di registrazione per determinare se il dispositivo può essere eseguito il provisioning. Se il servizio rileva una registrazione di singoli disabilitata per il dispositivo, impedisce il dispositivo di connettersi, anche se esiste un gruppo di registrazione abilitata per intermedio o radice della CA nella catena di certificati del dispositivo. Per disattivare un singolo dispositivo in un gruppo di registrazione, seguire questi passaggi:

1. Accedere al portale di Azure e fare clic su **All resources**  (Tutte le risorse) dal menu a sinistra.
2. Dall'elenco delle risorse, selezionare il servizio di provisioning che contiene il gruppo di registrazione per il dispositivo che si desidera disattivare.
3. Nel servizio di provisioning, fare clic su **Manage enrollments** (Gestisci registrazioni), quindi selezionare la scheda **Individual Enrollments** (Registrazioni singole).
4. Fare clic sul pulsante **Aggiungi** in alto. 
5. Selezionare **x. 509** come meccanismo di sicurezza per il dispositivo e caricare il certificato del dispositivo. Questo è il certificato di entità finale con segno è installato nel dispositivo, che verrà utilizzato per generare i certificati di autenticazione.
6. Immettere il **ID dispositivo IoT Hub** per il dispositivo. 
7. Per disabilitare la voce di registrazione, selezionare **disabilitare** sul **abilitare voce** passare. 
8. Fare clic su **Save**. In seguito alla creazione della registrazione, dovrebbero venire visualizzati il dispositivo con il **le registrazioni dei singoli** scheda. 

    ![Disabilitare la voce di registrazione singoli nel portale di](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)




