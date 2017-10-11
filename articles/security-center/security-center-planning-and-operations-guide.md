---
title: Guida alla pianificazione e alla gestione del Centro sicurezza di Azure | Documentazione Microsoft
description: Questo documento contiene indicazioni utili per l'adozione del Centro sicurezza di Azure e considerazioni relative alle operazioni quotidiane.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: f984e4a2-ac97-40bf-b281-2f7f473494c4
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: c502e4363dbaa37455d1aad90d1e9fa855fd09b0
ms.contentlocale: it-it
ms.lasthandoff: 06/17/2017

---
# <a name="azure-security-center-planning-and-operations-guide"></a>Guida alla pianificazione e alla gestione del Centro sicurezza di Azure
Questa guida è destinata a professionisti IT, architetti IT, analisti della sicurezza delle informazioni e amministratori cloud le cui organizzazioni intendano usare il Centro sicurezza di Azure.

>[!NOTE] 
>A partire dall'inizio di giugno 2017, il Centro sicurezza usa Microsoft Monitoring Agent per raccogliere e archiviare i dati. Per altre informazioni, vedere [Migrazione della piattaforma del Centro sicurezza di Azure](security-center-platform-migration.md). Le informazioni contenute in questo articolo si riferiscono alle funzionalità del Centro sicurezza dopo la transizione a Microsoft Monitoring Agent.
>

## <a name="planning-guide"></a>Guida alla pianificazione
Questa guida illustra una serie di passaggi e attività che è possibile eseguire per ottimizzare l'uso del Centro sicurezza in base ai requisiti di sicurezza e al modello di gestione cloud dell'organizzazione. Per sfruttare tutti i vantaggi del Centro sicurezza, è importante sapere come i vari utenti o team dell'organizzazione usano il servizio per soddisfare le esigenze di gestione e sviluppo, monitoraggio, governance e risposta agli eventi imprevisti in modo sicuro. Le aree principali da considerare quando si intende usare il Centro sicurezza sono:

* Ruoli di sicurezza e controlli di accesso
* Criteri di sicurezza e raccomandazioni
* Raccolta dati e archiviazione
* Monitoraggio continuo della sicurezza
* Risposta agli eventi imprevisti

Nella sezione successiva si apprenderà come pianificare ogni singola area e applicare le raccomandazioni in base alle esigenze.

> [!NOTE]
> Per un elenco delle domande comuni che possono risultare utili anche durante la fase di progettazione e di pianificazione, vedere [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) .
> 

## <a name="security-roles-and-access-controls"></a>Ruoli di sicurezza e controlli di accesso
A seconda delle dimensioni e della struttura dell'organizzazione, il Centro sicurezza di Azure potrebbe essere usato da più utenti e team per l'esecuzione di diverse attività correlate alla sicurezza. Nel diagramma seguente è riportato un esempio di utenti fittizi e dei rispettivi ruoli e responsabilità in materia di sicurezza:

![Ruoli](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-new.png)

Il Centro sicurezza consente a questi utenti di svolgere i relativi ruoli, Ad esempio:

**Jeff (proprietario del carico di lavoro cloud)**

* Gestisce un carico di lavoro cloud e le risorse correlate.
* È responsabile delle attività di implementazione e gestione della protezione secondo i criteri di sicurezza aziendale.

**Ellen (CISO/CIO)**

* È responsabile di tutti gli aspetti della sicurezza aziendale.
* Deve conoscere i comportamenti di sicurezza aziendale per i carichi di lavoro cloud.
* Ha l'esigenza di ricevere informazioni sui rischi e sugli attacchi principali.

**David (sicurezza IT)**

* Imposta i criteri di sicurezza aziendali per garantire l'applicazione della protezione appropriata.
* Monitora la conformità ai criteri.
* Genera report per la direzione o i revisori.

**Judy (attività di sicurezza)**

* Monitora e risponde agli avvisi di sicurezza 24x7.
* Si occupa dell'escalation al proprietario del carico di lavoro cloud o all'analista della sicurezza IT.

**Sam (analista della sicurezza)**

* Analizza gli attacchi
* Collabora con il proprietario del carico di lavoro cloud per applicare la correzione. 

Il Centro sicurezza usa il [controllo degli accessi in base al ruolo (RBAC)o](../active-directory/role-based-access-control-configure.md), che fornisce [ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md) assegnabili a utenti, gruppi e servizi in Azure. Quando un utente apre il Centro sicurezza, visualizza solo informazioni correlate alle risorse a cui ha accesso. All'utente viene infatti assegnato il ruolo Proprietario, Collaboratore o Lettore per la sottoscrizione o il gruppo di risorse a cui appartiene la risorsa. Oltre a questi ruoli, esistono due ruoli specifici del Centro sicurezza:

- **Ruolo con autorizzazioni di lettura per la sicurezza**: l'utente che appartiene a questo ruolo dispone dei diritti di visualizzazione per il Centro sicurezza, inclusi avvisi, criteri, raccomandazioni e integrità, ma non sarà in grado di apportare modifiche.
- **Amministratore della protezione**: come il ruolo con autorizzazioni di lettura per la sicurezza, ma è anche in grado di aggiornare i criteri di sicurezza e di ignorare raccomandazioni e avvisi.

I ruoli del Centro sicurezza descritti in precedenza non hanno accesso ad altre aree del servizio di Azure come Archiviazione, Web e dispositivi mobili o Internet delle cose.  

> [!NOTE]
> Un utente deve essere almeno proprietario di una sottoscrizione o di un gruppo di risorse oppure un collaboratore per poter visualizzare il Centro sicurezza di Azure. 
> 
> 

Con gli utenti tipo descritti nel diagramma precedente sarebbe necessario il controllo degli accessi in base al ruolo seguente:

**Jeff (proprietario del carico di lavoro cloud)**

* Proprietario del gruppo di risorse/Collaboratore.

**David (sicurezza IT)**

* Proprietario della sottoscrizione/Collaboratore o Amministratore della protezione

**Judy (attività di sicurezza)**

* Ruolo con autorizzazioni di lettura per la sottoscrizione o per la sicurezza per visualizzare gli avvisi
* Proprietario della sottoscrizione/Collaboratore o Amministratore della protezione per ignorare gli avvisi

**Sam (analista della sicurezza)**

* Ruolo con autorizzazioni di lettura per la sottoscrizione per visualizzare gli avvisi
* Proprietario della sottoscrizione/Collaboratore per ignorare gli avvisi
* Potrebbe essere richiesto l'accesso all'area di lavoro

Altre informazioni importanti da considerare:

* Solto i ruoli Proprietario/Collaboratore della sottoscrizione e Amministratore della protezione possono modificare i criteri di sicurezza.
* Solo i ruoli Proprietario e Collaboratore della sottoscrizione e del gruppo di risorse possono applicare le raccomandazioni relative alla sicurezza per una risorsa.

Quando si pianifica il controllo di accesso con il Controllo degli accessi in base al ruolo per il Centro sicurezza, è necessario sapere chi userà il Centro sicurezza nell'organizzazione. Si dovrà sapere anche quali tipi di attività eseguiranno e configurare di conseguenza il Controllo degli accessi in base al ruolo.

> [!NOTE]
> È consigliabile assegnare il ruolo con il minor numero di autorizzazioni che permetta agli utenti di completare le attività. Ad esempio, agli utenti che hanno solo necessità di visualizzare le informazioni sullo stato di sicurezza delle risorse senza intervenire, per applicare le raccomandazioni o modificare i criteri, deve essere assegnato il ruolo Lettore.
> 
> 

## <a name="security-policies-and-recommendations"></a>Criteri di sicurezza e raccomandazioni
I criteri di sicurezza definiscono il set di controlli consigliati per le risorse all'interno della sottoscrizione specificata. Nel Centro sicurezza è possibile definire i criteri in base ai requisiti di sicurezza della società e al tipo di applicazioni o al livello di riservatezza dei dati.

I criteri abilitati nel livello sottoscrizione vengono propagati automaticamente a tutti i gruppi di risorse all'interno della sottoscrizione, come illustrato nel diagramma seguente:

![Criteri di sicurezza](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig2-newUI.png)

> [!NOTE]
> Per esaminare i criteri modificati, è possibile usare i [log di controllo di Azure](https://blogs.msdn.microsoft.com/cloud_solution_architect/2015/03/10/audit-logs-for-azure-events/). Le modifiche ai criteri vengono sempre registrate nei log di controllo di Azure.
> 
> 

### <a name="security-recommendations"></a>Suggerimenti per la sicurezza
Prima di configurare i criteri di sicurezza, è consigliabile esaminare tutte le [raccomandazioni sulla sicurezza](security-center-recommendations.md)e determinare se sono appropriate per le varie sottoscrizioni e i vari gruppi di risorse usati. È importante sapere anche quali azioni devono essere eseguite in relazione alle [raccomandazioni sulla sicurezza](https://docs.microsoft.com/en-us/azure/security-center/security-center-recommendations) e quali utenti dell'organizzazione sono responsabili del monitoraggio per le nuove raccomandazioni e dell'adozione delle misure necessarie.

Il Centro sicurezza visualizza una raccomandazione perché vengano specificati i dettagli dei contatti di sicurezza per la sottoscrizione di Azure. Queste informazioni verranno usate da Microsoft per contattare l'utente se il Microsoft Security Response Center (MSRC) rileva che un'entità illegale o non autorizzata ha effettuato l'accesso ai dati del cliente. Per altre informazioni su come abilitare questa raccomandazione, vedere [Specificare i dettagli dei contatti di sicurezza nel Centro sicurezza di Azure](security-center-provide-security-contact-details.md) .

## <a name="data-collection-and-storage"></a>Raccolta dati e archiviazione
Il Centro sicurezza di Azure usa Microsoft Monitoring Agent, lo stesso agente usato da Operations Management Suite e dal servizio Log Analytics, per raccogliere dati di protezione dalle macchine virtuali. I dati raccolti dall'agente verranno archiviati nelle aree di lavoro di Log Analytics.

### <a name="agent"></a>Agente

Dopo aver abilitato la raccolta dati nei criteri di sicurezza, Microsoft Monitoring Agent (per [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) o [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)) è installato in tutte le macchine virtuali di Azure supportate e in quelle nuove che vengono create.  Se la macchina virtuale ha già installato Microsoft Monitoring Agent, il Centro sicurezza di Azure userà l'agente installato corrente. Il processo dell'agente è progettato per non essere invasivo e ridurre al minimo l'impatto sulle prestazioni delle VM.

Microsoft Monitoring Agent per Windows richiede l'uso della porta TCP 443. Vedere l'[articolo sulla risoluzione dei problemi](security-center-troubleshooting-guide.md) per altri dettagli.

Per disabilitare successivamente la raccolta dati, è possibile usare i criteri di sicurezza. Tuttavia, poiché Microsoft Monitoring Agent può essere usato da altri servizi di gestione e monitoraggio di Azure, l'agente non sarà disinstallato automaticamente quando si disattiva la raccolta dati nel Centro sicurezza. È possibile disinstallare manualmente l'agente se necessario.

> [!NOTE]
> Per un elenco delle macchine virtuali supportate, vedere [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md).
> 

### <a name="workspace"></a>Area di lavoro

I dati raccolti da Microsoft Monitoring Agent (per conto del Centro sicurezza di Azure) verranno archiviati in una o più aree di lavoro di Log Analytics esistenti associate alla sottoscrizione di Azure o in una o più aree di lavoro nuove, a seconda dell'area geografica della macchina virtuale. 

Nel portale di Azure è possibile visualizzare un elenco delle aree di lavoro di Log Analytics, incluse quelle create dal Centro sicurezza di Azure. Per le nuove aree di lavoro verrà creato un gruppo di risorse correlato. Entrambi seguiranno questa convenzione di denominazione: 

* Area di lavoro: *DefaultWorkspace-[subscription-ID]-[geo]*
* Gruppo di risorse: *DefaultResouceGroup-[geo]*

Per le aree di lavoro create dal Centro sicurezza di Azure, i dati vengono conservati per 30 giorni. Per le aree di lavoro esistenti, il periodo di memorizzazione dipende dal piano tariffario dell'area di lavoro.

> [!NOTE]
> Microsoft è fortemente impegnata nella protezione della privacy e della sicurezza dei dati. Microsoft è conforme alle più rigorose linee guida sulla sicurezza e sulla conformità in tutte le fasi, dalla codifica all'esecuzione di un servizio. Per altre informazioni sulla privacy e sulla gestione dei dati, leggere [Sicurezza dei dati nel Centro sicurezza di Azure](security-center-data-security.md).
> 

## <a name="ongoing-security-monitoring"></a>Monitoraggio continuo della sicurezza
Dopo la configurazione iniziale e l'applicazione delle raccomandazioni del Centro sicurezza, il passaggio successivo consiste nel valutare i processi operativi del Centro sicurezza.

Per accedere al Centro sicurezza dal portale di Azure è possibile fare clic su **Esplora** e digitare **Centro sicurezza** nel campo **Filtro**. Le visualizzazioni che l'utente ottiene dipendono da questi filtri applicati. L'esempio seguente mostra un ambiente con molti problemi da risolvere:

![dashboard](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig6.png)

> [!NOTE]
> Il Centro sicurezza non interferisce con le normali procedure operative, esegue un monitoraggio passivo delle distribuzioni e fornisce raccomandazioni in base ai criteri di sicurezza abilitati.

Quando si acconsente esplicitamente per la prima volta all'uso del Centro sicurezza per l'ambiente Azure corrente, assicurarsi di esaminare tutte le raccomandazioni nel riquadro **Raccomandazioni** o nelle singole risorse (**Calcolo**, **Rete**, **Archiviazione e dati** e **Applicazione**).

Dopo avere applicato tutte le raccomandazioni, la sezione **Prevenzione** sarà verde per tutte le risorse che sono state corrette. Il monitoraggio continuo a questo punto risulta più semplice perché le azioni da intraprendere dipendono unicamente dalle modifiche apportate nei riquadri Integrità sicurezza delle risorse e Raccomandazioni.

La sezione **Rilevamento** è più reattiva e contiene avvisi relativi a problemi in corso o che si sono verificati in precedenza e sono stati rilevati dai controlli del Centro sicurezza e da sistemi di terze parti. Il riquadro Avvisi di sicurezza mostra grafici a barre che rappresentano il numero di avvisi di rilevamento delle minacce trovati ogni giorno e la relativa distribuzione per categorie basate sulla gravità, bassa, media o alta. Per altre informazioni sugli avvisi di sicurezza, vedere [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md).

> [!NOTE]
> È anche possibile usare Microsoft Power BI per visualizzare i dati del Centro sicurezza. Vedere [Ottenere informazioni dettagliate sui dati del Centro sicurezza di Azure con Power BI](security-center-powerbi.md).
> 
> 

### <a name="monitoring-for-new-or-changed-resources"></a>Monitoraggio di risorse nuove o modificate
Gli ambienti Azure sono per la maggior parte dinamici, con nuove risorse che vengono attivate e disattivate a intervalli regolari, nuove configurazioni, modifiche e così via. Il Centro sicurezza contribuisce a garantire la visibilità dello stato di sicurezza delle nuove risorse.

Quando si aggiungono nuove risorse all'ambiente Azure, come VM o database SQL, il Centro sicurezza le individua automaticamente e iniziare a monitorarne la sicurezza. Ciò include anche i ruoli di lavoro e i ruoli Web PaaS. Se la raccolta dati è abilitata nei [criteri di sicurezza](security-center-policies.md), vengono abilitate automaticamente funzionalità di monitoraggio aggiuntive per le macchine virtuali.

![Aree principali](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3-newUI.png)

1. Per le macchine virtuali, fare clic su **Calcolo** nella sezione **Prevenzione**. Gli eventuali problemi di abilitazione dei dati e le relative raccomandazioni verranno visualizzati nella scheda **Panoramica**, nella sezione **Raccomandazioni sul monitoraggio**.
2. Per conoscere gli eventuali rischi di sicurezza identificati per la nuova risorsa, vedere **Raccomandazioni** .
3. Accade spesso che nelle nuove macchine virtuali aggiunte all'ambiente inizialmente sia installato solo il sistema operativo. Il proprietario della risorsa potrebbe avere bisogno di tempo per distribuire altre app per l'uso da parte delle macchine virtuali.  È preferibile conoscere la finalità del carico di lavoro, ad esempio se verrà usato come server applicazioni. In base alla finalità del nuovo carico di lavoro, è possibile abilitare i **Criteri di sicurezza** appropriati, che rappresentano il terzo passaggio del flusso di lavoro.
4. Con l'aggiunta di nuove risorse all'ambiente Azure, potrebbero essere visualizzati nuovi avvisi nel riquadro **Avvisi di sicurezza** . Verificare sempre se sono presenti nuovi avvisi in questo riquadro e intraprendere le azioni necessarie in base alle raccomandazioni del Centro sicurezza.

È anche possibile monitorare regolarmente lo stato delle risorse esistenti per identificare le modifiche alla configurazione che hanno creato rischi di sicurezza, deviazioni dalle baseline consigliate e avvisi di sicurezza. Avviare il dashboard del Centro sicurezza. Qui sono presenti tre aree principali da esaminare in modo continuativo.

![Operazioni](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4-newUI.png)

1. La sezione **Prevenzione** consente di accedere rapidamente alle risorse chiave. Usare questa opzione per monitorare Calcolo, Rete, Archiviazione e dati e Applicazioni.
2. Il pannello **Raccomandazioni** consente di esaminare le raccomandazioni del Centro sicurezza. Durante il monitoraggio continuo è normale non ricevere raccomandazioni ogni giorno, perché tutte le raccomandazioni sono state applicate durante la configurazione iniziale del Centro sicurezza. Per questo motivo, in questa sezione potrebbero non essere presenti nuove informazioni ogni giorno e sarà sufficiente accedervi solo quando necessario.
3. La sezione **Rilevamento** potrebbe cambiare molto di frequente o solo saltuariamente. Esaminare sempre gli avvisi di sicurezza e intraprendere le azioni necessarie in base alle raccomandazioni del Centro sicurezza.

## <a name="incident-response"></a>Risposta agli eventi imprevisti
Il Centro sicurezza rileva le minacce appena si verificano e avvisa l'utente. Le organizzazioni devono monitorare i nuovi avvisi di sicurezza e intraprendere le azioni necessarie per indagare più a fondo o correggere l'attacco. Per altre informazioni sul funzionamento del rilevamento delle minacce nel Centro sicurezza, vedere [Funzionalità di rilevamento del Centro sicurezza di Azure](security-center-detection-capabilities.md).

Per quanto offrire una guida alla creazione di un piano di risposta agli eventi imprevisti non rientri nelle finalità di questo articolo, il ciclo di vita della risposta del Centro sicurezza di Microsoft Azure nel cloud verrà usato come base per le fasi della risposta agli eventi imprevisti. Le fasi sono illustrate nel diagramma seguente:

![Attività sospetta](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> Per tale scopo è possibile usare la [Computer Security Incident Handling Guide](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) (Guida alla gestione degli eventi imprevisti della sicurezza nei computer) del National Institute of Standards and Technology (NIST).
> 

È possibile usare gli avvisi del Centro sicurezza durante le fasi seguenti.

* **Rilevamento**: identificazione di un'attività sospetta in una o più risorse. 
* **Valutazione**: esecuzione della valutazione iniziale per ottenere altre informazioni sull'attività sospetta.
* **Diagnosi**: uso dei passaggi di correzione per completare la procedura tecnica per risolvere il problema.

Ogni avviso di sicurezza fornisce informazioni che possono essere usate per comprendere meglio la natura dell'attacco e trovare soluzioni di mitigazione. Alcuni avvisi forniscono anche collegamenti ad altre informazioni o fonti di informazioni all'interno di Azure. È possibile usare le informazioni fornite per ulteriori ricerche e per avviare la mitigazione dei rischi. È inoltre possibile eseguire ricerche nei dati relativi alla sicurezza archiviati nell'area di lavoro.

L'esempio seguente mostra un'attività RDP sospetta in azione:

![Attività sospetta](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Questo pannello include dettagli relativi al momento in cui si è verificato l'attacco, al nome host di origine, alla macchina virtuale di destinazione e ai passaggi necessari per applicare la raccomandazione. In alcuni casi le informazioni sull'origine dell'attacco potrebbero non essere disponibili. Per altre informazioni su questo tipo di comportamento, vedere [Missing Source Information in Azure Security Center Alerts](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) (Informazioni sull'origine mancanti negli avvisi del Centro sicurezza di Azure).

Nel video [How to Leverage the Azure Security Center & Microsoft Operations Management Suite for an Incident Response](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) (Come sfruttare il Centro sicurezza di Azure e Microsoft Operations Management Suite per la risposta a un evento imprevisto) è possibile vedere alcune demo che consentono di comprendere come usare il Centro sicurezza per ognuna di queste fasi.

> [!NOTE]
> Vedere [Uso del Centro sicurezza di Azure per rispondere agli eventi imprevisti](security-center-incident-response.md) per altre informazioni sull'uso delle funzionalità del Centro sicurezza di Azure per rispondere agli eventi imprevisti. 
> 
> 

## <a name="see-also"></a>Vedere anche
In questo documento è stato descritto come pianificare l'adozione del Centro sicurezza di Azure. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md)
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare l'integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : post di blog sulla sicurezza e sulla conformità di Azure.


