---
title: Connettore Azure per FHIR (anteprima)-Guida alla risoluzione dei problemi e procedure
description: Come risolvere i problemi relativi al connettore comune di Azure per i messaggi di errore e le condizioni di FHIR (anteprima) e copiare i file di mapping
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: jasteppe
ms.openlocfilehash: b404fa5322d3afa8cbf71741382d44dd0433d49c
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553683"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Guida alla risoluzione dei problemi del connettore Azure per FHIR (anteprima)

Questo articolo illustra la procedura per la risoluzione dei problemi relativi al connettore comune di Azure per i messaggi di errore e le condizioni di FHIR *.  

Si apprenderà anche come creare copie del connettore Azure per i file di mapping JSON FHIR per la modifica e l'archiviazione all'esterno del portale di Azure o per fornire al supporto tecnico di Azure l'apertura di un ticket di supporto. 

## <a name="error-messages-and-fixes"></a>Messaggi di errore e correzioni

|Message   |Condizione  |Fix         |
|----------|-----------|------------|
|Nome mapping non valido. il nome del mapping deve essere Device o FHIR|Il tipo di mapping specificato non è Device o FHIR|Usare uno dei due tipi di mapping supportati (ad esempio, Device o FHIR)|
|Rigenera parametri chiave non definiti|Rigenera richiesta chiave|Includere i parametri nella richiesta di rigenerazione della chiave|
|È stato raggiunto il numero massimo di istanze del connettore di Internet delle cose di cui è possibile eseguire il provisioning in questa sottoscrizione|È stato raggiunto il connettore Azure per la quota di sottoscrizioni FHIR (il valore predefinito è (2) per sottoscrizione)|Eliminare una delle istanze esistenti del connettore Azure per FHIR, usare una sottoscrizione diversa che non ha raggiunto la quota di sottoscrizione o richiedere un aumento della quota della sottoscrizione|
|Lo spostamento della risorsa non è supportato per la risorsa API di Azure abilitata per il connettore Internet|Tentativo di eseguire un'operazione di spostamento su un'API di Azure per la risorsa FHIR con una o più istanze del connettore Azure per FHIR|Elimina le istanze esistenti del connettore Azure per FHIR per eseguire e completare l'operazione di spostamento|
|Il provisioning del connettore Internet non è stato eseguito|Tentativo di usare i servizi figlio (connessioni & mapping) quando non è stato eseguito il provisioning dell'elemento padre (connettore Azure FHIR per Azure)|Effettuare il provisioning di un connettore Azure per FHIR|
|La richiesta non è supportata|La richiesta API specifica non è supportata|Usa la richiesta API corretta|
|L'account non esiste|Il tentativo di aggiungere un connettore Azure per FHIR e l'API di Azure per la risorsa FHIR non esiste|Creare la risorsa API di Azure per FHIR e quindi ripetere l'operazione|
|La versione FHIR della risorsa API di Azure per FHIR non è supportata per il connettore Internet|Tentativo di usare un connettore Azure per FHIR con una versione non compatibile dell'API di Azure per la risorsa FHIR|Creare una nuova risorsa API di Azure per FHIR (versione R4) o usare un'API di Azure esistente per la risorsa FHIR (versione R4)

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-mapping-files"></a>Creazione di copie del connettore Azure per i file di mapping FHIR (anteprima)
La copia del connettore Azure per i file di mapping di FHIR può essere utile per la modifica e l'archiviazione all'esterno del sito Web di portale di Azure e per fornire supporto tecnico di Azure all'apertura di un ticket di supporto.

> [!NOTE]
> JSON è l'unico formato supportato per i file di mapping del dispositivo e del FHIR in questo momento.

> [!TIP]
> Scopri di più sul connettore Azure Internet per il [dispositivo FHIR e i file JSON di mapping FHIR](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. Per il Dashboard delle risorse di Azure per FHIR nella sezione **"componenti aggiuntivi"** , selezionare **"connettore Internet (anteprima)"** in basso a sinistra.

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="Connettore Internet" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Selezionare il **"connettore"** da cui verranno copiati i file di mapping.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="Connettore Internet" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Questo processo può essere usato anche per copiare e salvare il contenuto del file JSON **"Configure FHIR mapping"** .

3. Selezionare **"Configura mapping dispositivi"**.

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="Connettore Internet" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. Selezionare il contenuto del file JSON ed eseguire un'operazione di copia (ad esempio, selezionare Ctrl + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="Connettore Internet" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Eseguire un'operazione Incolla (ad esempio, selezionare Ctrl + v) in un nuovo file all'interno di un editor, ad esempio Visual Studio Code, blocco note, e salvare il file con estensione JSON.

> [!TIP]
> Se si apre un ticket di [supporto tecnico di Azure](https://azure.microsoft.com/support/create-ticket/) per il connettore Azure per FHIR, assicurarsi di includere copie dei file di mapping per facilitare il processo di risoluzione dei problemi.

## <a name="next-steps"></a>Passaggi successivi

Vedere le domande frequenti sul connettore Azure per FHIR.

>[!div class="nextstepaction"]
>[Connettore Azure per le domande frequenti su FHIR](fhir-faq.md#azure-iot-connector-for-fhir-preview)

* Nel portale di Azure, il connettore Azure per FHIR viene indicato come connettore Internet (anteprima).

FHIR è il marchio registrato di HL7, usato con l'autorizzazione di HL7.