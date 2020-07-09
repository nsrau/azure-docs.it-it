---
title: Informazioni di riferimento sulle API di portale Cloud Partner-Microsoft Commercial Marketplace
description: Descrizione dei prerequisiti per usare ed elencare le operazioni all'API marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 56594b41bd0b771d0cb9a2f49611d9b6c6d393db
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86113582"
---
# <a name="cloud-partner-portal-api-reference"></a>Riferimento all'API del portale Cloud Partner

> [!NOTE]
> Le API del portale Cloud Partner sono integrate con il Centro per i partner e continueranno a funzionare dopo la migrazione delle offerte in esso. L'integrazione introduce piccole modifiche. Esaminare le [modifiche apportate alle API cpp](#changes-to-cpp-apis-after-the-migration-to-partner-center) elencate in questo documento per assicurarsi che il codice continui a funzionare dopo la migrazione al centro per i partner.

Le API REST del portale Cloud Partner consentono il recupero programmatico e la manipolazione di carichi di lavoro, offerte e profili di pubblicazione. Le API usano il controllo degli accessi in base al ruolo (RBAC) per applicare le autorizzazioni corrette in fase di elaborazione.

Questo riferimento fornisce i dettagli tecnici per le API REST del portale Cloud Partner. Gli esempi di payload in questo documento sono solo di riferimento e sono soggetti a modifiche in seguito all'aggiunta di nuove funzionalità.

## <a name="prerequisites-and-considerations"></a>Prerequisiti e considerazioni

Prima di usare le API, è opportuno rivedere:

- L'articolo [Prerequisiti](./cloud-partner-portal-api-prerequisites.md) per informazioni su come aggiungere un'entità servizio al proprio account e ottenere un token di accesso di Azure Active Directory (Azure AD) per l'autenticazione.
- Le due strategie di [controllo della concorrenza](./cloud-partner-portal-api-concurrency-control.md) disponibili per la chiamata di queste API.
- API aggiuntiva [Considerazioni](./cloud-partner-portal-api-considerations.md), ad esempio il controllo delle versioni e la gestione degli errori.

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>Modifiche alle API CPP dopo la migrazione al centro per i partner

| **API** | **Descrizione modifica** | **Impatto** |
| ------- | ---------------------- | ---------- |
| Pubblicazione POST, GoLive, annullamento | Per le offerte migrate, l'intestazione della risposta avrà un formato diverso ma continuerà a funzionare allo stesso modo, indicando un percorso relativo per recuperare lo stato dell'operazione. | Quando si inviano richieste POST corrispondenti per un'offerta, l'intestazione Percorso avrà uno dei due formati seguenti, a seconda dello stato di migrazione dell'offerta:<ul><li>Offerte non migrate<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Offerte migrate<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| Operazione GET | Per i tipi di offerta che in precedenza supportano il campo ' Notification-email ' nella risposta, questo campo sarà deprecato e non verrà più restituito per le offerte migrate. | Per le offerte migrate, non verranno più inviate notifiche all'elenco dei messaggi di posta elettronica specificati nelle richieste. Al contrario, il servizio API si allineerà con il processo di posta elettronica di notifica nel Centro per i partner per inviare messaggi di posta elettronica. In particolare, le notifiche verranno inviate all'indirizzo di posta elettronica impostato nella sezione Informazioni account nel Centro per i partner, per notificare l'avanzamento dell'operazione.<br><br>Per assicurarsi che venga fornita la posta elettronica corretta per le notifiche, vedere l'indirizzo di posta elettronica indicato nella sezione informazioni di contatto del venditore delle [impostazioni dell'account](https://partner.microsoft.com/dashboard/account/management) nel centro per i partner.  |

## <a name="common-tasks"></a>Attività comuni

Questo riferimento illustra nel dettaglio le API per eseguire le seguenti attività comuni.

### <a name="offers"></a>Offerte

- [Recuperare tutte le offerte](./cloud-partner-portal-api-retrieve-offers.md)
- [Recupera un'offerta specifica](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [Recupero dello stato dell'offerta](./cloud-partner-portal-api-retrieve-offer-status.md)
- [Creare un'offerta](./cloud-partner-portal-api-creating-offer.md)
- [Pubblicare un'offerta](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Operazioni

- [Recuperare le operazioni](./cloud-partner-portal-api-retrieve-operations.md)
- [Annullare le operazioni](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Pubblicare un'app

- [Passare in produzione](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Altre attività

- [Impostare i prezzi per le offerte di macchine virtuali](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Risoluzione dei problemi

- [Risoluzione dei problemi di autenticazione](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
