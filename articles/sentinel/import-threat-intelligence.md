---
title: Importare Intelligence per le minacce in Sentinel di Azure | Microsoft Docs
description: Usare i feed di intelligence per le minacce in Sentinel di Azure per analizzare i dati, rilevare le minacce e generare avvisi ed eventi imprevisti. Visualizzare le informazioni di intelligence per le minacce con le cartelle di lavoro.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2020
ms.author: yelevin
ms.openlocfilehash: e04d7fa1f319ca3969d8acdc0235e2838bb3a88d
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90997610"
---
# <a name="import-threat-intelligence-into-azure-sentinel"></a>Importare Intelligence per le minacce in Sentinel di Azure

## <a name="introduction-to-threat-intelligence"></a>Introduzione all'Intelligence per le minacce

Il servizio di intelligence per le minacce informatico è costituito da informazioni che descrivono minacce note esistenti o potenziali per sistemi e utenti. Questo tipo di informazioni è costituito da molte forme, da report scritti che illustrano le motivazioni, l'infrastruttura e le tecniche di un particolare attore minaccia, a osservazioni specifiche di indirizzi IP, domini e hash di file associati a minacce informatiche. CTI viene usato dalle organizzazioni per fornire un contesto essenziale a attività insolite, in modo che il personale addetto alla sicurezza possa intervenire rapidamente per proteggere gli utenti e le risorse. Il CTI può essere originato da molte posizioni, ad esempio feed di dati Open Source, comunità di intelligence per le minacce, feed di intelligence commerciali e Intelligence locale raccolti nel corso delle indagini di sicurezza all'interno di un'organizzazione.

All'interno di una soluzione SIEM (Security Information and Event Management) come Azure Sentinel, la forma di CTI più utilizzata è rappresentata dagli indicatori di minaccia, spesso definiti indicatori di compromissione o di IOC. Gli indicatori di minaccia sono dati che associano osservazioni quali URL, hash di file o indirizzi IP con attività di minaccia note, ad esempio phishing, botnet o malware. Questa forma di intelligence per le minacce è spesso detta Intelligence per le minacce tattiche perché può essere applicata ai prodotti di sicurezza e all'automazione su larga scala per proteggere e rilevare potenziali minacce per un'organizzazione. In Sentinel di Azure è possibile usare gli indicatori di minaccia per individuare le attività dannose osservate nel proprio ambiente e fornire il contesto agli investigatori della sicurezza per aiutare a prendere decisioni di risposta.

È possibile integrare le funzionalità di intelligence per le minacce in Azure Sentinel mediante le attività seguenti:

- Usare **i connettori dati** per varie piattaforme ti per [importare Intelligence](./connect-threat-intelligence.md) per le minacce in Sentinel di Azure.
- Visualizzare e gestire l'Intelligence per le minacce importata nei **log** e nella nuova area di intelligence per le **minacce** di Azure Sentinel.
- Usare i modelli di regola di **analisi** incorporati per generare avvisi di sicurezza ed eventi imprevisti usando l'Intelligence per le minacce importata.
- Visualizzare le informazioni chiave sull'Intelligence per le minacce in Sentinel di Azure con la **cartella di lavoro Intelligence**per le minacce.

L'Intelligence per le minacce fornisce anche un contesto utile all'interno di altre esperienze di Sentinel di Azure, ad esempio la **caccia** e i **notebook**e anche se non trattato in questo articolo, queste esperienze vengono affrontate in [questo eccezionale post di Blog di Ian Hellen in Jupyter notebooks in Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239), che illustra l'uso di CTI nei notebook.

## <a name="azure-sentinel-data-connectors-for-threat-intelligence"></a>Connettori dati sentinella di Azure per Intelligence per le minacce

Analogamente a tutti gli altri dati degli eventi in Sentinel di Azure, gli indicatori di minaccia vengono importati usando i connettori di dati. In Sentinel di Azure sono disponibili due connettori dati specifici per gli indicatori di minaccia, **Intelligence** per le minacce e **piattaforme di intelligence**per le minacce. È possibile usare il connettore dati da solo o entrambi i connettori, a seconda della posizione in cui l'organizzazione genera indicatori di minaccia. Parliamo di ogni connettore dati.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>Aggiunta di indicatori di minaccia a Sentinel di Azure con il connettore dati delle piattaforme Intelligence per le minacce

Molte organizzazioni usano soluzioni di piattaforma di intelligence per le minacce per aggregare i feed degli indicatori di minaccia da un'ampia gamma di origini, per curare i dati all'interno della piattaforma e quindi per scegliere gli indicatori di minaccia da applicare a diverse soluzioni di sicurezza, ad esempio dispositivi di rete, soluzioni di protezione avanzata dalle minacce o SIEM, ad esempio Sentinel di Azure. Se l'organizzazione usa una soluzione TIP integrata, ad esempio MISP, anomali ThreatStream, ThreatConnect o Palo Alto Networks ' MineMeld, il **connettore di dati delle piattaforme di intelligence** per le minacce consente di usare il suggerimento per importare gli indicatori di minaccia in Azure Sentinel. Poiché il connettore funziona con l' [API tiIndicators di sicurezza di Microsoft Graph](https://docs.microsoft.com/graph/api/resources/tiindicator) a questo scopo, il connettore può essere usato anche da qualsiasi piattaforma di intelligence per le minacce personalizzata per sfruttare i vantaggi dell'API tiIndicators per inviare indicatori a Sentinel di Azure e ad altre soluzioni di sicurezza Microsoft come Defender ATP.

:::image type="content" source="media/import-threat-intelligence/threat-intel-import-path.png" alt-text="Percorso di importazione Intelligence per le minacce":::

Seguire questa procedura per importare gli indicatori di minaccia in Sentinel di Azure dalla soluzione integrata per le funzionalità di suggerimento o di intelligence per le minacce:

1. Ottenere un ID applicazione e un segreto client dalla Azure Active Directory

1. Immettere queste informazioni nella soluzione TIP o in un'applicazione personalizzata

1. Abilitare il connettore dati delle piattaforme Intelligence per le minacce in Sentinel di Azure

Ecco una descrizione dettagliata di ognuno di questi passaggi.

#### <a name="obtain-an-application-id-and-client-secret-from-your-azure-active-directory"></a>Ottenere un ID applicazione e un segreto client dalla Azure Active Directory

Se si utilizza un suggerimento o una soluzione personalizzata, l'API tiIndicators richiede alcune informazioni di base per connettersi e inviare indicatori di minaccia. Di seguito sono riportate le tre informazioni che è necessario ottenere:
- ID applicazione (client)
- ID directory (tenant)
- Segreto client

Queste informazioni provengono sempre dalla Azure Active Directory tramite un processo denominato **registrazione dell'app** che include i tre passaggi seguenti:
- Registrare un'app con Azure Active Directory
- Specificare le autorizzazioni richieste dall'app per connettersi all'API Microsoft Graph tiIndicators e inviare indicatori di minaccia
- Ottenere il consenso dell'organizzazione per concedere queste autorizzazioni all'applicazione.  

**Registrare un'applicazione con Azure Active Directory**

1. Aprire il [portale di Azure](https://portal.azure.com/) e passare al servizio **Azure Active Directory** .

1. Selezionare **registrazioni** per l'app dal menu e selezionare **nuova registrazione**.

1. Scegliere un nome per la registrazione dell'applicazione, selezionare il pulsante di opzione **single-tenant** e selezionare **Register (registra**).

    :::image type="content" source="media/import-threat-intelligence/threat-intel-register-application.png" alt-text="Registrare un'applicazione":::

1. Dalla schermata risultante copiare i valori di ID dell' **applicazione (client)** e della **Directory (tenant)** . Queste sono le prime due informazioni che saranno necessarie in seguito per configurare il suggerimento o la soluzione personalizzata per inviare indicatori di minaccia a Sentinel di Azure.

**Specificare le autorizzazioni richieste dall'applicazione**

1. Aprire il [portale di Azure](https://portal.azure.com/) e passare al servizio **Azure Active Directory** .

1. Selezionare **registrazioni** per l'app dal menu e selezionare l'app appena registrata.

1. Selezionare **autorizzazioni API** dal menu e fare clic sul pulsante **Aggiungi un'autorizzazione** .

1. Nella pagina **selezionare un'API** selezionare **Microsoft Graph** per scegliere da un elenco di Microsoft Graph autorizzazioni.

1. Quando viene richiesto **il tipo di autorizzazione richiesto dall'applicazione? selezionare le** **autorizzazioni dell'applicazione**. Questo è il tipo di autorizzazioni usate dalle applicazioni che eseguono l'autenticazione con l'ID app e i segreti dell'app (chiavi API).

1. Selezionare **ThreatIndicators. ReadWrite. OwnedBy** e selezionare **Aggiungi autorizzazioni** per aggiungere questa autorizzazione all'elenco di autorizzazioni dell'app.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-1.png" alt-text="Specificare le autorizzazioni":::

**Ottenere il consenso dell'organizzazione per concedere queste autorizzazioni**

1. Per concedere il consenso, è necessario un Azure Active Directory amministratore globale per selezionare il pulsante **concedi il consenso dell'amministratore per il tenant** nella pagina delle autorizzazioni dell'API dell'app. Se non si dispone del ruolo di amministratore globale per l'account, questo pulsante non sarà disponibile e sarà necessario chiedere a un amministratore globale dell'organizzazione di eseguire questo passaggio.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-2.png" alt-text="Fornisci il consenso":::

1. Una volta concesso il consenso all'app, verrà visualizzato un segno di spunta verde in **stato**.
 
Ora che l'app è stata registrata e sono state concesse le autorizzazioni, è possibile ottenere l'ultima cosa nell'elenco, ovvero un segreto client per l'app.

1. Aprire il [portale di Azure](https://portal.azure.com/) e passare al servizio **Azure Active Directory** .

1. Selezionare **registrazioni** per l'app dal menu e selezionare l'app appena registrata.

1. Selezionare **certificati & segreti** dal menu e fare clic sul pulsante **nuovo segreto client** per ottenere un segreto (chiave API) per l'app.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-client-secret.png" alt-text="Ottenere il segreto client":::

1. Fare clic sul pulsante **Aggiungi** e **assicurarsi di copiare il segreto client**, poiché non è possibile recuperare nuovamente questo segreto se si esce da questa pagina. Questo valore sarà necessario quando si configura un suggerimento o una soluzione personalizzata.

#### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>Immettere queste informazioni nella soluzione TIP o in un'applicazione personalizzata

Sono ora disponibili tutte e tre le informazioni necessarie per configurare il suggerimento o la soluzione personalizzata per inviare indicatori di minaccia a Sentinel di Azure. 
- ID applicazione (client)
- ID directory (tenant)
- Segreto client

Immettere questi valori nella configurazione del suggerimento integrato o della soluzione personalizzata, laddove necessario, e gli indicatori di minaccia verranno inviati tramite l'API Microsoft Graph tiIndicators destinata a Sentinel di Azure.

#### <a name="enable-the-threat-intelligence-platforms-data-connector-in-azure-sentinel"></a>Abilitare il connettore dati delle piattaforme Intelligence per le minacce in Sentinel di Azure

L'ultimo passaggio del processo di integrazione consiste nell'abilitare il connettore dati delle **piattaforme Intelligence** per le minacce in Sentinel di Azure. Questo è il passaggio che importa gli indicatori di minaccia inviati dal suggerimento o dalla soluzione personalizzata tramite l'API Microsoft Graph tiIndicators in Sentinel di Azure. Questi indicatori saranno disponibili per tutte le aree di lavoro di Sentinel di Azure per l'organizzazione. Seguire questa procedura per abilitare il connettore dati delle piattaforme Intelligence per le minacce per ogni area di lavoro:

1. Aprire il [portale di Azure](https://portal.azure.com/) e passare al servizio **Sentinel di Azure** .

1. Scegliere l' **area di lavoro** in cui si desidera importare gli indicatori di minaccia inviati dal suggerimento o dalla soluzione personalizzata.

1. Selezionare **connettori dati** dal menu, selezionare **piattaforme di intelligence** per le minacce dalla raccolta di connettori, quindi fare clic sul pulsante **Apri pagina connettore** .

1. Poiché è già stata completata la registrazione dell'app e la configurazione del suggerimento o della soluzione personalizzata per l'invio degli indicatori di minaccia, l'unico passaggio a sinistra consiste nel fare clic sul pulsante **Connetti** .

Entro pochi minuti, gli indicatori di minaccia devono iniziare a scorrere in questa area di lavoro di Azure Sentinel.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>Aggiunta di indicatori di minaccia a Sentinel di Azure con il connettore di dati Intelligence per le minacce

Lo standard di settore più ampiamente adottato per la trasmissione dell'Intelligence per le minacce è una [combinazione del formato dati Stix e del protocollo Taxii](https://oasis-open.github.io/cti-documentation/). Se l'organizzazione ottiene indicatori di minaccia da soluzioni che supportano la versione corrente di STIX/Taxis (2,0 o 2,1), è possibile usare il connettore di dati di intelligence per le **minacce** per portare gli indicatori di minaccia in Azure Sentinel. Il connettore di dati di intelligence per le minacce consente a un client di TAXI incorporato in Sentinel di Azure di importare Intelligence per le minacce dai server Taxis 2. x.

:::image type="content" source="media/import-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="Percorso di importazione Taxis":::
 
Seguire questa procedura per importare gli indicatori di minaccia formattati di STIX in Sentinel di Azure da un server Taxis:

1. Ottenere la radice dell'API del server TAXI e l'ID raccolta

1. Abilitare il connettore di dati Intelligence per le minacce in Sentinel di Azure

A questo punto è possibile esaminare in dettaglio ognuno di questi passaggi.

#### <a name="obtain-the-taxii-server-api-root-and-collection-id"></a>Ottenere la radice dell'API del server TAXI e l'ID raccolta

I server Taxis 2. x annunciano le radici API, che sono URL che ospitano raccolte di intelligence per le minacce. Spesso la radice API può essere ottenuta tramite la pagina della documentazione del provider di intelligence per le minacce che ospita il server TAXIi. Tuttavia, talvolta le uniche informazioni pubblicizzate sono un URL noto come endpoint di individuazione. In tal caso, è facile trovare la radice API usando l'endpoint di individuazione. Se si conosce già la radice dell'API del server Taxis e gli ID raccolta che si vuole usare, è possibile passare alla sezione successiva, **abilitare il connettore di dati di intelligence per le minacce in Azure Sentinel**.

Verrà ora esaminato un esempio effettivo di come usare una semplice utilità della riga di comando denominata [curl](https://en.wikipedia.org/wiki/CURL), disponibile in Windows e nella maggior parte delle distribuzioni Linux, per individuare la radice dell'API ed esplorare le raccolte di un server Taxis a partire dall'endpoint di individuazione. Per questo esempio verrà usato l'endpoint di individuazione del server [anomali limo](https://www.anomali.com/community/limo) ThreatStream taxii 2,0.

1.  Da un browser, passare all' [endpoint di individuazione del server THREATSTREAM taxis 2,0](https://limo.anomali.com/taxii) per recuperare la radice dell'API. Eseguire l'autenticazione con l'utente e la password `guest` .

    ```json
    {
        "api_roots":
        [
            "https://limo.anomali.com/api/v1/taxii2/feeds/",
            "https://limo.anomali.com/api/v1/taxii2/trusted_circles/",
            "https://limo.anomali.com/api/v1/taxii2/search_filters/"
        ],
        "contact": "info@anomali.com",
        "default": "https://limo.anomali.com/api/v1/taxii2/feeds/",
        "description": "TAXII 2.0 Server (guest)",
        "title": "ThreatStream Taxii 2.0 Server"
    }
    ```

2.  Usare l'utilità cURL e la radice API ( https://limo.anomali.com/api/v1/taxii2/feeds/) dal passaggio precedente per visualizzare l'elenco degli ID raccolta ospitati nella radice API

    ```json
    curl -u guest https://limo.anomali.com/api/v1/taxii2/feeds/collections/
    {
        "collections":
        [
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "107",
                "title": "Phish Tank"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "135",
                "title": "Abuse.ch Ransomware IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "136",
                "title": "Abuse.ch Ransomware Domains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "150",
                "title": "DShield Scanning IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "200",
                "title": "Malware Domain List - Hotlist"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "209",
                "title": "Blutmagie TOR Nodes"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "31",
                "title": "Emerging Threats C&C Server"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "33",
                "title": "Lehigh Malwaredomains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "41",
                "title": "CyberCrime"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "68",
                "title": "Emerging Threats - Compromised"
            }
        ]
    }
    ```

Sono ora disponibili tutte le informazioni necessarie per connettere Sentinel di Azure a una o più raccolte di server Taxis fornite da anomali limo.

| **Radice API** (https://limo.anomali.com/api/v1/taxii2/feeds/) | ID raccolta |
| ------------------------------------------------------------ | ------------: |
| **Serbatoio attività**                                               | 107           |
| **Indirizzi IP ransomware Abuse.ch**                                  | 135           |
| **Domini ransomware Abuse.ch**                              | 136           |
| **Indirizzi IP di analisi DShield**                                     | 150           |
| **Elenco di domini malware-hotlist**                            | 200           |
| **Nodi TOR blutmagie**                                      | 209           |
| **Minacce emergenti C&Server C**                              |  31           |
| **Malwaredomains di Lehigh**                                    |  33           |
| **Crimine informatico**                                               |  41           |
| **Minacce emergenti-compromessi**                           |  68           |
|

#### <a name="enable-the-threat-intelligence---taxii-data-connector-in-azure-sentinel"></a>Abilitare il connettore di dati Intelligence per le minacce in Sentinel di Azure

Per importare gli indicatori di minaccia in Azure Sentinel da un server TAXI, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/) e passare al servizio **Sentinel di Azure** .

1. Scegliere l' **area di lavoro** in cui si desidera importare gli indicatori di minaccia dal server Taxii.

1. Selezionare **connettori dati** dal menu, selezionare **Threat Intelligence-Taxis** dalla raccolta di connettori e fare clic sul pulsante **Apri pagina connettore** .

1. Digitare un **nome** per la raccolta di server Taxis, **l'URL radice API**, l' **ID raccolta**, il **nome utente** (se necessario) e la **password** (se necessario) e fare clic sul pulsante **Aggiungi** .

    :::image type="content" source="media/import-threat-intelligence/threat-intel-configure-taxii-servers.png" alt-text="Configurare i server TAXI":::
 
Si dovrebbe ricevere la conferma che è stata stabilita una connessione al server TAXIi ed è possibile ripetere il passaggio (4) sopra il numero di volte desiderato per connettersi a più raccolte dallo stesso o da diversi server TAXI.

## <a name="view-your-threat-indicators-in-azure-sentinel"></a>Visualizzare gli indicatori di minaccia in Sentinel di Azure

Ora che sono stati importati correttamente gli indicatori di minaccia in Sentinel di Azure usando le **piattaforme di intelligence** per le minacce e/o il connettore di dati Intelligence per le **minacce** , è possibile visualizzarli nella tabella **ThreatIntelligenceIndicator** nei **log** in cui sono archiviati tutti i dati degli eventi di Azure Sentinel. Questa tabella è la base per le query eseguite da altre funzionalità di Sentinel di Azure, ad esempio l'analisi e le cartelle di lavoro. Ecco come trovare e visualizzare gli indicatori di minaccia nella tabella **ThreatIntelligenceIndicator** .

1. Aprire il [portale di Azure](https://portal.azure.com/) e passare al servizio **Sentinel di Azure** .

1. Scegliere l' **area di lavoro** in cui sono stati importati gli indicatori di minaccia usando il connettore dati di intelligence per le minacce.

1. Selezionare **logs (registri** ) nella sezione **generale** del menu di Azure Sentinel.

1. La tabella **ThreatIntelligenceIndicator** si trova nel gruppo **Sentinel di Azure** .

1. Selezionare l'icona **Anteprima dati** (occhio) accanto al nome della tabella e selezionare il pulsante **Visualizza nell'editor di query** per eseguire una query in cui vengono visualizzati i record della tabella.

I risultati dovrebbero essere simili all'indicatore di minaccia di esempio illustrato di seguito:

:::image type="content" source="media/import-threat-intelligence/threat-intel-sample-query.png" alt-text="Dati di query di esempio":::
 
## <a name="manage-your-threat-indicators-in-the-new-threat-intelligence-area-of-azure-sentinel"></a>Gestire gli indicatori di minaccia nella nuova area di intelligence per le minacce di Azure Sentinel

Con la nuova area di intelligence per le **minacce** , accessibile dal menu Sentinel di Azure, è anche possibile visualizzare, ordinare, filtrare e cercare gli indicatori di minaccia importati senza neanche scrivere una query di **log** . Questa nuova area consente anche di creare indicatori di minaccia direttamente nell'interfaccia Sentinel di Azure, nonché di eseguire attività amministrative comuni di intelligence per le minacce, come l'assegnazione di tag agli indicatori e la creazione di nuovi indicatori correlati alle indagini di sicurezza.
Vengono ora esaminate due delle attività più comuni, ovvero la creazione di nuovi indicatori di minaccia e l'assegnazione di tag agli indicatori per semplificare il raggruppamento e il riferimento.

1. Aprire il [portale di Azure](https://portal.azure.com/) e passare al servizio **Sentinel di Azure** .

1. Scegliere l' **area di lavoro** in cui sono stati importati gli indicatori di minaccia usando il connettore dati di intelligence per le minacce.

1. Selezionare **Intelligence** per le minacce dalla sezione gestione delle minacce del menu Sentinel di Azure.

1. Selezionare il pulsante **Aggiungi nuovo** nel menu superiore della pagina.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-add-new-indicator.png" alt-text="Aggiungere un nuovo indicatore di minaccia" lightbox="media/import-threat-intelligence/threat-intel-add-new-indicator.png":::

1. Scegliere il tipo di indicatore, quindi completare i campi obbligatori contrassegnati con un asterisco rosso (*) nel pannello **nuovo indicatore** .

1. Selezionare **Applica**. L'indicatore viene aggiunto alla griglia degli indicatori e viene inviato anche alla tabella ThreatIntelligenceIndicator nei **log**.

L'assegnazione di tag agli indicatori di minaccia è un modo semplice per raggrupparli insieme per facilitarne l'individuazione. In genere, è possibile applicare un tag a indicatori correlati a un evento imprevisto specifico o a indicatori che rappresentano le minacce da un particolare attore noto o una campagna di attacco ben nota. È possibile contrassegnare gli indicatori di minaccia singolarmente oppure selezionare più indicatori e contrassegnarli tutti contemporaneamente. Di seguito è riportato un esempio di assegnazione di tag a più indicatori con ID evento imprevisto. Poiché l'assegnazione di tag è in formato libero, una procedura consigliata consiste nel creare convenzioni di denominazione standard per i tag degli indicatori di minaccia. È possibile applicare più tag a ogni indicatore.

:::image type="content" source="media/import-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="Applicare tag agli indicatori di minaccia" lightbox="media/import-threat-intelligence/threat-intel-tagging-indicators.png":::

## <a name="analytics-puts-your-threat-indicators-to-work-detecting-potential-threats"></a>Analytics inserisce gli indicatori di minaccia per il rilevamento di potenziali minacce

Gli indicatori di minaccia sono stati inseriti in Sentinel di Azure; si è visto come visualizzarli e gestirli; Ora è possibile vedere le operazioni che è possibile eseguire. Il caso d'uso più importante per gli indicatori di minaccia nelle soluzioni SIEM come Azure Sentinel è costituito dalle regole di Power Analytics.  Queste regole basate su indicatori confrontano gli eventi non elaborati delle origini dati in base agli indicatori di minaccia per rilevare le minacce alla sicurezza dell'organizzazione. In Azure Sentinel **Analytics**è possibile creare regole di analisi che vengono eseguite in base a una pianificazione e generano avvisi di sicurezza. Le regole sono basate su query, insieme a configurazioni che determinano la frequenza di esecuzione della regola, il tipo di risultati della query che genera avvisi di sicurezza e le risposte automatiche da attivare quando vengono generati gli avvisi.

Sebbene sia sempre possibile creare nuove regole di analisi da zero, Azure Sentinel fornisce un set di modelli di regole predefiniti, creati da Microsoft Security Engineers, che è possibile usare così com'è o modificare in base alle esigenze. È possibile identificare facilmente i modelli di regole che usano gli indicatori di minaccia, perché tutti sono denominati a partire da "**ti map**...". Tutti questi modelli di regole funzionano in modo analogo, con l'unica differenza è il tipo di indicatori di minaccia utilizzati (dominio, posta elettronica, hash file, indirizzo IP o URL) e il tipo di evento in base al quale eseguire la corrispondenza. Ogni modello elenca le origini dati necessarie per il funzionamento della regola, quindi è possibile visualizzare immediatamente se sono già stati importati gli eventi necessari in Sentinel di Azure.

Verrà ora esaminato uno di questi modelli di regole e verrà illustrato come abilitare e configurare la regola per generare avvisi di sicurezza usando gli indicatori di minaccia importati in Sentinel di Azure. Per questo esempio, verrà usato il modello di regola denominato **ti map IP Entity in AzureActivity**. Questa regola corrisponderà a qualsiasi indicatore di minaccia di tipo indirizzo IP con tutti gli eventi di attività di Azure. Quando viene rilevata una corrispondenza, viene generato un **avviso** , nonché un **evento imprevisto** corrispondente per l'analisi da un team operativo di sicurezza. Questa regola di analisi funzionerà correttamente solo se è stato abilitato uno o entrambi i connettori dati di intelligence per le **minacce** (per importare gli indicatori di minaccia) e il connettore dati **attività di Azure** (per importare gli eventi a livello di sottoscrizione di Azure).

1. Aprire il [portale di Azure](https://portal.azure.com/) e passare al servizio **Sentinel di Azure** .

1. Scegliere l' **area di lavoro** in cui sono stati importati gli indicatori di minaccia usando i connettori dati di intelligence per le **minacce** e i dati delle attività di Azure usando il connettore dati **attività**

1. Selezionare **Analytics** dalla sezione **configurazione** del menu Sentinel di Azure.

1. Selezionare la scheda **modelli di regola** per visualizzare l'elenco dei modelli delle regole di analisi disponibili.

1. Passare alla regola che consente di eseguire il **mapping dell'entità IP a AzureActivity** e assicurarsi di avere connesso tutte le origini dati necessarie, come illustrato di seguito.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-required-data-sources.png" alt-text="Origini dati obbligatorie":::

1. Selezionare questa regola e selezionare il pulsante **Crea regola** . Verrà visualizzata una procedura guidata per configurare la regola. Completare le impostazioni qui e selezionare il pulsante **Avanti: impostare la logica della regola >** .

    :::image type="content" source="media/import-threat-intelligence/threat-intel-create-analytics-rule.png" alt-text="Crea regola di analisi":::

1. La parte relativa alla logica della regola della procedura guidata contiene:
    - Query che verrà utilizzata nella regola.

    - Mapping di entità, che indicano ad Azure Sentinel come riconoscere entità quali account, indirizzi IP e URL, in modo che gli **eventi imprevisti** e le **indagini** apprendano come usare i dati in tutti gli avvisi di sicurezza generati da questa regola.

    - Pianificazione per l'esecuzione di questa regola.

    - Il numero di risultati della query necessari prima che venga generato un avviso di sicurezza.

    Le impostazioni predefinite del modello sono:
    - Eseguire una volta all'ora.

    - Trova la corrispondenza degli indicatori di minaccia degli indirizzi IP dalla tabella **ThreatIntelligenceIndicator** con tutti gli indirizzi IP trovati nell'ultima ora di eventi della tabella **AzureActivity** .

    - Genera un avviso di sicurezza se i risultati della query sono maggiori di zero, vale a dire se vengono trovate corrispondenze.

È possibile lasciare le impostazioni predefinite o modificare una di queste per soddisfare i requisiti. Al termine, selezionare il pulsante **Next: Automatic response >**

1. Questo passaggio della procedura guidata consente di configurare qualsiasi automazione che si vuole attivare quando viene generato un avviso di sicurezza da questa regola di analisi. L'automazione in Sentinel di Azure viene eseguita usando **PlayBook**basati su app per la logica di Azure. Per altre informazioni, vedere questa [esercitazione: configurare le risposte automatiche alle minacce in Sentinel di Azure](./tutorial-respond-threats-playbook.md). Per questo esempio, è sufficiente selezionare il pulsante **Next: Review >** per continuare.

1. L'ultimo passaggio convalida le impostazioni nella regola. Quando si è pronti per abilitare la regola, selezionare il pulsante **Crea** .

Ora che è stata abilitata la regola di analisi, è possibile trovare la regola abilitata nella scheda **regole attive** della sezione **Analytics** di Azure Sentinel. È possibile modificare, abilitare, disabilitare, duplicare o eliminare la regola attiva da questa posizione. La nuova regola viene eseguita immediatamente dopo l'attivazione e da allora in poi verrà eseguita in base alla pianificazione definita.

Secondo le impostazioni predefinite, ogni volta che la regola viene eseguita in base alla pianificazione, tutti i risultati trovati genereranno un avviso di sicurezza. Gli avvisi di sicurezza in Azure Sentinel possono essere visualizzati nella sezione **logs** di Azure Sentinel, nella tabella **SecurityAlert** nel gruppo **Sentinel di Azure** .

In Sentinel di Azure, gli avvisi generati dalle regole di analisi generano anche gli eventi imprevisti di sicurezza che si possono trovare in **eventi** imprevisti in **gestione minacce** nel menu di Azure Sentinel. Gli eventi imprevisti sono gli elementi che i team operativi di sicurezza eseguiranno la valutazione e analizzeranno per determinare le azioni di risposta appropriate. È possibile trovare informazioni dettagliate in questa [esercitazione: esaminare gli eventi imprevisti con Sentinel di Azure](./tutorial-investigate-cases.md).

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>Le cartelle di lavoro forniscono informazioni approfondite sull'Intelligence per le minacce

Infine, è possibile usare una cartella di lavoro di Azure Sentinel per visualizzare le informazioni chiave sull'Intelligence per le minacce in Sentinel di Azure ed è possibile personalizzare facilmente le cartelle di lavoro in base alle esigenze aziendali.
Viene ora illustrato come trovare la cartella di lavoro di intelligence per le minacce fornita in Sentinel di Azure. verrà inoltre illustrato come apportare modifiche alla cartella di lavoro per personalizzarla.

1. Aprire il [portale di Azure](https://portal.azure.com/) e passare al servizio **Sentinel di Azure** .

1. Scegliere l' **area di lavoro** in cui sono stati importati gli indicatori di minaccia usando il connettore dati di intelligence per le minacce.

1. Selezionare **cartelle di lavoro** dalla sezione **gestione minacce** del menu Sentinel di Azure.

1. Passare alla cartella di lavoro intitolata Intelligence per le **minacce** e verificare di avere i dati nella tabella **ThreatIntelligenceIndicator** , come illustrato di seguito.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-verify-data.png" alt-text="Verificare i dati":::
 
1. Selezionare il pulsante **Salva** e scegliere un percorso di Azure in cui archiviare la cartella di lavoro. Questo passaggio è obbligatorio se si intende modificare la cartella di lavoro in qualsiasi modo e salvare le modifiche.

1. Selezionare ora il pulsante **Visualizza cartella di lavoro salvata** per aprire la cartella di lavoro per la visualizzazione e la modifica.

1. Verranno ora visualizzati i grafici predefiniti forniti dal modello. Verranno ora apportate alcune modifiche a uno dei grafici. Selezionare il pulsante **modifica** nella parte superiore della pagina per passare alla modalità di modifica per la cartella di lavoro.

1. Aggiungere un nuovo grafico di indicatori di minaccia per tipo di minaccia. Scorrere fino alla fine della pagina e selezionare Aggiungi query.

1. Aggiungere il testo seguente alla casella di testo della **query di log dell'area di lavoro log Analytics** :
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. Nell'elenco a discesa **visualizzazione** selezionare **grafico a barre**.

1. Selezionare il pulsante **modifica completato** . È stato creato un nuovo grafico per la cartella di lavoro.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-bar-chart.png" alt-text="Grafico a barre":::

Le cartelle di lavoro di forniscono dashboard interattivi avanzati che offrono informazioni dettagliate su tutti gli aspetti di Azure Sentinel. È possibile eseguire molte operazioni con le cartelle di lavoro e, sebbene i modelli forniti siano un ottimo punto di partenza, è probabile che si vogliano approfondire e personalizzare questi modelli o creare nuovi dashboard che combinano diverse origini dati in modo da poter visualizzare i dati in modi univoci. Poiché le cartelle di lavoro di Azure Sentinel sono basate sulle cartelle di lavoro di monitoraggio di Azure, è già disponibile una documentazione completa e molti altri modelli. Un ottimo punto di partenza è questo articolo su come [creare report interattivi con le cartelle di lavoro di monitoraggio di Azure](../azure-monitor/platform/workbooks-overview.md). 

[In GitHub](https://github.com/microsoft/Application-Insights-Workbooks) è disponibile anche una ricca community di cartelle di lavoro di monitoraggio di Azure, in cui è possibile scaricare modelli aggiuntivi e contribuire ai propri modelli.

## <a name="next-steps"></a>Passaggi successivi
In questo documento sono state illustrate le funzionalità di intelligence per le minacce di Sentinel di Azure e il nuovo pannello Intelligence per le minacce. Per istruzioni pratiche sull'uso delle funzionalità di intelligence per le minacce di Azure Sentinel, vedere gli articoli seguenti:

- [Connettere i dati di intelligence](./connect-threat-intelligence.md) per le minacce a Sentinel di Azure.
- Creare avvisi [predefiniti](./tutorial-detect-threats-built-in.md) o [personalizzati](./tutorial-detect-threats-custom.md) e [analizzare](./tutorial-investigate-cases.md) gli eventi imprevisti in Sentinel di Azure.