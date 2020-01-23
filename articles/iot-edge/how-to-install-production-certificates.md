---
title: Installare i certificati nel dispositivo-Azure IoT Edge | Microsoft Docs
description: Creare certificati di test e apprendere come installarli in un dispositivo Azure IoT Edge per prepararsi alla distribuzione di produzione.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cf073572cd5b371ec484c99f14cbefb4cba75ce7
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76509904"
---
# <a name="install-production-certificates-on-an-iot-edge-device"></a>Installare i certificati di produzione in un dispositivo IoT Edge

Tutti i dispositivi IoT Edge usano i certificati per creare connessioni sicure tra il runtime e tutti i moduli in esecuzione nel dispositivo.
IoT Edge i dispositivi che funzionano come gateway usano questi stessi certificati per connettersi anche ai dispositivi downstream.

Quando si installa per la prima volta IoT Edge e si effettua il provisioning del dispositivo, il dispositivo viene configurato con certificati temporanei per poter testare il servizio.
Questi certificati temporanei scadono entro 90 giorni oppure possono essere reimpostati riavviando il computer.
Quando si è pronti per spostare i dispositivi in uno scenario di produzione o si vuole creare uno scenario di gateway, è necessario fornire i propri certificati.
Questo articolo illustra i passaggi per installare i certificati nei dispositivi IoT Edge.

Per altre informazioni sui diversi tipi di certificati e sui relativi ruoli in uno scenario di IoT Edge, vedere informazioni su [come Azure IOT Edge usa i certificati](iot-edge-certs.md).

>[!NOTE]
>Il termine "CA radice" usato in questo articolo si riferisce al certificato pubblico dell'autorità superiore della catena di certificati per la soluzione Internet delle cose. Non è necessario utilizzare la radice del certificato di un'autorità di certificazione con diffusore o la radice dell'autorità di certificazione dell'organizzazione. In molti casi, si tratta in realtà di un certificato pubblico CA intermedio.

## <a name="prerequisites"></a>Prerequisiti

* Un dispositivo IoT Edge, in esecuzione in [Windows](how-to-install-iot-edge-windows.md) o [Linux](how-to-install-iot-edge-linux.md).
* Disporre di un certificato dell'autorità di certificazione (CA) radice, autofirmato o acquistato da un'autorità di certificazione commerciale attendibile come Baltimora, Verisign, DigiCert o GlobalSign.

Se non si ha ancora un'autorità di certificazione radice, ma si vuole provare IoT Edge funzionalità che richiedono certificati di produzione (ad esempio, scenari di gateway), è possibile [creare certificati demo per testare le funzionalità del dispositivo IOT Edge](how-to-create-test-certificates.md).

## <a name="create-production-certificates"></a>Creare certificati di produzione

Per creare i file seguenti, è necessario usare la propria autorità di certificazione:

* CA radice
* Certificato CA del dispositivo
* Chiave privata della CA del dispositivo

In questo articolo si fa riferimento alla *CA radice* non è l'autorità di certificazione in primo piano per un'organizzazione. Si tratta dell'autorità di certificazione superiore per lo scenario IoT Edge, che il modulo dell'hub IoT Edge, i moduli utente e tutti i dispositivi downstream usano per stabilire una relazione di trust tra loro.

Per visualizzare un esempio di questi certificati, esaminare gli script che creano certificati demo in [gestione dei certificati della CA di test per esempi ed esercitazioni](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

## <a name="install-certificates-on-the-device"></a>Installare i certificati nel dispositivo

Installare la catena di certificati nel dispositivo IoT Edge e configurare il runtime IoT Edge per fare riferimento ai nuovi certificati.

Ad esempio, se sono stati usati gli script di esempio per [creare i certificati demo](how-to-create-test-certificates.md), i tre file che è necessario copiare nel dispositivo IOT Edge sono i seguenti:

* Certificato CA del dispositivo: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Chiave privata della CA del dispositivo: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* CA radice: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Copiare i tre file di certificato e di chiave nel dispositivo IoT Edge.

   È possibile usare un servizio come [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) o una funzione come [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) per spostare i file del certificato.  Se i certificati sono stati generati nel dispositivo IoT Edge stesso, è possibile ignorare questo passaggio e usare il percorso della directory di lavoro.

2. Aprire il file di configurazione del daemon di sicurezza di IoT Edge.

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

3. Impostare le proprietà del **certificato** nel file config. YAML sull'URI del file del certificato e dei file di chiave nel dispositivo IOT Edge. Rimuovere il carattere `#` prima delle proprietà del certificato per rimuovere il commento dalle quattro righe. Verificare che la riga **certificati:** non includa spazi vuoti precedenti e che gli elementi nidificati siano rientrati in due spazi. Ad esempio:

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "file:///c:/path/device-ca.cert.pem"
        device_ca_pk: "file:///c:/path/device-ca.key.pem"
        trusted_ca_certs: "file:///c:/path/root-ca.root.ca.cert.pem"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "file:///path/device-ca.cert.pem"
        device_ca_pk: "file:///path/device-ca.key.pem"
        trusted_ca_certs: "file:///path/root-ca.root.ca.cert.pem"
      ```

4. Nei dispositivi Linux, assicurarsi che l'utente **iotedge** disponga delle autorizzazioni di lettura per la directory che contiene i certificati.

## <a name="next-steps"></a>Passaggi successivi

L'installazione dei certificati in un dispositivo IoT Edge è un passaggio necessario prima di distribuire la soluzione nell'ambiente di produzione. Scopri di più su come [preparare la distribuzione della tua IOT Edge soluzione nell'](production-checklist.md)ambiente di produzione.
