---
title: I criteri di sicurezza del Centro sicurezza di Azure possono essere impostati come parte dei criteri di Azure e visualizzati nel Centro sicurezza | Microsoft Docs
description: Questo documento descrive come impostare i criteri in Criteri di Azure o visualizzarli nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/25/2018
ms.author: rkarlin
ms.openlocfilehash: 330b66e64484417e50f39c35cf90a6fd62b1e888
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334670"
---
# <a name="view-security-policies"></a>Visualizzare i criteri di sicurezza

Questo articolo illustra come vengono configurati i criteri di sicurezza e come visualizzarli nel Centro sicurezza. Il Centro sicurezza di Azure assegna automaticamente i [criteri di sicurezza predefiniti](security-center-policy-definitions.md) in ogni sottoscrizione caricata. È possibile configurarli in [Criteri di Azure](../azure-policy/azure-policy-introduction.md), che consente anche di impostare i criteri nei gruppi di gestione e in più sottoscrizioni.

Per istruzioni su come impostare i criteri usando PowerShell, consulta [Guida introduttiva: Creare un'assegnazione di criteri per identificare le risorse non conformi con il modulo Azure RM di PowerShell](../azure-policy/assign-policy-definition-ps.md).



## <a name="what-are-security-policies"></a>Informazioni sui criteri di sicurezza
Un criterio di sicurezza definisce la configurazione specifica dei carichi di lavoro e contribuisce ad assicurare la conformità ai requisiti aziendali o normativi per la sicurezza. In Criteri di Azure è possibile definire criteri per le sottoscrizioni di Azure e adattarli al tipo di carico di lavoro o alla riservatezza dei dati. Ad esempio, le applicazioni che usano dati regolamentati come le informazioni personali possono richiedere un livello di sicurezza maggiore di quello degli altri carichi di lavoro. Per impostare un criterio nelle sottoscrizioni o nei gruppi di gestione, impostarle nei [Criteri di Azure](../azure-policy/azure-policy-introduction.md).



I criteri di sicurezza determinano i suggerimenti per la sicurezza ottenuti nel Centro sicurezza di Azure. È possibile monitorare la conformità a tali criteri per identificare potenziali vulnerabilità e attenuare le minacce. Per altre informazioni su come determinare l'opzione più appropriata, vedere l'elenco di [criteri di sicurezza predefiniti](security-center-policy-definitions.md).

### <a name="management-groups"></a>Gruppi di gestione
Se l'organizzazione dispone di molte sottoscrizioni, potrebbe essere necessario gestire in modo efficace l'accesso, i criteri e la conformità per tali sottoscrizioni. I gruppi di gestione di Azure forniscono un livello di ambito oltre le sottoscrizioni. Le sottoscrizioni sono organizzate in contenitori chiamati "gruppi di gestione" a cui vengono applicati i criteri di governance. Tutte le sottoscrizioni all'interno di un gruppo di gestione ereditano automaticamente i criteri applicati al gruppo di gestione. A ogni directory viene assegnato un gruppo di gestione principale denominato gruppo di gestione "radice". Questo gruppo di gestione radice è integrato nella gerarchia in modo da ricondurre al suo interno tutti i gruppi di gestione e le sottoscrizioni. Il gruppo di gestione radice permette l'applicazione di criteri globali e assegnazioni di Controllo degli accessi in base al ruolo a livello di directory. Per configurare i gruppi di gestione per l'uso con Centro sicurezza di Azure, seguire le istruzioni nell'articolo [Ottenere visibilità a livello di tenant per il Centro sicurezza di Azure](security-center-management-groups.md).

> [!NOTE]
> È importante comprendere la gerarchia dei gruppi di gestione e delle sottoscrizioni. Vedere [Organizzare le risorse con i gruppi di gestione di Azure](../governance/management-groups/index.md#root-management-group-for-each-directory) per altre informazioni sui gruppi di gestione, la gestione radice e l'accesso ai gruppi di gestione.
>

## <a name="how-security-policies-work"></a>Funzionamento dei criteri di sicurezza
Il Centro sicurezza crea automaticamente un criterio di sicurezza predefinito per ogni sottoscrizione di Azure. È possibile modificare i criteri in Criteri di Azure per eseguire le operazioni seguenti:
- Creare nuove definizioni dei criteri.
- Assegnare i criteri ai gruppi di gestione e alle sottoscrizioni, che possono rappresentare un'intera organizzazione o una business unit all'interno dell'organizzazione.
- Monitorare la conformità ai criteri.

Per altre informazioni su Criteri di Azure, vedere [Creare e gestire i criteri per applicare la conformità](../azure-policy/create-manage-policy.md).

Un criterio di Azure è costituito dai componenti seguenti:

- Un **criterio** è una regola.
- Un'**iniziativa** è una raccolta di criteri.
- Un'**assegnazione** è l'applicazione di un'iniziativa o di un criterio a un ambito specifico (gruppo di gestione, sottoscrizione o gruppo di risorse).

Una risorsa viene valutata in base ai criteri ad essa assegnati e riceve un rapporto di conformità in base al numero di criteri a cui la risorsa è conforme.

## <a name="who-can-edit-security-policies"></a>Utenti che possono modificare i criteri di sicurezza
Il Centro sicurezza usa il controllo degli accessi in base al ruolo, che fornisce ruoli predefiniti assegnabili a utenti, gruppi e servizi in Azure. Quando un utente apre il Centro sicurezza, visualizza solo informazioni correlate alle risorse a cui ha accesso. All'utente viene infatti assegnato il ruolo Proprietario, Collaboratore o Lettore per la sottoscrizione o il gruppo di risorse a cui appartiene la risorsa. Oltre a questi ruoli, esistono due ruoli specifici del Centro sicurezza:

- Ruolo con autorizzazioni di lettura per la sicurezza: l'utente con questo ruolo dispone dei diritti di visualizzazione per il Centro sicurezza, inclusi avvisi, criteri, raccomandazioni e integrità, ma non può apportare modifiche.
- Amministratore della sicurezza: l'utente con questo ruolo dispone degli stessi diritti di visualizzazione del ruolo con autorizzazioni di lettura per la sicurezza, ma può anche aggiornare i criteri di sicurezza e ignorare raccomandazioni e avvisi.

## <a name="edit-security-policies"></a>Modificare i criteri di sicurezza
È possibile modificare i criteri di sicurezza predefiniti per ogni sottoscrizione e gruppo di gestione di Azure in [Criteri di Azure](../governance/policy/tutorials/create-and-manage.md). Per modificare i criteri di sicurezza, è necessario essere un proprietario, un collaboratore o un amministratore della sicurezza della sottoscrizione o del gruppo di gestione che la include.

Per istruzioni su come modificare un criterio di sicurezza in Criteri di Azure, vedere [Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md).

## <a name="view-security-policies"></a>Visualizzare i criteri di sicurezza

Per visualizzare i criteri di sicurezza nel Centro sicurezza:

1. Nel dashboard del **Centro sicurezza** selezionare **Criteri di sicurezza**.

    ![Riquadro Gestione dei criteri](./media/security-center-policies/security-center-policy-mgt.png)

  Nella schermata **Gestione dei criteri** è possibile visualizzare il numero dei gruppi di gestione, delle sottoscrizioni e delle aree di lavoro, oltre alla struttura dei gruppi di gestione.

  > [!NOTE]
  > - Il dashboard Centro sicurezza potrebbe mostrare in **Copertura della sottoscrizione** un numero di sottoscrizioni maggiore rispetto a quello indicato in **Gestione dei criteri**. In Copertura della sottoscrizione è riportato il numero di sottoscrizioni di tipo Standard, Gratuito e “senza copertura”. Le sottoscrizioni "senza copertura" non hanno abilitato il Centro sicurezza e non vengono visualizzate in **Gestione dei criteri**.
  >

  Le colonne nella tabella mostrano:

 - **Assegnazione dell'iniziativa relativa ai criteri**: iniziative e [criteri predefiniti](security-center-policy-definitions.md) del Centro sicurezza assegnati a una sottoscrizione o a un gruppo di gestione.
 - **Conformità**: punteggio di conformità complessiva per un gruppo di gestione, una sottoscrizione o un'area di lavoro. Il punteggio è la media ponderata delle assegnazioni. La media ponderata si scompone nel numero di criteri in una singola assegnazione e nel numero di risorse a cui si applica l'assegnazione.

 Ad esempio, se la sottoscrizione dispone di due macchine virtuali e un'iniziativa con cinque criteri assegnati, sono presenti 10 valutazioni nella sottoscrizione. Se una delle macchine virtuali non è conforme a due dei criteri, il punteggio di conformità complessiva dell'assegnazione della sottoscrizione è pari all'80%.

 - **Copertura**: identifica il piano tariffario, Gratuito o Standard, in cui viene eseguito il gruppo di gestione, la sottoscrizione o l'area di lavoro.  Per altre informazioni sui piani tariffari di Centro sicurezza, vedere [Prezzi](security-center-pricing.md).
 - **Impostazioni**: per le sottoscrizioni è disponibile il collegamento **Modifica impostazioni**. Selezionando **Modifica impostazioni**, è possibile aggiornare le [impostazioni del Centro sicurezza](security-center-policies-overview.md) per ogni sottoscrizione o gruppo di gestione.

2. Selezionare la sottoscrizione o il gruppo di gestione di cui si vogliono visualizzare i criteri.

  - La schermata **Criteri di sicurezza** rispecchia l'azione eseguita dai criteri assegnati nella sottoscrizione o nel gruppo di gestione selezionato.
  - Nella parte superiore usare i collegamenti forniti per aprire ogni **assegnazione** dei criteri che si applica alla sottoscrizione o al gruppo di gestione. È possibile usare i collegamenti per accedere all'assegnazione e modificare o disabilitare i criteri. Se ad esempio si nota che l'assegnazione di un determinato criterio rifiuta di fatto la protezione di un endpoint, è possibile usare il collegamento per accedere al criterio e modificarlo o disabilitarlo.
  - Nell'elenco dei criteri è possibile visualizzare l'applicazione effettiva del criterio nella sottoscrizione o nel gruppo di gestione. Ciò significa che vengono prese in esame le impostazioni di ogni criterio che si applicano all'ambito e viene fornito il risultato cumulativo dell'azione che viene eseguita dal criterio. Se ad esempio in un'assegnazione il criterio è disabilitato, ma in un'altra è impostato su AuditIfNotExist, l'effetto cumulativo applica AuditIfNotExist. L'effetto più attivo ha sempre la precedenza.
  - L'effetto dei criteri può essere: Append, Audit, AuditIfNotExists, Deny, DeployIfNotExists, Disabled. Per altre informazioni su come vengono applicati gli effetti, vedere [Informazioni sugli effetti di Criteri](../governance/policy/concepts/effects.md).

   ![Schermata dei criteri](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> - Quando si visualizzano i criteri assegnati, è possibile visualizzare più assegnazioni e anche come è configurata ogni singola assegnazione.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come configurare i criteri di sicurezza nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md): informazioni sulla pianificazione e considerazioni di progettazione per il Centro sicurezza di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestire e rispondere agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitorare le soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): informazioni su come monitorare l'integrità delle soluzioni dei partner.
* [Gain tenant-wide visibility for Azure Security Center](security-center-management-groups.md) (Ottenere visibilità a livello di tenant per il Centro sicurezza di Azure): informazioni su come configurare i gruppi di gestione per il Centro sicurezza di Azure.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): risposte alle domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure.

Per altre informazioni su Criteri di Azure, vedere [Informazioni su Criteri di Azure](../azure-policy/azure-policy-introduction.md).
