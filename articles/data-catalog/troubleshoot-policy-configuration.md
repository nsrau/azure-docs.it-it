---
title: Come risolvere i problemi di Azure Data Catalog
description: Questo articolo descrive i problemi di risoluzione dei problemi comuni per le risorse di Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 06/13/2019
ms.openlocfilehash: ed74e90e5e8ed55b75968f51cb50e6a1b4cdd75d
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203497"
---
# <a name="troubleshooting-azure-data-catalog"></a>Risoluzione dei problemi di Azure Data Catalog

Questo articolo descrive i problemi di risoluzione dei problemi comuni per le risorse di Azure Data Catalog. 

## <a name="functionality-limitations"></a>Limitazioni di funzionalità

Quando si usa Azure Data Catalog, sono limitata le funzionalità seguenti:

- Gli account con il tipo **ruolo Guest** non sono supportati. Non è possibile aggiungere gli account guest come utenti di Azure Data Catalog e gli utenti guest non è possibile usare il portale in www.azuredatacatalog.com.

- Creazione di risorse di Azure Data Catalog usando i comandi di modelli di Resource Manager di Azure o Azure PowerShell non è supportata.

- La risorsa di Azure Data Catalog non è possibile spostare tra i tenant di Azure.

## <a name="azure-active-directory-policy-configuration"></a>Configurazione dei criteri di Azure Active Directory

In alcune situazioni è possibile accedere al portale di Azure Data Catalog, ma quando si prova ad accedere allo strumento di registrazione dell'origine dati viene visualizzato un messaggio di errore che impedisce l'accesso. Questo errore può verificarsi quando si usa la rete aziendale o quando ci si connette dall'esterno della rete aziendale.

Lo strumento di registrazione usa l' *autenticazione basata su form* per convalidare l'accesso utente in Azure Active Directory. Per completare l'accesso, un amministratore di Azure Active Directory deve abilitare l'autenticazione basata su form nei *criteri di autenticazione globali*.

I criteri di autenticazione globali consentono di abilitare l'autenticazione separatamente per connessioni Intranet ed Extranet, come illustrato nell'immagine seguente. Se l'autenticazione basata su form non è abilitato per la rete da cui ci si connette, potrebbero verificarsi errori di accesso.

 ![Criteri di autenticazione globali di Azure Active Directory](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Per altre informazioni, vedere [Configurazione dei criteri di autenticazione](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)).

## <a name="next-steps"></a>Passaggi successivi

* [Creare una Azure Data Catalog](data-catalog-get-started.md)
