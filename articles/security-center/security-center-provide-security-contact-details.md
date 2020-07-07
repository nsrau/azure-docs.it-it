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
ms.openlocfilehash: 08ad761e81909e6ab23c7c07f5ce05865136bc47
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82204101"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Specificare i dettagli dei contatti di sicurezza nel Centro sicurezza di Azure
Il Centro sicurezza di Azure consiglierà di specificare i dettagli dei contatti di sicurezza per la sottoscrizione di Azure, se non è già stato fatto. Queste informazioni verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente. Microsoft Security Response Center esegue il monitoraggio selettivo della sicurezza della rete e dell'infrastruttura di Azure e riceve informazioni sulle minacce e segnalazioni di violazioni da terzi.

Alla prima occorrenza giornaliera di un avviso e solo per gli avvisi di elevata gravità viene inviata una notifica tramite posta elettronica. Le preferenze di posta elettronica possono essere configurate solo per i criteri della sottoscrizione. I gruppi di risorse all'interno di una sottoscrizione ereditano queste impostazioni. Gli avvisi sono disponibili solo nel livello standard del Centro sicurezza di Azure.

Le notifiche di avviso tramite posta elettronica vengono inviate:
- A un singolo destinatario di posta elettronica per tipo di avviso al giorno  
- Non vengono inviati più di 3 messaggi di posta elettronica a un singolo destinatario in un solo giorno
- Ogni messaggio di posta elettronica contiene un unico avviso, non un'aggregazione di avvisi
- Solo per avvisi con gravità alta

> [!TIP]
> Per gli avvisi con altri livelli di gravità, creare un' [automazione del flusso di lavoro](workflow-automation.md) per usare un'app per la logica che invierà messaggi di posta elettronica al personale interessato.
 
Ad esempio, se è già stato inviato un messaggio di posta elettronica per avvisare l'utente di un attacco RDP, questo non riceverà nessun altro messaggio di posta elettronica relativo all'attacco RDP nello stesso giorno, anche se viene attivato un altro avviso. 

> [!IMPORTANT]
> Il documento introduce il servizio usando una distribuzione di esempio.  Questa non è una guida dettagliata.

## <a name="set-up-email-notifications-for-alerts"></a>Configurare le notifiche di posta elettronica per gli avvisi<a name="email"></a>

1. Per un utente con ruolo amministratore della sicurezza o proprietario della sottoscrizione, aprire la pagina delle **notifiche tramite posta elettronica** :

    - Per gli avvisi, aprire **prezzi & impostazioni**, selezionare la sottoscrizione pertinente e selezionare **notifiche di posta elettronica**.

    - Se si sta implementando una raccomandazione, in **raccomandazioni**selezionare **specificare i dettagli dei contatti di sicurezza**e selezionare la sottoscrizione di Azure in cui inserire le informazioni di contatto. Si aprirà **Notifiche tramite posta elettronica**.

   ![Specificare dettagli del contatto per la sicurezza][2]

1. Immettere l'indirizzo o gli indirizzi di posta elettronica del contatto di sicurezza, separati da virgole. Non esiste alcun limite al numero di indirizzi di posta elettronica che è possibile immettere.

1. Per ricevere messaggi di posta elettronica relativi agli avvisi di elevata gravità, attivare l'opzione **Send me emails about alerts**(Invia messaggi di posta elettronica sugli avvisi). Per gli altri livelli di gravità usare un'app per la logica come illustrato in [automazione dei flussi di lavoro](workflow-automation.md).

1. È possibile inviare notifiche tramite posta elettronica ai proprietari delle sottoscrizioni (l'amministratore e i coamministratori del servizio classico, oltre al ruolo di proprietario RBAC nell'ambito della sottoscrizione).

1. Per applicare le informazioni di contatto di sicurezza alla sottoscrizione, selezionare **Salva**.

## <a name="see-also"></a>Vedere anche
Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel centro sicurezza di Azure](tutorial-security-policy.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione delle raccomandazioni di sicurezza nel centro sicurezza di Azure](security-center-recommendations.md) : informazioni su come i consigli consentono di proteggere le risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare lo stato di integrità delle soluzioni dei partner.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
