---
title: Selezionare un layout di pagina-Azure Active Directory B2C
description: Informazioni su come selezionare un layout di pagina in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 612d2e3a9a5a324f7d6d8e1b63b6b7e297047239
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063847"
---
# <a name="select-a-page-layout-in-azure-active-directory-b2c-using-custom-policies"></a>Selezionare un layout di pagina in Azure Active Directory B2C usando criteri personalizzati

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

È possibile abilitare il codice lato client JavaScript nei criteri di Azure Active Directory B2C (Azure AD B2C) se si usano flussi utente o criteri personalizzati. Per abilitare JavaScript per le applicazioni, è necessario aggiungere un elemento ai [criteri personalizzati](active-directory-b2c-overview-custom.md), selezionare un layout di pagina e usare [b2clogin.com](b2clogin.md) nelle richieste.

Un layout di pagina è un'associazione di elementi che Azure AD B2C fornisce e il contenuto fornito.

Questo articolo illustra come selezionare un layout di pagina in Azure AD B2C tramite la configurazione in un criterio personalizzato.

> [!NOTE]
> Se si vuole abilitare JavaScript per i flussi utente, vedere la pagina relativa alle [versioni del layout di pagina e JavaScript in Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="replace-datauri-values"></a>Sostituire i valori di DataUri

Nei criteri personalizzati possono essere inclusi elementi [ContentDefinition](contentdefinitions.md) che definiscono i modelli HTML usati nel percorso utente. L'elemento **ContentDefinition** contiene un elemento **DataUri** che fa riferimento agli elementi della pagina forniti da Azure AD B2C. L'elemento **LoadUri** è il percorso relativo del contenuto HTML e CSS specificato dall'utente.

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

Per selezionare un layout di pagina, è necessario modificare i valori di **DataUri** in [ContentDefinitions](contentdefinitions.md) nei criteri. Passando dai valori di **DataUri** precedenti a quelli nuovi, si seleziona un pacchetto non modificabile. Il vantaggio dell'uso di questo pacchetto è la certezza che non cambierà e non provocherà un comportamento imprevisto nella pagina.

Per configurare un layout di pagina, usare la tabella seguente per trovare i valori **DataUri** .

| Valore di DataUri precedente | Nuovo valore di DataUri |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0` |

## <a name="version-change-log"></a>Log delle modifiche della versione

I pacchetti del layout di pagina vengono aggiornati periodicamente per includere correzioni e miglioramenti negli elementi della pagina. Nel log delle modifiche seguente vengono specificate le modifiche introdotte in ogni versione.

### <a name="110"></a>1.1.0

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
  - Correzioni di accessibilità
  - CSS predefinito rimosso
- Pagina autocertificata (selfasserted)
  - Rimosso avviso di annullamento
  - Classe CSS per gli elementi error
  - Mostra/Nascondi la logica di errore migliorata
  - CSS predefinito rimosso
- SSP unificato (unifiedssp)
  - Aggiunto il controllo Mantieni l'accesso (KMSI)

### <a name="100"></a>1.0.0

- Versione iniziale

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come personalizzare l'interfaccia utente delle applicazioni sono disponibili nell'argomento [Personalizzare l'interfaccia utente dell'applicazione usando un criterio personalizzato in Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
