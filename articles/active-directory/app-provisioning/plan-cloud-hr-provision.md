---
title: Pianificare l'applicazione cloud HR per Azure Active Directory il provisioning degli utenti
description: Questo articolo descrive il processo di distribuzione dell'integrazione dei sistemi HR cloud, ad esempio giorni lavorativi e SuccessFactors, con Azure Active Directory. L'integrazione di Azure AD con il sistema HR cloud genera un sistema di gestione del ciclo di vita delle identità completo.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: b96b679e967fd898f072b4b1ae195e3dd1061c04
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235695"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Pianificare l'applicazione cloud HR per Azure Active Directory il provisioning degli utenti

In passato, il personale IT si fidava di metodi manuali per creare, aggiornare ed eliminare i dipendenti. Hanno usato metodi come il caricamento di file CSV o script personalizzati per sincronizzare i dati dei dipendenti. Questi processi di provisioning sono soggetti a errori, non sicuri e difficili da gestire.

Per gestire i cicli di vita delle identità di dipendenti, fornitori o lavoratori dipendenti, il [servizio di provisioning utenti Azure Active Directory (Azure ad)](../app-provisioning/user-provisioning.md) offre l'integrazione con applicazioni HR (Human Resources) basate sul cloud. Esempi di applicazioni includono giorni lavorativi o SuccessFactors.

Azure AD usa questa integrazione per abilitare i flussi di lavoro delle applicazioni HR cloud (app) seguenti:

- **Effettuare il provisioning degli utenti per Active Directory:** Effettuare il provisioning di set di utenti selezionati da un'app Cloud HR in uno o più domini Active Directory.
- **Effettuare il provisioning degli utenti solo cloud per Azure ad:** Negli scenari in cui non viene usato Active Directory, effettuare il provisioning degli utenti direttamente dall'app Cloud HR al Azure AD.
- **Scrivere nuovamente nell'app Cloud HR:** Scrivere gli indirizzi di posta elettronica e gli attributi del nome utente dal Azure AD di nuovo all'app HR cloud.

> [!NOTE]
> Questo piano di distribuzione illustra come distribuire i flussi di lavoro delle app HR cloud con Azure AD provisioning degli utenti. Per informazioni su come distribuire il provisioning utenti automatico in app SaaS (Software as a Service), vedere [pianificare una distribuzione automatica del provisioning utenti](https://aka.ms/deploymentplans/provisioning).

## <a name="enabled-hr-scenarios"></a>Scenari HR abilitati

Il servizio di provisioning utenti Azure AD consente l'automazione dei seguenti scenari di gestione del ciclo di vita delle identità basati sulle risorse umane:

- **Assunzione di nuovi dipendenti:** Quando un nuovo dipendente viene aggiunto all'app Cloud HR, viene creato automaticamente un account utente in Active Directory e Azure AD con l'opzione per riscrivere l'indirizzo di posta elettronica e gli attributi del nome utente nell'app HR cloud.
- **Aggiornamenti del profilo e dell'attributo del dipendente:** Quando un record dipendente, ad esempio nome, titolo o Manager, viene aggiornato nell'app HR cloud, il relativo account utente viene aggiornato automaticamente in Active Directory e Azure AD.
- **Interruzioni dei dipendenti:** Quando un dipendente viene terminato nell'app HR cloud, il relativo account utente viene disabilitato automaticamente in Active Directory e Azure AD.
- **Riassunzioni dei dipendenti:** Quando un dipendente viene riassunto nell'app Cloud HR, il relativo account precedente può essere riattivato automaticamente o sottoposto a provisioning per Active Directory e Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Per chi è questa integrazione più adatta?

L'integrazione delle app HR cloud con Azure AD provisioning degli utenti è particolarmente adatta per le organizzazioni che:

- Si vuole una soluzione predefinita basata sul cloud per il provisioning utenti cloud HR.
- Richiedere il provisioning utenti diretto dall'app Cloud HR per Active Directory o Azure AD.
- Richiedere il provisioning degli utenti usando i dati ottenuti dall'app Cloud HR.
- Richiedere l'Unione, lo stato di trasferimento e l'uscita degli utenti per la sincronizzazione in uno o più Active Directory foreste, domini e unità organizzative basate solo sulle informazioni sulle modifiche rilevate nell'app Cloud HR.
- Usare Office 365 per la posta elettronica.

## <a name="learn"></a>Informazioni

Il provisioning degli utenti crea una base per la governance delle identità in corso. Migliora la qualità dei processi di business che si basano sui dati di identità autorevoli.

### <a name="terms"></a>Termini

Questo articolo usa i termini seguenti:

- **Sistema di origine**: repository di utenti che Azure ad provisioning da. Un esempio è un'app HR cloud come la giornata lavorativa o SuccessFactors.
- **Sistema di destinazione**: il repository di utenti a cui il Azure ad effettua il provisioning. Gli esempi sono Active Directory, Azure AD, Office 365 o altre app SaaS.
- **Joiner-Movers-processo di uscita**: un termine usato per nuove assunzioni, trasferimenti e terminazioni usando un'app HR cloud come un sistema di record. Il processo viene completato quando il servizio esegue correttamente il provisioning degli attributi necessari per il sistema di destinazione.

### <a name="key-benefits"></a>Vantaggi principali

Questa funzionalità del provisioning IT basato su HR offre i vantaggi aziendali seguenti:

- **Aumentare la produttività:** È ora possibile automatizzare l'assegnazione degli account utente e delle licenze di Office 365 e fornire l'accesso ai gruppi di chiavi. Automatizzando le assegnazioni, i nuovi assunti hanno accesso immediato ai propri strumenti di lavoro e aumentano la produttività.
- **Gestisci i rischi:** Per aumentare la sicurezza, è possibile automatizzare le modifiche in base allo stato dei dipendenti o alle appartenenze ai gruppi con dati provenienti dall'app Cloud HR. L'automazione delle modifiche garantisce che le identità degli utenti e l'accesso alle app principali vengano aggiornate automaticamente quando gli utenti passano o lasciano l'organizzazione.
- **Conformità e governance degli indirizzi:** Azure AD supporta i log di controllo nativi per le richieste di provisioning degli utenti eseguite dalle app del sistema di origine e di destinazione. Con il controllo è possibile tenere traccia degli utenti che hanno accesso alle app da un'unica schermata.
- **Gestione dei costi:** Il provisioning automatico riduce i costi evitando inefficienze ed errori umani associati al provisioning manuale. Riduce la necessità di soluzioni di provisioning utente personalizzate sviluppate nel tempo usando piattaforme legacy e obsolete.

### <a name="licensing"></a>Licenza

Per configurare l'app HR cloud per Azure AD l'integrazione del provisioning degli utenti, è necessario disporre di una [licenza Azure ad Premium](https://azure.microsoft.com/pricing/details/active-directory/) valida e di una licenza per l'app HR cloud, ad esempio giornata lavorativa o SuccessFactors.

È anche necessaria una licenza di sottoscrizione valida Azure AD Premium P1 o superiore per ogni utente che verrà originata dall'app Cloud HR ed eseguito il provisioning in Active Directory o Azure AD. Il numero di licenze non corrette possedute nell'app HR cloud potrebbe causare errori durante il provisioning dell'utente.

### <a name="prerequisites"></a>Prerequisiti

- Azure AD [amministratore di identità ibrido](../users-groups-roles/directory-assign-admin-roles.md#hybrid-identity-administrator)  per configurare l'agente di provisioning Azure ad Connect.
- Azure AD ruolo di [amministratore dell'applicazione](../users-groups-roles/directory-assign-admin-roles.md#application-administrator) per configurare l'app di provisioning nel portale di Azure
- Un'istanza di test e produzione dell'app HR cloud.
- Autorizzazioni di amministratore nell'app Cloud HR per creare un utente di integrazione del sistema e apportare modifiche ai dati dei dipendenti di test a scopo di test.
- Per il provisioning degli utenti in Active Directory, è necessario un server che esegue Windows Server 2012 o versione successiva con .NET 4.7.1 + Runtime per ospitare l'agente di provisioning di Azure AD Connect
- [Azure ad Connect](../hybrid/whatis-azure-ad-connect.md) per la sincronizzazione degli utenti tra Active Directory e Azure ad.

### <a name="training-resources"></a>Risorse di formazione

| **Risorse** | **Collegamento e descrizione** |
|:-|:-|
| Video | [Che cos'è il provisioning utenti in Active Directory di Azure?](https://youtu.be/_ZjARPpI6NI) |
| | [Come distribuire il provisioning utenti in Active Directory di Azure](https://youtu.be/pKzyts6kfrw) |
| Esercitazioni | [Elenco delle esercitazioni sull'integrazione di app SaaS con Azure AD](../saas-apps/tutorial-list.md) |
| | [Esercitazione: Configurare Workday per il provisioning utenti automatico](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| Domande frequenti | [Provisioning utenti automatizzato](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Provisioning da giorni lavorativi a Azure AD](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Architettura della soluzione

Nell'esempio seguente viene descritta l'architettura della soluzione di provisioning degli utenti end-to-end per ambienti ibridi comuni e sono inclusi:

- **Flusso di dati HR autorevole dall'app Cloud HR al Active Directory.** In questo flusso, l'evento HR (Joiner-Movers-Leavers-processo) viene avviato nel tenant cloud HR app. Il servizio di provisioning Azure AD e l'agente di provisioning di Azure AD Connect effettuano il provisioning dei dati utente dal tenant dell'app HR cloud al Active Directory. A seconda dell'evento, può comportare operazioni di creazione, aggiornamento, abilitazione e disabilitazione in Active Directory.
- **Sincronizzare con Azure AD e scrivere di nuovo il messaggio di posta elettronica e il nome utente dall'Active Directory locale all'app Cloud HR.** Dopo l'aggiornamento degli account in Active Directory, questo viene sincronizzato con Azure AD tramite Azure AD Connect. Gli indirizzi di posta elettronica e gli attributi del nome utente possono essere riscritti nel tenant dell'app HR cloud.

![Diagramma del flusso di lavoro](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Descrizione del flusso di lavoro

Nel diagramma sono indicati i passaggi principali seguenti:  

1. Il **team HR** esegue le transazioni nel tenant del cloud HR app.
2. **Azure ad servizio di provisioning** esegue i cicli pianificati dal tenant dell'app HR cloud e identifica le modifiche che devono essere elaborate per la sincronizzazione con Active Directory.
3. **Azure ad servizio di provisioning** richiama l'agente di provisioning di Azure ad Connect con un payload di richiesta che contiene Active Directory operazioni di creazione, aggiornamento, abilitazione e disabilitazione dell'account.
4. **Azure ad Connect agente di provisioning** utilizza un account del servizio per gestire i dati dell'account di Active Directory.
5. **Azure ad Connect** esegue la [sincronizzazione](../hybrid/how-to-connect-sync-whatis.md) Delta per eseguire il pull degli aggiornamenti in Active Directory.
6. **Active Directory** gli aggiornamenti vengono sincronizzati con Azure ad.
7. Il **servizio di provisioning di Azure ad** scrive gli attributi di posta elettronica e il nome utente da Azure ad al tenant dell'app HR cloud.

## <a name="plan-the-deployment-project"></a>Pianificare il progetto di distribuzione

Considerare le esigenze organizzative quando si determina la strategia per la distribuzione nell'ambiente in uso.

### <a name="engage-the-right-stakeholders"></a>Coinvolgere gli stakeholder appropriati

Quando i progetti tecnologici hanno esito negativo, in genere lo fanno a causa di una mancata corrispondenza delle aspettative su conseguenze, risultati e responsabilità. Per evitare questi problemi, [assicurarsi di coinvolgere gli stakeholder appropriati](https://aka.ms/deploymentplans). Assicurarsi inoltre che i ruoli stakeholder nel progetto siano ben noti. Documentare le parti interessate e i rispettivi input e responsabilità del progetto.

Includere un rappresentante dell'organizzazione HR che può fornire input sui processi aziendali HR esistenti e sull'identità del ruolo di lavoro, oltre ai requisiti di elaborazione dei dati dei processi.

### <a name="plan-communications"></a>Pianificare le comunicazioni

La comunicazione è fondamentale per il successo di un nuovo servizio. Comunica in modo proattivo con gli utenti in merito a quando e come la loro esperienza cambierà. Informare gli utenti su come ottenere supporto se si verificano problemi.

### <a name="plan-a-pilot"></a>Pianificare un progetto pilota

L'integrazione di processi aziendali HR e flussi di lavoro di identità dall'app Cloud HR ai sistemi di destinazione richiede una notevole quantità di convalida dei dati, trasformazione dei dati, pulizia dei dati e test end-to-end prima di poter distribuire la soluzione in produzione.

Eseguire la configurazione iniziale in un [ambiente pilota](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) prima di ridimensionarla a tutti gli utenti nell'ambiente di produzione.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Selezionare Cloud HR provisioning Connector Apps

Per semplificare Azure AD il provisioning dei flussi di lavoro tra l'app Cloud HR e la Active Directory, è possibile aggiungere più app del connettore di provisioning dalla raccolta di app di Azure AD:

- **App HR cloud per il provisioning dell'utente Active Directory**: questa app del connettore di provisioning facilita il provisioning degli account utente dall'app HR cloud a un singolo dominio di Active Directory. Se sono presenti più domini, è possibile aggiungere un'istanza di questa app dalla raccolta di app di Azure AD per ogni dominio di Active Directory in cui è necessario effettuare il provisioning.
- **App HR cloud per il provisioning utenti Azure ad**: Sebbene Azure ad Connect sia lo strumento da usare per sincronizzare Active Directory utenti Azure ad, questa app del connettore di provisioning può essere usata per semplificare il provisioning degli utenti solo cloud dall'app Cloud HR a un singolo tenant di Azure ad.
- **Write-back cloud HR app**: questa app del connettore di provisioning facilita il writeback degli indirizzi di posta elettronica dell'utente da Azure ad all'app Cloud HR.

Ad esempio, l'immagine seguente elenca le app del connettore per la giornata lavorativa disponibili nella raccolta di app Azure AD.

![Raccolta di app del portale di Azure Active Directory](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>Diagramma del flusso decisionale

Usare il seguente diagramma di flusso decisionale per identificare le app di provisioning HR cloud rilevanti per il proprio scenario.

![Diagramma del flusso decisionale](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>Progettare la topologia di distribuzione dell'agente di provisioning di Azure AD Connect

L'integrazione del provisioning tra l'app Cloud HR e Active Directory richiede quattro componenti:

- Tenant app Cloud HR
- App del connettore di provisioning
- Agente di provisioning di Azure AD Connect
- Dominio di Active Directory

La topologia di distribuzione dell'agente di provisioning di Azure AD Connect dipende dal numero di tenant di app HR cloud e Active Directory domini figlio che si intende integrare. Se si dispone di più domini Active Directory, dipende dal fatto che i domini Active Directory siano contigui [o non contigui](/windows-server/identity/ad-ds/plan/disjoint-namespace).

In base alla decisione scelta, scegliere uno degli scenari di distribuzione:

- Tenant di app HR per cloud singolo-> di destinazione di uno o più domini Active Directory figlio in una foresta trusted
- Tenant di app HR Single cloud-> destinazione di più domini figlio in una foresta Active Directory non contigua

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>Tenant di app HR per cloud singolo-> di destinazione di uno o più domini Active Directory figlio in una foresta trusted

Si consiglia la configurazione di produzione seguente:

|Requisito|Recommendation|
|:-|:-|
|Numero di agenti di provisioning Azure AD Connect da distribuire|Due (per la disponibilità elevata e il failover)
|Numero di app del connettore di provisioning da configurare|Un'app per dominio figlio|
|Host server per Azure AD Connect agente di provisioning|Windows 2012 R2 + con la linea di visibilità per i controller di dominio Active Directory geolocati</br>Può coesistere con Azure AD Connect servizio|

![Flusso in agenti locali](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>Tenant di app HR Single cloud-> destinazione di più domini figlio in una foresta Active Directory non contigua

Questo scenario implica il provisioning degli utenti dall'app Cloud HR ai domini in foreste Active Directory non contigue.

Si consiglia la configurazione di produzione seguente:

|Requisito|Recommendation|
|:-|:-|
|Numero di agenti di provisioning di Azure AD Connect da distribuire in locale|Due per foresta Active Directory non contigua|
|Numero di app del connettore di provisioning da configurare|Un'app per dominio figlio|
|Host server per Azure AD Connect agente di provisioning|Windows 2012 R2 + con la linea di visibilità per i controller di dominio Active Directory geolocati</br>Può coesistere con Azure AD Connect servizio|

![Foresta Active Directory non contigua del tenant dell'app HR Single cloud](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Requisiti dell'agente di provisioning Azure AD Connect

L'app HR cloud per Active Directory soluzione di provisioning degli utenti richiede la distribuzione di uno o più agenti di provisioning di Azure AD Connect in server che eseguono Windows 2012 R2 o versione successiva. Il server deve avere almeno 4 GB di RAM e .NET 4.7.1 + Runtime. Verificare che il server host disponga dell'accesso di rete al dominio Active Directory di destinazione.

Per preparare l'ambiente locale, la configurazione guidata dell'agente di provisioning di Azure AD Connect registra l'agente con il tenant di Azure AD, [apre le porte](../manage-apps/application-proxy-add-on-premises-application.md#open-ports), [consente l'accesso agli URL](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)e supporta la [configurazione del proxy HTTPS in uscita](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).

L'agente di provisioning utilizza un account del servizio per comunicare con i domini Active Directory. Prima di installare l'agente, creare un account del servizio in Active Directory utenti e computer che soddisfino i requisiti seguenti:

- Una password senza scadenza
- Autorizzazioni di controllo delegate per la lettura, la creazione, l'eliminazione e la gestione degli account utente

È possibile selezionare i controller di dominio che devono gestire le richieste di provisioning. Se si dispone di diversi controller di dominio distribuiti geograficamente, installare l'agente di provisioning nello stesso sito dei controller di dominio preferiti. Questo posizionamento migliora l'affidabilità e le prestazioni della soluzione end-to-end.

Per la disponibilità elevata, è possibile distribuire più di un Azure AD Connect agente di provisioning. Registrare l'agente per gestire lo stesso set di domini Active Directory locali.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Pianificare i filtri di ambito e il mapping degli attributi

Quando si Abilita il provisioning dall'app HR cloud a Active Directory o Azure AD, il portale di Azure controlla i valori dell'attributo tramite il mapping degli attributi.

### <a name="define-scoping-filters"></a>Definire i filtri di ambito

Usare i [filtri di ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) per definire le regole basate su attributi che determinano quali utenti devono essere sottoposti a provisioning dall'app HR cloud per Active Directory o Azure ad.

Quando si avvia il processo di join, raccogliere i requisiti seguenti:

- L'app per le risorse umane cloud è usata per portare a bordo i dipendenti e i lavoratori condizionali?
- Si prevede di usare l'app Cloud HR per Azure AD il provisioning degli utenti per gestire i dipendenti e i lavoratori condizionali?
- Si prevede di implementare l'app Cloud HR per Azure AD il provisioning degli utenti solo per un subset di utenti dell'app Cloud HR? Un esempio può essere costituito solo da dipendenti.

A seconda dei requisiti, quando si configurano i mapping degli attributi, è possibile impostare il campo **ambito dell'oggetto di origine** in modo da selezionare i set di utenti nell'app Cloud HR nell'ambito per il provisioning in Active Directory. Per altre informazioni, vedere l'esercitazione cloud HR app per i filtri di ambito comunemente usati.

### <a name="determine-matching-attributes"></a>Determinare gli attributi corrispondenti

Con il provisioning, è possibile trovare la corrispondenza con gli account esistenti tra il sistema di origine e quello di destinazione. Quando si integra l'app Cloud HR con il servizio di provisioning di Azure AD, è possibile [configurare il mapping degli attributi](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings) per determinare quali dati utente devono fluire dall'app HR cloud a Active Directory o Azure ad.

Quando si avvia il processo di join, raccogliere i requisiti seguenti:

- Qual è l'ID univoco in questa app Cloud HR usato per identificare ogni utente?
- Dal punto di vista del ciclo di vita delle identità, come si gestiscono i riassunti? I riassunti mantengono gli ID dei dipendenti precedenti?
- Si elaborano assunzioni future con data e si creano in anticipo Active Directory account?
- Dal punto di vista del ciclo di vita delle identità, come si gestisce la conversione dei dipendenti da un lavoratore condizionale o in caso contrario?
- Gli utenti convertiti mantengono i vecchi account di Active Directory o ne ottengono di nuovi?

A seconda dei requisiti, Azure AD supporta il mapping diretto da attributo a attributo fornendo valori costanti o [scrivendo espressioni per i mapping degli attributi](../app-provisioning/functions-for-customizing-application-data.md). Questa flessibilità consente di controllare in modo definitivo gli elementi popolati nell'attributo dell'app di destinazione. È possibile usare l' [API Microsoft Graph](../app-provisioning/export-import-provisioning-configuration.md) e Graph Explorer per esportare i mapping degli attributi e lo schema del provisioning degli utenti in un file JSON e importarli di nuovo in Azure ad.

Per impostazione predefinita, l'attributo nell'app Cloud HR che rappresenta l'ID dipendente univoco viene usato come attributo corrispondente *mappato all'attributo univoco in Active Directory.* Ad esempio, nello scenario di app per la giornata lavorativa viene eseguito il mapping dell'attributo **WorkerID** di **giornata lavorativa** all'attributo Active Directory **EmployeeID** .

È possibile impostare più attributi corrispondenti e assegnare la precedenza corrispondente. Sono valutati in base alla precedenza corrispondente. Quando viene rilevata una corrispondenza la valutazione degli attributi corrispondenti termina.

È anche possibile [personalizzare i mapping degli attributi predefiniti](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types), ad esempio modificando o eliminando i mapping degli attributi esistenti. È anche possibile creare nuovi mapping degli attributi in base alle esigenze aziendali. Per altre informazioni, vedere l'esercitazione cloud HR app (ad esempio, [giornata lavorativa](../saas-apps/workday-inbound-tutorial.md#managing-your-configuration)) per un elenco di attributi personalizzati di cui eseguire il mapping.

### <a name="determine-user-account-status"></a>Determinare lo stato dell'account utente

Per impostazione predefinita, l'app del connettore di provisioning esegue il mapping dello stato del profilo utente HR allo stato dell'account utente in Active Directory o Azure AD per determinare se abilitare o disabilitare l'account utente.

Quando si avvia il processo di Joiner-Leavers, raccogliere i requisiti seguenti.

| Processo | Requisiti |
| - | - |
| **Falegnami** | Dal punto di vista del ciclo di vita delle identità, come si gestiscono i riassunti? I riassunti mantengono gli ID dei dipendenti precedenti? |
| | Si elaborano assunzioni future con data e si creano in anticipo Active Directory account? Questi account vengono creati in uno stato abilitato o disabilitato? |
| | Dal punto di vista del ciclo di vita delle identità, come si gestisce la conversione dei dipendenti da un lavoratore condizionale o in caso contrario? |
| | Gli utenti convertiti mantengono i vecchi account di Active Directory o ne ottengono di nuovi? |
| **Che** | Le terminazioni sono gestite in modo diverso per i dipendenti e i ruoli di lavoro contingente in Active Directory? |
| | Quali date effettive vengono considerate per l'elaborazione della terminazione utente? |
| | In che modo le conversioni dei dipendenti e dei lavoratori condizionali influiscono sugli account di Active Directory esistenti? |
| | In che modo è possibile elaborare l'operazione di revoca in Active Directory? È necessario gestire le operazioni di annullamento se i assunzioni con data successiva vengono creati in Active Directory come parte del processo di join. |

A seconda dei requisiti, è possibile personalizzare la logica di mapping usando le [espressioni Azure ad](../app-provisioning/functions-for-customizing-application-data.md) in modo che l'account Active Directory venga abilitato o disabilitato in base a una combinazione di punti dati.

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>Mappare le app HR cloud a Active Directory attributi utente

Ogni app HR cloud viene fornita con app Cloud HR predefinite per Active Directory mapping.

Quando si avvia il processo di Joiners-Movers-Leavers, raccogliere i requisiti seguenti.

| Processo | Requisiti |
| - | - |
| **Falegnami** | Il processo di creazione dell'account Active Directory manuale, automatizzato o parzialmente automatizzato? |
| | Si prevede di propagare gli attributi personalizzati dall'app HR cloud a Active Directory? |
| **Motori** | Quali attributi si desidera elaborare ogni volta che un'operazione di spostamento viene eseguita nell'app HR cloud? |
| | Vengono eseguite convalide di attributi specifici al momento dell'aggiornamento dell'utente? In caso affermativo, specificare i dettagli. |
| **Che** | Le terminazioni sono gestite in modo diverso per i dipendenti e i ruoli di lavoro contingente in Active Directory? |
| | Quali date effettive vengono considerate per l'elaborazione della terminazione utente? |
| | In che modo le conversioni dei dipendenti e dei lavoratori condizionali influiscano sugli account di Active Directory esistenti? |

A seconda dei requisiti, è possibile modificare i mapping per soddisfare gli obiettivi di integrazione. Per altre informazioni, vedere l'esercitazione sull'app HR cloud specifica (ad esempio, [giornata lavorativa](../saas-apps/workday-inbound-tutorial.md#part-4-configure-attribute-mappings)) per un elenco di attributi personalizzati di cui eseguire il mapping.

### <a name="generate-a-unique-attribute-value"></a>Genera un valore di attributo univoco

Quando si avvia il processo di join, potrebbe essere necessario generare valori di attributo univoci quando si impostano attributi come CN, samAccountName e UPN, che presenta vincoli univoci.

La funzione Azure AD [SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) valuta ogni regola e quindi controlla il valore generato per l'univocità nel sistema di destinazione. Per un esempio, vedere [generare un valore univoco per l'attributo userPrincipalName (UPN)](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute).

> [!NOTE]
> Questa funzione è attualmente supportata solo per la giornata lavorativa Active Directory il provisioning degli utenti. Non può essere usato con altre app di provisioning.

### <a name="configure-active-directory-ou-container-assignment"></a>Configurare Active Directory assegnazione del contenitore OU

Si tratta di un requisito comune per inserire gli account utente Active Directory in contenitori basati su Business Unit, posizioni e reparti. Quando si avvia un processo di spostamento e in caso di modifica di un'organizzazione di supervisione, potrebbe essere necessario spostare l'utente da un'unità organizzativa a un'altra in Active Directory.

Utilizzare la funzione [Switch ()](../app-provisioning/functions-for-customizing-application-data.md#switch) per configurare la logica di business per l'assegnazione dell'unità organizzativa ed eseguirne il mapping all'attributo Active Directory **parentDistinguishedName**.

Se, ad esempio, si desidera creare utenti in un'unità organizzativa basata sul **comune**degli attributi HR, è possibile utilizzare l'espressione seguente:

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Con questa espressione, se il valore del comune è Dallas, Austin, Seattle o Londra, l'account utente verrà creato nell'unità organizzativa corrispondente. Se non esiste alcuna corrispondenza, l'account viene creato nell'unità organizzativa predefinita.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Pianificare il recapito delle password di nuovi account utente

Quando si avvia il processo di join, è necessario impostare e recapitare una password temporanea dei nuovi account utente. Con il servizio HR cloud per Azure AD il provisioning degli utenti, è possibile implementare la funzionalità di [reimpostazione Azure ad della password self-service](../authentication/tutorial-enable-sspr.md) (SSPR) per l'utente nel primo giorno.

SSPR è un modo semplice per gli amministratori IT di consentire agli utenti di reimpostare le password o sbloccare i propri account. È possibile eseguire il provisioning dell'attributo del **numero di cellulare** dall'app Cloud HR per Active Directory e sincronizzarlo con Azure ad. Dopo che l'attributo del **numero mobile** è Azure ad, è possibile abilitare SSPR per l'account dell'utente. Quindi, il primo giorno, il nuovo utente può usare il numero mobile registrato e verificato per l'autenticazione.

## <a name="plan-for-initial-cycle"></a>Pianificare il ciclo iniziale

Quando il servizio di provisioning Azure AD viene eseguito per la prima volta, viene eseguito un [ciclo iniziale](../app-provisioning/how-provisioning-works.md#initial-cycle) sull'app HR cloud per creare uno snapshot di tutti gli oggetti utente nell'app Cloud HR. Il tempo impiegato per i cicli iniziali dipende direttamente dal numero di utenti presenti nel sistema di origine. Il ciclo iniziale per alcuni tenant app Cloud HR con oltre 100.000 utenti può richiedere molto tempo.

**Per i tenant cloud HR app di grandi dimensioni (>30.000 utenti),** eseguire il ciclo iniziale in fasi progressive. Avviare gli aggiornamenti incrementali solo dopo aver convalidato che gli attributi corretti siano impostati in Active Directory per diversi scenari di provisioning degli utenti. Seguire l'ordine qui.

1. Eseguire il ciclo iniziale solo per un set limitato di utenti impostando il [filtro di ambito](#plan-scoping-filters-and-attribute-mapping).
2. Verificare Active Directory il provisioning dell'account e i valori degli attributi impostati per gli utenti selezionati per la prima esecuzione. Se il risultato soddisfa le aspettative, espandere il filtro di ambito per includere progressivamente più utenti e verificare i risultati per la seconda esecuzione.

Quando si è soddisfatti dei risultati del ciclo iniziale per gli utenti di test, avviare gli [aggiornamenti incrementali](../app-provisioning/how-provisioning-works.md#incremental-cycles).

## <a name="plan-testing-and-security"></a>Pianificare test e sicurezza

In ogni fase della distribuzione dal progetto pilota iniziale tramite l'abilitazione del provisioning degli utenti, assicurarsi di verificare che i risultati siano quelli previsti e di controllare i cicli di provisioning.

### <a name="plan-testing"></a>Panificare i test

Dopo aver configurato l'app HR cloud per Azure AD il provisioning degli utenti, eseguire i test case per verificare se la soluzione soddisfa i requisiti dell'organizzazione.

|Scenari|Risultati previsti|
|:-|:-|
|Il nuovo dipendente viene assunto nell'app Cloud HR.| -Viene effettuato il provisioning dell'account utente in Active Directory.</br>-L'utente può accedere alle app Active Directory-Domain ed eseguire le azioni desiderate.</br>-Se Azure AD Connect sincronizzazione è configurata, anche l'account utente viene creato nel Azure AD.
|L'utente è terminato nell'app Cloud HR.|-L'account utente è disabilitato in Active Directory.</br>-L'utente non può accedere ad alcuna app aziendale protetta da Active Directory.
|L'organizzazione di supervisione utente viene aggiornata nell'app Cloud HR.|In base al mapping degli attributi, l'account utente viene spostato da un'unità organizzativa a un'altra in Active Directory.|
|HR aggiorna il responsabile dell'utente nell'app Cloud HR.|Il campo Manager in Active Directory viene aggiornato in modo da riflettere il nome del nuovo responsabile.|
|HR riassume un dipendente in un nuovo ruolo.|Il comportamento dipende dal modo in cui l'app HR cloud è configurata per generare ID dipendente:</br>-Se il vecchio ID dipendente viene riutilizzato per una riassunzione, il connettore Abilita l'account Active Directory esistente per l'utente.</br>-Se il riassunzione ottiene un nuovo ID dipendente, il connettore crea un nuovo account di Active Directory per l'utente.|
|HR converte il dipendente in un contratto di lavoro o viceversa.|Viene creato un nuovo account di Active Directory per il nuovo utente e l'account precedente viene disabilitato alla data effettiva della conversione.|

Usare i risultati precedenti per determinare come eseguire la transizione dell'implementazione del provisioning utenti automatico in produzione in base alle tempistiche stabilite.

> [!TIP]
> Usare tecniche quali la riduzione dei dati e lo scrubbing dei dati quando si aggiorna l'ambiente di test con i dati di produzione per rimuovere o mascherare i dati personali sensibili in conformità con gli standard di privacy e sicurezza. 

### <a name="plan-security"></a>Pianificare la sicurezza

Per la distribuzione di un nuovo servizio è in genere necessario eseguire una verifica della sicurezza. Se è richiesta o non è stata eseguita una verifica della sicurezza, vedere i molti Azure AD [white paper](https://www.microsoft.com/download/details.aspx?id=36391) che forniscono una panoramica dell'identità come servizio.

### <a name="plan-rollback"></a>Pianificare il rollback

L'implementazione del provisioning utenti cloud HR potrebbe non funzionare nel modo desiderato nell'ambiente di produzione. In tal caso, i passaggi di rollback seguenti possono risultare utili per il ripristino di uno stato valido noto precedente.

1. Esaminare il [report di riepilogo del provisioning](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) e i [log di provisioning](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) per determinare le operazioni non corrette eseguite sugli utenti o sui gruppi interessati. Per altre informazioni sul report di riepilogo del provisioning e sui log, vedere [gestire il provisioning utenti dell'app HR cloud](#manage-your-configuration).
2. L'ultimo stato valido noto degli utenti o dei gruppi interessati può essere determinato tramite i log di controllo del provisioning o rivedendo i sistemi di destinazione (Azure AD o Active Directory).
3. Collaborare con il proprietario dell'app per aggiornare gli utenti o i gruppi interessati direttamente nell'app usando gli ultimi valori di stato validi noti.

## <a name="deploy-the-cloud-hr-app"></a>Distribuire l'app HR cloud

Scegliere l'app Cloud HR allineata ai requisiti della soluzione.

**Giornata lavorativa**: per importare i profili di lavoro dalla giornata lavorativa a Active Directory e Azure ad, vedere [esercitazione: configurare la giornata lavorativa per il provisioning utenti automatico](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment). Facoltativamente, è possibile riscrivere l'indirizzo di posta elettronica, il nome utente e il numero di telefono per la giornata lavorativa.

**SAP SuccessFactors**: per importare i profili di lavoro da SuccessFactors in Active Directory e Azure ad, vedere [esercitazione: configurare SAP SuccessFactors per il provisioning utenti automatico](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md). Facoltativamente, è possibile scrivere nuovamente l'indirizzo di posta elettronica e il nome utente in SuccessFactors.

## <a name="manage-your-configuration"></a>Gestire la configurazione

Azure AD possibile fornire informazioni aggiuntive sull'utilizzo del provisioning degli utenti dell'organizzazione e sull'integrità operativa tramite i log di controllo e i report.

### <a name="gain-insights-from-reports-and-logs"></a>Ottenere informazioni dettagliate da report e log

Dopo un [ciclo iniziale](../app-provisioning/how-provisioning-works.md#initial-cycle)riuscito, il servizio di provisioning Azure ad continua a eseguire aggiornamenti incrementali back-to-back a tempo indeterminato, a intervalli definiti nelle esercitazioni specifiche per ogni app, fino a quando non si verifica uno degli eventi seguenti:

- Il servizio è stato arrestato manualmente. Un nuovo ciclo iniziale viene attivato usando il [portale di Azure](https://portal.azure.com/) o il comando [API Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) appropriato.
- Un nuovo ciclo iniziale viene attivato a causa di una modifica nei mapping degli attributi o nei filtri di ambito.
- Il processo di provisioning entra in quarantena a causa di una frequenza di errore elevata. Rimane in quarantena per più di quattro settimane, a quel punto viene disabilitata automaticamente.

Per esaminare questi eventi e tutte le altre attività eseguite dal servizio di provisioning, [informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md).

#### <a name="azure-monitor-logs"></a>Log di Monitoraggio di Azure

Tutte le attività eseguite dal servizio di provisioning vengono registrate nei log di controllo Azure AD. È possibile instradare i log di controllo Azure AD ai log di monitoraggio di Azure per un'ulteriore analisi. Con i log di monitoraggio di Azure (noti anche come area di lavoro Log Analytics), è possibile eseguire query sui dati per individuare gli eventi, analizzare le tendenze ed eseguire la correlazione tra varie origini dati. Guarda questo [video](https://youtu.be/MP5IaCTwkQg) per scoprire i vantaggi derivanti dall'uso dei log di monitoraggio di Azure per i log Azure ad in scenari utente pratici.

Installare le [visualizzazioni di log Analytics per Azure ad log attività](../reports-monitoring/howto-install-use-log-analytics-views.md) per ottenere l'accesso ai [report predefiniti](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) relativi agli eventi di provisioning nell'ambiente.

Per altre informazioni, vedere come [analizzare i log attività di Azure ad con i log di monitoraggio di Azure](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md).

### <a name="manage-personal-data"></a>Gestire dati personali

L'agente di provisioning di Azure AD Connect installato in Windows Server crea log nel registro eventi di Windows che potrebbero contenere dati personali a seconda dell'app HR cloud per Active Directory mapping degli attributi. Per rispettare gli obblighi relativi alla privacy degli utenti, configurare un'attività pianificata di Windows per cancellare il registro eventi e assicurarsi che i dati non vengano conservati oltre 48 ore.

Azure AD servizio di provisioning non genera report, esegue analisi o fornisce informazioni dettagliate oltre 30 giorni, perché il servizio non archivia, elabora o mantiene i dati oltre i 30 giorni.

### <a name="troubleshoot"></a>Risolvere problemi

Per risolvere eventuali problemi che potrebbero verificarsi durante il provisioning, vedere gli articoli seguenti:

- [Problemi di configurazione del provisioning utenti in un'applicazione della raccolta di Azure AD](application-provisioning-config-problem.md)
- [Sincronizzare un attributo dal Active Directory locale a Azure AD per il provisioning in un'applicazione](user-provisioning-sync-attributes-for-mapping.md)
- [Problemi di salvataggio delle credenziali dell'amministratore durante la configurazione del provisioning utenti in un'applicazione della raccolta di Azure Active Directory](application-provisioning-config-problem-storage-limit.md)
- [Nessun utente è sottoposto a provisioning per un'applicazione della raccolta di Azure AD](application-provisioning-config-problem-no-users-provisioned.md)
- [È in corso il provisioning di un set errato di utenti per un'applicazione della raccolta di Azure AD](application-provisioning-config-problem-wrong-users-provisioned.md)
- [Configurazione di Visualizzatore eventi di Windows per la risoluzione dei problemi dell'agente](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Configurazione dei log di controllo del portale di Azure per la risoluzione dei problemi di servizio](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Riconoscimento di log per operazioni di creazione per l'account utente di AD](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [Riconoscimento di log per operazioni di aggiornamento manager](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [Risoluzione degli errori più comuni](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Passaggi successivi

- [Scrittura di espressioni per i mapping degli attributi](functions-for-customizing-application-data.md)
- [Azure AD synchronization API overview](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) (Panoramica dell'API di sincronizzazione di Azure AD)
- [Ignora l'eliminazione di account utente che non rientrano nell'ambito](skip-out-of-scope-deletions.md)
- [Agente di provisioning di Azure AD Connect: cronologia delle versioni](provisioning-agent-release-version-history.md)