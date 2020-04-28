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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78183976"
---
# <a name="page-layout-versions"></a>Versioni del layout di pagina

I pacchetti del layout di pagina vengono aggiornati periodicamente per includere correzioni e miglioramenti negli elementi della pagina. Nel log delle modifiche seguente vengono specificate le modifiche introdotte in ogni versione.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="200"></a>2.0.0

- Pagina autocertificata (`selfasserted`)
  - Aggiunta del supporto per la [visualizzazione dei controlli](display-controls.md) nei criteri personalizzati.

## <a name="120"></a>1.2.0

- Tutte le pagine
  -  Correzioni di accessibilità
  - È ora possibile aggiungere l' `data-preload="true"` attributo [nei tag HTML](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) per controllare l'ordine di caricamento per CSS e JavaScript.
    - Caricare i file CSS collegati allo stesso tempo del modello HTML in modo che non venga "sfarfallio" tra il caricamento dei file.
    - Controllare l'ordine in cui i `script` tag vengono recuperati ed eseguiti prima del caricamento della pagina.
  - Il campo posta elettronica `type=email` è ora e le tastiere per dispositivi mobili forniranno i suggerimenti corretti
  - Supporto per la conversione di Chrome
- Pagine unificate e autocertificate
  - I campi nome utente/indirizzo di posta elettronica e `form` password ora usano l'elemento HTML per consentire a Edge e Internet Explorer di salvare correttamente queste informazioni.

## <a name="110"></a>1.1.0

- Pagina eccezione (globalexception)
  - Correzione accessibilità
  - Rimosso il messaggio predefinito quando non è presente alcun contatto dal criterio
  - CSS predefinito rimosso
- Pagina multi-factor authentication (a più fattori)
  - Pulsante ' conferma codice ' rimosso
  - Il campo di input per il codice ora accetta solo un input composto da sei (6) caratteri
  - La pagina tenterà automaticamente di verificare il codice immesso quando viene immesso un codice di 6 cifre, senza che sia necessario fare clic su un pulsante
  - Se il codice è errato, il campo di input viene cancellato automaticamente
  - Dopo tre (3) tentativi con codice errato, B2C Invia un errore all'relying party
  -  Correzioni di accessibilità
  - CSS predefinito rimosso
- Pagina autocertificata (selfasserted)
  - Rimosso avviso di annullamento
  - Classe CSS per gli elementi error
  - Mostra/Nascondi la logica di errore migliorata
  - CSS predefinito rimosso
- SSP unificato (unifiedssp)
  - Aggiunto il controllo Mantieni l'accesso (KMSI)

## <a name="100"></a>1.0.0

- Versione iniziale

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate su come personalizzare l'interfaccia utente delle applicazioni in criteri personalizzati, vedere [personalizzare l'interfaccia utente dell'applicazione usando un criterio personalizzato](custom-policy-ui-customization.md).
