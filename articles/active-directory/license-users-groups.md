---
title: Assegnare licenze a utenti in Azure Active Directory | Microsoft Docs
description: Informazioni su come assegnare licenze a se stessi e ad altri utenti in Azure Active Directory.
services: active-directory
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jeffgilb
custom: it-pro
ms.openlocfilehash: c4509cdb003687083d0456c1957b19cf35ee056a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="quickstart-license-users-in-azure-active-directory"></a>Guida introduttiva: Assegnare licenze a utenti in Azure Active Directory
I servizi di Azure AD basati su licenza funzionano attivando una sottoscrizione di Azure Active Directory (Azure AD) nel tenant di Azure. Dopo aver attivato la sottoscrizione, le funzionalità del servizio vengono gestite dagli amministratori di Azure AD e usate dagli utenti dotati di licenza. Quando si acquista Enterprise Mobility + Security, Azure AD Premium o Azure AD Basic, il tenant viene aggiornato con la sottoscrizione, inclusi il relativo periodo di validità e le licenze prepagate. Le informazioni relative alla sottoscrizione, tra cui il numero di licenze disponibili o assegnate, sono disponibili tramite il Portale di Azure nel riquadro **Licenze** di **Azure Active Directory**. Il pannello **Licenze** è anche la posizione migliore per gestire le assegnazioni delle licenze.

Anche se per configurare le funzionalità a pagamento è sufficiente ottenere una sottoscrizione, è comunque necessario assegnare agli utenti le licenze per le funzionalità a pagamento di Azure AD. Agli utenti che devono accedere a una funzionalità a pagamento di Azure AD, o che vengono gestiti tramite questa funzionalità, deve essere assegnata una licenza. Un'assegnazione di licenza consiste nell'associazione di un utente a un servizio acquistato, ad esempio Azure AD Premium o Basic oppure Enterprise Mobility + Security.

È possibile usare l'[assegnazione delle licenze in base al gruppo](active-directory-licensing-whatis-azure-portal.md) per impostare le regole, ad esempio le seguenti:
* Tutti gli utenti nella directory ottengono automaticamente una licenza
* Tutti gli utenti con la posizione appropriata ottengono una licenza
* È possibile delegare la decisione ad altri responsabili nell'organizzazione (tramite [gruppi self-service](active-directory-accessmanagement-self-service-group-management.md))

> [!TIP]
> Per una descrizione dettagliata dell'assegnazione delle licenze ai gruppi, inclusi scenari di gestione licenze di Office 365 e scenari avanzati, vedere [Assegnare licenze a un gruppo di utenti in Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md).

## <a name="assign-licenses-to-users-and-groups"></a>Assegnare licenze a utenti e gruppi
Se si usa una sottoscrizione attiva, è necessario prima assegnare una licenza a se stessi e quindi aggiornare il browser per verificare che siano visibili tutte le funzionalità previste nella sottoscrizione. Il passaggio successivo consiste nell'assegnare licenze agli utenti che devono accedere alle funzionalità a pagamento di Azure AD. Uno dei metodi di assegnazione più semplici è quello di assegnare le licenze a gruppi di utenti anziché a singoli utenti. Se si assegnano licenze a un gruppo, a tutti i membri del gruppo viene assegnata una licenza. Quando un utente viene aggiunto al gruppo o rimosso dal gruppo, anche la relativa licenza viene automaticamente assegnata o rimossa. 

> [!NOTE]
> Alcuni servizi Microsoft non sono disponibili in tutte le posizioni. Per assegnare una licenza a un utente, l'amministratore deve prima specificare la proprietà **Località di utilizzo** per l'utente. Per impostare questa proprietà , usare **Utente** &gt; **Profilo** &gt; **Impostazioni** nel portale di Azure. Quando si esegue l'assegnazione di licenze a gruppi, gli utenti per cui non è specificata una località di utilizzo ereditano la località della directory.

Per assegnare una licenza, in **Azure Active Directory** &gt; **Licenze** &gt; **Tutti i prodotti**, selezionare uno o più prodotti e quindi scegliere **Assegna** sulla barra dei comandi.

![Selezionare una licenza da assegnare](media/license-users-groups/select-license-to-assign.png)

È possibile usare il pannello **Utenti e gruppi** per scegliere più utenti o gruppi o disabilitare i piani di servizio nel prodotto. Per cercare nomi di utenti e gruppi, usare la casella di ricerca visualizzata nell'area superiore.

![Selezionare un utente o un gruppo per l'assegnazione di licenze](media/license-users-groups/select-user-for-license-assignment.png)

Quando si assegna una licenza a un gruppo, è possibile che trascorra del tempo prima che tutti gli utenti ereditino la licenza, a seconda del numero di utenti del gruppo. È possibile controllare lo stato di elaborazione nel pannello **Gruppo**, nel riquadro **Licenze**.

![Stato di assegnazione delle licenze](media/license-users-groups/license-assignment-status.png)

Durante l'assegnazione di licenze di Azure AD, possono verificarsi errori di assegnazione, che però sono relativamente rari quando si gestiscono prodotti Azure AD ed Enterprise Mobility + Security. I potenziali errori di assegnazione sono limitati a:
- Conflitto di assegnazione: si verifica se in precedenza è stata assegnata all'utente una licenza non compatibile con la licenza corrente. In questo caso, per assegnare la nuova licenza è necessario rimuovere quella corrente.
- Superamento della soglia di licenze disponibili: quando il numero di utenti nei gruppi assegnati supera le licenze disponibili, lo stato di assegnazione di un utente riflette un errore dovuto a mancanza di licenze.

### <a name="azure-ad-b2b-collaboration-licensing"></a>Licenze per Collaborazione B2B di Azure AD

Collaborazione B2B consente di invitare gli utenti guest nel tenant di Azure AD per fornire l'accesso ai servizi di Azure AD e alle risorse di Azure disponibili.  

Non è previsto alcun addebito per l'invito degli utenti in B2B e per la loro assegnazione a un'applicazione in Azure AD. Per gli utenti di Collaborazione B2B sono gratuiti anche un massimo di 10 applicazioni per utente guest e 3 report di base. Se un utente guest dispone di licenze appropriate nel tenant di Azure AD del partner, potrà usufruire di tale licenza anche per il tenant di cui è guest.

Se si vuole fornire l'accesso alle funzionalità a pagamento di Azure AD, gli utenti guest B2B devono disporre delle licenze appropriate di Azure AD. Un tenant che invia gli inviti con una licenza a pagamento di Azure AD può assegnare i diritti utente di Collaborazione B2B ad altri cinque utenti guest invitati ad accedere al tenant. Per scenari e informazioni, vedere [Linee guida sulla Collaborazione B2B di Azure Active Directory](active-directory-b2b-licensing.md).

## <a name="view-assigned-licenses"></a>Visualizzare licenze assegnate

In **Azure Active Directory** &gt; **Licenze** &gt; **Tutti i prodotti** è visualizzato un riepilogo delle licenze assegnate e disponibili.

![Visualizzare il riepilogo delle licenze](media/license-users-groups/view-license-summary.png)

Quando si seleziona un prodotto specifico, è disponibile un elenco dettagliato di utenti e gruppi assegnati. L'elenco **Utenti con licenza** mostra tutti gli utenti che attualmente usano una licenza e indica se la licenza è stata assegnata direttamente all'utente o è stata ereditata da un gruppo.

![Visualizzare i dettagli delle licenze](media/license-users-groups/view-license-detail.png)

Analogamente, l'elenco **Gruppi con licenza** mostra tutti i gruppi a cui sono state assegnate licenze. Selezionare un utente o un gruppo per aprire il pannello **Licenze**, che mostra tutte le licenze assegnate a tale oggetto.

## <a name="remove-a-license"></a>Rimuovere una licenza

Per rimuovere una licenza, passare all'utente o al gruppo e aprire il riquadro **Licenze**. Selezionare la licenza e fare clic su **Rimuovi**.

![Rimuovere una licenza](media/license-users-groups/remove-license.png)

Le licenze che l'utente ha ereditato da un gruppo non possono essere rimosse direttamente. In alternativa, rimuovere l'utente dal gruppo da cui sta ereditando la licenza.


## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva si è appreso come assegnare licenze a utenti e gruppi in una directory di Azure AD. 

È possibile usare il collegamento seguente per configurare le assegnazioni delle licenze di sottoscrizione in Azure AD dal portale di Azure.

> [!div class="nextstepaction"]
> [Assegnare licenze di Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Overview) 