---
title: Creare e usare i criteri password in Servizi di dominio Azure AD. Documenti Microsoft
description: Informazioni su come e perché usare criteri granulari per le password per proteggere e controllare le password degli account in un dominio gestito di Servizi di dominio Active Directory di Azure.Learn how and why to use fine-grained password policies to secure and control account passwords in an Azure AD DS managed domain.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: c4402c1ce2f051c8d1911e7c0332d4cac787ce1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613205"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Criteri password e di blocco dell'account nei domini gestiti

Per gestire la sicurezza degli utenti in Servizi di dominio Azure Active Directory, è possibile definire criteri granulari per le password che controllano le impostazioni di blocco degli account o la lunghezza e la complessità minime della password. Un criterio granulare predefinito per le password viene creato e applicato a tutti gli utenti in un dominio gestito di Azure AD DS.A default fineed password policy is created and applied to all users in an Azure AD DS managed domain. Per fornire un controllo granulare e soddisfare specifiche esigenze aziendali o di conformità, è possibile creare e applicare criteri aggiuntivi a gruppi di utenti specifici.

Questo articolo illustra come creare e configurare criteri granulari per le password in Servizi di dominio Active Directory usando il Centro di amministrazione di Active Directory.This article shows you how to create and configure a fine-grained password policy in Azure AD DS using the Active Directory Administrative Center.

> [!NOTE]
> I criteri password sono disponibili solo per i domini gestiti di Servizi di dominio Active Directory di Azure creati usando il modello di distribuzione di Resource Manager.Password policies are available only for Azure AD DS managed domains created using the Resource Manager deployment model. Per i domini gestiti meno recenti creati utilizzando Classic, eseguire la migrazione dal modello di [rete virtuale classica a Resource Manager][migrate-from-classic].

## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, sono necessarie le risorse e i privilegi seguenti:To complete this article, you need the following resources and privileges:

* Una sottoscrizione di Azure attiva.
  * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
  * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
  * Se necessario, completare l'esercitazione per [creare e configurare un'istanza][create-azure-ad-ds-instance]di Servizi di dominio Azure Active Directory.
  * L'istanza di Servizi di dominio Active Directory di Azure deve essere stata creata usando il modello di distribuzione di Resource Manager.The Azure AD DS instance must have been created using the Resource Manager deployment model. Se necessario, eseguire la migrazione dal modello di [rete virtuale classica a Resource Manager][migrate-from-classic].
* Una macchina virtuale di gestione di Windows Server aggiunta al dominio gestito di Servizi di dominio Active Directory di Azure.A Windows Server management VM that is joined to the Azure AD DS managed domain.
  * Se necessario, completare l'esercitazione per creare una macchina virtuale di [gestione.][tutorial-create-management-vm]
* Un account utente membro del gruppo di *amministratori dei controller di dominio di Azure AD* nel tenant di Azure AD.

## <a name="default-password-policy-settings"></a>Impostazioni predefinite dei criteri password

I criteri granulari per le password (FGPP) consentono di applicare restrizioni specifiche per i criteri di blocco di password e account a utenti diversi in un dominio. Ad esempio, per proteggere gli account con privilegi è possibile applicare impostazioni di blocco degli account più severe rispetto ai normali account senza privilegi. È possibile creare più FGPP all'interno di un dominio gestito di Azure AD DS e specificare l'ordine di priorità per applicarli agli utenti.

I criteri vengono distribuiti tramite l'associazione di gruppo in un dominio gestito di Servizi di dominio Active Directory di Azure e le modifiche apportate vengono applicate al successivo accesso utente. La modifica del criterio non sblocca un account utente già bloccato.

I criteri password si comportano in modo leggermente diverso a seconda di come è stato creato l'account utente a cui vengono applicati. In Azure AD DS è possibile creare un account utente in due modi:There are two ways a user account can be created in Azure AD DS:

* L'account utente può essere sincronizzato da Azure AD. Sono inclusi gli account utente solo cloud creati direttamente in Azure e gli account utente ibridi sincronizzati da un ambiente di Servizi di dominio Active Directory locale tramite Azure AD Connect.This includes cloud-only user accounts created directly in Azure, and hybrid user accounts synchronized from an on-premises AD DS environment using Azure AD Connect.
    * La maggior parte degli account utente in Servizi di dominio Active Directory di Azure viene creata tramite il processo di sincronizzazione da Azure AD.
* L'account utente può essere creato manualmente in un dominio gestito di Servizi di dominio Active Directory di Azure e non esiste in Azure AD.

A tutti gli utenti, indipendentemente da come vengono creati, i criteri di blocco account seguenti vengono applicati in base ai criteri password predefiniti in Servizi di dominio Active Directory di Azure:All users, regardless of how they're created, have the following account lockout policies applied by the default password policy in Azure AD DS:

* **Durata del blocco dell'account:** 30
* **Numero di tentativi di accesso non riusciti consentiti:** 5
* **Reimposta numero di tentativi di accesso non riusciti dopo:** 30 minuti
* **Validità massima password (durata):** 90 giorni

Con queste impostazioni predefinite, gli account utente vengono bloccati per 30 minuti se vengono utilizzate cinque password non valide entro 2 minuti. Gli account vengono sbloccati automaticamente dopo 30 minuti.

I blocchi degli account si verificano solo all'interno del dominio gestito. Gli account utente sono bloccati solo in Servizi di dominio Active Directory di Azure e solo a causa di tentativi di accesso non riusciti nel dominio gestito. Gli account utente sincronizzati da Azure AD o locali non sono bloccati nelle directory di origine, ma solo in Servizi di dominio Active Directory di Azure.User accounts that were synchronized in from Azure AD or on-premises aren't locked out in their source directories, only in Azure AD DS.

Se si dispone di criteri password di Azure AD che specificano una validità massima della password superiore a 90 giorni, tale validità viene applicata ai criteri predefiniti in Servizi di dominio Active Directory di Azure.If you have an Azure AD password policy that specifies a maximum password age greater than 90 days, that password age is applied to the default policy in Azure AD DS. È possibile configurare criteri password personalizzati per definire una validità massima diversa delle password in Servizi di dominio Active Directory di Azure.You can configure a custom password policy to define a different maximum password age in Azure AD DS. Prestare attenzione se si ha una validità massima della password più breve configurata in un criterio password di Servizi di dominio Active Directory di Azure rispetto ad Azure AD o in un ambiente Servizi di dominio Active Directory locale. In questo scenario, la password di un utente potrebbe scadere in Servizi di dominio Active Directory di Azure prima che venga richiesta di modifica in Azure AD o in un ambiente Servizi di dominio Active Directory locale.

Per gli account utente creati manualmente in un dominio gestito di Azure AD DS, le impostazioni della password aggiuntive seguenti vengono applicate anche dai criteri predefiniti. Queste impostazioni non si applicano agli account utente sincronizzati da Azure AD, poiché un utente non può aggiornare la password direttamente in Servizi di dominio Active Directory di Azure.These settings don't apply to user accounts synchronized in from Azure AD, as a user can't update their password directly in Azure AD DS.

* **Lunghezza minima password (caratteri):** 7
* **Le password devono soddisfare i requisiti di complessità**

Non è possibile modificare le impostazioni di blocco account o password nel criterio password predefinito. I membri del gruppo *AAD DC Administrators* possono invece creare criteri password personalizzati e configurarli per sostituire (precedenza) sui criteri predefiniti predefiniti, come illustrato nella sezione successiva.

## <a name="create-a-custom-password-policy"></a>Creare criteri password personalizzatiCreate a custom password policy

Durante la compilazione e l'esecuzione di applicazioni in Azure, è possibile configurare criteri password personalizzati. Ad esempio, è possibile creare un criterio per impostare impostazioni diverse dei criteri di blocco account.

I criteri password personalizzati vengono applicati ai gruppi in un dominio gestito di Servizi di dominio Active Directory di Azure.Custom password policies are applied to groups in an Azure AD DS managed domain. Questa configurazione sostituisce in modo efficace il criterio predefinito.

Per creare criteri password personalizzati, usare gli Strumenti di amministrazione di Active Directory da una macchina virtuale aggiunta a un dominio. Il Centro di amministrazione di Active Directory consente di visualizzare, modificare e creare risorse in un dominio gestito di Azure AD DS, incluse le unità unità radio.

> [!NOTE]
> Per creare criteri password personalizzati in un dominio gestito di Servizi di dominio Active Directory di Azure, è necessario accedere a un account utente membro del gruppo *Amministratori controller di dominio AAD.*

1. Nella schermata Start selezionare **Strumenti di amministrazione.** Viene visualizzato un elenco di strumenti di gestione disponibili che sono stati installati nell'esercitazione per creare una macchina virtuale di [gestione.][tutorial-create-management-vm]
1. Per creare e gestire unità utente, selezionare **Centro di amministrazione** di Active Directory dall'elenco degli strumenti di amministrazione.
1. Nel riquadro sinistro scegliere il dominio gestito di Azure AD DS, ad esempio *aaddscontoso.com*.
1. Aprire il contenitore **Sistema,** quindi il **contenitore Impostazioni password**.

    Vengono visualizzati i criteri password predefiniti per il dominio gestito di Servizi di dominio Active Directory di Azure.A built-in password policy for the Azure AD DS managed domain is shown. Non è possibile modificare questo criterio predefinito. Creare invece un criterio password personalizzato per eseguire l'override del criterio predefinito.

    ![Creare criteri password nel Centro di amministrazione di Active Directory](./media/password-policy/create-password-policy-adac.png)

1. Nel pannello **Attività** a destra, selezionate **Nuovo > Impostazioni password**.
1. Nella finestra di dialogo **Crea impostazioni password** immettere un nome per il criterio, ad esempio *MyCustomFGPP*.
1. Quando esistono più criteri password, il criterio con la precedenza più alta, o priorità, viene applicato a un utente. Più è basso il numero, maggiore sarà la priorità. Il criterio password predefinito ha una priorità di *200*.

    Impostare la precedenza per i criteri password personalizzati per eseguire l'override dell'impostazione predefinita, ad esempio *1*.

1. Modificare le altre impostazioni dei criteri password come desiderato. Ricordare i seguenti punti chiave:

    * Impostazioni come la complessità delle password, la validità o l'ora di scadenza solo per gli utenti creati manualmente in un dominio gestito di Servizi di dominio Active Directory di Azure.Settings like password complexity, age, or expiration time only to users manually created in an Azure AD DS managed domain.
    * Le impostazioni di blocco dell'account si applicano a tutti gli utenti, ma hanno effetto solo all'interno del dominio gestito e non in Azure AD stesso.

    ![Creare criteri granulari personalizzati per le passwordCreate a custom fineed password policy](./media/how-to/custom-fgpp.png)

1. Deselezionare **Proteggi da eliminazioni accidentali**. Se questa opzione è selezionata, non è possibile salvare il file FGPP.
1. Nella sezione **Si applica direttamente a** selezionare il pulsante **Aggiungi.** Nella finestra di dialogo **Seleziona utenti o gruppi,** selezionare il pulsante **Posizioni.**

    ![Selezionare gli utenti e i gruppi a cui applicare i criteri password](./media/how-to/fgpp-applies-to.png)

1. I criteri password possono essere applicati solo ai gruppi. Nella finestra di dialogo **Percorsi** espandere il nome di dominio, ad esempio *aaddscontoso.com*, quindi selezionare un'unità organizzativa, ad esempio **Utenti AADDC**. Se si dispone di un'unità organizzativa personalizzata contenente un gruppo di utenti che si desidera applicare, selezionare tale unità organizzativa.

    ![Selezionare l'unità organizzativa a cui appartiene il gruppo](./media/how-to/fgpp-container.png)

1. Digitare il nome del gruppo a cui si desidera applicare il criterio, quindi selezionare **Controlla nomi** per verificare l'esi0no.

    ![Cercare e selezionare il gruppo da applicare FGPP](./media/how-to/fgpp-apply-group.png)

1. Con il nome del gruppo selezionato ora visualizzato nella sezione **Si applica direttamente a,** selezionare **OK** per salvare i criteri password personalizzati.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui criteri password e sull'utilizzo dell'interfaccia di amministrazione di Active Directory, vedere i seguenti articoli:

* [Informazioni sui criteri granulari per le password](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Configurare criteri granulari per le password tramite l'interfaccia di amministrazione di Active Directory](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
