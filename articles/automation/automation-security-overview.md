---
title: Introduzione all'autenticazione in Automazione di Azure
description: Questo articolo offre una panoramica della sicurezza in Automazione e dei diversi metodi di autenticazione disponibili per gli account di Automazione in Automazione di Azure.
keywords: sicurezza in Automazione, proteggere Automazione; autenticazione in Automazione
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 109bb6dd29ea9c4239e0abcfc668f1185f7e9783
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82114531"
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Introduzione all'autenticazione in Automazione di Azure

Automazione di Azure consente di automatizzare le attività sulle risorse in Azure, in locale e con altri provider di servizi cloud, ad esempio Amazon Web Services (AWS). Un runbook, per eseguire le azioni obbligatorie, deve avere le autorizzazioni per accedere in modo sicuro alle risorse con i diritti minimi necessari nella sottoscrizione.

Questo articolo illustra i vari scenari di autenticazione supportati da automazione di Azure e come iniziare a usare l'ambiente o gli ambienti che è necessario gestire.  

## <a name="automation-account-overview"></a>Panoramica dell'account di Automazione

Al primo avvio di Automazione di Azure sarà necessario creare almeno un account di Automazione. Gli account di Automazione consentono di isolare le risorse di Automazione (runbook, asset, configurazioni) dalle risorse contenute in altri account di Automazione. È possibile usare gli account di Automazione per separare le risorse in ambienti logici distinti. Ad esempio, è possibile usare un account per lo sviluppo, uno per la produzione e un altro per l'ambiente locale. Un account di Automazione di Azure è diverso dagli account Microsoft creati nella sottoscrizione di Azure.

Le risorse di Automazione per ogni account di Automazione sono associate a una singola area di Azure, ma gli account di Automazione possono gestire tutte le risorse nella sottoscrizione. Il motivo principale per cui creare gli account di Automazione in aree diverse è la presenza di criteri che richiedono dati e risorse per essere isolati in un'area specifica.

Tutte le attività eseguite sulle risorse con Azure Resource Manager e i cmdlet di Azure in Automazione di Azure devono eseguire l'autenticazione in Azure con l'autenticazione basata su credenziali dell'identità dell'organizzazione di Azure Active Directory. Gli account RunAs in automazione di Azure forniscono l'autenticazione per la gestione delle risorse in Azure usando i cmdlet di Azure. Quando si crea un account RunAs, viene creato un nuovo utente entità servizio in Azure Active Directory (AD) e viene assegnato a tale utente il ruolo Collaboratore a livello di sottoscrizione. Per i runbook che usano i ruoli di lavoro ibridi per runbook nelle macchine virtuali di Azure, è possibile usare [identità gestite per le risorse di Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) invece degli account RunAs per l'autenticazione con le risorse di Azure.

L'entità servizio per un account RunAs non dispone delle autorizzazioni per leggere Azure AD per impostazione predefinita. Se si desidera aggiungere autorizzazioni per la lettura o la gestione di Azure AD, è necessario concedere le autorizzazioni per l'entità servizio in **autorizzazioni API**. Per altre informazioni, vedere [aggiungere autorizzazioni per accedere alle API Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

Il controllo degli accessi in base al ruolo è disponibile con Azure Resource Manager per l'esecuzione di azioni consentite con un account utente di Azure AD e per l'autenticazione di tale entità servizio. Per altre informazioni utili per sviluppare il modello per la gestione delle autorizzazioni di Automazione, vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).  

Manuali operativi in esecuzione in un ruolo di lavoro ibrido per Runbook nel data center o in servizi di calcolo in altri ambienti cloud come AWS, non può usare lo stesso metodo usato in genere per l'autenticazione manuali operativi per le risorse di Azure. Il motivo è che queste risorse vengono eseguite all'esterno di Azure e di conseguenza devono usare le proprie credenziali di sicurezza definite in Automazione per autenticare le risorse cui accedono in locale. Per altre informazioni sull'autenticazione runbook con i ruoli di lavoro Runbook, vedere [autenticare manuali operativi per i ruoli di lavoro ibridi per Runbook](automation-hrw-run-runbooks.md). 

## <a name="next-steps"></a>Passaggi successivi

* [Creare un account di automazione dalla portale di Azure](automation-create-standalone-account.md).

* [Creare un account di automazione usando Azure Resource Manager modello](automation-create-account-template.md).

* [Eseguire l'autenticazione con Amazon Web Services (AWS)](automation-config-aws-account.md).
