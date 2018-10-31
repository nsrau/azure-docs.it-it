---
title: Fattori che influenzano le prestazioni di Azure AD Connect
description: Questo documento illustra come diversi fattori influiscono sul motore di provisioning di Azure AD Connect. Questi fattori consentono alle organizzazioni di pianificare la distribuzione di Azure AD Connect per assicurarsi che soddisfi i requisiti di sincronizzazione.
services: active-directory
author: billmath
manager: mtillman
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2018
ms.reviewer: martincoetzer
ms.author: billmath
ms.openlocfilehash: cdca1b31f9b6cf10113dc0dba70b8f8991bafa2b
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093970"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>Fattori che influenzano le prestazioni di Azure AD Connect

Azure AD Connect esegue la sincronizzazione di Active Directory e Azure AD. Questo server è un componente fondamentale dello spostamento delle identità utente nel cloud. I fattori principali che influiscono sulle prestazioni di Azure AD Connect sono:

| **Fattore di progettazione**| **Definizione** |
|:-|-|
| Topologia| Distribuzione dei componenti e degli endpoint che devono essere gestiti da Azure AD Connect in rete. |
| Scalabilità| Numero di oggetti, ad esempio utenti, gruppi e unità organizzative, che devono essere gestiti da Azure AD Connect. |
| Hardware| Hardware (fisico o virtuale) per Azure AD Connect e capacità di prestazioni dipendenti di ogni componente hardware, incluse CPU, memoria, rete e configurazione del disco rigido. |
| Configurazione| Come Azure AD Connect elabora le directory e le informazioni. |
| Caricamento| Frequenza delle modifiche degli oggetti. I carichi possono variare nell'arco di un'ora, un giorno o una settimana. A seconda del componente, potrebbe essere necessario eseguire la progettazione per un picco di carico o un carico medio. |

Questo documento descrive i fattori che influenzano le prestazioni del motore di provisioning di Azure AD Connect. Le organizzazioni complesse o di grandi dimensioni (organizzazioni che effettuano il provisioning di più di 100.000 oggetti) possono usare le raccomandazioni per ottimizzare l'implementazione di Azure AD Connect, se si verificano i problemi relativi alle prestazioni indicati in questo documento. Questo documento non fornisce informazioni sugli altri componenti di Azure AD Connect, ad esempio [Azure AD Connect Health](how-to-connect-health-agent-install.md), e sugli agenti.

> [!IMPORTANT]
> Microsoft non supporta la modifica o l'uso del servizio Azure AD Connect al di fuori delle azioni descritte in modo formale. Ognuna di queste azioni potrebbe provocare uno stato incoerente o non supportato del sevizio di sincronizzazione Azure AD Connect. Microsoft pertanto non offre il supporto tecnico per distribuzioni di questo tipo.

## <a name="azure-ad-connect-component-factors"></a>Fattori dei componenti di Azure AD Connect

Il diagramma seguente mostra un'architettura generale del motore di provisioning che si connette a una singola foresta, anche se sono supportate più foreste. Questa architettura illustra l'interazione tra i vari componenti.

![AzureADConnentInternal](media/plan-connect-performance-factors/AzureADConnentInternal.png)

Il motore di provisioning si connette a ogni foresta di Active Directory e ad Azure AD. Il processo di lettura delle informazioni da ogni directory viene chiamato importazione. L'esportazione è invece l'aggiornamento delle directory dal motore di provisioning. La sincronizzazione valuta le regole relative a come gli oggetti si sposteranno all'interno del motore di provisioning. Per un approfondimento, è possibile vedere [Servizio di sincronizzazione Azure AD Connect: informazioni sull'architettura](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture).

Per consentire la sincronizzazione da Active Directory ad Azure AD, Azure AD Connect usa le aree, le regole e i processi di staging seguenti:

* **Spazio connettore**: gli oggetti di ogni directory connessa, ovvero le directory effettive, vengono inseriti temporaneamente qui prima di poter essere elaborati dal motore di provisioning. Azure AD ha il proprio spazio connettore e ogni foresta a cui ci si connette ha il proprio spazio connettore.
* **Metaverse**: gli oggetti da sincronizzare vengono creati qui in base alle regole di sincronizzazione. Gli oggetti devono esistere nel metaverse prima di poter popolare gli oggetti e gli attributi per le altre directory connesse. Esiste un solo metaverse.
* **Regole di sincronizzazione**: determinano quali oggetti verranno creati (proiettati) o connessi (aggiunti) negli oggetti del metaverse. Le regole di sincronizzazione determinano anche quali valori degli attributi verranno copiati o trasformati in e dalle directory.
* **Profili di esecuzione**: aggregano i passaggi del processo di copia degli oggetti e dei valori degli attributi in base alle regole di sincronizzazione tra le aree di staging e le directory connesse.

Esistono profili di esecuzione diversi per ottimizzare le prestazioni del motore di provisioning. La maggior parte delle organizzazioni userà le pianificazioni e i profili di esecuzione predefiniti per le normali operazioni, ma per alcune organizzazioni potrebbe essere necessario [modificare la pianificazione](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) o attivare altri profili di esecuzione per gestire situazioni non comuni. Sono disponibili i seguenti profili di esecuzione:

### <a name="initial-sync-profile"></a>Profilo di sincronizzazione iniziale

Il profilo di sincronizzazione iniziale è il processo di lettura delle directory connesse, ad esempio una foresta di Active Directory, per la prima volta. Esegue quindi un'analisi su tutte le voci nel database del motore di sincronizzazione. Il ciclo iniziale creerà nuovi oggetti in Azure AD e il completamento richiederà più tempo se le foreste di Active Directory sono di grandi dimensioni. La sincronizzazione iniziale include i passaggi seguenti:

1. Importazione completa su tutti i connettori
2. Sincronizzazione completa su tutti i connettori
3. Esportazione su tutti i connettori

### <a name="delta-sync-profile"></a>Profilo di sincronizzazione delta

Per ottimizzare il processo di sincronizzazione, questo profilo di esecuzione elabora solo le modifiche (creazione, eliminazione e aggiornamento) degli oggetti nelle directory connesse, dall'ultimo processo di sincronizzazione. Per impostazione predefinita, il profilo di sincronizzazione delta viene eseguito ogni 30 minuti. Le organizzazioni devono cercare di mantenere questo intervallo di tempo al di sotto dei 30 minuti, per assicurarsi che Azure AD sia aggiornato. Per monitorare l'integrità di Azure AD Connect, usare l'[agente di monitoraggio dell'integrità](how-to-connect-health-sync.md) per visualizzare eventuali problemi relativi al processo. Il ciclo di sincronizzazione delta include i passaggi seguenti:

1. Importazione differenziale su tutti i connettori
2. Sincronizzazione delta su tutti i connettori
3. Esportazione su tutti i connettori

Uno scenario di sincronizzazione delta di un'organizzazione aziendale tipico è il seguente:

- ~1% degli oggetti viene eliminato
- ~1% degli oggetti viene creato
- ~5% degli oggetti viene modificato

La frequenza di modifica può variare a seconda di quanto spesso l'organizzazione aggiorna gli utenti in Active Directory. Ad esempio, frequenze di modifica più elevate possono essere determinate dalle assunzioni e dalle riduzioni stagionali della forza lavoro.

### <a name="full-sync-profile"></a>Profilo di sincronizzazione completa

Un ciclo di sincronizzazione completa è necessario se è stata apportata una delle modifiche seguenti alla configurazione:



- È stato esteso l'ambito degli oggetti o degli attributi da importare dalle directory connesse, come quando si aggiunge un dominio o un'unità organizzativa all'ambito di importazione.
- Sono state apportate modifiche alle regole di sincronizzazione, come quando si crea una nuova regola per popolare la posizione di un utente in Azure AD da extension_attribute3 in Active Directory. Questo aggiornamento richiede che il motore di provisioning riesamini tutti gli utenti esistenti per aggiornare le relative posizioni e poter applicare la modifica in futuro.

In un ciclo di sincronizzazione completa sono incluse le operazioni seguenti:

1. Importazione completa su tutti i connettori
2. Sincronizzazione completa/delta su tutti i connettori
3. Esportazione su tutti i connettori

> [!NOTE]
> È necessaria un'attenta pianificazione quando si eseguono aggiornamenti in blocco di molti oggetti in Active Directory o in Azure AD. Con gli aggiornamenti in blocco, il processo di sincronizzazione delta richiederà più tempo per l'importazione, perché vengono modificati molti oggetti. Le importazioni possono richiedere molto tempo anche se l'aggiornamento in blocco non influisce sul processo di sincronizzazione. Ad esempio, l'assegnazione di licenze a molti utenti in Azure AD determinerà un ciclo di importazione lungo da Azure AD, ma non comporterà modifiche agli attributi in Active Directory.

### <a name="synchronization"></a>Sincronizzazione

Il runtime del processo di sincronizzazione ha le caratteristiche di prestazioni seguenti:

* La sincronizzazione è a thread singolo, vale a dire che il motore di provisioning non esegue elaborazioni parallele dei profili di esecuzione delle directory connesse, degli oggetti o degli attributi.
* La durata dell'importazione cresce in modo lineare con il numero di oggetti da sincronizzare. Se ad esempio l'importazione di 10.000 oggetti richiede 10 minuti, quella di 20.000 oggetti richiederà circa 20 minuti sullo stesso server.
* Anche l'esportazione è lineare.
* La sincronizzazione aumenterà in modo esponenziale in base al numero di oggetti con riferimenti ad altri oggetti. Le appartenenze ai gruppi e i gruppi annidati hanno l'impatto maggiore sulle prestazioni, perché i membri fanno riferimento agli oggetti utente o ad altri gruppi. Questi riferimenti devono trovarsi e fare riferimento agli oggetti effettivi nel metaverse per completare il ciclo di sincronizzazione.

### <a name="filtering"></a>Filtri

Le dimensioni della topologia di Active Directory da importare è il fattore principale che influenza le prestazioni e il tempo complessivo richiesto dai componenti interni del motore di provisioning.

È consigliabile usare i [filtri](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) per ridurre gli oggetti a quelli da sincronizzare, evitando così di elaborare ed esportare in Azure AD oggetti non necessari. Sono disponibili le seguenti tecniche di filtro, in ordine di preferenza:



- **Filtro basato su dominio**: usare questa opzione per selezionare domini specifici da sincronizzare con Azure AD. È necessario aggiungere e rimuovere domini dalla configurazione del motore di sincronizzazione quando si apportano modifiche all'infrastruttura locale dopo aver installato il servizio di sincronizzazione Azure AD Connect.
- **Filtro di unità organizzativa**: usa le unità organizzative per specificare come destinazione determinati oggetti nei domini di Active Directory per il provisioning in Azure AD. Il filtro di unità organizzative è il secondo meccanismo di filtro consigliato perché usa semplici query con ambito LDAP per importare un subset ridotto di oggetti da Active Directory.
- **Filtro di attributi per oggetto**: usa i valori degli attributi sugli oggetti per determinare se effettuare il provisioning in Azure AD di un oggetto specifico in Active Directory. Il filtro degli attributi è ideale per ottimizzare i filtri, quando il filtro dei domini e delle unità organizzative non soddisfa i requisiti specifici dei filtri. Il filtro degli attributi non riduce il tempo di importazione, ma può ridurre i tempi di sincronizzazione ed esportazione.
- **Filtro basato su gruppo**: usa l'appartenenza ai gruppi per determinare se effettuare il provisioning degli oggetti in Azure AD. Il filtro basato su gruppo è adatto solo a situazioni di test e non è consigliato per l'ambiente di produzione, a causa dell'ulteriore overhead necessario per controllare l'appartenenza ai gruppi durante il ciclo di sincronizzazione.

Molti [oggetti sezionatore](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects) persistenti nello spazio connettore di Active Directory possono prolungare i tempi di sincronizzazione, perché il motore di provisioning deve rivalutare la possibile connessione di ogni oggetto sezionatore nel ciclo di sincronizzazione. Per ovviare a questo problema, prendere in considerazione una delle indicazioni seguenti:



- Inserire gli oggetti sezionatore al di fuori dell'ambito di importazione usando il filtro del dominio o dell'unità organizzativa.
- Proiettare/Aggiungere gli oggetti nel metaverse e impostare l'attributo [cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) su True, per impedire il provisioning di questi oggetti nello spazio connettore di Azure AD.

> [!NOTE]
> L'applicazione di filtri a un numero eccessivo di oggetti può confondere gli utenti o creare problemi relativi alle autorizzazioni dell'applicazione. In un'implementazione online di Exchange ibrida, ad esempio, gli utenti con cassette postali locali visualizzeranno più utenti nell'elenco indirizzi globale degli utenti con cassette postali in Exchange online. In altri casi, un utente può decidere di concedere l'accesso in un'app cloud a un altro utente che non fa parte dell'ambito del set di oggetti filtrati.

### <a name="attribute-flows"></a>Flussi di attributi

I flussi di attributi sono i processi di copia o trasformazione dei valori degli attributi degli oggetti da una directory connessa a un'altra directory connessa. Vengono definiti come parte delle regole di sincronizzazione. Quando ad esempio il numero di telefono di un utente viene modificato in Active Directory, verrà aggiornato anche in Azure AD. Le organizzazioni possono [modificare i flussi di attributi](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-change-the-configuration) in base ai vari requisiti. È consigliabile copiare i flussi di attributi esistenti prima di modificarli.

I semplici reindirizzamenti, ad esempio il trasferimento di un valore di attributo a un attributo diverso, non hanno un impatto sostanziale sulle prestazioni. Un esempio di un reindirizzamento è il trasferimento di un numero di cellulare in Active Directory al numero di telefono dell'ufficio in Azure AD.

La trasformazione dei valori degli attributi può influire sulle prestazioni del processo di sincronizzazione. La trasformazione dei valori degli attributi include la modifica, la riformattazione, la concatenazione o la sottrazione dei valori degli attributi.

Le organizzazioni possono impedire il passaggio di determinati attributi in Azure AD, ma ciò non influirà sulle prestazioni del motore di provisioning.

> [!NOTE]
> Non eliminare i flussi di attributi indesiderati nelle regole di sincronizzazione. È più consigliabile disabilitarli, perché le regole eliminate vengono ricreate durante gli aggiornamenti di Azure AD Connect.

## <a name="azure-ad-connect-dependency-factors"></a>Fattori di dipendenza di Azure AD Connect

Le prestazioni di Azure AD Connect dipendono dalle prestazioni delle directory connesse in cui vengono eseguite le importazioni e le esportazioni, ad esempio dalle dimensioni dell'istanza di Active Directory da importare o dalla latenza di rete per il servizio Azure AD. Il database SQL usato dal motore di provisioning influisce anche sulle prestazioni complessive del ciclo di sincronizzazione.

### <a name="active-directory-factors"></a>Fattori di Active Directory

Come accennato in precedenza, il numero di oggetti da importare influenza notevolmente le prestazioni. L'[hardware e i prerequisiti per Azure AD Connect](how-to-connect-install-prerequisites.md) determinano livelli di hardware specifici in base alle dimensioni della distribuzione. Azure AD Connect supporta solo topologie specifiche, come illustrato in [Topologie per Azure AD Connect](plan-connect-topologies.md). Non sono disponibili consigli e ottimizzazioni relativi alle prestazioni per le topologie non supportate.

Assicurarsi che il server di Azure AD Connect soddisfi i requisiti hardware in base alle dimensioni dell'istanza di Active Directory da importare. Una connettività di rete lenta o non efficiente tra il server di Azure AD Connect e i controller di dominio di Active Directory può rallentare l'importazione.

### <a name="azure-ad-factors"></a>Fattori di Azure AD

Azure AD usa la limitazione delle richieste per proteggere il servizio cloud da attacchi Denial of Service (DoS). Azure AD ha attualmente un limite massimo di 7.000 operazioni di scrittura ogni 5 minuti (84.000 all'ora). È ad esempio possibile limitare le operazioni seguenti:



- Esportazione di Azure AD Connect ad Azure AD.
- Applicazioni o script di PowerShell che aggiornano Azure AD direttamente, anche in background, ad esempio le appartenenze a gruppi dinamiche.
- Utenti che aggiornano i record di identità, ad esempio quando eseguono la registrazione per MFA o la reimpostazione password self-service.
- Operazioni nell'interfaccia utente grafica.

Pianificare le attività di distribuzione e manutenzione, per assicurarsi che il ciclo del servizio di sincronizzazione Azure AD Connect non sia interessato dalla limitazione delle richieste. Un numero elevato di assunzioni e la conseguente creazione di migliaia di identità utente, ad esempio, può causare aggiornamenti delle appartenenze a gruppi dinamiche, delle assegnazioni di licenze e delle registrazioni di reimpostazione della password self-service. Si consiglia di distribuire queste operazioni di scrittura su più ore o alcuni giorni.

### <a name="sql-database-factors"></a>Fattori del database SQL

Le dimensioni della topologia di Active Directory di origine influiranno sulle prestazioni del database SQL. Seguire i [requisiti hardware](how-to-connect-install-prerequisites.md) per il database di SQL Server e tenere presente quanto segue:



- Le organizzazioni con più di 100.000 utenti possono ridurre le latenze di rete usando lo stesso server come posizione condivisa per il database SQL e il motore di provisioning.
- A causa dei requisiti di input e output (I/O) elevati su disco del processo di sincronizzazione, usare unità SSD per il database SQL del motore di provisioning per ottenere risultati ottimali. Se non è possibile, prendere in considerazione le configurazioni RAID 0 o RAID 1.
- Non eseguire preventivamente una sincronizzazione completa perché potrebbe causare una varianza non necessaria e rallentare i tempi di risposta.

## <a name="conclusion"></a>Conclusioni

Per ottimizzare le prestazioni dell'implementazione di Azure AD Connect, tenere presente quanto segue:



- Usare la [configurazione hardware consigliata](how-to-connect-install-prerequisites.md) in base alle dimensioni dell'implementazione per il server di Azure AD Connect.
- Quando si aggiorna Azure AD Connect in distribuzioni su larga scala, valutare l'uso del [metodo di migrazione swing](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version#swing-migration), per assicurarsi che il tempo di inattività sia minimo e l'affidabilità sia ottimale. 
- Usare un'unità SSD per il database SQL per ottenere prestazioni di scrittura ottimali.
- Filtrare l'ambito di Active Directory per includere solo gli oggetti di cui è necessario effettuare il provisioning in Azure AD, in base a dominio, unità organizzativa o attributo.
- Se è necessario modificare le regole del flusso di attributi predefinite, copiare prima una regola, quindi modificare la copia e disabilitare la regola originale. Ricordare di eseguire di nuovo una sincronizzazione completa.
- Pianificare il tempo sufficiente per il profilo di esecuzione della sincronizzazione completa iniziale.
- Cercare di completare il ciclo di sincronizzazione delta in 30 minuti. Se il profilo di sincronizzazione delta non viene completato in 30 minuti, modificare la frequenza di sincronizzazione predefinita per poter includere un ciclo di sincronizzazione delta completo.
- Monitorare l'[integrità del servizio di sincronizzazione Azure AD Connect](how-to-connect-health-agent-install.md) in Azure AD.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
