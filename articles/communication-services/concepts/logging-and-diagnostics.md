---
title: Log di Servizi di comunicazione
titleSuffix: An Azure Communication Services concept document
description: Informazioni sulla registrazione in Servizi di comunicazione di Azure
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/15/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: aad4cdfe38ee9dd7530cb8ebe21cded18cb0a1ec
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92128627"
---
# <a name="communication-services-logs"></a>Log di Servizi di comunicazione

Servizi di comunicazione di Azure offre funzionalità di registrazione che è possibile usare per monitorare ed eseguire il debug della soluzione. Queste funzionalità possono essere configurate tramite il portale di Azure.

## <a name="enable-diagnostic-logs-in-your-resource"></a>Abilitare i log di diagnostica nella risorsa

Quando viene creata una risorsa, per impostazione predefinita la registrazione è disattivata. Per abilitare la registrazione, passare al pannello **Impostazioni di diagnostica** nel menu delle risorse sotto la sezione **Monitoraggio** . Quindi fare clic su **Aggiungi impostazione di diagnostica** .

Successivamente, selezionare l'archivio di destinazione. Attualmente sono supportati gli account di archiviazione e Log Analytics come archivi di destinazione. Dopo aver selezionato i tipi di log da acquisire, salvare le impostazioni di diagnostica.
 
Le nuove impostazioni diventano effettive dopo circa 10 minuti. I log inizieranno a essere visualizzati nell'archivio di destinazione configurato all'interno del riquadro Log della risorsa di Servizi di comunicazione.

:::image type="content" source="./media/diagnostic-settings.png" alt-text="Opzioni delle impostazioni di diagnostica di Servizi di comunicazione di Azure.":::

Per altre informazioni sulla configurazione della diagnostica, vedere la panoramica dei [log delle risorse di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview).

## <a name="resource-log-categories"></a>Categorie di log delle risorse

Servizi di comunicazione offre tre tipi di log che è possibile abilitare:

* **Log di utilizzo** : forniscono i dati di utilizzo associati a ogni offerta di servizio fatturata
* **Log operativi delle chat** : forniscono informazioni di base relative al servizio chat
* **Log operativi degli SMS** : forniscono informazioni di base relative al servizio SMS

### <a name="usage-logs-schema"></a>Schema dei log di utilizzo

| Proprietà | Descrizione |
| -------- | ---------------|
| Timestamp | Il timestamp (UTC) relativo alla data e all'ora in cui è stato generato il log. |
| Nome operazione | L'operazione associata al record del log. |
| Versione dell'operazione | L'oggetto `api-version` associato all'operazione se operationName è stato eseguito con un'API. Se non esiste un'API corrispondente a questa operazione, la versione rappresenta la versione di tale operazione nel caso in cui le proprietà associate all'operazione cambino in futuro. |
| Category | La categoria di log dell'evento. La categoria è la granularità con cui è possibile abilitare o disabilitare i log di una particolare risorsa. Le proprietà che appaiono all'interno del BLOB delle proprietà di un evento sono le stesse all'interno di una particolare categoria di log e tipo di risorsa. |
| ID correlazione | L'ID degli eventi correlati. Può essere usato per identificare eventi correlati tra più tabelle. |
| Proprietà | Altri dati applicabili a varie modalità di Servizi di comunicazione. |
| ID record | L'ID univoco di un record di utilizzo specificato. |
| Usage Type | La modalità di utilizzo. Ad esempio Chat, PSTN, NAT a così via. |
| Tipo di unità | Il tipo di unità su cui è basato l'utilizzo per una specifica modalità, ad esempio minuti, megabyte, messaggi e così via. |
| Quantità | Il numero di unità usate o consumate per questo record. |

### <a name="chat-operational-logs"></a>Log operativi delle chat

| Proprietà | Descrizione |
| -------- | ---------------|
| TimeGenerated | Il timestamp (UTC) relativo alla data e all'ora in cui è stato generato il log. |
| OperationName | L'operazione associata al record del log. |
| CorrelationID | L'ID degli eventi correlati. Può essere usato per identificare eventi correlati tra più tabelle. |
| OperationVersion | L'oggetto api-version associato all'operazione, se operationName è stato eseguito con un'API. Se non esiste un'API corrispondente a questa operazione, la versione rappresenta la versione di tale operazione nel caso in cui le proprietà associate all'operazione cambino in futuro. |
| Category | La categoria di log dell'evento. La categoria è la granularità con cui è possibile abilitare o disabilitare i log di una particolare risorsa. Le proprietà che appaiono all'interno del BLOB delle proprietà di un evento sono le stesse all'interno di una particolare categoria di log e tipo di risorsa. |
| ResultType | Stato dell'operazione. |
| ResultSignature | Il sottostato dell'operazione. Se questa operazione corrisponde a una chiamata API REST, questo campo contiene il codice di stato HTTP della chiamata REST corrispondente. |
| ResultDescription | La descrizione in testo statico di questa operazione. |
| DurationMs | La durata dell'operazione in millisecondi. |
| CallerIpAddress | L'indirizzo IP del chiamante, se l'operazione corrisponde a una chiamata API proveniente da un'entità con un indirizzo IP accessibile pubblicamente. |
| Level | Il livello di gravità dell'evento. |
| URI | URI della richiesta. |
| UserId | L'ID utente del mittente della richiesta. |
| ChatThreadId | L'ID thread della chat associato alla richiesta. |
| ChatMessageId | L'ID messaggio della chat associato alla richiesta. |
| SdkType | Il tipo di SDK usato nella richiesta. |
| PlatformType | Il tipo di piattaforma usato nella richiesta. |

### <a name="sms-operational-logs"></a>Log operativi degli SMS

| Proprietà | Descrizione |
| -------- | ---------------|
| TimeGenerated | Il timestamp (UTC) relativo alla data e all'ora in cui è stato generato il log. |
| OperationName | L'operazione associata al record del log. |
| CorrelationID | L'ID degli eventi correlati. Può essere usato per identificare eventi correlati tra più tabelle. |
| OperationVersion | L'oggetto api-version associato all'operazione, se operationName è stato eseguito con un'API. Se non esiste un'API corrispondente a questa operazione, la versione rappresenta la versione di tale operazione nel caso in cui le proprietà associate all'operazione cambino in futuro. |
| Category | La categoria di log dell'evento. La categoria è la granularità con cui è possibile abilitare o disabilitare i log di una particolare risorsa. Le proprietà che appaiono all'interno del BLOB delle proprietà di un evento sono le stesse all'interno di una particolare categoria di log e tipo di risorsa. |
| ResultType | Stato dell'operazione. |
| ResultSignature | Il sottostato dell'operazione. Se questa operazione corrisponde a una chiamata API REST, questo campo contiene il codice di stato HTTP della chiamata REST corrispondente. |
| ResultDescription | La descrizione in testo statico di questa operazione. |
| DurationMs | La durata dell'operazione in millisecondi. |
| CallerIpAddress | L'indirizzo IP del chiamante, se l'operazione corrisponde a una chiamata API proveniente da un'entità con un indirizzo IP accessibile pubblicamente. |
| Level | Il livello di gravità dell'evento. |
| URI | URI della richiesta. |
| OutgoingMessageLength | Il numero di caratteri del messaggio in uscita. |
| IncomingMessageLength | Il numero di caratteri del messaggio in ingresso. |
| DeliveryAttempts | Il numero di tentativi effettuati per recapitare questo messaggio. |
| PhoneNumber | Il numero di telefono a cui viene inviato il messaggio SMS. |
| SdkType | Il tipo di SDK usato nella richiesta. |
| PlatformType | Il tipo di piattaforma usato nella richiesta. |
| Metodo | Il metodo usato nella richiesta. |
