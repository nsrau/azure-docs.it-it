---
title: Selezionare un contratto di pagina in Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come selezionare un contratto di pagina in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: fa48d3825b5b942497b1eabd19a1eeb7e0141058
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003143"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>Selezionare un contratto di pagina in Azure Active Directory B2C tramite criteri personalizzati

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

È possibile abilitare il codice lato client JavaScript nei criteri di Azure Active Directory (Azure AD) B2C, indipendentemente dal fatto che si usino flussi utente o criteri personalizzati. Questo articolo illustra come selezionare un contratto di pagina in Azure AD B2C configurandolo in [criteri personalizzati](active-directory-b2c-overview-custom.md). Un contratto di pagina è un'associazione di elementi forniti da Azure AD B2C e del contenuto fornito dall'utente. Se si prevede di usare [JavaScript](javascript-samples.md), è necessario definire una versione del contratto di pagina per tutte le definizioni del contenuto nei criteri personalizzati.

> [!NOTE]
> Se si vuole abilitare JavaScript per i flussi utente, vedere [Informazioni sull'uso di JavaScript e sulle versioni del contratto di pagina in un flusso utente](user-flow-javascript-overview.md).

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

Per selezionare un contratto di pagina, è necessario modificare i valori di **DataUri** negli elementi [ContentDefinition](contentdefinitions.md) nei criteri. Passando dai valori di **DataUri** precedenti a quelli nuovi, si seleziona un pacchetto non modificabile. Il vantaggio dell'uso di questo pacchetto è la certezza che non cambierà e non provocherà un comportamento imprevisto nella pagina. 

Per configurare un contratto di pagina, usare la tabella seguente per trovare i valori di **DataUri**. 

| Valore di DataUri precedente | Nuovo valore di DataUri |
| ----------------- | ----------------- |
| urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0 | 
| urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0 |
| urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0 | 
| urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0 |
| urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0 |

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come personalizzare l'interfaccia utente delle applicazioni sono disponibili nell'argomento [Personalizzare l'interfaccia utente dell'applicazione usando un criterio personalizzato in Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).



