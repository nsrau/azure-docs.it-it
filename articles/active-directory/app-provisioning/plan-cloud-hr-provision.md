---
title: Pianificare l'applicazione HR cloud per il provisioning degli utenti di Azure Active DirectoryPlan cloud HR application to Azure Active Directory user provisioning
description: Questo articolo descrive il processo di distribuzione dell'integrazione dei sistemi HR cloud, ad esempio Workday e SuccessFactors, con Azure Active Directory.This article describes the deployment process of integrating cloud HR systems, such as Workday and SuccessFactors, with Azure Active Directory. L'integrazione di Azure AD con il sistema HR cloud si traduce in un sistema completo di gestione del ciclo di vita delle identità.
services: active-directory
author: martincoetzer
manager: CelesteDG
tags: azuread
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: martinco
ms.reviewer: arvindha
ms.openlocfilehash: 28abe2dfa5a1a13ba09e20202180cb5e47d94072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522433"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Pianificare l'applicazione HR cloud per il provisioning degli utenti di Azure Active DirectoryPlan cloud HR application to Azure Active Directory user provisioning

Storicamente, il personale IT si è affidato a metodi manuali per creare, aggiornare ed eliminare i dipendenti. Hanno usato metodi come il caricamento di file CSV o script personalizzati per sincronizzare i dati dei dipendenti. Questi processi di provisioning sono soggetti a errori, non sicuri e difficili da gestire.

Per gestire i cicli di vita delle identità di dipendenti, fornitori o lavoratori contingenti, il servizio di provisioning degli utenti di [Azure Active Directory (Azure AD)](../app-provisioning/user-provisioning.md) offre l'integrazione con le applicazioni HR (Human Resources) basate su cloud. Esempi di applicazioni includono Workday o SuccessFactors.Examples of applications include Workday or SuccessFactors.

Azure AD usa questa integrazione per abilitare i flussi di lavoro dell'applicazione HR cloud (app) seguenti:Azure AD uses this integration to enable the following cloud HR application (app) workflows:

- **Effettuare il provisioning degli utenti in Active Directory:** Effettua il provisioning di set di utenti selezionati da un'app CLOUD HR in uno o più domini di Active Directory.
- **Effettuare il provisioning degli utenti solo cloud in Azure AD:Provision cloud-only users to Azure AD:** Negli scenari in cui Active Directory non viene usato, eseguire il provisioning degli utenti direttamente dall'app CLOUD HR ad Azure AD.
- **Esegui write back nell'app CLOUD HR:** Scrivere di nuovo gli indirizzi di posta elettronica e gli attributi del nome utente da Azure AD nell'app CLOUD HR.

> [!NOTE]
> Questo piano di distribuzione illustra come distribuire i flussi di lavoro dell'app HR cloud con il provisioning degli utenti di Azure AD. Per informazioni su come distribuire il provisioning automatico degli utenti alle app SaaS (Information User Provisioning), vedere Pianificare una distribuzione automatica del provisioning degli [utenti.](https://aka.ms/deploymentplans/provisioning)

## <a name="enabled-hr-scenarios"></a>Scenari HR abilitati

Il servizio di provisioning degli utenti di Azure AD consente l'automazione dei seguenti scenari di gestione del ciclo di vita delle identità basati su risorse umane:The Azure AD user provisioning service enables automation of the following HR-based identity lifecycle management scenarios:

- **Assunzione** di nuovi dipendenti: Quando un nuovo dipendente viene aggiunto all'app CLOUD HR, viene creato automaticamente un account utente in Active Directory e Azure AD con l'opzione per riscrivere l'indirizzo di posta elettronica e gli attributi del nome utente nell'app HR cloud.
- **Aggiornamento dell'attributo e** del profilo dei dipendenti: Quando un record dipendente, ad esempio nome, titolo o responsabile, viene aggiornato nell'app CLOUD HR, il relativo account utente viene aggiornato automaticamente in Active Directory e Azure AD.
- **Cessazioni dei dipendenti:** Quando un dipendente viene terminato nell'app cloud HR, il relativo account utente viene disabilitato automaticamente in Active Directory e Azure AD.
- **Assunzioni dei dipendenti:** Quando un dipendente viene riassuntato nell'app CLOUD HR, il suo account precedente può essere riattivato o rieseguito automaticamente in Active Directory e Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>A chi è più adatta questa integrazione?

L'integrazione dell'app CLOUD HR con il provisioning degli utenti di Azure AD è ideale per le organizzazioni che:The cloud HR app integration with Azure AD user provisioning is ideald for organizations that:

- Desideri una soluzione predefinita basata su cloud per il provisioning degli utenti delle risorse umane cloud.
- Richiedere il provisioning diretto degli utenti dall'app CLOUD HR ad Active Directory o Azure AD.
- Richiedere agli utenti di eseguire il provisioning utilizzando i dati ottenuti dall'app CLOUD HR.
- Richiedere l'aggiunta, lo spostamento e l'abbandono degli utenti in una o più foreste, domini e aree utente di Active Directory in base solo alle informazioni sulle modifiche rilevate nell'app HR cloud.
- Usare Office 365 per la posta elettronica.

## <a name="learn"></a>Informazioni

Il provisioning degli utenti crea una base per una governance continua delle identità. Migliora la qualità dei processi aziendali che si basano su dati di identità autorevoli.

### <a name="terms"></a>Termini

In questo articolo vengono utilizzati i termini seguenti:

- **Sistema di origine:** il repository degli utenti di cui Azure AD effettua il provisioning. Un esempio è un'app cloud HR, ad esempio Workday o SuccessFactors.An example is a cloud HR app such as Workday or SuccessFactors.
- Sistema di **destinazione:** il repository degli utenti a cui Azure AD esegue il provisioning. Esempi sono Active Directory, Azure AD, Office 365 o altre app SaaS.
- **Processo Joiners-Movers-Leavers:** termine utilizzato per nuove assunzioni, trasferimenti e terminazioni tramite un'app CLOUD HR come sistema di record. Il processo viene completato quando il servizio esegue correttamente il provisioning degli attributi necessari al sistema di destinazione.

### <a name="key-benefits"></a>Vantaggi principali

Questa funzionalità del provisioning IT basato sulle risorse umane offre i seguenti vantaggi aziendali significativi:

- **Aumentare la produttività:** È ora possibile automatizzare l'assegnazione di account utente e licenze di Office 365 e fornire l'accesso ai gruppi chiave. L'automazione delle assegnazioni consente ai nuovi assunti di accedere immediatamente ai propri strumenti di lavoro e aumenta la produttività.
- **Gestire i rischi:** È possibile aumentare la sicurezza automatizzando le modifiche in base allo stato dei dipendenti o alle appartenenze ai gruppi con i dati che vengono riprodotti dall'app HR cloud. L'automazione delle modifiche garantisce che le identità utente e l'accesso alle app chiave vengano aggiornati automaticamente quando gli utenti passano la transizione o escono dall'organizzazione.
- **Affrontare la conformità e la governance:** Azure AD supporta i log di controllo nativi per le richieste di provisioning degli utenti eseguite dalle app dei sistemi di origine e di destinazione. Con il controllo, è possibile tenere traccia di chi ha accesso alle app da un'unica schermata.
- **Gestire i costi:** Il provisioning automatico riduce i costi evitando inefficienze ed errori umani associati al provisioning manuale. Riduce la necessità di soluzioni di provisioning degli utenti sviluppate su misura create nel tempo utilizzando piattaforme legacy e obsolete.

### <a name="licensing"></a>Gestione delle licenze

Per configurare l'app HR cloud per l'integrazione di provisioning degli utenti di Azure AD, è necessaria una licenza di Azure AD Premium valida e una licenza per l'app HR cloud, ad esempio Workday o SuccessFactors.To configure the cloud HR app to Azure AD user provisioning integration, you require a valid [Azure AD Premium license](https://azure.microsoft.com/pricing/details/active-directory/) and a license for the cloud HR app, such as Workday or SuccessFactors.

È inoltre necessaria una licenza di sottoscrizione valida di Azure AD Premium P1 o superiore per ogni utente che verrà originato dall'app HR cloud e di cui è stato eseguito il provisioning in Active Directory o Azure AD. Qualsiasi numero improprio di licenze possedute nell'app cloud HR potrebbe causare errori durante il provisioning degli utenti.

### <a name="prerequisites"></a>Prerequisiti

- Accesso di amministratore globale di Azure AD per configurare l'agente di provisioning di Azure AD Connect.Azure AD global administrator access to configure the Azure AD Connect provisioning agent.
- Un'istanza di test e produzione dell'app CLOUD HR.
- Autorizzazioni di amministratore nell'app CLOUD HR per creare un utente di integrazione del sistema e apportare modifiche ai dati dei dipendenti a scopo di test.
- Per il provisioning degli utenti in Active Directory, è necessario un server che esegue Windows Server 2012 o versione successiva con runtime di .NET 4.7.1 per ospitare l'agente di [provisioning di Azure AD Connect.](https://go.microsoft.com/fwlink/?linkid=847801)
- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) per la sincronizzazione degli utenti tra Active Directory e Azure AD.

### <a name="training-resources"></a>Risorse per la formazione

| **Risorse** | **Collegamento e descrizione** |
|:-|:-|
| Video | [Che cos'è il provisioning degli utenti in Active Azure Directory?](https://youtu.be/_ZjARPpI6NI) |
| | [Come distribuire il provisioning degli utenti in Active Azure DirectoryHow to deploy user provisioning in Active Azure Directory](https://youtu.be/pKzyts6kfrw) |
| Esercitazioni | [Elenco di esercitazioni su come integrare le app SaaS con Azure ADList of tutorials on how to integrate SaaS apps with Azure AD](../saas-apps/tutorial-list.md) |
| | [Esercitazione: Configurare Workday per il provisioning utenti automatico](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| Domande frequenti | [Provisioning utenti automatizzato](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Provisioning da Workday ad Azure AD](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Architettura della soluzione

L'esempio seguente descrive l'architettura della soluzione di provisioning degli utenti end-to-end per ambienti ibridi comuni e include:The following example describes the end-to-end user provisioning solution architecture for common hybrid environments and includes:

- **Flusso di dati HR autorevoli dall'app CLOUD HR ad Active Directory.** In questo flusso, l'evento HR (processo Joiners-Movers-Leavers) viene avviato nel tenant dell'app HR cloud. Il servizio di provisioning di Azure AD e l'agente di provisioning di Azure AD Connect effettuano il provisioning dei dati utente dal tenant dell'app HR cloud in Active Directory. A seconda dell'evento, potrebbe portare a creare, aggiornare, abilitare e disabilitare le operazioni in Active Directory.
- **Eseguire la sincronizzazione con Azure AD e scrivere la posta elettronica e il nome utente da Active Directory locale all'app HR cloud.** Dopo l'aggiornamento degli account in Active Directory, viene sincronizzato con Azure AD tramite Azure AD Connect.After the accounts are updated in Active Directory, it's synced with Azure AD through Azure AD Connect. Gli indirizzi di posta elettronica e gli attributi del nome utente possono essere riscritti nel tenant dell'app HR cloud.

![Diagramma del flusso di lavoro](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Descrizione del flusso di lavoro

Nel diagramma sono indicati i seguenti passaggi chiave:  

1. **Il team HR** esegue le transazioni nel tenant dell'app HR cloud.
2. Il servizio di provisioning di **Azure AD** esegue i cicli pianificati dal tenant dell'app HR cloud e identifica le modifiche che devono essere elaborate per la sincronizzazione con Active Directory.
3. **Il servizio** di provisioning di Azure AD Richiede l'agente di provisioning di Azure AD Connect con un payload di richiesta che contiene le operazioni di creazione, aggiornamento, abilitazione e disabilitazione dell'account di Active Directory.Azure AD provisioning service invokes the Azure AD Connect provisioning agent with a request payload that contains Active Directory account create, update, enable, and disable operations.
4. **L'agente** di provisioning di Azure AD Connect usa un account del servizio per gestire i dati dell'account di Active Directory.Azure AD Connect provisioning agent uses a service account to manage Active Directory account data.
5. **Azure AD Connect** esegue la [sincronizzazione](../hybrid/how-to-connect-sync-whatis.md) delta per eseguire il pull degli aggiornamenti in Active Directory.Azure AD Connect runs delta sync to pull updates in Active Directory.
6. **Gli** aggiornamenti di Active Directory vengono sincronizzati con Azure AD.
7. Il servizio di provisioning di **Azure AD** esegue il writeback dell'attributo di posta elettronica e del nome utente da Azure AD al tenant dell'app HR cloud.

## <a name="plan-the-deployment-project"></a>Pianificare il progetto di distribuzione

Considerare le esigenze dell'organizzazione mentre si determina la strategia per questa distribuzione nell'ambiente.

### <a name="engage-the-right-stakeholders"></a>Coinvolgi le parti interessate giuste

Quando i progetti tecnologici falliscono, in genere lo fanno a causa di aspettative non corrispondenti su impatto, risultati e responsabilità. Per evitare queste insidie, [assicurati di coinvolgere le parti interessate giuste.](https://aka.ms/deploymentplans) Assicurarsi inoltre che i ruoli delle parti interessate nel progetto siano ben compresi. Documentare le parti interessate e il loro input e responsabilità del progetto.

Includere un rappresentante dell'organizzazione HR in grado di fornire input sui processi aziendali HR esistenti e sull'identità dei lavoratori, oltre ai requisiti di elaborazione dei dati dei processi.

### <a name="plan-communications"></a>Pianificare le comunicazioni

La comunicazione è fondamentale per il successo di ogni nuovo servizio. Comunica in modo proattivo con gli utenti quando e come cambierà la loro esperienza. Far loro sapere come ottenere supporto se si verificano problemi.

### <a name="plan-a-pilot"></a>Pianificare un progetto pilota

L'integrazione dei processi aziendali HR e dei flussi di lavoro di identità dall'app HR cloud ai sistemi di destinazione richiede una notevole quantità di convalida dei dati, trasformazione dei dati, pulizia dei dati e test end-to-end prima di poter distribuire la soluzione nell'ambiente di produzione.

Eseguire la configurazione iniziale in un [ambiente pilota](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) prima di ridimensionarla a tutti gli utenti nell'ambiente di produzione.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Seleziona le app del connettore di provisioning DELLE risorse umane cloud

Per facilitare il provisioning dei flussi di lavoro di Azure AD tra l'app CLOUD HR e Active Directory, è possibile aggiungere più app connettore di provisioning dalla raccolta di app di Azure AD:To facilitate Azure AD provisioning workflows between the cloud HR app and Active Directory, you can add multiple provisioning connector apps from the Azure AD app gallery:

- **App Cloud HR per**il provisioning degli utenti di Active Directory: questa app connettore di provisioning facilita il provisioning degli account utente dall'app HR cloud a un singolo dominio di Active Directory. Se sono presenti più domini, è possibile aggiungere un'istanza di questa app dalla raccolta di app di Azure AD per ogni dominio di Active Directory in cui è necessario effettuare il provisioning.
- **Cloud HR app per**il provisioning degli utenti di Azure AD: mentre Azure AD Connect è lo strumento che deve essere usato per sincronizzare gli utenti di Active Directory con Azure AD, questa app connettore di provisioning può essere usata per facilitare il provisioning degli utenti solo cloud dall'app CLOUD HR a un singolo tenant di Azure AD.
- **Write-back dell'app Cloud HR:** questa app connettore di provisioning facilita il writeback degli indirizzi di posta elettronica dell'utente da Azure AD all'app CLOUD HR.

Ad esempio, l'immagine seguente elenca le app del connettore Workday disponibili nella raccolta di app di Azure AD.

![Raccolta di app del portale di Azure Active DirectoryAzure Active Directory portal app gallery](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>Diagramma di flusso delle decisioni

Utilizzare il diagramma di flusso delle decisioni seguente per identificare le app di provisioning delle risorse umane cloud rilevanti per lo scenario.

![Diagramma di flusso delle decisioni](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>Progettare la topologia di distribuzione dell'agente di provisioning di Azure AD Connect

L'integrazione di provisioning tra l'app CLOUD HR e Active Directory richiede quattro componenti:

- Tenant dell'app Cloud HR
- App connettore di provisioning
- Agente di provisioning di Azure AD ConnectAzure AD Connect provisioning agent
- Dominio di Active Directory

La topologia di distribuzione dell'agente di provisioning di Azure AD Connect dipende dal numero di tenant di app HR cloud e domini figlio di Active Directory che si intende integrare. Se si dispone di più domini di Active Directory, dipende dal fatto che i domini di Active Directory siano contigui o [disgiunti](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/disjoint-namespace).

In base alla decisione, scegliere uno degli scenari di distribuzione:

- Tenant dell'app HR cloud singolo-> domini figlio singoli o multipli di Active Directory in una foresta trusted
- Singolo tenant di app HR cloud: > di destinazione di più domini figlio in una foresta di Active Directory disgiunta

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>Tenant dell'app HR cloud singolo-> domini figlio singoli o multipli di Active Directory in una foresta trusted

Si consiglia la seguente configurazione di produzione:

|Requisito|Recommendation|
|:-|:-|
|Numero di agenti di provisioning di Azure AD Connect da distribuire|Due (per disponibilità elevata e failover)
|Numero di app connettore di provisioning da configurare|Un'app per dominio figlio|
|Host server per l'agente di provisioning di Azure AD Connect|Windows 2012 R2 con linea di vista ai controller di dominio di Active Directory geolocalizzati</br>Può coesistere con il servizio Azure AD ConnectCan coexist with Azure AD Connect service|

![Flusso verso agenti locali](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>Singolo tenant di app HR cloud: > di destinazione di più domini figlio in una foresta di Active Directory disgiunta

Questo scenario prevede il provisioning degli utenti dall'app CLOUD HR a domini in foreste di Active Directory disgiunte.

Si consiglia la seguente configurazione di produzione:

|Requisito|Recommendation|
|:-|:-|
|Numero di agenti di provisioning di Azure AD Connect da distribuire in localeNumber of Azure AD Connect provisioning agents to deploy on-premises|Due per foresta di Active Directory disgiunta|
|Numero di app connettore di provisioning da configurare|Un'app per dominio figlio|
|Host server per l'agente di provisioning di Azure AD Connect|Windows 2012 R2 con linea di vista ai controller di dominio di Active Directory geolocalizzati</br>Può coesistere con il servizio Azure AD ConnectCan coexist with Azure AD Connect service|

![Singolo tenant app HR cloud disgiunto foresta di Active Directory](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Requisiti dell'agente di provisioning di Azure AD ConnectAzure AD Connect provisioning agent requirements

L'app cloud HR per la soluzione di provisioning degli utenti di Active Directory richiede la distribuzione di uno o più agenti di provisioning di Azure AD Connect nei server che eseguono Windows 2012 R2 o versione successiva. I server devono disporre di almeno 4 GB di RAM e di runtime di .NET 4.7.1. Assicurarsi che il server host disponga dell'accesso di rete al dominio di Active Directory di destinazione.

Per preparare l'ambiente locale, la configurazione guidata dell'agente di provisioning di Azure AD Connect registra l'agente con il tenant di Azure AD, [apre le porte](../manage-apps/application-proxy-add-on-premises-application.md#open-ports), consente [l'accesso agli URL](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)e supporta la configurazione del proxy HTTPS in [uscita.](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)

L'agente di provisioning utilizza un account di servizio per comunicare con i domini di Active Directory. Prima di installare l'agente, creare un account di servizio in Utenti e computer di Active Directory che soddisfi i requisiti seguenti:

- Una password che non scade
- Autorizzazioni di controllo delegate per la lettura, la creazione, l'eliminazione e la gestione degli account utente

È possibile selezionare i controller di dominio che devono gestire le richieste di provisioning. Se si dispone di più controller di dominio distribuiti geograficamente, installare l'agente di provisioning nello stesso sito dei controller di dominio preferiti. Questo posizionamento migliora l'affidabilità e le prestazioni della soluzione end-to-end.

Per la disponibilità elevata, è possibile distribuire più di un agente di provisioning di Azure AD Connect.For high availability, you can deploy more than one Azure AD Connect provisioning agent. Registrare l'agente per gestire lo stesso set di domini di Active Directory locali.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Pianificare i filtri di ambito e il mapping degli attributiPlan scoping filters and attribute mapping

Quando si abilita il provisioning dall'app CLOUD HR ad Active Directory o Azure AD, il portale di Azure controlla i valori degli attributi tramite il mapping degli attributi.

### <a name="define-scoping-filters"></a>Definire i filtri di ambitoDefine scoping filters

Usare i filtri di [ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) per definire le regole basate sugli attributi che determinano quali utenti devono essere sottoposti a provisioning dall'app CLOUD HR ad Active Directory o Azure AD.

Quando si avvia il processo Joiner, raccogliere i requisiti seguenti:When you initiate the Joiners process, gather the following requirements:

- L'app cloud HR viene utilizzata per coinvolgere sia i dipendenti che i lavoratori contingenti?
- Si prevede di usare l'app per le risorse umane cloud per il provisioning degli utenti di Azure AD per gestire sia i dipendenti che i lavoratori contingenti?
- Si prevede di implementare l'app HR cloud per il provisioning degli utenti di Azure AD solo per un sottoinsieme degli utenti dell'app HR cloud? Un esempio potrebbe essere solo dipendenti.

A seconda dei requisiti, quando si configurano i mapping degli attributi, è possibile impostare il campo **Ambito oggetto** di origine per selezionare i set di utenti nell'app HR cloud che devono essere nell'ambito per il provisioning in Active Directory. Per altre informazioni, vedere l'esercitazione sull'app CLOUD HR per i filtri di ambito di uso comune.

### <a name="determine-matching-attributes"></a>Determinare gli attributi corrispondenti

Con il provisioning, è possibile abbinare gli account esistenti tra il sistema di origine e quello di destinazione. Quando si integra l'app HR cloud con il servizio di provisioning di Azure AD, è possibile [configurare](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings) il mapping degli attributi per determinare quali dati utente devono essere trasportati dall'app HR cloud ad Active Directory o Azure AD.

Quando si avvia il processo Joiner, raccogliere i requisiti seguenti:When you initiate the Joiners process, gather the following requirements:

- Qual è l'ID univoco in questa app CLOUD HR usata per identificare ogni utente?
- Dal punto di vista del ciclo di vita dell'identità, come si gestiscono i reliquie? Le riassunzioni mantengono i vecchi ID dipendente?
- Elaborano le assunzioni con data futura e create account Active Directory per loro in anticipo?
- Dal punto di vista del ciclo di vita dell'identità, come si gestisce il dipendente alla conversione contingente dei lavoratori o in altro modo?
- Gli utenti convertiti mantengono i vecchi account di Active Directory o ne ottengono di nuovi?

A seconda dei requisiti, Azure AD supporta il mapping diretto tra attributi e attributi fornendo valori costanti o [scrivendo espressioni per i mapping degli attributi.](../app-provisioning/functions-for-customizing-application-data.md) Questa flessibilità offre il controllo finale di ciò che viene popolato nell'attributo dell'app di destinazione. È possibile usare [l'API Microsoft Graph](../app-provisioning/export-import-provisioning-configuration.md) e Graph Explorer per esportare i mapping e lo schema degli attributi di provisioning degli utenti in un file JSON e importarlo nuovamente in Azure AD.

Per impostazione predefinita, l'attributo nell'app CLOUD HR che rappresenta l'ID dipendente univoco viene utilizzato come attributo corrispondente *mappato all'attributo univoco in Active Directory.* Ad esempio, nello scenario dell'app Workday, l'attributo **WorkerID** **Workday** viene mappato all'attributo **employeeID** di Active Directory.

È possibile impostare più attributi corrispondenti e assegnare la precedenza corrispondente. Vengono valutati in base alla precedenza corrispondente. Quando viene rilevata una corrispondenza la valutazione degli attributi corrispondenti termina.

È inoltre possibile [personalizzare i mapping di attributi predefiniti,](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types)ad esempio la modifica o l'eliminazione di mapping di attributi esistenti. È inoltre possibile creare nuovi mapping di attributi in base alle esigenze aziendali. Per altre informazioni, vedere l'esercitazione sull'app HR cloud (ad esempio [Workday](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)) per un elenco di attributi personalizzati di cui eseguire il mapping.

### <a name="determine-user-account-status"></a>Determinare lo stato dell'account utente

Per impostazione predefinita, l'app del connettore di provisioning esegue il mapping dello stato del profilo utente HR allo stato dell'account utente in Active Directory o Azure AD per determinare se abilitare o disabilitare l'account utente.

Quando si avvia il processo Joiners-Leavers, raccogliere i requisiti seguenti.

| Process | Requisiti |
| - | - |
| **Falegnami** | Dal punto di vista del ciclo di vita dell'identità, come si gestiscono i reliquie? Le riassunzioni mantengono i vecchi ID dipendente? |
| | Elaborano le assunzioni con data futura e create account Active Directory per loro in anticipo? Questi account vengono creati in uno stato abilitato o disabilitato? |
| | Dal punto di vista del ciclo di vita dell'identità, come si gestisce il dipendente alla conversione contingente dei lavoratori o in altro modo? |
| | Gli utenti convertiti mantengono i vecchi account di Active Directory o ne ottengono di nuovi? |
| **Lieviti** | Le terminazioni vengono gestite in modo diverso per i dipendenti e i lavoratori contingenti in Active Directory? |
| | Quali date di validità vengono considerate per l'elaborazione della cessazione dell'utente? |
| | In che modo le conversioni dei dipendenti e dei lavoratori contingenti influiscono sugli account di Active Directory esistenti? |
| | Come si elabora l'operazione Rescind in Active Directory? Le operazioni di revoca devono essere gestite se le assunzioni datate future vengono create in Active Directory come parte del processo Joiner. |

A seconda dei requisiti, è possibile personalizzare la logica di mapping usando le espressioni di [Azure AD](../app-provisioning/functions-for-customizing-application-data.md) in modo che l'account di Active Directory sia abilitato o disabilitato in base a una combinazione di punti dati.

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>Mappare l'app HR cloud agli attributi utente di Active Directory

Ogni app CLOUD HR viene fornita con l'app CLOUD HR predefinita per i mapping di Active Directory.

Quando si avvia il processo Joiners-Movers-Leavers, raccogliere i requisiti seguenti.

| Process | Requisiti |
| - | - |
| **Falegnami** | Il processo di creazione degli account di Active Directory è manuale, automatizzato o parzialmente automatizzato? |
| | Si prevede di propagare gli attributi personalizzati dall'app cloud HR ad Active Directory? |
| **Motori** | Quali attributi si desidera elaborare ogni volta che viene eseguita un'operazione Movers nell'app CLOUD HR? |
| | Eseguire validazioni di attributi specifiche al momento degli aggiornamenti degli utenti? In caso affermativo, fornire i dettagli. |
| **Lieviti** | Le terminazioni vengono gestite in modo diverso per i dipendenti e i lavoratori contingenti in Active Directory? |
| | Quali date di validità vengono considerate per l'elaborazione della cessazione dell'utente? |
| | In che modo le conversioni dei lavoratori dipendenti e dipendenti contingenti influiscono sugli account di Active Directory esistenti? |

A seconda dei requisiti, è possibile modificare i mapping per soddisfare gli obiettivi di integrazione. Per altre informazioni, vedere l'esercitazione specifica sull'app HR cloud (ad esempio [Workday](../saas-apps/workday-inbound-tutorial.md#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)) per un elenco di attributi personalizzati di cui eseguire il mapping.

### <a name="generate-a-unique-attribute-value"></a>Generare un valore di attributo univoco

Quando si avvia il processo Joiners, potrebbe essere necessario generare valori di attributo univoci quando si impostano attributi come CN, samAccountName e l'UPN, che dispone di vincoli univoci.

La funzione di Azure AD [SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) valuta ogni regola e quindi controlla il valore generato per verificare l'univocità nel sistema di destinazione. Per un esempio, vedere [Generare un valore univoco per l'attributo userPrincipalName (UPN).](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute)

> [!NOTE]
> Questa funzione è attualmente supportata solo per il provisioning degli utenti da Workday ad Active Directory. Non può essere usato con altre app di provisioning.

### <a name="configure-active-directory-ou-container-assignment"></a>Configurare l'assegnazione del contenitore dell'unità organizzativa di Active Directory

È un requisito comune inserire gli account utente di Active Directory in contenitori in base a Business Unit, sedi e reparti. Quando si avvia un processo Movers e se viene apportata una modifica all'organizzazione di supervisione, potrebbe essere necessario spostare l'utente da un'unità organizzativa a un'altra in Active Directory.

Utilizzare la funzione [Switch()](../app-provisioning/functions-for-customizing-application-data.md#switch) per configurare la logica di business per l'assegnazione dell'unità organizzativa e mapparla all'attributo di Active Directory **parentDistinguishedName**.

Ad esempio, se si desidera creare utenti in unità organizzative in base all'attributo HR **Municipality**, è possibile utilizzare l'espressione seguente:

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Con questa espressione, se il valore di Municipalità è Dallas, Austin, Seattle o Londra, l'account utente verrà creato nell'unità organizzativa corrispondente. Se non c'è corrispondenza, l'account viene creato nell'unità organizzativa predefinita.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Pianificare il recapito delle password di nuovi account utente

Quando si avvia il processo Joiners, è necessario impostare e consegnare una password temporanea dei nuovi account utente. Con il provisioning degli utenti da monitoraggio e risorse umane di Azure AD, è possibile implementare la funzionalità di [reimpostazione della password self-service](../authentication/quickstart-sspr.md) (SSPR) di Azure AD per l'utente il primo giorno.

SSPR è un mezzo semplice per gli amministratori IT per consentire agli utenti di reimpostare le password o sbloccare i propri account. È possibile eseguire il provisioning dell'attributo **Mobile Number** dall'app cloud HR ad Active Directory e sincronizzarlo con Azure AD. Dopo che l'attributo **Numero di cellulare** è in Azure AD, è possibile abilitare SSPR per l'account dell'utente. Poi, il primo giorno, il nuovo utente può utilizzare il numero di cellulare registrato e verificato per l'autenticazione.

## <a name="plan-for-initial-cycle"></a>Pianificare il ciclo iniziale

Quando il servizio di provisioning di Azure AD viene eseguito per la prima volta, esegue un [ciclo iniziale](../app-provisioning/how-provisioning-works.md#initial-cycle) sull'app HR cloud per creare uno snapshot di tutti gli oggetti utente nell'app HR cloud. Il tempo impiegato per i cicli iniziali dipende direttamente dal numero di utenti presenti nel sistema di origine. Il ciclo iniziale per alcuni tenant di app HR cloud con oltre 100.000 utenti può richiedere molto tempo.

Per i tenant di app HR cloud di **grandi dimensioni (>30.000 utenti),** eseguire il ciclo iniziale in fasi progressive. Avviare gli aggiornamenti incrementali solo dopo aver verificato che gli attributi corretti vengono impostati in Active Directory per scenari di provisioning degli utenti diversi. Segui l'ordine qui.

1. Eseguire il ciclo iniziale solo per un set limitato di utenti impostando il [filtro di ambito](#plan-scoping-filters-and-attribute-mapping).
2. Verificare il provisioning degli account di Active Directory e i valori degli attributi impostati per gli utenti selezionati per la prima esecuzione. Se il risultato soddisfa le aspettative, espandere il filtro di ambito per includere progressivamente più utenti e verificare i risultati per la seconda esecuzione.

Dopo essere soddisfatti dei risultati del ciclo iniziale per gli utenti di test, avviare [gli aggiornamenti incrementali.](../app-provisioning/how-provisioning-works.md#incremental-cycles)

## <a name="plan-testing-and-security"></a>Pianificare test e sicurezza

In ogni fase della distribuzione dal progetto pilota iniziale all'abilitazione del provisioning degli utenti, assicurarsi di testare che i risultati siano quelli previsti e di controllare i cicli di provisioning.

### <a name="plan-testing"></a>Pianificare i test

Dopo aver configurato l'app HR cloud per il provisioning degli utenti di Azure AD, eseguire test case per verificare se questa soluzione soddisfa i requisiti dell'organizzazione.

|Scenari|Risultati previsti|
|:-|:-|
|Il nuovo dipendente viene assunto nell'app CLOUD HR.| - Viene eseguito il provisioning dell'account utente in Active Directory.</br>- L'utente può accedere alle app del dominio di Active Directory ed eseguire le azioni desiderate.</br>- Se è configurata la sincronizzazione di Azure AD Connect, l'account utente viene creato anche in Azure AD.- If Azure AD Connect sync is configured, the user account also gets created in Azure AD.
|L'utente viene terminato nell'app CLOUD HR.|- L'account utente è disabilitato in Active Directory.</br>- L'utente non può accedere ad tutte le app aziendali protette da Active Directory.
|L'organizzazione di supervisione utente viene aggiornata nell'app cloud HR.|In base al mapping degli attributi, l'account utente si sposta da un'unità organizzativa a un'altra in Active Directory.|
|HR aggiorna il responsabile dell'utente nell'app cloud HR.|Il campo manager in Active Directory viene aggiornato per riflettere il nome del nuovo responsabile.|
|HR riaffiora un dipendente in un nuovo ruolo.|Il comportamento dipende dalla configurazione dell'app CLOUD HR per generare gli ID dei dipendenti:Behavior depends on how the cloud HR app is configured to generate employee IDs:</br>- Se il vecchio ID dipendente viene riutilizzato per una riassunzione, il connettore abilita l'account di Active Directory esistente per l'utente.</br>- Se la nuova assunzione ottiene un nuovo ID dipendente, il connettore crea un nuovo account di Active Directory per l'utente.|
|HR converte il dipendente in un addetto al lavoro o viceversa.|Viene creato un nuovo account di Active Directory per la nuova persona e l'account precedente viene disabilitato alla data di validità della conversione.|

Usa i risultati precedenti per determinare come eseguire la transizione dell'implementazione del provisioning automatico degli utenti nell'ambiente di produzione in base alle sequenze temporali stabilite.

> [!TIP]
> Utilizzare tecniche come la riduzione dei dati e lo scrubbing dei dati quando si aggiorna l'ambiente di test con i dati di produzione per rimuovere o mascherare i dati personali sensibili per soddisfare gli standard di privacy e sicurezza. 

### <a name="plan-security"></a>Pianificare la sicurezza

È comune che una revisione della sicurezza sia necessaria come parte della distribuzione di un nuovo servizio. Se è necessaria o non è stata eseguita una revisione della sicurezza, vedere i numerosi [white paper](https://www.microsoft.com/download/details.aspx?id=36391) di Azure AD che forniscono una panoramica dell'identità come servizio.

### <a name="plan-rollback"></a>Pianificare il rollback

L'implementazione del provisioning degli utenti HR cloud potrebbe non funzionare come desiderato nell'ambiente di produzione. In tal caso, i passaggi di rollback seguenti possono essere utili per ripristinare uno stato valido noto precedente.

1. Esaminare il report di riepilogo del [provisioning](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) e i log di [provisioning](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) per determinare quali operazioni non corrette sono state eseguite sugli utenti o i gruppi interessati. Per altre informazioni sul report di riepilogo del provisioning e sui log, vedere Gestire il [provisioning degli utenti dell'app HR cloud.](#manage-your-configuration)
2. L'ultimo stato valido noto degli utenti o dei gruppi interessati può essere determinato tramite i log di controllo del provisioning o esaminando i sistemi di destinazione (Azure AD o Active Directory).
3. Collaborare con il proprietario dell'app per aggiornare gli utenti o i gruppi interessati direttamente nell'app usando gli ultimi valori di stato valido noti.

## <a name="deploy-the-cloud-hr-app"></a>Distribuire l'app cloud HR

Scegli l'app CLOUD HR in linea con i requisiti della tua soluzione.

**Workday:** per importare i profili dei lavoratori da Workday ad Active Directory e Azure AD, vedere [Esercitazione: Configurare Workday per](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment)il provisioning automatico degli utenti. Facoltativamente, è possibile riscrivere l'indirizzo di posta elettronica e il nome utente in Workday.

## <a name="manage-your-configuration"></a>Gestire la configurazione

Azure AD può fornire ulteriori informazioni sull'utilizzo del provisioning degli utenti e sull'integrità operativa dell'organizzazione tramite i log di controllo e i report.

### <a name="gain-insights-from-reports-and-logs"></a>Ottieni informazioni dettagliate da report e log

Dopo un [ciclo iniziale](../app-provisioning/how-provisioning-works.md#initial-cycle)riuscito, il servizio di provisioning di Azure AD continua a eseguire aggiornamenti incrementali back-to-back a tempo indeterminato, a intervalli definiti nelle esercitazioni specifiche per ogni app, fino a quando non si verifica uno degli eventi seguenti:

- Il servizio viene arrestato manualmente. Un nuovo ciclo iniziale viene attivato tramite il portale di [Azure](https://portal.azure.com/) o il comando [API Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) appropriato.
- Un nuovo ciclo iniziale viene attivato a causa di una modifica nei mapping degli attributi o nei filtri di ambito.
- Il processo di provisioning viene messo in quarantena a causa di un tasso di errore elevato. Rimane in quarantena per più di quattro settimane, quando viene disattivato automaticamente.

Per esaminare questi eventi e tutte le altre attività eseguite dal servizio di provisioning, [vedere come esaminare i log e ottenere report sull'attività](../app-provisioning/check-status-user-account-provisioning.md)di provisioning.

#### <a name="azure-monitor-logs"></a>Log di Monitoraggio di Azure

Tutte le attività eseguite dal servizio di provisioning vengono registrate nei log di controllo di Azure AD. È possibile instradare i log di controllo di Azure AD ai log di Monitoraggio di Azure per un'ulteriore analisi. Con i log di Monitoraggio di Azure (nota anche come area di lavoro log Analytics), è possibile eseguire query sui dati per trovare eventi, analizzare tendenze ed eseguire correlazioni tra varie origini dati. Guardare questo [video](https://youtu.be/MP5IaCTwkQg) per informazioni sui vantaggi dell'uso dei log di Monitoraggio di Azure per i log di Azure AD in scenari utente pratici.

Installare le [visualizzazioni](../reports-monitoring/howto-install-use-log-analytics-views.md) di analisi dei log per i log attività di Azure AD per ottenere l'accesso [ai report predefiniti](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) relativi agli eventi di provisioning nell'ambiente.

Per altre informazioni, vedere come [analizzare i log](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)attività di Azure AD con i log di Monitoraggio di Azure.For more information, see how to analyze the Azure AD activity logs with your Azure Monitor logs .

### <a name="manage-personal-data"></a>Gestire dati personali

L'agente di provisioning di Azure AD Connect installato nel server Windows crea log nel log eventi di Windows che potrebbero contenere dati personali a seconda dell'app HR cloud per i mapping degli attributi di Active Directory.The Azure AD Connect provisioning agent installed on the Windows server creates logs in the Windows event log that might contain personal data depending on your cloud HR app to Active Directory attribute mappings. Per rispettare gli obblighi di privacy degli utenti, impostare un'attività pianificata di Windows per cancellare il registro eventi e assicurarsi che nessun dato venga mantenuto oltre le 48 ore.

Il servizio di provisioning di Azure AD non genera report, non esegue analisi o fornisce informazioni dettagliate oltre i 30 giorni perché il servizio non archivia, elabora o mantiene dati oltre 30 giorni.

### <a name="troubleshoot"></a>Risolvere i problemi

Per risolvere eventuali problemi che potrebbero verificarsi durante il provisioning, vedere gli articoli seguenti:To troubleshoot any issues that might turn up during provisioning, see the following articles:

- [Problemi di configurazione del provisioning utenti in un'applicazione della raccolta di Azure AD](application-provisioning-config-problem.md)
- [Sincronizzare un attributo da Active Directory locale ad Azure AD per il provisioning in un'applicazione](user-provisioning-sync-attributes-for-mapping.md)
- [Il provisioning utenti per un'applicazione della raccolta di AD Azure richiede alcune ore o tempi maggiori](application-provisioning-when-will-provisioning-finish.md)
- [Problemi di salvataggio delle credenziali dell'amministratore durante la configurazione del provisioning utenti in un'applicazione della raccolta di Azure Active Directory](application-provisioning-config-problem-storage-limit.md)
- [Nessun utente è sottoposto a provisioning per un'applicazione della raccolta di Azure AD](application-provisioning-config-problem-no-users-provisioned.md)
- [È in corso il provisioning di un set errato di utenti per un'applicazione della raccolta di Azure AD](application-provisioning-config-problem-wrong-users-provisioned.md)
- [Configurazione di Visualizzatore eventi di Windows per la risoluzione dei problemi dell'agente](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Configurazione dei log di controllo del portale di Azure per la risoluzione dei problemi di servizio](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Riconoscimento di log per operazioni di creazione per l'account utente di AD](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [Informazioni sui log per le operazioni di aggiornamento di ManagerUnderstanding logs for Manager update operations](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [Risoluzione degli errori più comuni](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Passaggi successivi

- [Scrittura di espressioni per i mapping di attributiWriting expressions for attribute mappings](functions-for-customizing-application-data.md)
- [Azure AD synchronization API overview](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) (Panoramica dell'API di sincronizzazione di Azure AD)
- [Ignorare l'eliminazione degli account utente che escono dall'ambito](skip-out-of-scope-deletions.md)
- [Agente di provisioning di Azure AD Connect: cronologia delle versioniAzure AD Connect Provisioning Agent: Version release history](provisioning-agent-release-version-history.md)
