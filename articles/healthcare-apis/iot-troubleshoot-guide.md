---
title: 'Connettore Azure per FHIR (connettore Internet): Guida alla risoluzione dei problemi e procedure'
description: Come risolvere i problemi relativi alle condizioni e ai messaggi di errore comuni del connettore Internet e copiare i file di mapping
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jasteppe
ms.openlocfilehash: eff1272318413da7855134b0a8a44dd0a0711a6c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285578"
---
# <a name="iot-connector-preview-troubleshooting-guide"></a>Guida alla risoluzione dei problemi del connettore Internet (anteprima)

Questo articolo illustra i passaggi per la risoluzione dei problemi relativi ai messaggi di errore e alle condizioni del connettore Common Internet (anteprima).  

Si apprenderà anche come creare copie dei file di mapping JSON (anteprima) per la modifica e l'archiviazione all'esterno del portale di Azure.

## <a name="error-messages-and-fixes"></a>Messaggi di errore e correzioni

|Message   |Condizione  |Fix         |
|----------|-----------|------------|
|Nome mapping non valido. il nome del mapping deve essere Device o FHIR|Il tipo di mapping specificato non è Device o FHIR|Usare uno dei due tipi di mapping supportati (ad esempio, Device o FHIR)|
|Rigenera parametri chiave non definiti|Rigenera richiesta chiave|Includere i parametri nella richiesta di rigenerazione della chiave|
|È stato raggiunto il numero massimo di istanze del connettore Internet per cui è possibile eseguire il provisioning in questa sottoscrizione|È stata raggiunta la quota di sottoscrizione del connettore Internet (il valore predefinito è 2 per sottoscrizione|Eliminare uno dei connettori esistenti, usare una sottoscrizione diversa che non abbia raggiunto la quota di (2) connettori per sottoscrizione o richiedere un aumento della quota della sottoscrizione|
|Lo spostamento della risorsa non è supportato per la risorsa API di Azure abilitata per il connettore Internet|Tentativo di eseguire un'operazione di spostamento su un'API di Azure per la risorsa FHIR con uno o più connettori Internet|Elimina i connettori esistenti per eseguire/completare le operazioni di spostamento|
|Il collegamento privato è abilitato per l'API di Azure per la risorsa FHIR.  Il collegamento privato non è supportato con il connettore Internet|Tentativo di aggiungere un connettore Internet ad un'API di Azure per la risorsa FHIR con collegamento privato abilitato|Selezionare o creare un'API di Azure per la risorsa FHIR (versione R4) per cui non è abilitato il collegamento privato|
|Il provisioning del connettore Internet non è stato eseguito|Tentativo di usare i servizi figlio (connessioni & mapping) quando non è stato eseguito il provisioning dell'elemento padre (connettore Internet degli elementi)|Effettuare il provisioning di un connettore Internet|
|La richiesta non è supportata|La richiesta API specifica non è supportata|Usa la richiesta API corretta|
|L'account non esiste|Il tentativo di aggiungere un connettore Internet e l'API di Azure per la risorsa FHIR non esiste|Creare la risorsa API di Azure per FHIR e quindi ripetere l'operazione|
|La versione FHIR della risorsa API di Azure per FHIR non è supportata per il connettore Internet|Tentativo di usare un connettore Internet con una versione incompatibile della risorsa API di Azure per FHIR|Creare una nuova risorsa API di Azure per FHIR (versione R4) o usare un'API di Azure esistente per la risorsa FHIR (versione R4)

## <a name="creating-copies-of-the-iot-connector-preview-mapping-files"></a>Creazione di copie dei file di mapping del connettore Internet (anteprima)
> [!NOTE]
> JSON è l'unico formato supportato per i file di mapping del dispositivo e del FHIR in questo momento.

> [!TIP]
> La copia dei file di mapping del connettore Internet può essere utile per la modifica e l'archiviazione all'esterno del sito Web del portale di Azure e per fornire al supporto tecnico di Azure l'apertura di un ticket di supporto.
> 
> Altre informazioni sul dispositivo del connettore Internet [e sui file JSON di mapping FHIR](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. Per il Dashboard delle risorse di Azure per FHIR nella sezione **"componenti aggiuntivi"** , selezionare **"connettore Internet (anteprima)"** in basso a sinistra.

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="Connettore Internet" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Selezionare il **"connettore"** da cui verranno copiati i file di mapping.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="Connettore Internet" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

3. Selezionare **"Configura mapping dispositivi"**.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="Connettore Internet" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

> [!NOTE]
> Questo processo può essere usato anche per la copia o il salvataggio del contenuto del JSON **"Configure FHIR mapping"** .

4. Selezionare il contenuto del file JSON ed eseguire un'operazione di copia (ad esempio, selezionare Ctrl + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="Connettore Internet" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Eseguire un'operazione Incolla (ad esempio, selezionare Ctrl + v) in un nuovo file all'interno di un editor, ad esempio Visual Studio Code, blocco note, e salvare il file con estensione JSON.

> [!TIP]
> Se si apre un ticket di [supporto tecnico di Azure](https://azure.microsoft.com/support/create-ticket/) per il connettore Internet, assicurarsi di includere copie dei file di mapping per facilitare il processo di risoluzione dei problemi.

## <a name="next-steps"></a>Passaggi successivi

Vedere le domande frequenti sul connettore Internet

>[!div class="nextstepaction"]
>[Domande frequenti sul connettore Internet](fhir-faq.md#iot-connector-preview)


FHIR è il marchio registrato di HL7, usato con l'autorizzazione di HL7.