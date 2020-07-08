---
title: Panoramica dell'autenticazione di account con Automazione di Azure
description: Questo articolo offre una panoramica dell'autenticazione di account con Automazione di Azure.
keywords: sicurezza in Automazione, proteggere Automazione; autenticazione in Automazione
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: d2b9f705c73e667f34e46fdeed3c80af1e65fb12
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830107"
---
# <a name="automation-account-authentication-overview"></a>Panoramica dell'autenticazione di account di Automazione

Automazione di Azure consente di automatizzare le attività sulle risorse in Azure, in locale e con altri provider di servizi cloud, ad esempio Amazon Web Services (AWS). È possibile usare i runbook per automatizzare le attività oppure un ruolo di lavoro ibrido per runbook se è necessario gestire attività non di Azure. Entrambi gli ambienti richiedono le autorizzazioni per accedere in modo sicuro alle risorse con i diritti minimi necessari nella sottoscrizione di Azure.

Questo articolo illustra i diversi scenari di autenticazione supportati da Automazione di Azure e descrive come iniziare partendo da uno o più ambienti che è necessario gestire.

## <a name="automation-account"></a>Account di Automazione 

Al primo avvio di Automazione di Azure sarà necessario creare almeno un account di Automazione. Gli account di Automazione consentono di isolare le risorse di Automazione (runbook, asset, configurazioni) dalle risorse di altri account. È possibile usare gli account di Automazione per separare le risorse in ambienti logici distinti. Ad esempio, è possibile usare un account per lo sviluppo, uno per la produzione e un altro per l'ambiente locale. Un account di Automazione di Azure è diverso dagli account Microsoft creati nella sottoscrizione di Azure. Per un'introduzione alla creazione di un account di Automazione, vedere [Creare un account di Automazione](automation-quickstart-create-account.md).

## <a name="automation-resources"></a>Risorse di Automazione

Le risorse di Automazione per ogni account di Automazione sono associate a una singola area di Azure, ma l'account può gestire tutte le risorse nella sottoscrizione di Azure. Il motivo principale per cui creare gli account di Automazione in aree diverse è la presenza di criteri che richiedono che dati e risorse siano isolati in un'area specifica.

Tutte le attività eseguite sulle risorse con Azure Resource Manager e i cmdlet di PowerShell in Automazione di Azure devono eseguire l'autenticazione in Azure con l'autenticazione basata su credenziali dell'identità dell'organizzazione di Azure Active Directory (Azure AD). 

## <a name="run-as-account"></a>account RunAs

Gli account RunAs in Automazione di Azure offrono l'autenticazione per la gestione delle risorse in Azure con i cmdlet di PowerShell. Quando si crea un account RunAs, viene creato un nuovo utente dell'entità servizio in Azure AD, a cui viene assegnato il ruolo Collaboratore a livello della sottoscrizione. Per i runbook che usano i ruoli di lavoro ibridi per runbook nelle macchine virtuali di Azure, è possibile usare l'[autenticazione dei runbook con identità gestite](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) invece degli account RunAs per l'autenticazione con le risorse di Azure.

## <a name="service-principal-for-run-as-account"></a>Entità servizio per l'account RunAs

Per impostazione predefinita l'entità servizio per un account RunAs non ha le autorizzazioni di lettura per Azure AD. Se vogliono aggiungere autorizzazioni di lettura o gestione di Azure AD, è necessario concederle all'entità servizio in **Autorizzazioni API**. Per altre informazioni, vedere [Aggiungere autorizzazioni per accedere ad API Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Il controllo degli accessi in base al ruolo è disponibile con Azure Resource Manager per concedere a un account utente di Azure AD e a un account RunAs l'autorizzazione per le azioni consentite e per l'autenticazione dell'entità servizio. Per altre informazioni utili per sviluppare il modello per la gestione delle autorizzazioni di Automazione, vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).  

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Autenticazione dei runbook con ruolo di lavoro ibrido per runbook 

I runbook eseguiti in un ruolo di lavoro ibrido per runbook nel data center o in servizi di calcolo in altri ambienti cloud, come AWS, non possono usare lo stesso metodo usato solitamente per l'autenticazione dei runbook per le risorse di Azure. Il motivo è che queste risorse vengono eseguite all'esterno di Azure e di conseguenza devono usare le proprie credenziali di sicurezza definite in Automazione per autenticare le risorse cui accedono in locale. Per altre informazioni sull'autenticazione di runbook con i ruoli di lavoro per runbook, vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md). 

## <a name="next-steps"></a>Passaggi successivi

* Per creare un account di Automazione dal portale di Azure, vedere [Creare un account di Automazione di Azure autonomo](automation-create-standalone-account.md).
* Se si preferisce creare l'account usando un modello, vedere [Creare un account di Automazione usando un modello di Azure Resource Manager](automation-create-account-template.md).
* Per l'autenticazione con Amazon Web Services, vedere [Autenticare runbook con Amazon Web Services](automation-config-aws-account.md).