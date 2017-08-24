---
title: "Considerazioni di progettazione dell'identità ibrida di Azure Active Directory - Determinare la strategia di adozione del ciclo di vita della soluzione ibrida di gestione delle identità | Documentazione Microsoft"
description: "Consente di definire le attività della gestione ibrida delle identità in base alle opzioni disponibili per ogni fase del ciclo di vita."
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: 420b6046-bd9b-4fce-83b0-72625878ae71
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 18b40486a66d8e092a8af299460145989a1ab99d
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Determinare la strategia di adozione del ciclo di vita della soluzione ibrida di gestione delle identità
In questa attività verrà definita la strategia di gestione delle identità in modo che la soluzione ibrida soddisfi i requisiti aziendali definiti in [Determinare le attività della gestione ibrida delle identità](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md).

Per definire le attività della gestione ibrida delle identità in base al ciclo di vita end-to-end presentato in precedenza in questo passaggio, è necessario valutare le opzioni disponibili per ogni fase del ciclo di vita.

## <a name="access-management-and-provisioning"></a>Gestione e provisioning degli accessi
Una buona soluzione di gestione degli accessi degli account consente di monitorare in modo accurato chi ha accesso a quali informazioni all'interno dell'organizzazione.

Il controllo di accesso è una funzione critica di un sistema di provisioning centralizzato. Oltre a proteggere le informazioni sensibili, il controllo di accesso espone gli account esistenti con autorizzazioni non approvate o non più necessari. Per controllare gli account obsoleti, il sistema di provisioning collega le informazioni dell'account alle informazioni autorevoli relative agli utenti proprietari degli account. Le informazioni autorevoli sulle identità utente sono in genere mantenute nei database e nelle directory del reparto risorse umane.

Nelle aziende IT sofisticate gli account includono centinaia di parametri che definiscono le autorità e questi dettagli possono essere controllati dal sistema di provisioning. È possibile identificare i nuovi utenti con i dati forniti dall'origine autorevole. La funzionalità di approvazione della richiesta di accesso avvia i processi che approvano (o rifiutano) il provisioning delle risorse per tali utenti.

| Fase di gestione del ciclo di vita | Locale | Cloud | Ibrido |
| --- | --- | --- | --- |
| Gestione e provisioning degli account |Il ruolo del server Servizi di dominio Active Directory® consente di creare un'infrastruttura scalabile, sicura e gestibile per la gestione di utenti e risorse, nonché di fornire supporto per le applicazioni abilitate per le directory, ad esempio Microsoft® Exchange Server. <br><br> [È possibile eseguire il provisioning dei gruppi in Servizi di dominio Active Directory tramite un Identity manager](https://technet.microsoft.com/library/ff686261.aspx) <br>[ È possibile eseguire il provisioning degli utenti in Servizi di dominio Active Directory](https://technet.microsoft.com/library/ff686263.aspx) <br><br> Gli amministratori possono usare il controllo di accesso per gestire l'accesso degli utenti alle risorse condivise per motivi di sicurezza. In Active Directory il controllo di accesso è gestito a livello di oggetto impostando diversi livelli di accesso, o autorizzazioni, agli oggetti, ad esempio Controllo completo, Scrittura, Lettura o Nessun accesso. Il controllo di accesso in Active Directory definisce il modo in cui gli utenti possono usare gli oggetti di Active Directory. Per impostazione predefinita, le autorizzazioni sugli oggetti in Active Directory sono configurate sull'impostazione più sicura. |È necessario creare un account per ogni utente che accederà a un servizio cloud Microsoft. È anche possibile modificare gli account utente oppure eliminarli quando non sono più necessari. Per impostazione predefinita, gli utenti non hanno autorizzazioni di amministratore, ma è possibile scegliere facoltativamente di assegnarle. Per altre informazioni, vedere [Gestione degli utenti in Azure AD](active-directory-create-users.md). <br><br> In Azure Active Directory una delle caratteristiche principali è la possibilità di gestire l'accesso alle risorse. Queste risorse possono far parte della directory, come nel caso delle autorizzazioni per gestire oggetti tramite i ruoli nella directory, o delle risorse esterne alla directory, come ad esempio le applicazioni SaaS, i servizi di Azure e i siti di SharePoint o delle risorse locali. <br><br> L'elemento principale della soluzione per la gestione degli accessi di Azure Active Directory è il gruppo di sicurezza. Il proprietario della risorsa (o l'amministratore della directory) può assegnare ad un gruppo il compito di fornire un determinato diritto di accesso per le risorse che possiede. Ai membri del gruppo verrà fornito l'accesso e il proprietario della risorsa può delegare il diritto di gestire l'elenco di membri di un gruppo a un altro utente, ad esempio un responsabile di reparto o un amministratore dell'helpdesk<br> <br> L'argomento relativo alla gestione dei gruppi in Azure AD fornisce informazioni dettagliate su come gestire gli accessi tramite i gruppi. |Estendere le identità di Active Directory nel cloud tramite la sincronizzazione e la federazione |

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo
Il controllo degli accessi in base al ruolo usa i ruoli e i criteri di provisioning per valutare, testare e imporre regole e processi aziendali per concedere l'accesso agli utenti. Gli amministratori principali creano i criteri di provisioning, assegnano gli utenti ai ruoli e definiscono set di assegnazioni alle risorse per questi ruoli. Il controllo degli accessi in base al ruolo estende la soluzione di gestione delle identità per usare processi basati sul software e ridurre l'interazione manuale dell'utente nel processo di provisioning.
Il controllo degli accessi in base al ruolo di Azure AD permette alla società di limitare la quantità di operazioni che possono essere eseguite da un soggetto una volta effettuato l'accesso al portale di gestione di Azure. L'uso del controllo degli accessi in base al ruolo per controllare gli accessi al portale, permette agli amministratori IT di delegare l'accesso tramite gli approcci di gestione dell'accesso seguenti:

* **Assegnazione di ruolo basata su gruppo**: è possibile assegnare l'accesso ai gruppi di Azure AD che possono essere sincronizzati dall'istanza di Active Directory locale. Questo consente di sfruttare gli investimenti esistenti dell'organizzazione in termini di strumenti e processi per la gestione dei gruppi. È anche possibile usare la funzionalità di delega della gestione dei gruppi disponibile in Azure AD Premium.
* **Uso dei ruoli predefiniti in Azure**: sono disponibili tre ruoli, proprietario, collaboratore e lettore, per garantire che utenti e gruppi siano autorizzati a eseguire solo le attività necessarie per svolgere il proprio lavoro.
* **Accesso granulare alle risorse**: è possibile assegnare ruoli a utenti e gruppi per una sottoscrizione, un gruppo di risorse o una singola risorsa di Azure specifica, ad esempio un sito Web o un database. In questo modo, è possibile fare in modo che gli utenti possano accedere a tutte le risorse necessarie e non abbiano accesso alle risorse che invece non devono gestire.

## <a name="provisioning-and-other-customization-options"></a>Provisioning e altre opzioni di personalizzazione
Il team può usare piani e requisiti aziendali per stabilire in che misura personalizzare la soluzione di gestione delle identità. Ad esempio, per i flussi di lavoro e gli adattatori personalizzati una grande impresa può richiedere un piano di implementazione a fasi basato su una sequenza temporale per eseguire il provisioning delle applicazioni usate più di frequente per varie aree geografiche in modo incrementale. Un altro piano di personalizzazione può invece prevedere il provisioning di due o più applicazioni per l'intera organizzazione, una volta superata la fase di test. È possibile personalizzare l'interazione tra applicazione e utente e modificare le procedure per il provisioning delle risorse per adottare il provisioning automatizzato.

È possibile effettuare il deprovisioning per rimuovere un componente o un servizio. Quando si effettua il deprovisioning di un account, ad esempio, significa che l'account viene eliminato da una risorsa.

Il modello ibrido di provisioning delle risorse combina approcci basati su richiesta e sul ruolo, entrambi supportati da Azure AD. Un'azienda può ad esempio decidere di automatizzare l'accesso con l'assegnazione basata sul ruolo oppure di gestire tutte le altre eccezioni o richieste di accesso tramite un modello basato sulla richiesta. Altre aziende possono invece iniziare con l'assegnazione manuale per poi evolvere verso un modello ibrido, con l'intenzione di effettuare una distribuzione completamente basata sul ruolo in futuro.

Per altre organizzazioni, il provisioning completamente basato su ruolo potrebbe risultare un'opzione poco pratica per motivi di natura aziendale e essere indirizzati verso un approccio ibrido. Alcune società potrebbero già essere soddisfatte con il provisioning basato su richiesta e non intendono investire ulteriormente nella definizione e nella gestione dei criteri di provisioning automatizzato basato su ruolo.

## <a name="license-management"></a>Gestione licenze
La gestione delle licenze basata su gruppo in Azure AD consente agli amministratori di assegnare gli utenti a un gruppo di sicurezza e Azure AD assegna automaticamente le licenze a tutti i membri del gruppo. Se in seguito un utente viene aggiunto o rimosso dal gruppo, anche la licenza verrà assegnata o rimossa, a seconda del caso.

È possibile usare i gruppi sincronizzati da Active Directory locale o gestiti in Azure AD e in combinazione con la funzionalità Gestione gruppi in modalità self-service di Azure AD Premium è facile delegare l'assegnazione delle licenze ai decision maker appropriati. È garantito che problemi quali conflitti di licenze e dati mancanti per la località vengono risolti automaticamente.

## <a name="self-regulating-user-administration"></a>Regolazione automatica dell'amministrazione degli utenti
Quando l'azienda inizia a effettuare il provisioning delle risorse per tutte le organizzazioni interne, si implementa la funzionalità di regolazione automatica dell'amministrazione degli utenti. In questo caso, è possibile usufruire dei vantaggi derivanti dal provisioning degli utenti in tutta l'organizzazione. In questo ambiente, una modifica dello stato di un utente si riflette automaticamente nei diritti di accesso in tutta l'organizzazione e in tutte le aree geografiche. Consente di ridurre i costi di provisioning e semplificare i processi di accesso e approvazione. Permette di realizzare l'intero potenziale del controllo degli accessi in base al ruolo per la gestione degli accessi end-to-end nell'organizzazione. Favorisce la riduzione dei costi amministrativi tramite procedure automatizzate che regolamentano il provisioning degli utenti. Migliora la sicurezza grazie all'automazione dell'imposizione dei criteri di sicurezza, alla semplificazione e alla centralizzazione del ciclo di vita degli utenti e al provisioning delle risorse per grandi quantità di utenti.

> [!NOTE]
> Per altre informazioni, vedere Configurazione di Azure AD per la gestione self-service dell'accesso alle applicazioni
> 
> 

I servizi di Azure AD basati su licenza o diritti funzionano attivando una sottoscrizione nel tenant di directory/servizio di Azure AD. Una volta attivata la sottoscrizione, le funzionalità del servizio possono essere gestite dagli amministratori della directory o del servizio e utilizzate dagli utenti con licenza. Per altre informazioni, vedere la sezione Come funzionano le licenze di Azure AD?
Integrazione con altri provider di terze parti

Azure Active Directory offre autenticazione Single Sign-On e sicurezza avanzata per l'accesso alle applicazioni a migliaia di applicazioni SaaS e di applicazioni Web locali. Per un elenco dettagliato delle applicazioni SaaS supportate nella raccolta di applicazioni di Azure Active Directory, vedere l'elenco di compatibilità per la federazione di Azure Active Directory: provider di identità di terze parti che possono essere usati per implementare l'accesso Single Sign-On

## <a name="define-synchronization-management"></a>Definire la soluzione di gestione della sincronizzazione
L'integrazione delle directory locali con Azure AD rende gli utenti più produttivi in quanto fornisce un'identità comune per accedere alle risorse cloud e locali. Con questa integrazione utenti e organizzazioni possono sfruttare i vantaggi seguenti:

* Le organizzazioni possono fornire agli utenti un'identità ibrida comune per i servizi locali e basati sul cloud sfruttando Windows Server Active Directory per connettersi quindi ad Azure Active Directory.
* Gli amministratori possono fornire l'accesso condizionale in base alla risorsa dell'applicazione, al dispositivo e all'identità utente, al percorso di rete e all'autenticazione a più fattori.
* Gli utenti possono fruttare la propria identità comune degli account in Azure AD per Office 365, Intune, app SaaS e applicazioni di terze parti.
* Gli sviluppatori possono compilare applicazioni che sfruttano il modello di identità comune, integrando le applicazioni in Active Directory locale o in Azure per le applicazioni basate sul cloud.

La figura seguente mostra una panoramica generale del processo di sincronizzazione delle identità.

![](./media/hybrid-id-design-considerations/identitysync.png)

Processo di sincronizzazione delle identità

Esaminare la tabella seguente per un confronto tra le opzioni di sincronizzazione:

| Opzione di gestione della sincronizzazione | Vantaggi | Svantaggi: |
| --- | --- | --- |
| Basata sulla sincronizzazione (tramite DirSync o AAD Connect) |Utenti e gruppi sincronizzati in locale e su cloud <br>  **Controllo dei criteri**: è possibile impostare i criteri degli account tramite Active Directory. L'amministratore può quindi gestire i criteri per le password, le restrizioni per le workstation, i controlli di blocco e altri elementi senza dover eseguire attività aggiuntive nel cloud.  <br>  **Controllo di accesso**: l'amministratore può limitare l'accesso al servizio cloud in modo che i servizi siano accessibili tramite l'ambiente aziendale, tramite server online o entrambi. <br>  Diminuzione delle chiamate all'assistenza tecnica: se gli utenti devono ricordare un numero minore di password, è meno probabile che le dimentichino. <br>  Sicurezza: le identità e le informazioni degli utenti sono protette perché tutti i server e i servizi utilizzati per l'accesso Single Sign-On sono gestiti e controllati in locale. <br>  Supporto dell'autenticazione avanzata: è possibile usare l'autenticazione avanzata, nota anche come autenticazione a due fattori, con il servizio cloud. In tal caso, però, sarà necessario usare l'accesso Single Sign-On. | |
| Basata su federazione (tramite AD FS) |Abilitata tramite il servizio token di sicurezza. Quando si configura un servizio token di sicurezza per l'accesso Single Sign-On a un servizio Microsoft Cloud, si crea un trust federativo tra il servizio token di sicurezza locale e il dominio federato specificato nel tenant di Azure AD. <br> Consente agli utenti finali di usare lo stesso set di credenziali per accedere a più risorse <br>Gli utenti finali non devono gestire più set di credenziali, ma devono tuttavia fornire le credenziali a ognuna delle risorse partecipanti. Sono supportati gli scenari B2B e B2C. |Richiede personale specializzato per la distribuzione e la manutenzione di server AD FS locali dedicati. Sono previste limitazioni per l'uso dell'autenticazione avanzata nel caso in cui si intenda usare AD FS per il servizio token di sicurezza. Per altre informazioni, vedere [Configurazione delle opzioni avanzate per AD FS 2.0](http://go.microsoft.com/fwlink/?linkid=235649). |

> [!NOTE]
> Per altre informazioni, vedere [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).
> 
> 

## <a name="see-also"></a>Vedere anche
[Panoramica delle considerazioni di progettazione](active-directory-hybrid-identity-design-considerations-overview.md)


