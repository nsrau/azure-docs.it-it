---
title: Come gestire l'accesso dei dispositivi al servizio Device Provisioning nell'hub IoT di Azure | Microsoft Docs
description: Come revocare l'accesso dei dispositivi al servizio Device Provisioning nel portale di Azure
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
ms.openlocfilehash: 6f1dad648b228163219c8f722eed3897f4ba4d22
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="how-to-revoke-device-access-to-your-provisioning-service-in-the-azure-portal"></a>Come revocare l'accesso dei dispositivi al servizio di provisioning nel portale di Azure

L'adeguata gestione delle credenziali dei dispositivi è un fattore fondamentale per i sistemi di alto profilo quali, ad esempio, le soluzioni IoT. Una procedura consigliata per tali sistemi prevede l'uso di un piano strutturato e definito contenente le procedure di revoca dell'accesso per i dispositivi qualora le relative credenziali, siano esse token di firma di accesso condiviso o certificati X.509, risultino compromesse. In questo articolo viene descritto come revocare l'accesso dei dispositivi durante la fase di provisioning.

Per informazioni su come revocare l'accesso dei dispositivi a un hub IoT dopo l'esecuzione del provisioning del dispositivo, vedere [Disabilitare i dispositivi](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices).

> [!NOTE] 
> Tenere presente i criteri di ripetizione dei dispositivi per i quali si sta per revocare l'accesso. Ad esempio, è possibile che un dispositivo con criteri di ripetizione infiniti cerchi di eseguire in modo continuativo la registrazione del servizio di provisioning, con il conseguente esaurimento delle risorse del servizio e con un potenziale impatto negativo sulle prestazioni.

## <a name="blacklist-devices-with-an-individual-enrollment-entry"></a>Inserire i dispositivi con una voce di registrazione singola nell'elenco di elementi non consentiti

Le registrazioni singole sono valide per un singolo dispositivo e possono usare certificati X.509 o token di firma di accesso condiviso (in un TPM reale o virtuale) come meccanismo di attestazione. I dispositivi che usano i token di firma di accesso condiviso come meccanismo di attestazione possono eseguire il provisioning solo tramite una registrazione singola. Per inserire un dispositivo con una voce di registrazione singola nell'elenco di elementi non consentiti, è possibile disabilitare o eliminare la relativa voce di registrazione: 

- Per inserire temporaneamente il dispositivo nell'elenco di elementi non consentiti, è possibile disabilitare la voce di registrazione. 

    1. Accedere al portale di Azure e fare clic su **Tutte le risorse** nel menu a sinistra.
    2. Selezionare nell'elenco di risorse il servizio di provisioning da inserire nell'elenco di elementi non consentiti per il dispositivo in uso.
    3. Nel servizio di provisioning, fare clic su **Manage enrollments** (Gestisci registrazioni), quindi selezionare la scheda **Individual Enrollments** (Registrazioni singole).
    4. Fare clic sulla voce di registrazione per il dispositivo che si desidera inserire nell'elenco di elementi non consentiti per aprirla. 
    5. Fare clic su **Disabilita** nella parte inferiore della voce dell'elenco di registrazione e quindi fare clic su **Salva**.  

        ![Disabilitare la voce di registrazione singola nel portale](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)
    
- Per inserire in modo permanente il dispositivo nell'elenco di elementi non consentiti, è possibile eliminare la relativa voce di registrazione.

    1. Accedere al portale di Azure e fare clic su **Tutte le risorse** nel menu a sinistra.
    2. Selezionare nell'elenco di risorse il servizio di provisioning da inserire nell'elenco di elementi non consentiti per il dispositivo in uso.
    3. Nel servizio di provisioning, fare clic su **Manage enrollments** (Gestisci registrazioni), quindi selezionare la scheda **Individual Enrollments** (Registrazioni singole).
    4. Selezionare la casella accanto alla voce di registrazione per il dispositivo che si vuole inserire nell'elenco di elementi non consentiti. 
    5. Fare clic su **Elimina** nella parte superiore della finestra e quindi fare clic su **Sì** per confermare che si vuole rimuovere la registrazione. 

        ![Eliminare la voce di registrazione singola nel portale](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)
    
    6. Dopo aver completato l'azione, la voce apparirà rimossa dall'elenco delle registrazioni singole.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-using-an-enrollment-group"></a>Inserire un certificato intermedio X.509 o un certificato CA radice nell'elenco di elementi non consentiti mediante un gruppo di registrazioni

I certificati X.509 vengono in genere organizzati in una catena di certificati attendibili. Se un certificato in un punto qualsiasi di una catena risulta compromesso e pertanto la relativa attendibilità risulta danneggiata, il certificato in questione deve essere inserito nell'elenco di elementi non consentiti per impedire ai dispositivi che si trovano a valle nella catena e che contengono tale certificato di eseguire il provisioning mediante il servizio Device Provisioning. Per altre informazioni sui certificati X.509 e sulle modalità di uso con il servizio di provisioning, vedere [Certificati X.509](./concepts-security.md#x509-certificates). 

Un gruppo di registrazioni è una voce relativa a dispositivi che condividono un meccanismo di attestazione comune basato su certificati X.509, firmati dalla stessa CA radice o intermedia. La voce relativa al gruppo di registrazioni è configurata con il certificato X.509 associato alla CA radice o intermedia, nonché con qualsiasi valore di configurazione, ad esempio lo stato del dispositivo gemello e la connessione all'hub IoT, condiviso dai dispositivi con tale certificato nella catena di certificati. Per inserire il certificato nell'elenco di elementi non consentiti, è possibile disabilitare o eliminare il relativo gruppo di registrazioni:

- Per inserire temporaneamente il certificato nell'elenco di elementi non consentiti, è possibile disabilitare il relativo gruppo di registrazioni. 

    1. Accedere al portale di Azure e fare clic su **Tutte le risorse** nel menu a sinistra.
    2. Selezionare nell'elenco di risorse il servizio di provisioning da inserire nell'elenco di elementi non consentiti per il certificato di firma in uso.
    3. Nel servizio di provisioning, fare clic su **Manage enrollments** (Gestisci registrazioni), quindi selezionare la scheda **Enrollment Groups** (Gruppi di registrazione).
    4. Fare clic sul gruppo di registrazioni per il certificato che si vuole inserire nell'elenco di elementi non consentiti per aprirlo.
    5. Fare clic su **Modifica gruppo** in alto a sinistra della voce del gruppo di registrazioni.
    6. Per disabilitare la voce di registrazione, selezionare **Disabilita** per l'opzione **Abilita voce** e quindi fare clic su **Salva**.  

        ![Disabilitare la voce del gruppo di registrazioni nel portale](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
- Per inserire in modo permanente il certificato nell'elenco di elementi non consentiti, è possibile eliminare il relativo gruppo di registrazioni.

    1. Accedere al portale di Azure e fare clic su **Tutte le risorse** nel menu a sinistra.
    2. Selezionare nell'elenco di risorse il servizio di provisioning da inserire nell'elenco di elementi non consentiti per il dispositivo in uso.
    3. Nel servizio di provisioning, fare clic su **Manage enrollments** (Gestisci registrazioni), quindi selezionare la scheda **Enrollment Groups** (Gruppi di registrazione).
    4. Selezionare la casella accanto al gruppo di registrazioni per il certificato che si vuole inserire nell'elenco di elementi non consentiti. 
    5. Fare clic su **Elimina** nella parte superiore della finestra e quindi fare clic su **Sì** per confermare che si vuole rimuovere il gruppo di registrazioni. 

        ![Eliminare una voce del gruppo di registrazioni gruppo nel portale](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

    6. Dopo aver completato l'azione, la voce apparirà rimossa dall'elenco dei gruppi di registrazioni.  

> [!NOTE]
> Se si elimina un gruppo di registrazioni per un certificato, i dispositivi contenenti tale certificato nella relativa catena di certificati potrebbero ancora essere in grado di eseguire la registrazione se a un livello superiore di tale catena esiste un gruppo di registrazioni abilitato per il certificato radice o un altro certificato intermedio.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Inserire dispositivi specifici in un gruppo di registrazione nell'elenco di elementi non consentiti

I dispositivi che implementano il meccanismo di attestazione basato su certificati X.509 usano la catena di certificati e la chiave privata del dispositivo per eseguire l'autenticazione. Quando un dispositivo si connette ed esegue l'autenticazione con il servizio Device Provisioning, il servizio ricerca una registrazione specifica corrispondente alle credenziali del dispositivo prima di cercare i gruppi di registrazioni per determinare se è possibile eseguire il provisioning del dispositivo stesso. Se per il dispositivo viene rilevata una registrazione singola disabilitata, il servizio impedisce al dispositivo di connettersi anche se esiste un gruppo di registrazioni abilitato per una CA radice o intermedia nella relativa catena di certificati. Per inserire un singolo dispositivo in un gruppo di registrazioni nell'elenco di elementi non consentiti, seguire questa procedura:

1. Accedere al portale di Azure e fare clic su **All resources**  (Tutte le risorse) dal menu a sinistra.
2. Nell'elenco delle risorse fare clic sul servizio di provisioning che contiene il gruppo di registrazioni per il dispositivo che si vuole inserire nell'elenco di elementi non consentiti.
3. Nel servizio di provisioning, fare clic su **Manage enrollments** (Gestisci registrazioni), quindi selezionare la scheda **Individual Enrollments** (Registrazioni singole).
4. Fare clic sul pulsante **Aggiungi** in alto. 
5. Selezionare **X.509** come meccanismo di sicurezza per il dispositivo e caricare il certificato del dispositivo. Questo sarà il certificato dell'entità finale firmato installato nel dispositivo, che verrà usato per generare i certificati per l'autenticazione.
6. Nel campo **ID dispositivo hub IoT** immettere un valore valido per il dispositivo. 
7. Per disabilitare la voce di registrazione, selezionare **Disabilita** per l'opzione **Abilita voce**. 
8. Fare clic su **Save**. Dopo avere creato la registrazione, sotto la scheda **Registrazioni singole** verrà visualizzato il dispositivo. 

    ![Disabilitare la voce di registrazione singola nel portale](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)




