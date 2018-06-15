---
title: Come usare il provisioning automatico del servizio Device Provisioning in hub IoT per registrare MXChip IoT DevKit nell'hub IoT | Microsoft Docs
description: Come usare il provisioning automatico del servizio Device Provisioning in hub IoT per registrare MXChip IoT DevKit nell'hub IoT.
author: liydu
ms.author: liydu
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: 331e589aadf783fc40ab6efbfb554469c27e48d8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629678"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Usare il provisioning automatico del servizio Device Provisioning in hub IoT per registrare MXChip IoT DevKit nell'hub IoT

Questo articolo descrive come usare il [provisioning automatico](concepts-auto-provisioning.md) del servizio Device Provisioning in hub IoT per registrare MXChip IoT DevKit nell'hub IoT di Azure. In questa esercitazione si apprenderà come:

* Configurare l'endpoint globale del servizio Device Provisioning in un dispositivo.
* Usare un segreto univoco del dispositivo (Unique Device Secret, UDS) per generare un certificato X.509.
* Registrare un singolo dispositivo.
* Verificare che il dispositivo sia registrato.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) è una scheda all-in-one compatibile con Arduino con sensori e periferiche avanzati. È possibile sviluppare per questa scheda tramite l'[estensione di Visual Studio Code per Arduino](https://aka.ms/arduino). Il DevKit è dotato di un [catalogo di progetti](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) in crescita che rappresenta una guida per sviluppare e creare prototipi di soluzioni di Internet delle cose (IoT) che usano i servizi di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare la procedura di questa esercitazione, effettuare le attività seguenti:

* Preparare il DevKit seguendo i passaggi descritti in [Connettere DevKit di IoT AZ3166 all'hub IoT di Azure nel cloud](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).
* Eseguire l'aggiornamento al firmware più recente (1.3.0 o versioni successive) seguendo l'esercitazione [Update DevKit firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) (Aggiornare il firmware del DevKit).
* Creare e collegare un hub IoT con un'istanza del servizio Device Provisioning seguendo i passaggi descritti in [Configurare il servizio Device Provisioning in hub IoT con il portale di Azure](/azure/iot-dps/quick-setup-auto-provision).

## <a name="build-and-deploy-auto-provisioning-registration-software-to-the-device"></a>Compilare e distribuire al dispositivo il software di registrazione per il provisioning automatico

Per connettere il DevKit all'istanza del servizio Device Provisioning creato:

1. Nel portale di Azure selezionare il riquadro **Panoramica** del servizio Device Provisioning e prendere nota dei valori **Endpoint dispositivo globale** e **Ambito ID**.
  ![Endpoint dispositivo globale e Ambito ID del servizio Device Provisioning](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. Verificare che `git` sia installato nel computer e venga aggiunto alle variabili di ambiente accessibili alla finestra di comando. Vedere gli [strumenti client Git di Software Freedom Conservancy](https://git-scm.com/download/) per installare la versione più recente.

3. Aprire un prompt dei comandi. Clonare il repository GitHub per il codice di esempio del servizio Device Provisioning:
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. Aprire Visual Studio Code, connettere il DevKit al computer e quindi aprire la cartella che contiene il codice clonato.

5. Aprire **DevKitDPS.ino**. Trovare e sostituire `[Global Device Endpoint]` e `[ID Scope]` con i valori annotati.
  ![Endpoint del servizio Device Provisioning](./media/how-to-connect-mxchip-iot-devkit/endpoint.png) È possibile lasciare vuoto il valore **registrationId**. L'applicazione ne genera uno in base alla versione del firmware e all'indirizzo MAC. Se si vuole personalizzare l'ID di registrazione, è necessario usare una combinazione di caratteri alfanumerici, caratteri minuscoli e trattini non più lunga di 128 caratteri. Per altre informazioni, vedere [Gestire le registrazioni dei dispositivi con il portale di Azure](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments).

6. Usare Quick Open in Visual Studio Code (Windows: `Ctrl+P`, macOS: `Cmd+P`) e digitare *task device-upload* per compilare e caricare il codice nel DevKit.

7. La finestra di output mostra se l'attività ha avuto esito positivo.

## <a name="save-a-unique-device-secret-on-an-stsafe-security-chip"></a>Salvare un segreto UDS nel chip di sicurezza STSAFE

È possibile configurare il provisioning automatico in un dispositivo in base al [meccanismo di attestazione](concepts-security.md#attestation-mechanism) del dispositivo stesso. MXChip IoT DevKit usa lo standard [Device Identity Composition Engine (DICE)](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) del [Trusted Computing Group (TCG)](https://trustedcomputinggroup.org). Un *segreto UDS* salvato in un chip di sicurezza STSAFE del DevKit viene usato per generare il certificato [X.509](concepts-security.md#x509-certificates) univoco del dispositivo. Il certificato viene usato in seguito per il processo di registrazione nel servizio Device Provisioning e durante la registrazione in fase di esecuzione.

Un segreto UDS tipico è una stringa di 64 caratteri, come illustrato nell'esempio seguente:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Ognuno dei due caratteri viene usato come valore esadecimale per il calcolo della sicurezza. Il segreto UDS di esempio precedente viene risolto in: `0x19`, `0xe2`, `0x5a`, `0x25`, `0x9d`, `0x0c`, `0x2b`, `0xe0`, `0x3a`, `0x02`, `0xd4`, `0x16`, `0xc0`, `0x5c`, `0x48`, `0xcc`, `0xd0`, `0xcc`, `0x7d`, `0x17`, `0x43`, `0x45`, `0x8a`, `0xae`, `0x1c`, `0xb4`, `0x88`, `0xb0`, `0x74`, `0x99`, `0x3e`, `0xae`.

Per salvare un segreto UDS nel DevKit:

1. Aprire il monitor seriale usando uno strumento, ad esempio Putty. Per informazioni dettagliate, vedere [Use configuration mode](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) (Usare la modalità di configurazione).

2. Con il DevKit connesso al computer, tenere premuto il pulsante **A** e quindi premere e rilasciare il pulsante **Reset** (Reimposta) per entrare in modalità di configurazione. La schermata illustra l'ID del DevKit e la voce Configuration (Configurazione).

3. Prendere la stringa del segreto UDS di esempio e modificare uno o più caratteri in altri valori compresi tra `0` e `f` per il proprio segreto UDS.

4. Nella finestra del monitor seriale digitare *set_dps_uds [valore_segreto_UDS]* e premere INVIO.
  > [!NOTE]
  > Ad esempio, se si imposta il segreto UDS modificando gli ultimi due caratteri in `f`, è necessario immettere il comando come segue: `set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff`.

5. Senza chiudere la finestra del monitor seriale, premere il pulsante **Reset** (Reimposta) sul DevKit.

6. Prendere nota dei valori **DevKit MAC Address** (Indirizzo MAC DevKit) e **DevKit Firmware Version** (Versione del firmware DevKit).
  ![Versione del firmware](./media/how-to-connect-mxchip-iot-devkit/firmware-version.png)

## <a name="generate-an-x509-certificate"></a>Generare un certificato X.509

### <a name="windows"></a>Windows

1. Aprire Esplora file e passare alla cartella che contiene il codice di esempio del servizio Device Provisioning clonato in precedenza. Nella cartella **.build** trovare e copiare **DPS.ino.bin** e **DPS.ino.map** nella cartella che contiene il codice.
  ![File generati](./media/how-to-connect-mxchip-iot-devkit/generated-files.png)
  > [!NOTE]
  > Se la configurazione di `built.path` per Arduino è stata modificata in un'altra cartella, è necessario trovare tali file nella cartella configurata.

2. Incollare questi due file nella cartella **tools** allo stesso livello della cartella **.build**.

3. Eseguire **dps_cert_gen.exe**. Seguire le istruzioni per immettere il **segreto UDS**, l'**indirizzo MAC** del DevKit e la **versione del firmware** per generare il certificato X.509.
  ![Eseguire dps-cert-gen.exe](./media/how-to-connect-mxchip-iot-devkit/dps-cert-gen.png)

4. Dopo la generazione del certificato X.509, un certificato con estensione **pem** viene salvato nella stessa cartella.

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Creare una voce di registrazione del dispositivo nel servizio Device Provisioning

1. Passare all'istanza del servizio Device Provisioning nel portale di Azure. Selezionare **Gestisci registrazioni** e quindi la scheda **Registrazioni singole**. ![Registrazioni singole](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. Selezionare **Aggiungi**.

3. Nel pannello "Add Enrollment" (Aggiungi registrazione):
   - Selezionare **X.509** sotto **Meccanismo**
   - Fare clic su "Selezionare un file" in **Primary Certificate .pem or .cer file** (File di certificato primario con estensione pem o cer)
   - Nella finestra di dialogo Apri File individuare e caricare il certificato **PEM** appena generato
   - Lasciare le altre impostazioni sui valori predefiniti e fare clic su **Salva**

   ![Caricamento del certificato](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

## <a name="start-the-devkit"></a>Avviare il DevKit

1. Aprire Visual Studio Code e il monitor seriale.

2. Premere il pulsante di **reimpostazione** sul DevKit.

Il DevKit inizia la procedura di registrazione con il servizio Device Provisioning.

![Output di Visual Studio Code](./media/how-to-connect-mxchip-iot-devkit/vscode-output.png)

## <a name="verify-that-the-devkit-is-registered-with-azure-iot-hub"></a>Verificare che il DevKit sia registrato con l'hub IoT di Azure

In seguito all'avvio del dispositivo, si verificano le azioni seguenti:

1. Il dispositivo invia una richiesta di registrazione al servizio Device Provisioning.
2. Il servizio Device Provisioning invia di nuovo una richiesta di registrazione a cui risponde il dispositivo.
3. Al termine della registrazione il servizio Device Provisioning invia l'URI dell'hub IoT, l'ID dispositivo e la chiave crittografata di nuovo al dispositivo.
4. L'applicazione client dell'hub IoT nel dispositivo si connette all'hub.
5. Successivamente alla connessione all'hub il dispositivo viene visualizzato in Device Explorer dell'hub IoT.
  ![Dispositivo registrato](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="change-the-device-id"></a>Modificare l'ID dispositivo

L'ID dispositivo predefinito registrato nell'hub IoT di Azure è *AZ3166*. Se si vuole modificare l'ID, seguire le istruzioni in [Customize device ID](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/) (Personalizzare l'ID dispositivo).

## <a name="problems-and-feedback"></a>Problemi e commenti

In caso di problemi, fare riferimento alle numerose [domande frequenti](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) sul DevKit o contattare i canali seguenti per ricevere assistenza:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come registrare un dispositivo in modo sicuro al servizio Device Provisioning usando Device Identity Composition Engine, in modo che il dispositivo possa registrarsi automaticamente all'hub IoT di Azure. 

In breve, si è appreso come:

> [!div class="checklist"]
> * Configurare l'endpoint globale del servizio Device Provisioning in un dispositivo.
> * Usare un segreto UDS per generare un certificato X.509.
> * Registrare un singolo dispositivo.
> * Verificare che il dispositivo sia registrato.

Altre informazioni in [Creare ed effettuare il provisioning di un dispositivo simulato](./quick-create-simulated-device.md).

