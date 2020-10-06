---
title: Panoramica dell'autenticazione di account con Automazione di Azure
description: Questo articolo offre una panoramica dell'autenticazione di account con Automazione di Azure.
keywords: sicurezza in Automazione, proteggere Automazione; autenticazione in Automazione
services: automation
ms.subservice: process-automation
ms.date: 09/28/2020
ms.topic: conceptual
ms.openlocfilehash: bcb5f61c93bd4c3ff7c0f81ae808807f7deb71df
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91766097"
---
# <a name="automation-account-authentication-overview"></a>Panoramica dell'autenticazione di account di Automazione

Automazione di Azure consente di automatizzare le attività sulle risorse in Azure, in locale e con altri provider di servizi cloud, ad esempio Amazon Web Services (AWS). È possibile usare manuali operativi per automatizzare le attività o un ruolo di lavoro ibrido per runbook se si hanno processi aziendali o operativi da gestire all'esterno di Azure. Per lavorare in uno di questi ambienti, è necessario disporre delle autorizzazioni per accedere in modo sicuro alle risorse con i diritti minimi necessari.

Questo articolo illustra i diversi scenari di autenticazione supportati da Automazione di Azure e descrive come iniziare partendo da uno o più ambienti che è necessario gestire.

## <a name="automation-account"></a>Account di Automazione

Al primo avvio di Automazione di Azure sarà necessario creare almeno un account di Automazione. Gli account di Automazione consentono di isolare le risorse di Automazione (runbook, asset, configurazioni) dalle risorse di altri account. È possibile usare gli account di Automazione per separare le risorse in ambienti logici distinti. Ad esempio, è possibile usare un account per lo sviluppo, uno per la produzione e un altro per l'ambiente locale. Un account di Automazione di Azure è diverso dagli account Microsoft creati nella sottoscrizione di Azure. Per un'introduzione alla creazione di un account di Automazione, vedere [Creare un account di Automazione](automation-quickstart-create-account.md).

## <a name="automation-resources"></a>Risorse di Automazione

Le risorse di Automazione per ogni account di Automazione sono associate a una singola area di Azure, ma l'account può gestire tutte le risorse nella sottoscrizione di Azure. Il motivo principale per cui creare gli account di Automazione in aree diverse è la presenza di criteri che richiedono che dati e risorse siano isolati in un'area specifica.

Tutte le attività eseguite sulle risorse con Azure Resource Manager e i cmdlet di PowerShell in Automazione di Azure devono eseguire l'autenticazione in Azure con l'autenticazione basata su credenziali dell'identità dell'organizzazione di Azure Active Directory (Azure AD).

## <a name="run-as-accounts"></a>Account RunAs

Gli account RunAs in automazione di Azure forniscono l'autenticazione per la gestione di risorse Azure Resource Manager o risorse distribuite nel modello di distribuzione classica. In automazione di Azure sono disponibili due tipi di account RunAs:

* Account RunAs di Azure
* Account RunAs classico di Azure

Per altre informazioni su questi due modelli di distribuzione, vedere [Gestione risorse e distribuzione classica](../azure-resource-manager/management/deployment-models.md).

>[!NOTE]
>Le sottoscrizioni Azure Cloud Solution Provider (CSP) supportano solo il modello di Azure Resource Manager. I servi diversi da Azure Resource Manager non sono disponibili nel programma. Quando si usa una sottoscrizione CSP, non viene creato l'account RunAs classico di Azure, ma l'account RunAs di Azure. Per altre informazioni sulle sottoscrizioni CSP, vedere [Servizi disponibili nelle sottoscrizioni CSP](/azure/cloud-solution-provider/overview/azure-csp-available-services).

### <a name="run-as-account"></a>account RunAs

L'account RunAs di Azure gestisce le risorse di Azure in base al servizio di distribuzione e gestione Azure Resource Manager per Azure.

Quando si crea un account RunAs, vengono eseguite le attività seguenti:

* Crea un'applicazione Azure AD con un certificato autofirmato, crea un account dell'entità servizio per l'applicazione in Azure AD e assegna il ruolo [collaboratore](../role-based-access-control/built-in-roles.md#contributor) per l'account nella sottoscrizione corrente. È possibile cambiare l'impostazione del certificato in Proprietario o in qualsiasi altro ruolo. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).

* Crea un asset del certificato di Automazione denominato `AzureRunAsCertificate` nell'account di Automazione specificato. L'asset del certificato contiene la chiave privata del certificato usata dall'applicazione Azure AD.

* Crea un asset della connessione di Automazione denominato `AzureRunAsConnection` nell'account di Automazione specificato. L'asset di connessione contiene l'ID applicazione, l'ID tenant, l'ID sottoscrizione e l'identificazione personale del certificato.

### <a name="azure-classic-run-as-account"></a>Account RunAs classico di Azure

L'account RunAs classico di Azure gestisce le risorse classiche di Azure in base al modello di distribuzione classica. Per creare o rinnovare questo tipo di account RunAs, è necessario essere un coamministratore della sottoscrizione.

Quando si crea un account RunAs classico di Azure, vengono eseguite le attività seguenti.

* Crea un certificato di gestione nella sottoscrizione.

* Crea un asset del certificato di Automazione denominato `AzureClassicRunAsCertificate` nell'account di Automazione specificato. L'asset di certificato contiene la chiave privata del certificato usata dal certificato di gestione.

* Crea un asset della connessione di Automazione denominato `AzureClassicRunAsConnection` nell'account di Automazione specificato. L'asset della connessione contiene il nome della sottoscrizione, l'ID sottoscrizione e il nome dell'asset del certificato.

>[!NOTE]
>Gli account RunAs classici di Azure non vengono creati per impostazione predefinita mentre si crea un account di Automazione. Questo account viene creato singolarmente seguendo i passaggi descritti nell'articolo [gestire l'account RunAs](manage-runas-account.md#create-a-run-as-account-in-azure-portal) .

## <a name="service-principal-for-run-as-account"></a>Entità servizio per l'account RunAs

Per impostazione predefinita l'entità servizio per un account RunAs non ha le autorizzazioni di lettura per Azure AD. Se vogliono aggiungere autorizzazioni di lettura o gestione di Azure AD, è necessario concederle all'entità servizio in **Autorizzazioni API**. Per altre informazioni, vedere [aggiungere autorizzazioni per accedere all'API Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Il controllo degli accessi in base al ruolo è disponibile con Azure Resource Manager per concedere a un account utente di Azure AD e a un account RunAs l'autorizzazione per le azioni consentite e per l'autenticazione dell'entità servizio. Per altre informazioni utili per sviluppare il modello per la gestione delle autorizzazioni di Automazione, vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Autenticazione dei runbook con ruolo di lavoro ibrido per runbook

I runbook eseguiti in un ruolo di lavoro ibrido per runbook nel data center o in servizi di calcolo in altri ambienti cloud, come AWS, non possono usare lo stesso metodo usato solitamente per l'autenticazione dei runbook per le risorse di Azure. Il motivo è che queste risorse vengono eseguite all'esterno di Azure e di conseguenza devono usare le proprie credenziali di sicurezza definite in Automazione per autenticare le risorse cui accedono in locale. Per altre informazioni sull'autenticazione di runbook con i ruoli di lavoro per runbook, vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).

Per i runbook che usano i ruoli di lavoro ibridi per runbook nelle macchine virtuali di Azure, è possibile usare l'[autenticazione dei runbook con identità gestite](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) invece degli account RunAs per l'autenticazione con le risorse di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Per creare un account di Automazione dal portale di Azure, vedere [Creare un account di Automazione di Azure autonomo](automation-create-standalone-account.md).
* Se si preferisce creare l'account usando un modello, vedere [Creare un account di Automazione usando un modello di Azure Resource Manager](quickstart-create-automation-account-template.md).
* Per l'autenticazione con Amazon Web Services, vedere [Autenticare runbook con Amazon Web Services](automation-config-aws-account.md).