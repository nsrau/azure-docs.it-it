---
title: Risolvere i problemi relativi alle connessioni del dispositivo ad Azure IoT Central | Microsoft Docs
description: Risolvere i problemi relativi alla mancata visualizzazione dei dati dai dispositivi in IoT Central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2020
ms.topic: troubleshooting
ms.service: iot-central
ms.openlocfilehash: 2bf48b6808fccb1f4344e66a2b8f1fc2d4c52ef6
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322450"
---
# <a name="troubleshoot-why-data-from-your-devices-isnt-showing-up-in-azure-iot-central"></a>Risolvere i problemi perché i dati dei dispositivi non vengono visualizzati in Azure IoT Central

Questo documento consente agli sviluppatori di dispositivi di scoprire perché i dati che i dispositivi inviano a IoT Central potrebbero non essere visualizzati nell'applicazione.

Ci sono due aree principali da analizzare:

- Problemi di connettività del dispositivo
  - Problemi di autenticazione come il dispositivo con credenziali non valide
  - Problemi relativi alla connettività di rete
  - Il dispositivo non è approvato o bloccato
- Problemi di forma del payload del dispositivo

Questa guida alla risoluzione dei problemi si concentra sui problemi di connettività del dispositivo e sui problemi di forma del payload del dispositivo.

## <a name="device-connectivity-issues"></a>Problemi di connettività del dispositivo

Questa sezione consente di determinare se i dati raggiungono IoT Central.

Se non è già stato fatto, installare lo `az cli` strumento e l' `azure-iot` estensione.

Per informazioni su come installare `az cli` , vedere [installare l'interfaccia della](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)riga di comando di Azure.

Per [installare](https://docs.microsoft.com/cli/azure/azure-cli-reference-for-IoT?view=azure-cli-latest#extension-reference-installation) l' `azure-iot` estensione, eseguire il comando seguente:

```cmd/bash
az extension add --name azure-iot
```

> [!NOTE]
> È possibile che venga richiesto di installare la `uamqp` libreria la prima volta che si esegue un comando di estensione.

Dopo aver installato l' `azure-iot` estensione, avviare il dispositivo per verificare se i messaggi inviati stanno per IOT Central.

Usare i comandi seguenti per accedere alla sottoscrizione in cui è presente l'applicazione IoT Central:

```cmd/bash
az login
az set account --subscription <your-subscription-id>
```

Per monitorare i dati di telemetria inviati dal dispositivo, usare il comando seguente:

```cmd/bash
az iot central diagnostics monitor-events --app-id <app-id> --device-id <device-name>
```

Se il dispositivo è connesso correttamente a IoT Central, viene visualizzato un output simile al seguente:

```cmd/bash
Monitoring telemetry.
Filtering on device: device-001
{
    "event": {
        "origin": "device-001",
        "module": "",
        "interface": "",
        "component": "",
        "payload": {
            "temp": 65.57910343679293,
            "humid": 36.16224660107426
        }
    }
}
```

Per monitorare gli aggiornamenti della proprietà che il dispositivo sta scambiando con IoT Central, usare il comando di anteprima seguente:

```cmd/bash
az iot central diagnostics monitor-properties --app-id <app-id> --device-id <device-name>
```

Se il dispositivo invia correttamente gli aggiornamenti delle proprietà, viene visualizzato un output simile al seguente:

```cmd/bash
Changes in reported properties:
version : 32
{'state': 'true', 'name': {'value': {'value': 'Contoso'}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac
': 200}, 'brightness': {'value': {'value': 2}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac': 200}, 'p
rocessorArchitecture': 'ARM', 'swVersion': '1.0.0'}
```

Se i dati vengono visualizzati nel terminale, i dati li stanno rendendo fino all'applicazione IoT Central.

Se dopo alcuni minuti non viene visualizzato alcun dato, provare a premere il `Enter` tasto o `return` sulla tastiera, nel caso in cui l'output sia bloccato.

Se non vengono visualizzati dati nel terminale, è probabile che il dispositivo abbia problemi di connettività di rete o che non invii correttamente i dati per IoT Central.

### <a name="check-the-provisioning-status-of-your-device"></a>Verificare lo stato di provisioning del dispositivo

Se i dati non vengono visualizzati nel monitor, verificare lo stato del provisioning del dispositivo eseguendo il comando seguente:

```cmd/bash
az iot central device registration-info --app-id <app-id> --device-id <device-name>
```

L'output seguente mostra un esempio di dispositivo bloccato dalla connessione:

```json
{
  "@device_id": "v22upeoqx6",
  "device_registration_info": {
    "device_status": "blocked",
    "display_name": "Environmental Sensor - v22upeoqx6",
    "id": "v22upeoqx6",
    "instance_of": "urn:krhsi_k0u:modelDefinition:w53jukkazs",
    "simulated": false
  },
  "dps_state": {
    "error": "Device is blocked from connecting to IoT Central application. Unblock the device in IoT Central and retry. Learn more:
https://aka.ms/iotcentral-docs-dps-SAS",
    "status": null
  }
}
```

| Stato del provisioning dei dispositivi | Descrizione | Possibile mitigazione |
| - | - | - |
| Sottoposto a provisioning | Nessun problema immediatamente riconoscibile. | N/D |
| Registrato | Il dispositivo non è ancora connesso a IoT Central. | Verificare la presenza di problemi di connettività nei log del dispositivo. |
| Bloccato | Il dispositivo è bloccato dalla connessione a IoT Central. | Il dispositivo è bloccato dalla connessione all'applicazione IoT Central. Sbloccare il dispositivo in IoT Central e riprovare. Per altre informazioni, vedere [bloccare i dispositivi](concepts-get-connected.md#device-status-values). |
| Unapproved | Il dispositivo non è approvato. | Il dispositivo non è approvato per la connessione all'applicazione IoT Central. Approvare il dispositivo in IoT Central e riprovare. Per altre informazioni, vedere [approva dispositivi](concepts-get-connected.md#connect-without-registering-devices) |
| Non associato | Il dispositivo non è associato a un modello di dispositivo. | Associare il dispositivo a un modello di dispositivo in modo che IoT Central sappia come analizzare i dati. |

Altre informazioni sui [codici di stato del dispositivo](concepts-get-connected.md#device-status-values).

### <a name="error-codes"></a>Codici di errore

Se non si è ancora in grado di diagnosticare il motivo per cui i dati non vengono visualizzati in `monitor-events` , il passaggio successivo consiste nel cercare i codici di errore segnalati dal dispositivo.

Avviare una sessione di debug nel dispositivo o raccogliere i log dal dispositivo. Verificare la presenza di eventuali codici di errore segnalati dal dispositivo.

Nelle tabelle seguenti vengono illustrati i codici di errore comuni e le possibili azioni da attenuare.

Se si verificano problemi relativi al flusso di autenticazione:

| Codice di errore | Descrizione | Possibile mitigazione |
| - | - | - |
| 400 | Il corpo della richiesta non è valido. Ad esempio, non può essere analizzato o l'oggetto non può essere convalidato. | Assicurarsi di inviare il corpo della richiesta corretto come parte del flusso di attestazione o usare un SDK per dispositivi. |
| 401 | Non è possibile convalidare il token di autorizzazione. Ad esempio, è scaduta o non si applica all'URI della richiesta. Questo codice di errore viene anche restituito ai dispositivi come parte del flusso di attestazione TPM. | Verificare che il dispositivo abbia le credenziali corrette. |
| 404 | L'istanza del servizio Device provisioning o una risorsa, ad esempio una registrazione, non esiste. | [Archiviare un ticket con il supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)tecnico. |
| 412 | La `ETag` nella richiesta non corrisponde a quella `ETag` della risorsa esistente, come per ogni RFC7232. | [Archiviare un ticket con il supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)tecnico. |
| 429 | Le operazioni sono limitate dal servizio. Per i limiti di servizio specifici, vedere [limiti del servizio Device provisioning in hub](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits). | Ridurre la frequenza dei messaggi, suddividere le responsabilità tra più dispositivi. |
| 500 | An internal error occurred. | Consente di [archiviare un ticket con supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) tecnico per verificare se è più utile. |

## <a name="payload-shape-issues"></a>Problemi di forma del payload

Una volta stabilito che il dispositivo invia dati a IoT Central, il passaggio successivo consiste nel verificare che il dispositivo invii i dati in un formato valido.

Esistono due categorie principali di problemi comuni che provocano la mancata visualizzazione dei dati del dispositivo in IoT Central:

- Mancata corrispondenza tra il modello di dispositivo e i dati del dispositivo:
  - Mancata corrispondenza di denominazione, ad esempio errori di digitazione o problemi di corrispondenza dei casi.
  - Proprietà non modellate in cui lo schema non è definito nel modello di dispositivo.
  - Schema non corrispondente, ad esempio un tipo definito nel modello come `boolean` , ma i dati sono una stringa.
  - Lo stesso nome di telemetria è definito in più interfacce, ma il dispositivo non è Plug and Play conforme.
- La forma dei dati non è un JSON valido. Per altre informazioni, vedere payload di dati di [telemetria, proprietà e comandi](concepts-telemetry-properties-commands.md).

Per individuare le categorie in cui si trova il problema, eseguire il comando più appropriato per lo scenario:

- Per convalidare la telemetria, usare il comando Preview:

    ```cmd/bash
    az iot central diagnostics validate-messages --app-id <app-id> --device-id <device-name>
    ```

- Per convalidare gli aggiornamenti delle proprietà, utilizzare il comando anteprima

    ```cmd/bash
    az iot central diagnostics validate-properties --app-id <app-id> --device-id <device-name>
    ```

È possibile che venga richiesto di installare la `uamqp` libreria la prima volta che si esegue un `validate` comando.

L'output seguente mostra messaggi di errore e di avviso di esempio dal comando Validate:

```cmd/bash
Validating telemetry.
Filtering on device: v22upeoqx6.
Exiting after 300 second(s), or 10 message(s) have been parsed (whichever happens first).
[WARNING] [DeviceId: v22upeoqx6] No encoding found. Expected encoding 'utf-8' to be present in message header.

[WARNING] [DeviceId: v22upeoqx6] Content type '' is not supported. Expected Content type is 'application/json'.

[ERROR] [DeviceId: v22upeoqx6] [TemplateId: urn:krhsi_k0u:modelDefinition:w53jukkazs] Datatype of field 'humid' does not match the da
tatype 'double'. Data '56'. All dates/times/datetimes/durations must be ISO 8601 compliant.
```

Se si preferisce usare un'interfaccia utente grafica, usare la vista **dati IOT Central RAW** per verificare se un elemento non è in fase di modellazione. La visualizzazione **dati non elaborati** non rileva se il dispositivo sta INVIAndo JSON in formato non valido.

:::image type="content" source="media/troubleshoot-connection/raw-data-view.png" alt-text="Screenshot della visualizzazione dati non elaborati":::

Dopo aver rilevato il problema, potrebbe essere necessario aggiornare il firmware del dispositivo oppure creare un nuovo modello di dispositivo che modella i dati precedentemente non modellati.

Se si sceglie di creare un nuovo modello che modella correttamente i dati, eseguire la migrazione dei dispositivi dal modello precedente al nuovo modello. Per altre informazioni, vedere [gestire i dispositivi nell'applicazione IoT Central di Azure](howto-manage-devices.md).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, è possibile contattare gli esperti di Azure nei [Forum MSDN e stack overflow di Azure](https://azure.microsoft.com/support/community/). In alternativa, è possibile archiviare un [ticket di supporto di Azure](https://portal.azure.com/#create/Microsoft.Support).

Per altre informazioni, vedere [supporto di Azure e opzioni](../../iot-fundamentals/iot-support-help.md)per la guida.
