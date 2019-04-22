---
title: Come usare il provisioning automatico del servizio Device Provisioning in hub IoT per registrare MXChip IoT DevKit nell'hub IoT | Microsoft Docs
description: Come usare il provisioning automatico del servizio Device Provisioning in hub IoT per registrare MXChip IoT DevKit nell'hub IoT.
author: liydu
ms.author: liydu
ms.date: 12/18/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: 80e4895e0b276e701a6d7f10d8fc67649db0f188
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58904492"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Usare il provisioning automatico del servizio Device Provisioning in hub IoT per registrare MXChip IoT DevKit nell'hub IoT

Questo articolo descrive come usare il [provisioning automatico](concepts-auto-provisioning.md) del servizio Device Provisioning in hub IoT per registrare MXChip IoT DevKit nell'hub IoT di Azure. In questa esercitazione si apprenderà come:

* Configurare l'endpoint globale del servizio Device Provisioning in un dispositivo.
* Usare un segreto univoco del dispositivo (Unique Device Secret, UDS) per generare un certificato X.509.
* Registrare un singolo dispositivo.
* Verificare che il dispositivo sia registrato.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) è una scheda all-in-one compatibile con Arduino con sensori e periferiche avanzati. È possibile sviluppare per tale scheda con [Azure IoT Device Workbench](https://aka.ms/iot-workbench) oppure il pacchetto di estensione [Azure IoT Tools](https://aka.ms/azure-iot-tools) in Visual Studio Code. Il DevKit è dotato di un [catalogo di progetti](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) in crescita che rappresenta una guida per sviluppare e creare prototipi di soluzioni di Internet delle cose (IoT) che usano i servizi di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare la procedura di questa esercitazione, effettuare le attività seguenti:

* Configurare il DevKit Wi-Fi e preparare l'ambiente di sviluppo seguendo i passaggi descritti in [connettere DevKit di IoT AZ3166 all'IoT Hub di Azure nel cloud](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Eseguire l'aggiornamento al firmware più recente (1.3.0 o versioni successive) seguendo l'esercitazione [Update DevKit firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) (Aggiornare il firmware del DevKit).
* Creare e collegare un hub IoT con un'istanza del servizio Device Provisioning seguendo i passaggi descritti in [Configurare il servizio Device Provisioning in hub IoT con il portale di Azure](/azure/iot-dps/quick-setup-auto-provision).

## <a name="open-sample-project"></a>Aprire il progetto di esempio

1. Assicurarsi che il DevKit IoT **non** sia connesso al computer. Avviare per prima cosa Visual Studio Code, quindi connettere il DevKit al computer.

1. Premere `F1` per aprire il riquadro comandi, digitare e selezionare **Azure IoT Device Workbench: Open Examples** (Azure IoT Device Workbench: Apri esempi). Quindi selezionare **IoT DevKit** (DevKit di IoT) come lavagna.

1. Nella pagina degli esempi di IoT Workbench, individuare **Device Registration with DPS** (Registrazione del dispositivo con DPS) e fare clic su **Open Sample** (Apri esempio). Selezionare quindi il percorso predefinito per scaricare il codice di esempio.
    ![Aprire esempio](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>Salvare un segreto UDS (Unique Device Secret) nell'archivio di sicurezza del dispositivo

È possibile configurare il provisioning automatico in un dispositivo in base al [meccanismo di attestazione](concepts-security.md#attestation-mechanism) del dispositivo stesso. MXChip IoT DevKit usa lo standard [Device Identity Composition Engine (DICE)](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) del [Trusted Computing Group (TCG)](https://trustedcomputinggroup.org). Un **segreto UDS** salvato in un chip di sicurezza STSAFE ([STSAFE-A100](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)) del DevKit viene usato per generare il certificato [X.509](concepts-security.md#x509-certificates) univoco del dispositivo. Il certificato viene usato in seguito per il processo di registrazione nel servizio Device Provisioning e durante la registrazione in fase di esecuzione.

Un segreto UDS tipico è una stringa di 64 caratteri, come illustrato nell'esempio seguente:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Per salvare un segreto UDS nel DevKit:

1. In VS Code fare clic sulla barra di stato per selezionare la porta COM per il DevKit.
  ![Selezionare la porta COM](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. Su DevKit, tenere premuto il **pulsante A**, premere e rilasciare il pulsante di **reimpostazione** e quindi rilasciare il **pulsante A**. Il DevKit passa alla modalità di configurazione.

1. Premere `F1` per aprire il riquadro comandi, digitare e selezionare **Azure IoT Device Workbench: Configure Device Settings... > Config Unique Device String (UDS)** (Configura impostazioni dispositivo > Configura stringa UDS).
  ![Configurare la stringa UDS](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. Prendere nota della stringa UDS generata. Sarà necessaria per generare il certificato X.509. Premere quindi `Enter`.
  ![Copiare la stringa UDS](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. Verificare nella notifica che la stringa UDS sia stata configurata correttamente nel chip STSAFE.
  ![Esito positivo della configurazione della stringa UDS](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> In alternativa, è possibile configurare la stringa UDS tramite la porta seriale usando utilità come Putty. Vedere [Use configuration mode](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) (Usare la modalità di configurazione) per questa operazione.

## <a name="update-the-global-device-endpoint-and-id-scope"></a>Aggiornare Endpoint dispositivo globale e Ambito ID

Nel codice del dispositivo è necessario specificare l'[endpoint di provisioning del dispositivo](/azure/iot-dps/concepts-service#device-provisioning-endpoint) e l'ambito ID per garantire l'isolamento del tenant.

1. Nel portale di Azure selezionare il riquadro **Panoramica** del servizio Device Provisioning e prendere nota dei valori **Endpoint dispositivo globale** e **Ambito ID**.
  ![Ambito ID ed endpoint globale del servizio Device Provisioning](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. Aprire **DeKitDPS.ino**. Trovare e sostituire `[Global Device Endpoint]` e `[ID Scope]` con i valori annotati.
  ![Endpoint del servizio Device Provisioning](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. Compilare la variabile `registrationId` nel codice. Sono consentiti solo caratteri alfanumerici, caratteri minuscoli e trattini con un massimo di 128 caratteri. Prendere nota anche del valore.
  ![ID registrazione](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. Premere `F1`, digitare e selezionare **Azure IoT Device Workbench: Upload Device Code** (Azure IoT Device Workbench: Carica il codice del dispositivo). Vengono avviati la compilazione e il caricamento nel codice in DevKit.
  ![Caricamento del dispositivo](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>Generare il certificato X.509

Il [meccanismo di attestazione](/azure/iot-dps/concepts-device#attestation-mechanism) usato da questo esempio è il certificato X.509. È necessario usare un'utilità per generarlo.

> [!NOTE]
> Attualmente il generatore di certificati X.509 supporta solo Windows.

1. In VS Code fare clic su `F1`, digitare e selezionare **Apri nuovo terminale** per aprire una finestra del terminale.

1. Eseguire `dps_cert_gen.exe` nella cartella `tool`.

1. Specificare il percorso del file binario compilato come `..\.build\DevKitDPS`. Incollare quindi la **stringa UDS** e il valore **registrationId** annotati. 
  ![Generare il certifico X.509](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. Nella stessa cartella viene generato un file di certificato X.509 con estensione `.pem`.
  ![File X.509](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>Creare una voce per la registrazione dei dispositivi

1. Nel portale di Azure aprire il servizio Device Provisioning, passare alla sezione Gestisci registrazioni e fare clic su **Aggiungi registrazione singola**.
  ![Aggiungi registrazione singola](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. Fare clic sull'icona del file accanto a **File di certificato primario con estensione pem o cer** per caricare il file `.pem` generato.
  ![Caricare il file con estensione pem](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>Verificare che il DevKit sia registrato nell'hub IoT di Azure

Premere il pulsante di **reimpostazione** sul DevKit. Dovrebbe essere visualizzata l'indicazione **DPS Connected!** (DPS connesso) nella schermata del DevKit. Dopo il riavvio del dispositivo, si verificano le azioni seguenti:

1. Il dispositivo invia una richiesta di registrazione al servizio Device Provisioning.
1. Il servizio Device Provisioning invia di nuovo una richiesta di registrazione a cui risponde il dispositivo.
1. Al termine della registrazione il servizio Device Provisioning invia l'URI dell'hub IoT, l'ID dispositivo e la chiave crittografata di nuovo al dispositivo.
1. L'applicazione client dell'hub IoT nel dispositivo si connette all'hub.
1. Successivamente alla connessione all'hub il dispositivo viene visualizzato in Device Explorer dell'hub IoT.
  ![Dispositivo registrato](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>Problemi e commenti

In caso di problemi, fare riferimento alle numerose [domande frequenti](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) sul DevKit o contattare i canali seguenti per ricevere assistenza:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come registrare un dispositivo in modo sicuro al servizio Device Provisioning usando Device Identity Composition Engine, in modo che il dispositivo possa registrarsi automaticamente all'hub IoT di Azure. 

In breve, si è appreso come:

> [!div class="checklist"]
> * Configurare l'endpoint globale del servizio Device Provisioning in un dispositivo.
> * Usare un segreto UDS per generare un certificato X.509.
> * Registrare un singolo dispositivo.
> * Verificare che il dispositivo sia registrato.

Altre informazioni in [Creare ed effettuare il provisioning di un dispositivo simulato](./quick-create-simulated-device.md).

