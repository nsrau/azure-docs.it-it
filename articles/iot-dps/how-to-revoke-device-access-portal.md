---
title: Come annullare la registrazione di un dispositivo nel servizio Device Provisioning in hub IoT
description: Come annullare la registrazione di un dispositivo per impedire il provisioning tramite il servizio Device Provisioning in hub IoT
author: bryanla
ms.author: bryanla
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 4b6c948765575c92c962999fe394ffaf1a0d22f3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628845"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Come annullare la registrazione di un dispositivo nel servizio Device Provisioning in hub IoT

L'adeguata gestione delle credenziali dei dispositivi è un fattore fondamentale per i sistemi di alto profilo quali, ad esempio, le soluzioni IoT. Una procedura consigliata per questi sistemi prevede l'uso di un piano strutturato e definito contenente le procedure di revoca dell'accesso per i dispositivi qualora le relative credenziali, siano esse token di firma di accesso condiviso o certificati X.509, risultino compromesse. 

La registrazione di un dispositivo nel servizio Device Provisioning ne consente il [provisioning automatico](concepts-auto-provisioning.md). Eseguire il provisioning di un dispositivo vuol dire registrarlo nell'hub IoT, in modo che possa ricevere lo stato iniziale [del dispositivo gemello](~/articles/iot-hub/iot-hub-devguide-device-twins.md) e iniziare a segnalare i dati di telemetria. Questo articolo descrive come annullare la registrazione di un dispositivo nell'istanza del servizio di provisioning ed evitare che ne venga eseguito nuovamente il provisioning in futuro.

> [!NOTE] 
> Tenere presente i criteri di ripetizione dei dispositivi per i quali si sta per revocare l'accesso. Ad esempio, è possibile che un dispositivo con criteri di ripetizione infiniti cerchi di eseguire in modo continuativo la registrazione del servizio di provisioning, con il conseguente esaurimento delle risorse del servizio e con un potenziale impatto negativo sulle prestazioni.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>Inserire i dispositivi usando una voce di registrazione singola nell'elenco di elementi non consentiti

Le registrazioni singole sono valide per un singolo dispositivo e possono usare certificati X.509 o token di firma di accesso condiviso (in un TPM reale o virtuale) come meccanismo di attestazione. I dispositivi che usano i token di firma di accesso condiviso come meccanismo di attestazione possono eseguire il provisioning solo tramite una registrazione singola. Per inserire un dispositivo con una voce di registrazione singola nell'elenco di elementi non consentiti, è possibile disabilitare o eliminare la relativa voce di registrazione. 

Per inserire temporaneamente il dispositivo nell'elenco di elementi non consentiti disabilitando la voce di registrazione: 

1. Accedere al portale di Azure e selezionare **Tutte le risorse** nel menu a sinistra.
2. Selezionare nell'elenco di risorse il servizio di provisioning da inserire nell'elenco di elementi non consentiti per il dispositivo in uso.
3. Nel servizio di provisioning, fare clic su **Gestisci registrazioni** e quindi selezionare la scheda **Registrazioni singole**.
4. Selezionare la voce di registrazione per il dispositivo che si vuole inserire nell'elenco di elementi non consentiti. 
5. Scorrere verso il basso e selezionare **Disabilita** per l'opzione **Abilita voce** e quindi scegliere **Salva**.  

   [![Disabilitare la voce di registrazione singola nel portale](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png#lightbox)  

Per inserire in modo permanente il dispositivo nell'elenco di elementi non consentiti eliminando la relativa voce di registrazione:

1. Accedere al portale di Azure e selezionare **Tutte le risorse** nel menu a sinistra.
2. Selezionare nell'elenco di risorse il servizio di provisioning da inserire nell'elenco di elementi non consentiti per il dispositivo in uso.
3. Nel servizio di provisioning, fare clic su **Gestisci registrazioni** e quindi selezionare la scheda **Registrazioni singole**.
4. Selezionare la casella di controllo accanto alla voce di registrazione per il dispositivo che si vuole inserire nell'elenco di elementi non consentiti. 
5. Selezionare **Elimina** nella parte superiore della finestra e quindi fare clic su **Sì** per confermare che si vuole rimuovere la registrazione. 

   ![Eliminare la voce di registrazione singola nel portale](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)
    
Dopo aver completato la procedura, la voce apparirà rimossa dall'elenco delle registrazioni singole.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Inserire un certificato intermedio X.509 o un certificato CA radice nell'elenco di elementi non consentiti mediante un gruppo di registrazioni

I certificati X.509 vengono in genere organizzati in una catena di certificati attendibili. Se un certificato in un punto qualsiasi di una catena risulta compromesso, la relativa attendibilità risulta danneggiata. Il certificato deve essere quindi inserito nell'elenco di elementi non consentiti per impedire al servizio Device Provisioning di effettuare il provisioning dei dispositivi che si trovano a valle nella catena e che contengono tale certificato. Per altre informazioni sui certificati X.509 e sulle modalità di uso con il servizio di provisioning, vedere [Certificati X.509](./concepts-security.md#x509-certificates). 

Un gruppo di registrazioni è una voce relativa a dispositivi che condividono un meccanismo di attestazione comune basato su certificati X.509, firmati dalla stessa CA radice o intermedia. La voce relativa al gruppo di registrazioni è configurata con il certificato X.509 associato alla CA radice o intermedia, nonché con qualsiasi valore di configurazione, ad esempio lo stato del dispositivo gemello e la connessione all'hub IoT, condiviso dai dispositivi con tale certificato nella catena di certificati. Per inserire il certificato nell'elenco di elementi non consentiti, è possibile disabilitare o eliminare il relativo gruppo di registrazioni.

Per inserire temporaneamente il certificato nell'elenco di elementi non consentiti disabilitando il relativo gruppo di registrazioni: 

1. Accedere al portale di Azure e selezionare **Tutte le risorse** nel menu a sinistra.
2. Selezionare nell'elenco di risorse il servizio di provisioning da inserire nell'elenco di elementi non consentiti per il certificato di firma in uso.
3. Nel servizio di provisioning, fare clic su **Gestisci registrazioni** e quindi selezionare la scheda **Gruppi di registrazioni**.
4. Selezionare il gruppo di registrazioni che usa il certificato da inserire nell'elenco di elementi non consentiti.
5. Selezionare **Disabilita** per l'opzione **Abilita voce** e quindi scegliere **Salva**.  

   ![Disabilitare la voce del gruppo di registrazioni nel portale](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Per inserire in modo permanente il certificato nell'elenco di elementi non consentiti eliminando il relativo gruppo di registrazioni:

1. Accedere al portale di Azure e selezionare **Tutte le risorse** nel menu a sinistra.
2. Selezionare nell'elenco di risorse il servizio di provisioning da inserire nell'elenco di elementi non consentiti per il dispositivo in uso.
3. Nel servizio di provisioning, fare clic su **Gestisci registrazioni** e quindi selezionare la scheda **Gruppi di registrazioni**.
4. Selezionare la casella di controllo accanto al gruppo di registrazioni per il certificato che si vuole inserire nell'elenco di elementi non consentiti. 
5. Selezionare **Elimina** nella parte superiore della finestra e quindi fare clic su **Sì** per confermare che si vuole rimuovere il gruppo di registrazioni. 

   ![Eliminare una voce del gruppo di registrazioni gruppo nel portale](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Dopo aver completato la procedura, la voce apparirà rimossa dall'elenco dell'elenco dei gruppi di registrazioni.  

> [!NOTE]
> Se si elimina un gruppo di registrazioni per un certificato, i dispositivi contenenti tale certificato nella relativa catena di certificati potrebbero ancora essere in grado di eseguire la registrazione se a un livello superiore di tale catena esiste un gruppo di registrazioni abilitato per il certificato radice o un altro certificato intermedio.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Inserire dispositivi specifici in un gruppo di registrazione nell'elenco di elementi non consentiti

I dispositivi che implementano il meccanismo di attestazione basato su certificati X.509 usano la catena di certificati e la chiave privata del dispositivo per eseguire l'autenticazione. Quando un dispositivo si connette ed esegue l'autenticazione con il servizio Device Provisioning, il servizio cerca prima una registrazione specifica corrispondente alle credenziali del dispositivo e quindi cerca i gruppi di registrazioni per determinare se è possibile eseguire il provisioning del dispositivo stesso. Se per il dispositivo viene rilevata una registrazione singola disabilitata, il servizio impedisce al dispositivo di connettersi anche se esiste un gruppo di registrazioni abilitato per una CA radice o intermedia nella relativa catena di certificati. 

Per inserire un singolo dispositivo in un gruppo di registrazioni nell'elenco di elementi non consentiti, seguire questa procedura:

1. Accedere al portale di Azure e selezionare **Tutte le risorse** nel menu a sinistra.
2. Nell'elenco delle risorse selezionare il servizio di provisioning che contiene il gruppo di registrazioni per il dispositivo che si vuole inserire nell'elenco di elementi non consentiti.
3. Nel servizio di provisioning, fare clic su **Gestisci registrazioni** e quindi selezionare la scheda **Registrazioni singole**.
4. Selezionare il pulsante **Aggiungi** in alto. 
5. Selezionare **X.509** come meccanismo di attestazione per il dispositivo e caricare il certificato del dispositivo. Questo sarà il certificato dell'entità finale firmato installato nel dispositivo, che verrà usato per generare i certificati per l'autenticazione.
6. Nel campo **ID dispositivo hub IoT** immettere un valore valido per il dispositivo. 
7. Selezionare **Disabilita** per l'opzione **Abilita voce** e quindi scegliere **Salva**. 

    [![Disabilitare la voce di registrazione singola per disabilitare il dispositivo dalla registrazione di gruppo nel portale](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

Dopo avere creato la registrazione, nella la scheda **Registrazioni singole** verrà visualizzato il dispositivo.

## <a name="next-steps"></a>Passaggi successivi

L'annullamento della registrazione è anche parte del più ampio processo di deprovisioning. Il deprovisioning di un dispositivo include sia l'annullamento della registrazione nel servizio di provisioning, sia l'annullamento della registrazione nell'hub IoT. Per informazioni sul processo completo, vedere [Come eseguire il deprovisioning di dispositivi di cui in precedenza è stato eseguito il provisioning automatico](how-to-unprovision-devices.md) 

