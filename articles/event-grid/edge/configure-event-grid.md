---
title: Configurazione-IoT Edge griglia di eventi di Azure | Microsoft Docs
description: Configurazione in griglia di eventi in IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: a9bac9a84e1ba034b011691ae82a1cb67eb71af0
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992431"
---
# <a name="event-grid-configuration"></a>Configurazione griglia di eventi

Griglia di eventi offre molte configurazioni che possono essere modificate per ogni ambiente. La sezione seguente è un riferimento a tutte le opzioni disponibili e ai relativi valori predefiniti.

## <a name="tls-configuration"></a>Configurazione TLS

Per informazioni sull'autenticazione client in generale, vedere [sicurezza e autenticazione](security-authentication.md). Esempi di utilizzo sono disponibili in [questo articolo](configure-api-protocol.md).

| Nome proprietà | Descrizione |
| ---------------- | ------------ |
|`inbound:serverAuth:tlsPolicy`| Criteri TLS del modulo di griglia di eventi. Il valore predefinito è solo HTTPS.
|`inbound:serverAuth:serverCert:source`| Origine del certificato del server usato dal modulo di griglia di eventi per la relativa configurazione TLS. Il valore predefinito è IoT Edge.

## <a name="incoming-client-authentication"></a>Autenticazione client in ingresso

Per informazioni sull'autenticazione client in generale, vedere [sicurezza e autenticazione](security-authentication.md). Esempi sono disponibili in [questo articolo](configure-client-auth.md).

| Nome proprietà | Descrizione |
| ---------------- | ------------ |
|`inbound:clientAuth:clientCert:enabled`| Per attivare o disattivare l'autenticazione client basata su certificato. Il valore predefinito è true.
|`inbound:clientAuth:clientCert:source`| Origine per la convalida dei certificati client. Il valore predefinito è IoT Edge.
|`inbound:clientAuth:clientCert:allowUnknownCA`| Criteri per consentire un certificato client autofirmato. Il valore predefinito è true.
|`inbound:clientAuth:sasKeys:enabled`| Per attivare o disattivare l'autenticazione client basata sulla chiave SAS. Il valore predefinito è off.
|`inbound:clientAuth:sasKeys:key1`| Uno dei valori di per convalidare le richieste in ingresso.
|`inbound:clientAuth:sasKeys:key2`| Secondo valore facoltativo per convalidare le richieste in ingresso.

## <a name="outgoing-client-authentication"></a>Autenticazione client in uscita
Per informazioni sull'autenticazione client in generale, vedere [sicurezza e autenticazione](security-authentication.md). Esempi sono disponibili in [questo articolo](configure-identity-auth.md).

| Nome proprietà | Descrizione |
| ---------------- | ------------ |
|`outbound:clientAuth:clientCert:enabled`| Per attivare/disattivare l'associazione di un certificato di identità per le richieste in uscita. Il valore predefinito è true.
|`outbound:clientAuth:clientCert:source`| Origine per il recupero del certificato in uscita del modulo di griglia di eventi. Il valore predefinito è IoT Edge.

## <a name="webhook-event-handlers"></a>Gestori eventi webhook

Per informazioni sull'autenticazione client in generale, vedere [sicurezza e autenticazione](security-authentication.md). Esempi sono disponibili in [questo articolo](configure-webhook-subscriber-auth.md).

| Nome proprietà | Descrizione |
| ---------------- | ------------ |
|`outbound:webhook:httpsOnly`| Criteri per controllare se saranno consentiti solo i sottoscrittori HTTPS. Il valore predefinito è true (solo HTTPS).
|`outbound:webhook:skipServerCertValidation`| Flag per controllare se convalidare il certificato del Sottoscrittore. Il valore predefinito è true.
|`outbound:webhook:allowUnknownCA`| Criteri per controllare se un certificato autofirmato può essere presentato da un Sottoscrittore. Il valore predefinito è true. 

## <a name="delivery-and-retry"></a>Recapito e nuovo tentativo

Per informazioni generali su questa funzionalità, vedere [recapito e riprovare](delivery-retry.md).

| Nome proprietà | Descrizione |
| ---------------- | ------------ |
| `broker:defaultMaxDeliveryAttempts` | Numero massimo di tentativi di recapitare un evento. Il valore predefinito è 30.
| `broker:defaultEventTimeToLiveInSeconds` | Time-to-Live (TTL) in secondi dopo il quale un evento viene eliminato se non viene recapitato. Il valore predefinito è **7200** secondi

## <a name="output-batching"></a>Batch di output

Per informazioni generali su questa funzionalità, vedere [invio in batch e output in batch](delivery-output-batching.md).

| Nome proprietà | Descrizione |
| ---------------- | ------------ |
| `api:deliveryPolicyLimits:maxBatchSizeInBytes` | Valore massimo consentito per la manopola `ApproxBatchSizeInBytes`. Il valore predefinito è `1_058_576`.
| `api:deliveryPolicyLimits:maxEventsPerBatch` | Valore massimo consentito per la manopola `MaxEventsPerBatch`. Il valore predefinito è `50`.
| `broker:defaultMaxBatchSizeInBytes` | Dimensioni massime della richiesta di recapito quando si specifica solo `MaxEventsPerBatch`. Il valore predefinito è `1_058_576`.
| `broker:defaultMaxEventsPerBatch` | Numero massimo di eventi da aggiungere a un batch quando viene specificato solo `MaxBatchSizeInBytes`. Il valore predefinito è `10`.
