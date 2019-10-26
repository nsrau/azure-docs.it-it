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
ms.date: 10/08/2019
ms.author: iainfou
ms.openlocfilehash: ffcff84c7778ec3d6395e1c7a706c0deb2a0dc90
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893426"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Criteri password e di blocco dell'account nei domini gestiti

Per gestire la sicurezza degli utenti in Azure Active Directory Domain Services (Azure AD DS), è possibile definire criteri specifici per le password che controllano le impostazioni di blocco degli account o la lunghezza e la complessità minime della password. Un criterio granulare per le password predefinito viene creato e applicato a tutti gli utenti in un dominio gestito Azure AD DS. Per fornire un controllo granulare e soddisfare specifiche esigenze aziendali o di conformità, è possibile creare criteri aggiuntivi e applicarli a gruppi di utenti specifici.

Questo articolo illustra come creare e configurare un criterio granulare per le password in Azure AD DS usando il Centro di amministrazione di Active Directory.

> [!NOTE]
> I criteri password sono disponibili solo per i domini gestiti Azure AD DS creati usando il modello di distribuzione Gestione risorse. Per i domini gestiti meno recenti creati con la distribuzione classica, [eseguire la migrazione dal modello di rete virtuale classica a gestione risorse][migrate-from-classic].

## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
  * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
  * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
  * Se necessario, completare l'esercitazione per [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
  * È necessario che l'istanza di Azure AD DS sia stata creata utilizzando il modello di distribuzione Gestione risorse. Se necessario, [eseguire la migrazione dal modello di rete virtuale classica a gestione risorse][migrate-from-classic].
* Una macchina virtuale di gestione di Windows Server aggiunta al dominio gestito di Azure AD DS.
  * Se necessario, completare l'esercitazione per [creare una macchina virtuale di gestione][tutorial-create-management-vm].
* Un account utente membro del gruppo di *amministratori dei controller di dominio di Azure AD* nel tenant di Azure AD.

## <a name="default-password-policy-settings"></a>Impostazioni predefinite dei criteri password

I criteri granulari per le password (vengono mostrate) consentono di applicare restrizioni specifiche per i criteri di blocco di password e account a utenti diversi in un dominio. Ad esempio, per proteggere gli account con privilegi è possibile applicare impostazioni di blocco degli account più restrittive rispetto agli account senza privilegi regolari. È possibile creare più vengono mostrate all'interno di un dominio gestito Azure AD DS e specificare l'ordine di priorità per applicarli agli utenti.

I criteri vengono distribuiti tramite l'associazione di gruppo in un dominio gestito di Azure AD DS e tutte le modifiche apportate vengono applicate al successivo accesso dell'utente. La modifica dei criteri non comporta lo sblocco di un account utente già bloccato.

I criteri password si comportano in modo leggermente diverso a seconda del modo in cui è stato creato l'account utente a cui sono stati applicati. In Azure AD DS è possibile creare un account utente in due modi:

* L'account utente può essere sincronizzato da Azure AD. Sono inclusi gli account utente solo cloud creati direttamente in Azure e gli account utente ibridi sincronizzati da un ambiente Active Directory Domain Services locale usando Azure AD Connect.
    * La maggior parte degli account utente in Azure AD DS vengono creati tramite il processo di sincronizzazione da Azure AD.
* L'account utente può essere creato manualmente in un dominio gestito Azure AD DS e non esiste nel Azure AD.

Tutti gli utenti, indipendentemente dal modo in cui sono stati creati, presentano i criteri di blocco degli account seguenti applicati dai criteri password predefiniti in Azure AD DS:

* **Durata blocco account:** 30
* **Numero di tentativi di accesso non riusciti consentiti:** 5
* Il **conteggio tentativi di accesso non riusciti è stato reimpostato dopo:** 30 minuti
* **Validità massima password (durata):** 90 giorni

Con queste impostazioni predefinite, gli account utente vengono bloccati per 30 minuti se vengono usate cinque password non valide entro 2 minuti. Gli account vengono sbloccati automaticamente dopo 30 minuti.

I blocchi degli account si verificano solo nel dominio gestito. Gli account utente vengono bloccati solo in Azure AD DS e solo a causa di tentativi di accesso non riusciti sul dominio gestito. Gli account utente sincronizzati in da Azure AD o in locale non sono bloccati nelle directory di origine, ma solo in Azure AD DS.

Se si dispone di un criterio di Azure AD password che specifica una durata massima della password superiore a 90 giorni, la validità della password viene applicata ai criteri predefiniti in Azure AD DS. È possibile configurare criteri password personalizzati per definire una validità massima della password diversa in Azure AD DS. Prestare attenzione se la validità massima della password è configurata in un criterio di Azure AD password DS rispetto a Azure AD o a un ambiente di servizi di dominio Active Directory locale. In questo scenario, la password di un utente può scadere in Azure AD DS prima che venga richiesto di modificare Azure AD in un ambiente di servizi di dominio Active Directory locale.

Per gli account utente creati manualmente in un dominio gestito Azure AD DS, vengono applicate anche le impostazioni di password aggiuntive seguenti dal criterio predefinito. Queste impostazioni non si applicano agli account utente sincronizzati da Azure AD, perché un utente non può aggiornare la propria password direttamente in Azure AD DS.

* **Lunghezza minima password (caratteri):** 7
* **Le password devono soddisfare i requisiti di complessità**

Non è possibile modificare le impostazioni di blocco account o password nei criteri password predefiniti. Al contrario, i membri del gruppo *AAD DC Administrators* possono creare criteri password personalizzati e configurarli per sostituire (avere la precedenza rispetto a) i criteri predefiniti predefiniti, come illustrato nella sezione successiva.

## <a name="create-a-custom-password-policy"></a>Creare criteri password personalizzati

Quando si compilano ed eseguono applicazioni in Azure, è possibile configurare un criterio personalizzato per le password. Ad esempio, è possibile creare un criterio per impostare diverse impostazioni dei criteri di blocco degli account.

I criteri password personalizzati vengono applicati ai gruppi in un dominio gestito Azure AD DS. Questa configurazione sostituisce in modo efficace i criteri predefiniti.

Per creare un criterio personalizzato per le password, usare gli strumenti di amministrazione Active Directory da una macchina virtuale aggiunta a un dominio. Il Centro di amministrazione di Active Directory consente di visualizzare, modificare e creare risorse in un dominio gestito Azure AD DS, incluse le unità organizzative.

> [!NOTE]
> Per creare un criterio personalizzato per le password in un dominio gestito di Azure AD DS, è necessario avere eseguito l'accesso a un account utente membro del gruppo di *amministratori di AAD DC* .

1. Dalla schermata Start selezionare strumenti di **Amministrazione**. Viene visualizzato un elenco di strumenti di gestione disponibili che sono stati installati nell'esercitazione per [creare una macchina virtuale di gestione][tutorial-create-management-vm].
1. Per creare e gestire le unità organizzative, selezionare **centro di amministrazione di Active Directory** dall'elenco di strumenti di amministrazione.
1. Nel riquadro sinistro scegliere il dominio gestito di Azure AD DS, ad esempio *contoso.com*.
1. Aprire il contenitore di **sistema** , quindi il **contenitore Impostazioni password**.

    Viene visualizzato un criterio di password predefinito per il dominio gestito Azure AD DS. Non è possibile modificare questo criterio predefinito. In alternativa, creare un criterio password personalizzato per sostituire i criteri predefiniti.

    ![Creare un criterio password nell'Centro di amministrazione di Active Directory](./media/password-policy/create-password-policy-adac.png)

1. Nel pannello **attività** a destra selezionare **nuovo > Impostazioni password**.
1. Nella finestra di dialogo **Crea impostazioni password** immettere un nome per il criterio, ad esempio *MyCustomFGPP*.
1. Quando sono presenti più criteri password, a un utente vengono applicati i criteri con la precedenza più alta o la priorità. Più è basso il numero, maggiore sarà la priorità. La priorità dei criteri password predefiniti è *200*.

    Impostare la precedenza per i criteri password personalizzati per sostituire il valore predefinito, ad esempio *1*.

1. Modificare le altre impostazioni dei criteri password nel modo desiderato. Tenere presente i seguenti punti chiave:

    * Impostazioni come la complessità della password, l'età o la scadenza solo per gli utenti creati manualmente in un dominio gestito Azure AD DS.
    * Le impostazioni di blocco degli account si applicano a tutti gli utenti, ma hanno effetto solo nel dominio gestito.

    ![Creare criteri specifici per le password personalizzati](./media/how-to/custom-fgpp.png)

1. Deselezionare **Proteggi da eliminazioni accidentali**. Se questa opzione è selezionata, non è possibile salvare il FGPP.
1. Nella sezione **si applica direttamente a** selezionare il pulsante **Aggiungi** . Nella finestra di dialogo **Seleziona utenti o gruppi** fare clic sul pulsante **Località**.

    ![Selezionare gli utenti e i gruppi ai quali applicare i criteri password](./media/how-to/fgpp-applies-to.png)

1. È possibile applicare i criteri password solo ai gruppi. Nella finestra di dialogo **locations (percorsi** ) espandere il nome di dominio, ad esempio *contoso.com*, quindi selezionare un'unità organizzativa, ad esempio **aaddc computers users**. Se si dispone di un'unità organizzativa personalizzata che contiene un gruppo di utenti che si desidera applicare, selezionare tale unità organizzativa.

    ![Selezionare l'unità organizzativa a cui appartiene il gruppo](./media/how-to/fgpp-container.png)

1. Digitare il nome del gruppo a cui si desidera applicare il criterio, quindi selezionare **Controlla nomi** per convalidare l'esistenza del gruppo.

    ![Cercare e selezionare il gruppo da applicare FGPP](./media/how-to/fgpp-apply-group.png)

1. Con il nome del gruppo selezionato ora visualizzato in **applica direttamente alla** sezione, selezionare **OK** per salvare i criteri password personalizzati.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui criteri per le password e sull'utilizzo di Active Directory Administration Center, vedere gli articoli seguenti:

* [Informazioni sui criteri granulari per le password](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Configurare i criteri granulari per le password usando l'interfaccia di amministrazione di Active Directory](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
