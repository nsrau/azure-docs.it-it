---
title: Creare e usare criteri password in Azure AD Domain Services | Microsoft Docs
description: Informazioni su come e perché usare i criteri granulari per le password per proteggere e controllare le password degli account in un dominio gestito di Azure AD DS.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 08/08/2019
ms.author: iainfou
ms.openlocfilehash: 45fb2daaeaf9ee788207d43d805e070320372ca0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617165"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Criteri password e di blocco dell'account nei domini gestiti

Per gestire la sicurezza degli account in Azure Active Directory Domain Services (Azure AD DS), è possibile definire criteri specifici per le password che controllano impostazioni quali lunghezza minima della password, ora di scadenza della password o complessità della password. Un criterio password predefinito viene applicato a tutti gli utenti in un dominio gestito Azure AD DS. Per fornire un controllo granulare e soddisfare specifiche esigenze aziendali o di conformità, è possibile creare criteri aggiuntivi e applicarli a gruppi di utenti specifici.

Questo articolo illustra come creare e configurare un criterio granulare per le password usando il Centro di amministrazione di Active Directory.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
  * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o solo cloud.
  * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un Azure Active Directory Domain Services dominio gestito abilitato e configurato nel tenant del Azure AD.
  * Se necessario, completare l'esercitazione per [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Una macchina virtuale di gestione di Windows Server aggiunta al dominio gestito di Azure AD DS.
  * Se necessario, completare l'esercitazione per [creare una macchina virtuale di gestione][tutorial-create-management-vm].
* Un account utente membro del gruppo *amministratori Azure ad controller* di dominio nel tenant del Azure ad.

## <a name="fine-grained-password-policies-fgpp-overview"></a>Panoramica di criteri specifici per le password (FGPP)

I criteri granulari per le password (vengono mostrate) consentono di applicare restrizioni specifiche per i criteri di blocco di password e account a utenti diversi in un dominio. Per proteggere gli account con privilegi, ad esempio, è possibile applicare impostazioni di password più restrittive rispetto agli account con privilegi regolari. È possibile creare più vengono mostrate per specificare i criteri password in un dominio gestito di Azure AD DS.

Le impostazioni della password seguenti possono essere configurate con FGPP:

* Lunghezza minima password
* Cronologia delle password
* Le password devono soddisfare i requisiti di complessità
* Validità minima password
* Validità massima password
* Criteri di blocco account
  * Durata del blocco account
  * Numero di tentativi di accesso non riusciti consentiti
  * Reimpostare il numero di tentativi di accesso non riusciti dopo

FGPP influiscono solo sugli utenti creati in Azure AD DS. Gli utenti del cloud e gli utenti del dominio sincronizzati nel dominio gestito di Azure AD DS da Azure AD non sono interessati dai criteri per le password.

I criteri vengono distribuiti tramite l'associazione di gruppo nel dominio gestito di Azure AD DS e tutte le modifiche apportate vengono applicate al successivo accesso dell'utente. La modifica dei criteri non comporta lo sblocco di un account utente già bloccato.

## <a name="default-fine-grained-password-policy-settings"></a>Impostazioni predefinite dei criteri per le password con granularità fine

In un dominio gestito Azure AD DS, i criteri password seguenti sono configurati per impostazione predefinita e applicati a tutti gli utenti:

* **Lunghezza minima password (caratteri):** 7
* **Validità massima password (durata):** 90 giorni
* **Le password devono soddisfare i requisiti di complessità**

Per impostazione predefinita, vengono configurati i criteri di blocco degli account seguenti:

* **Durata blocco account:** 30
* **Numero di tentativi di accesso non riusciti consentiti:** 5
* **Reimposta i tentativi di accesso non riusciti dopo:** 30 minuti

Con queste impostazioni predefinite, gli account utente vengono bloccati per 30 minuti se vengono usate cinque password non valide entro 2 minuti. Gli account vengono sbloccati automaticamente dopo 30 minuti.

Non è possibile modificare o eliminare i criteri granulari predefiniti per le password. Al contrario, i membri del gruppo *AAD DC Administrators* possono creare un FGPP personalizzato e configurarlo in modo da eseguire l'override (avere la precedenza rispetto a) la FGPP incorporata predefinita, come illustrato nella sezione successiva.

## <a name="create-a-custom-fine-grained-password-policy"></a>Creare criteri specifici per le password personalizzati

Durante la compilazione di applicazioni e in Azure, può essere necessario configurare un FGPP personalizzato. Alcuni esempi della necessità di creare un FGPP personalizzato includono per impostare un criterio di blocco degli account diverso o per configurare un'impostazione predefinita per la durata della password per il dominio gestito.

È possibile creare un FGPP personalizzato e applicarlo a gruppi specifici nel dominio gestito di Azure AD DS. Questa configurazione esegue l'override del valore predefinito di FGPP. È anche possibile creare criteri specifici per le password personalizzati e applicarli a tutte le ou personalizzate create nel dominio gestito di Azure AD DS.

Per creare un criterio granulare per le password, usare gli strumenti di amministrazione Active Directory da una macchina virtuale aggiunta a un dominio. Il Centro di amministrazione di Active Directory consente di visualizzare, modificare e creare risorse in un dominio gestito Azure AD DS, incluse le unità organizzative.

> [!NOTE]
> Per creare un criterio granulare per le password in un dominio gestito di Azure AD DS, è necessario avere eseguito l'accesso a un account utente membro del gruppo di *amministratori di AAD DC* .

1. Dalla schermata Start selezionare strumenti di **Amministrazione**. Viene visualizzato un elenco di strumenti di gestione disponibili che sono stati installati nell'esercitazione per [creare una macchina virtuale di gestione][tutorial-create-management-vm].
1. Per creare e gestire le unità organizzative, selezionare **centro di amministrazione di Active Directory** dall'elenco di strumenti di amministrazione.
1. Nel riquadro sinistro scegliere il dominio gestito di Azure AD DS, ad esempio *contoso.com*.
1. Nel pannello **attività** a destra selezionare **nuovo > Impostazioni password**.
1. Nella finestra di dialogo **Crea impostazioni password** immettere un nome per il criterio, ad esempio *MyCustomFGPP*. Impostare la precedenza su in modo appropriato per sostituire il valore predefinito di FGPP ( *200*), ad esempio *1*.

    Modificare le altre impostazioni dei criteri password in base alle esigenze, ad esempio **applica cronologia password** per richiedere all'utente di creare una password diversa dalle *24* password precedenti.

    ![Creare criteri specifici per le password personalizzati](./media/how-to/custom-fgpp.png)

1. Deselezionare **Proteggi da eliminazioni accidentali**. Se questa opzione è selezionata, non è possibile salvare il FGPP.
1. Nella sezione **si applica direttamente a** selezionare il pulsante **Aggiungi** . Nella finestra di dialogo **Seleziona utenti o gruppi** fare clic sul pulsante **Località**.

    ![Selezionare gli utenti e i gruppi ai quali applicare i criteri password](./media/how-to/fgpp-applies-to.png)

1. I criteri granulari per le password possono essere applicati solo ai gruppi. Nella finestra di dialogo locations ( **percorsi** ) espandere il nome di dominio, ad esempio *contoso.com*, quindi selezionare un'unità organizzativa, ad esempio **aaddc computers users**. Se si dispone di un'unità organizzativa personalizzata che contiene un gruppo di utenti che si desidera applicare, selezionare tale unità organizzativa.

    ![Selezionare l'unità organizzativa a cui appartiene il gruppo](./media/how-to/fgpp-container.png)

1. Digitare il nome del gruppo a cui si desidera applicare il criterio, quindi selezionare **Controlla nomi** per convalidare l'esistenza del gruppo.

    ![Cercare e selezionare il gruppo da applicare FGPP](./media/how-to/fgpp-apply-group.png)

1. Con il nome del gruppo selezionato ora visualizzato in **applica direttamente alla** sezione, selezionare **OK** per salvare i criteri password personalizzati.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui criteri granulari per le password e sull'utilizzo di Active Directory Administration Center, vedere gli articoli seguenti:

* [Informazioni sui criteri granulari per le password](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Configurare i criteri granulari per le password usando l'interfaccia di amministrazione di Active Directory](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
