---
title: Note sulla versione per il Centro sicurezza di Azure
description: Descrizione delle novità e delle modifiche apportate al Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2020
ms.author: memildin
ms.openlocfilehash: 997b7b1442d29c4daa4e47c89a7f53d46c3b8c81
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85969586"
---
# <a name="whats-new-in-azure-security-center"></a>Novità del Centro sicurezza di Azure

La sicurezza di Azure è in fase di sviluppo attivo e riceve miglioramenti su base continuativa. Per stare al passo con gli sviluppi più recenti, questa pagina fornisce informazioni sugli argomenti seguenti:

- Nuove funzionalità
- Correzioni di bug
- Funzionalità deprecate

Poiché questa pagina viene aggiornata regolarmente, è consigliabile consultarla spesso. Se si cercano informazioni precedenti agli ultimi sei mesi, è possibile trovarle in [Archive for What's new in Azure Security Center](release-notes-archive.md) (Archivio per le novità nel Centro sicurezza di Azure).

## <a name="july-2020"></a>Luglio 2020

Gli aggiornamenti di luglio includono:
- [Protezione dalle minacce per archiviazione di Azure espansa per includere File di Azure e Azure Data Lake Storage Gen2 (anteprima)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)

### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Protezione dalle minacce per archiviazione di Azure espansa per includere File di Azure e Azure Data Lake Storage Gen2 (anteprima)

La protezione dalle minacce per archiviazione di Azure rileva attività potenzialmente dannose negli account di archiviazione di Azure. Il Centro sicurezza Visualizza gli avvisi quando rileva i tentativi di accesso o di exploit degli account di archiviazione. 

I dati possono essere protetti indipendentemente dal fatto che vengano archiviati come contenitori BLOB, condivisioni file o data Lake. 

Altre informazioni sulla [protezione dalle minacce per archiviazione di Azure](threat-protection.md#threat-protection-for-azure-storage-).


## <a name="june-2020"></a>Giugno 2020

Gli aggiornamenti di giugno includono:
- [API per il Punteggio sicuro (anteprima)](#secure-score-api-preview)
- [Sicurezza avanzata dei dati per i computer SQL (Azure, altri cloud e locali) (anteprima)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [Due nuove raccomandazioni per distribuire l'agente di Log Analytics ai computer Azure Arc (anteprima)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Nuovi criteri per creare configurazioni di automazione del flusso di lavoro e esportazione continua su larga scala](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Nuova raccomandazione per l'uso di gruppi per proteggere le macchine virtuali non connesse a Internet](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Nuovi criteri per l'abilitazione di protezione dalle minacce e sicurezza avanzata dei dati](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>API per il Punteggio sicuro (anteprima)

È ora possibile accedere al Punteggio tramite l' [API per il Punteggio sicuro](https://docs.microsoft.com/rest/api/securitycenter/securescores/) (attualmente in anteprima). I metodi API offrono la flessibilità necessaria per eseguire query sui dati e creare un proprio meccanismo di creazione di report dei punteggi sicuri nel tempo. Ad esempio, è possibile usare l'API dei **punteggi sicuri** per ottenere il punteggio per una sottoscrizione specifica. Inoltre, è possibile usare l'API di **controllo del Punteggio sicuro** per elencare i controlli di sicurezza e il punteggio corrente delle sottoscrizioni.

Per esempi di strumenti esterni resi possibili con l'API per il Punteggio sicuro, vedere [l'area relativa al Punteggio sicuro della community di GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Altre informazioni sul [Punteggio sicuro e sui controlli di sicurezza nel centro sicurezza di Azure](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>Sicurezza avanzata dei dati per i computer SQL (Azure, altri cloud e locali) (anteprima)

La protezione avanzata dei dati del Centro sicurezza di Azure per i computer SQL ora protegge i server SQL ospitati in Azure, in altri ambienti cloud e anche in computer locali. In questo modo si estendono le protezioni per i server SQL nativi di Azure per supportare completamente gli ambienti ibridi.

La sicurezza dei dati avanzata offre la valutazione della vulnerabilità e la protezione avanzata dalle minacce per i computer SQL ovunque si trovino.

Il programma di installazione prevede due passaggi:

1. Distribuzione dell'agente di Log Analytics nel computer host del SQL Server per fornire la connessione all'account Azure.

1. Abilitazione del bundle facoltativo nella pagina dei prezzi e delle impostazioni del Centro sicurezza.

Altre informazioni sulla [sicurezza dei dati avanzata per i computer SQL](security-center-iaas-advanced-data.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Due nuove raccomandazioni per distribuire l'agente di Log Analytics ai computer Azure Arc (anteprima)

Sono state aggiunte due nuove raccomandazioni per facilitare la distribuzione dell' [agente log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent) nei computer Azure Arc e garantire che siano protetti dal centro sicurezza di Azure:

- **Log Analytics Agent deve essere installato nei computer Azure Arc basati su Windows (anteprima)**
- **Log Analytics Agent deve essere installato nei computer Azure Arc basati su Linux (anteprima)**

Queste nuove raccomandazioni verranno visualizzate negli stessi quattro controlli di sicurezza della raccomandazione esistente (correlata). **è necessario installare l'agente di monitoraggio nei computer**: correggere le configurazioni di sicurezza, applicare il controllo delle applicazioni adattivi, applicare gli aggiornamenti del sistema e abilitare Endpoint Protection.

Le raccomandazioni includono inoltre la funzionalità di correzione rapida che consente di velocizzare il processo di distribuzione. 

Altre informazioni su queste due nuove raccomandazioni sono disponibili nella tabella [consigli calcolo e app](recommendations-reference.md#recs-computeapp) .

Altre informazioni sul modo in cui il Centro sicurezza di Azure usa l'agente in [che cos'è il log Analytics Agent?](https://docs.microsoft.com/azure/security-center/faq-data-collection-agents#what-is-the-log-analytics-agent).

Altre informazioni sulle [estensioni per i computer Azure Arc](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal).



### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Nuovi criteri per creare configurazioni di automazione del flusso di lavoro e esportazione continua su larga scala

L'automazione dei processi di monitoraggio e risposta agli eventi imprevisti dell'organizzazione può migliorare significativamente il tempo necessario per analizzare e mitigare gli eventi imprevisti della sicurezza.

Per distribuire le configurazioni di automazione nell'organizzazione, usare i criteri di Azure "DeployIfdNotExist" predefiniti per creare e configurare le procedure di [esportazione continua](continuous-export.md) e di [automazione del flusso di lavoro](workflow-automation.md) :

I criteri si trovano in criteri di Azure:


|Obiettivo  |Policy  |ID condizione  |
|---------|---------|---------|
|Esportazione continua nell'hub eventi|[Distribuisci esportazione in hub eventi per gli avvisi e le raccomandazioni del Centro sicurezza di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Esportazione continua in Log Analytics area di lavoro|[Distribuisci esportazione nell'area di lavoro Log Analytics per gli avvisi e le raccomandazioni del Centro sicurezza di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Automazione del flusso di lavoro per gli avvisi di sicurezza|[Distribuisci automazione del flusso di lavoro per gli avvisi del Centro sicurezza di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Automazione del flusso di lavoro per raccomandazioni sulla sicurezza|[Distribuisci automazione del flusso di lavoro per le raccomandazioni del Centro sicurezza di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Introduzione ai [modelli di automazione dei flussi di lavoro](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Altre informazioni sull'uso dei due criteri di esportazione in [esportare continuamente avvisi e raccomandazioni del Centro sicurezza di Azure tramite i criteri](https://techcommunity.microsoft.com/t5/azure-security-center/continuously-export-azure-security-center-alerts-and/ba-p/1440745).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Nuova raccomandazione per l'uso di gruppi per proteggere le macchine virtuali non connesse a Internet

Il controllo di sicurezza "implementa procedure consigliate per la sicurezza" include ora la nuova raccomandazione seguente:

- **Le macchine virtuali senza connessione Internet devono essere protette con i gruppi di sicurezza di rete**

Una raccomandazione esistente, **le macchine virtuali con connessione Internet devono essere protette con i gruppi di sicurezza di rete**, senza distinguere tra macchine virtuali con connessione Internet e non Internet. Per entrambe, è stata generata una raccomandazione con gravità elevata se una macchina virtuale non è stata assegnata a un gruppo di sicurezza di rete. Questa nuova raccomandazione separa i computer non connessi a Internet per ridurre i falsi positivi ed evitare gli avvisi non necessari a livello di gravità.

Per altre informazioni, vedere la tabella [raccomandazioni sulla rete](recommendations-reference.md#recs-network) .




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Nuovi criteri per l'abilitazione di protezione dalle minacce e sicurezza avanzata dei dati

I nuovi criteri indicati di seguito sono stati aggiunti all'iniziativa ASC default e sono progettati per facilitare l'abilitazione della protezione dalle minacce o della sicurezza dei dati avanzata per i tipi di risorsa pertinenti.

I criteri si trovano in criteri di Azure:


| Policy                                                                                                                                                                                                                                                                | ID condizione                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [La sicurezza dei dati avanzata deve essere abilitata nei server di database SQL di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [È necessario abilitare la sicurezza dei dati avanzata in SQL Server nei computer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Advanced Threat Protection deve essere abilitato negli account di archiviazione](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Advanced Threat Protection deve essere abilitato negli insiemi di credenziali Azure Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Advanced Threat Protection deve essere abilitato nei piani di servizio app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [È necessario abilitare Advanced Threat Protection nei registri di Container Registry di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [È necessario abilitare Advanced Threat Protection nei cluster del servizio Azure Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [Advanced Threat Protection deve essere abilitato nelle macchine virtuali](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

Scopri di più sulla [protezione dalle minacce nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/threat-protection).





## <a name="may-2020"></a>Maggio 2020

Gli aggiornamenti in possono includere:
- [Regole di eliminazione degli avvisi (anteprima)](#alert-suppression-rules-preview)
- [La valutazione delle vulnerabilità delle macchine virtuali è ora disponibile a livello generale](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Modifiche all'accesso JIT (just-in-time) alle macchine virtuali](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [Le raccomandazioni personalizzate sono state spostate in un controllo di sicurezza distinto](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [Interruttore aggiunto per visualizzare le raccomandazioni nei controlli o come elenco semplice](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Controllo di sicurezza espanso "Implement security best practices" (Implementa procedure consigliate per la sicurezza)](#expanded-security-control-implement-security-best-practices)
- [I criteri personalizzati con metadati personalizzati sono ora disponibili a livello generale](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Migrazione delle funzionalità di analisi dei dump di arresto anomalo al rilevamento di attacchi senza file](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Regole di eliminazione degli avvisi (anteprima)

Questa nuova funzionalità, attualmente in anteprima, semplifica le attività correlate agli avvisi. Usare regole per nascondere automaticamente gli avvisi noti come innocui o correlati alle normali attività dell'organizzazione. In questo modo è possibile concentrarsi sulle minacce più pertinenti. 

Gli avvisi che corrispondono alle regole di eliminazione abilitate vengono comunque generati, ma il relativo stato verrà impostato su Ignorato. È possibile visualizzare lo stato nel portale di Azure o accedere agli avvisi di sicurezza del Centro sicurezza.

Le regole di eliminazione definiscono i criteri per cui gli avvisi devono essere automaticamente ignorati. In genere, si usa una regola di eliminazione per:

- eliminare gli avvisi identificati come falsi positivi

- eliminare gli avvisi che vengono attivati troppo spesso per essere utili

Sono disponibili altre informazioni sull'[eliminazione degli avvisi](alerts-suppression-rules.md) dalla protezione dalle minacce del Centro sicurezza di Azure.


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>La valutazione delle vulnerabilità delle macchine virtuali è ora disponibile a livello generale

Il livello standard del Centro sicurezza include ora una valutazione integrata delle vulnerabilità per le macchine virtuali, senza costi aggiuntivi. Questa estensione è supportata da Qualys, ma segnala i risultati direttamente al Centro sicurezza. Non è necessaria una licenza Qualys, né un account Qualys: tutto viene gestito senza interruzioni all'interno del Centro sicurezza.

La nuova soluzione può analizzare continuamente le macchine virtuali per individuare le vulnerabilità e presentare i risultati nel Centro sicurezza. 

Per distribuire la soluzione, usare la nuova raccomandazione per la sicurezza:

"Abilitare la soluzione di valutazione delle vulnerabilità predefinita nelle macchine virtuali (con tecnologia Qualys)"

Sono disponibili altre informazioni sulla [valutazione delle vulnerabilità integrata del Centro sicurezza per le macchine virtuali](built-in-vulnerability-assessment.md).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Modifiche all'accesso JIT (just-in-time) alle macchine virtuali

Il Centro sicurezza include una funzionalità opzionale per proteggere le porte di gestione delle macchine virtuali. Questa funzionalità garantisce una difesa contro la forma più comune di attacchi di forza bruta.

Questo aggiornamento apporta le seguenti modifiche a questa funzionalità:

- La raccomandazione che consiglia di abilitare JIT in una macchina virtuale è stata rinominata. "Il controllo di accesso alla rete JIT deve essere applicato alle macchine virtuali" è ora diventato: "Le porte di gestione delle macchine virtuali devono essere protette tramite un controllo di accesso alla rete JIT".

- La raccomandazione è stata impostata per attivarsi solo se sono presenti porte di gestione aperte.

Sono disponibili altre informazioni sulla [funzionalità di accesso JIT](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Le raccomandazioni personalizzate sono state spostate in un controllo di sicurezza distinto

Uno dei controlli di sicurezza introdotti con il punteggio di sicurezza migliorato è "Implement security best practices" (Implementa procedure consigliate per la sicurezza). Eventuali raccomandazioni personalizzate create per le sottoscrizioni sono state inserite automaticamente nel controllo. 

Per semplificare l'individuazione delle raccomandazioni personalizzate, sono state spostate in un controllo di sicurezza dedicato, "Custom Recommendations" (Raccomandazioni personalizzate). Questo controllo non ha alcun effetto sul punteggio di sicurezza.

Altre informazioni sui controlli di sicurezza sono disponibili in [Enhanced secure score (preview) in Azure Security Center](secure-score-security-controls.md) (Punteggio di sicurezza migliorato (anteprima) nel Centro sicurezza di Azure).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Interruttore aggiunto per visualizzare le raccomandazioni nei controlli o come elenco semplice

I controlli di sicurezza sono gruppi logici di raccomandazioni correlate alla sicurezza. Riflettono le superfici di attacco vulnerabili. Un controllo è un set di raccomandazioni sulla sicurezza, con istruzioni che consentono di implementare tali raccomandazioni.

Per verificare immediatamente in che modo l'organizzazione protegge ogni singola superficie di attacco, esaminare i punteggi per ogni controllo di sicurezza.

Per impostazione predefinita, le raccomandazioni vengono visualizzate nei controlli di sicurezza. Da questo aggiornamento è inoltre possibile visualizzarle come elenco. Per visualizzarle come semplici elenchi ordinati in base allo stato di integrità delle risorse interessate, usare il nuovo interruttore di raggruppamento per controlli. L'interruttore è in cima all'elenco nel portale.

I controlli di sicurezza, e questo interruttore, fanno parte della nuova esperienza di assegnazione dei punteggi di sicurezza. Ricordarsi di inviare feedback dall'interno del portale.

Altre informazioni sui controlli di sicurezza sono disponibili in [Enhanced secure score (preview) in Azure Security Center](secure-score-security-controls.md) (Punteggio di sicurezza migliorato (anteprima) nel Centro sicurezza di Azure).

![Interruttore "Raggruppa per controlli" per le indicazioni](\media\secure-score-security-controls\recommendations-group-by-toggle.gif)

### <a name="expanded-security-control-implement-security-best-practices"></a>Controllo di sicurezza espanso "Implement security best practices" (Implementa procedure consigliate per la sicurezza) 

Uno dei controlli di sicurezza introdotti con il punteggio di sicurezza migliorato è "Implement security best practices" (Implementa procedure consigliate per la sicurezza). Quando una raccomandazione si trova in questo controllo, non ha alcun effetto sul punteggio di sicurezza. 

Con questo aggiornamento, tre raccomandazioni sono state spostate dai controlli in cui erano originariamente posizionate e inserite in questo controllo di procedure consigliate. Ciò è dovuto al fatto che il rischio di queste tre raccomandazioni è risultato inferiore a quello inizialmente previsto.

Sono state introdotte due nuove raccomandazioni aggiunte a questo controllo.

Le tre raccomandazioni spostate sono:

- **La funzionalità MFA deve essere abilitata per gli account con autorizzazioni di lettura per la sottoscrizione** (in origine, il controllo "Abilita MFA")
- **Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione** (in origine, il controllo "Manage access and permissions") (Gestisci accesso e autorizzazioni)
- **Deve essere designato un massimo di 3 proprietari per la sottoscrizione** (in origine, il controllo "Manage access and permissions") (Gestisci accesso e autorizzazioni)

Le due nuove raccomandazioni aggiunte al controllo sono:

- L' **estensione di configurazione Guest deve essere installata in macchine virtuali Windows (anteprima)** : l'uso della [configurazione Guest di criteri di Azure](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) offre visibilità all'interno delle macchine virtuali alle impostazioni server e applicazione (solo Windows).

- **Windows Defender exploit Guard dovrebbe essere abilitato nei computer (anteprima)** : Windows Defender exploit Guard usa l'agente di configurazione Guest di criteri di Azure. Exploit Guard include quattro componenti progettati per bloccare i dispositivi da un'ampia gamma di vettori di attacco e comportamenti di blocco usati comunemente negli attacchi malware, consentendo al contempo alle aziende di bilanciare i requisiti di rischi per la sicurezza e produttività (solo Windows).

Per altre informazioni su Windows Defender Exploit Guard, vedere [Creare e distribuire criteri di Exploit Guard](https://docs.microsoft.com/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Altre informazioni sui controlli di sicurezza in [Punteggio sicuro avanzato (anteprima)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>I criteri personalizzati con metadati personalizzati sono ora disponibili a livello generale

I criteri personalizzati fanno ora parte delle raccomandazioni del Centro sicurezza, del punteggio di sicurezza e del dashboard degli standard di conformità alle normative. Questa funzionalità è ora disponibile a livello generale e consente di estendere la copertura della valutazione della sicurezza dell'organizzazione nel Centro sicurezza. 

Creare un'iniziativa personalizzata in Criteri di Azure, aggiungervi criteri e integrarla nel Centro sicurezza di Azure, quindi visualizzarla come raccomandazione.

È stata anche aggiunta la possibilità di modificare i metadati delle raccomandazioni personalizzate. Le opzioni dei metadati includono gravità, procedure di correzione, informazioni sulle minacce e altro ancora.  

Sono disponibili altre informazioni sull'[ottimizzazione delle raccomandazioni personalizzate con informazioni dettagliate](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Migrazione delle funzionalità di analisi dei dump di arresto anomalo al rilevamento di attacchi senza file 

Le funzionalità di rilevamento delle analisi dei dump di arresto anomalo di Windows sono state integrate nel [rilevamento degli attacchi senza file](https://docs.microsoft.com/azure/security-center/threat-protection#windows-fileless). L'analisi di rilevamento degli attacchi senza file offre versioni migliorate dei seguenti avvisi di sicurezza per i computer Windows: individuazione di code injection, rilevamento di moduli Windows mascherati, individuazione di shellcode e rilevamento di un segmento di codice sospetto.

Alcuni vantaggi di questa transizione:

- **Rilevamento di malware proattivo e tempestivo** : approccio CdA che prevede l'attesa di un arresto anomalo e quindi l'esecuzione dell'analisi per individuare artefatti dannosi. L'uso del rilevamento di attacchi senza file consente di identificare in modo proattivo le minacce in memoria durante l'esecuzione. 

- **Avvisi avanzati**: gli avvisi di sicurezza dal rilevamento di attacchi senza file includono miglioramenti non disponibili con l'analisi dei dump di arresto anomalo, come le informazioni sulle connessioni di rete attive. 

- **Aggregazione di avvisi**: quando l'analisi dei dump di arresto anomalo rilevava più schemi di attacco in un singolo evento di arresto, attivava più avvisi di sicurezza. Il rilevamento di attacchi senza file combina tutti gli schemi di attacco identificati dallo stesso processo in un singolo avviso, eliminando la necessità di correlare più avvisi.

- **Riduzione dei requisiti nell'area di lavoro Log Analytics**: i dump di arresto anomalo contenenti dati potenzialmente sensibili non verranno più caricati nell'area di lavoro Log Analytics.



## <a name="april-2020"></a>Aprile 2020

Gli aggiornamenti di aprile includono:
- [I pacchetti di conformità dinamici sono ora disponibili a livello generale](#dynamic-compliance-packages-are-now-generally-available)
- [Raccomandazioni per l'identità ora incluse nel livello gratuito del Centro sicurezza di Azure](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>I pacchetti di conformità dinamici sono ora disponibili a livello generale

Il dashboard di conformità alle normative del Centro sicurezza di Azure include ora **pacchetti di conformità dinamici** (ora disponibili a livello generale) per tenere traccia di altri standard di settore e normativi.

È possibile aggiungere pacchetti di conformità dinamici alla sottoscrizione o al gruppo di gestione dalla pagina dei criteri di sicurezza del Centro sicurezza. Quando viene caricato uno standard o un benchmark, viene visualizzato nel dashboard di conformità alle normative insieme ai dati di conformità associati mappati come valutazioni. È anche possibile scaricare un report di riepilogo per gli standard caricati.

Ora è possibile aggiungere standard come:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK Official e UK NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (novità)** (una rappresentazione più completa di Azure CIS 1.1.0)

Inoltre, è stato aggiunto di recente **Azure Security Benchmark**, le linee guida specifiche di Azure, create da Microsoft per le procedure consigliate per la sicurezza e la conformità basate su framework di conformità comuni. Nel dashboard verranno supportati altri standard non appena saranno disponibili.  
 
Sono disponibili altre informazioni sulla [personalizzazione del set di standard nel dashboard di conformità alle normative](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Raccomandazioni per l'identità ora incluse nel livello gratuito del Centro sicurezza di Azure

Sono ora disponibili a livello generale raccomandazioni di sicurezza relative a identità e accesso nel livello gratuito del Centro sicurezza di Azure. Questa operazione rientra nell'impegno volto a rendere gratuite le funzionalità CSPM (Cloud Security Posture Management). Fino ad ora, queste raccomandazioni erano disponibili solo nel piano tariffario standard.

Esempi di raccomandazioni relative a identità e accesso includono:

- "La funzionalità MFA deve essere abilitata per gli account con autorizzazioni di proprietario per la sottoscrizione".
- "Deve essere designato un massimo di 3 proprietari per la sottoscrizione".
- "Gli account deprecati devono essere rimossi dalla sottoscrizione".

Se si hanno sottoscrizioni nel piano tariffario gratuito, il punteggio di sicurezza corrispondente sarà influenzato da questa modifica, in quanto non sono state precedentemente valutate in termini di sicurezza dell'accesso e delle identità.

Sono disponibili altre informazioni sulle [raccomandazioni relative a identità e accesso](recommendations-reference.md#recs-identity).

Sono disponibili altre informazioni sul [monitoraggio di identità e accesso](security-center-identity-access.md).


## <a name="march-2020"></a>Marzo 2020

Gli aggiornamenti di marzo includono:
- [L'automazione del flusso di lavoro è ora disponibile a livello generale](#workflow-automation-is-now-generally-available)
- [Integrazione del Centro sicurezza di Azure con Windows Admin Center](#integration-of-azure-security-center-with-windows-admin-center)
- [Protezione per il servizio Azure Kubernetes](#protection-for-azure-kubernetes-service)
- [Esperienza JIT migliorata](#improved-just-in-time-experience)
- [Due raccomandazioni sulla sicurezza per le applicazioni Web sono ora deprecate](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>L'automazione del flusso di lavoro è ora disponibile a livello generale

La funzionalità di automazione del flusso di lavoro del Centro sicurezza di Azure è ora disponibile a livello generale. È possibile usarla per attivare automaticamente le app per la logica negli avvisi di sicurezza e nelle raccomandazioni. Sono inoltre disponibili trigger manuali per gli avvisi e tutte le raccomandazioni per le quali è disponibile l'opzione di correzione rapida.

Ogni programma di sicurezza include più flussi di lavoro per la risposta agli eventi imprevisti. Questi processi possono includere la notifica a stakeholder di rilievo, l'avvio di un processo di gestione delle modifiche e l'applicazione di procedure di correzione specifiche. Gli esperti di sicurezza raccomandano di automatizzare quante più procedure possibili. L'automazione riduce il sovraccarico e può migliorare la sicurezza garantendo che i passaggi del processo vengano eseguiti rapidamente, in modo coerente e in base ai requisiti predefiniti.

Per altre informazioni sulle funzionalità automatiche e manuali del Centro sicurezza per l'esecuzione dei flussi di lavoro, vedere [Automazione del flusso di lavoro](workflow-automation.md).

Sono disponibili altre informazioni sulla [creazione di app per la logica](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integrazione del Centro sicurezza di Azure con Windows Admin Center

È ora possibile spostare i server Windows locali da Windows Admin Center direttamente nel Centro sicurezza di Azure. Il Centro sicurezza diventa quindi il punto centrale in cui visualizzare le informazioni di sicurezza per tutte le risorse Windows Admin Center, inclusi i server locali, le macchine virtuali e carichi di lavoro PaaS aggiuntivi.

Dopo aver spostato un server da Windows Admin Center al Centro sicurezza di Azure, sarà possibile:

- Visualizzare gli avvisi di sicurezza e le raccomandazioni nell'estensione del Centro sicurezza di Windows Admin Center.
- Visualizzare il comportamento di sicurezza e recuperare informazioni dettagliate aggiuntive dei server gestiti di Windows Admin Center nel Centro sicurezza all'interno del portale di Azure (o tramite un'API).

Sono disponibili altre informazioni su [come integrare il Centro sicurezza di Azure con Windows Admin Center](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Protezione per il servizio Azure Kubernetes

Il Centro sicurezza di Azure espande le funzionalità di sicurezza dei contenitori per proteggere il servizio Azure Kubernetes.

La piattaforma Kubernetes open source è talmente diffusa da essere diventata standard di settore per l'orchestrazione dei contenitori. Ciononostante, non sono ancora disponibili informazioni chiare su come proteggere un ambiente Kubernetes. Per difendere le aree di attacco di un'applicazione aggiunta a un contenitore, è necessario avere una certa esperienza per garantire che l'infrastruttura sia configurata in modo sicuro e costantemente monitorata per potenziali minacce.

La difesa del Centro sicurezza include:

- **Individuazione e visibilità**: individuazione continua delle istanze gestite del servizio Azure Kubernetes all'interno delle sottoscrizioni registrate nel Centro sicurezza.
- **Raccomandazioni sulla sicurezza**: raccomandazioni utili per la conformità alle procedure consigliate sulla sicurezza per il servizio Azure Kubernetes. Queste raccomandazioni sono incluse nel punteggio di sicurezza per garantire che vengano visualizzate nell'ambito del comportamento di sicurezza dell'organizzazione. Un esempio di raccomandazione relativa al servizio Azure Kubernetes potrebbe essere "Il controllo degli accessi in base al ruolo deve essere usato per limitare l'accesso a un cluster del servizio Kubernetes".
- **Protezione dalle minacce**: grazie all'analisi continua della distribuzione del servizio Azure Kubernetes, il Centro sicurezza avvisa l'utente in merito a minacce e attività dannose rilevate a livello di host e del cluster del servizio.

Sono disponibili altre informazioni sull'[integrazione del servizio Azure Kubernetes con il Centro sicurezza](azure-kubernetes-service-integration.md).

Sono disponibili altre informazioni sulle [funzionalità di sicurezza dei contenitori nel Centro sicurezza](container-security.md).


### <a name="improved-just-in-time-experience"></a>Esperienza JIT migliorata

Le funzionalità, il funzionamento e l'interfaccia utente per gli strumenti just-in-time (JIT) del Centro sicurezza di Azure che proteggono le porte di gestione sono stati migliorati come segue: 

- **Campo di giustificazione**: quando si richiede l'accesso a una macchina virtuale tramite la pagina JIT del portale di Azure, è disponibile un nuovo campo facoltativo per immettere una giustificazione per la richiesta. Le informazioni immesse in questo campo possono essere rilevate nel log attività. 
- **Pulizia automatica di regole JIT ridondanti**: ogni volta che si aggiorna un criterio JIT, viene eseguito automaticamente uno strumento di pulizia per verificare la validità dell'intero set di regole. Lo strumento cerca eventuali mancate corrispondenze tra le regole nei criteri e le regole nel gruppo di sicurezza di rete. Se lo strumento di pulizia rileva una mancata corrispondenza, determina la cause e, se l'operazione è sicura, rimuove le regole incorporate che non sono più necessarie. Lo strumento di pulizia non elimina mai le regole create dall'utente. 

Sono disponibili altre informazioni sulla [funzionalità di accesso JIT](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Due raccomandazioni sulla sicurezza per le applicazioni Web sono ora deprecate

Due raccomandazioni sulla sicurezza per le applicazioni Web sono ora considerate deprecate: 

- È consigliabile applicare la protezione avanzata alle regole per le applicazioni Web nei gruppi di sicurezza di rete IaaS
    (criterio correlato: È necessario rafforzare le regole dei gruppi di sicurezza di rete per le applicazioni Web nella soluzione IaaS).

- L'accesso ai Servizi app deve essere limitato
    (criterio correlato: [Anteprima]: L'accesso ai Servizi app deve essere limitato).

Queste raccomandazioni non verranno più visualizzate nel relativo elenco del Centro sicurezza. I criteri correlati non verranno più inclusi nell'iniziativa denominata "Criteri predefiniti del Centro sicurezza".

Sono disponibili altre informazioni sulle [raccomandazioni relative alla sicurezza](recommendations-reference.md).



## <a name="february-2020"></a>Febbraio 2020

### <a name="fileless-attack-detection-for-linux-preview"></a>Rilevamento di attacchi senza file per Linux (anteprima)

Dal momento che gli utenti malintenzionati impiegano metodi sempre più sofisticati per evitare il rilevamento, il Centro sicurezza di Azure estende il rilevamento degli attacchi senza file a Linux, oltre che a Windows. Gli attacchi senza file sfruttano le vulnerabilità del software, introducono payload dannosi in processi di sistema benigni e si nascondono nella memoria. Queste tecniche:

- riducono al minimo o eliminano del tutto le tracce di malware sul disco
- riducono notevolmente le probabilità di rilevamento da parte di soluzioni di analisi dei malware basate su disco

Per contrastare questa minaccia, il Centro sicurezza di Azure ha rilasciato il rilevamento degli attacchi senza file per Windows nell'ottobre 2018 e ora ha esteso questa funzionalità anche a Linux. 

