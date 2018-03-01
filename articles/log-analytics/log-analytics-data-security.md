---
title: Sicurezza dei dati di Log Analytics | Documentazione Microsoft
description: Informazioni su come Log Analytics garantisce la privacy degli utenti e ne protegge i dati.
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: a33bb05d-b310-4f2c-8f76-f627e600c8e7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: magoedte
ms.openlocfilehash: bfd9b3302c73e50408cdd68b25317630aa087d7f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="log-analytics-data-security"></a>Sicurezza dei dati di Log Analytics
Scopo di questo documento è fornire informazioni specifiche su Azure Log Analytics che integrino le informazioni disponibili nel [Centro protezione di Azure](../security/security-microsoft-trust-center.md).  

Questo articolo illustra il modo in cui i dati vengono raccolti, elaborati e protetti da Log Analytics. È possibile usare gli agenti per connettersi al servizio Web, usare System Center Operations Manager per raccogliere dati operativi o recuperare dati da Diagnostica di Azure per usarli in Log Analytics. 

Il servizio Log Analytics gestisce i dati basati sul cloud in modo sicuro usando i metodi seguenti:

* Separazione dei dati
* Conservazione dei dati
* Sicurezza fisica
* Gestione di eventi imprevisti
* Conformità
* Certificazioni degli standard di sicurezza

Contattare Microsoft per qualsiasi domanda, suggerimento o problema riguardo a qualsiasi aspetto delle informazioni riportate in questo articolo, compresi i criteri di sicurezza. A questo scopo, visitare la pagina relativa alle [opzioni di supporto di Azure](http://azure.microsoft.com/support/options/).

## <a name="data-segregation"></a>Separazione dei dati
Dopo essere stati inseriti nel servizio Log Analytics, i dati vengono mantenuti separati logicamente in ogni componente del servizio. Tutti i dati vengono contrassegnati in base all'area di lavoro. Tale contrassegno persiste per tutto il ciclo di vita dei dati e viene applicato a ogni livello del servizio. I dati sono archiviati in un database dedicato nel cluster di archiviazione nell'area selezionata.

## <a name="data-retention"></a>Conservazione dei dati
I dati di ricerca nei log indicizzati vengono archiviati e conservati in base al piano tariffario. Per altre informazioni, vedere [Prezzi di Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

Microsoft conserva i dati secondo i termini del [contratto di sottoscrizione](https://azure.microsoft.com/support/legal/subscription-agreement/).  Quando vengono eliminati i dati, viene eliminato anche l'account di Archiviazione di Microsoft Azure in cui risiedono.  L'eliminazione dei dati del cliente non comporta la distruzione delle unità fisiche.  

La tabella seguente elenca alcune delle soluzioni disponibili e propone alcuni esempi del tipo di dati raccolti da tali soluzioni.

| **Soluzione** | **Tipi di dati** |
| --- | --- |
| Capacity and Performance |Dati e metadati sulle prestazioni |
| Malware Assessment |Dati e metadati di configurazione |
| Gestione degli aggiornamenti |Metadati e dati di stato |
| Log Management |Log eventi definiti dall'utente, log eventi di Windows e/o log di IIS |
| Change Tracking |Inventario software, metadati di daemon dei servizi di Windows e Linux e metadati di file Windows/Linux |
| SQL and Active Directory Assessment |Dati WMI, dati del Registro di sistema, dati sulle prestazioni e risultati delle visualizzazioni a gestione dinamica di SQL Server |

La tabella seguente mostra esempi di tipi di dati:

| **Tipo di dati** | **Fields** |
| --- | --- |
| Avviso |Alert Name, Alert Description, BaseManagedEntityId, Problem ID, IsMonitorAlert, RuleId, ResolutionState, Priority, Severity, Category, Owner, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Configurazione |CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Event |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Nota:** OMS raccoglie gli eventi scritti con campi personalizzati nel registro eventi di Windows. |
| Metadata |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Prestazioni |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Stato |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Sicurezza fisica
Il servizio Log Analytics è gestito da personale Microsoft e tutte le attività vengono registrate e possono essere controllate. Log Analytics viene gestito come servizio di Azure e soddisfa tutti i requisiti di conformità e di sicurezza di Azure. È possibile verificare i dettagli sulla sicurezza fisica delle risorse di Azure a pagina 18 della [panoramica della sicurezza in Microsoft Azure](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). I diritti di accesso fisici per proteggere le aree vengono modificati nell'arco della giornata lavorativa per chi non è più responsabile del servizio OMS, includendo il trasferimento e la chiusura. Altre informazioni sull'infrastruttura fisica globale sono disponibili nei [data center di Microsoft](https://www.microsoft.com/server-cloud/cloud-os/global-datacenters.aspx).

## <a name="incident-management"></a>Gestione di eventi imprevisti
OMS dispone di un processo di gestione degli eventi imprevisti a cui aderiscono tutti i servizi di Microsoft. Per riepilogare:

* Usare un modello di responsabilità condivisa in cui la responsabilità della sicurezza viene ripartita tra Microsoft e il cliente
* Gestire gli eventi imprevisti correlati alla sicurezza di Azure:
  * Avviare un'indagine quando viene rilevato un evento imprevisto
  * Far valutare l'impatto e la gravità dell'evento imprevisto a un membro del team di risposta agli eventi imprevisti su chiamata. In base alle prove raccolte, la valutazione può o non può comportare un'ulteriore l'escalation al team di risposta di sicurezza.
  * Far eseguire la diagnosi di un evento imprevisto ad esperti di risposta di sicurezza per condurre l'indagine tecnica o forense e identificare le strategie di contenimento, attenuazione e risoluzione. Se il team di sicurezza ritiene che i dati dei clienti possano essere esposti al rischio di accesso illegale o non autorizzato di un singolo utente, viene avviata l'esecuzione in parallelo del processo di notifica di evento imprevisto al cliente.  
  * Stabilizzare e correggere l'evento imprevisto. Il team di risposta agli eventi imprevisti sviluppa un piano di recupero per porre rimedio al problema. I passaggi per il contenimento della crisi, ad esempio la messa in quarantena dei sistemi coinvolti, possono verificarsi immediatamente e in parallelo con la diagnosi. È possibile pianificare le operazioni di risoluzione dei problemi a lungo termine che vengono eseguite dopo il superamento del rischio immediato.  
  * Chiudere l'evento imprevisto ed eseguire una relazione finale. Il team di risposta agli eventi imprevisti redige una relazione finale che descrive nei dettagli l'evento imprevisto, con l'intenzione di modificare i criteri, le procedure e i processi per evitare che tale evento possa ripetersi in futuro.
* Inviare ai clienti una notifica sugli eventi imprevisti relativi alla sicurezza:
  * Determinare l'ambito dei clienti coinvolti e inviare il prima possibile una notifica a chiunque sia stato coinvolto
  * Creare un avviso per fornire ai clienti tutte le informazioni dettagliate affinché possano svolgere le opportune indagini e rispettare gli impegni presi con gli utenti finali senza ritardare eccessivamente il processo di notifica.
  * Confermare e dichiarare l'evento imprevisto, in base alle esigenze.
  * Informare i clienti con una notifica sull'evento imprevisto senza ritardi ingiustificati e nel rispetto dei termini legali o contrattuali. La notifica di eventi imprevisti di sicurezza viene recapitata a uno o più amministratori del cliente per mezzo di un qualsiasi canale scelto da Microsoft, inclusa la posta elettronica.
* Svolgere la formazione e verificare la preparazione del team:
  * Il personale di Microsoft è tenuto a completare una formazione in materia di sicurezza e consapevolezza, per poter identificare e segnalare problemi di sicurezza sospetti.  
  * Gli operatori che usano il servizio Microsoft Azure hanno l'obbligo di sostenere una formazione aggiuntiva in relazione alla loro possibilità di accedere ai sistemi riservati che ospitano i dati dei clienti.
  * Il personale di risposta di sicurezza Microsoft riceve una formazione specializzata per i ruoli ricoperti

In caso di perdita di dati di un cliente, viene inviata una notifica a tale cliente nell'arco di un giorno. Con il servizio non si è tuttavia mai verificata alcuna perdita di dati. 

Per altre informazioni sulle modalità di risposta agli eventi imprevisti in merito alla sicurezza di Microsoft, vedere [Risposta di Microsoft Azure Security nel cloud](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/1/Microsoft Azure Security Response in the cloud.pdf).

## <a name="compliance"></a>Conformità
Il programma di governance e per la sicurezza delle informazioni del team di sviluppo e assistenza del software Log Analytics supporta requisiti interni ed è conforme alle leggi e alle normative, come descritto nel [Centro protezione di Azure](https://azure.microsoft.com/support/trust-center/) e nella sezione [Conformità del centro protezione di Microsoft](https://www.microsoft.com/TrustCenter/Compliance/default.aspx). Qui viene anche descritto il modo in cui Log Analytics stabilisce i requisiti di sicurezza, identifica i controlli di sicurezza, gestisce e controlla i rischi. Ogni anno viene effettuata una revisione di criteri, standard, procedure e linee guida.

Ciascun membro del team di sviluppo riceve una formazione formale sulla sicurezza delle applicazioni. Internamente, viene usato un sistema di controllo della versione per lo sviluppo di software, che protegge ogni singolo progetto software.

Microsoft si avvale di un team per la sicurezza e conformità che supervisiona e valuta tutti i servizi in Microsoft. Il team è composto da addetti alla sicurezza delle informazioni che non sono associati ai reparti tecnici dove si sviluppa Log Analytics. Gli addetti alla sicurezza dispongono della propria catena di gestione ed eseguono valutazioni indipendenti di prodotti e servizi per garantirne la sicurezza e la conformità.

Il consiglio di amministrazione di Microsoft viene tenuto aggiornato tramite un report annuale su tutti i programmi per la sicurezza delle informazioni messi in atto da Microsoft.

Il team dedicato allo sviluppo software e al servizio Log Analytics è impegnato attivamente nella collaborazione con il team legale di Microsoft, nonché con il team dedicato alla gestione della conformità e con altri partner di settore, per acquisire varie certificazioni.

## <a name="certifications-and-attestations"></a>Certificazioni e attestazioni
Azure Log Analytics soddisfa i requisiti seguenti:

* [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* Conformità al [Payment Card Industry Data Security Standard (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) del PCI Security Standard Council
* [Service Organization Controls (SOC) 1 di tipo 1 e SOC 2 di tipo 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2)
* [HIPAA e HITECH](https://www.microsoft.com/TrustCenter/Compliance/HIPAA) per le società che hanno un contratto di società in affari HIPAA
* Criteri di progettazione comuni di Windows
* Microsoft Trustworthy Computing
* Come servizio di Azure, i componenti usati da Log Analytics aderiscono ai requisiti di conformità di Azure. Altre informazioni disponibili in [Centro protezione Microsoft - Conformità](https://www.microsoft.com/TrustCenter/Compliance/default.aspx).

> [!NOTE]
> In alcune certificazioni e attestazioni Log Analytics viene indicato con il nome precedente, *Operational Insights*.
>
>

## <a name="cloud-computing-security-data-flow"></a>Flusso di dati sulla sicurezza del cloud computing
Il diagramma seguente mostra un'architettura di sicurezza cloud come flusso di informazioni in uscita dall'azienda, il modo in cui tale flusso viene protetto durante il percorso verso il servizio Log Analytics e infine come viene visualizzato nel portale di Azure o nel portale classico di OMS. IL diagramma riporta anche informazioni aggiuntive su ogni passaggio.

![Immagine relativa alla sicurezza e alla raccolta dati di Log Analytics](./media/log-analytics-data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Iscriversi a Log Analytics e raccogliere dati
Per permettere all'organizzazione di inviare dati a Log Analytics, configurare un agente Windows o Linux in esecuzione sulle macchine virtuali Azure o sui computer fisici o virtuali nell'ambiente o in un altro provider di servizi cloud.  Se si usa Operations Manager, dal gruppo di gestione configurare l'agente di Operations Manager. Gli utenti (che possono essere utenti singoli o gruppi) creano una o più aree di lavoro di Log Analytics e registrano gli agenti usando uno degli account seguenti:

* [ID aziendale](../active-directory/sign-up-organization.md)
* [Account Microsoft - Outlook, Office Live, MSN](http://www.microsoft.com/account/default.aspx)

Un'area di lavoro di Log Analytics viene usata per raccogliere, aggregare, analizzare e presentare i dati. Un'area di lavoro è univoca e viene usata principalmente per eseguire la partizione dei dati. Si possono ad esempio gestire i dati di produzione con un'area di lavoro e i dati di test con un'altra area di lavoro. Le aree di lavoro vengono anche usate da un amministratore per controllare l'accesso ai dati ad opera degli utenti. A ogni area di lavoro possono essere associati più account utente, ognuno dei quali può accedere a più aree di lavoro di Log Analytics. Creare le aree di lavoro in base all'area data center. Ogni area di lavoro viene replicata negli altri data center dell'area, principalmente per la disponibilità del servizio Log Analytics.

Per Operations Manager, il gruppo di gestione di Operations Manager stabilisce una connessione con il servizio Log Analytics. Quindi si specificano i sistemi gestiti tramite agente del gruppo di gestione a cui è consentito raccogliere e inviare dati al servizio. A seconda della soluzione abilitata, i dati di queste soluzioni vengono inviati direttamente da un server di gestione di Operations Manager al servizio Log Analytics oppure, a causa del volume dei dati raccolti dal sistema gestito dall'agente, vengono inviati direttamente dall'agente al servizio. Per i sistemi non monitorati da Operations Manager, ognuno si connette in modo sicuro direttamente al servizio Log Analytics.

Tutte le comunicazioni tra i sistemi connessi e il servizio di Log Analytics sono crittografate.  Il protocollo TLS (HTTPS) viene usato per la crittografia.  Viene seguita la procedura di Microsoft SDL per assicurare che Log Analytics sia aggiornato con i più recenti progressi nel campo dei protocolli di crittografia.

Ogni tipo di agente raccoglie dati per Log Analytics. Il tipo di dati raccolti dipende dai tipi di soluzioni usati. È possibile visualizzare un riepilogo della raccolta di dati in [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md). Inoltre, per la maggior parte delle soluzioni sono disponibili altre informazioni dettagliate sulla raccolta. Una soluzione è costituita da un bundle di visualizzazioni, query di ricerca, regole di raccolta dati e logica di elaborazione predefinite. Solo gli amministratori possono usare Log Analytics per importare una soluzione. Dopo l'importazione, la soluzione viene spostata nei server di gestione di Operations Manager (se usati) e quindi negli agenti scelti. Gli agenti raccoglieranno quindi i dati.

## <a name="2-send-data-from-agents"></a>2. Invio dei dati dagli agenti
Si registrano tutti i tipi di agente con una chiave di registrazione e viene stabilita una connessione sicura tra l'agente e il servizio Log Analytics usando l'autenticazione basata su certificati e SSL con la porta 443. Log Analytics usa un archivio segreto per generare e gestire le chiavi. Le chiavi private sono soggette a rotazione ogni 90 giorni, vengono archiviate in Azure e sono gestite dalle operazioni di Azure in ottemperanza alle procedure consigliate in materia di conformità e normative.

Con Operations Manager, il gruppo di gestione registrato con un'area di lavoro di Log Analytics stabilisce una connessione HTTPS protetta con un server di gestione di Operations Manager.

Per gli agenti Windows o Linux in esecuzione su macchine virtuali di Azure, viene usata una chiave di archiviazione di sola lettura per leggere gli eventi di diagnostica nelle tabelle di Azure.  

Per ogni agente che invia report a un gruppo di gestione di Operations Manager integrato con Log Analytics, se il server di gestione non è in grado di comunicare con il servizio per un motivo qualsiasi, i dati raccolti vengono archiviati in locale in una cache temporanea sul server di gestione.   Provano a inviare i dati ogni 8 minuti per 2 ore.  Per i dati che ignorano il server di gestione e vengono inviati direttamente a Log Analytics, il comportamento è coerente con l'agente di Windows.  

I dati memorizzati nella cache dell'agente del server di gestione o Windows sono protetti dall'archivio credenziali del sistema operativo. Se il servizio non può elaborare i dati dopo due ore, i dati vengono accodati dagli agenti. Se la coda si riempie, l'agente inizia a eliminare i tipi di dati, a partire dai dati sulle prestazioni. Il limite delle code agente è una chiave di registro modificabile quando necessario. I dati raccolti vengono compressi e inviati al servizio ignorando i database dei gruppi di gestione di Operations Manager, che in questo modo non vengono sovraccaricati. Dopo l'invio, i dati raccolti vengono rimossi dalla cache.

Come descritto sopra, i dati provenienti dal server di gestione o dagli agenti connessi direttamente vengono inviati tramite SSL ai data center di Microsoft Azure. Facoltativamente, è possibile usare ExpressRoute per proteggere ulteriormente i dati. ExpressRoute è un modo per connettersi direttamente ad Azure da una rete WAN esistente, ad esempio una rete VPN MPLS (multi-protocol label switching), fornita da un provider di servizi di rete. Per altre informazioni, vedere [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. Il servizio Log Analytics riceve ed elabora i dati
Per garantire che i dati in arrivo provengano da un'origine attendibile, il servizio Log Analytics convalida i certificati e l'integrità dei dati con l'autenticazione di Azure. I dati non elaborati vengono quindi archiviati in un Hub di eventi di Azure nell'area in cui verranno infine archiviati i dati inattivi. Il tipo dei dati archiviati dipende dai tipi di soluzioni importati e usati per raccogliere i dati. Successivamente, il servizio Log Analytics elabora i dati non elaborati e li inserisce nel database.

Il periodo di conservazione dei dati raccolti archiviati nel database varia a seconda del piano selezionato quando è stata creata l'area di lavoro.  Per il livello a pagamento, i dati raccolti sono disponibili per 31 giorni per impostazione predefinita, ma è possibile estendere il periodo a 365 giorni.  Questi dati non sono ancora crittografati quando inattivi. L'implementazione di questa funzionalità è stata pianificata per la metà del 2018. 

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Usare Log Analytics per accedere ai dati
Per accedere all'area di lavoro Log Analytics, accedere al portale di Azure usando l'account aziendale o l'account Microsoft configurato prima. Tutto il traffico tra il portale e il servizio Log Analytics viene inviato tramite un canale HTTPS sicuro. Quando si usa il portale, viene generato un ID di sessione nel client utente (Web browser) e i dati vengono archiviati in una cache locale fino al termine della sessione. Dopodiché, la cache viene svuotata. I cookie sul lato client, che non contengono informazioni personali, non vengono rimossi automaticamente. I cookie di sessione sono protetti e contrassegnati HTTPOnly. Dopo un periodo di inattività prestabilito, la sessione del portale di Azure termina.

## <a name="next-steps"></a>Passaggi successivi
* È possibile ottenere informazioni su come raccogliere i dati con Log Analytics per le macchine virtuali di Azure seguendo la [guida introduttiva alle macchine virtuali di Azure](log-analytics-quick-collect-azurevm.md).  

*  Se si desidera raccogliere dati da computer Windows o Linux fisici o virtuali nell'ambiente in uso, vedere [Avvio rapido per i computer Linux](log-analytics-quick-collect-linux-computer.md) o la [Avvio rapido per i computer Windows](log-analytics-quick-collect-windows-computer.md)

