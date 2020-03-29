---
title: Configurazione - Azure Event Grid IoT Edge Documenti Microsoft
description: Configurazione in Griglia di eventi su Edge Edge.Configuration in Event Grid on IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 841b5092775353bbe3340dbbd55610026f998a15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76846463"
---
# <a name="event-grid-configuration"></a>Configurazione griglia di eventi

Griglia di eventi fornisce molte configurazioni che possono essere modificate per ogni ambiente. La sezione seguente è un riferimento a tutte le opzioni disponibili e ai relativi valori predefiniti.

## <a name="tls-configuration"></a>Configurazione TLS

Per informazioni sull'autenticazione client in generale, vedere [Sicurezza e autenticazione](security-authentication.md). Esempi del suo utilizzo possono essere trovati in [questo articolo](configure-api-protocol.md).

| Nome proprietà | Descrizione |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| Criteri TLS del modulo Griglia di eventi. Il valore predefinito è solo HTTPS.
|`inbound__serverAuth__serverCert__source`| Origine del certificato server utilizzato dal modulo Griglia di eventi per la configurazione TLS. Il valore predefinito è IoT Edge.Default value is IoT Edge.

## <a name="incoming-client-authentication"></a>Autenticazione client in ingresso

Per informazioni sull'autenticazione client in generale, vedere [Sicurezza e autenticazione](security-authentication.md). Esempi possono essere trovati in [questo articolo](configure-client-auth.md).

| Nome proprietà | Descrizione |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| Per attivare/disattivare l'autenticazione client basata su certificati. Il valore predefinito è true.
|`inbound__clientAuth__clientCert__source`| Origine per la convalida dei certificati client. Il valore predefinito è IoT Edge.Default value is IoT Edge.
|`inbound__clientAuth__clientCert__allowUnknownCA`| Criterio per consentire un certificato client autofirmato. Il valore predefinito è true.
|`inbound__clientAuth__sasKeys__enabled`| Per attivare/disattivare l'autenticazione client basata su chiave di accesso utente. Il valore predefinito è disattivato.
|`inbound__clientAuth__sasKeys__key1`| Uno dei valori per convalidare le richieste in ingresso.
|`inbound__clientAuth__sasKeys__key2`| Secondo valore facoltativo per convalidare le richieste in ingresso.

## <a name="outgoing-client-authentication"></a>Autenticazione client in uscita
Per informazioni sull'autenticazione client in generale, vedere [Sicurezza e autenticazione](security-authentication.md). Esempi possono essere trovati in [questo articolo](configure-identity-auth.md).

| Nome proprietà | Descrizione |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| Per attivare/disattivare il collegamento di un certificato di identità per le richieste in uscita. Il valore predefinito è true.
|`outbound__clientAuth__clientCert__source`| Origine per il recupero del certificato in uscita del modulo Griglia di eventi. Il valore predefinito è IoT Edge.Default value is IoT Edge.

## <a name="webhook-event-handlers"></a>Gestori eventi Webhook

Per informazioni sull'autenticazione client in generale, vedere [Sicurezza e autenticazione](security-authentication.md). Esempi possono essere trovati in [questo articolo](configure-webhook-subscriber-auth.md).

| Nome proprietà | Descrizione |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| Criteri per controllare se saranno consentiti solo i sottoscrittori HTTPS. Il valore predefinito è true (solo HTTPS).
|`outbound__webhook__skipServerCertValidation`| Flag per controllare se convalidare il certificato del sottoscrittore. Il valore predefinito è true.
|`outbound__webhook__allowUnknownCA`| Criteri per controllare se un certificato autofirmato può essere presentato da un sottoscrittore. Il valore predefinito è true. 

## <a name="delivery-and-retry"></a>Recapito e nuovo tentativo

Per informazioni su questa funzionalità in generale, vedere [Recapito e ripetizione dei tentativi](delivery-retry.md).

| Nome proprietà | Descrizione |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Numero massimo di tentativi di recapito di un evento. Il valore predefinito è 30.
| `broker__defaultEventTimeToLiveInSeconds` | Durata (TTL) in pochi secondi dopo i quali un evento verrà eliminato se non viene recapitato. Il valore predefinito è **7200** secondi

## <a name="output-batching"></a>Suddivisione in batch per l'output

Per informazioni su questa funzionalità in generale, vedere [Invio in batch di recapito e output](delivery-output-batching.md).

| Nome proprietà | Descrizione |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | Valore massimo consentito per la `ApproxBatchSizeInBytes` manopola. Il valore predefinito è `1_058_576`.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Valore massimo consentito per la `MaxEventsPerBatch` manopola. Il valore predefinito è `50`.
| `broker__defaultMaxBatchSizeInBytes` | Dimensione massima della `MaxEventsPerBatch` richiesta di recapito quando viene specificata solo. Il valore predefinito è `1_058_576`.
| `broker__defaultMaxEventsPerBatch` | Numero massimo di eventi da aggiungere `MaxBatchSizeInBytes` a un batch quando viene specificato solo. Il valore predefinito è `10`.

## <a name="metrics"></a>Metriche

Per informazioni sull'uso delle metriche con Griglia di eventi in Edge IoT, vedere [Monitorare argomenti e sottoscrizioni](monitor-topics-subscriptions.md)

| Nome proprietà | Descrizione |
| ---------------- | ------------ |
| `metrics__reporterType` | Tipo di reporter per l'enpoint delle metriche. L'impostazione predefinita è `none` e disabilita le metriche. L'impostazione consente `prometheus` di abilitare le metriche nel formato Prometheus exposition.