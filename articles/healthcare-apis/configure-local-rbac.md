---
title: Configurare il controllo degli accessi in base al ruolo locale (RBAC) per l'API di Azure per FHIR
description: Questo articolo descrive come configurare l'API di Azure per FHIR per usare un tenant di Azure AD esterno per il piano dati
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: mihansen
ms.openlocfilehash: a8c1b36d6a439297dfb0bbcb34efe059349fc5a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871161"
---
# <a name="configure-local-rbac-for-fhir"></a>Configurare RBAC locale per FHIR 

Questo articolo illustra come configurare l'API di Azure per FHIR per usare un tenant esterno, secondario Azure Active Directory per la gestione dell'accesso al piano dati. Usare questa modalità solo se non è possibile usare il tenant Azure Active Directory associato alla sottoscrizione.

> [!NOTE]
> Se il piano dati del servizio FHIR è configurato per usare il tenant di Azure Active Directory primario associato alla sottoscrizione, usare il controllo degli accessi [in base al ruolo di Azure per assegnare i ruoli del piano dati](configure-azure-rbac.md).

## <a name="add-service-principal"></a>Aggiunta di un'entità servizio

Il controllo degli accessi in base al ruolo locale consente di usare un tenant di Azure Active Directory esterno con il server FHIR. Per consentire al sistema RBAC di controllare l'appartenenza a gruppi nel tenant, l'API di Azure per FHIR deve avere un'entità servizio nel tenant. Questa entità servizio verrà creata automaticamente nei tenant collegati alle sottoscrizioni che hanno distribuito l'API di Azure per FHIR, ma nel caso in cui il tenant non disponga di una sottoscrizione associata, un amministratore tenant dovrà creare questa entità servizio con uno dei comandi seguenti:

Uso del `Az` modulo PowerShell:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

in alternativa, è possibile usare il `AzureAd` modulo di PowerShell:

```azurepowershell-interactive
New-AzureADServicePrincipal -AppId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

oppure usare l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az ad sp create --id 3274406e-4e0a-4852-ba4f-d7226630abb7
```

## <a name="configure-local-rbac"></a>Configura RBAC locale

È possibile configurare l'API di Azure per FHIR per usare un tenant di Azure Active Directory esterno o secondario nel pannello **autenticazione** :

![Assegnazioni RBAC locali](media/rbac/local-rbac-guids.png).

Nella casella autorità immettere un tenant di Azure Active Directory valido. Una volta che il tenant è stato convalidato, la casella **ID oggetto consentiti** deve essere attivata ed è possibile immettere un elenco di ID oggetto identità. Questi ID possono essere gli ID oggetto identità di:

* Utente Azure Active Directory.
* Un'entità servizio di Azure Active Directory.
* Un gruppo di sicurezza di Azure Active Directory.

Per informazioni dettagliate, vedere l'articolo su come [trovare gli ID oggetto Identity](find-identity-object-ids.md) .

Dopo aver immesso gli ID oggetto necessari, fare clic su **Salva** e attendere il salvataggio delle modifiche prima di provare ad accedere al piano dati usando gli utenti, le entità servizio o i gruppi assegnati.

## <a name="caching-behavior"></a>Comportamento di memorizzazione nella cache

L'API di Azure per FHIR memorizza nella cache le decisioni per un massimo di 5 minuti. Se si concede a un utente l'accesso al server FHIR aggiungendoli all'elenco degli ID oggetto consentiti o se vengono rimossi dall'elenco, è necessario attendere fino a cinque minuti per la propagazione delle modifiche nelle autorizzazioni.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come assegnare l'accesso al piano dati FHIR usando un tenant esterno (secondario) Azure Active Directory. Ulteriori informazioni sulle impostazioni aggiuntive per l'API di Azure per FHIR:
 
>[!div class="nextstepaction"]
>[Impostazioni aggiuntive API di Azure per FHIR](azure-api-for-fhir-additional-settings.md)

