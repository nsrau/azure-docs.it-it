---
title: Gestire i certificati dei dispositivi - Azure IoT Edge Documenti Microsoft
description: Creare certificati di test, installarli e gestirli in un dispositivo Azure IoT Edge per preparare la distribuzione di produzione.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c18c3d560adb3c3cae54bda808ee5842c260fd6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79539208"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>Gestire i certificati in un dispositivo IoT Edge

Tutti i dispositivi IoT Edge usano certificati per creare connessioni sicure tra il runtime e tutti i moduli in esecuzione nel dispositivo. I dispositivi IoT Edge che funzionano come gateway usano questi stessi certificati per connettersi anche ai dispositivi downstream.

## <a name="install-production-certificates"></a>Installare i certificati di produzione

Quando si installa IoT Edge per la prima volta e si esegue il provisioning del dispositivo, il dispositivo viene configurato con certificati temporanei in modo da poter testare il servizio.
Questi certificati temporanei scadono tra 90 giorni o possono essere reimpostati riavviando il computer.
Quando si è pronti per spostare i dispositivi in uno scenario di produzione o si vuole creare uno scenario gateway, è necessario fornire i propri certificati.
In questo articolo viene illustrata la procedura per installare i certificati nei dispositivi IoT Edge.This article demonstrates the steps to install certificates on your IoT Edge devices.

Per altre informazioni sui diversi tipi di certificati e sui relativi ruoli in uno scenario IoT Edge, vedere Informazioni sull'utilizzo dei certificati da parte di Azure IoT Edge .To learn more about the different types of certificates and their roles in an IoT Edge scenario, see [Understand how Azure IoT Edge uses certificates](iot-edge-certs.md).

>[!NOTE]
>Il termine "CA radice" usato in questo articolo si riferisce al certificato pubblico dell'autorità più in alto della catena di certificati per la soluzione IoT. Non è necessario utilizzare la radice del certificato di un'autorità di certificazione sindacato o la radice dell'autorità di certificazione dell'organizzazione. In molti casi, si tratta in realtà di un certificato pubblico CA intermedio.

### <a name="prerequisites"></a>Prerequisiti

* Un dispositivo IoT Edge, in esecuzione su [Windows](how-to-install-iot-edge-windows.md) o [Linux](how-to-install-iot-edge-linux.md).
* Disporre di un certificato dell'autorità di certificazione (CA) radice, autofirmato o acquistato da un'autorità di certificazione commerciale attendibile come Baltimora, Verisign, DigiCert o GlobalSign.

Se non si dispone ancora di un'autorità di certificazione radice, ma si desidera provare le funzionalità di IoT Edge che richiedono certificati di produzione (ad esempio scenari gateway), è possibile [creare certificati dimostrativi per testare le funzionalità dei dispositivi perimetrali IoT.](how-to-create-test-certificates.md)

### <a name="create-production-certificates"></a>Creare certificati di produzione

È consigliabile utilizzare l'autorità di certificazione personalizzata per creare i seguenti file:

* CA radice
* Certificato CA del dispositivo
* Chiave privata CA dispositivo

In questo articolo, ciò che si fa riferimento come la *CA radice* non è l'autorità di certificazione più in alto per un'organizzazione. È l'autorità di certificazione più importante per lo scenario IoT Edge, che il modulo hub IoT Edge, i moduli utente e tutti i dispositivi downstream utilizzano per stabilire l'attendibilità tra loro.

Per un esempio di questi certificati, esaminare gli script che creano certificati dimostrativi in Gestione dei certificati CA di [prova per esempi ed esercitazioni](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

### <a name="install-certificates-on-the-device"></a>Installare i certificati nel dispositivoInstall certificates on the device

Installare la catena di certificati nel dispositivo Edge IoT e configurare il runtime di Edge IoT in modo che faccia riferimento ai nuovi certificati.

Ad esempio, se sono stati utilizzati gli script di esempio per [creare certificati dimostrativi](how-to-create-test-certificates.md), copiare i seguenti file nel dispositivo IoT-Edge:

* Certificato CA dispositivo:`<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Chiave privata CA per dispositivi:`<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* CA radice:`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Copiare i tre file di codice e certificato nel dispositivo IoT Edge.

   È possibile usare un servizio come L'insieme di [credenziali delle](https://docs.microsoft.com/azure/key-vault) chiavi di Azure o una funzione come Protocollo di copia [sicura](https://www.ssh.com/ssh/scp/) per spostare i file del certificato.  Se i certificati sono stati generati sul dispositivo IoT Edge stesso, è possibile ignorare questo passaggio e utilizzare il percorso della directory di lavoro.

1. Aprire il file di configurazione del daemon di sicurezza di IoT Edge.

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

1. Impostare le proprietà del **certificato** nel file config.yaml sul percorso completo del certificato e dei file di chiave nel dispositivo IoT Edge. Rimuovere `#` il carattere prima delle proprietà del certificato per rimuovere il commento dalle quattro righe. Assicurarsi che i **certificati:** riga non contiene spazi vuoti precedenti e che gli elementi nidificati sono rientrati di due spazi. Ad esempio:

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "c:\\<path>\\device-ca.cert.pem"
        device_ca_pk: "c:\\<path>\\device-ca.key.pem"
        trusted_ca_certs: "c:\\<path>\\root-ca.root.ca.cert.pem"
      ```

   * Linux:

      ```yaml
      certificates:
        device_ca_cert: "<path>/device-ca.cert.pem"
        device_ca_pk: "<path>/device-ca.key.pem"
        trusted_ca_certs: "<path>/root-ca.root.ca.cert.pem"
      ```

1. Nei dispositivi Linux, assicurarsi che l'utente **iotedge** disponga delle autorizzazioni di lettura per la directory che contiene i certificati.

1. Se in precedenza sono stati usati altri certificati per IoT Edge nel dispositivo, eliminare i file nelle due directory seguenti prima di avviare o riavviare IoT Edge:

   * Windows: `C:\ProgramData\iotedge\hsm\certs` e`C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux: `/var/lib/iotedge/hsm/certs` e`/var/lib/iotedge/hsm/cert_keys`

## <a name="customize-certificate-lifetime"></a>Personalizzare la durata del certificato

IoT Edge genera automaticamente certificati sul dispositivo in diversi casi, tra cui:

* Se non si forniscono certificati di produzione personalizzati durante l'installazione e il provisioning di IoT Edge, il gestore della sicurezza IoT Edge genera automaticamente un **certificato CA del dispositivo.** Questo certificato autofirmato è destinato solo agli scenari di sviluppo e test, non all'ambiente di produzione. Questo certificato scade dopo 90 giorni.
* Il gestore della sicurezza IoT Edge genera anche un certificato CA del **carico di lavoro** firmato dal certificato CA del dispositivo

Per altre informazioni sulla funzione dei diversi certificati in un dispositivo perimetrale IoT, vedere Comprendere in che modo [Azure IoT Edge usa](iot-edge-certs.md)i certificati.

Per questi due certificati generati automaticamente, è possibile impostare il flag **auto_generated_ca_lifetime_days** in config.yaml per configurare il numero di giorni per la durata dei certificati.

>[!NOTE]
>Esiste un terzo certificato generato automaticamente creato dal gestore della sicurezza IoT Edge, ovvero il **certificato del server hub IoT Edge**. Questo certificato ha sempre un 90 giorni, ma viene rinnovato automaticamente prima della scadenza. Il **valore auto_generated_ca_lifetime_days** non influisce su questo certificato.

Per configurare la scadenza del certificato a un valore diverso da quello predefinito di 90 giorni, aggiungere il valore in giorni alla sezione **certificates** del file config.yaml.

```yaml
certificates:
  device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
  device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
  trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
  auto_generated_ca_lifetime_days: <value>
```

Se sono stati forniti certificati CA per dispositivi personalizzati, questo valore si applica comunque al certificato CA del carico di lavoro, a condizione che il valore di durata impostato sia inferiore alla durata del certificato CA del dispositivo.

Dopo aver specificato il flag nel file config.yaml, attenersi alla seguente procedura:

1. Eliminare il contenuto `hsm` della cartella.

   Windows: `C:\ProgramData\iotedge\hsm\certs and C:\ProgramData\iotedge\hsm\cert_keys` Linux:`/var/lib/iotedge/hsm/certs and /var/lib/iotedge/hsm/cert_keys`

1. Riavviare il servizio IoT Edge.

   Windows:

   ```powershell
   Restart-Service iotedge
   ```

   Linux:

   ```bash
   sudo systemctl restart iotedge
   ```

1. Confermare l'impostazione della durata.

   Windows:

   ```powershell
   iotedge check --verbose
   ```

   Linux:

   ```bash
   sudo iotedge check --verbose
   ```

   Controllare l'output della **disponibilità di produzione:** controllo dei certificati, che elenca il numero di giorni fino alla scadenza dei certificati CA del dispositivo generati automaticamente.

## <a name="next-steps"></a>Passaggi successivi

L'installazione di certificati in un dispositivo Perimetrale IoT è un passaggio necessario prima di distribuire la soluzione nell'ambiente di produzione. Ulteriori informazioni su come preparare la distribuzione della [soluzione IoT Edge nell'ambiente di produzione.](production-checklist.md)
