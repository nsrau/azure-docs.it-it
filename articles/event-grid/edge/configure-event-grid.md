---
title: Configurazione-IoT Edge griglia di eventi di Azure | Microsoft Docs
description: Configurazione in griglia di eventi in IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 632227579fd021a0d2ce1d0b1bb0b8a8288c5f47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171670"
---
# <a name="event-grid-configuration"></a>Configurazione griglia di eventi

Griglia di eventi offre molte configurazioni che possono essere modificate per ogni ambiente. La sezione seguente è un riferimento a tutte le opzioni disponibili e ai relativi valori predefiniti.

## <a name="tls-configuration"></a>Configurazione TLS

Per informazioni sull'autenticazione client in generale, vedere [sicurezza e autenticazione](security-authentication.md). Esempi di utilizzo sono disponibili in [questo articolo](configure-api-protocol.md).

| Nome proprietà | Descrizione |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| Criteri TLS del modulo di griglia di eventi. Il valore predefinito è solo HTTPS.
|`inbound__serverAuth__serverCert__source`| Origine del certificato del server usato dal modulo di griglia di eventi per la relativa configurazione TLS. Il valore predefinito è IoT Edge.

## <a name="incoming-client-authentication"></a>Autenticazione client in ingresso

Per informazioni sull'autenticazione client in generale, vedere [sicurezza e autenticazione](security-authentication.md). Esempi sono disponibili in [questo articolo](configure-client-auth.md).

| Nome proprietà | Descrizione |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| Per attivare o disattivare l'autenticazione client basata su certificato. Il valore predefinito è true.
|`inbound__clientAuth__clientCert__source`| Origine per la convalida dei certificati client. Il valore predefinito è IoT Edge.
|`inbound__clientAuth__clientCert__allowUnknownCA`| Criteri per consentire un certificato client autofirmato. Il valore predefinito è true.
|`inbound__clientAuth__sasKeys__enabled`| Per attivare o disattivare l'autenticazione client basata sulla chiave SAS. Il valore predefinito è off.
|`inbound__clientAuth__sasKeys__key1`| Uno dei valori di per convalidare le richieste in ingresso.
|`inbound__clientAuth__sasKeys__key2`| Secondo valore facoltativo per convalidare le richieste in ingresso.

## <a name="outgoing-client-authentication"></a>Autenticazione client in uscita
Per informazioni sull'autenticazione client in generale, vedere [sicurezza e autenticazione](security-authentication.md). Esempi sono disponibili in [questo articolo](configure-identity-auth.md).

| Nome proprietà | Descrizione |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| Per attivare/disattivare l'associazione di un certificato di identità per le richieste in uscita. Il valore predefinito è true.
|`outbound__clientAuth__clientCert__source`| Origine per il recupero del certificato in uscita del modulo di griglia di eventi. Il valore predefinito è IoT Edge.

## <a name="webhook-event-handlers"></a>Gestori eventi webhook

Per informazioni sull'autenticazione client in generale, vedere [sicurezza e autenticazione](security-authentication.md). Esempi sono disponibili in [questo articolo](configure-webhook-subscriber-auth.md).

| Nome proprietà | Descrizione |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| Criteri per controllare se saranno consentiti solo i sottoscrittori HTTPS. Il valore predefinito è true (solo HTTPS).
|`outbound__webhook__skipServerCertValidation`| Flag per controllare se convalidare il certificato del Sottoscrittore. Il valore predefinito è true.
|`outbound__webhook__allowUnknownCA`| Criteri per controllare se un certificato autofirmato può essere presentato da un Sottoscrittore. Il valore predefinito è true. 

## <a name="delivery-and-retry"></a>Recapito e nuovo tentativo

Per informazioni generali su questa funzionalità, vedere [recapito e riprovare](delivery-retry.md).

| Nome proprietà | Descrizione |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Numero massimo di tentativi di recapitare un evento. Il valore predefinito è 30.
| `broker__defaultEventTimeToLiveInSeconds` | Time-to-Live (TTL) in secondi dopo il quale un evento viene eliminato se non viene recapitato. Il valore predefinito è  **7200** secondi

## <a name="output-batching"></a>Suddivisione in batch per l'output

Per informazioni generali su questa funzionalità, vedere [invio in batch e output in batch](delivery-output-batching.md).

| Nome proprietà | Descrizione |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | Valore massimo consentito per la `ApproxBatchSizeInBytes` manopola. Il valore predefinito è `1_058_576`.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Valore massimo consentito per la `MaxEventsPerBatch` manopola. Il valore predefinito è `50`.
| `broker__defaultMaxBatchSizeInBytes` | Dimensioni massime della richiesta di recapito quando `MaxEventsPerBatch` si specifica solo. Il valore predefinito è `1_058_576`.
| `broker__defaultMaxEventsPerBatch` | Numero massimo di eventi da aggiungere a un batch quando `MaxBatchSizeInBytes` si specifica solo. Il valore predefinito è `10`.

## <a name="metrics"></a>Metriche

Per informazioni sull'uso delle metriche con griglia di eventi in IoT Edge, vedere [monitorare gli argomenti e le sottoscrizioni](monitor-topics-subscriptions.md)

| Nome proprietà | Descrizione |
| ---------------- | ------------ |
| `metrics__reporterType` | Tipo di report per l'endpoint della metrica. Il valore predefinito è `none` e Disabilita la metrica. L'impostazione di su `prometheus` Abilita le metriche nel formato di esposizione Prometeo.