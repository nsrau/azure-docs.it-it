---

title: Introduzione alle licenze nell&quot;anteprima di Azure Active Directory | Documentazione Microsoft
description: Descrizione delle licenze di Azure Active Directory, del loro funzionamento, di come iniziare a usarle e delle procedure consigliate, anche per Office 365, Microsoft Intune e le edizioni di base e Premium di Azure Active Directory
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
ms.date: 02/27/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 3bad24f35cf7867f1e02e8470c602a7f6a9ce8fb
ms.lasthandoff: 03/09/2017


---

# <a name="license-yourself-and-your-users-in-azure-active-directory-preview"></a>Concessione di licenze a se stessi e agli utenti in anteprima di Azure Active Directory

> [!div class="op_single_selector"]
> * [Portale di Azure](active-directory-licensing-get-started-azure-portal.md)
> * [portale di Azure classico](active-directory-licensing-what-is.md)
>
>

Azure Active Directory (Azure AD) è una soluzione e piattaforma IDaaS (Identity as a Service) di Microsoft. Azure AD è offerto in varie versioni funzionali e tecniche che spaziano da Azure AD Free, disponibile con qualsiasi servizio Microsoft come Office 365, Dynamics, Microsoft Intune e Azure (Azure AD non genera alcun addebito di utilizzo in questa modalità), alle versioni Azure AD a pagamento quali Enterprise Mobility Suite (EMS), Azure AD Premium (P1 e P2) e Azure AD Basic nonché Azure Multi-Factor Authentication (MFA). Come molti servizi online Microsoft, la maggior parte delle versioni a pagamento di Azure AD sono fornite tramite diritti per utente in quanto sono in Office 365, Microsoft Intune e Azure AD. In questi casi, l'acquisto del servizio è rappresentato da una o più sottoscrizioni e ogni sottoscrizione include un numero di licenze pre-acquisto nel tenant. I diritti per utente vengono ottenuti tramite assegnazione delle licenze, creazione di un collegamento tra l'utente e il prodotto, abilitazione dei componenti del servizio per l'utente e utilizzo di una delle licenze prepagate.

[Provare Azure AD Premium](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

Per una panoramica estesa delle funzionalità del servizio Azure AD, vedere [Cos'è Azure AD](https://azure.microsoft.com/en-us/documentation/articles/active-directory-whatis/). Per altre informazioni, vedere la pagina Contratti di servizio.

> [!NOTE]
> Le sottoscrizioni di Azure con pagamento a consumo sono diverse: nonostante siano rappresentate anche nella directory, queste sottoscrizioni consentono di creare risorse Azure e di associarle al metodo di pagamento. In questo caso, NON esiste un numero di licenze associato alla sottoscrizione. L'associazione degli utenti alla sottoscrizione, per l'accesso alla gestione delle risorse della sottoscrizione, si ottiene concedendo agli utenti le autorizzazioni per eseguire operazioni sulle risorse di Azure associate alla sottoscrizione.

## <a name="how-does-azure-ad-licensing-work"></a>Come funzionano le licenze di Azure AD?

I servizi di Azure AD basati su licenza funzionano attivando una sottoscrizione nel tenant di directory/servizio di Azure AD. Quando la sottoscrizione è attiva, le funzionalità del servizio possono essere gestite dagli amministratori della directory o del servizio e usate dagli utenti con licenza.

Quando si acquista o si attiva Enterprise Mobility Suite, Azure AD Premium o Azure AD Basic, la directory viene aggiornata con la sottoscrizione, inclusi il relativo periodo di validità e le licenze prepagate. Le informazioni relative alla sottoscrizione, tra cui il numero di licenze disponibili o assegnate, sono disponibili tramite il Portale di Azure nel riquadro &gt; **Licenze** di Azure Active Directory per la directory specifica. Questa è anche la posizione migliore per gestire le assegnazioni delle licenze.

Ogni sottoscrizione è costituita da uno o più piani di servizio, ognuno associato al livello funzionale incluso del tipo di servizio, ad esempio, Azure AD, Azure MFA, Microsoft Intune, Exchange Online o SharePoint Online.  La gestione delle licenze di Azure AD NON richiede la gestione del livello del piano di servizio. Questo aspetto differisce da Office 365, che si basa su questa modalità di configurazione avanzata per gestire l'accesso ai servizi inclusi. Azure AD si basa sulla configurazione del servizio, per abilitare funzionalità e gestire autorizzazioni individuali.

> [!IMPORTANT]
> Le sottoscrizioni di Azure AD Premium e Basic e quelle di Enterprise Mobility Suite sono limitate alla directory o al tenant di cui è stato effettuato il provisioning. Le sottoscrizioni non possono essere suddivise tra le directory o usate per autorizzare utenti in altre directory. Lo spostamento di una sottoscrizione tra directory è possibile, ma richiede l'invio di un ticket di supporto o l'annullamento e il riacquisto, nel caso di acquisti diretti.
>
> Al momento dell'acquisto di Azure AD o di Enterprise Mobility Suite con contratti multilicenza, l'attivazione della sottoscrizione viene eseguita automaticamente quando il contratto include altri Microsoft Online Services, ad esempio Office 365.

### <a name="assigning-licenses"></a>Assegnazione delle licenze

Anche se per configurare le funzionalità a pagamento è sufficiente ottenere una sottoscrizione, l'uso delle funzionalità a pagamento di Azure AD richiede la distribuzione delle licenze agli utenti giusti. In generale, a tutti gli utenti che devono accedere a una funzionalità a pagamento di Azure AD o gestiti tramite tale funzionalità, deve essere assegnata una licenza. Un'assegnazione di licenze è un mapping tra un utente e un servizio acquistato, ad esempio Azure AD Premium, Basic o Enterprise Mobility Suite.

Gestire quali utenti nella directory devono disporre di una licenza è semplice. Questo è possibile assegnando le licenze a gruppi nel Portale di Azure o assegnando le licenze direttamente agli utenti giusti tramite il portale, PowerShell o le API. Quando si assegnano le licenze a un gruppo, a tutti i membri del gruppo verrà assegnata una licenza. Quando gli utenti vengono aggiunti al gruppo oppure rimossi dal gruppo, anche la licenza appropriata verrà assegnata o rimossa. L’opzione di assegnazione al gruppo può utilizzare qualsiasi tipo di gestione del gruppo disponibile ed è coerente con l’assegnazione alle applicazioni in base al gruppo. Utilizzando questo approccio, è possibile impostare regole che prevedano l’assegnazione automatica a tutti gli utenti nella directory, assicurarsi che tutti gli utenti con la posizione appropriata dispongano di una licenza o anche delegare la decisione ad altri responsabili dell'organizzazione. 

Per una discussione dettagliata sull'assegnazione delle licenze ai gruppi, inclusi scenari di gestione licenze di Office 365 e scenari avanzati, vedere [questo articolo](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-licensing-group-assignment-azure-portal).

## <a name="getting-started-with-azure-ad-licensing"></a>Introduzione alle licenze di Azure AD

Iniziare a usare Azure AD è semplice; è sempre possibile creare la propria directory come parte di una registrazione a una versione di valutazione gratuita di Azure. [Altre informazioni sulla registrazione come organizzazione](https://azure.microsoft.com/en-us/documentation/articles/sign-up-organization/). Quanto riportato di seguito può essere utile per verificare che la directory sia perfettamente allineata con i servizi Microsoft potenzialmente in uso o che si prevede di utilizzare e con gli obiettivi di ottenimento del servizio.

Queste sono le procedure consigliate:

- Se si usa già uno qualsiasi dei servizi aziendali di Microsoft, è già disponibile una directory di Azure AD. In questo caso, è necessario continuare a usare la stessa directory per altri servizi, in modo che la gestione delle identità di base, tra cui provisioning e Single Sign-On (SSO) ibrido, possa essere usata in tutti i servizi. Gli utenti avranno un unico punto di accesso e potranno beneficiare di funzionalità più complesse tra i servizi. Di conseguenza, se si decide di acquistare un servizio a pagamento di Azure AD per la propria forza lavoro, è consigliabile usare la stessa directory per il servizio.

- Se si prevede di usare Azure AD per un altro tipo di utenti, ad esempio partner, clienti e così via, se si vogliono valutare i servizi di Azure AD in modo indipendente dal servizio di produzione o si intende configurare un ambiente sandbox per i servizi, è consigliabile creare prima una nuova directory dal Portale di Azure classico. Altre informazioni sulla creazione di una nuova directory di Azure AD nel Portale di Azure classico. La nuova directory verrà creata con l'account come utente esterno con autorizzazioni di amministratore globale. Accedendo al Portale di Azure con questo account, sarà possibile visualizzare la directory e accedere a tutte le attività di amministrazione della directory stessa.

> [!NOTE]
> Azure AD supporta "utenti esterni", ovvero gli account utente di un'istanza di Azure AD creati utilizzando un account Microsoft (MSA) o un'identità di Azure AD da un'altra directory. Sebbene sia in corso l’estensione di questa funzionalità a tutti i servizi aziendali di Microsoft, al momento tali account non sono supportati in alcune delle esperienze di servizi; ad esempio, il portale di amministrazione di Office 365 non supporta attualmente tali utenti. Di conseguenza, gli utenti esterni con account Microsoft non saranno in grado di accedere al portale di amministrazione di Office 365, mentre gli utenti esterni di altre directory Azure AD verranno ignorati. Nel secondo caso, tramite queste esperienze è possibile accedere solo all'account locale dell'utente oppure alla directory Azure AD o Office 365 in cui è stato originariamente creato l'utente.

Come indicato, Azure AD dispone di diverse versioni di pagamento. Queste versioni dispongono di alcune piccole differenze in merito alla disponibilità di acquisto:

|  Prodotto       |              EA/VL  | Apri  | CSP |  Diritti di utilizzo MPN  | Acquisto diretto |  Versione di valutazione |
|  -------------- | ------------- | ------- | ------ | ----- | ---------------- | ----------------- | ------- |
|  Enterprise Mobility Suite  | X   |    X  |    X  |     X | &nbsp;  | X |
|  Azure AD Premium P2     |    X    |   X   |   X   | &nbsp;  |  X  |   X  
|  Azure AD Premium P1     |    X   |    X    |  X   |  &nbsp; |  X  |  &nbsp; |             
|  Azure AD Basic          |    X   |    X   |  X  |  X  | &nbsp; | &nbsp;  |

### <a name="select-one-or-more-license-trials"></a>Selezionare una o più versioni di valutazione delle licenza

È possibile attivare una sottoscrizione di valutazione di Azure AD Premium o Enterprise Mobility Suite in Azure Active Directory &gt; **Avvio rapido**.

![selezionare una versione di valutazione della licenza](media/active-directory-licensing-get-started-azure-portal/select-a-license-trial.png)

Le licenze di valutazione saranno ora disponibili nel pannello **Licenze**.

### <a name="assign-licenses-to-users-and-groups"></a>Assegnare licenze a utenti e gruppi

Quando la sottoscrizione è attiva, è necessario assegnare una licenza a se stessi e aggiornare il browser per verificare che siano visualizzate tutte le funzionalità. Il passaggio successivo è assegnare licenze agli utenti che dovranno accedere o essere inclusi nelle funzionalità a pagamento di Azure AD. Come accennato in precedenza in [Assegnazione delle licenze](#assigning-licenses), il modo migliore per eseguire questa operazione è identificare il gruppo che rappresenta i destinatari desiderati e assegnargli la licenza; così facendo, agli utenti aggiunti al gruppo o rimossi dal gruppo nel corso del ciclo di vita verrà rispettivamente assegnata o rimossa la licenza.

> [!NOTE]
> Alcuni servizi Microsoft non sono disponibili in tutte le località; prima di assegnare una licenza a un utente, l'amministratore deve specificare la proprietà "Località di utilizzo" per l'utente. Questa operazione può essere eseguita al percorso Utente &gt; Profilo &gt; Impostazioni nel Portale di Azure. Quando si usa l'assegnazione di licenze ai gruppi, eventuali utenti per cui non è specificata una località d'uso erediteranno la località della directory.

Per assegnare una licenza a un gruppo o a singoli utenti, in Azure Active Directory &gt; Licenze &gt; All Products (Tutti i prodotti) selezionare uno o più prodotti e fare clic sul pulsante **Assegna** nella barra dei comandi.

![selezionare una licenza da assegnare](media/active-directory-licensing-get-started-azure-portal/select-license-to-assign.png)

Viene visualizzato un nuovo pannello in cui è possibile scegliere più utenti o gruppi e, facoltativamente, disabilitare i piani di servizio nel prodotto. Per cercare nomi di utenti e gruppi, è possibile usare la barra di ricerca nella parte superiore.

![selezionare un utente o gruppo per l'assegnazione delle licenze](media/active-directory-licensing-get-started-azure-portal/select-user-for-license-assignment.png)

Quando si assegna una licenza al gruppo, prima che tutti gli utenti ereditino la licenza potrebbe trascorrere del tempo a seconda del numero di utenti del gruppo. Lo stato dell'elaborazione può essere controllato nel pannello del gruppo, nel riquadro **Licenze**.

![stato di assegnazione delle licenze](media/active-directory-licensing-get-started-azure-portal/license-assignment-status.png)

Durante l'assegnazione delle licenze di Azure AD, possono verificarsi errori di assegnazione, che però sono relativamente rari quando si gestiscono prodotti Azure ED e EMS. I potenziali errori di assegnazione sono limitati a:
- Conflitto di assegnazione: si verifica se in precedenza è stata assegnata all'utente una licenza non compatibile con la licenza corrente. In questo caso, per assegnare la nuova licenza è necessario rimuovere quella attuale.
- Superamento della soglia di licenze disponibili: quando il numero di utenti nei gruppi assegnati supera le licenze disponibili, lo stato di assegnazione degli utenti rifletterà un errore di assegnazione per mancanza di licenze.

Informazioni dettagliate sull'assegnazione di licenze ai gruppi sono disponibili in questo articolo.

### <a name="view-assigned-licenses"></a>Visualizzare licenze assegnate

In Azure Active Directory &gt; **Licenze** &gt; **All Products** (Tutti i prodotti) viene mostrato un riepilogo delle licenze assegnate e disponibili.

![visualizzare il riepilogo delle licenze](media/active-directory-licensing-get-started-azure-portal/view-license-summary.png)

Quando si fa clic su un prodotto specifico, è disponibile un elenco dettagliato di utenti e gruppi assegnati . **Utenti con licenza** visualizza tutti gli utenti che attualmente usano una licenza, incluse informazioni sul fatto che la licenza è stata assegnata direttamente all'utente o è stata ereditata da un gruppo.

![visualizzare i dettagli delle licenze](media/active-directory-licensing-get-started-azure-portal/view-license-detail.png)

Analogamente, **Gruppi con licenza** visualizza tutti i gruppi a cui sono state assegnate licenze. Fare clic sull'utente o sul gruppo in queste viste per aprire il pannello **Licenze** contenente tutte le licenze assegnate all'oggetto.

### <a name="removing-a-license"></a>Rimozione di una licenza

Per rimuovere una licenza, passare all'utente o gruppo e aprire il riquadro **Licenze**. Selezionare la licenza e fare clic su **Rimuovi**.

![rimuovere una licenza](media/active-directory-licensing-get-started-azure-portal/remove-license.png)

Si noti che non è possibile rimuovere direttamente licenze ereditate dall'utente da un gruppo. In alternativa, rimuovere l'utente dal gruppo da cui sta ereditando la licenza.

### <a name="extending-trials"></a>Estendere le versioni di valutazione

Le estensioni delle versioni di valutazione per i clienti sono disponibili come servizio self-service tramite il portale di Office 365. L'amministratore del cliente può accedere al portale di Office (l'accesso dipende dalle autorizzazioni per il portale di Office) e selezionare la sua versione di valutazione di Azure AD Premium. Fare clic sul collegamento **Estendi periodo di valutazione** per avviare il processo di estensione. È necessaria una carta di credito, ma non verrà addebitata alcuna somma.

![estendere una versione di valutazione nel Portale di Azure](media/active-directory-licensing-get-started-azure-portal/extend-trial-beginning.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sugli scenari avanzati per la gestione delle licenze tramite i gruppi, vedere questo articolo.

A questo punto si è pronti per configurare e utilizzare alcune delle funzionalità di Azure AD Premium.

* [Reimpostazione della password self-service](active-directory-manage-passwords.md)
* [Gestione di gruppi self-service](active-directory-accessmanagement-self-service-group-management.md)
* [Stato di Azure AD Connect](active-directory-aadconnect-health.md)
* [Assegnazione di gruppi alle applicazioni](active-directory-manage-groups.md)
* [Assegnazione delle licenze a un gruppo](active-directory-licensing-group-assignment-azure-portal.md)
* [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
* [Acquisto diretto di licenze di Azure AD Premium](http://aka.ms/buyaadp)

