---
title: Eseguire il provisioning di un dispositivo con il servizio Device Provisioning in hub IoT di Azure| Microsoft Docs
description: Eseguire il provisioning del dispositivo in un singolo hub IoT con il servizio Device Provisioning in hub IoT di Azure
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 266b9b7eb228744075627e1e80710e63c27880cc
ms.openlocfilehash: bf50699d2dc67294d554ba15713254a8b88d8ade
ms.contentlocale: it-it
ms.lasthandoff: 09/06/2017

---

# <a name="provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>Eseguire il provisioning del dispositivo in un hub IoT con il servizio Device Provisioning in hub IoT di Azure

Nell'esercitazione precedente abbiamo appreso come configurare un dispositivo per la connessione al servizio Device Provisioning. In questa esercitazione si apprenderà come usare questo servizio per eseguire il provisioning del dispositivo a un singolo hub IoT, usando gli **_elenchi di registrazione_**. Questa esercitazione illustra come:

> [!div class="checklist"]
> * Registrare il dispositivo
> * Avviare il dispositivo
> * Verificare che il dispositivo sia registrato

## <a name="prerequisites"></a>Prerequisiti

Prima di procedere, assicurarsi di configurare il dispositivo e il relativo *modulo di protezione hardware* come descritto nell'esercitazione [Configurare un dispositivo per il provisioning usando il servizio Device Provisioning in hub IoT di Azure](./tutorial-set-up-device.md).


<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>Registrare il dispositivo

Questo passaggio prevede l'aggiunta di elementi di sicurezza esclusivi del dispositivo al servizio Device Provisioning. Di seguito sono indicati gli elementi di sicurezza.

- Per i dispositivi basati su TPM:
    - *Chiave di verifica dell'autenticità*, univoca per ogni chip TPM o simulazione. Per altre informazioni leggere [Informazioni sulla chiave di verifica dell'autenticità del TPM](https://technet.microsoft.com/library/cc770443.aspx).
    - *ID di registrazione*, usato per identificare in modo univoco un dispositivo nell'ambito o nello spazio dei nomi. Può coincidere o meno con l'ID dispositivo. L'ID è obbligatorio per ogni dispositivo. Per i dispositivi basati su TPM, l'ID di registrazione può essere derivato dal TPM stesso, ad esempio un hash SHA-256 della chiave di verifica dell'autenticità del TPM.

    ![Informazioni di registrazione per TPM nel portale](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)

- Per i dispositivi basati su X.509:
    - [Certificato emesso nel chip X.509](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx) o nella simulazione, sotto forma di file con estensione *pem* o *cer*. Per la registrazione individuale è necessario usare il *certificato del firmatario* per il sistema X.509, mentre per i gruppi di registrazione è necessario usare il *certificato radice*.

    ![Informazioni di registrazione per X.509 nel portale](./media/tutorial-provision-device-to-hub/x509-device-enrollment.png)


È possibile registrare il dispositivo nel servizio Device Provisioning in due modi:

- **Enrollment Groups** (Gruppi di registrazione) Rappresenta un gruppo di dispositivi che condividono un meccanismo di attestazione specifico. È consigliabile usare un gruppo di registrazione per un numero elevato di dispositivi che condividono una configurazione iniziale desiderata o per i dispositivi destinati allo stesso tenant.

    ![Gruppi di registrazione per X.509 nel portale](./media/tutorial-provision-device-to-hub/x509-enrollment-groups.png)

- **Individual Enrollments** (Registrazioni individuali) Rappresenta una voce per un singolo dispositivo che può eseguire la registrazione al servizio Device Provisioning. Le registrazioni individuali possono usare certificati X.509 o token di firma di accesso condiviso (in un TPM reale o virtuale) come meccanismo di attestazione. È consigliabile usare le registrazioni individuali per i dispositivi che richiedono configurazioni iniziali univoche oppure per i dispositivi che possono usare solo token di firma di accesso condiviso tramite TPM o TPM virtuale come meccanismo di attestazione. In caso di registrazione individuale è possibile specificare l'ID dispositivo hub IoT desiderato.

Di seguito sono indicati i passaggi per registrare il dispositivo nel portale:

1. Prendere nota degli elementi di sicurezza per il modulo di protezione hardware nel dispositivo. Potrebbe essere necessario usare le API riportate nella sezione relativa all'[estrazione di elementi di sicurezza](./tutorial-set-up-device.md#extractsecurity) dell'esercitazione precedente in un ambiente di sviluppo.

1. Accedere al portale di Azure, fare clic sul pulsante **Tutte le risorse** nel menu a sinistra e aprire il servizio Device Provisioning.

1. Nel pannello di riepilogo del servizio Device Provisioning selezionare **Manage enrollments** (Gestisci registrazioni). Selezionare la scheda **Individual Enrollments** (Registrazioni individuali) o **Enrollment Groups** (Gruppi di registrazione) in base alla configurazione del dispositivo. Fare clic sul pulsante **Add** (Aggiungi) in alto. Selezionare **TPM** o **X.509** come *meccanismo* di attestazione dell'identità e immettere gli elementi di sicurezza appropriati come descritto in precedenza. È possibile immettere un nuovo **ID di dispositivo hub IoT**. Al termine, fare clic sul pulsante **Save** (Salva). 

1. Quando la registrazione del dispositivo è completata, verrà visualizzato nel portale come riportato di seguito:

    ![Successful TPM enrollment in the portal (Registrazione TPM nel portale riuscita)](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)


## <a name="start-the-device"></a>Avviare il dispositivo

A questo punto la configurazione seguente è pronta per la registrazione del dispositivo:

1. Il dispositivo o il gruppo di dispositivi sono registrati per il servizio Device Provisioning e 
2. Il dispositivo include il chip del modulo di protezione hardware configurato ed è accessibile tramite l'applicazione usando il client SDK del servizio Device Provisioning.

Avviare il dispositivo per consentire all'applicazione client di eseguire la registrazione al servizio Device Provisioning.  


## <a name="verify-the-device-is-registered"></a>Verificare che il dispositivo sia registrato

In seguito all'avvio del dispositivo dovrebbero verificarsi le azioni seguenti. Vedere l'applicazione di esempio del simulatore TPM [dps_client_sample](https://github.com/Azure/azure-iot-device-auth/blob/master/dps_client/samples/dps_client_sample/dps_client_sample.c) per altri dettagli. 

1. Il dispositivo invia una richiesta di registrazione al servizio Device Provisioning.
2. Per i dispositivi TPM il servizio Device Provisioning invia di nuovo una richiesta di registrazione a cui risponde il dispositivo. 
3. Al termine della registrazione il servizio Device Provisioning invia l'URI dell'hub IoT, l'ID dispositivo e la chiave crittografata di nuovo al dispositivo. 
4. L'applicazione client dell'hub IoT nel dispositivo si connette quindi all'hub. 
5. Successivamente alla connessione all'hub il dispositivo dovrebbero essere visualizzato in **Device Explorer** dell'hub IoT. 

    ![Successful connection to hub in the portal (Connessione all'hub nel portale riuscita)](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Registrare il dispositivo
> * Avviare il dispositivo
> * Verificare che il dispositivo sia registrato

Passare all'esercitazione successiva per informazioni su come eseguire il provisioning di più dispositivi tra hub con bilanciamento del carico. 

> [!div class="nextstepaction"]
> [Eseguire il provisioning dei dispositivi in più hub IoT con bilanciamento del carico](./tutorial-provision-multiple-hubs.md)

