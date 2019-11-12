---
title: Uso dei criteri di sicurezza | Microsoft Docs
description: Questo articolo descrive come usare i criteri di sicurezza nel centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 45a27be400753b56c42310a340334feba8a420c5
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2019
ms.locfileid: "73906878"
---
# <a name="working-with-security-policies"></a>Utilizzo dei criteri di sicurezza

Questo articolo illustra come vengono configurati i criteri di sicurezza e come visualizzarli nel Centro sicurezza. 

## <a name="introduction-to-security-policies"></a>Introduzione ai criteri di sicurezza

Un criterio di sicurezza definisce la configurazione desiderata dei carichi di lavoro e contribuisce a garantire la conformità ai requisiti di sicurezza della società o dei regolatori.

Il Centro sicurezza di Azure apporta le raccomandazioni sulla sicurezza in base ai criteri scelti. I criteri del Centro sicurezza si basano sulle iniziative dei criteri create in criteri di Azure. È possibile usare i [criteri di Azure](../governance/policy/overview.md) per gestire i criteri e impostare i criteri tra i gruppi di gestione e tra più sottoscrizioni.

Il Centro sicurezza offre le opzioni seguenti per l'uso dei criteri di sicurezza:

* **Visualizzare e modificare i criteri predefiniti predefiniti** : quando si Abilita il Centro sicurezza, un'iniziativa predefinita denominata ' ASC default ' viene assegnata automaticamente a tutte le sottoscrizioni registrate del Centro sicurezza (livelli gratuito o standard). Per personalizzare questa iniziativa, è possibile abilitare o disabilitare singoli criteri al suo interno. Per informazioni sulle opzioni disponibili, vedere l'elenco dei [criteri di sicurezza predefiniti](security-center-policy-definitions.md) .

* **Aggiungere criteri personalizzati** : se si desidera personalizzare le iniziative di sicurezza applicate alla sottoscrizione, è possibile eseguire questa operazione nel centro sicurezza. Si riceveranno quindi consigli se i computer non seguono i criteri creati. Per istruzioni sulla creazione e l'assegnazione di criteri personalizzati, vedere [uso dei criteri di sicurezza personalizzati](custom-security-policies.md).

* **Aggiungere i criteri di conformità normativi** : il dashboard di conformità normativa del Centro sicurezza Mostra lo stato di tutte le valutazioni all'interno dell'ambiente nel contesto di uno standard o di una norma particolare, ad esempio Azure CIS, NIST SP 800-53 R4, Swift CSP CSCF-V2020). Per altre informazioni, vedi [Migliorare la conformità alle normative](security-center-compliance-dashboard.md).


## <a name="managing-your-security-policies"></a>Gestione dei criteri di sicurezza

Per visualizzare i criteri di sicurezza nel Centro sicurezza:

1. Nel dashboard del **Centro sicurezza** selezionare **Criteri di sicurezza**.

    ![Riquadro Gestione dei criteri](./media/security-center-policies/security-center-policy-mgt.png)

   Nella schermata **Gestione dei criteri** è possibile visualizzare il numero dei gruppi di gestione, delle sottoscrizioni e delle aree di lavoro, oltre alla struttura dei gruppi di gestione.

1. Selezionare la sottoscrizione o il gruppo di gestione di cui si vogliono visualizzare i criteri.

1. Verrà visualizzata la pagina Criteri di sicurezza per la sottoscrizione o il gruppo di gestione. Mostra i criteri disponibili e assegnati.

   ![Schermata dei criteri](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > Se è presente un'etichetta "MG ereditato" insieme ai criteri predefiniti, significa che il criterio è stato assegnato a un gruppo di gestione e ereditato dalla sottoscrizione che si sta visualizzando.


1. Scegliere tra le opzioni disponibili in questa pagina:

    1. Per usare i criteri di settore, fare clic su **Aggiungi altri standard**. Per altre informazioni, vedere [aggiornare i pacchetti di conformità dinamici](update-regulatory-compliance-packages.md).

    1. Per assegnare e gestire le iniziative personalizzate, fare clic su **Aggiungi iniziative personalizzate**. Per ulteriori informazioni, vedere [utilizzo dei criteri di sicurezza personalizzati](custom-security-policies.md).

    1. Per visualizzare e modificare i criteri predefiniti, fare clic su **Visualizza criterio effettivo** e procedere come descritto di seguito. 

       ![Schermata dei criteri](./media/security-center-policies/policy-screen.png)
       
       Questa schermata dei **criteri di sicurezza** riflette l'azione eseguita dai criteri assegnati alla sottoscrizione o al gruppo di gestione selezionato.
       
       * Usare i collegamenti nella parte superiore per aprire un' **assegnazione** dei criteri che si applica alla sottoscrizione o al gruppo di gestione. Questi collegamenti consentono di accedere all'assegnazione e modificare o disabilitare i criteri. Se, ad esempio, si nota che un'assegnazione di criteri specifica nega effettivamente Endpoint Protection, utilizzare il collegamento per modificare o disabilitare i criteri.
       
       * Nell'elenco dei criteri è possibile visualizzare l'applicazione effettiva del criterio nella sottoscrizione o nel gruppo di gestione. Vengono prese in considerazione le impostazioni di ogni criterio applicato all'ambito e viene visualizzato il risultato cumulativo delle azioni eseguite dal criterio. Se, ad esempio, in un'assegnazione dei criteri è disabilitata, ma in un'altra è impostata su AuditIfNotExist, l'effetto cumulativo si applica a AuditIfNotExist. L'effetto più attivo ha sempre la precedenza.
       
       * L'effetto dei criteri può essere: Append, Audit, AuditIfNotExists, Deny, DeployIfNotExists, Disabled. Per altre informazioni su come vengono applicati gli effetti, vedere [Informazioni sugli effetti di Criteri](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > Quando si visualizzano i criteri assegnati, è possibile visualizzare più assegnazioni e anche come è configurata ogni singola assegnazione.


## <a name="who-can-edit-security-policies"></a>Utenti che possono modificare i criteri di sicurezza

È possibile modificare i criteri di sicurezza tramite il portale Criteri di Azure, l'API REST o Windows PowerShell.

Il Centro sicurezza usa il controllo degli accessi in base al ruolo, che fornisce ruoli predefiniti assegnabili a utenti, gruppi e servizi in Azure. Quando un utente apre il Centro sicurezza, visualizza solo informazioni correlate alle risorse a cui ha accesso. Il che significa che agli utenti viene assegnato il ruolo di *proprietario*, *collaboratore*o *lettore* alla sottoscrizione della risorsa. Oltre a questi ruoli, esistono due ruoli specifici del Centro sicurezza:

- **Ruolo con autorizzazioni di lettura per la sicurezza**: l'utente con questo ruolo dispone dei diritti di visualizzazione per il Centro sicurezza, inclusi avvisi, criteri, raccomandazioni e integrità, ma non può apportare modifiche.
- **Amministratore della sicurezza**: l'utente con questo ruolo dispone degli stessi diritti di visualizzazione del *ruolo con autorizzazioni di lettura per la sicurezza*, ma può anche aggiornare i criteri di sicurezza e ignorare raccomandazioni e avvisi.


## <a name="disable-security-policies"></a>Disabilitare i criteri di sicurezza
Se i criteri di sicurezza predefiniti generano una raccomandazione non pertinente per l'ambiente in uso, è possibile arrestarla disabilitando la definizione dei criteri che invia la raccomandazione.
Per ulteriori informazioni sulle raccomandazioni, vedere [gestione delle raccomandazioni sulla sicurezza](security-center-recommendations.md).

1. Nella sezione **policy & Compliance** del Centro sicurezza fare clic su **criteri di sicurezza**.

   ![gestione dei criteri](./media/tutorial-security-policy/policy-management.png)

2. Fare clic sulla sottoscrizione o sul gruppo di gestione per il quale si desidera disabilitare la raccomandazione.

   > [!NOTE]
   > Tenere presente che un gruppo di gestione applica i criteri alle relative sottoscrizioni. Se pertanto si disabilitano i criteri di una sottoscrizione e la sottoscrizione appartiene a un gruppo di gestione che utilizza ancora gli stessi criteri, si continuerà a ricevere le raccomandazioni relative ai criteri. I criteri verranno comunque applicati dal livello di gestione e le raccomandazioni verranno comunque generate.

1. Fare clic su **Visualizza criterio valido**.

   ![disabilitare i criteri](./media/tutorial-security-policy/view-effective-policy.png)

1. Fare clic sul criterio assegnato.

   ![disabilitare i criteri](./media/tutorial-security-policy/security-policy.png)

1. Nella sezione **parametri** cercare i criteri che richiamano la raccomandazione che si desidera disabilitare e nell'elenco a discesa selezionare **disabilitato**

   ![disabilitare i criteri](./media/tutorial-security-policy/disable-policy.png)

1. Fare clic su **Save**.

   > [!NOTE]
   > Per rendere effettive le modifiche ai criteri di disabilitazione possono essere necessarie fino a 12 ore.



## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono stati illustrati i criteri di sicurezza. Per informazioni correlate, vedere gli articoli seguenti:

* Per istruzioni su come impostare i criteri usando PowerShell, vedere [Guida introduttiva: creare un'assegnazione di criteri per identificare le risorse non conformi usando il modulo Azure PowerShell](../governance/policy/assign-policy-powershell.md)

* Per istruzioni su come modificare i criteri di sicurezza in criteri di Azure, vedere [creare e gestire criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md).

* Per istruzioni su come impostare un criterio tra le sottoscrizioni o i gruppi di gestione usando criteri di Azure, vedere informazioni su [criteri di Azure.](../governance/policy/overview.md)