---
title: Introduzione alla concessione di licenze in Azure Active Directory | Microsoft Docs
description: Come vengono concesse le licenze in Azure Active Directory e quali sono le procedure consigliate da seguire
services: active-directory
keywords: Licenze di Azure AD
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: b62571e446aa0680c653d0a9d109207af26ad786
ms.contentlocale: it-it
ms.lasthandoff: 07/01/2017

---

# <a name="license-yourself-and-your-users-in-azure-active-directory"></a>Concessione di licenze a se stessi e agli utenti in Azure Active Directory

> [!div class="op_single_selector"]
> * [Istruzioni del portale di Azure](active-directory-licensing-get-started-azure-portal.md)
> * [Informazioni sul portale di Azure classico](active-directory-licensing-what-is.md)
>
>

Azure Active Directory (Azure AD) è una soluzione e piattaforma IDaaS (Identity as a Service) di Microsoft. Azure AD è offerta in varie edizioni di servizio:

* Azure Active Directory Free, disponibile con qualsiasi servizio Microsoft, come Office 365, Dynamics, Microsoft Intune o Azure. Azure AD non genera alcun addebito di utilizzo in questa modalità.

* Edizioni a pagamento di Azure AD, ad esempio:
  - Enterprise Mobility + Security 
  - Azure AD Premium (P1 e P2)
  - Azure AD Basic
  - Azure Multi-Factor Authentication

Come molti servizi online Microsoft, la maggior parte delle versioni a pagamento di Azure AD sono fornite tramite diritti per utente in quanto sono in Office 365, Microsoft Intune e Azure AD. In questi casi, l'acquisto del servizio è rappresentato da una o più sottoscrizioni e ogni sottoscrizione include un numero di licenze preacquistate nel tenant. I diritti per utente vengono ottenuti tramite una delle azioni seguenti:

* Assegnazione di una licenza. 
* Creazione di un collegamento tra l'utente e il prodotto.
* Abilitazione dei componenti del servizio per l'utente.
* Utilizzo di una delle licenze prepagate.

[Provare Azure AD Premium](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

Per una panoramica estesa delle funzionalità del servizio Azure AD, vedere [Informazioni su Azure Active Directory](active-directory-whatis.md). Per altre informazioni, vedere la [pagina Contratti di servizio](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/).

> [!NOTE]
> Le sottoscrizioni di Azure con pagamento in base al consumo consentono di creare risorse di Azure e quindi associarle al metodo di pagamento. Non è definito un numero di licenze associato alla sottoscrizione. Quando si concede a un utente l'autorizzazione per eseguire operazioni sulle risorse di Azure associate alla sottoscrizione, l'utente viene associato alla sottoscrizione e può gestire le risorse correlate.

## <a name="how-does-azure-active-directory-licensing-work"></a>Come funzionano le licenze di Azure Active Directory?

I servizi di Azure AD basati su licenza funzionano attivando una sottoscrizione nel tenant di servizio di Azure AD. Una volta attivata la sottoscrizione, le funzionalità del servizio vengono gestite dagli amministratori di Azure AD e usate dagli utenti dotati di licenza.

### <a name="manage-subscription-information"></a>Gestire le informazioni sulla sottoscrizione

Quando si acquista Enterprise Mobility + Security, Azure AD Premium o Azure AD Basic, il tenant viene aggiornato con la sottoscrizione, inclusi il relativo periodo di validità e le licenze prepagate. Le informazioni relative alla sottoscrizione, tra cui il numero di licenze disponibili o assegnate, sono disponibili tramite il portale di Azure: in **Azure Active Directory** aprire il riquadro **Licenze** per la directory specifica. Il pannello **Licenze** è anche la posizione migliore per gestire le assegnazioni delle licenze.

Ogni sottoscrizione è costituita da uno o più piani di servizio, ad esempio Azure AD, Multi-Factor Authentication, Intune, Exchange Online o SharePoint Online.  La gestione delle licenze di Azure AD *non* richiede la gestione del livello del piano di servizio. Office 365 è diverso perché si basa su questa modalità di configurazione avanzata per gestire l'accesso ai servizi inclusi. Azure AD si basa sulla configurazione in servizio per abilitare funzionalità e gestire autorizzazioni individuali.

> [!IMPORTANT]
> Le sottoscrizioni di Azure AD Premium e Basic e quelle di Enterprise Mobility + Security sono limitate alla directory o al tenant di cui è stato effettuato il provisioning. Le sottoscrizioni non possono essere suddivise tra le directory o usate per autorizzare utenti in altre directory. Lo spostamento di una sottoscrizione tra directory è possibile, ma richiede l'invio di un ticket di supporto o l'annullamento e il riacquisto nel caso di acquisti diretti.
>
> Quando si acquista Azure AD o Enterprise Mobility + Security tramite una sottoscrizione basata su contratti multilicenza e quando il contratto include altri Microsoft Online Services, ad esempio Office 365, l'attivazione viene eseguita automaticamente. 

### <a name="assign-licenses"></a>Assegnare licenze

Anche se per configurare le funzionalità a pagamento è sufficiente ottenere una sottoscrizione, è necessario distribuire agli utenti le licenze per le funzionalità a pagamento di Azure AD. Agli utenti che devono accedere a una funzionalità a pagamento di Azure AD, o che vengono gestiti tramite tale funzionalità, deve essere assegnata una licenza. Un'assegnazione di licenza consiste nell'associazione di un utente a un servizio acquistato, ad esempio Azure AD Premium o Basic oppure Enterprise Mobility + Security.


Per gestire gli utenti presenti nella directory che devono disporre di una licenza, è possibile eseguire una di queste operazioni: 

* Assegnazione delle licenze a gruppi nel [portale di Azure](active-directory-licensing-whatis-azure-portal.md).
* Assegnazione diretta delle licenze agli utenti tramite il portale, PowerShell o le API. 

Quando si assegnano le licenze a un gruppo, a tutti i membri del gruppo viene assegnata una licenza. Quando gli utenti vengono aggiunti al gruppo oppure rimossi dal gruppo, anche la licenza appropriata viene assegnata o rimossa. L'assegnazione al gruppo può utilizzare qualsiasi tipo di gestione del gruppo disponibile ed è coerente con l'assegnazione alle applicazioni in base al gruppo.

È possibile usare l'[assegnazione delle licenze in base al gruppo](active-directory-licensing-whatis-azure-portal.md) per impostare le regole, ad esempio le seguenti:
* Tutti gli utenti nella directory ottengono automaticamente una licenza
* Tutti gli utenti con la posizione appropriata ottengono una licenza
* È possibile delegare la decisione ad altri responsabili nell'organizzazione (tramite [gruppi self-service](active-directory-accessmanagement-self-service-group-management.md))

Per una descrizione dettagliata dell'assegnazione delle licenze ai gruppi, inclusi scenari di gestione licenze di Office 365 e scenari avanzati, vedere [Assegnare licenze a un gruppo di utenti in Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md).

## <a name="get-started-with-azure-ad-licensing"></a>Introduzione alle licenze di Azure AD

Iniziare a usare Azure AD è semplice. È sempre possibile creare la propria directory come parte di una registrazione a una [versione di valutazione gratuita di Azure](https://azure.microsoft.com/offers/ms-azr-0044p/).

Le procedure consigliate seguenti sono utili per verificare che il tenant sia allineato con altri servizi Microsoft potenzialmente in uso e con gli obiettivi per il servizio:

- Se si usa già uno qualsiasi dei servizi aziendali di Microsoft, si ha già un tenant di Azure AD. È utile continuare a usare lo stesso tenant per altri servizi, in modo che la gestione delle identità di base, tra cui provisioning e Single Sign-On (SSO) ibrido, possa essere usata in tutti i servizi. Con Single Sign-On, gli utenti possono beneficiare delle funzionalità avanzate per i vari servizi. Se quindi si decide di acquistare un servizio a pagamento di Azure AD per la propria forza lavoro, è consigliabile usare lo stesso tenant.

- È invece consigliabile usare un nuovo tenant nel portale di Azure, se si prevede quanto segue:
  - Usare Azure AD per un diverso set di utenti, ad esempio partner o clienti.
  - Valutare i servizi di Azure AD in modo indipendente dal servizio di produzione.
  - Configurare un ambiente sandbox per i servizi.

  La nuova directory viene creata con il proprio account come utente esterno con autorizzazioni di amministratore globale. Accedendo al Portale di Azure con questo account, è possibile visualizzare il tenant e accedere a tutte le attività di amministrazione.

> [!NOTE]
> Azure AD supporta gli "utenti guest", ovvero gli account utente di un tenant di Azure AD creati usando un account Microsoft o un'identità di Azure AD da un altro tenant. Il portale di amministrazione di Office 365 non supporta attualmente questi utenti. Gli utenti guest con account Microsoft non sono in grado di accedere al portale di amministrazione di Office 365, mentre gli utenti guest di altri tenant Azure AD vengono ignorati. Nel secondo caso è possibile accedere solo all'account locale dell'utente nel tenant di Azure AD o Office 365 in cui l'utente è stato originariamente creato.

### <a name="select-one-or-more-license-trials"></a>Selezionare una o più versioni di valutazione delle licenza

È possibile attivare una sottoscrizione di valutazione di Azure AD Premium o Enterprise Mobility + Security in **Azure Active Directory** &gt; **Avvio rapido**.

![Selezionare una versione di valutazione](media/active-directory-licensing-get-started-azure-portal/select-a-license-trial.png)

Le licenze di valutazione sono disponibili nel pannello **Licenze**.

### <a name="assign-licenses-to-users-and-groups"></a>Assegnare licenze a utenti e gruppi

Una volta attivata la sottoscrizione, è necessario assegnare una licenza a se stessi. Aggiornare quindi il browser per verificare che tutte le funzionalità siano visibili. Il passaggio successivo consiste nell'assegnare licenze agli utenti che devono accedere alle funzionalità a pagamento di Azure AD. Come descritto in [Assegnare licenze](#assign-licenses), un modo semplice per eseguire questa operazione è quello di identificare il gruppo che rappresenta i destinatari desiderati e assegnare la licenza a tale gruppo. In questo modo, gli utenti che vengono aggiunti o rimossi dal gruppo durante il relativo ciclo di vita vengono rispettivamente assegnati o rimossi dalla licenza.

> [!NOTE]
> Alcuni servizi Microsoft non sono disponibili in tutte le posizioni. Per assegnare una licenza a un utente, l'amministratore deve prima specificare la proprietà **Località di utilizzo** per l'utente. Per impostare questa proprietà , usare **Utente** &gt; **Profilo** &gt; **Impostazioni** nel portale di Azure. Quando si esegue l'assegnazione di licenze a gruppi, gli utenti per cui non è specificata una località di utilizzo ereditano la località della directory.

Per assegnare una licenza, in **Azure Active Directory** &gt; **Licenze** &gt; **Tutti i prodotti**, selezionare uno o più prodotti e quindi scegliere **Assegna** sulla barra dei comandi.

![Selezionare una licenza da assegnare](media/active-directory-licensing-get-started-azure-portal/select-license-to-assign.png)

È possibile usare il pannello **Utenti e gruppi** per scegliere più utenti o gruppi o disabilitare i piani di servizio nel prodotto. Per cercare nomi di utenti e gruppi, usare la casella di ricerca visualizzata nell'area superiore.

![Selezionare un utente o un gruppo per l'assegnazione di licenze](media/active-directory-licensing-get-started-azure-portal/select-user-for-license-assignment.png)

Quando si assegna una licenza a un gruppo, è possibile che trascorra del tempo prima che tutti gli utenti ereditino la licenza, a seconda del numero di utenti del gruppo. È possibile controllare lo stato di elaborazione nel pannello **Gruppo**, nel riquadro **Licenze**.

![Stato di assegnazione delle licenze](media/active-directory-licensing-get-started-azure-portal/license-assignment-status.png)

Durante l'assegnazione di licenze di Azure AD, possono verificarsi errori di assegnazione, che però sono relativamente rari quando si gestiscono prodotti Azure AD ed Enterprise Mobility + Security. I potenziali errori di assegnazione sono limitati a:
- Conflitto di assegnazione: si verifica se in precedenza è stata assegnata all'utente una licenza non compatibile con la licenza corrente. In questo caso, per assegnare la nuova licenza è necessario rimuovere quella corrente.
- Superamento della soglia di licenze disponibili: quando il numero di utenti nei gruppi assegnati supera le licenze disponibili, lo stato di assegnazione di un utente riflette un errore dovuto a mancanza di licenze.

#### <a name="azure-ad-b2b-collaboration-licensing"></a>Licenze per Collaborazione B2B di Azure AD

Collaborazione B2B consente di invitare gli utenti guest nel tenant di Azure AD per fornire l'accesso ai servizi di Azure AD e alle risorse di Azure disponibili.  

Non è previsto alcun addebito per l'invito degli utenti in B2B e per la loro assegnazione a un'applicazione in Azure AD. Per gli utenti di Collaborazione B2B sono gratuiti anche un massimo di 10 applicazioni per utente guest e 3 report di base. Se un utente guest dispone di licenze appropriate nel tenant di Azure AD del partner, potrà usufruire di tale licenza anche per il tenant di cui è guest.

Se si vuole fornire l'accesso alle funzionalità a pagamento di Azure AD, gli utenti guest B2B devono disporre delle licenze appropriate di Azure AD. Un tenant che invia gli inviti con una licenza a pagamento di Azure AD può assegnare i diritti utente di Collaborazione B2B ad altri cinque utenti guest invitati ad accedere al tenant. Per scenari e informazioni, vedere [Linee guida sulla Collaborazione B2B di Azure Active Directory](active-directory-b2b-licensing.md).

### <a name="view-assigned-licenses"></a>Visualizzare licenze assegnate

In **Azure Active Directory** &gt; **Licenze** &gt; **Tutti i prodotti** è visualizzato un riepilogo delle licenze assegnate e disponibili.

![Visualizzare il riepilogo delle licenze](media/active-directory-licensing-get-started-azure-portal/view-license-summary.png)

Quando si seleziona un prodotto specifico, è disponibile un elenco dettagliato di utenti e gruppi assegnati. L'elenco **Utenti con licenza** mostra tutti gli utenti che attualmente usano una licenza e indica se la licenza è stata assegnata direttamente all'utente o è stata ereditata da un gruppo.

![Visualizzare i dettagli delle licenze](media/active-directory-licensing-get-started-azure-portal/view-license-detail.png)

Analogamente, l'elenco **Gruppi con licenza** mostra tutti i gruppi a cui sono state assegnate licenze. Selezionare un utente o un gruppo per aprire il pannello **Licenze**, che mostra tutte le licenze assegnate a tale oggetto.

### <a name="remove-a-license"></a>Rimuovere una licenza

Per rimuovere una licenza, passare all'utente o al gruppo e aprire il riquadro **Licenze**. Selezionare la licenza e fare clic su **Rimuovi**.

![Rimuovere una licenza](media/active-directory-licensing-get-started-azure-portal/remove-license.png)

Le licenze che l'utente ha ereditato da un gruppo non possono essere rimosse direttamente. In alternativa, rimuovere l'utente dal gruppo da cui sta ereditando la licenza.

### <a name="extend-trials"></a>Estendere le versioni di valutazione

Le estensioni delle versioni di valutazione per i clienti sono disponibili come iscrizione self-service tramite il portale di Office 365. Un amministratore del cliente può accedere al portale di Office, in base alle autorizzazioni definite per il portale, e selezionare la versione di valutazione di Azure AD Premium. Fare clic sul collegamento **Estendi periodo di valutazione** per avviare il processo di estensione. È necessaria una carta di credito, ma non viene addebitata alcuna somma.

![Estendere una versione di valutazione nel portale di Azure](media/active-directory-licensing-get-started-azure-portal/extend-trial-beginning.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sugli scenari avanzati per la gestione delle licenze tramite i gruppi, vedere l'articolo [Assegnare licenze a un gruppo](active-directory-licensing-group-assignment-azure-portal.md).

Fare clic sui collegamenti seguenti per informazioni su come configurare e usare altre funzionalità a pagamento di Azure AD:

* [Reimpostazione della password self-service](active-directory-manage-passwords.md)
* [Gestione di gruppi self-service](active-directory-accessmanagement-self-service-group-management.md)
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
* [Acquisto diretto di licenze di Azure AD Premium](http://aka.ms/buyaadp)

