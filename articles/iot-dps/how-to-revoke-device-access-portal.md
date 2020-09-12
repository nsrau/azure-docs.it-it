---
title: Annullare la registrazione del dispositivo dal servizio Device provisioning in hub Azure
description: Come annullare la registrazione di un dispositivo per impedire il provisioning tramite il servizio Device provisioning in hub Azure
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: abc625c27617c27a79fe284393cdf7b281973bc4
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299259"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Come annullare la registrazione di un dispositivo nel servizio Device Provisioning in hub IoT

L'adeguata gestione delle credenziali dei dispositivi è un fattore fondamentale per i sistemi di alto profilo quali, ad esempio, le soluzioni IoT. Una procedura consigliata per questi sistemi prevede l'uso di un piano strutturato e definito contenente le procedure di revoca dell'accesso per i dispositivi qualora le relative credenziali, siano esse token di firma di accesso condiviso o certificati X.509, risultino compromesse. 

La registrazione di un dispositivo nel servizio Device Provisioning ne consente il [provisioning automatico](concepts-auto-provisioning.md). Eseguire il provisioning di un dispositivo vuol dire registrarlo nell'hub IoT, in modo che possa ricevere lo stato iniziale [del dispositivo gemello](~/articles/iot-hub/iot-hub-devguide-device-twins.md) e iniziare a segnalare i dati di telemetria. Questo articolo descrive come annullare la registrazione di un dispositivo nell'istanza del servizio di provisioning ed evitare che ne venga eseguito nuovamente il provisioning in futuro.

> [!NOTE] 
> Tenere presente i criteri di ripetizione dei dispositivi per i quali si sta per revocare l'accesso. Ad esempio, è possibile che un dispositivo con criteri di ripetizione infiniti cerchi di eseguire in modo continuativo la registrazione del servizio di provisioning, con il conseguente esaurimento delle risorse del servizio e con un potenziale impatto negativo sulle prestazioni.

## <a name="disallow-devices-by-using-an-individual-enrollment-entry"></a>Non consentire i dispositivi usando una singola voce di registrazione

Le registrazioni singole sono valide per un singolo dispositivo e possono usare certificati X.509 o token di firma di accesso condiviso (in un TPM reale o virtuale) come meccanismo di attestazione. I dispositivi che usano i token di firma di accesso condiviso come meccanismo di attestazione possono essere sottoposti a provisioning solo tramite una registrazione singola. Per non consentire un dispositivo con una registrazione singola, è possibile disabilitare o eliminare la relativa voce di registrazione. 

Per impedire temporaneamente al dispositivo di disabilitare la voce di registrazione: 

1. Accedere al portale di Azure e selezionare **Tutte le risorse** nel menu a sinistra.
2. Nell'elenco delle risorse selezionare il servizio di provisioning da cui si vuole impedire il dispositivo.
3. Nel servizio di provisioning, fare clic su **Gestisci registrazioni** e quindi selezionare la scheda **Registrazioni singole**.
4. Selezionare la voce di registrazione per il dispositivo che si vuole non consentire. 

    ![Selezionare la propria registrazione singola](./media/how-to-revoke-device-access-portal/select-individual-enrollment.png)

5. Nella pagina della registrazione scorrere verso il basso, selezionare **Disabilita** per l'opzione **Abilita voce** e quindi scegliere **Salva**.  

   ![Disabilitare la voce di registrazione singola nel portale](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

Per impedire in modo permanente il dispositivo eliminando la relativa voce di registrazione:

1. Accedere al portale di Azure e selezionare **Tutte le risorse** nel menu a sinistra.
2. Nell'elenco delle risorse selezionare il servizio di provisioning da cui si vuole impedire il dispositivo.
3. Nel servizio di provisioning, fare clic su **Gestisci registrazioni** e quindi selezionare la scheda **Registrazioni singole**.
4. Selezionare la casella di controllo accanto alla voce di registrazione per il dispositivo che si vuole non consentire. 
5. Selezionare **Elimina** nella parte superiore della finestra e quindi fare clic su **Sì** per confermare che si vuole rimuovere la registrazione. 

   ![Eliminare la voce di registrazione singola nel portale](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)


Dopo aver completato la procedura, la voce apparirà rimossa dall'elenco delle registrazioni singole.  

## <a name="disallow-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Non consentire un certificato X. 509 intermedio o CA radice usando un gruppo di registrazioni

I certificati X.509 vengono in genere organizzati in una catena di certificati attendibili. Se un certificato in un punto qualsiasi di una catena risulta compromesso, la relativa attendibilità risulta danneggiata. Il certificato non deve essere consentito per impedire al servizio Device provisioning di effettuare il provisioning dei dispositivi a valle in una catena che contiene il certificato. Per altre informazioni sui certificati X.509 e sulle modalità di uso con il servizio di provisioning, vedere [Certificati X.509](./concepts-security.md#x509-certificates). 

Un gruppo di registrazioni è una voce relativa a dispositivi che condividono un meccanismo di attestazione comune basato su certificati X.509, firmati dalla stessa CA radice o intermedia. La voce relativa al gruppo di registrazioni è configurata con il certificato X.509 associato alla CA radice o intermedia, nonché con qualsiasi valore di configurazione, ad esempio lo stato del dispositivo gemello e la connessione all'hub IoT, condiviso dai dispositivi con tale certificato nella catena di certificati. Per non consentire il certificato, è possibile disabilitare o eliminare il relativo gruppo di registrazioni.

Per impedire temporaneamente il certificato disabilitando il gruppo di registrazione: 

1. Accedere al portale di Azure e selezionare **Tutte le risorse** nel menu a sinistra.
2. Nell'elenco delle risorse selezionare il servizio di provisioning da cui si vuole impedire il certificato di firma.
3. Nel servizio di provisioning, fare clic su **Gestisci registrazioni** e quindi selezionare la scheda **Gruppi di registrazioni**.
4. Selezionare il gruppo di registrazioni utilizzando il certificato che si desidera non consentire.
5. Selezionare **Disabilita** per l'opzione **Abilita voce** e quindi scegliere **Salva**.  

   ![Disabilitare la voce del gruppo di registrazioni nel portale](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Per impedire definitivamente il certificato eliminando il relativo gruppo di registrazione:

1. Accedere al portale di Azure e selezionare **Tutte le risorse** nel menu a sinistra.
2. Nell'elenco delle risorse selezionare il servizio di provisioning da cui si vuole impedire il dispositivo.
3. Nel servizio di provisioning, fare clic su **Gestisci registrazioni** e quindi selezionare la scheda **Gruppi di registrazioni**.
4. Selezionare la casella di controllo accanto al gruppo di registrazioni per il certificato che si vuole non consentire. 
5. Selezionare **Elimina** nella parte superiore della finestra e quindi fare clic su **Sì** per confermare che si vuole rimuovere il gruppo di registrazioni. 

   ![Eliminare una voce del gruppo di registrazioni gruppo nel portale](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Dopo aver completato la procedura, la voce apparirà rimossa dall'elenco dell'elenco dei gruppi di registrazioni.  

> [!NOTE]
> Se si elimina un gruppo di registrazioni per un certificato, i dispositivi contenenti tale certificato nella relativa catena di certificati potrebbero ancora essere in grado di eseguire la registrazione se a un livello superiore di tale catena esiste un gruppo di registrazioni abilitato per il certificato radice o un altro certificato intermedio.

## <a name="disallow-specific-devices-in-an-enrollment-group"></a>Non consentire dispositivi specifici in un gruppo di registrazione

I dispositivi che implementano il meccanismo di attestazione basato su certificati X.509 usano la catena di certificati e la chiave privata del dispositivo per eseguire l'autenticazione. Quando un dispositivo si connette ed esegue l'autenticazione con il servizio Device Provisioning, il servizio cerca prima una registrazione specifica corrispondente alle credenziali del dispositivo e quindi cerca i gruppi di registrazioni per determinare se è possibile eseguire il provisioning del dispositivo stesso. Se per il dispositivo viene rilevata una registrazione singola disabilitata, il servizio impedisce al dispositivo di connettersi anche se esiste un gruppo di registrazioni abilitato per una CA radice o intermedia nella relativa catena di certificati. 

Per non consentire un singolo dispositivo in un gruppo di registrazione, attenersi alla procedura seguente:

1. Accedere al portale di Azure e selezionare **Tutte le risorse** nel menu a sinistra.
2. Dall'elenco di risorse selezionare il servizio di provisioning che contiene il gruppo di registrazioni per il dispositivo che si vuole non consentire.
3. Nel servizio di provisioning, fare clic su **Gestisci registrazioni** e quindi selezionare la scheda **Registrazioni singole**.
4. Selezionare il pulsante **Aggiungi registrazione singola** nella parte superiore. 
5. Nella pagina **Aggiungi registrazione ** selezionare **X.509** come **meccanismo** di attestazione per il dispositivo.

    Caricare il certificato del dispositivo e immettere l'ID del dispositivo da non consentire. Come certificato, usare il certificato dell'entità finale firmato installato nel dispositivo. Il dispositivo usa tale certificato per l'autenticazione.

    ![Impostare le proprietà del dispositivo non consentito](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group-1.png)

6. Scorrere fino alla fine della pagina **Aggiungi registrazione** e selezionare **Disabilita** per l'opzione **Abilita voce**, quindi selezionare **Salva**. 

    [![Usare la voce di registrazione singola disabilitata per disabilitare il dispositivo dalla registrazione del gruppo nel portale](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

Dopo avere creato la registrazione, nella scheda **Registrazioni singole** viene visualizzata la registrazione del dispositivo disabilitato. 

## <a name="next-steps"></a>Passaggi successivi

L'annullamento della registrazione è anche parte del più ampio processo di deprovisioning. Il deprovisioning di un dispositivo include sia l'annullamento della registrazione nel servizio di provisioning, sia l'annullamento della registrazione nell'hub IoT. Per informazioni sul processo completo, vedere [Come eseguire il deprovisioning di dispositivi di cui in precedenza è stato eseguito il provisioning automatico](how-to-unprovision-devices.md) 
