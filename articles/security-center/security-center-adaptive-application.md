---
title: Controlli delle applicazioni adattivi nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento aiuta a usare il controllo delle applicazioni adattivo nel Centro sicurezza di Azure per inserire nell'elenco elementi consentiti le applicazioni in esecuzione nelle macchine virtuali di Azure.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 18ae6a970455646b7a25170f5abefa52a98b0ba2
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---
# <a name="adaptive-application-controls-in-azure-security-center-preview"></a>Controlli delle applicazioni adattivi nel Centro sicurezza di Azure (anteprima)
Questa procedura dettagliata fornisce informazioni su come configurare il controllo delle applicazioni nel Centro sicurezza di Azure.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Cosa sono i controlli delle applicazioni adattivi nel Centro sicurezza?
I controlli delle applicazioni adattivi consentono di controllare quali applicazioni possono essere eseguite nelle macchine virtuali in Azure e, tra gli altri vantaggi, garantiscono la protezione avanzata delle macchine virtuali contro i malware. Il Centro sicurezza usa Machine Learning per analizzare i processi in esecuzione nella macchina virtuale e, grazie a questa funzionalità intelligente, aiuta ad applicare regole di inserimento nell'elenco elementi consentiti. Questa funzionalità semplifica notevolmente il processo di configurazione e gestione degli elenchi elementi consentiti, permettendo di:

- Bloccare i tentativi di esecuzione di applicazioni dannose, inclusi quelli che potrebbero altrimenti non venire rilevati dalle soluzioni antimalware oppure inviare un avviso per tali tentativi.
- Rispettare i criteri di sicurezza dell'organizzazione che impongono l'uso solo di software concesso in licenza.
- Evitare l'uso di software indesiderato nel proprio ambiente.
- Evitare l'esecuzione di app obsolete e non supportate.
- Impedire l'uso di strumenti software specifici non consentiti nell'organizzazione.
- Consentire al personale IT di controllare l'accesso ai dati sensibili tramite l'utilizzo delle app.

## <a name="how-to-enable-adaptive-application-controls"></a>Come si abilitano i controlli delle applicazioni adattivi?
I controlli delle applicazioni adattivi aiutano a definire un set di applicazioni che è possibile eseguire in gruppi di risorse configurati. Questa funzionalità è disponibile solo per computer Windows (tutte le versioni, modalità classica o Azure Resource Manager). Seguire questa procedura per configurare l'inserimento delle applicazioni nell'elenco elementi consentiti nel Centro sicurezza:

1.  Aprire il dashboard **Centro sicurezza** e fare clic su **Panoramica**.
2.  In **Difesa cloud avanzata** il riquadro **Adaptive application controls** (Controlli applicazioni adattivi) mostra il numero di macchine virtuali in cui è attualmente attivato il controllo rispetto al numero di macchine virtuali totale. Viene indicato anche il numero di problemi rilevati nell'ultima settimana: 

    ![Controlli delle applicazioni adattivi](./media/security-center-adaptive-application\security-center-adaptive-application-fig1.png)

3. Fare clic sul riquadro **Adaptive application controls** (Controlli applicazioni adattivi) per visualizzare altre opzioni.

    ![controls](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

4. La sezione Gruppi di risorse contiene tre schede:
    * **Consigliato**: elenco dei gruppi di risorse per cui è consigliato il controllo delle applicazioni. Il Centro sicurezza usa Machine Learning per identificare le macchine virtuali che sono buone candidate per il controllo delle applicazioni, in base al fatto che le macchine virtuali eseguano in modo coerente le stesse applicazioni.
    * **Configurato**: elenco dei gruppi di risorse contenenti le macchine virtuali configurate con il controllo delle applicazioni. 
    * **Nessuna raccomandazione**: elenco dei gruppi di risorse contenenti le macchine virtuali senza alcuna raccomandazione per il controllo delle applicazioni. Ad esempio, le macchine virtuali in cui le applicazioni cambiano sempre e che non hanno raggiunto uno stato stabile.

Le sezioni seguenti descrivono in modo più dettagliato ogni opzione e il modo in cui usarla.

### <a name="configure-a-new-application-control-policy"></a>Configurare nuovi criteri di controllo delle applicazioni
Fare clic sulla scheda **Consigliato** per un elenco dei gruppi di risorse con raccomandazioni per il controllo delle applicazioni:

![Consigliato](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

L'elenco include:
- **NOME**: nome della sottoscrizione e del gruppo di risorse
- **MACCHINE VIRTUALI**: numero di macchine virtuali nel gruppo di risorse
- **STATO**: stato delle raccomandazioni, che nella maggior parte dei casi è aperto
- **GRAVITÀ**: livello di gravità delle raccomandazioni

Selezionare un gruppo di risorse per aprire l'opzione **Create application control rules** (Crea regole di controllo applicazioni):

![Regole di controllo applicazioni](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

In **Seleziona macchine virtuali** esaminare l'elenco di macchine virtuali consigliate e deselezionare quelle a cui non si vuole applicare il controllo delle applicazioni. In **Selezionare i processi per le regole di inserimento nell'elenco elementi consentiti** esaminare l'elenco delle applicazioni consigliate e deselezionare quelle a cui non si vuole applicare il controllo. L'elenco include:

- **NOME**: percorso completo dell'applicazione.
- **PROCESSI**: numero di applicazioni in ogni percorso.
- **COMUNE**: true indica che i processi sono stati eseguiti nella maggior parte delle macchine virtuali nel gruppo di risorse.
- **SFRUTTABILE**: un'icona di avviso indica se le applicazioni possono essere usate da un utente malintenzionato per aggirare l'inserimento nell'elenco elementi consentiti. Si consiglia di esaminare le applicazioni prima dell'approvazione. 

Dopo avere selezionato le opzioni desiderate, fare clic sul pulsante **Crea**. Per impostazione predefinita, il Centro sicurezza abilita sempre il controllo delle applicazioni in modalità *Controllo*. Dopo aver verificato che l'elenco elementi consentiti non abbia effetti negativi sul carico di lavoro, è possibile modificare la modalità scegliendo *Applica*.

> [!NOTE]
> Come procedura consigliata per la sicurezza, il Centro sicurezza cerca sempre di creare una regola relativa all'editore per le applicazioni che devono essere inserite nell'elenco elementi consentiti e, solo se un'applicazione non ha informazioni relative all'editore (ovvero non è firmata), viene creata una regola di percorso per il percorso completo del file EXE specifico.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Modifica e monitoraggio di un gruppo configurato con il controllo delle applicazioni

Per modificare e monitorare un gruppo configurato con il controllo delle applicazioni, fare clic su **CONFIGURATO** in **Gruppi di risorse**:

![Gruppi di risorse](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

L'elenco include:

- **NOME**: nome della sottoscrizione e del gruppo di risorse
- **MACCHINE VIRTUALI**: numero di macchine virtuali nel gruppo di risorse
- **MODALITÀ**: la modalità di controllo registra i tentativi di eseguire applicazioni non inserite nell'elenco elementi consentiti e il blocco non consente l'esecuzione delle applicazioni non inserite nell'elenco elementi consentiti
- **GRAVITÀ**: livello di gravità delle raccomandazioni

Selezionare un gruppo di risorse per apportare le modifiche nella pagina **Edit application control policy** (Modifica criteri di controllo applicazioni).

![Protezione](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

In **Protection Mode** (Modalità di protezione) è possibile selezionare una delle opzioni seguenti:
- **Controllo**: in questa modalità, la soluzione di controllo delle applicazioni non applica le regole, ma controlla solo l'attività nelle macchine virtuali protette. Si tratta dell'opzione consigliata per scenari in cui si vuole osservare il comportamento generale prima di bloccare l'esecuzione di un'app nella macchina virtuale di destinazione.
- **Applica**: in questa modalità la soluzione di controllo delle applicazioni applica le regole e garantisce il blocco delle applicazioni la cui esecuzione non è consentita. 

Come indicato in precedenza, per impostazione predefinita i nuovi criteri di controllo delle applicazioni vengono sempre configurati in modalità *Controllo*. In **Policy extension** (Estensione criteri) è possibile aggiungere i percorsi delle applicazioni da inserire nell'elenco elementi consentiti. Una volta aggiunti questi percorsi, il Centro sicurezza crea le regole appropriate per queste applicazioni, oltre a quelle già presenti. Nella sezione **Problemi** sono elencate tutte le violazioni correnti.

![Problemi](./media/security-center-adaptive-application/security-center-adaptive-application-fig7.png)

L'elenco include:

- **PROBLEMI**: le violazioni che sono state registrate e che possono includere quanto segue:
    - **ViolationsBlocked**: quando la soluzione è attivata in modalità Applica e un'applicazione non inserita nell'elenco elementi consentiti prova ad avviare l'esecuzione.
    - **ViolationsAudited**: quando la soluzione è attivata in modalità Controllo e un'applicazione non inserita nell'elenco elementi consentiti viene eseguita.
    - **RulesViolatedManually**: quando un utente ha provato a configurare manualmente le regole nelle macchine virtuali e non con il portale di gestione del Centro sicurezza di Azure.
- **N. DI VM**: numero di macchine virtuali con questo tipo di problema.

Se si fa clic su ognuna delle righe, si viene reindirizzati alla pagina [Log attività di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), in cui è possibile visualizzare le informazioni su tutte le macchine virtuali con questo tipo di violazione. Se si fa clic sui tre punti alla fine di ogni riga, è possibile eliminare la voce specifica. La sezione **Configured virtual machines** (Macchine virtuali configurate) elenca le macchine virtuali a cui applicano queste regole. 

![Macchine virtuali configurate](./media/security-center-adaptive-application/security-center-adaptive-application-fig8.png)

**Regole di inserimento delle entità di pubblicazione nell'elenco elementi consentiti** elenca le applicazioni per cui è stata creata una regola relativa all'editore in base alle informazioni sul certificato trovate per ogni applicazione. Per altre informazioni, vedere [Understanding Publisher Rules in Applocker](https://docs.microsoft.com/windows/device-security/applocker/understanding-the-publisher-rule-condition-in-applocker) (Informazioni sulle regole per gli editori in Applocker).

![Regole di inserimento nell'elenco elementi consentiti](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

Se si fa clic sui tre punti alla fine di ogni riga, è possibile eliminare la regola specifica. L'elenco **Regole di inserimento dei percorsi nell'elenco elementi consentiti** elenca il percorso completo (incluso il file eseguibile) delle applicazioni che non sono firmate con un certificato digitale, ma sono comunque presenti nelle regole di inserimento nell'elenco elementi consentiti. 

> [!NOTE]
> Per impostazione predefinita, come procedura consigliata per la sicurezza, il Centro sicurezza cerca sempre di creare una regola relativa all'editore per i file EXE che devono essere inseriti nell'elenco elementi consentiti e, solo se un file EXE non ha informazioni relative all'editore (ovvero non è firmato), viene creata una regola di percorso per il percorso completo del file EXE specifico.

![Regole di inserimento dei percorsi nell'elenco elementi consentiti](./media/security-center-adaptive-application/security-center-adaptive-application-fig10.png)

L'elenco contiene:
- **NOME**: percorso completo del file eseguibile.
- **SFRUTTABILE**: true indica che le applicazioni possono essere usate da un utente malintenzionato per aggirare l'inserimento nell'elenco elementi consentiti.  

Se si fa clic sui tre punti alla fine di ogni riga, è possibile eliminare la regola specifica. Dopo avere apportato le modifiche, è possibile fare clic sul pulsante **Salva** oppure, se si decide di non applicare le modifiche, fare clic su **Ignora**.

### <a name="not-recommended-list"></a>Elenco elementi senza raccomandazioni

Il Centro sicurezza propone l'inserimento delle applicazioni nell'elenco elementi consentiti solo per le macchine virtuali che eseguono un set stabile di applicazioni. Non vengono create raccomandazioni se le applicazioni nelle macchine virtuali associate continuano a cambiare. 

![Raccomandazione](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

L'elenco contiene:
- **NOME**: nome della sottoscrizione e del gruppo di risorse.
- **MACCHINE VIRTUALI**: numero di macchine virtuali nel gruppo di risorse.

## <a name="preview-registration"></a>Registrazione dell'anteprima

I controlli delle applicazioni adattivi sono disponibili per i clienti Standard del Centro sicurezza di Azure come anteprima pubblica limitata. Inviare un messaggio di posta elettronica con l'ID sottoscrizione a [Microsoft](mailto:ASC_appcontrol@microsoft.com) per accedere all'anteprima.

## <a name="see-also"></a>Vedere anche
In questo documento si è appreso come usare il controllo delle applicazioni adattivo nel Centro sicurezza di Azure per inserire nell'elenco elementi consentiti le applicazioni in esecuzione nelle macchine virtuali di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informazioni su come gestire gli avvisi e rispondere agli eventi imprevisti di sicurezza nel Centro sicurezza.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Informazioni sugli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Informazioni sui diversi tipi di avvisi di sicurezza.
* [Guida alla risoluzione dei problemi del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Informazioni su come risolvere i problemi comuni nel Centro sicurezza. 
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.


