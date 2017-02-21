---
title: "Proprietà di un utente di Collaborazione B2B di Azure Active Directory | Documentazione Microsoft"
description: "Le proprietà di un utente di Collaborazione B2B di Azure Active Directory sono configurabili"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/03/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: ae154e09e3b9ef2182e74b1dc5a0d8da3922b0df


---

# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Proprietà di un utente di Collaborazione B2B di Azure Active Directory

## <a name="defining-a-b2b-collaboration-user"></a>Definizione di un utente di Collaborazione B2B
Un utente di Collaborazione B2B è un utente con UserType = Guest, ovvero un utente guest. L'utente rappresenta in genere un utente di un'organizzazione partner e ha privilegi limitati nella directory di invito per impostazione predefinita. In base alle esigenze dell'organizzazione mittente dell'invito, un utente di Collaborazione B2B può trovarsi in uno dei seguenti stati dell'account:
1. Incluso in un'istanza esterna di Azure Active Directory (Azure AD), rappresentato come utente guest nell'organizzazione host. In tal caso, l'utente B2B accede con un account Azure AD appartenente alla relativa tenancy home. Se l'organizzazione esterna a cui appartiene l'utente non usa Azure AD al momento dell'invito, l'utente guest viene creato in Azure AD quando l'utente riscatta l'invito, dopo la verifica del relativo indirizzo di posta elettronica. Tale tenancy è anche detta "Just-In-Time" (JIT) o tenancy virale.
2. Incluso nell'account Microsoft, rappresentato come utente guest nell'organizzazione host. In tal caso, l'utente guest accede con un account Microsoft. Nell'aggiornamento dell'anteprima pubblica per Azure AD di Collaborazione B2B l'identità non MSA basata su social network, come google.com o simili, dell'utente invitato viene creata come account Microsoft al momento del riscatto dell'offerta.
3. Incluso nell'istanza locale di Active Directory dell'organizzazione host, sincronizzato con l'istanza di Azure AD dell'organizzazione host. In questa versione è necessario impostare manualmente su guest l'attributo UserType di tali utenti nel cloud tramite PowerShell. Nelle versioni future la modifica verrà eseguita automaticamente come parte di Azure AD Connect.
4. Incluso nell'istanza di Azure AD dell'organizzazione host con UserType = Guest, con le credenziali gestite dall'organizzazione host.

  ![visualizzazione iniziali del mittente dell'invito](media/active-directory-b2b-user-properties/redemption-diagram.png)


Viene ora illustrato l'aspetto dell'utente di Collaborazione B2B nello stato 1 in Azure AD.

### <a name="before-invitation-redemption"></a>Prima del riscatto dell'invito

![prima del riscatto dell'offerta](media/active-directory-b2b-user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Dopo il riscatto dell'invito

![dopo il riscatto dell'offerta](media/active-directory-b2b-user-properties/after-redemption.png)

## <a name="key-properties-of-the-b2b-collaboration-user"></a>Proprietà chiava dell'utente di Collaborazione B2B

### <a name="usertype"></a>UserType
Questo attributo indica la relazione tra l'utente e la tenancy host. I valori possibili sono due:
- Membro: indica un dipendente dell'organizzazione host o un utente retribuito dall'organizzazione. Ad esempio, questo utente deve poter accedere a siti solo interni e non viene considerato un collaboratore esterno.
- Guest: indica un utente che non è considerato interno all'azienda. Può trattarsi di un collaboratore esterno, un partner, un cliente e così via che non deve ricevere promemoria interni del CEO, ad esempio, o usufruire di benefit aziendali.

  > ![NOTA] L'attributo UserType non ha alcun legame con la modalità di accesso, con il ruolo della directory a cui l'utente appartiene e così via. Indica semplicemente la relazione dell'utente con l'organizzazione host e permette alll'organizzazione di applicare tutti i criteri che dipendono da questo attributo.

### <a name="source"></a>Origine
Indica la modalità di accesso dell'utente.

- Utente invitato: l'utente è stato invitato ma non ha ancora riscattato l'invito.

- Active Directory esterno: l'utente è incluso in un'organizzazione esterna ed esegue l'autenticazione con un account Azure AD appartenente all'altra organizzazione. Questo corrisponde allo stato 1 precedente.

- Account Microsoft: l'utente è incluso in MSA ed esegue l'autenticazione con un account Microsoft. Questo corrisponde allo stato 2 precedente.

- AD di Windows Server: l'utente è sincronizzato da un'istanza locale di Active Directory appartenente all'organizzazione. Questo corrisponde allo stato 3 precedente.

- Azure Active Directory: l'utente esegue l'autenticazione con un account Azure AD appartenente all'organizzazione. Questo corrisponde allo stato 4 precedente.

  > ![NOTA] Origine e UserType sono attributi indipendenti. Un valore dell'origine non implica un attributo UserType specifico.

## <a name="can-b2b-users-be-added-as-members-instead-of-guests"></a>Possibilità di aggiungere utenti B2B come membri anziché come guest
In genere, utente B2B è sinonimo di utente guest. Un utente di Collaborazione B2B viene quindi aggiunto come utente con UserType = Guest per impostazione predefinita. In alcuni casi, tuttavia, l'organizzazione partner fa parte di un gruppo più ampio a cui appartiene anche l'organizzazione host. In tal caso, l'organizzazione host potrebbe voler considerare gli utenti dell'organizzazione partner come membri e non come utenti guest. A tale scopo, è possibile usare le API di gestione inviti B2B per aggiungere o invitare un utente dell'organizzazione partner come membro dell'organizzazione host.

## <a name="filtering-for-guest-users-in-the-directory"></a>Applicazione del filtro agli utenti guest nella directory

![filtrare gli utenti guest](media/active-directory-b2b-user-properties/filter-guest-users.png)

## <a name="converting-usertype"></a>Conversione di UserType
In PowerShell attualmente gli utenti possono convertire UserType da Membro a Guest e viceversa. Tuttavia, la proprietà UserType dovrebbe rappresentare la relazione dell'utente con l'organizzazione. È quindi consigliabile modificarla solo se la relazione dell'utente con l'organizzazione è cambiata. Se tale relazione cambia, sarà necessario rispondere ad altri interrogativi, come i quelli riportati di seguito. È necessario modificare l'UPN? L'utente deve continuare ad accedere alle risorse a cui aveva accesso? È necessario assegnare una cassetta postale all'utente? Non è consigliabile modificare UserType in PowerShell come attività atomica. Nelle versioni future questa proprietà non sarà più modificabile tramite PowerShell, non è quindi consigliabile creare una dipendenza da questo valore.

## <a name="removing-guest-user-limitations"></a>Rimozione delle limitazioni dell'utente guest
In alcuni casi può essere necessario dare privilegi più elevati agli utenti guest. A tale scopo, è possibile aggiungere un utente guest a un ruolo qualsiasi o rimuovere le restrizioni dell'utente guest predefinito dalla directory per dare loro gli stessi privilegi dei membri. Per altre informazioni, continuare la lettura.

È possibile disabilitare le limitazioni dell'utente guest predefinito per dare agli utenti guest nella directory aziendale le stesse autorizzazioni directory dei normali utenti membro.

![rimuovere le limitazioni dell'utente guest](media/active-directory-b2b-user-properties/remove-guest-limitations.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Aggiunta di un utente di Collaborazione B2B a un ruolo](active-directory-b2b-add-guest-to-role.md)
* [Delegare gli inviti a Collaborazione B2B](active-directory-b2b-delegate-invitations.md)
* [Gruppi dinamici e Collaborazione B2B](active-directory-b2b-dynamic-groups.md)
* [Codici ed esempi di PowerShell per Collaborazione B2B](active-directory-b2b-code-samples.md)
* [Configurare app SaaS per Collaborazione B2B](active-directory-b2b-configure-saas-apps.md)
* [Token utente per Collaborazione B2B](active-directory-b2b-user-token.md)
* [Mapping delle attestazioni utente per Collaborazione B2B](active-directory-b2b-claims-mapping.md)
* [Condivisione esterna di Office 365](active-directory-b2b-o365-external-user.md)
* [Limitazioni correnti di Collaborazione B2B](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


