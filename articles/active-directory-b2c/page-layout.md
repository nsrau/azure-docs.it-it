---
title: Versioni del layout di pagina
titleSuffix: Azure AD B2C
description: Cronologia delle versioni del layout di pagina per la personalizzazione dell'interfaccia utente nei criteri personalizzati.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3d0cb06f84fdd96d099e05f55ba62c37cb1192c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183976"
---
# <a name="page-layout-versions"></a>Versioni del layout di pagina

I pacchetti di layout di pagina vengono aggiornati periodicamente per includere correzioni e miglioramenti nei relativi elementi di pagina. Il registro delle modifiche seguente specifica le modifiche introdotte in ogni versione.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="200"></a>2.0.0

- Pagina auto-asserita (`selfasserted`)
  - Aggiunto il supporto per [i controlli](display-controls.md) di visualizzazione nei criteri personalizzati.

## <a name="120"></a>1.2.0

- Tutte le pagine
  -  Correzioni di accessibilità
  - È ora possibile `data-preload="true"` aggiungere l'attributo [nei tag HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) per controllare l'ordine di caricamento per CSS e JavaScript.
    - Caricare i file CSS collegati contemporaneamente al modello HTML in modo che non 'flicker' tra il caricamento dei file.
    - Controllare l'ordine `script` in cui i tag vengono recuperati ed eseguiti prima del caricamento della pagina.
  - Il campo `type=email` E-mail è ora e le tastiere mobili forniranno i suggerimenti corretti
  - Supporto per Chrome translate
- Pagine unificate e auto-assediate
  - I campi nome utente/e-mail e password ora utilizzano l'elemento `form` HTML per consentire a Edge e Internet Explorer (IE) di salvare correttamente queste informazioni.

## <a name="110"></a>1.1.0

- Pagina Eccezione (globalexception)
  - Correzione di accessibilità
  - Rimosso il messaggio predefinito quando non è presente alcun contatto dal criterio
  - CSS predefinito rimosso
- Pagina MFA (multifattore)
  - Pulsante 'Conferma codice' rimosso
  - Il campo di immissione per il codice ora accetta solo l'input fino a sei (6) caratteri
  - La pagina tenterà automaticamente di verificare il codice inserito quando viene inserito un codice a 6 cifre, senza che sia necessario fare clic su alcun pulsante
  - Se il codice è errato, il campo di immissione viene cancellato automaticamente
  - Dopo tre (3) tentativi con un codice non corretto, B2C invia un errore alla relying party
  -  Correzioni di accessibilità
  - CSS predefinito rimosso
- Pagina auto-assediata (autoassediata)
  - Rimosso l'avviso di annullamento
  - Classe CSS per gli elementi di errore
  - Mostra/nascondi logica di errore migliorata
  - CSS predefinito rimosso
- Unified SSP (unifiedssp)
  - Aggiunto il controllo Mantieni l'accesso (KMSI)

## <a name="100"></a>1.0.0

- Versione iniziale

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate su come personalizzare l'interfaccia utente delle applicazioni nei criteri personalizzati, vedere [Personalizzare l'interfaccia utente dell'applicazione utilizzando criteri personalizzati.](custom-policy-ui-customization.md)
