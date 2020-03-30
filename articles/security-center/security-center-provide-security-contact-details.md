---
title: Specificare i dettagli dei contatti di sicurezza nel Centro sicurezza di Azure | Documentazione Microsoft
description: Questo documento illustra come specificare i dettagli dei contatti di sicurezza nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2019
ms.author: memildin
ms.openlocfilehash: 64a9600a3014f7e85a7f924d38882bfadaf631db
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387819"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Specificare i dettagli dei contatti di sicurezza nel Centro sicurezza di Azure
Il Centro sicurezza di Azure consiglierà di specificare i dettagli dei contatti di sicurezza per la sottoscrizione di Azure, se non è già stato fatto. Queste informazioni verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente. Microsoft Security Response Center esegue il monitoraggio selettivo della sicurezza della rete e dell'infrastruttura di Azure e riceve informazioni sulle minacce e segnalazioni di violazioni da terzi.

Alla prima occorrenza giornaliera di un avviso e solo per gli avvisi di elevata gravità viene inviata una notifica tramite posta elettronica. Le preferenze di posta elettronica possono essere configurate solo per i criteri della sottoscrizione. I gruppi di risorse all'interno di una sottoscrizione ereditano queste impostazioni. Gli avvisi sono disponibili solo nel livello Standard del Centro sicurezza di Azure.Alerts are available only in the Standard tier of Azure Security Center.

Le notifiche di avviso tramite posta elettronica vengono inviate:
- A un singolo destinatario di posta elettronica per tipo di avviso al giorno  
- Non vengono inviati più di 3 messaggi di posta elettronica a un singolo destinatario in un solo giorno
- Ogni messaggio di posta elettronica contiene un unico avviso, non un'aggregazione di avvisi
- Solo per avvisi con gravità alta

> [!TIP]
> Per gli avvisi con altri livelli di gravità, creare [un'automazione del flusso](workflow-automation.md) di lavoro per usare un'app per la logica che invierà messaggi di posta elettronica al personale pertinente.
 
Ad esempio, se è già stato inviato un messaggio di posta elettronica per avvisare l'utente di un attacco RDP, questo non riceverà nessun altro messaggio di posta elettronica relativo all'attacco RDP nello stesso giorno, anche se viene attivato un altro avviso. 

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio.  Questa non è una guida dettagliata.

## <a name="set-up-email-notifications-for-alerts"></a>Configurare le notifiche tramite posta elettronica per gli avvisi<a name="email"></a>

1. Aprire la pagina **Notifiche e-mail:**

    - Per gli avvisi, aprire **Impostazioni di & prezzi**, selezionare la sottoscrizione pertinente e selezionare Notifiche **tramite posta elettronica**.

    - Se si implementa un suggerimento, in **Raccomandazioni**selezionare **Fornisci dettagli contatto**di sicurezza , selezionare la sottoscrizione di Azure su cui fornire le informazioni di contatto. Si aprirà **Notifiche tramite posta elettronica**.

   ![Specificare dettagli del contatto per la sicurezza][2]

1. Immettere l'indirizzo o gli indirizzi di posta elettronica del contatto di sicurezza, separati da virgole. Non c'è limite al numero di indirizzi email che puoi inserire.

1. Per ricevere messaggi di posta elettronica relativi agli avvisi di elevata gravità, attivare l'opzione **Send me emails about alerts**(Invia messaggi di posta elettronica sugli avvisi). Per altri livelli di gravità utilizzare un'app per la logica come spiegato [nell'automazione del flusso](workflow-automation.md)di lavoro.

1. È possibile inviare notifiche tramite posta elettronica ai proprietari della sottoscrizione (amministratore del servizio classico e coamministratori, oltre al ruolo Proprietario RBAC nell'ambito della sottoscrizione).

1. Per applicare le informazioni di contatto di sicurezza all'abbonamento, selezionare **Salva**.

## <a name="see-also"></a>Vedere anche
Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Setting security policies in Azure Security Center:](tutorial-security-policy.md) informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.Setting security policies in Azure Security Center -- Learn how to configure security policies for your Azure subscriptions and resource groups.
* [Gestione dei consigli](security-center-recommendations.md) sulla sicurezza nel Centro sicurezza di Azure: informazioni su come i consigli consentono di proteggere le risorse di Azure.Managing security recommendations in Azure Security Center -- Learn how recommendations help you protect your Azure resources.
* [Monitoraggio dell'integrità](security-center-monitoring.md) della sicurezza nel Centro sicurezza di Azure: informazioni su come monitorare l'integrità delle risorse di Azure.Security health monitoring in Azure Security Center-- Learn how to monitor the health of your Azure resources.
* [Gestione e risposta agli avvisi](security-center-managing-and-responding-alerts.md) di sicurezza nel Centro sicurezza di Azure: informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare lo stato integrità delle soluzioni dei partner.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
