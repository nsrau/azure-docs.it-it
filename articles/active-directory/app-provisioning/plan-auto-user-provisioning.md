---
title: Pianificare una distribuzione automatica del provisioning degli utenti per Azure Active DirectoryPlan an automatic user provisioning deployment for Azure Active Directory
description: Linee guida per la pianificazione e l'esecuzione del provisioning automatico degli utenti
services: active-directory
author: martincoetzer
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: martinco
ms.reviewer: arvindha
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d2f284fddfc49632e467adbf5877856b40a81dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522411"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>Pianificare una distribuzione automatica del provisioning utenti

Molte organizzazioni si affidano alle applicazioni SaaS (Software as a Service), come ServiceNow, Sscaler e Slack, per la produttività degli utenti finali. Storicamente il personale IT si è affidato a metodi di provisioning manuali come il caricamento di file CSV o l'utilizzo di script personalizzati per gestire in modo sicuro le identità utente in ogni applicazione SaaS. Questi processi sono soggetti a errori, insicuri e difficili da gestire.

Il provisioning automatico degli utenti di Azure Active Directory (Azure AD) semplifica questo processo automatizzando in modo sicuro la creazione, la manutenzione e la rimozione delle identità utente nelle applicazioni SaaS in base alle regole di business. Questa automazione consente di ridimensionare in modo efficace i sistemi di gestione delle identità in ambienti solo cloud e ibridi quando si espande la dipendenza dalle soluzioni basate su cloud.

Per comprendere meglio la funzionalità, vedere Automatizzare il provisioning e il deprovisioning degli utenti nelle [applicazioni SaaS con Azure Active Directory.See Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](../app-provisioning/user-provisioning.md) to better understand the functionality.

## <a name="learn"></a>Informazioni

Il provisioning degli utenti crea una base per una governance delle identità continua e migliora la qualità dei processi aziendali che si basano su dati di identità autorevoli.

### <a name="key-benefits"></a>Vantaggi principali

I vantaggi principali dell'abilitazione del provisioning automatico degli utenti sono:The key benefits of enabling automatic user provisioning are:

* **Aumento della produttività**. È possibile gestire le identità utente nelle applicazioni SaaS con un'unica interfaccia di gestione del provisioning degli utenti. Questa interfaccia dispone di un singolo set di criteri di provisioning.

* **Gestire il rischio**. È possibile aumentare la sicurezza automatizzando le modifiche in base allo stato dei dipendenti o alle appartenenze ai gruppi che definiscono i ruoli e/o l'accesso.

* **Affrontare la conformità e la governance**. Azure AD supporta i log di controllo nativi per ogni richiesta di provisioning degli utenti. Le richieste vengono eseguite sia nel sistema di origine che in quello di destinazione. In questo modo è possibile tenere traccia di chi ha accesso alle applicazioni da un'unica schermata.

* **Riduci i costi**. Il provisioning automatico degli utenti riduce i costi evitando inefficienze ed errori umani associati al provisioning manuale. Riduce la necessità di soluzioni di provisioning degli utenti sviluppate su misura, script e log di controllo.

### <a name="licensing"></a>Gestione delle licenze

Azure AD offre l'integrazione self-service di qualsiasi applicazione che usa i modelli forniti nel menu della raccolta applicazioni. Per un elenco completo dei requisiti di licenza, vedere la pagina delle licenze di [Azure AD.](https://azure.microsoft.com/pricing/details/active-directory/)

#### <a name="application-licensing"></a>Licenze per applicazioni

Avrai bisogno delle licenze appropriate per le applicazioni che desideri eseguire automaticamente il provisioning. Discutere con i proprietari dell'applicazione se gli utenti assegnati all'applicazione dispongono delle licenze appropriate per i ruoli dell'applicazione. Se Azure AD gestisce il provisioning automatico in base ai ruoli, i ruoli assegnati in Azure AD devono essere allineati alle licenze delle applicazioni. Le licenze non corrette di proprietà dell'applicazione possono causare errori durante il provisioning/aggiornamento di un utente.

### <a name="terms"></a>Termini

In questo articolo vengono utilizzati i termini seguenti:

* Operazioni CRUD - Azioni eseguite sugli account utente: crea, lettura, aggiornamento, eliminazione.

* Single Sign-On (SSO): la possibilità per un utente di accedere una sola volta e di accedere a tutte le applicazioni abilitate per SSO. Nel contesto del provisioning degli utenti, SSO è il risultato di un singolo account per gli utenti che utilizzano il provisioning automatico degli utenti.

* Sistema di origine: archivio di utenti di cui Azure AD esegue il provisioning. Azure AD è il sistema di origine per la maggior parte dei connettori di provisioning preintegrati. Tuttavia, esistono alcune eccezioni per le applicazioni cloud come SAP, Workday e AWS. Ad esempio, vedere [Provisioning degli utenti da Workday ad AD](../saas-apps/workday-inbound-tutorial.md).

* Sistema di destinazione: repository di utenti a cui Azure AD esegue il provisioning. Il sistema di destinazione è in genere un'applicazione SaaS, ad esempio ServiceNow, scalare e Slack. Il sistema di destinazione può anche essere un sistema locale, ad esempio AD.

* [System for Cross-domain Identity Management (SCIM)](https://aka.ms/scimoverview) - Uno standard aperto che consente l'automazione del provisioning degli utenti. SCIM comunica i dati sull'identità utente tra provider di identità come Microsoft e provider di servizi come Salesforce o altre app SaaS che richiedono informazioni sull'identità dell'utente.

### <a name="training-resources"></a>Risorse per la formazione

| Risorse| Collegamento e descrizione |
| - | - |
| Webinar on demand| [Gestire le applicazioni aziendali con Azure ADManage your Enterprise Applications with Azure AD](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>Scopri come Azure AD può aiutarti a raggiungere SSO per le tue applicazioni SaaS aziendali e le best practice per il controllo dell'accesso. |
| Video| [Che cos'è il provisioning degli utenti in Active Azure Directory?](https://youtu.be/_ZjARPpI6NI) <br> [Come distribuire il provisioning degli utenti in Active Azure Directory?](https://youtu.be/pKzyts6kfrw) <br> [Integrazione di Salesforce con Azure AD: come automatizzare il provisioning degli utenti](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| Corsi online| SkillUp Online: [Gestione delle identità](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Informazioni su come integrare Azure AD con molte applicazioni SaaS e proteggere l'accesso degli utenti a tali applicazioni. |
| Pubblicazioni| [Autenticazione moderna con Azure Active Directory per applicazioni Web (Riferimento per sviluppatori) 1a edizione](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0).  <br> Si tratta di una guida autorevole e approfondita per la creazione di soluzioni di autenticazione di Active Directory per questi nuovi ambienti. |
| Esercitazioni| Vedere [l'elenco di esercitazioni su come integrare le app SaaS con Azure AD.](../saas-apps/tutorial-list.md) |
| Domande frequenti| [Domande frequenti](../app-provisioning/user-provisioning.md) sul provisioning automatico degli utenti |

### <a name="solution-architectures"></a>Architetture delle soluzioni

Il servizio di provisioning di Azure AD effettua il provisioning degli utenti nelle app SaaS e in altri sistemi connettendosi agli endpoint API di gestione utenti forniti da ogni fornitore di applicazioni. Gli endpoint dell'API di gestione utenti consentono ad Azure AD di creare, aggiornare e rimuovere utenti a livello di codice.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>Provisioning automatico degli utenti per le aziende ibride

In questo esempio, gli utenti e/o i gruppi vengono creati in un database delle risorse umane connesso a una directory locale. Il servizio di provisioning di Azure AD gestisce il provisioning automatico degli utenti nelle applicazioni SaaS di destinazione.

 ![provisioning degli utenti](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**Descrizione del flusso di lavoro:**

1. Gli utenti/gruppi vengono creati in un'applicazione/sistema HR locale, ad esempio SAP. 

1. **L'agente Azure AD Connect** esegue le sincronizzazioni pianificate delle identità (utenti e gruppi) dall'AD locale ad Azure AD.

1. **Il servizio** di provisioning di Azure AD inizia un [ciclo iniziale](../app-provisioning/user-provisioning.md) sul sistema di origine e sul sistema di destinazione. 

1. Il servizio di provisioning di **Azure AD** esegue una query sul sistema di origine per tutti gli utenti e i gruppi modificati dopo il ciclo iniziale ed esegue il push delle modifiche nei cicli [incrementali.](../app-provisioning/user-provisioning.md)

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>Provisioning automatico degli utenti per le aziende solo cloud

In questo esempio, la creazione di utenti si verifica in Azure AD e il servizio di provisioning di Azure AD gestisce il provisioning automatico degli utenti per le applicazioni di destinazione (SaaS).

![Figura 2](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**Descrizione del flusso di lavoro:**

1. Gli utenti/gruppi vengono creati in Azure AD.

1. **Il servizio** di provisioning di Azure AD inizia un [ciclo iniziale](../app-provisioning/user-provisioning.md) sul sistema di origine e sul sistema di destinazione. 

1. Il servizio di provisioning di **Azure AD** esegue una query sul sistema di origine per tutti gli utenti e i gruppi aggiornati dopo il ciclo iniziale ed esegue qualsiasi ciclo [incrementale.](../app-provisioning/user-provisioning.md)

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>Provisioning automatico degli utenti per le applicazioni HR cloud 

In questo esempio, gli utenti e o i gruppi vengono creati in un'applicazione HR cloud, ad esempio Workday e SuccessFactors.In this example, the users and or groups are created in a cloud HR application like Workday and SuccessFactors. The Azure AD provisioning service and Azure AD Connect provisioning agent provisions the user data from the cloud HR app tenant into AD. Dopo aver aggiornato gli account in Active Directory, questi vengono sincronizzati con Azure AD tramite Azure AD Connect e gli indirizzi di posta elettronica e gli attributi del nome utente possono essere riscritti nel tenant dell'app HR cloud.

![Figura 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  **Il team HR** esegue le transazioni nel tenant dell'app HR cloud.
2.  Il servizio di provisioning di **Azure AD** esegue i cicli pianificati dal tenant dell'app HR cloud e identifica le modifiche che devono essere elaborate per la sincronizzazione con Active Directory.
3.  **Il servizio** di provisioning di Azure AD richiama l'agente di provisioning di Azure AD Connect con un payload della richiesta contenente le operazioni di creazione/aggiornamento/abilitazione/abilitazione/disabilitazione dell'account AD.
4.  **L'agente** di provisioning di Azure AD Connect usa un account del servizio per gestire i dati dell'account AD.
5.  **Azure AD Connect** esegue la sincronizzazione delta per eseguire il pull degli aggiornamenti in Active Directory.Azure AD Connect runs delta sync to pull updates in AD.
6.  **Gli** aggiornamenti di Active Directory vengono sincronizzati con Azure AD. 
7.  **Il servizio** di provisioning di Azure AD scrive l'attributo e il nome utente da Azure AD al tenant dell'app HR cloud.

## <a name="plan-the-deployment-project"></a>Pianificare il progetto di distribuzione

Considerare le esigenze dell'organizzazione per determinare la strategia per la distribuzione del provisioning degli utenti nell'ambiente.

### <a name="engage-the-right-stakeholders"></a>Coinvolgi le parti interessate giuste

Quando i progetti tecnologici falliscono, è in genere a causa di aspettative non corrispondenti su impatto, risultati e responsabilità. Per evitare queste insidie, assicurati di [coinvolgere le parti interessate giuste](https://aka.ms/deploymentplans) e che i ruoli degli stakeholder nel progetto siano ben compresi documentando le parti interessate e il loro input e responsabilità del progetto.

### <a name="plan-communications"></a>Pianificare le comunicazioni

La comunicazione è fondamentale per il successo di ogni nuovo servizio. Comunicare in modo proattivo con gli utenti come cambierà la loro esperienza, quando cambierà e come ottenere supporto in caso di problemi.

### <a name="plan-a-pilot"></a>Pianificare un progetto pilota

È consigliabile che la configurazione iniziale del provisioning automatico degli utenti si appaia in un ambiente di test con un piccolo sottoinsieme di utenti prima di ridimensionarla per tutti gli utenti nell'ambiente di produzione. Vedere [le procedure consigliate](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) per l'esecuzione di un progetto pilota.

#### <a name="best-practices-for-a-pilot"></a>Procedure consigliate per un progetto pilota  

Un progetto pilota consente di eseguire test con un piccolo gruppo prima di implementare una funzionalità per tutti. Assicurarsi che, come parte dei test, ogni caso d'uso all'interno dell'organizzazione venga accuratamente testato.

Nella prima ondata, indirizzare l'IT, l'usabilità e altri utenti appropriati che possono testare e fornire feedback. Utilizzare questo feedback per sviluppare ulteriormente le comunicazioni e le istruzioni inviate agli utenti e per fornire informazioni dettagliate sui tipi di problemi che il personale di supporto potrebbe vedere.

Allargare l'implementazione a gruppi di utenti più grandi aumentando l'ambito dei gruppi interessati. Questa operazione può essere eseguita tramite [l'appartenenza dinamica al gruppo](../users-groups-roles/groups-dynamic-membership.md)o aggiungendo manualmente gli utenti ai gruppi di destinazione.

## <a name="plan-application-connections-and-administration"></a>Pianificare le connessioni e l'amministrazione delle applicazioni

Usare il portale di Azure AD per visualizzare e gestire tutte le applicazioni che supportano il provisioning. Consultate [Ricerca delle app nel portale.](../app-provisioning/configure-automatic-user-provisioning-portal.md)

### <a name="determine-the-type-of-connector-to-use"></a>Determinare il tipo di connettore da utilizzare

I passaggi necessari per abilitare e configurare il provisioning automatico variano a seconda dell'applicazione. Se l'applicazione di cui si vuole eseguire automaticamente il provisioning è elencata nella raccolta di [app di Azure AD SaaS,](../saas-apps/tutorial-list.md)è necessario selezionare [l'esercitazione sull'integrazione specifica dell'app](../saas-apps/tutorial-list.md) per configurare il connettore di provisioning utente predefinito.

In caso contrario, attenersi alla seguente procedura:

1. [Creare una richiesta](../develop/howto-app-gallery-listing.md) per un connettore di provisioning utente preintegrato. Il nostro team lavorerà con te e lo sviluppatore dell'applicazione per l'onboarding della tua applicazione sulla nostra piattaforma se supporta SCIM.

1. Usa il supporto per il provisioning di utenti generici [BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) per l'app. Questo è un requisito per Azure AD per eseguire il provisioning degli utenti all'app senza un connettore di provisioning pre-integrato.

1. Se l'applicazione è in grado di utilizzare il connettore BYOA SCIM, fare riferimento [all'esercitazione sull'integrazione BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) per configurare il connettore SCIM BYOA per l'applicazione.

Per altre informazioni, vedere [Quali applicazioni e sistemi è possibile usare con il provisioning automatico degli utenti](../app-provisioning/user-provisioning.md) di Azure AD.

### <a name="collect-information-to-authorize-application-access"></a>Raccogliere informazioni per autorizzare l'accesso alle applicazioniCollect information to authorize application access

La configurazione del provisioning automatico degli utenti è un processo per applicazione. Per ogni applicazione, è necessario fornire [le credenziali](../app-provisioning/configure-automatic-user-provisioning-portal.md) di amministratore per connettersi all'endpoint di gestione utenti del sistema di destinazione.

L'immagine seguente mostra una versione delle credenziali di amministratore necessarie:

![Schermata di provisioning per gestire le impostazioni di provisioning degli account utente](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

Mentre alcune applicazioni richiedono il nome utente e la password di amministratore, altre potrebbero richiedere un token di connessione.

## <a name="plan-user-and-group-provisioning"></a>Pianificare il provisioning di utenti e gruppi

Se si abilita il provisioning degli utenti per le app aziendali, il portale di [Azure](https://portal.azure.com/) controlla i valori degli attributi tramite il mapping degli attributi.

### <a name="determine-operations-for-each-saas-app"></a>Determinare le operazioni per ogni app SaaSDetermine operations for each SaaS app

Ogni applicazione può avere attributi utente o gruppo univoci che devono essere mappati agli attributi in Azure AD. L'applicazione può avere solo un sottoinsieme di operazioni CRUD disponibili.

Per ogni applicazione, documentare le seguenti informazioni:

* Operazioni di provisioning CRUD da eseguire sugli oggetti utente e o gruppo per i sistemi di destinazione. Ad esempio, ogni proprietario aziendale dell'app SaaS potrebbe non volere tutte le operazioni possibili.

* Attributi disponibili nel sistema di origine

* Attributi disponibili nel sistema di destinazione

* Mappatura degli attributi tra sistemi.

### <a name="choose-which-users-and-groups-to-provision"></a>Scegliere gli utenti e i gruppi di cui eseguire il provisioning

Prima di implementare il provisioning automatico degli utenti, è necessario determinare gli utenti e i gruppi di cui eseguire il provisioning nell'applicazione.

* Usare i filtri di [ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) per definire regole basate su attributi che determinano gli utenti di cui viene eseguito il provisioning in un'applicazione.

* Successivamente, utilizzare [le assegnazioni di utenti e gruppi](../manage-apps/assign-user-or-group-access-portal.md) in base alle esigenze per ulteriori filtri.

### <a name="define-user-and-group-attribute-mapping"></a>Definire il mapping degli attributi di utenti e gruppi

Per implementare il provisioning automatico degli utenti, è necessario definire gli attributi utente e gruppo necessari per l'applicazione. Esiste un set preconfigurato di attributi e mapping di [attributi](../app-provisioning/configure-automatic-user-provisioning-portal.md) tra gli oggetti utente di Azure AD e gli oggetti utente di ogni applicazione SaaS. Non tutte le app SaaS abilitano gli attributi di gruppo.

Azure AD supporta il mapping diretto tra attributi e attributi, fornendo valori costanti o scrivendo espressioni per i mapping degli [attributi.](../app-provisioning/functions-for-customizing-application-data.md) Questa flessibilità consente di controllare correttamente ciò che verrà popolato nell'attributo del sistema di destinazione. È possibile usare [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) ed Graph Explorer per esportare i mapping e lo schema degli attributi di provisioning degli utenti in un file JSON e importarlo nuovamente in Azure AD.

Per altre informazioni, vedere Personalizzazione dei mapping degli attributi di provisioning degli utenti per le applicazioni SaaS in Azure Active Directory.For more information, see [Customizing User Provisioning Attribute-Mappings for SaaS Applications in Azure Active Directory](../app-provisioning/customize-application-attributes.md).

### <a name="special-considerations-for-user-provisioning"></a>Considerazioni speciali per il provisioning degli utenti

Per ridurre i problemi post-distribuzione, tenere presente quanto segue:Consider the following to reduce issues post-deployment:

* Assicurarsi che gli attributi utilizzati per eseguire il mapping degli oggetti utente/gruppo tra le applicazioni di origine e di destinazione siano resilienti. Non devono causare il provisioning non corretto di utenti/gruppi se gli attributi cambiano (ad esempio, un utente passa a una parte diversa della società).

* Le applicazioni possono avere restrizioni e/o requisiti specifici che devono essere soddisfatti per il corretto funzionamento del provisioning degli utenti. Ad esempio, Slack tronca i valori per determinati attributi. Fare riferimento alle [esercitazioni](../saas-apps/tutorial-list.md) sul provisioning automatico degli utenti specifiche per ogni applicazione.

* Verificare la coerenza dello schema tra i sistemi di origine e di destinazione. I problemi comuni includono attributi quali UPN o posta non corrispondente. Ad esempio, UPN in *john_smith@contoso.com* Azure AD impostato come *jsmith@contoso.com*e nell'app, è . Per ulteriori informazioni, vedere Informazioni di riferimento sullo schema di [utenti e gruppi](../app-provisioning/use-scim-to-provision-users-and-groups.md).

## <a name="plan-testing-and-security"></a>Pianificare test e sicurezza

In ogni fase della distribuzione assicurarsi che si sta testando che i risultati sono come previsto e il controllo dei cicli di provisioning.

### <a name="plan-testing"></a>Pianificare i test

Dopo aver configurato il provisioning automatico degli utenti per l'applicazione, verranno eseguiti test case per verificare che questa soluzione soddisfi i requisiti dell'organizzazione.

| Scenari| Risultati previsti |
| - | - |
| L'utente viene aggiunto a un gruppo assegnato al sistema di destinazione | Viene eseguito il provisioning dell'oggetto utente nel sistema di destinazione. <br>L'utente può accedere al sistema di destinazione ed eseguire le azioni desiderate. |
| L'utente viene rimosso da un gruppo assegnato al sistema di destinazione | Viene eseguito il deprovisioning dell'oggetto utente nel sistema di destinazione.<br>L'utente non può accedere al sistema di destinazione. |
| Le informazioni utente vengono aggiornate in Azure AD da qualsiasi metodoUser information is updated in Azure AD by any method | Gli attributi utente aggiornati si riflettono nel sistema di destinazione dopo un ciclo incrementale |
| L'utente non è nell'ambito | L'oggetto utente è disabilitato o eliminato. <br>Nota: questo comportamento viene sostituito per il [provisioning di Workday.](skip-out-of-scope-deletions.md) |

### <a name="plan-security"></a>Pianificare la sicurezza

È comune che una revisione della sicurezza sia necessaria come parte di una distribuzione. Se è necessaria una revisione della sicurezza, vedere i numerosi [white paper di](https://www.microsoft.com/download/details.aspx?id=36391) Azure AD che forniscono una panoramica per l'identità come servizio.

### <a name="plan-rollback"></a>Pianificare il rollback

Se l'implementazione del provisioning automatico degli utenti non funziona come desiderato nell'ambiente di produzione, i passaggi di rollback seguenti possono essere utili per ripristinare uno stato valido noto precedente:

1. Esaminare il report di riepilogo del [provisioning](../app-provisioning/check-status-user-account-provisioning.md) e i log di [provisioning](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) per determinare quali operazioni non corrette si sono verificate negli utenti e/o gruppi interessati.

1. Utilizzare i log di controllo del provisioning per determinare l'ultimo stato valido noto degli utenti e/o dei gruppi interessati. Esaminare anche i sistemi di origine (Azure AD o AD).

1. Collaborare con il proprietario dell'applicazione per aggiornare gli utenti e/o i gruppi interessati direttamente nell'applicazione utilizzando gli ultimi valori di stato valido noti.

## <a name="deploy-automatic-user-provisioning-service"></a>Distribuire il servizio di provisioning automatico degli utentiDeploy automatic user provisioning service

Scegliere i passaggi in linea con i requisiti della soluzione.

### <a name="prepare-for-the-initial-cycle"></a>Prepararsi per il ciclo iniziale

Quando il servizio di provisioning di Azure AD viene eseguito per la prima volta, il ciclo iniziale sul sistema di origine e sui sistemi di destinazione crea uno snapshot di tutti gli oggetti utente per ogni sistema di destinazione.

Quando si abilita il provisioning automatico per un'applicazione, il ciclo iniziale può richiedere da 20 minuti a diverse ore. La durata dipende dalle dimensioni della directory di Azure AD e dal numero di utenti nell'ambito per il provisioning. Vedere [Come migliorare le prestazioni](../app-provisioning/application-provisioning-when-will-provisioning-finish.md)di provisioning .

Il servizio di provisioning archivia lo stato di entrambi i sistemi dopo il ciclo iniziale, migliorando le prestazioni dei cicli incrementali successivi.

### <a name="configure-automatic-user-provisioning"></a>Configurare il provisioning automatico degli utenti

Usare il portale di [Azure](https://portal.azure.com/) per gestire il provisioning e il deprovisioning automatici degli account utente per le applicazioni che lo supportano. Seguire i passaggi descritti in Come si configura il [provisioning automatico in un'applicazione?](../app-provisioning/user-provisioning.md)

Il servizio di provisioning utenti di Azure AD può essere configurato e gestito anche con l'[API di Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview).

## <a name="manage-automatic-user-provisioning"></a>Gestire il provisioning automatico degli utenti

Dopo aver distribuito, è necessario gestire la soluzione.

### <a name="monitor-user-provisioning-operation-health"></a>Monitorare l'integrità delle operazioni di provisioning degli utentiMonitor user provisioning operation health

Dopo un [ciclo iniziale](../app-provisioning/user-provisioning.md)riuscito, il servizio di provisioning di Azure AD eseguirà gli aggiornamenti incrementali per un tempo indefinito, a intervalli specifici per ogni applicazione, fino a quando non si verifica uno dei seguenti eventi:

* Il servizio viene arrestato manualmente e viene attivato un nuovo ciclo iniziale tramite il portale di [Azure](https://portal.azure.com/)o il comando [API Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) appropriato.

* Un nuovo ciclo iniziale viene attivato da una modifica nei mapping degli attributi o nei filtri di ambito.

* Il processo di provisioning va in quarantena a causa di un alto tasso di errore e rimane in quarantena per più di quattro settimane quando verrà disabilitato automaticamente.

Per esaminare questi eventi e tutte le altre attività eseguite dal servizio di provisioning, vedere Log di [provisioning](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)di Azure AD.

Per comprendere la durata dei cicli di provisioning e monitorare lo stato del processo di provisioning, è possibile [controllare lo stato del provisioning degli utenti.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)

### <a name="gain-insights-from-reports"></a>Ottieni informazioni dettagliate dai rapporti

Azure AD può fornire [ulteriori informazioni sull'utilizzo](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) del provisioning degli utenti e sull'integrità operativa dell'organizzazione tramite i log di controllo e i report.

Gli amministratori devono controllare il rapporto di riepilogo del provisioning per monitorare l'integrità operativa del processo di provisioning. Tutte le attività eseguite dal servizio di provisioning vengono registrate nei log di controllo di Azure AD. Vedere [Esercitazione: Creazione di rapporti sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

È consigliabile assumere la proprietà e utilizzare questi report in base a una cadenza che soddisfi i requisiti dell'organizzazione. Azure AD conserva la maggior parte dei dati di controllo per 30 giorni.

### <a name="troubleshoot"></a>Risolvere i problemi

Fare riferimento ai collegamenti seguenti per risolvere eventuali problemi che potrebbero verificarsi durante il provisioning:

* [Problemi di configurazione del provisioning utenti in un'applicazione della raccolta di Azure AD](../app-provisioning/application-provisioning-config-problem.md)

* [Sincronizzare un attributo da Active Directory locale ad Azure AD per il provisioning in un'applicazione](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [Il provisioning utenti per un'applicazione della raccolta di AD Azure richiede alcune ore o tempi maggiori](../app-provisioning/application-provisioning-when-will-provisioning-finish.md)

* [Problemi di salvataggio delle credenziali dell'amministratore durante la configurazione del provisioning utenti in un'applicazione della raccolta di Azure Active Directory](../app-provisioning/application-provisioning-config-problem-storage-limit.md)

* [Nessun utente è sottoposto a provisioning per un'applicazione della raccolta di Azure AD](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [È in corso il provisioning di un set errato di utenti per un'applicazione della raccolta di Azure AD](../app-provisioning/application-provisioning-config-problem-wrong-users-provisioned.md)

### <a name="helpful-documentation"></a>Documentazione utile

* [Scrittura di espressioni per i mapping di attributiWriting expressions for attribute mappings](../app-provisioning/functions-for-customizing-application-data.md)

* [Azure AD synchronization API overview](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) (Panoramica dell'API di sincronizzazione di Azure AD)

* [Ignorare l'eliminazione degli account utente che escono dall'ambito](skip-out-of-scope-deletions.md)

* [Agente di provisioning di Azure AD Connect: cronologia delle versioniAzure AD Connect Provisioning Agent: Version release history](provisioning-agent-release-version-history.md)

#### <a name="resources"></a>Risorse

* [Fornire commenti e suggerimenti sui prodotti](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Tieniti aggiornato sulle novità di Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

* [Forum di Azure AD di overflow dello stack](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>Passaggi successivi
* [Configurare il provisioning automatico degli utenti](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Esportare o importare la configurazione di provisioning tramite l'API Microsoft Graph](../app-provisioning/export-import-provisioning-configuration.md)

* [Scrittura di espressioni per i mapping degli attributi in Azure Active DirectoryWriting expressions for attribute mappings in Azure Active directory](../app-provisioning/functions-for-customizing-application-data.md)
