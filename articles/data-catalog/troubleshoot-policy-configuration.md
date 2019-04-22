---
title: Come configurare i criteri di Azure Active Directory per Azure Data Catalog
description: Si potrebbe verificarsi una situazione in cui è possibile accedere al portale di Azure Data Catalog, ma quando si prova ad accedere allo strumento di registrazione dell'origine dati, viene visualizzato un messaggio di errore.
author: markingmyname
ms.author: maghan
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/06/2019
ms.openlocfilehash: 558f8845f5469bf157188e20f1ec65a07ff8355f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59363140"
---
# <a name="azure-active-directory-policy-configuration"></a>Configurazione dei criteri di Azure Active Directory

In alcune situazioni è possibile accedere al portale di Azure Data Catalog, ma quando si prova ad accedere allo strumento di registrazione dell'origine dati viene visualizzato un messaggio di errore che impedisce l'accesso. Questo errore può verificarsi quando si usa la rete aziendale o quando ci si connette dall'esterno della rete aziendale.

## <a name="registration-tool"></a>Strumento di registrazione

Lo strumento di registrazione usa l' *autenticazione basata su form* per convalidare l'accesso utente in Azure Active Directory. Per completare l'accesso, un amministratore di Azure Active Directory deve abilitare l'autenticazione basata su form nei *criteri di autenticazione globali*.

I criteri di autenticazione globali consentono di abilitare l'autenticazione separatamente per connessioni Intranet ed Extranet, come illustrato nell'immagine seguente. Se l'autenticazione basata su form non è abilitato per la rete da cui ci si connette, potrebbero verificarsi errori di accesso.

 ![Criteri di autenticazione globali di Azure Active Directory](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Per altre informazioni, vedere [Configurazione dei criteri di autenticazione](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Passaggi successivi

* [Creare una Azure Data Catalog](data-catalog-get-started.md)