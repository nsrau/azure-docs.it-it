---
title: Come usare MXChip IoT DevKit per la connessione al servizio Device Provisioning in hub IoT di Azure | Microsoft Docs
description: Come usare MXChip IoT DevKit per la connessione al servizio Device Provisioning in hub IoT di Azure
services: iot-dps
keywords: 
author: liydu
ms.author: liydu
ms.date: 02/20/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 77c8a6a5e384d27dca2582cc0fedc2bd23c0592e
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-hub-device-provisioning-service"></a>Connettere MXChip IoT DevKit al servizio Device Provisioning in hub IoT di Azure

Questo articolo descrive come configurare DevKit in modo che esegua automaticamente la registrazione automatica per l'hub IoT mediante il servizio Device Provisioning. In questa esercitazione si apprenderà come:

* Configurare l'endpoint globale del servizio Device Provisioning nel dispositivo
* Usare un segreto univoco del dispositivo (Unique Device Secret, UDS) per generare il certificato X.509
* Registrare un singolo dispositivo
* Verificare che il dispositivo sia registrato

[MXChip IoT DevKit](https://aka.ms/iot-devkit) è una scheda all-in-one compatibile con Arduino con sensori e periferiche avanzati. È possibile sviluppare per questa scheda tramite l'[estensione di Visual Studio Code per Arduino](https://aka.ms/arduino). Per la scheda esiste un [catalogo di progetti](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) in crescita che rappresenta una guida per sviluppare e creare prototipi di soluzioni di Internet delle cose (IoT) che usano i servizi di Microsoft Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare la procedura di questa esercitazione, sono necessari gli elementi seguenti:

* Preparare il DevKit seguendo l'[Introduzione](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Eseguire l'aggiornamento al firmware più recente (>= 1.3.0) con l'esercitazione sull'[aggiornamento del firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/).
* Creare e collegare l'hub IoT con l'istanza del servizio Device Provisioning seguendo le istruzioni in [Configurare il provisioning automatico](https://docs.microsoft.com/en-us/azure/iot-dps/quick-setup-auto-provision).

## <a name="set-up-the-device-provisioning-service-configuration-on-the-device"></a>Impostare la configurazione del servizio Device Provisioning nel dispositivo

Per abilitare il DevKit per la connessione all'istanza del servizio Device Provisioning creato:

1. Nel portale di Azure selezionare la **Panoramica** del servizio Device Provisioning e prendere nota dei valori **Endpoint dispositivo globale** e **Ambito ID**.
  ![Endpoint dispositivo globale e Ambito ID del servizio Device Provisioning](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. Verificare che `git` sia installato nel computer e venga aggiunto alle variabili di ambiente accessibili alla finestra di comando. Vedere gli [strumenti client Git di Software Freedom Conservancy](https://git-scm.com/download/) per installare la versione più recente.

3. Aprire un prompt dei comandi. Clonare il repository GitHub per il codice di esempio del servizio Device Provisioning:
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. Avviare Visual Studio Code e connettere il DevKit al computer, quindi aprire la cartella che contiene il codice clonato.

5. Aprire **DevKitDPS.ino**, quindi trovare e sostituire `[Global Device Endpoint]` e `[ID Scope]` con i valori di cui si è appena preso nota.
  ![Endpoint del servizio Device Provisioning](./media/how-to-connect-mxchip-iot-devkit/endpoint.png) È possibile lasciare vuoto il valore **registrationId**; l'applicazione ne genera uno in base alla versione del firmware e all'indirizzo MAC. Se si desidera personalizzarlo, l'ID di registrazione deve usare una combinazione di caratteri alfanumerici, caratteri minuscoli e trattini non più lunga di 128 caratteri. Per altre informazioni, vedere [Gestire le registrazioni dei dispositivi con il portale di Azure](https://docs.microsoft.com/en-us/azure/iot-dps/how-to-manage-enrollments).

6. Usare **Quick Open** in Visual Studio Code (Windows: `Ctrl+P`, macOS: `Cmd+P`) e digitare **task device-upload** per compilare e caricare il codice nel DevKit.

7. Verificare la corretta esecuzione dell'attività nella finestra di output.

## <a name="save-unique-device-secret-on-stsafe-security-chip"></a>Salvare il segreto UDS nel chip di sicurezza STSAFE

È possibile configurare il servizio di Device Provisioning nel dispositivo in base al relativo [modulo di protezione hardware](https://azure.microsoft.com/en-us/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/). Il DevKit usa lo standard [Device Identity Composition Engine (DICE)](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) del [Trusted Computing Group (TCG)](https://trustedcomputinggroup.org). Un **segreto UDS** salvato nel chip di sicurezza STSAFE del DevKit viene usato per generare il certificato [X.509](https://docs.microsoft.com/en-us/azure/iot-dps/tutorial-set-up-device#select-a-hardware-security-module) univoco del dispositivo. Il certificato può in seguito essere usato per il processo di registrazione nel servizio Device Provisioning.

Un **segreto UDS** tipico è una stringa di 64 caratteri. Di seguito ne è riportato un esempio:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Ognuno dei due caratteri viene usato come valore esadecimale per il calcolo della sicurezza. Di conseguenza, il segreto UDS di esempio precedente viene risolto in: "`0x19`, `0xe2`, `0x5a`, `0x25`, `0x9d`, `0x0c`, `0x2b`, `0xe0`, `0x3a`, `0x02`, `0xd4`, `0x16`, `0xc0`, `0x5c`, `0x48`, `0xcc`, `0xd0`, `0xcc`, `0x7d`, `0x17`, `0x43`, `0x45`, `0x8a`, `0xae`, `0x1c`, `0xb4`, `0x88`, `0xb0`, `0x74`, `0x99`, `0x3e`, `0xae`".

Per salvare il segreto UDS nel DevKit:

1. Aprire una finestra di monitoraggio seriale usando uno strumento come Putty. Per informazioni dettagliate, vedere [Use Configuration Mode](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) (Usare la modalità di configurazione).

2. Con il DevKit connesso al computer, tenere premuto il pulsante A e quindi premere e rilasciare il pulsante di reimpostazione per entrare in modalità di configurazione. La schermata dovrebbe mostrare l'ID DevKit e la voce **'Configuration'** (Configurazione).

3. Prendere la lunga stringa di esempio di segreto UDS precedente e modificare uno o più caratteri in altri valori compresi tra `0` e `f`. Il risultato verrà usato come segreto UDS personale.

4. Nella finestra del monitoraggio seriale digitare **set_dps_uds [your_own_uds_value]** e premere INVIO per salvare.
  > [!NOTE]
  > Ad esempio, se si imposta il proprio segreto UDS modificando gli ultimi due caratteri in `f`, è necessario immettere il comando come **set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff**.

5. Senza chiudere la finestra di monitoraggio seriale, premere il pulsante di reimpostazione sul DevKit.

6. Prendere nota dei valori **DevKit MAC Address** (Indirizzo MAC DevKit) e **DevKit Firmware Version** (Versione del firmware DevKit).
  ![Versione del firmware](./media/how-to-connect-mxchip-iot-devkit/firmware-version.png)

## <a name="generate-x509-certificate"></a>Generare il certificato X.509

### <a name="windows"></a>Windows

1. Aprire Esplora file e passare alla cartella contenente il codice di esempio DSP clonato. Trovare e copiare i file **DPS.ino.bin** e **DPS.ino.map** presenti nella cartella **.build**.
  ![File generati](./media/how-to-connect-mxchip-iot-devkit/generated-files.png)
  > [!NOTE]
  > Se è stata modificata la configurazione `built.path` per Arduino impostando un'altra cartella, è necessario cercare questi file nella cartella configurata.

2. Incollare questi due file nella cartella **tools** allo stesso livello della cartella **.build**.

3. Eseguire **dps_cert_gen.exe**, seguire le istruzioni per immettere **segreto UDS**, **indirizzo MAC** del DevKit e la **versione del firmware** per generare il certificato X.509.
  ![Eseguire dps-cert-gen.exe](./media/how-to-connect-mxchip-iot-devkit/dps-cert-gen.png)

4. Osservare l'esito positivo della generazione, un certificato **.pem** viene salvato nella stessa cartella.

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Creare una voce di registrazione del dispositivo nel servizio Device Provisioning

1. Passare al servizio di provisioning nel portale di Azure. Fare clic su **Gestisci registrazioni** e selezionare la scheda **Registrazioni singole**. ![Registrazioni singole](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. Fare clic su **Aggiungi**.

3. In **Meccanismo** scegliere **X.509**.
  ![Caricare il certificato](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

4. In **File di certificato con estensione pem o cer** caricare il certificato **.pem** appena ottenuto.

5. Lasciare le altre impostazioni sui valori predefiniti e fare clic su **Salva**.

## <a name="start-the-devkit"></a>Avviare il DevKit

1. Avviare Visual Studio Code e aprire il monitor seriale.

2. Premere il pulsante di **reimpostazione** sul DevKit.

Il DevKit dovrebbe iniziare la procedura di registrazione con il servizio Device Provisioning.

![Output di Visual Studio Code](./media/how-to-connect-mxchip-iot-devkit/vscode-output.png)

## <a name="verify-the-devkit-is-registered-on-iot-hub"></a>Verificare che il DevKit sia registrato nell'hub IoT

In seguito all'avvio del dispositivo dovrebbero verificarsi le azioni seguenti:

1. Il dispositivo invia una richiesta di registrazione al servizio Device Provisioning.
2. Il servizio Device Provisioning invia di nuovo una richiesta di registrazione a cui risponde il dispositivo.
3. Al termine della registrazione il servizio Device Provisioning invia l'URI dell'hub IoT, l'ID dispositivo e la chiave crittografata di nuovo al dispositivo.
4. L'applicazione client dell'hub IoT nel dispositivo si connette quindi all'hub.
5. Successivamente alla connessione all'hub il dispositivo dovrebbe essere visualizzato in Device Explorer dell'hub IoT.
  ![Dispositivo registrato](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="change-device-id"></a>Modificare l'ID dispositivo

L'ID dispositivo predefinito registrato nell'hub IoT di Azure è **AZ3166**. Per modificarlo, seguire le [istruzioni qui](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problemi e commenti

In caso di problemi, fare riferimento alle [domande frequenti](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) o contattare Microsoft tramite i canali seguenti:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passaggi successivi

A questo punto si è appreso come preparare il DevKit per registrare un dispositivo in modo sicuro nel servizio Device Provisioning, perché possa eseguire automaticamente la registrazione nell'hub IoT. Questa esercitazione illustra come:

> [!div class="checklist"]
> * Configurare l'endpoint globale del servizio Device Provisioning nel dispositivo
> * Usare un segreto univoco del dispositivo (Unique Device Secret, UDS) per generare il certificato X.509
> * Registrare un singolo dispositivo
> * Verificare che il dispositivo sia registrato

Procedere alle esercitazioni successive per apprendere come:

> [!div class="nextstepaction"]
> [Creare ed effettuare il provisioning di un dispositivo simulato](./quick-create-simulated-device.md)

