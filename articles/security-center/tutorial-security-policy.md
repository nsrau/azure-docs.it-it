---
title: Uso dei criteri di sicurezza | Microsoft Docs
description: Questo articolo descrive come usare i criteri di sicurezza nel Centro sicurezza di Azure.This article describes how to work with security policies in Azure Security Center.
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
ms.openlocfilehash: c98ae7c95ac3fc186786612dd3d8d8bd55fa816f
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024881"
---
# <a name="working-with-security-policies"></a>Utilizzo dei criteri di sicurezza

Questo articolo illustra come vengono configurati i criteri di sicurezza e come visualizzarli nel Centro sicurezza. 

## <a name="introduction-to-security-policies"></a>Introduzione ai criteri di sicurezza

I criteri di sicurezza definiscono la configurazione desiderata dei carichi di lavoro e consentono di garantire la conformità ai requisiti di sicurezza dell'azienda o delle autorità di regolamentazione.

Il Centro sicurezza di Azure fornisce i consigli di sicurezza in base ai criteri scelti. I criteri del Centro sicurezza si basano sulle iniziative dei criteri create in Criteri di Azure.Security Center policies are based on policy initiatives created in Azure Policy. È possibile usare Criteri di [Azure](../governance/policy/overview.md) per gestire i criteri e impostare criteri tra gruppi di gestione e tra più sottoscrizioni.

Il Centro sicurezza offre le opzioni seguenti per l'utilizzo dei criteri di sicurezza:

* **Visualizzare e modificare i criteri predefiniti predefiniti:** quando si abilita il Centro sicurezza, un'iniziativa predefinita denominata "Impostazione predefinita ASC" viene assegnata automaticamente a tutte le sottoscrizioni registrate del Centro sicurezza (livelli gratuito o Standard). Per personalizzare questa iniziativa, è possibile abilitare o disabilitare singoli criteri al suo interno. Vedere l'elenco dei criteri di [sicurezza predefiniti](security-center-policy-definitions.md) per comprendere le opzioni disponibili.

* **Aggiungere criteri personalizzati:** se si desidera personalizzare le iniziative di sicurezza applicate alla sottoscrizione, è possibile farlo nel Centro sicurezza. Si riceveranno quindi suggerimenti se i computer non seguono i criteri creati. Per istruzioni sulla creazione e l'assegnazione di criteri personalizzati, vedere Utilizzo di criteri di [sicurezza personalizzati.](custom-security-policies.md)

* Aggiungere criteri di **conformità alle normative:** il dashboard di conformità normativa del Centro sicurezza mostra lo stato di tutte le valutazioni all'interno dell'ambiente nel contesto di uno standard o di una regolamentazione particolare (ad esempio Azure CIS, NIST SP 800-53 R4, SWIFT CSP CSCF-v2020). Per ulteriori informazioni, vedere [Migliorare la conformità alle normative](security-center-compliance-dashboard.md).


## <a name="managing-your-security-policies"></a>Gestione dei criteri di sicurezza

Per visualizzare i criteri di sicurezza nel Centro sicurezza:

1. Nel dashboard del **Centro sicurezza** selezionare **Criteri di sicurezza**.

    ![Riquadro Gestione dei criteri](./media/security-center-policies/security-center-policy-mgt.png)

   Nella schermata **Gestione dei criteri** è possibile visualizzare il numero dei gruppi di gestione, delle sottoscrizioni e delle aree di lavoro, oltre alla struttura dei gruppi di gestione.

1. Selezionare la sottoscrizione o il gruppo di gestione di cui si vogliono visualizzare i criteri.

1. Viene visualizzata la pagina dei criteri di sicurezza per la sottoscrizione o il gruppo di gestione. Mostra i criteri disponibili e assegnati.

   ![Schermata dei criteri](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > Se accanto al criterio predefinito è presente l'etichetta "MG ereditata", significa che il criterio è stato assegnato a un gruppo di gestione ed ereditato dalla sottoscrizione visualizzata.


1. Scegli tra le opzioni disponibili in questa pagina:

    1. Per utilizzare i criteri di settore, selezionare **Aggiungi altri standard**. Per ulteriori informazioni, consultate [Aggiornamento ai pacchetti di conformità dinamica.](update-regulatory-compliance-packages.md)

    1. Per assegnare e gestire iniziative personalizzate, selezionare **Aggiungi iniziative personalizzate**. Per ulteriori informazioni, vedere [Utilizzo di criteri](custom-security-policies.md)di sicurezza personalizzati .

    1. Per visualizzare e modificare il criterio predefinito, selezionare **Visualizza criteri effettivi** e procedere come descritto di seguito. 

       ![Schermata dei criteri](./media/security-center-policies/policy-screen.png)
       
       Questa schermata Criteri di **sicurezza** riflette l'azione eseguita dai criteri assegnati alla sottoscrizione o al gruppo di gestione selezionato.
       
       * Usare i collegamenti nella parte superiore per aprire **un'assegnazione** di criteri applicabile alla sottoscrizione o al gruppo di gestione. Questi collegamenti consentono di accedere all'assegnazione e modificare o disabilitare il criterio. Ad esempio, se si nota che una particolare assegnazione di criteri nega effettivamente la protezione degli endpoint, utilizzare il collegamento per modificare o disabilitare il criterio.
       
       * Nell'elenco dei criteri è possibile visualizzare l'applicazione effettiva del criterio nella sottoscrizione o nel gruppo di gestione. Vengono prese in considerazione le impostazioni di ogni criterio che si applicano all'ambito e viene visualizzato il risultato cumulativo delle azioni intraprese dal criterio. Ad esempio, se in un'assegnazione del criterio è disabilitata, ma in un'altra è impostata su AuditIfNotExist, l'effetto cumulativo applica AuditIfNotExist. L'effetto più attivo ha sempre la precedenza.
       
       * L'effetto dei criteri può essere: Append, Audit, AuditIfNotExists, Deny, DeployIfNotExists, Disabled. Per altre informazioni su come vengono applicati gli effetti, vedere [Informazioni sugli effetti di Criteri](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > Quando si visualizzano i criteri assegnati, è possibile visualizzare più assegnazioni e anche come è configurata ogni singola assegnazione.


## <a name="who-can-edit-security-policies"></a>Utenti che possono modificare i criteri di sicurezza

È possibile modificare i criteri di sicurezza tramite il portale Criteri di Azure, l'API REST o Windows PowerShell.

Il Centro sicurezza usa il controllo degli accessi in base al ruolo, che fornisce ruoli predefiniti assegnabili a utenti, gruppi e servizi in Azure. Quando un utente apre il Centro sicurezza, visualizza solo informazioni correlate alle risorse a cui ha accesso. Ciò significa che agli utenti viene assegnato il ruolo di *proprietario,* *collaboratore*o *lettore* alla sottoscrizione della risorsa. Oltre a questi ruoli, esistono due ruoli specifici del Centro sicurezza:As as these roles, there are two specific Security Center roles:

- **Lettore di sicurezza:** dispone dei diritti di visualizzazione per il Centro sicurezza, che include consigli, avvisi, criteri e integrità, ma non possono apportare modifiche.
- **Amministratore della sicurezza**: disporre degli stessi diritti di visualizzazione del lettore di *sicurezza*e possono inoltre aggiornare i criteri di sicurezza e ignorare i suggerimenti e gli avvisi.


## <a name="disable-security-policies"></a>Disabilitare i criteri di sicurezzaDisable security policies
Se i criteri di sicurezza predefiniti generano una raccomandazione non rilevante per l'ambiente, è possibile interromperla disabilitando la definizione dei criteri che invia la raccomandazione.
Per ulteriori informazioni sui suggerimenti, vedere [Gestione dei suggerimenti per](security-center-recommendations.md)la sicurezza .

1. Nel Centro sicurezza selezionare **Criteri di sicurezza**nella sezione Criteri & **Conformità.**

   ![gestione delle politiche](./media/tutorial-security-policy/policy-management.png)

2. Selezionare la sottoscrizione o il gruppo di gestione per cui si vuole disabilitare il suggerimento.

   > [!NOTE]
   > Tenere presente che un gruppo di gestione applica i propri criteri alle relative sottoscrizioni. Pertanto, se si disabilita un criterio di una sottoscrizione e la sottoscrizione appartiene a un gruppo di gestione che usa ancora lo stesso criterio, si continuerà a ricevere le raccomandazioni relative al criterio in questione. Il criterio verrà comunque applicato dal livello di gestione e le raccomandazioni continueranno a essere generate.

1. Selezionare **Visualizza criterio effettivo**.

   ![disattivare il criterio](./media/tutorial-security-policy/view-effective-policy.png)

1. Selezionare il criterio assegnato.

   ![disattivare il criterio](./media/tutorial-security-policy/security-policy.png)

1. Nella sezione **PARAMETERS** cercare il criterio che richiama la raccomandazione che si desidera disabilitare e nell'elenco a discesa selezionare **Disabilitato**

   ![disattivare il criterio](./media/tutorial-security-policy/disable-policy.png)

1. Selezionare **Salva**.

   > [!NOTE]
   > L'effetto di modifica dei criteri di disabilitazione può richiedere fino a 12 ore.



## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono stati appresi i criteri di sicurezza. Per informazioni correlate, vedere gli articoli seguenti:For related information, see the following articles:

* Per istruzioni su come impostare i criteri usando PowerShell, vedere Guida introduttiva: Creare un'assegnazione di criteri per identificare le risorse non conformi usando il modulo di Azure PowerShellFor instructions on how to set policies using PowerShell, [see Quickstart: Create a policy assignment to identify non-compliant resources using the Azure PowerShell module](../governance/policy/assign-policy-powershell.md)

* Per istruzioni su come modificare i criteri di sicurezza in Criteri di Azure, vedere [Creare e gestire criteri per applicare la conformità.](../governance/policy/tutorials/create-and-manage.md)

* Per istruzioni su come impostare un criterio tra sottoscrizioni o gruppi di gestione usando Criteri di Azure, vedere [Che cos'è Criteri](../governance/policy/overview.md) di Azure?