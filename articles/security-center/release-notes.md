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
ms.date: 08/12/2020
ms.author: memildin
ms.openlocfilehash: f3aeccd30a9c89c2a43dfb85d4a57274037ec05f
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89569255"
---
# <a name="whats-new-in-azure-security-center"></a>Novità del Centro sicurezza di Azure

La sicurezza di Azure è in fase di sviluppo attivo e riceve miglioramenti su base continuativa. Per stare al passo con gli sviluppi più recenti, questa pagina fornisce informazioni sugli argomenti seguenti:

- Nuove funzionalità
- Correzioni di bug
- Funzionalità deprecate

Poiché questa pagina viene aggiornata regolarmente, è consigliabile consultarla spesso. Se si cercano informazioni precedenti agli ultimi sei mesi, è possibile trovarle in [Archive for What's new in Azure Security Center](release-notes-archive.md) (Archivio per le novità nel Centro sicurezza di Azure).


## <a name="september-2020"></a>Settembre 2020

Gli aggiornamenti di settembre includono:

- [I risultati della valutazione della vulnerabilità sono ora disponibili nell'esportazione continua](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Impedisci configurazioni di sicurezza imponendo consigli per la creazione di nuove risorse](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Raccomandazioni del gruppo di sicurezza di rete migliorate](#network-security-group-recommendations-improved)
- [Raccomandazione AKS di anteprima deprecata "i criteri di sicurezza di Pod devono essere definiti nei servizi Kubernetes"](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [Notifiche tramite posta elettronica dal centro sicurezza di Azure migliorata](#email-notifications-from-azure-security-center-improved)
- [Il Punteggio sicuro non include le raccomandazioni per l'anteprima](#secure-score-doesnt-include-preview-recommendations)
- [Le raccomandazioni includono ora un indicatore di gravità e l'intervallo di aggiornamento](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)

### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>I risultati della valutazione della vulnerabilità sono ora disponibili nell'esportazione continua

Usare l'esportazione continua per trasmettere avvisi e consigli in tempo reale a hub eventi di Azure, aree di lavoro Log Analytics o monitoraggio di Azure. Da qui è possibile integrare questi dati con SIEMs (ad esempio, Sentinel di Azure, Power BI, Azure Esplora dati e altro ancora.

Gli strumenti di valutazione della vulnerabilità integrata del Centro sicurezza restituiscono risultati sulle risorse come raccomandazioni praticabili in una raccomandazione "padre", ad esempio "le vulnerabilità nelle macchine virtuali devono essere risolte". 

I risultati di sicurezza sono ora disponibili per l'esportazione tramite esportazione continua quando si selezionano le raccomandazioni e si Abilita l'opzione **Includi risultati di sicurezza** .

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Includi risultati di sicurezza/Nascondi nella configurazione dell'esportazione continua" :::

Pagine correlate:

- [Soluzione di valutazione della vulnerabilità integrata del Centro sicurezza per le macchine virtuali di Azure](deploy-vulnerability-assessment-vm.md)
- [Soluzione integrata di valutazione della vulnerabilità del Centro sicurezza per immagini di Azure Container Registry](monitor-container-security.md)
- [Esportazione continua](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Impedisci configurazioni di sicurezza imponendo consigli per la creazione di nuove risorse

Le configurazioni non configurate per la sicurezza sono una delle principali cause degli incidenti di sicurezza. Il Centro sicurezza offre ora la possibilità di *evitare* configurazioni errate delle nuove risorse per quanto concerne raccomandazioni specifiche. 

Questa funzionalità può aiutare a proteggere i carichi di lavoro e stabilizzare il Punteggio sicuro.

L'applicazione di una configurazione sicura, basata su una raccomandazione specifica, viene offerta in due modalità:

- Usando l'effetto di **negazione** di criteri di Azure, è possibile arrestare la creazione di risorse non integre.

- Con l'opzione **Imponi** è possibile sfruttare l'effetto **DeployIfNotExist** di criteri di Azure e correggere automaticamente le risorse non conformi al momento della creazione
 
Questa opzione è disponibile per le raccomandazioni di sicurezza selezionate e si trova nella parte superiore della pagina dei dettagli delle risorse.

Per altre informazioni, vedere [Impedisci configurazioni errate con le raccomandazioni Imponi/nega](prevent-misconfigurations.md).

###  <a name="network-security-group-recommendations-improved"></a>Raccomandazioni del gruppo di sicurezza di rete migliorate

Sono state migliorate le seguenti raccomandazioni di sicurezza relative ai gruppi di sicurezza di rete per ridurre alcune istanze di falsi positivi.

- È consigliabile limitare tutte le porte di rete nei gruppi di sicurezza di rete associati alla VM
- È consigliabile chiudere le porte di gestione nelle macchine virtuali
- Le macchine virtuali con connessione Internet devono essere protette con i gruppi di sicurezza di rete
- Le subnet devono essere associate a un gruppo di sicurezza di rete


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Raccomandazione AKS di anteprima deprecata "i criteri di sicurezza di Pod devono essere definiti nei servizi Kubernetes"

La raccomandazione di anteprima "i criteri di sicurezza di pod da definire nei servizi Kubernetes" è stata deprecata come descritto nella documentazione del [servizio Kubernetes di Azure](https://docs.microsoft.com/azure/aks/use-pod-security-policies) .

La funzionalità criteri di sicurezza pod (anteprima) è impostata per la deprecazione e non sarà più disponibile dopo il 15 ottobre 2020 a favore di criteri di Azure per AKS.

Dopo che i criteri di sicurezza pod (anteprima) sono deprecati, è necessario disabilitare la funzionalità in tutti i cluster esistenti usando la funzionalità deprecata per eseguire gli aggiornamenti futuri del cluster e rimanere nel supporto tecnico di Azure.


### <a name="email-notifications-from-azure-security-center-improved"></a>Notifiche tramite posta elettronica dal centro sicurezza di Azure migliorata

Sono state migliorate le seguenti aree dei messaggi di posta elettronica relativi agli avvisi di sicurezza: 

- Aggiunta della possibilità di inviare notifiche tramite posta elettronica sugli avvisi per tutti i livelli di gravità
- Aggiunta della possibilità di inviare notifiche agli utenti con ruoli RBAC diversi nella sottoscrizione
- Per impostazione predefinita, i proprietari delle sottoscrizioni vengono notificati in modo proattivo agli avvisi con gravità elevata, che hanno una probabilità elevata di violazioni autentiche.
- Il campo numero di telefono è stato rimosso dalla pagina di configurazione delle notifiche di posta elettronica

Per altre informazioni, vedere [configurare le notifiche di posta elettronica per gli avvisi di sicurezza](security-center-provide-security-contact-details.md).


### <a name="secure-score-doesnt-include-preview-recommendations"></a>Il Punteggio sicuro non include le raccomandazioni per l'anteprima 

Il Centro sicurezza valuta continuamente risorse, sottoscrizioni e organizzazione per rilevare problemi di sicurezza. Aggrega quindi tutti i risultati in un singolo punteggio, in modo da poter indicare, a colpo d'occhio, lo stato di sicurezza attuale: maggiore è il punteggio, minore è il livello di rischio identificato.

Quando vengono individuate nuove minacce, i nuovi consigli sulla sicurezza vengono resi disponibili nel centro sicurezza attraverso nuove raccomandazioni. Per evitare che le modifiche sorprendano il Punteggio sicuro e per fornire un periodo di tolleranza in cui è possibile esplorare le nuove raccomandazioni prima che influiscano sui punteggi, le raccomandazioni contrassegnate come **Anteprima** non sono più incluse nei calcoli del Punteggio sicuro. Dovrebbero comunque essere corretti, laddove possibile, in modo che, al termine del periodo di anteprima, contribuiscano al punteggio.

Inoltre, le raccomandazioni di **Anteprima** non eseguono il rendering di una risorsa "non integro".

Esempio di raccomandazione per l'anteprima:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Raccomandazione con il flag di anteprima":::

[Altre informazioni sul punteggio sicuro](secure-score-security-controls.md).


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>Le raccomandazioni includono ora un indicatore di gravità e l'intervallo di aggiornamento

La pagina dei dettagli per le indicazioni include ora un indicatore di intervallo di aggiornamento (se pertinente) e una visualizzazione chiara della gravità dell'indicazione.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Pagina di raccomandazione che mostra la validità e la gravità":::



## <a name="august-2020"></a>Agosto 2020

Gli aggiornamenti di agosto includono:

- [Inventario asset: nuova e potente visualizzazione del comportamento di sicurezza degli asset](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Aggiunta del supporto per le impostazioni predefinite di sicurezza Azure Active Directory (per l'autenticazione a più fattori)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Aggiunta raccomandazione entità servizio](#service-principals-recommendation-added)
- [Valutazione della vulnerabilità sulle macchine virtuali-raccomandazioni e criteri consolidati](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Nuovi criteri di sicurezza AKS aggiunti a ASC_default Initiative: per l'uso da soli clienti con anteprima privata](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Inventario asset: nuova e potente visualizzazione del comportamento di sicurezza degli asset

Inventario asset del Centro sicurezza (attualmente in anteprima) consente di visualizzare il comportamento di sicurezza delle risorse connesse al centro sicurezza.

Il Centro sicurezza analizza periodicamente lo stato di sicurezza delle risorse di Azure per identificare le potenziali vulnerabilità di sicurezza. Vengono quindi fornite indicazioni su come correggere tali vulnerabilità. Quando una risorsa presenta raccomandazioni in attesa, verranno visualizzate nell'inventario.

È possibile usare la visualizzazione e i relativi filtri per esplorare i dati relativi alle posture di sicurezza e intraprendere ulteriori azioni in base alle proprie scoperte.

Altre informazioni sull' [inventario degli asset](asset-inventory.md).


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Aggiunta del supporto per le impostazioni predefinite di sicurezza Azure Active Directory (per l'autenticazione a più fattori)

Il Centro sicurezza ha aggiunto il supporto completo per le [impostazioni predefinite di sicurezza](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults), le protezioni Microsoft per la sicurezza delle identità gratuite.

Le impostazioni predefinite di sicurezza forniscono impostazioni di sicurezza delle identità preconfigurate per difendere l'organizzazione da attacchi comuni relativi all'identità. Impostazioni predefinite per la sicurezza che proteggono già più di 5 milioni tenant generali; 50.000 i tenant sono protetti anche dal centro sicurezza.

Il Centro sicurezza offre ora una raccomandazione di sicurezza ogni volta che identifica una sottoscrizione di Azure senza che siano abilitate le impostazioni predefinite di sicurezza. Fino ad ora, il Centro sicurezza consiglia di abilitare la funzionalità di autenticazione a più fattori tramite l'accesso condizionale, che fa parte della licenza di Azure Active Directory (AD) Premium. Per i clienti che usano Azure AD gratuita, è ora consigliabile abilitare le impostazioni predefinite di sicurezza. 

Il nostro obiettivo è incoraggiare più clienti a proteggere gli ambienti cloud con l'autenticazione a più fattori e mitigare uno dei rischi più elevati che è anche il più significativo per il [Punteggio sicuro](https://docs.microsoft.com/azure/security-center/secure-score-security-controls).

Altre informazioni sulle [impostazioni predefinite di sicurezza](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults).


### <a name="service-principals-recommendation-added"></a>Aggiunta raccomandazione entità servizio

È stata aggiunta una nuova raccomandazione per consigliare che i clienti del Centro sicurezza che usano i certificati di gestione per gestire le sottoscrizioni passano a entità servizio.

Si consiglia di utilizzare le **entità servizio per proteggere le sottoscrizioni anziché i certificati di gestione** . si consiglia di utilizzare le entità servizio o Azure Resource Manager per gestire in modo più sicuro le sottoscrizioni. 

Altre informazioni sugli [oggetti applicazione e gli oggetti entità servizio sono disponibili in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Valutazione della vulnerabilità sulle macchine virtuali-raccomandazioni e criteri consolidati

Il Centro sicurezza controlla le VM per rilevare se sta eseguendo una soluzione di valutazione della vulnerabilità. Se non viene trovata alcuna soluzione di valutazione della vulnerabilità, il Centro sicurezza fornisce una raccomandazione per semplificare la distribuzione.

Quando vengono rilevate vulnerabilità, il Centro sicurezza fornisce una raccomandazione che riepiloga i risultati da analizzare e correggere se necessario.

Per garantire un'esperienza coerente a tutti gli utenti, indipendentemente dal tipo di scanner usato, abbiamo unificato quattro consigli nei due seguenti elementi:

|Raccomandazione unificata|Descrizione delle modifiche|
|----|:----|
|**Una soluzione di valutazione della vulnerabilità dovrebbe essere abilitata nelle macchine virtuali**|Sostituisce le due raccomandazioni seguenti:<br> **•** Abilitare la soluzione di valutazione della vulnerabilità incorporata nelle macchine virtuali (con tecnologia Qualys (ora deprecata) (inclusa nel livello standard)<br> **•** La soluzione di valutazione della vulnerabilità deve essere installata nelle macchine virtuali (ora deprecate) (livelli standard e gratuito)|
|**È necessario correggere le vulnerabilità nelle macchine virtuali**|Sostituisce le due raccomandazioni seguenti:<br>**•** Correggere le vulnerabilità rilevate nelle macchine virtuali (basate su Qualys) (ora deprecate)<br>**•** Le vulnerabilità devono essere risolte da una soluzione di valutazione della vulnerabilità (ora deprecata)|
|||

A questo punto si userà la stessa raccomandazione per distribuire l'estensione di valutazione della vulnerabilità del Centro sicurezza o una soluzione con licenza privata ("BYOL") da un partner, ad esempio Qualys o Rapid7.

Inoltre, quando vengono rilevate e segnalate vulnerabilità al centro sicurezza, una singola raccomandazione segnala i risultati indipendentemente dalla soluzione di valutazione della vulnerabilità che li ha identificati.

#### <a name="updating-dependencies"></a>Aggiornamento delle dipendenze

Se si dispone di script, query o automazione che fanno riferimento ai consigli precedenti o alle chiavi/nomi dei criteri, utilizzare le tabelle seguenti per aggiornare i riferimenti:

##### <a name="before-august-2020"></a>Prima del 2020 agosto

|Recommendation|Scope|
|----|:----|
|**Abilitare la soluzione di valutazione della vulnerabilità incorporata nelle macchine virtuali (con tecnologia Qualys)**<br>Chiave: 550e890b-E652-4D22-8274-60b3bdb24c63|Predefinito|
|**Correggere le vulnerabilità rilevate nelle macchine virtuali (con tecnologia Qualys)**<br>Chiave: 1195afff-c881-495E-9bc5-1486211ae03f|Predefinito|
|**È consigliabile installare una soluzione di valutazione della vulnerabilità nelle macchine virtuali**<br>Chiave: 01b1ed4c-B733-4fee-B145-f23236e70cf3|BYOL|
|**Le vulnerabilità devono essere risolte tramite una soluzione di valutazione della vulnerabilità**<br>Chiave: 71992a2a-D168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|Criteri|Scope|
|----|:----|
|**La soluzione Valutazione della vulnerabilità deve essere abilitata nelle macchine virtuali**<br>ID criterio: 501541f7-f7e7-4cd6-868C-4190fdad3ac9|Predefinito|
|**Le vulnerabilità devono essere risolte da una soluzione di valutazione della vulnerabilità**<br>ID criterio: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>Da agosto 2020

|Recommendation|Scope|
|----|:----|
|**Una soluzione di valutazione della vulnerabilità dovrebbe essere abilitata nelle macchine virtuali**<br>Chiave: ffff0522-1e88-47FC-8382-2a80ba848f5d|Incorporato + BYOL|
|**È necessario correggere le vulnerabilità nelle macchine virtuali**<br>Chiave: 1195afff-c881-495E-9bc5-1486211ae03f|Incorporato + BYOL|
||||

|Criteri|Scope|
|----|:----|
|[**La valutazione della vulnerabilità deve essere abilitata nelle macchine virtuali**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>ID criterio: 501541f7-f7e7-4cd6-868C-4190fdad3ac9 |Incorporato + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Nuovi criteri di sicurezza AKS aggiunti a ASC_default Initiative: per l'uso da soli clienti con anteprima privata

Per assicurarsi che i carichi di lavoro Kubernetes siano protetti per impostazione predefinita, il Centro sicurezza aggiunge i criteri a livello di Kubernetes e le raccomandazioni per la protezione avanzata, incluse le opzioni di imposizione con il controllo di ammissione Kubernetes.

La fase iniziale di questo progetto include un'anteprima privata e l'aggiunta di nuovi criteri (disabilitati per impostazione predefinita) al ASC_default Initiative.

È possibile ignorare questi criteri in modo sicuro e non vi sarà alcun impatto sull'ambiente. Se si vuole abilitarli, iscriversi per l'anteprima in https://aka.ms/SecurityPrP e selezionare una delle opzioni seguenti:

1. **Anteprima singola** : per partecipare solo a questa anteprima privata. Specificare in modo esplicito "ASC Continuous Scan" come anteprima che si desidera aggiungere.
1. **Programma in corso** : da aggiungere a questa e alle anteprime private future. Sarà necessario completare un profilo e un contratto di privacy.


## <a name="july-2020"></a>Luglio 2020

Gli aggiornamenti di luglio includono:
- [La valutazione della vulnerabilità per le macchine virtuali è ora disponibile per le immagini non del Marketplace](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Protezione dalle minacce per archiviazione di Azure espansa per includere File di Azure e Azure Data Lake Storage Gen2 (anteprima)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Otto nuove raccomandazioni per abilitare le funzionalità di protezione dalle minacce](#eight-new-recommendations-to-enable-threat-protection-features)
- [Miglioramenti della sicurezza del contenitore: analisi più veloce del registro di sistema e documentazione aggiornata](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Controlli applicazione adattivi aggiornati con una nuova raccomandazione e supporto per i caratteri jolly nelle regole di percorso](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Sono stati deprecati sei criteri per la sicurezza dei dati avanzata SQL](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>La valutazione della vulnerabilità per le macchine virtuali è ora disponibile per le immagini non del Marketplace

Quando si distribuisce una soluzione di valutazione della vulnerabilità, il Centro sicurezza ha precedentemente eseguito un controllo di convalida prima della distribuzione. Il controllo è stato per confermare uno SKU del Marketplace della macchina virtuale di destinazione. 

Da questo aggiornamento, il controllo è stato rimosso ed è ora possibile distribuire gli strumenti di valutazione della vulnerabilità ai computer Windows e Linux "personalizzati". Le immagini personalizzate sono quelle modificate dalle impostazioni predefinite del Marketplace.

Sebbene sia ora possibile distribuire l'estensione Integrated vulnerabilità Assessment (basata su Qualys) in molti più computer, il supporto è disponibile solo se si usa un sistema operativo elencato in [distribuire lo scanner di vulnerabilità integrato in macchine virtuali di livello standard](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-vulnerability-scanner-to-standard-tier-vms) .

Scopri di più sullo [scanner di vulnerabilità integrato per le macchine virtuali (solo livello standard)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).

Per altre informazioni sull'uso della soluzione di valutazione della vulnerabilità con licenza privata da Qualys o Rapid7, vedere [distribuzione di una soluzione di analisi delle vulnerabilità dei partner](deploy-vulnerability-assessment-vm.md).


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Protezione dalle minacce per archiviazione di Azure espansa per includere File di Azure e Azure Data Lake Storage Gen2 (anteprima)

La protezione dalle minacce per archiviazione di Azure rileva attività potenzialmente dannose negli account di archiviazione di Azure. Il Centro sicurezza Visualizza gli avvisi quando rileva i tentativi di accesso o di exploit degli account di archiviazione. 

I dati possono essere protetti indipendentemente dal fatto che vengano archiviati come contenitori BLOB, condivisioni file o data Lake. 

Altre informazioni sulla [protezione dalle minacce per archiviazione di Azure](threat-protection.md#threat-protection-for-azure-storage-).




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Otto nuove raccomandazioni per abilitare le funzionalità di protezione dalle minacce

Sono state aggiunte otto nuove raccomandazioni per fornire un modo semplice per abilitare le funzionalità di protezione dalle minacce del Centro sicurezza di Azure per i tipi di risorsa seguenti: macchine virtuali, piani di servizio app, server di database SQL di Azure, SQL Server in computer, account di archiviazione di Azure, cluster di servizi Kubernetes di Azure, registri di Azure Container Registry e insiemi di credenziali Azure Key Vault.

I nuovi consigli sono:

- **La soluzione Sicurezza dei dati avanzata deve essere abilitata nei server del database SQL di Azure**
- **La soluzione Sicurezza dei dati avanzata deve essere abilitata in SQL Server in macchine virtuali**
- **La soluzione Advanced Threat Protection deve essere abilitata nei piani di servizio app di Azure**
- **La soluzione Advanced Threat Protection deve essere abilitata nei registri in Registro Azure Container**
- **La soluzione Advanced Threat Protection deve essere abilitata negli insiemi di credenziali in Azure Key Vault**
- **La soluzione Advanced Threat Protection deve essere abilitata nei cluster del servizio Azure Kubernetes**
- **La soluzione Advanced Threat Protection deve essere abilitata negli account di archiviazione di Azure**
- **Advanced Threat Protection deve essere abilitato nelle macchine virtuali**

Queste nuove raccomandazioni appartengono al controllo di sicurezza **Enable Advanced Threat Protection** .

Le raccomandazioni includono inoltre la funzionalità di correzione rapida. 

> [!IMPORTANT]
> Il monitoraggio e l'aggiornamento di questi consigli comporteranno addebiti per la protezione delle risorse pertinenti. Questi addebiti inizieranno immediatamente se sono presenti risorse correlate nella sottoscrizione corrente. O in futuro, se vengono aggiunti in un secondo momento.
> 
> Se, ad esempio, non sono presenti cluster di servizi Kubernetes di Azure nella sottoscrizione e si Abilita la protezione dalle minacce, non verrà addebitato alcun costo. Se, in futuro, si aggiunge un cluster nella stessa sottoscrizione, esso verrà automaticamente protetto e gli addebiti inizieranno in quel momento.

Per altre informazioni, vedere la pagina di [riferimento](recommendations-reference.md)relativa alle raccomandazioni sulla sicurezza.

Scopri di più sulla [protezione dalle minacce nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/threat-protection).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Miglioramenti della sicurezza del contenitore: analisi più veloce del registro di sistema e documentazione aggiornata

Nell'ambito degli investimenti continui nel dominio di sicurezza del contenitore, siamo lieti di condividere un miglioramento significativo delle prestazioni delle analisi dinamiche del Centro sicurezza delle immagini del contenitore archiviate in Azure Container Registry. Le analisi vengono in genere completate in circa due minuti. In alcuni casi, potrebbero essere necessari fino a 15 minuti.

Per migliorare la chiarezza e le linee guida relative alle funzionalità di sicurezza del contenitore del Centro sicurezza di Azure, sono state aggiornate anche le pagine della documentazione sulla sicurezza dei contenitori. 

Altre informazioni sulla sicurezza del contenitore del Centro sicurezza sono disponibili negli articoli seguenti:

- [Panoramica delle funzionalità di sicurezza del contenitore del Centro sicurezza](https://docs.microsoft.com/azure/security-center/container-security)
- [Dettagli dell'integrazione con Container Registry di Azure](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration)
- [Dettagli dell'integrazione con il servizio Azure Kubernetes](https://docs.microsoft.com/azure/security-center/azure-kubernetes-service-integration)
- [Procedura: analizzare i registri e rafforzare gli host Docker](https://docs.microsoft.com/azure/security-center/monitor-container-security)
- [Avvisi di sicurezza dalle funzionalità di protezione dalle minacce per i cluster del servizio Kubernetes di Azure](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-akscluster)
- [Avvisi di sicurezza dalle funzionalità di protezione dalle minacce per gli host del servizio Kubernetes di Azure](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-containerhost)
- [Raccomandazioni sulla sicurezza per i contenitori](https://docs.microsoft.com/azure/security-center/recommendations-reference#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Controlli applicazione adattivi aggiornati con una nuova raccomandazione e supporto per i caratteri jolly nelle regole di percorso

La funzionalità controlli applicazione adattivi ha ricevuto due aggiornamenti significativi:

* Una nuova raccomandazione identifica un comportamento potenzialmente legittimo che non è stato precedentemente consentito. La nuova raccomandazione, le **regole di consenso nei criteri di controllo delle applicazioni adattivi devono essere aggiornate**. viene chiesto di aggiungere nuove regole al criterio esistente per ridurre il numero di falsi positivi negli avvisi di violazione dei controlli applicazione adattivi.

* Le regole di percorso ora supportano i caratteri jolly. Da questo aggiornamento è possibile configurare regole di percorso consentite utilizzando caratteri jolly. Esistono due scenari supportati:

    * Utilizzo di un carattere jolly alla fine di un percorso per consentire tutti i file eseguibili all'interno di questa cartella e sottocartelle

    * Utilizzando un carattere jolly all'interno di un percorso per abilitare un nome di eseguibile noto con un nome di cartella modificabile (ad esempio, cartelle utente personali con un eseguibile noto, nomi di cartella generati automaticamente e così via).


[Altre informazioni sull'applicazione di controlli applicazioni adattivi](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Sono stati deprecati sei criteri per la sicurezza dei dati avanzata SQL

Sei criteri correlati alla sicurezza avanzata dei dati per i computer SQL sono deprecati:

- I tipi Advanced Threat Protection devono essere impostati su "All" nelle impostazioni avanzate di sicurezza dei dati dell'istanza gestita di SQL
- I tipi Advanced Threat Protection devono essere impostati su' all'nelle impostazioni di sicurezza avanzata dei dati di SQL Server
- Le impostazioni avanzate di sicurezza dei dati per l'istanza gestita di SQL devono contenere un indirizzo di posta elettronica a cui ricevere gli avvisi di sicurezza
- Le impostazioni avanzate di sicurezza dei dati per SQL Server devono contenere un indirizzo di posta elettronica a cui ricevere gli avvisi di sicurezza
- Le notifiche tramite posta elettronica agli amministratori e ai proprietari della sottoscrizione devono essere abilitate nelle impostazioni di Sicurezza dei dati avanzata dell'istanza gestita di SQL
- Le notifiche tramite posta elettronica agli amministratori e ai proprietari della sottoscrizione devono essere abilitate nelle impostazioni di Sicurezza dei dati avanzata del server SQL

Altre informazioni sui [criteri predefiniti](security-center-policy-definitions.md).





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


|Obiettivo  |Criteri  |ID condizione  |
|---------|---------|---------|
|Esportazione continua nell'hub eventi|[Distribuisci esportazione in hub eventi per gli avvisi e le raccomandazioni del Centro sicurezza di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Esportazione continua nell'area di lavoro Log Analytics|[Distribuisci esportazione nell'area di lavoro Log Analytics per gli avvisi e le raccomandazioni del Centro sicurezza di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
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


| Criteri                                                                                                                                                                                                                                                                | ID condizione                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [La soluzione Sicurezza dei dati avanzata deve essere abilitata nei server del database SQL di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [La soluzione Sicurezza dei dati avanzata deve essere abilitata in SQL Server in macchine virtuali](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [La soluzione Advanced Threat Protection deve essere abilitata negli account di archiviazione di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [La soluzione Advanced Threat Protection deve essere abilitata negli insiemi di credenziali in Azure Key Vault](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [La soluzione Advanced Threat Protection deve essere abilitata nei piani di servizio app di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [La soluzione Advanced Threat Protection deve essere abilitata nei registri in Registro Azure Container](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [La soluzione Advanced Threat Protection deve essere abilitata nei cluster del servizio Azure Kubernetes](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [La soluzione Advanced Threat Protection deve essere abilitata nelle macchine virtuali](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
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

Sono disponibili altre informazioni sulla [valutazione delle vulnerabilità integrata del Centro sicurezza per le macchine virtuali](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Modifiche all'accesso JIT (just-in-time) alle macchine virtuali

Il Centro sicurezza include una funzionalità opzionale per proteggere le porte di gestione delle macchine virtuali. Questa funzionalità garantisce una difesa contro la forma più comune di attacchi di forza bruta.

Questo aggiornamento apporta le seguenti modifiche a questa funzionalità:

- La raccomandazione che consiglia di abilitare JIT in una macchina virtuale è stata rinominata. "Il controllo di accesso alla rete JIT deve essere applicato alle macchine virtuali" è ora diventato: "Le porte di gestione delle macchine virtuali devono essere protette tramite un controllo di accesso alla rete JIT".

- La raccomandazione viene attivata solo se sono presenti porte di gestione aperte.

Sono disponibili altre informazioni sulla [funzionalità di accesso JIT](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Le raccomandazioni personalizzate sono state spostate in un controllo di sicurezza distinto

Un controllo di sicurezza introdotto con il Punteggio sicuro migliorato è stato "implementare le procedure consigliate per la sicurezza". Eventuali raccomandazioni personalizzate create per le sottoscrizioni sono state inserite automaticamente nel controllo. 

Per semplificare l'individuazione delle raccomandazioni personalizzate, sono state spostate in un controllo di sicurezza dedicato, "Custom Recommendations" (Raccomandazioni personalizzate). Questo controllo non ha alcun effetto sul punteggio di sicurezza.

Altre informazioni sui controlli di sicurezza sono disponibili in [Enhanced secure score (preview) in Azure Security Center](secure-score-security-controls.md) (Punteggio di sicurezza migliorato (anteprima) nel Centro sicurezza di Azure).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Interruttore aggiunto per visualizzare le raccomandazioni nei controlli o come elenco semplice

I controlli di sicurezza sono gruppi logici di raccomandazioni correlate alla sicurezza. Riflettono le superfici di attacco vulnerabili. Un controllo è un set di raccomandazioni sulla sicurezza, con istruzioni che consentono di implementare tali raccomandazioni.

Per verificare immediatamente in che modo l'organizzazione protegge ogni singola superficie di attacco, esaminare i punteggi per ogni controllo di sicurezza.

Per impostazione predefinita, le raccomandazioni vengono visualizzate nei controlli di sicurezza. Da questo aggiornamento è inoltre possibile visualizzarli come elenco. Per visualizzarle come semplici elenchi ordinati in base allo stato di integrità delle risorse interessate, usare il nuovo interruttore di raggruppamento per controlli. L'interruttore è in cima all'elenco nel portale.

I controlli di sicurezza, e questo interruttore, fanno parte della nuova esperienza di assegnazione dei punteggi di sicurezza. Ricordarsi di inviare feedback dall'interno del portale.

Altre informazioni sui controlli di sicurezza sono disponibili in [Enhanced secure score (preview) in Azure Security Center](secure-score-security-controls.md) (Punteggio di sicurezza migliorato (anteprima) nel Centro sicurezza di Azure).

![Interruttore "Raggruppa per controlli" per le indicazioni](\media\secure-score-security-controls\recommendations-group-by-toggle.gif)

### <a name="expanded-security-control-implement-security-best-practices"></a>Controllo di sicurezza espanso "Implement security best practices" (Implementa procedure consigliate per la sicurezza) 

Un controllo di sicurezza introdotto con il Punteggio sicuro migliorato è "implementare le procedure consigliate per la sicurezza". Quando una raccomandazione si trova in questo controllo, non ha alcun effetto sul punteggio di sicurezza. 

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

Sono disponibili altre informazioni sull'[ottimizzazione delle raccomandazioni personalizzate con informazioni dettagliate](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information).



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