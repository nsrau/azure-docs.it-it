---
title: Proprietà di un utente di Collaborazione B2B di Azure Active Directory | Microsoft Docs
description: Le proprietà di un utente di Collaborazione B2B di Azure Active Directory sono configurabili
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/25/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 0cfd7888acf942e4af875c37c2472ff086f9119b
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/14/2018
ms.locfileid: "39057893"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Proprietà di un utente di Collaborazione B2B di Azure Active Directory

Un utente di Collaborazione B2B (business-to-business ) di Azure Active Directory (Azure AD) è un utente con UserType = Guest. L'utente guest rappresenta in genere un utente di un'organizzazione partner e ha privilegi limitati nella directory di invito per impostazione predefinita.

In base alle esigenze dell'organizzazione che emette l'invito, un utente di Collaborazione B2B di Azure AD può trovarsi in uno dei seguenti stati dell'account:

- Stato 1: incluso in un'istanza esterna di Azure AD e rappresentato come utente guest nell'organizzazione che emette l'invito. In questo caso, l'utente B2B accede con un account Azure AD appartenente al tenant che riceve l'invito. Se l'organizzazione partner non usa Azure AD, viene comunque creato l'utente guest in Azure AD. È necessario che l'utente riscatti l'invito e che Azure AD ne verifichi l'indirizzo di posta elettronica. Questa disposizione è detta anche tenancy JIT o "virale".

- Stato 2: incluso in un account Microsoft e rappresentato come utente guest nell'organizzazione host. In tal caso, l'utente guest accede con un account Microsoft. L'identità basata su social network (come google.com o simili) dell'utente invitato, che non è un account Microsoft, viene creata come account Microsoft al momento del riscatto dell'offerta.

- Stato 3: incluso nell'istanza locale di Active Directory dell'organizzazione host e sincronizzato con l'istanza di Azure AD dell'organizzazione host. In questa versione è necessario usare PowerShell per modificare manualmente l'attributo UserType di tali utenti nel cloud.

- Stato 4: incluso nell'istanza di Azure AD dell'organizzazione host con UserType = Guest e credenziali gestite dall'organizzazione host.

  ![Visualizzazione iniziali del mittente dell'invito](media/user-properties/redemption-diagram.png)


Viene ora illustrato l'aspetto dell'utente di Collaborazione B2B di Azure AD nello stato 1 in Azure AD.

### <a name="before-invitation-redemption"></a>Prima del riscatto dell'invito

![Prima del riscatto dell'offerta](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Dopo il riscatto dell'invito

![Dopo il riscatto dell'offerta](media/user-properties/after-redemption.png)

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Proprietà chiave dell'utente di Collaborazione B2B di Azure AD
### <a name="usertype"></a>UserType
Questa proprietà indica la relazione tra l'utente e la tenancy host. I valori possibili per questa proprietà sono due.
- Membro: questo valore indica un dipendente dell'organizzazione host e un utente retribuito dall'organizzazione. L'utente prevede ad esempio di avere accesso solo ai siti interni e non viene considerato un collaboratore esterno.

- Guest: questo valore indica un utente che non è considerato interno all'azienda, come un collaboratore esterno, un partner, un cliente e così via. Un utente di questo tipo, ad esempio, non dovrà ricevere un promemoria interno del CEO o usufruire di benefit aziendali.

  > [!NOTE]
  > La proprietà UserType non ha alcun legame con la modalità di accesso, con il ruolo della directory dell'utente e così via. Questa proprietà indica semplicemente la relazione dell'utente con l'organizzazione host e consente all'organizzazione di applicare i criteri che dipendono da questa proprietà.

### <a name="source"></a>Sorgente
Questa proprietà indica la modalità di accesso dell'utente.

- Utente invitato: l'utente è stato invitato ma non ha ancora riscattato l'invito.

- Active Directory esterno: l'utente è incluso in un'organizzazione esterna ed esegue l'autenticazione con un account Azure AD appartenente all'altra organizzazione. Questo tipo di accesso corrisponde allo stato 1.

- Account Microsoft: l'utente è incluso in un account Microsoft ed esegue l'autenticazione con un account Microsoft. Questo tipo di accesso corrisponde allo stato 2.

- Active Directory di Windows Server: l'utente accede da un'istanza locale di Active Directory appartenente all'organizzazione. Questo tipo di accesso corrisponde allo stato 3.

- Azure Active Directory: l'utente esegue l'autenticazione con un account Azure AD appartenente all'organizzazione. Questo tipo di accesso corrisponde allo stato 4.
  > [!NOTE]
  > Source e UserType sono proprietà indipendenti. Un valore di Source non implica un valore UserType specifico.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Possibilità di aggiungere utenti B2B di Azure AD come membri anziché come guest
In genere, utente B2B di Azure AD è sinonimo di utente guest. Un utente di Collaborazione B2B di Azure AD viene quindi aggiunto come utente con UserType = Guest per impostazione predefinita. In alcuni casi, tuttavia, l'organizzazione partner fa parte di un'organizzazione più ampia a cui appartiene anche l'organizzazione host. In tal caso, l'organizzazione host potrebbe voler considerare gli utenti dell'organizzazione partner come membri e non come utenti guest. Usare le API di gestione inviti B2B di Azure AD per aggiungere o invitare un utente dell'organizzazione partner come membro dell'organizzazione host.

## <a name="filter-for-guest-users-in-the-directory"></a>Filtrare gli utenti guest nella directory

![Filtrare gli utenti guest](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Convertire UserType
Gli utenti possono attualmente convertire UserType da Membro a Guest e viceversa usando PowerShell. La proprietà UserType dovrebbe tuttavia rappresentare la relazione dell'utente con l'organizzazione. È quindi consigliabile modificare il valore di questa proprietà solo se cambia la relazione dell'utente con l'organizzazione. Se la relazione dell'utente cambia, è necessario risolvere alcuni problemi, ad esempio l'eventuale necessità di modificare il nome dell'entità utente (UPN)? L'utente deve continuare ad avere accesso alle stesse risorse? È necessario assegnare una cassetta postale all'utente? Non è consigliabile modificare UserType con PowerShell come attività atomica. Se questa proprietà non sarà più modificabile con PowerShell, non è consigliabile creare una dipendenza da questo valore.

## <a name="remove-guest-user-limitations"></a>Rimuovere le limitazioni dell'utente guest
In alcuni casi può essere necessario dare privilegi più elevati agli utenti guest. È possibile aggiungere un utente guest a qualsiasi ruolo nonché rimuovere le limitazioni dell'utente guest predefinito nella directory per assegnare all'utente gli stessi privilegi dei membri.

È possibile disabilitare le limitazioni dell'utente guest predefinito per assegnare all'utente guest nella directory aziendale le stesse autorizzazioni di un utente membro.

![Rimuovere le limitazioni dell'utente guest](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>È possibile creare utenti guest visibili nell'elenco indirizzi globale di Exchange?
Sì. Per impostazione predefinita, gli oggetti guest non sono visibili nell'elenco indirizzi globale dell'organizzazione, ma è possibile usare Azure Active Directory PowerShell per renderli visibili. Per informazioni dettagliate, vedere **È possibile creare oggetti guest visibili nell'elenco indirizzi globale?** in [Accesso Guest in gruppi di Office 365](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6#PickTab=FAQ). 

## <a name="next-steps"></a>Passaggi successivi

* [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
* [Token utente in Collaborazione B2B](user-token.md)
* [Mapping delle attestazioni utente per Collaborazione B2B](claims-mapping.md)
