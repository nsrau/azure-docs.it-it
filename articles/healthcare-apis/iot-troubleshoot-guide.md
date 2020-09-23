---
title: Connettore Azure per FHIR (anteprima)-Guida alla risoluzione dei problemi e procedure
description: Come risolvere i problemi relativi al connettore comune di Azure per i messaggi di errore e le condizioni di FHIR (anteprima) e copiare i file di mapping
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 09/16/2020
ms.author: jasteppe
ms.openlocfilehash: 64056ef2f63331686553c52040af9e10ee0ac468
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982995"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Guida alla risoluzione dei problemi del connettore Azure per FHIR (anteprima)

Questo articolo illustra la procedura per la risoluzione dei problemi relativi al connettore comune di Azure per i messaggi di errore e le condizioni di FHIR *.  

Si apprenderà anche come creare copie del connettore Azure per i mapping della conversione FHIR JSON (ad esempio, Device e FHIR).  

È possibile usare le copie JSON del mapping di conversione per la modifica e l'archiviazione all'esterno del portale di Azure.  

> [!TIP]
> Se si apre un ticket di [supporto tecnico di Azure](https://azure.microsoft.com/support/create-ticket/) per il connettore Azure per FHIR, assicurarsi di includere copie del mapping di conversione JSON per facilitare il processo di risoluzione dei problemi.

## <a name="device-and-fhir-conversion-mapping-json-template-validations-for-azure-iot-connector-for-fhir-preview"></a>Convalida dei modelli JSON per il mapping del dispositivo e della conversione FHIR per il connettore Azure Internet per FHIR (anteprima)
In questa sezione si apprenderà come eseguire il processo di convalida del connettore Azure per FHIR per convalidare i modelli JSON del dispositivo e del mapping della conversione FHIR prima di consentirne il salvataggio per l'uso.  Questi elementi sono necessari nel dispositivo e nel mapping della conversione FHIR JSON.

**Mapping del dispositivo**

|Elemento|Obbligatoria|
|:-------|:------|
|TypeName|True|
|TypeMatchExpression|True|
|DeviceIdExpression|True|
|TimestampExpression|True|
|Valori []. ValueName|True|
|Valori []. ValueExpression|True|

> [!NOTE]
> Valori []. ValueName e Values []. ValueExpression
>
> Questi elementi sono necessari solo se nella matrice è presente una voce di valore. è possibile che non sia stato eseguito il mapping dei valori. Viene usato quando i dati di telemetria inviati sono un evento. Ad esempio, quando un dispositivo IoMT indossabile viene inserito o rimosso. Gli elementi non hanno alcun valore ad eccezione di un nome che il connettore Azure Internet per FHIR corrisponde a e genera. Per la conversione di FHIR, il connettore Azure per FHIR esegue il mapping a un concetto di codice in base al tipo semantico. non vengono popolati i valori effettivi.

**Mapping di FHIR**

|Elemento|Obbligatoria|
|:------|:-------|
|TypeName|True|

> [!NOTE]
> Questo è l'unico elemento di mapping FHIR obbligatorio convalidato in questo momento.

## <a name="error-messages-and-fixes-for-azure-iot-connector-for-fhir-preview"></a>Messaggi di errore e correzioni per il connettore Azure per FHIR (anteprima)

|Messaggio|Visualizzato|Condizione|Fix| 
|-------|---------|---------|---|
|Nome mapping non valido. il nome del mapping deve essere Device o FHIR.|API|Il tipo di mapping specificato non è Device o FHIR.|Usare uno dei due tipi di mapping supportati (ad esempio, Device o FHIR).|
|Convalida non riuscita. Informazioni obbligatorie mancanti o non valide.|API e portale di Azure|Se si tenta di salvare un mapping di conversione, mancano le informazioni necessarie o l'elemento.|Aggiungere l'elemento o le informazioni sul mapping di conversione mancanti e tentare di salvare di nuovo il mapping di conversione.|
|Rigenera parametri chiave non definiti.|API|Rigenera la richiesta di chiave.|Includere i parametri nella richiesta di rigenerazione della chiave.|
|È stato raggiunto il numero massimo di istanze del connettore Internet per cui è possibile eseguire il provisioning in questa sottoscrizione.|API e portale di Azure|È stato raggiunto il connettore Azure per la quota di sottoscrizioni FHIR (il valore predefinito è (2) per sottoscrizione).|Eliminare una delle istanze esistenti del connettore Azure per FHIR.  Usare una sottoscrizione diversa che non ha raggiunto la quota di sottoscrizione.  Richiedere un aumento della quota della sottoscrizione.|
|Lo spostamento della risorsa non è supportato per la risorsa API di Azure abilitata per il connettore Internet.|API e portale di Azure|Tentativo di eseguire un'operazione di spostamento su un'API di Azure per la risorsa FHIR con una o più istanze del connettore Azure per FHIR.|Eliminare le istanze esistenti del connettore Azure per FHIR per eseguire l'operazione di spostamento.|
|Il provisioning del connettore Internet non è stato eseguito.|API|Il tentativo di usare i servizi figlio (connessioni & mapping) quando non è stato eseguito il provisioning dell'elemento padre (connettore Azure per FHIR).|Eseguire il provisioning di un connettore Azure per FHIR.|
|La richiesta non è supportata.|API|La richiesta API specifica non è supportata.|Usare la richiesta API corretta.|
|L'account non esiste.|API|Il tentativo di aggiungere un connettore Azure per FHIR e l'API di Azure per la risorsa FHIR non esiste.|Creare la risorsa API di Azure per FHIR, quindi ripetere l'operazione.|
|La versione FHIR della risorsa API di Azure per FHIR non è supportata per il connettore Internet.|API|Tentativo di usare un connettore Azure per FHIR con una versione incompatibile della risorsa API di Azure per FHIR.|Creare una nuova risorsa API di Azure per FHIR (versione R4) o usare un'API di Azure esistente per la risorsa FHIR (versione R4).

##  <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-azure-api-for-fhir"></a>Perché il connettore Azure per i dati FHIR (anteprima) non viene visualizzato nell'API di Azure per FHIR?

|Potenziali problemi|Correzioni|
|----------------|-----|
|È ancora in corso l'elaborazione dei dati.|I dati vengono uscita all'API di Azure per FHIR in batch (ogni ~ 15 minuti).  È possibile che i dati vengano ancora elaborati e che siano necessari altri tempi per la persistenza dei dati nell'API di Azure per FHIR.|
|Il mapping di conversione del dispositivo JSON non è stato configurato.|Configurare e salvare il mapping di conversione dei dispositivi JSON.|
|Il mapping di conversione FHIR JSON non è stato configurato.|Configurare e salvare in modo conforme il mapping di conversione FHIR JSON.|
|Il messaggio del dispositivo non contiene un'espressione prevista definita nel mapping del dispositivo.|Verificare le espressioni JsonPath definite nei token di corrispondenza del mapping dei dispositivi definiti nel messaggio del dispositivo.|
|Non è stata creata una risorsa del dispositivo nell'API di Azure per FHIR (tipo di risoluzione: solo ricerca) *.|Creare una risorsa dispositivo valida nell'API di Azure per FHIR. Assicurarsi che la risorsa del dispositivo contenga un identificatore che corrisponda all'identificatore del dispositivo fornito nel messaggio in arrivo.|
|Non è stata creata una risorsa paziente nell'API di Azure per FHIR (tipo di risoluzione: solo ricerca) *.|Creare una risorsa paziente valida nell'API di Azure per FHIR.|
|Il riferimento del dispositivo. patient non è impostato oppure il riferimento non è valido (tipo di risoluzione: solo ricerca) *.|Verificare che la risorsa del dispositivo contenga un [riferimento](https://www.hl7.org/fhir/device-definitions.html#Device.patient) valido a una risorsa del paziente.| 

* [Guida introduttiva di riferimento: distribuire il connettore Azure Internet (anteprima) usando portale di Azure](iot-fhir-portal-quickstart.md#create-new-azure-iot-connector-for-fhir-preview) per una descrizione funzionale del connettore Azure per i tipi di risoluzione FHIR (ad esempio, Lookup o create).

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-conversion-mapping-json"></a>Creazione di copie del connettore Azure per la conversione di FHIR (anteprima) JSON
La copia del connettore Azure per i file di mapping di FHIR può essere utile per la modifica e l'archiviazione all'esterno del sito Web portale di Azure.

Le copie dei file di mapping devono essere fornite al supporto tecnico di Azure quando si apre un ticket di supporto per facilitare la risoluzione dei problemi.

> [!NOTE]
> JSON è l'unico formato supportato per i file di mapping del dispositivo e del FHIR in questo momento.

> [!TIP]
> Altre informazioni sul connettore Azure per i dispositivi FHIR [e sul mapping della conversione FHIR JSON](https://docs.microsoft.com/azure/healthcare-apis/iot-mapping-templates)

1. Per il Dashboard delle risorse di Azure per FHIR nella sezione **"componenti aggiuntivi"** , selezionare **"connettore Internet (anteprima)"** in basso a sinistra.

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="Connector1" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. Selezionare il **"connettore"** da cui verrà copiato il mapping di conversione JSON.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="Connector2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> Questo processo può essere usato anche per copiare e salvare il contenuto del file JSON **"Configure FHIR mapping"** .

3. Selezionare **"Configura mapping dispositivi"**.

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="Connector3" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. Selezionare il contenuto del file JSON ed eseguire un'operazione di copia (ad esempio, selezionare Ctrl + c). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="Connector4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. Eseguire un'operazione Incolla (ad esempio, selezionare Ctrl + v) in un nuovo file all'interno di un editor, ad esempio Visual Studio Code, blocco note, e salvare il file con estensione JSON.

> [!TIP]
> Se si apre un ticket di [supporto tecnico di Azure](https://azure.microsoft.com/support/create-ticket/) per il connettore Azure per FHIR, assicurarsi di includere copie del mapping di conversione JSON per facilitare il processo di risoluzione dei problemi.

## <a name="next-steps"></a>Passaggi successivi

Vedere le domande frequenti sul connettore Azure per FHIR.

>[!div class="nextstepaction"]
>[Connettore Azure per le domande frequenti su FHIR](fhir-faq.md#azure-iot-connector-for-fhir-preview)

*Nel portale di Azure si fa riferimento al Connettore Azure IoT per FHIR come Connettore IoT (anteprima).

FHIR è il marchio registrato di HL7, usato con l'autorizzazione di HL7.
