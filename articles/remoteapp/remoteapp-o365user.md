---
title: Come usare Azure RemoteApp con gli account utente di Office 365 | Documentazione Microsoft
description: Informazioni su come usare Azure RemoteApp con gli account utente di Office 365
services: remoteapp
documentationcenter: 
author: piotrci
manager: mbaldwin
ms.assetid: aba70fd3-60b0-4b44-9321-1ab18760ccd5
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 532382c7e15ad9b67915e05e7c980a4123036a0f
ms.lasthandoff: 03/31/2017


---
# <a name="how-to-use-azure-remoteapp-with-office-365-user-accounts"></a>Come usare Azure RemoteApp con gli account utente di Office 365
> [!IMPORTANT]
> Azure RemoteApp verrà sospeso a partire dal 31 agosto 2017. Per i dettagli, vedere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Se si dispone di una sottoscrizione di Office 365, è presente una directory Azure Active Directory in cui sono archiviati i nomi utente e le password usati per accedere ai servizi di Office 365. Ad esempio, quando gli utenti attivano Office 365 ProPlus, eseguono l’autenticazione con Azure AD per cercare le licenze. La maggior parte dei clienti desidera utilizzare la stessa directory con Azure RemoteApp.

Se si distribuisce Azure RemoteApp, è molto probabile che venga usata una sottoscrizione di Azure associata a un Azure AD diverso. Per usare la directory di Office 365, è necessario spostare la sottoscrizione di Azure in questa directory.

Per informazioni su come distribuire le applicazioni client di Office 365, vedere [Come usare la sottoscrizione di Office 365 con Azure RemoteApp](remoteapp-officesubscription.md).

## <a name="phase-1-register-your-free-office-365-azure-active-directory-subscription"></a>Fase 1: Registrare la sottoscrizione gratuita di Office 365 Azure Active Directory
Se si usa il portale di Azure classico, usare i passaggi in [Registrazione della sottoscrizione di Azure Active Directory gratuita](https://technet.microsoft.com/library/dn832618.aspx) per ottenere l'accesso amministrativo ad Azure AD tramite il portale di gestione di Azure. Al termine di questo processo dovrebbe essere possibile accedere al portale di Azure e visualizzare qui la directory: a questo punto non verrà visualizzato molto di più perché la sottoscrizione di Azure completa in uso con Azure RemoteApp si trova in una directory diversa.

Ricordare il nome e la password dell'account amministratore creati in questo passaggio, perché saranno necessari nella fase 2.

Se si usa il portale di Azure, consultare il post di blog relativo alla [registrazione e attivazione gratuite di Azure Active Directory tramite il portale di Office 365](http://azureblogger.com/2016/01/how-to-register-and-activate-a-free-azure-active-directory-using-office-365-portal/).

## <a name="phase-2-change-the-azure-ad-associated-with-your-azure-subscription"></a>Fase 2: Modificare l'Azure AD associato alla sottoscrizione di Azure.
La sottoscrizione di Azure verrà modificata dalla directory corrente alla directory di Office 365 usata nella fase 1.

Seguire le istruzioni descritte in [Modificare il tenant di Azure Active Directory in RemoteApp di Azure](remoteapp-changetenant.md). Prestare particolare attenzione ai seguenti passaggi:

* Passaggio 1: Se è stato distribuito Azure RemoteApp (ARA) nella sottoscrizione, assicurarsi di rimuovere innanzitutto tutti gli account utente di Azure AD da qualsiasi raccolta ARA, prima di qualsiasi altra operazione. In alternativa, è possibile valutare l’eliminazione delle raccolte esistenti.
* Passaggio 2: Questo è un passaggio critico. È necessario usare un account Microsoft, ad esempio @outlook.com, come amministratore del servizio nella sottoscrizione. Questo avviene perché non è possibile avere account utente dall'istanza di Azure AD esistente collegati alla sottoscrizione, altrimenti non sarà possibile procedere allo spostamento in un'istanza di Azure AD diversa.
* Passaggio 4: Quando si aggiunge una directory esistente, il sistema richiederà di accedere con l'account amministratore per tale directory. Assicurarsi di utilizzare l'account amministratore dalla fase 1.
* Passaggio 5: Modificare la directory padre della sottoscrizione con la directory di Office 365. Il risultato finale deve essere che in Impostazioni -> Sottoscrizioni, nella sottoscrizione è elencata la directory di Office 365. 
  ![Modificare la directory padre della sottoscrizione](./media/remoteapp-o365user/settings.png)

A questo punto la sottoscrizione di Azure RemoteApp è associata a Office 365 Azure AD; è possibile usare gli account utente di Office 365 esistenti con Azure RemoteApp.


