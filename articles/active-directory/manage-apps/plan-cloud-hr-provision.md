---
title: Pianificare l'applicazione cloud HR per Azure Active Directory il provisioning degli utenti
description: Questo articolo descrive il processo di distribuzione dell'integrazione dei sistemi HR cloud, ad esempio la giornata lavorativa e Sucessfactors con Azure Active Directory. L'integrazione di Azure AD con il sistema HR cloud genera un sistema di gestione del ciclo di vita delle identità completo.
services: active-directory
author: martincoetzer
manager: CelesteDG
tags: azuread
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: martinco
ms.reviewer: arvindha
ms.openlocfilehash: 5d55aafc29b3b022d1023077d2d8f459b0608ae7
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555652"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Pianificare l'applicazione cloud HR per Azure Active Directory il provisioning degli utenti

Storicamente, il personale IT si è affidato a metodi manuali per la creazione, l'aggiornamento e l'eliminazione di dipendenti, ad esempio il caricamento di file CSV o script personalizzati per sincronizzare i dati dei dipendenti. Questi processi di provisioning sono soggetti a errori, non sicuri e difficili da gestire.

Per gestire facilmente i cicli di vita delle identità end-to-end dei dipendenti, dei fornitori o dei ruoli di lavoro condizionali, il [servizio di provisioning utenti Azure Active Directory (Azure ad)](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) offre l'integrazione con applicazioni HR (Human Resource) basate sul cloud come la giornata lavorativa o SuccessFactors.

Azure AD usa questa integrazione per abilitare i flussi di lavoro delle applicazioni HR cloud (app) seguenti:

- **Provisioning** di utenti a set di utenti selezionati da un'app Cloud HR in uno o più domini Active Directory (ad).
- **Provisioning di utenti solo cloud in** scenari di Azure ad in cui non viene usato ad, effettuare il provisioning degli utenti direttamente dall'app Cloud HR per Azure ad.
- **Scrivere nuovamente nell'app Cloud HR.** -scrivere gli indirizzi di posta elettronica e gli attributi del nome utente dal Azure AD di nuovo all'app HR cloud.

> [!NOTE]
> Questo piano di distribuzione guida come distribuire i flussi di lavoro delle app HR cloud con Azure AD provisioning degli utenti. Per informazioni sulla distribuzione del provisioning utenti automatico in app SaaS, vedere [pianificare una distribuzione automatica del provisioning utenti](https://aka.ms/deploymentplans/provisioning).

## <a name="enabled-hr-scenarios"></a>Scenari HR abilitati

Il servizio di provisioning utenti Azure AD consente l'automazione dei seguenti scenari di gestione del ciclo di vita delle identità basati sulle risorse umane:

- **Assunzione di nuovi dipendenti** : quando viene aggiunto un nuovo dipendente all'app Cloud HR, viene creato automaticamente un account utente in Active directory e Azure ad con l'opzione per eseguire il writeback dell'indirizzo di posta elettronica e degli attributi del nome utente nell'app Cloud HR.
- **Aggiornamenti del profilo e dell'attributo Employee** : quando un record Employee viene aggiornato nell'app Cloud HR (ad esempio, il nome, il titolo o il Manager), il relativo account utente verrà aggiornato automaticamente in ad e Azure ad.
- **Interruzioni dei dipendenti** : quando un dipendente viene terminato nell'app HR cloud, il relativo account utente viene disabilitato automaticamente in ad e Azure ad.
- **Riassunzioni dei dipendenti** : quando un dipendente viene riassunto nell'app HR cloud, il relativo account precedente può essere riattivato automaticamente o sottoposto a nuovo il provisioning in ad e Azure ad.

## <a name="who-is-this-integration-best-suited-for"></a>Per chi è questa integrazione più adatta?

L'integrazione delle app HR cloud con Azure AD provisioning degli utenti è particolarmente adatta per le organizzazioni che:

- Desidero una soluzione predefinita basata sul cloud per il provisioning utenti cloud HR
- richiedere il provisioning utenti diretto dall'app Cloud HR ad Active Directory o Azure AD
- Richiedi il provisioning degli utenti usando i dati ottenuti dall'app Cloud HR
- richiedere l'aggiunta, lo stato di trasferimento e la sincronizzazione degli utenti in una o più foreste di Active Directory, domini e unità organizzative in base alle informazioni sulle modifiche rilevate nell'app HR cloud
- usare Office 365 per la posta elettronica

## <a name="learn"></a>Apprendimento

Il provisioning degli utenti crea una base per la governance delle identità in corso e migliora la qualità dei processi aziendali basati sui dati di identità autorevoli.

### <a name="terms"></a>Termini

Questo articolo usa i termini seguenti:

- **Sistema di origine** : repository di utenti che Azure ad provisioning da, ad esempio un'app HR cloud come la giornata lavorativa e la SuccessFactors.
- **Sistema di destinazione** : il repository degli utenti a cui il Azure ad effettua il provisioning, ad esempio ad, Azure ad, Office365 o altre app Saas.
- **Joiner-Movers-Leavers Process** -un termine usato per nuove assunzioni, trasferimenti e terminazioni usando un'app HR cloud come un sistema di record. Il processo viene completato quando il servizio esegue correttamente il provisioning degli attributi necessari per il sistema di destinazione.

### <a name="key-benefits"></a>Vantaggi principali

Questa funzionalità del provisioning IT basato su HR offre vantaggi aziendali significativi, come indicato di seguito:

- **Aumentare la produttività** : è ora possibile automatizzare l'assegnazione degli account utente, le licenze Office365 e fornire l'accesso ai gruppi di chiavi. Automatizzando le assegnazioni, i nuovi assunti hanno accesso immediato ai propri strumenti di lavoro e aumentano la produttività.
- **Gestire i rischi** : è possibile aumentare la sicurezza automatizzando le modifiche in base allo stato dei dipendenti o alle appartenenze ai gruppi con i dati che fluiscono dall'app Cloud HR. L'automazione delle modifiche garantisce che le identità degli utenti e l'accesso alle app principali vengano aggiornate automaticamente quando gli utenti passano o lasciano l'organizzazione.
- **Conformità e governance degli indirizzi** : Azure ad supporta i log di controllo nativi per le richieste di provisioning degli utenti eseguite dalle app del sistema di origine e di destinazione. Il controllo consente di tenere traccia degli utenti che hanno accesso alle app da un'unica schermata.
- **Gestione** dei costi: il provisioning automatico riduce i costi evitando inefficienze ed errori umani associati al provisioning manuale. Riduce la necessità di soluzioni di provisioning utente personalizzate sviluppate nel tempo usando piattaforme legacy e obsolete.

### <a name="licensing"></a>Licenze

Per configurare l'app Cloud HR per Azure AD l'integrazione del provisioning degli utenti, è necessario disporre di una [licenza Azure ad Premium](https://azure.microsoft.com/pricing/details/active-directory/) valida e di una licenza per l'app HR cloud, ad esempio giornata lavorativa o SuccessFactors.

È anche necessaria una licenza di sottoscrizione valida Azure AD Premium P1 o superiore per ogni utente che verrà originata dall'app Cloud HR ed eseguito il provisioning in AD o Azure AD. Il numero di licenze non corrette possedute nell'app HR cloud può causare errori durante il provisioning dell'utente.

### <a name="prerequisites"></a>Prerequisiti

- Azure AD l'accesso amministratore globale per configurare l'agente di provisioning Azure AD Connect.
- Un'istanza di test e produzione dell'app HR cloud.
- Autorizzazioni di amministratore nell'app Cloud HR per creare un utente di integrazione del sistema e apportare modifiche ai dati dei dipendenti di test a scopo di test.
- Per il provisioning degli utenti in Active Directory, è necessario un server che esegue Windows Server 2012 o versione successiva con .NET 4.7.1 + Runtime per ospitare l' [agente di provisioning di Azure ad Connect](https://go.microsoft.com/fwlink/?linkid=847801).
- [Azure ad Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect) per la sincronizzazione degli utenti tra AD e Azure ad.

### <a name="training-resources"></a>Risorse di formazione

| **Risorse** | **Collegamento e descrizione** |
|:-|:-|
| Video | [Che cos'è il provisioning utenti in Active Directory di Azure?](https://youtu.be/_ZjARPpI6NI) |
| | [Come distribuire il provisioning utenti in Active Directory di Azure?](https://youtu.be/pKzyts6kfrw) |
| Esercitazioni | Vedere l' [elenco delle esercitazioni sull'integrazione di app Saas con Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) |
| | [Esercitazione: configurare la giornata lavorativa per il provisioning utenti automatico](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#frequently-asked-questions-faq) |
| FAQ | [Provisioning utenti automatizzato](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#frequently-asked-questions) |
| | [Provisioning da giorni lavorativi a Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Architettura della soluzione

Nell'esempio seguente viene descritta l'architettura della soluzione di provisioning degli utenti end-to-end per ambienti ibridi comuni e sono inclusi:

- **Flusso di dati HR autorevole: dall'app Cloud HR AD Active Directory** : in questo flusso, l'evento HR (Joiner-Movers-Leavers-Process) viene avviato nel tenant dell'app HR cloud. Il servizio di provisioning Azure AD e l'agente di provisioning di Azure AD Connect effettua il provisioning dei dati utente dal tenant app Cloud HR in Active Directory. A seconda dell'evento, può determinare operazioni di creazione/aggiornamento/abilitazione o disabilitazione in Active Directory.
- **Sincronizzare con Azure ad e la posta elettronica writeback e il nome utente da ad locale a cloud HR app** : dopo l'aggiornamento degli account in Active Directory, viene sincronizzato con Azure AD tramite Azure ad Connect e gli indirizzi di posta elettronica e gli attributi del nome utente possono essere riscritti nel tenant del cloud HR app.

![Diagramma del flusso di lavoro](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Descrizione del flusso di lavoro

I passaggi principali indicati nel diagramma sono:  

1. Il **team HR** esegue le transazioni nel tenant del cloud HR app.
2. **Azure ad servizio di provisioning** esegue i cicli pianificati dal tenant dell'app HR cloud e identifica le modifiche che devono essere elaborate per la sincronizzazione con Active Directory.
3. **Azure ad servizio di provisioning** richiama l'agente di provisioning di Azure ad Connect con un payload di richiesta contenente le operazioni di creazione/aggiornamento/abilitazione/disabilitazione dell'account Active Directory.
4. **Azure ad Connect agente di provisioning** usa un account del servizio per gestire i dati dell'account Active Directory.
5. **Azure ad Connect** esegue la [sincronizzazione](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) Delta per eseguire il pull degli aggiornamenti in Active Directory.
6. Gli aggiornamenti di **Active Directory** vengono sincronizzati con Azure ad.
7. **Azure ad il servizio di provisioning** writeback l'attributo di posta elettronica e il nome utente da Azure ad al tenant dell'app HR cloud.

## <a name="plan-the-deployment-project"></a>Pianificare il progetto di distribuzione

Prendere in considerazione le esigenze organizzative quando si determina la strategia per la distribuzione nell'ambiente in uso.

### <a name="engage-the-right-stakeholders"></a>Coinvolgere gli stakeholder appropriati

Quando i progetti tecnologici hanno esito negativo, in genere lo fanno a causa di una mancata corrispondenza delle aspettative in merito a conseguenze, risultati e responsabilità. Per evitare questi problemi, [assicurarsi di coinvolgere gli stakeholder appropriati](https://aka.ms/deploymentplans) e che i ruoli della parte interessata nel progetto siano ben comprensibili documentando gli stakeholder e i rispettivi input e responsabilità del progetto.

È necessario includere un rappresentante dell'organizzazione HR che può fornire input sui processi aziendali HR esistenti e sull'identità del ruolo di lavoro e sui requisiti di elaborazione dei dati dei processi.

### <a name="plan-communications"></a>Pianificare le comunicazioni

La comunicazione è fondamentale per il successo di un nuovo servizio. È necessario comunicare in modo proattivo con gli utenti in che modo cambiano l'esperienza, quando verrà modificata e come ottenere supporto se si verificano problemi.

### <a name="plan-a-pilot"></a>Pianificare un progetto pilota

L'integrazione di processi aziendali HR e flussi di lavoro di identità dall'app Cloud HR ai sistemi di destinazione richiede una notevole quantità di convalida dei dati, trasformazione dei dati, pulizia dei dati e test end-to-end prima di distribuire la soluzione nell'ambiente di produzione.

È consigliabile eseguire la configurazione iniziale in un [ambiente pilota](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans#best-practices-for-a-pilot) prima di ridimensionarla a tutti gli utenti nell'ambiente di produzione.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Selezionare Cloud HR provisioning Connector Apps

Per semplificare Azure AD il provisioning dei flussi di lavoro tra l'app Cloud HR e AD, sono disponibili più app del connettore di provisioning che è possibile aggiungere dalla raccolta di app Azure AD:

- **Cloud HR app per il provisioning utenti Active Directory** : questa app del connettore di provisioning facilita il provisioning degli account utente dall'app Cloud HR a un singolo dominio ad. Se sono presenti più domini, è possibile aggiungere un'istanza di questa app dalla raccolta di app Azure AD per ogni dominio AD di cui è necessario eseguire il provisioning.
- **App HR cloud per il provisioning utenti Azure ad** -mentre Azure ad Connect è lo strumento da usare per sincronizzare gli utenti di Active directory con Azure ad, questa app del connettore di provisioning può essere usata per facilitare il provisioning degli utenti solo cloud dall'app HR cloud a un singolo tenant Azure ad.
- **Cloud HR app writeback** : questa app del connettore di provisioning facilita il writeback degli indirizzi di posta elettronica dell'utente da Azure ad all'app Cloud HR.

Ad esempio, l'immagine seguente elenca le app del connettore per la giornata lavorativa disponibili nella raccolta di app Azure AD.

![Raccolta di app del portale di Azure Active Directory](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flowchart"></a>Diagramma di flusso decisionale

Usare il diagramma di flusso decisionale seguente per identificare le app di provisioning HR cloud rilevanti per il proprio scenario.

![Diagramma di flusso decisionale](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img3.png)

## <a name="design-azure-ad-connect-provisioning-agent-deployment-topology"></a>Progettazione Azure AD Connect topologia di distribuzione dell'agente di provisioning

L'integrazione del provisioning tra l'app HR cloud e AD richiede questi quattro componenti:

- Tenant app Cloud HR
- App del connettore di provisioning
- Agente di provisioning di Azure AD Connect
- Dominio AD

La topologia di distribuzione dell'agente di provisioning di Azure AD Connect dipende dal numero di tenant di app HR cloud e di domini figlio di Active Directory che si intende integrare. Se si dispone di più domini di Active Directory, dipende dalla presenza o meno di domini AD contigui o [non contigui](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace).

In base alla decisione scelta, scegliere uno degli scenari di distribuzione:

- Tenant di app HR Single cloud-> di destinazione di uno o più domini figlio AD in una foresta trusted
- Tenant di app HR Single cloud-> destinazione di più domini figlio in una foresta di Active Directory non contigua

### <a name="single-cloud-hr-app-tenant---single-or-multiple-target-ad-child-domains-in-a-trusted-forest"></a>Tenant app HR cloud singolo-> domini figlio AD di destinazione singoli o multipli in una foresta trusted

Si consiglia la configurazione di produzione seguente:

|Requisito|Recommendation|
|:-|:-|
|Numero di agenti di provisioning Azure AD Connect da distribuire|2 (per la disponibilità elevata e il failover)
|Numero di app del connettore di provisioning da configurare|Un'app per dominio figlio|
|Host server per Azure AD Connect agente di provisioning|Windows 2012 R2 + con la linea di visibilità per i controller di dominio AD con Geo-located</br>Può coesistere con Azure AD Connect servizio|

![Flusso in agenti locali](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-ad-forest"></a>Tenant di app HR Single cloud-> destinazione di più domini figlio in una foresta di Active Directory non contigua

Questo scenario implica il provisioning degli utenti dall'app Cloud HR ai domini in foreste di Active Directory non contigue.

Si consiglia la configurazione di produzione seguente:

|Requisito|Recommendation|
|:-|:-|
|Numero di agenti di provisioning di Azure AD Connect da distribuire in locale|2 per ogni foresta di Active Directory non contigua|
|Numero di app del connettore di provisioning da configurare|Un'app per dominio figlio|
|Host server per Azure AD Connect agente di provisioning|Windows 2012 R2 + con la linea di visibilità per i controller di dominio AD con Geo-located</br>Può coesistere con Azure AD Connect servizio|

![Foresta di Active Directory del tenant app HR singolo Cloud](./media/plan-cloudhr-provisioning/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Requisiti dell'agente di provisioning Azure AD Connect

La soluzione cloud HR app per il provisioning utenti Active Directory richiede la distribuzione di uno o più Azure AD Connect agenti di provisioning nei server che eseguono Windows 2012 R2 o versione successiva con almeno 4 GB di RAM e .NET 4.7.1 + Runtime. Verificare che il server host disponga dell'accesso di rete al dominio di Active Directory di destinazione.

Per preparare l'ambiente locale, la configurazione guidata dell'agente di provisioning di Azure AD Connect registra l'agente con il tenant di Azure AD, [apre le porte](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#open-ports), [consente l'accesso agli URL](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#allow-access-to-urls)e supporta la [configurazione del proxy HTTPS in uscita](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).

L'agente di provisioning usa un account del servizio per comunicare con i domini AD. Prima di installare l'agente, è consigliabile creare un account del servizio in utenti e computer di Active Directory che soddisfino i requisiti seguenti:

- Password senza scadenza
- Autorizzazioni di controllo delegate per la lettura, la creazione, l'eliminazione e la gestione degli account utente

È possibile selezionare i controller di dominio che devono gestire le richieste di provisioning. Se si dispone di diversi controller di dominio distribuiti geograficamente, installare l'agente di provisioning nello stesso sito dei controller di dominio preferiti per migliorare l'affidabilità e le prestazioni della soluzione end-to-end.

Per la disponibilità elevata, è possibile distribuire più di un agente di provisioning di Azure AD Connect e registrarlo per gestire lo stesso set di domini AD locali.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Pianificare i filtri di ambito e il mapping degli attributi

Quando si Abilita il provisioning dall'app Cloud HR ad AD o Azure AD, il portale di Azure controlla i valori degli attributi tramite il mapping degli attributi.

### <a name="define-scoping-filters"></a>Definire i filtri di ambito

Usare i [filtri di ambito](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters) per definire le regole basate su attributi che determinano quali utenti devono essere sottoposti a provisioning dall'app Cloud HR ad ad o Azure ad.

Quando si avvia il processo di join, raccogliere i requisiti seguenti:

- L'app Cloud HR è usata per caricare i dipendenti e i lavoratori condizionali?
- Si prevede di usare l'app Cloud HR per Azure AD il provisioning degli utenti per gestire i dipendenti e i lavoratori condizionali?
- Si prevede di implementare l'app Cloud HR per Azure AD il provisioning degli utenti solo per un subset di utenti di cloud HR app (ad esempio, solo per i dipendenti)?

A seconda dei requisiti, quando si configurano i mapping degli attributi, è possibile impostare il campo **ambito dell'oggetto di origine** in modo da selezionare i set di utenti nell'app Cloud HR nell'ambito per il provisioning in Active Directory. Vedere l'esercitazione cloud HR app per i filtri di ambito comunemente usati.

### <a name="determine-matching-attributes"></a>Determinare gli attributi corrispondenti

Con il provisioning, è possibile trovare la corrispondenza con gli account esistenti tra il sistema di origine e quello di destinazione. Quando si integra l'app Cloud HR con Azure AD servizio di provisioning, è possibile [configurare il mapping degli attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal#mappings) per determinare quali dati utente devono fluire dall'app Cloud HR ad ad o Azure ad.

Quando si avvia il processo di join, raccogliere i requisiti seguenti:

- Qual è l'ID univoco in questa app Cloud HR usato per identificare ogni utente?
- Dal punto di vista del ciclo di vita delle identità, come si gestiscono i riassunti? I riassunti mantengono il vecchio ID dipendente?
- Si elaborano in anticipo assunzioni con data successiva e account di Active Directory creati?
- Dal punto di vista del ciclo di vita delle identità, come si gestisce la conversione dei dipendenti da un lavoratore condizionale o in caso contrario?
- Gli utenti convertiti mantengono il vecchio account di Active Directory o ne ottengono di nuovi?

A seconda dei requisiti, Azure AD supporta il mapping diretto da attributo a attributo, fornendo valori costanti o [scrivendo espressioni per i mapping degli attributi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-writing-expressions-for-attribute-mappings). Questa flessibilità consente di controllare in modo definitivo gli elementi che verranno popolati nell'attributo dell'app di destinazione. È possibile usare [Microsoft Graph API](https://docs.microsoft.com/azure/active-directory/manage-apps/export-import-provisioning-configuration) e Graph Explorer per esportare i mapping degli attributi e lo schema del provisioning degli utenti in un file JSON e importarli di nuovo in Azure ad.

**Per impostazione predefinita**, l'attributo nell'app Cloud HR che rappresenta l'ID dipendente univoco viene usato come attributo corrispondente *mappato all'attributo univoco in ad.* Ad esempio, nello scenario di app per la giornata lavorativa viene eseguito il mapping dell'attributo *WorkerID* di *giornata lavorativa* all'attributo ad *EmployeeID* .

È possibile impostare più attributi corrispondenti e assegnare la precedenza corrispondente. Vengono valutati in base alla precedenza corrispondente. Quando viene rilevata una corrispondenza la valutazione degli attributi corrispondenti termina.

È anche possibile [personalizzare i mapping degli attributi predefiniti](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes#understanding-attribute-mapping-types) , ad esempio modificare o eliminare i mapping degli attributi esistenti o creare nuovi mapping degli attributi in base alle esigenze aziendali. Per un elenco di attributi personalizzati di cui eseguire il mapping, vedere l'esercitazione sull'app HR cloud, ad esempio la [giornata lavorativa](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-workday-to-active-directory-user-attribute-mapping-and-transformations).

### <a name="determine-user-account-status"></a>Determinare lo stato dell'account utente

Per impostazione predefinita, l'app del connettore di provisioning esegue il mapping dello **stato del profilo utente HR** allo **stato dell'account utente** in ad/Azure ad per determinare se abilitare o disabilitare l'account utente.

Quando si avvia il processo Joiner/Leavers, raccogliere i requisiti seguenti:

| Processo | Requisiti |
| - | - |
| **Falegnami** | Dal punto di vista del ciclo di vita delle identità, come si gestiscono i riassunti? I riassunti mantengono il vecchio ID dipendente? |
| | Si elaborano assunzioni con data successiva e si creano gli account di Active Directory in anticipo? Questi account vengono creati in stato abilitato/disabilitato? |
| | Dal punto di vista del ciclo di vita delle identità, come si gestisce la conversione dei dipendenti da un lavoratore condizionale o in caso contrario? |
| | Gli utenti convertiti mantengono il vecchio account di Active Directory o ne ottengono di nuovi? |
| **Che** | Le terminazioni sono gestite in modo diverso per i dipendenti e i lavoratori contingenti in Active Directory? |
| | Quali date effettive vengono considerate per l'elaborazione della terminazione utente? |
| | In che modo le conversioni dei dipendenti e dei dipendenti condizionali influiscano sugli account Active Directory esistenti? |
| | Come si elabora l'operazione di annullamento in Active Directory? Le operazioni di annullamento devono essere gestite se i assunzioni con data successiva vengono creati in Active Directory come parte del processo di join. |

A seconda dei requisiti, è possibile personalizzare la logica di mapping usando le [espressioni Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data) in modo che l'account Active Directory venga abilitato o disabilitato in base a una combinazione di punti dati.

### <a name="map-cloud-hr-app-to-ad-user-attributes"></a>Mappare le app HR cloud agli attributi utente di Active Directory

Ogni app HR cloud viene fornita con l'app Cloud HR predefinita per i mapping AD.

Quando si avvia il processo di join o di spostamento/uscita, raccogliere i requisiti seguenti:

| Processo | Requisiti |
| - | - |
| **Falegnami** | Il processo di creazione dell'account Active Directory è manuale, automatizzato o parzialmente automatizzato? |
| | Si prevede di propagare gli attributi personalizzati dall'app Cloud HR ad Active Directory? |
| **Motori** | Quali attributi si desidera elaborare ogni volta che viene eseguita un'operazione "Movers" nell'app HR cloud? |
| | Vengono eseguite convalide di attributi specifici al momento dell'aggiornamento dell'utente? In caso affermativo, immettere i dettagli. |
| **Che** | Le terminazioni sono gestite in modo diverso per i dipendenti e i lavoratori contingenti in Active Directory? |
| | Quali date effettive vengono considerate per l'elaborazione della terminazione utente? |
| | In che modo le conversioni dei dipendenti e dei dipendenti condizionali influiscano sugli account Active Directory esistenti? |

A seconda dei requisiti, è possibile modificare i mapping per soddisfare gli obiettivi di integrazione. Per un elenco di attributi personalizzati di cui eseguire il mapping, vedere l'esercitazione sull'app HR cloud specifica, ad esempio la [giornata lavorativa](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-workday-to-active-directory-user-attribute-mapping-and-transformations).

### <a name="generate-unique-attribute-value"></a>Genera valore di attributo univoco

Quando si avvia il processo di join, potrebbe essere necessario generare valori di attributo univoci quando si impostano attributi come CN, samAccountName e UPN con vincoli UNIQUE.

La funzione Azure AD [SelectUniqueValues](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#selectuniquevalue) valuta ogni regola e quindi controlla il valore generato per l'univocità nel sistema di destinazione. Vedere esempio [generare un valore univoco per l'attributo userPrincipalName (UPN)](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#generate-unique-value-for-userprincipalname-upn-attribute).

> [!NOTE]
> Questa funzione è attualmente supportata solo per il provisioning degli utenti in Active Directory "giorno lavorativo". Non può essere usato con altre app di provisioning.

### <a name="configure-ad-ou-container-assignment"></a>Configurare l'assegnazione del contenitore OU AD

È un requisito comune inserire gli account utente di Active Directory in contenitori basati su Business Unit, posizioni e reparti. Quando si avvia un processo di spostamento e in caso di modifica di un'organizzazione di supervisione, potrebbe essere necessario spostare l'utente da un'unità organizzativa a un'altra in Active Directory.

Utilizzare la funzione [Switch ()](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data#switch) per configurare la logica di business per l'assegnazione dell'unità organizzativa ed eseguirne il mapping all'attributo ad *parentDistinguishedName*.

Se, ad esempio, si desidera creare utenti in un'unità organizzativa basata sull'attributo HR "Municipio", è possibile utilizzare l'espressione seguente.

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Con questa espressione, se il valore del comune è Dallas, Austin, Seattle o Londra, l'account utente verrà creato nell'unità organizzativa corrispondente. Se non esiste alcuna corrispondenza, l'account verrà creato nell'unità organizzativa predefinita.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Pianificare il recapito delle password di nuovi account utente

Quando si avvia il processo di join, sarà necessario impostare e recapitare una password temporanea di nuovi account utente. Con il servizio HR cloud per Azure AD il provisioning degli utenti, è possibile implementare Azure AD funzionalità di [reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) (SSPR) per l'utente il giorno 1.

SSPR è un modo semplice per gli amministratori IT di consentire agli utenti di reimpostare le password o sbloccare i propri account. È possibile eseguire il provisioning dell'attributo del **numero di cellulare** dall'app Cloud HR AD Active Directory e sincronizzarlo con Azure ad. Quando l'attributo del **numero di cellulare** si trova in Azure ad, è possibile abilitare SSPR per l'account dell'utente in modo che il giorno 1 i nuovi utenti possano usare il numero di cellulare registrato e verificato per l'autenticazione.

## <a name="plan-for-initial-cycle"></a>Pianificare il ciclo iniziale

Quando il servizio di provisioning Azure AD viene eseguito per la prima volta, viene eseguito un [ciclo iniziale](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#what-happens-during-provisioning) sull'app HR cloud per creare uno snapshot di tutti gli oggetti utente nell'app Cloud HR. Il tempo impiegato per i cicli iniziali dipende direttamente dal numero di utenti presenti nel sistema di origine. Il ciclo iniziale per alcuni tenant app Cloud HR con oltre 100.000 utenti può richiedere molto tempo.

**Per i tenant cloud HR app di grandi dimensioni (> 30.000 utenti), è consigliabile** eseguire il ciclo iniziale in fasi progressive e avviare gli aggiornamenti incrementali solo dopo aver verificato che gli attributi corretti siano impostati in Active Directory per diversi scenari di provisioning degli utenti. Seguire l'ordine seguente:

1. Eseguire il ciclo iniziale solo per un set limitato di utenti impostando il [filtro di ambito](#plan-scoping-filters-and-attribute-mapping).
2. Verificare i valori degli attributi e del provisioning dell'account Active Directory impostati per gli utenti selezionati per la prima esecuzione. Se il risultato soddisfa le aspettative, espandere il filtro di ambito per includere progressivamente più utenti e verificare i risultati per la seconda esecuzione.

Quando si è soddisfatti dei risultati del ciclo iniziale per gli utenti di test, è possibile avviare gli [aggiornamenti incrementali](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#incremental-cycles).

## <a name="plan-testing-and-security"></a>Pianificare test e sicurezza

In ogni fase della distribuzione dal progetto pilota iniziale tramite l'abilitazione del provisioning degli utenti, assicurarsi di verificare che i risultati siano quelli previsti e di controllare i cicli di provisioning.

### <a name="plan-testing"></a>Test del piano

Dopo aver configurato l'app Cloud HR per Azure AD il provisioning degli utenti, è necessario eseguire i test case per verificare se la soluzione soddisfa i requisiti dell'organizzazione.

|Scenari|Risultati previsti|
|:-|:-|
|Nuova assunzione di dipendenti nell'app Cloud HR| -Viene eseguito il provisioning dell'account utente in AD.</br>-Gli utenti possono accedere alle app di dominio AD ed eseguire le azioni desiderate.</br>-Se la sincronizzazione di AAD Connect è configurata, l'account utente verrà creato anche in Azure AD.
|L'utente è terminato nell'app Cloud HR|-L'account utente è disabilitato in Active Directory.</br>-L'utente non può accedere ad alcuna app aziendale protetta da Active Directory.
|L'organizzazione di supervisione utente è stata aggiornata nell'app Cloud HR|In base al mapping degli attributi, l'account utente viene spostato da un'unità organizzativa a un'altra in Active Directory.|
|Il manager dell'utente di aggiornamenti HR nell'app Cloud HR|Il campo Manager in AD viene aggiornato in modo da riflettere il nome del nuovo responsabile.|
|HR riassume un dipendente in un nuovo ruolo.|Il comportamento dipende dal modo in cui l'app HR cloud è configurata per generare ID dipendente:</br>-Se l'ID del dipendente precedente viene riusato per i riassunti, il connettore Abilita l'account di Active Directory esistente per l'utente.</br>-Se i riassunti ottengono un nuovo ID dipendente, il connettore creerà un nuovo account di Active Directory per l'utente.|
|HR converte il dipendente in contratto di lavoro o viceversa|Viene creato un nuovo account di Active Directory per il nuovo utente e l'account precedente viene disabilitato alla data di conversione effettiva.|

Usare i risultati precedenti per determinare come eseguire la transizione dell'implementazione del provisioning utenti automatico in produzione in base alle tempistiche stabilite.

> [!TIP]
> È consigliabile usare tecniche come la riduzione dei dati e la pulitura dei dati quando si aggiorna l'ambiente di test con i dati di produzione per rimuovere/mascherare i dati sensibili (informazioni personali) per rispettare gli standard di privacy e sicurezza.

### <a name="plan-security"></a>Pianificare la sicurezza

Per la distribuzione di un nuovo servizio è normale che sia necessaria una revisione della sicurezza. Se è richiesta una verifica della sicurezza o non è ancora stata eseguita, esaminare le numerose Azure AD [white paper](https://www.microsoft.com/download/details.aspx?id=36391) che forniscono una panoramica dell'identità come servizio.

### <a name="plan-rollback"></a>Rollback del piano

Se l'implementazione del provisioning utenti cloud HR non funziona nel modo desiderato nell'ambiente di produzione, i passaggi di rollback seguenti possono risultare utili per ripristinare uno stato valido noto precedente:

1. Esaminare il [report di riepilogo del provisioning](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting#getting-provisioning-reports-from-the-azure-management-portal) e i [log di provisioning](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting#provisioning-audit-logs) (vedere gestire il provisioning utenti [dell'app HR cloud](#manage-your-configuration)) per determinare le operazioni non corrette eseguite sugli utenti e/o sui gruppi interessati.
2. L'ultimo stato valido noto degli utenti e/o dei gruppi interessati può essere determinato tramite i log di controllo del provisioning o rivedendo i sistemi di destinazione (Azure AD o AD).
3. Collaborare con il proprietario dell'app per aggiornare gli utenti e/o i gruppi interessati direttamente nell'app usando gli ultimi valori di stato validi noti.

## <a name="deploy-the-cloud-hr-app"></a>Distribuire l'app HR cloud

Scegliere l'app Cloud HR allineata ai requisiti della soluzione.

**Giornata lavorativa** : fare riferimento all' [esercitazione: configurare la giornata lavorativa per il provisioning utenti automatico](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#planning-your-deployment) per i passaggi da eseguire per importare i profili di lavoro da giorni lavorativi in Active Directory e Azure ad, con Write-back facoltativo dell'indirizzo di posta elettronica e nome utente per la giornata lavorativa.

## <a name="manage-your-configuration"></a>Gestire la configurazione

Azure AD possibile fornire informazioni aggiuntive sull'utilizzo del provisioning degli utenti dell'organizzazione e sull'integrità operativa tramite i log di controllo e i report.

### <a name="gain-insights-from-reports-and-logs"></a>Ottenere informazioni dettagliate da report e log

Dopo un [ciclo iniziale](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#what-happens-during-provisioning)riuscito, il servizio di provisioning di Azure ad continuerà a eseguire aggiornamenti incrementali back-to-back a tempo indeterminato, a intervalli definiti nelle esercitazioni specifiche per ogni app, fino a quando non si verifica uno degli eventi seguenti:

- Il servizio viene arrestato manualmente e viene attivato un nuovo ciclo iniziale usando il [portale di Azure](https://portal.azure.com/) o usando il comando [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) appropriato.
- Viene attivato un nuovo ciclo iniziale a causa di una modifica nei mapping degli attributi o nei filtri di ambito.
- Il processo di provisioning entra in quarantena a causa di una percentuale elevata di errori e rimane in quarantena per più di quattro settimane in cui verrà disabilitato automaticamente.

Per esaminare questi eventi e tutte le altre attività eseguite dal servizio di provisioning, [informazioni su come esaminare i log e ottenere report sulle attività di provisioning](https://docs.microsoft.com/azure/active-directory/manage-apps/check-status-user-account-provisioning).

#### <a name="azure-monitor-logs"></a>Log di Monitoraggio di Azure

Tutte le attività eseguite dal servizio di provisioning vengono registrate nei **log di controllo Azure ad**. È possibile instradare i log di controllo Azure AD ai log di monitoraggio di Azure per un'ulteriore analisi. **Log di monitoraggio di Azure (noto anche come area di lavoro log Analytics)** consente di eseguire query sui dati per individuare gli eventi, analizzare le tendenze ed eseguire la correlazione tra varie origini dati. Guarda questo [video](https://youtu.be/MP5IaCTwkQg) per scoprire i vantaggi derivanti dall'uso dei log di monitoraggio di Azure per i log Azure ad in scenari utente pratici.

È possibile installare le [visualizzazioni di log Analytics per Azure ad log attività](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views) per ottenere l'accesso ai [report predefiniti](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) relativi agli eventi di provisioning nell'ambiente.

Per altre informazioni, vedere [come analizzare i log attività di Azure ad con i log di monitoraggio di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

### <a name="manage-personal-data"></a>Gestire dati personali

L'agente di provisioning di Azure AD Connect installato in Windows Server crea log nel registro eventi di Windows che possono contenere dati personali a seconda dell'app Cloud HR per i mapping degli attributi AD. Per rispettare gli obblighi sulla privacy degli utenti, è possibile configurare un'attività pianificata di Windows per cancellare il registro eventi e assicurarsi che i dati non vengano conservati oltre 48 ore.

Azure AD servizio di provisioning non genera report, esegue analisi o fornisce informazioni dettagliate oltre 30 giorni. Di conseguenza, il servizio di provisioning di Azure AD non archivia, elabora o conserva i dati oltre i 30 giorni. 

### <a name="troubleshoot"></a>Risolvere problemi

Per risolvere eventuali problemi che possono verificarsi durante il provisioning, vedere i collegamenti seguenti:

- [Problemi di configurazione del provisioning utenti in un'applicazione raccolta Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)
- [Sincronizzare un attributo dal Active Directory locale a Azure AD per il provisioning in un'applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning-sync-attributes-for-mapping)
- [Il provisioning degli utenti in un'applicazione della raccolta Azure AD richiede ore o più](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish)
- [Problem saving administrator credentials while configuring user provisioning to an Azure Active Directory Gallery application](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-storage-limit) (Problemi di salvataggio delle credenziali dell'amministratore durante la configurazione del provisioning utenti in un'applicazione della raccolta di Azure Active Directory)
- [Nessun utente di cui è in corso il provisioning in un'applicazione raccolta Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned)
- [È in corso il provisioning di un set errato di utenti in un'applicazione raccolta Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-wrong-users-provisioned)
- [Configurazione di Visualizzatore eventi di Windows per la risoluzione dei problemi dell'agente](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Configurazione dei log di controllo del portale di Azure per la risoluzione dei problemi di servizio](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Riconoscimento di log per operazioni di creazione per l'account utente di AD](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#understanding-logs-for-ad-user-account-create-operations)
- [Riconoscimento di log per operazioni di aggiornamento manager](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#understanding-logs-for-manager-update-operations)
- [Risoluzione degli errori più comuni](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Passaggi successivi

- [Scrittura di espressioni per i mapping degli attributi](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)
- [Azure AD synchronization API overview](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) (Panoramica dell'API di sincronizzazione di Azure AD)
- [Ignora l'eliminazione di account utente che non rientrano nell'ambito](https://docs.microsoft.com/azure/active-directory/manage-apps/skip-out-of-scope-deletions)
- [Agente di provisioning di Azure AD Connect: cronologia delle versioni](https://docs.microsoft.com/azure/active-directory/manage-apps/provisioning-agent-release-version-history)
