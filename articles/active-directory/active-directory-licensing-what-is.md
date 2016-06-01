<properties
    pageTitle="Che cosa sono le licenze di Microsoft Azure Active Directory? | Microsoft Azure"
    description="Descrizione delle licenze di Microsoft Azure Active Directory, del funzionamento, di come iniziare a utilizzarlo e delle procedure consigliate, tra cui Office 365, Microsoft Intune e le edizioni di base e Premium di Azure Active Directory"
    services="active-directory"
	  keywords="Licenze di Azure AD"
    documentationCenter=""
    authors="curtand"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="05/16/2016"
    ms.author="curtand"/>

# Che cosa sono le licenze di Microsoft Azure Active Directory?

##Descrizione
Azure Active Directory (Azure AD) è una soluzione e piattaforma IDaaS (Identity as a Service) di Microsoft. Azure AD è offerta in varie versioni funzionali e tecniche che spaziano da Azure AD Free, disponibile con qualsiasi servizio Microsoft come Office 365, Dynamics, Microsoft Intune e Azure (Azure AD non genera alcun addebito di utilizzo in questa modalità), alle versioni Azure AD a pagamento quali Enterprise Mobility Suite (EMS), Azure AD Premium e Basic nonché Azure Multi-Factor Authentication (MFA). Come molti servizi online Microsoft, la maggior parte delle versioni a pagamento di Azure AD sono fornite tramite diritti per utente in quanto sono in Office 365, Microsoft Intune e Azure AD. In questi casi, l'acquisto del servizio è rappresentato da una o più sottoscrizioni e ogni sottoscrizione include un numero di pre-acquisto di licenze nel tenant. I diritti per utente vengono ottenuti tramite assegnazione delle licenze, creazione di un collegamento tra l'utente e il prodotto, abilitazione dei componenti del servizio per l'utente e utilizzo di una delle licenze prepagate.

[Provare Azure AD Premium](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

> [AZURE.NOTE] Il portale di amministrazione di AD Azure fa parte del portale di Azure classico. Sebbene l'uso di Azure AD non richieda alcun acquisto di Azure, l'accesso a questo portale richiede una sottoscrizione Azure attiva o una [sottoscrizione di valutazione di Azure](https://azure.microsoft.com/pricing/free-trial/).

Per una panoramica estesa delle funzionalità del servizio Azure AD, vedere [Cos'è Azure AD](active-directory-whatis.md). [Ulteriori informazioni sui livelli di servizio Azure AD](https://azure.microsoft.com/support/legal/sla/)

> [AZURE.NOTE]  Le sottoscrizioni di Azure con pagamento a consumo sono diverse: sebbene rappresentate anche nella directory, queste sottoscrizioni consentono di creare risorse Azure e di eseguirne il mapping al metodo di pagamento. In questo caso NON esiste alcun numero di licenza associato alla sottoscrizione. Per ottenere l’associazione degli utenti alla sottoscrizione e l'accesso degli utenti per la gestione delle risorse di sottoscrizione, è necessaria la concessione di autorizzazioni per operare su risorse Azure mappate alla sottoscrizione.


##Come funzionano le licenze di Azure AD?

I servizi di Azure AD basati su licenza o diritti funzionano attivando una sottoscrizione nel tenant di directory/servizio di Azure AD. Una volta attivata la sottoscrizione, le funzionalità del servizio possono essere gestite dagli amministratori della directory o del servizio e utilizzate dagli utenti con licenza.

Quando viene acquistata o attivata una versione Enterprise Mobility Suite, Azure AD Premium o Azure AD Basic, la directory viene aggiornata con la sottoscrizione, inclusi il relativo periodo di validità e le licenze prepagate. Le informazioni relative alla sottoscrizione, tra cui lo stato, il successivo evento del ciclo di vita e il numero di licenze assegnate o disponibili, sono disponibili tramite il portale di Azure classico nella scheda Licenze per la directory specifica. È anche la posizione migliore per gestire le assegnazioni delle licenze.

Ogni sottoscrizione è costituita da uno o più piani di servizio, ognuno dei quali esegue il mapping del livello di funzionalità incluso del tipo di servizio, ad esempio, Azure AD, Azure MFA, Microsoft Intune, Exchange Online o SharePoint Online. La gestione delle licenze Azure AD NON richiede la gestione del livello del piano di servizio. Questo aspetto differisce da Office 365, che si basa su questa modalità di configurazione avanzata per gestire l'accesso ai servizi inclusi. Azure AD si basa sulla configurazione del servizio, per abilitare funzionalità e gestire autorizzazioni individuali.

In generale, le informazioni sulla sottoscrizione di Azure AD vengono gestite tramite il portale di Azure classico, nella scheda Licenze per la directory specifica. Le sottoscrizioni di Azure AD, ad eccezione di Azure AD Premium, NON vengono visualizzate nel portale di Office.

> [AZURE.IMPORTANT] Le sottoscrizioni di Azure AD Premium e Basic e di Enterprise Mobility Suite, sono limitate alla relativa directory o al relativo tenant con provisioning. Le sottoscrizioni non possono essere suddivise tra le directory o utilizzate per autorizzare utenti in altre directory. Lo spostamento di una sottoscrizione tra directory è possibile, ma richiede l'invio di un ticket di supporto oppure l’annullamento e la ripetizione dell’acquisto nel caso di acquisti diretti.

> Al momento dell'acquisto di Azure AD o Enterprise Mobility Suite tramite i contratti multilicenza, l'attivazione della sottoscrizione viene eseguita automaticamente quando il contratto include altri servizi Microsoft Online, ad esempio Office 365.

Le funzionalità Azure Ad a pagamento estendono la directory. Tra gli esempi sono inclusi:
- L'assegnazione alle applicazioni in base al gruppo viene abilitata nell'ambito dell'applicazione specifica che si sta gestendo.
- Le funzionalità avanzate e self-service di gestione dei gruppi sono disponibili nella configurazione della directory o all'interno del gruppo specifico.
- I report sulla sicurezza Premium sono nella scheda Reporting
- L'individuazione delle applicazioni cloud viene visualizzata nel portale di Azure sotto Identità.

###Assegnazione delle licenze
Sebbene per configurare le funzionalità a pagamento sia sufficiente ottenere una sottoscrizione, l’utilizzo delle funzionalità a pagamento di Azure AD richiede la distribuzione delle licenze agli utenti giusti. In generale, a tutti gli utenti che devono accedere a una funzionalità a pagamento di Azure AD o gestiti tramite tale funzionalità, deve essere assegnata una licenza. Un'assegnazione di licenze è un mapping tra un utente e un servizio acquistato, ad esempio Azure AD Premium, Basic o Enterprise Mobility Suite.

Gestire quali utenti nella directory devono disporre di una licenza è semplice. È possibile procedere all’assegnazione a un gruppo per creare regole di assegnazione tramite il portale di amministrazione di Azure AD oppure all’assegnazione diretta delle licenze agli utenti giusti tramite un portale, PowerShell o API. Quando si assegnano le licenze a un gruppo, a tutti i membri del gruppo verrà assegnata una licenza. Quando gli utenti vengono aggiunti al gruppo oppure rimossi dal gruppo, anche la licenza appropriata verrà assegnata o rimossa. L’opzione di assegnazione al gruppo può utilizzare qualsiasi tipo di gestione del gruppo disponibile ed è coerente con l’assegnazione alle applicazioni in base al gruppo. Utilizzando questo approccio, è possibile impostare regole che prevedano l’assegnazione automatica a tutti gli utenti nella directory, assicurarsi che tutti gli utenti con la posizione appropriata dispongano di una licenza o anche delegare la decisione ad altri responsabili dell'organizzazione.

Con l'assegnazione delle licenze in base al gruppo, ogni utente privo di un percorso di utilizzo erediterà il percorso della directory durante l'assegnazione. Questo percorso può essere modificato dall'amministratore in qualsiasi momento. Quando l'assegnazione automatica non riesce a causa di un errore, le informazioni utente in tale tipo di licenza rifletteranno lo stato.

##Introduzione alle licenze di Azure AD

Iniziare a utilizzare Azure AD è semplice; è sempre possibile creare la propria directory come parte di una registrazione a una versione di valutazione gratuita di Azure. [Altre informazioni sulla registrazione come organizzazione](sign-up-organization.md). Quanto riportato di seguito può essere utile per verificare che la directory sia perfettamente allineata con i servizi Microsoft potenzialmente in uso o che si prevede di utilizzare e con gli obiettivi di ottenimento del servizio.

Queste sono le procedure consigliate:
- Se si usa già uno qualsiasi dei servizi aziendali di Microsoft, è già disponibile una directory di Azure AD. In questo caso, è necessario continuare a utilizzare la stessa directory per altri servizi, in modo che la gestione delle identità core, tra cui provisioning e SSO ibrido, possa essere utilizzata in tutti i servizi. Gli utenti avranno un unico punto di accesso e potranno beneficiare di funzionalità più complesse tra i servizi. Di conseguenza, se si decide di acquistare un servizio a pagamento di Azure AD per la propria forza lavoro, è consigliabile usare la stessa directory per il servizio.
- Se si prevede di usare Azure AD per un altro tipo di utenti, ad esempio partner, clienti e così via, si vuole valutare i servizi di Azure AD in modo indipendente dal servizio di produzione o si intende configurare un ambiente sandbox per i servizi, è consigliabile creare prima una nuova directory dal portale di Azure classico. [Altre informazioni sulla creazione di una nuova directory di Azure AD nel portale di Azure classico](active-directory-licensing-directory-independence.md). La nuova directory verrà creata con l'account come utente esterno con autorizzazioni di amministratore globale. Accedendo al portale di Azure classico con questo account, sarà possibile visualizzare la directory e accedere a tutte le attività di amministrazione della directory. Si consiglia di creare un account locale con privilegi appropriati per la gestione di altri servizi Microsoft (quelli a cui non è possibile accedere tramite il portale di Azure classico). [Altre informazioni sulla creazione di account utente in Azure AD](active-directory-create-users.md).

> [AZURE.NOTE] Azure AD supporta "utenti esterni", ovvero gli account utente di un'istanza di Azure AD creati utilizzando un account Microsoft (MSA) o un'identità di Azure AD da un'altra directory. Sebbene sia in corso l’estensione di questa funzionalità a tutti i servizi aziendali di Microsoft, al momento tali account non sono supportati in alcune delle esperienze di servizi; ad esempio, il portale di amministrazione di Office 365 non supporta attualmente tali utenti. Di conseguenza, gli utenti esterni con account Microsoft non saranno in grado di accedere al portale di amministrazione di Office 365, mentre gli utenti esterni di altre directory Azure AD verranno ignorati. Nel secondo caso, solo l'account locale dell'utente, la directory Azure AD o Office 365 in cui è stato originariamente creato l’utente, sarebbe accessibile tramite queste esperienze.

Come indicato, Azure AD dispone di diverse versioni di pagamento. Queste versioni dispongono di alcune piccole differenze in merito alla disponibilità di acquisto:


| Prodotto | EA/VL | Apri | 	CSP | 	Diritti di utilizzo MPN | 	Acquisto diretto | Versione di valutazione |
|---|---|---|---|---|---|---|
| Enterprise Mobility Suite |	X |	X |	X |	X | |	 	X |
| Azure AD Premium | X | X | X | | X | X |
| Azure AD Basic | X | X | X | X | <br /> | <br /> |

###Selezionare una o più versioni di valutazione delle licenza
 In tutti i casi, è possibile attivare una sottoscrizione di valutazione di Azure AD Premium o Enterprise Mobility Suite selezionando la versione di valutazione specifica desiderata nella scheda Licenze della directory. Entrambe le versioni di valutazione contengono una sottoscrizione di 30 giorni con 100 licenze.

![Piani di licenza per le versioni di valutazione di Azure Active Directory](./media/active-directory-licensing-what-is/trial_plans.png)

![Piani di licenza per le versioni di valutazione di Enterprise Mobility Suite](./media/active-directory-licensing-what-is/EMS_trial_plan.png)

![Piani di licenza per le versioni di valutazione attive](./media/active-directory-licensing-what-is/active_license_trials.png)

###Assegnare licenze
Quando la sottoscrizione è attiva, è necessario assegnare una licenza a se stessi e aggiornare il browser per verificare che siano visualizzate tutte le funzionalità. Il passaggio successivo è assegnare licenze agli utenti che dovranno accedere o essere inclusi nelle funzionalità a pagamento di Azure AD. Come accennato in precedenza in "Assegnazione delle licenze", il modo migliore per eseguire questa operazione è identificare il gruppo che rappresenta il gruppo di destinatari desiderato e assegnarlo alla licenza; così facendo, gli utenti aggiunti al gruppo o rimossi dal gruppo nel corso del ciclo di vita verranno assegnati alla licenza o rimossi dalla licenza.

Per assegnare una licenza a un gruppo o a singoli utenti, selezionare il piano di licenza che si desidera assegnare e fare clic su **Assegna** nella barra dei comandi.

![Piani di licenza per le versioni di valutazione attive](./media/active-directory-licensing-what-is/assign_licenses.png)

Nella finestra di dialogo di assegnazione per il piano selezionato è possibile selezionare gli utenti e aggiungerli nella colonna **Assegna** a destra. È possibile scorrere l'elenco degli utenti o cercare utenti specifici utilizzando l’icona a forma di binocolo nella parte superiore destra della griglia utenti. Per assegnare i gruppi, selezionare "Gruppi" dal menu **Mostra** e fare clic sul pulsante con il segno di spunta a destra per aggiornare le assegnazioni visualizzate.

![Assegnazione delle licenze ai gruppi](./media/active-directory-licensing-what-is/assign_licenses_to_groups.png)

È ora possibile eseguire ricerche o spostarsi tra i gruppi e aggiungerli alla colonna **Assegna** allo stesso modo. È possibile utilizzare queste funzioni per assegnare una combinazione di utenti e gruppi in un'unica operazione. Per completare il processo di assegnazione, fare clic sul pulsante con segno di spunta nell'angolo inferiore destro della pagina.

![Messaggio di stato per l’assegnazione di licenze](./media/active-directory-licensing-what-is/license_assignment_progress_message.png)

Quando viene assegnato un gruppo, i relativi membri ereditano le licenze entro 30 minuti, ma in genere entro 1-2 minuti.

Durante l'assegnazione delle licenze di Azure AD, possono verificarsi errori di assegnazione, ma sono relativamente rari. I potenziali errori di assegnazione sono limitati a:
- Conflitto di assegnazione: si verifica se a un utente in precedenza è stata assegnata una licenza non compatibile con la licenza corrente. In questo caso per assegnare la nuova licenza è necessario rimuovere quella precedente.
- Superamento della soglia di licenze disponibili: quando il numero di utenti nei gruppi assegnati supera le licenze disponibili lo stato di assegnazione degli utenti rifletterà un errore di assegnazione per mancanza di licenze.

###Visualizzare licenze assegnate

Nella scheda **Licenze** viene visualizzata una panoramica di riepilogo delle licenze assegnate che include l’evento del ciclo di vita di sottoscrizione disponibile, assegnato e successivo.

![Visualizzazione del numero di licenze assegnate](./media/active-directory-licensing-what-is/view_assigned_licenses.png)

Un elenco dettagliato degli utenti e dei gruppi assegnati, con lo stato di assegnazione e il percorso (diretto o ereditato da uno o più gruppi), è disponibile esplorando un piano di licenza.

![Visualizzazione dettagliata delle licenze assegnate per un piano di licenza](./media/active-directory-licensing-what-is/assigned_licenses_detail.png)

Rimuovere le licenze è facile quanto assegnarle. Se l'utente è assegnato direttamente o mediante un gruppo assegnato, per rimuovere la licenza è possibile selezionare il tipo di licenza, fare clic su **Rimuovi**, aggiungere l'utente o il gruppo all'elenco da rimuovere e confermare l'operazione. In alternativa, è possibile aprire un tipo di licenza, selezionare l'utente o il gruppo specifico e toccare **Rimuovi** sulla barra dei comandi. Per terminare l'ereditarietà di una licenza per un utente appartenente a un gruppo, è sufficiente rimuovere l'utente dal gruppo.

###Estendere le versioni di valutazione

Le estensioni delle versioni di valutazione per i clienti sono disponibili come servizio self-service tramite il portale di Office 365. L'amministratore del cliente può accedere al [portale di Office](https://portal.office.com/#Billing) (l'accesso dipende dalle autorizzazioni per il portale di Office) e selezionare la propria versione di valutazione di Azure AD Premium. Fare clic sul collegamento **Estendi la versione di valutazione** e seguire le istruzioni. Sarà necessario immettere il numero di una carta di credito, ma non verrà eseguito alcun addebito.

![Estensione di una versione di valutazione di licenza nel portale di Office](./media/active-directory-licensing-what-is/extend_license_trial.png)

I clienti possono inoltre richiedere un'estensione della versione di valutazione inviando una richiesta di supporto. L'amministratore del cliente può accedere alla [pagina del servizio di supporto](http://aka.ms/extendAADtrial) del portale di Office 365 (l'accesso dipende dalle autorizzazioni per la pagina del servizio di supporto di Office). In questa pagina selezionare "Sottoscrizioni e versioni di valutazione" in Funzionalità e "Domande sulla versione di valutazione" in Sintomo. Infine, immettere le informazioni relative alle circostanze

![Estensione di una versione di valutazione di licenza tramite una richiesta di supporto](./media/active-directory-licensing-what-is/alternate_office_aad_trial_extension.png)

## Passaggi successivi

A questo punto si è pronti per configurare e utilizzare alcune delle funzionalità di Azure AD Premium.

- [Reimpostazione della password self-service](active-directory-manage-passwords.md)
- [Gestione di gruppi self-service](active-directory-accessmanagement-self-service-group-management.md)
- [Stato di Azure AD Connect](active-directory-aadconnect-health.md)
- [Assegnazione di gruppi alle applicazioni](active-directory-manage-groups.md)
- [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
- [Acquisto diretto di licenze di Azure AD Premium](http://aka.ms/buyaadp)

<!---HONumber=AcomDC_0518_2016-->