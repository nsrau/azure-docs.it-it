---
title: Gestire i dati degli utenti nel Centro sicurezza di Azure | Microsoft Docs
description: " Questo articolo illustra come gestire i dati degli utenti nel Centro sicurezza di Azure. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: terrylan
ms.openlocfilehash: fcaac3d248b676e4b7b1fe0344b54e52ce1fb558
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302674"
---
# <a name="manage-user-data-in-azure-security-center"></a>Gestire i dati degli utenti nel Centro sicurezza di Azure
Questo articolo fornisce informazioni su come è possibile gestire i dati degli utenti nel Centro sicurezza di Azure. La gestione dei dati degli utenti include la possibilità di accedere ai dati, eliminarli o esportarli.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Un utente del Centro sicurezza a cui è assegnato il ruolo Lettore, Proprietario, Collaboratore o Amministratore dell'account può accedere ai dati dei clienti direttamente dallo strumento. Per altre informazioni sui ruoli Lettore, Proprietario e Collaboratore, vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/built-in-roles.md). Per altre informazioni sul ruolo Amministratore dell'account, vedere [Amministratori delle sottoscrizioni di Azure](../billing/billing-add-change-azure-subscription-administrator.md).

## <a name="searching-for-and-identifying-personal-data"></a>Ricerca e identificazione dei dati personali
Un utente del Centro sicurezza può visualizzare i dati personali tramite il portale di Azure. Centro sicurezza archivia solo i dettagli del contatto di sicurezza, ad esempio i numeri di telefono e gli indirizzi di posta elettronica. Per altre informazioni, vedere [Specificare i dettagli dei contatti di sicurezza nel Centro sicurezza di Azure](security-center-provide-security-contact-details.md).

Nel portale di Azure un utente può visualizzare le configurazioni IP consentite usando le funzionalità di accesso JIT (Just-In-Time) alla VM del Centro sicurezza. Per altre informazioni, vedere [Gestire l'accesso alle macchine virtuali con la funzionalità JIT (Just-in-Time) ](security-center-just-in-time.md).

Nel portale di Azure un utente può visualizzare gli avvisi di sicurezza forniti dal Centro sicurezza, inclusi gli indirizzi IP e i dettagli di un utente malintenzionato. Per altre informazioni, vedere [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md).

## <a name="classifying-personal-data"></a>Classificazione dei dati personali
Non è necessario classificare i dati personali presenti nella funzionalità di gestione dei contatti di sicurezza del Centro sicurezza. I dati salvati sono un indirizzo di posta elettronica (o più indirizzi di posta elettronica) e un numero di telefono. I [dati di contatto](security-center-provide-security-contact-details.md) vengono convalidati dal Centro sicurezza.

Non è necessario classificare gli indirizzi IP e i numeri di porta salvati dalla funzionalità [Just-In-Time](security-center-just-in-time.md) del Centro sicurezza.

Solo un utente a cui è assegnato il ruolo Amministratore può classificare i dati personali [visualizzando gli avvisi](security-center-managing-and-responding-alerts.md) in Centro sicurezza.

## <a name="securing-and-controlling-access-to-personal-data"></a>Sicurezza e controllo dell'accesso ai dati personali
Un utente del Centro sicurezza a cui è assegnato il ruolo Lettore, Proprietario, Collaboratore o Amministratore dell'account può accedere ai [dati di contatto di sicurezza](security-center-provide-security-contact-details.md).

Un utente del Centro sicurezza a cui è assegnato il ruolo Lettore, Proprietario, Collaboratore o Amministratore dell'account può accedere ai criteri [Just-In-Time](security-center-just-in-time.md).

Un utente del Centro sicurezza a cui è assegnato il ruolo Lettore, Proprietario, Collaboratore o Amministratore dell'account può visualizzare gli [avvisi](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Aggiornamento dei dati personali
Un utente del Centro sicurezza a cui è assegnato il ruolo Proprietario, Collaboratore o Amministratore dell'account può aggiornare i [dati di contatto di sicurezza](security-center-provide-security-contact-details.md) tramite il portale di Azure.

Un utente del Centro sicurezza a cui è assegnato il ruolo Proprietario, Collaboratore o Amministratore dell'account può aggiornare i [criteri Just-In-Time](security-center-just-in-time.md).

Un utente con ruolo Amministratore dell'account non può modificare gli eventi imprevisti di avviso. Gli [eventi imprevisti di avviso](security-center-managing-and-responding-alerts.md) sono considerati dati sulla sicurezza e sono di sola lettura.

## <a name="deleting-personal-data"></a>Eliminazione dei dati personali
Un utente del Centro sicurezza a cui è assegnato il ruolo Proprietario, Collaboratore o Amministratore dell'account può eliminare i [dati di contatto di sicurezza](security-center-provide-security-contact-details.md) tramite il portale di Azure.

Un utente del Centro sicurezza a cui è assegnato il ruolo Proprietario, Collaboratore o Amministratore dell'account può eliminare i [criteri Just-In-Time](security-center-just-in-time.md) tramite il portale di Azure.

Un utente del Centro sicurezza non può eliminare gli eventi imprevisti di avviso. In base alle esigenze di sicurezza, gli [eventi imprevisti di avviso](security-center-managing-and-responding-alerts.md) sono considerati dati di sola lettura.

## <a name="exporting-personal-data"></a>Esportazione dei dati personali
Un utente del Centro sicurezza a cui è assegnato il ruolo Lettore, Proprietario, Collaboratore o Amministratore dell'account può esportare i [dati di contatto di sicurezza](security-center-provide-security-contact-details.md) tramite:

- Esecuzione di una copia dal portale di Azure
- Esecuzione della chiamata API REST di Azure, GET HTTP:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
```

Un utente del Centro sicurezza a cui è assegnato il ruolo Amministratore dell'account può esportare i criteri [Just-In-Time](security-center-just-in-time.md) contenenti gli indirizzi IP, tramite:

- Esecuzione di una copia dal portale di Azure
- Esecuzione della chiamata API REST di Azure, GET HTTP:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
```

Un utente con ruolo Amministratore dell'account può esportare i dettagli dell'avviso tramite:

- Esecuzione di una copia dal portale di Azure
- Esecuzione della chiamata API REST di Azure, GET HTTP:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
```

Per altre informazioni, vedere [Get Security Alerts (GET Collection)](https://msdn.microsoft.com/library/mt704050.aspx) (Ottenere avvisi di sicurezza (raccolta GET)).

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Limitazione dell'uso dei dati personali per la profilatura o il marketing senza consenso dell'utente
Un utente del Centro sicurezza può scegliere di rifiutare esplicitamente eliminando i propri [dati di contatto di sicurezza](security-center-provide-security-contact-details.md).

I [dati Just-In-Time](security-center-just-in-time.md) vengono considerati non personali e vengono conservati per un periodo di 30 giorni.

I [dati sugli avvisi](security-center-managing-and-responding-alerts.md) vengono considerati dati sulla sicurezza e vengono conservati per un periodo di due anni.

## <a name="auditing-and-reporting"></a>Controllo e creazione di report
I log di controllo del contatto di sicurezza, i criteri Just-In-Time e gli aggiornamenti relativi agli avvisi vengono conservati in [Log attività di Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla gestione dei dati degli utenti, vedere [Gestire i dati degli utenti rilevati in un'indagine del Centro sicurezza di Azure](security-center-investigation-user-data.md).
