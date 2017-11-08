---
title: Creare una verifica di accesso per i membri di un gruppo o gli utenti con accesso a un'applicazione con Azure AD| Microsoft Docs
description: Informazioni su come creare una verifica di accesso per i membri di un gruppo o gli utenti con accesso a un'applicazione.
services: active-directory
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: ae926e8d3a856c9b488d46b481a1c7b5b581d532
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2017
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Creare una verifica di accesso dei membri del gruppo o dell'accesso all'applicazione con Azure AD

Le assegnazioni di accessi diventano "obsolete" quando gli utenti dispongono di accessi di cui non hanno più necessità. Per ridurre i rischi associati alle assegnazioni di accesso obsolete, gli amministratori possono usare Azure Active Directory (Azure AD) per creare una verifica di accesso per i membri del gruppo o gli utenti assegnati a un'applicazione. Per altre informazioni su questi scenari, vedere [Gestire l'accesso utente](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) e [Gestire l'accesso guest](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). 

## <a name="create-an-access-review"></a>Creare una verifica di accesso

1. Come amministratore globale, accedere alla [pagina delle verifiche di accesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) e selezionare **Programmi**.

2. Selezionare il programma che contiene il controllo della verifica di accesso da creare. **Programma predefinito** è sempre presente oppure è possibile creare un altro programma. Ad esempio, è possibile scegliere che sia disponibile un programma per ogni obiettivo di business o iniziativa di conformità.

3. All'interno del programma selezionare **Controlli**, quindi selezionare **Aggiungi** per aggiungere un controllo.

4. Assegnare un nome a ogni verifica di accesso. Facoltativamente è possibile assegnare una descrizione a ogni verifica. Il nome viene mostrato ai revisori.

5. Impostare le date di inizio e di fine. Per impostazione predefinita, la verifica di accesso viene iniziata lo stesso giorno in cui viene creata e termina entro un mese. È possibile cambiare le date di inizio e di fine per iniziare la verifica di accesso in futuro e impostare la durata sul numero di giorni desiderato.

6. Le verifiche di accesso possono essere relative ai membri di un gruppo o agli utenti assegnati a un'applicazione. È possibile anche definire l'ambito della verifica di accesso in modo da controllare solo gli utenti guest che sono membri o che sono stati assegnati all'app, anziché controllare tutti gli utenti che sono membri o che hanno accesso all'applicazione.

7. Selezionare uno o più utenti per verificare tutti gli utenti nell'ambito. In alternativa è possibile fare in modo che i membri verifichino il proprio accesso. Se la risorsa è un gruppo, è possibile richiedere ai proprietari del gruppo di eseguire la verifica. È possibile anche richiedere che i revisori specifichino un motivo per l'approvazione dell'accesso.

8. Infine selezionare **Inizia**.


## <a name="manage-the-access-review"></a>Gestire la verifica di accesso

Per impostazione predefinita, Azure AD invia un messaggio di posta elettronica ai revisori poco tempo dopo l'inizio della verifica. Se si imposta Azure AD in modo che non invii un messaggio di posta elettronica, assicurarsi di informare i revisori che vi è una verifica di accesso in attesa di completamento. È possibile mostrare ai membri le istruzioni su come [verificare l'accesso](active-directory-azure-ad-controls-perform-access-review.md). Se la verifica prevede che siano i guest a verificare il proprio accesso, è possibile mostrare le istruzioni su [come verificare il proprio accesso](active-directory-azure-ad-controls-perform-access-review.md).

Se alcuni revisori sono guest, i guest riceveranno la notifica tramite posta elettronica solo se hanno già accettato l'invito.


È possibile tenere traccia dello stato di avanzamento delle verifiche eseguite dai revisori nella sezione **Verifiche di accesso** del dashboard di Azure AD. Nessun diritto di accesso viene modificato nella directory fino al [completamento della verifica](active-directory-azure-ad-controls-complete-access-review.md).

## <a name="next-steps"></a>Passaggi successivi

Quando è stata avviata una verifica di accesso, Azure AD invia automaticamente ai revisori un messaggio di posta elettronica in cui si richiede di verificare l'accesso. Se un utente non ha ricevuto il messaggio di posta elettronica, è possibile inviargli le istruzioni su [come eseguire una verifica di accesso](active-directory-azure-ad-controls-perform-access-review.md). 

Al termine della verifica di accesso o se l'amministratore ha interrotto la verifica, seguire la procedura descritta in [completamento di una verifica di accesso](active-directory-azure-ad-controls-complete-access-review.md) per esaminare e applicare i risultati.


