---
title: Consigli del Centro sicurezza di Azure per computer & app
description: Raccomandazioni sulla sicurezza del Centro sicurezza di Azure che consentono di proteggere le macchine virtuali, i computer, le app Web e gli ambienti del servizio app.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2019
ms.author: memildin
ms.openlocfilehash: c0cf5951daf004a0c805b688f08d1ccfa4679615
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782164"
---
# <a name="compute-and-app-recommendations---reference-guide"></a>Raccomandazioni su calcolo e app-Guida di riferimento

Questo articolo fornisce l'elenco completo dei suggerimenti che è possibile vedere nel centro sicurezza di Azure per quanto riguarda i tipi di risorse seguenti:

* VM e computer
* Set di scalabilità VM
* Servizi cloud
* Servizi app
* Contenitori
* Risorse di calcolo

Per una spiegazione di come trovare questi e come risolverli, vedere [qui](security-center-virtual-machine-protection.md).

## Raccomandazioni su calcolo e app<a name="compute-and-app-recs"></a>
|Tipo di risorsa|Punteggio di sicurezza|Recommendation|Description|
|----|----|----|----|
|Servizio app|20|L'applicazione Web deve essere accessibile solo tramite HTTPS|Limitare l'accesso delle applicazioni Web solo tramite HTTPS.|
|Servizio app|20|L'app per le funzioni deve essere accessibile solo tramite HTTPS|Limitare l'accesso delle app per le funzioni solo tramite HTTPS.|
|Servizio app|5|I log di diagnostica nei servizi app devono essere abilitati|Abilitare i log e conservarli per un periodo massimo di un anno. Questo consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|Servizio app|10|Il debug remoto deve essere disattivato per l'applicazione Web|Disattivare il debug per le applicazioni Web se non è più necessario. Il debug remoto richiede che vengano aperte le porte in ingresso in un'app per le funzioni.|
|Servizio app|10|Il debug remoto deve essere disattivato per l'app per le funzioni|Disattivare il debug per l'app per le funzioni se non è più necessario. Il debug remoto richiede che vengano aperte le porte in ingresso in un'app per le funzioni.|
|Servizio app|10|Do not allow all ('*') resources to access your application (Non consentire a tutte le risorse ('*') di accedere all'applicazione)| Non consentire l'impostazione del parametro WEBSITE_LOAD_CERTIFICATES su "". L'impostazione del parametro su " comporta che tutti i certificati verranno caricati nell'archivio certificati personale delle applicazioni Web. Ciò può causare un abuso del principio dei privilegi minimi poiché è improbabile che il sito necessiti dell'accesso a tutti i certificati in fase di esecuzione.|
|Servizio app|20|CORS non deve consentire a tutte le risorse di accedere alle applicazioni Web|Consentire solo ai domini richiesti di interagire con l'applicazione Web. Condivisione di risorse tra le origini (CORS) non deve consentire a tutti i domini di accedere all'applicazione Web.|
|Servizio app|20|Condivisione risorse tra le origini non deve consentire a tutte le risorse di accedere all'app per le funzioni dell'utente| Consentire solo ai domini richiesti di interagire con l'app per le funzioni. Condivisione di risorse tra le origini (CORS) non deve consentire a tutti i domini di accedere all'applicazione per le funzioni.|
|Risorse di calcolo (Batch)|1|Le regole di avviso delle metriche devono essere configurate negli account batch|Configurare regole di avviso relative alle metriche nell'account Batch e abilitare le metriche Eventi eliminazione pool completati ed Eventi eliminazione pool avviati|
|Risorse di calcolo (Service Fabric)|10|I cluster di Service Fabric deve usare solo Azure Active Directory per l'autenticazione client|Eseguire l'autenticazione client solo tramite Azure Active Directory in Service Fabric.|
|Risorse di calcolo (account di automazione)|5|Le variabili dell'account di automazione devono essere crittografate|Abilitare la crittografia degli asset della variabile dell'account di Automazione quando si archiviano dati sensibili.|
|Risorse di calcolo (Ricerca)|5|Controllare l'abilitazione dei log di diagnostica per i servizi di ricerca|Abilitare i log e conservarli per un periodo massimo di un anno. Questo consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|Risorse di calcolo (bus di servizio)|5|I log di diagnostica nel bus di servizio devono essere abilitati|Abilitare i log e conservarli per un periodo massimo di un anno. Questo consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|Calcolo delle risorse (Analisi di flusso)|5|I log di diagnostica in analisi di flusso di Azure devono essere abilitati|Abilitare i log e conservarli per un periodo massimo di un anno. Questo consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|Risorse di calcolo (Batch)|5|Abilitare i log di diagnostica negli account Batch|Abilitare i log e conservarli per un periodo massimo di un anno. Questo consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|Risorse di calcolo (Hub eventi)|5|I log di diagnostica nell'hub eventi devono essere abilitati|Abilitare i log e conservarli per un periodo massimo di un anno. Questo consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|Risorse di calcolo (App per la logica)|5|Abilitare i log di diagnostica in App per la logica|Abilitare i log e conservarli per un periodo massimo di un anno. Questo consente di ricreare la traccia delle attività per scopi di analisi quando si verifica un evento imprevisto della sicurezza o la rete viene compromessa. |
|Risorse di calcolo (Service Fabric)|15|Impostare la proprietà ClusterProtectionLevel su EncryptAndSign in Service Fabric|Service Fabric offre tre livelli di protezione (None, Sign ed EncryptAndSign) per la comunicazione da nodo a nodo mediante un certificato cluster primario. Impostare il livello di protezione per garantire che tutti i messaggi da nodo a nodo vengano crittografati e firmati digitalmente. |
|Risorse di calcolo (bus di servizio)|1|Rimuovere tutte le regole di autorizzazione ad eccezione di RootManageSharedAccessKey dallo spazio dei nomi del bus di servizio |I client del bus di servizio non devono usare un criterio di accesso a livello di spazio dei nomi che assicura l'accesso a tutte le code e gli argomenti in uno spazio dei nomi. Per allinearsi al modello di sicurezza con privilegi minimi, è opportuno creare criteri di accesso a livello di entità per code e argomenti per assicurare l'accesso solo all'entità specifica.|
|Risorse di calcolo (Hub eventi)|1|Tutte le regole di autorizzazione eccetto RootManageSharedAccessKey devono essere rimosse dallo spazio dei nomi dell'hub eventi|I client di Hub eventi non devono usare un criterio di accesso a livello di spazio dei nomi che assicura l'accesso a tutte le code e gli argomenti in uno spazio dei nomi. Per allinearsi al modello di sicurezza con privilegi minimi, è opportuno creare criteri di accesso a livello di entità per code e argomenti per assicurare l'accesso solo all'entità specifica.|
|Risorse di calcolo (Hub eventi)|5|È necessario definire le regole di autorizzazione nell'entità dell'hub eventi|Controllare regole di autorizzazione nell'entità di Hub eventi per concedere l'accesso con privilegi minimi.|
|Machine|50|Installare l'agente di monitoraggio nei computer|Installare l'agente di monitoraggio per abilitare la raccolta di dati, la ricerca di aggiornamenti, l'analisi della baseline e la protezione degli endpoint in ogni computer.|
|Machine|50|Abilitare il provisioning automatico e la raccolta dei dati per le sottoscrizioni |Abilitare il provisioning automatico e la raccolta dei dati per i computer nelle sottoscrizioni per abilitare la raccolta dei dati, la ricerca di aggiornamenti, l'analisi della baseline e la protezione degli endpoint in ogni computer aggiunto alle sottoscrizioni.|
|Machine|40|Risolvere i problemi di integrità dell'agente di monitoraggio nei computer|Per la protezione completa del Centro sicurezza, risolvere i problemi dell'agente di monitoraggio nei computer seguendo le istruzioni nella Guida alla risoluzione dei problemi| 
|Machine|40|Risolvere i problemi di integrità della protezione degli endpoint nei computer|Per la protezione completa del Centro sicurezza, risolvere i problemi dell'agente di monitoraggio nei computer seguendo le istruzioni nella Guida alla risoluzione dei problemi.|
|Machine|40|Risolvere il problema dei dati di analisi mancanti nei computer|Risolvere il problema dei dati di analisi mancanti nelle macchine virtuali e nei computer. L'assenza di dati di analisi nei computer determina l'impossibilità di usufruire di valutazioni sulla sicurezza, ad esempio la ricerca di aggiornamenti, l'analisi della baseline e la ricerca di soluzioni di protezione degli endpoint.|
|Machine|40|Gli aggiornamenti di sistema devono essere installati nelle macchine|Installare gli aggiornamenti mancanti per la sicurezza del sistema e critici per proteggere i computer e le macchine virtuali Windows e Linux
|Machine|15|Aggiungere un Web Application Firewall| Aggiungere una soluzione WAF (web application firewall) per proteggere le applicazioni Web. |
|Machine|40|Aggiornare la versione del sistema operativo per i ruoli del servizio cloud|Aggiornare la versione del sistema operativo per i ruoli del servizio cloud alla versione più recente disponibile per la famiglia del sistema operativo.|
|Machine|35|Le vulnerabilità nella configurazione di sicurezza delle macchine devono essere risolte|Risolvere le vulnerabilità nella configurazione della sicurezza dei computer per proteggerli da attacchi.|
|Machine|35|Risolvere le vulnerabilità nella configurazione della sicurezza nei contenitori|Risolvere le vulnerabilità nella configurazione della sicurezza nei computer in cui è installato Docker per proteggerli da attacchi.|
|Machine|25|Abilitare i controlli applicazioni adattivi|Abilitare il controllo delle applicazioni per controllare quali applicazioni possono essere eseguite nelle macchine virtuali in Azure. Sarà così possibile rafforzare la protezione delle macchine virtuali dai malware. Il Centro sicurezza usa l'apprendimento automatico per analizzare le applicazioni in esecuzione in ogni macchina virtuale e, grazie a questa funzionalità intelligente, consente di applicare regole di autorizzazione. Questa funzionalità semplifica il processo di configurazione e gestione delle regole di autorizzazione delle applicazioni.|
|Machine|20|Installare la soluzione di protezione degli endpoint nei computer|Installare una soluzione di protezione degli endpoint nelle macchine virtuali per proteggerli da minacce e vulnerabilità.|
|Machine|20|Riavviare i computer per applicare gli aggiornamenti del sistema|Riavviare i computer per applicare gli aggiornamenti del sistema e proteggere i computer dalle vulnerabilità.|
|Machine|15|La crittografia del disco deve essere applicata nelle macchine virtuali|Crittografare i dischi delle macchine virtuali con Crittografia dischi di Azure per macchine virtuali Windows e Linux. Crittografia dischi di Azure sfrutta la funzionalità standard di settore BitLocker di Windows e la funzionalità DM-Crypt di Linux per offrire la crittografia del sistema operativo e del disco dati per proteggere e salvaguardare i dati e rispettare gli impegni in termini di sicurezza e conformità dell'organizzazione nell'insieme di credenziali delle chiavi di Azure del cliente. Se il requisito di conformità e sicurezza prevede la crittografia dei dati end-to-end tramite le chiavi di crittografia, tra cui la crittografia del disco temporaneo collegato in locale, usare Crittografia dischi di Azure. In alternativa, per impostazione predefinita, il servizio Managed Disks viene crittografato con dischi inattivi per impostazione predefinita usando la crittografia del servizio di archiviazione di Azure in cui le chiavi di crittografia sono chiavi gestite da Microsoft in Azure. Se questa condizione soddisfa i requisiti di sicurezza e conformità, è possibile usare la crittografia di Managed Disks predefinita.|
|Machine|30|Installare una soluzione di valutazione della vulnerabilità nelle macchine virtuali|Installare una soluzione di valutazione della vulnerabilità nelle macchine virtuali|
|Machine|15|Aggiungere un Web Application Firewall| Aggiungere una soluzione WAF (web application firewall) per proteggere le applicazioni Web. |
|Machine|30|Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità|Le macchine virtuali per cui viene distribuita una soluzione di terze parti per la valutazione della vulnerabilità vengono costantemente controllate per individuare vulnerabilità del sistema operativo e delle applicazioni. Ogni volta che vengono individuate queste vulnerabilità, sono disponibili altre informazioni nell'ambito dell'indicazione.|
|Machine|30|Installare una soluzione di valutazione della vulnerabilità nelle macchine virtuali|Installare una soluzione di valutazione della vulnerabilità nelle macchine virtuali|
|Machine|1|È necessario eseguire la migrazione delle macchine virtuali a nuove risorse AzureRM|Usare Azure Resource Manager per le macchine virtuali per offrire miglioramenti alla sicurezza, ad esempio il controllo degli accessi più sicuro (RBAC), il controllo migliore, la distribuzione e la governance basati su Gestione risorse, l'accesso alle identità gestite, l'accesso a Key Vault per i segreti, Autenticazione basata su Azure AD e supporto per i tag e i gruppi di risorse per semplificare la gestione della sicurezza. |
|Machine|30|Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità|Le macchine virtuali per cui viene distribuita una soluzione di terze parti per la valutazione della vulnerabilità vengono costantemente controllate per individuare vulnerabilità del sistema operativo e delle applicazioni. Ogni volta che vengono individuate queste vulnerabilità, sono disponibili altre informazioni nell'ambito dell'indicazione.|
|Set di scalabilità di macchine virtuali |4|I log di diagnostica nei set di scalabilità di macchine virtuali devono essere abilitati|Abilitare i log e conservarli per un periodo massimo di un anno. In questo modo è possibile ricreare la traccia delle attività per scopi di analisi. Ciò è utile quando si verifica un evento imprevisto di sicurezza o la rete è compromessa.|
|Set di scalabilità di macchine virtuali|35|Le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali devono essere risolte|Risolvere le vulnerabilità nella configurazione di sicurezza dei set di scalabilità di macchine virtuali per proteggerli da attacchi. |
|Set di scalabilità di macchine virtuali|5|Risolvere i problemi di integrità della protezione degli endpoint nei set di scalabilità di macchine virtuali|Risolvere i problemi di integrità della protezione degli endpoint nei set di scalabilità di macchine virtuali per proteggerli da minacce e vulnerabilità. |
|Set di scalabilità di macchine virtuali|10|Endpoint Protection deve essere installato nelle macchine virtuali|Installare una soluzione di protezione degli endpoint nei set di scalabilità di macchine virtuali per proteggerli da minacce e vulnerabilità. |
|Set di scalabilità di macchine virtuali|40|Gli aggiornamenti di sistema nei set di scalabilità di macchine virtuali devono essere installati|Installare gli aggiornamenti mancanti per la sicurezza del sistema e critici per proteggere i set di scalabilità di macchine virtuali Windows e Linux. |
|


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle raccomandazioni applicabili ad altri tipi di risorse di Azure, vedere gli argomenti seguenti:

* [Monitoraggio dell'identità e dell'accesso nel Centro sicurezza di Azure](security-center-identity-access.md)
* [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md)
* [Protezione del servizio SQL di Azure nel Centro sicurezza di Azure](security-center-sql-service-recommendations.md)
