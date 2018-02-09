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
ms.date: 01/30/2018
ms.author: yurid
ms.openlocfilehash: b1c30b6a0d768f04608c4e46a2e29d20f566964a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
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
I controlli delle applicazioni adattivi aiutano a definire un set di applicazioni che è possibile eseguire in gruppi di risorse configurati. Questa funzionalità è disponibile solo per computer Windows (tutte le versioni, versione classica o Azure Resource Manager). La procedura seguente può essere usata per configurare l'inserimento delle applicazioni nell'elenco elementi consentiti nel Centro sicurezza:

1. Aprire il dashboard **Centro sicurezza**.
2. Nel riquadro a sinistra selezionare **Controlli applicazione adattivi** in **Difesa cloud avanzata**.

    ![Difesa](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)

Verrà visualizzata la pagina **Controlli applicazione adattivi**.

![controls](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

La sezione **Gruppi di risorse** contiene tre schede:

* **Configurato**: elenco dei gruppi di risorse contenenti le macchine virtuali configurate con il controllo delle applicazioni.
* **Consigliato**: elenco dei gruppi di risorse per cui è consigliato il controllo delle applicazioni. Il Centro sicurezza usa Machine Learning per identificare le macchine virtuali che sono buone candidate per il controllo delle applicazioni, in base al fatto che le macchine virtuali eseguano in modo coerente le stesse applicazioni.
* **Nessuna raccomandazione**: elenco dei gruppi di risorse contenenti le macchine virtuali senza alcuna raccomandazione per il controllo delle applicazioni. Ad esempio, le macchine virtuali in cui le applicazioni cambiano sempre e che non hanno raggiunto uno stato stabile.

### <a name="configure-a-new-application-control-policy"></a>Configurare nuovi criteri di controllo delle applicazioni
1. Fare clic sulla scheda **Consigliato** per un elenco dei gruppi di risorse con raccomandazioni per il controllo delle applicazioni:

  ![Consigliato](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

  L'elenco include:

  - **NOME**: nome della sottoscrizione e del gruppo di risorse
  - **MACCHINE VIRTUALI**: numero di macchine virtuali nel gruppo di risorse
  - **STATO**: stato delle raccomandazioni, che nella maggior parte dei casi è aperto
  - **GRAVITÀ**: livello di gravità delle raccomandazioni

2. Selezionare un gruppo di risorse per aprire l'opzione **Create application control rules** (Crea regole di controllo applicazioni).

  ![Regole di controllo applicazioni](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

3. In **Seleziona macchine virtuali** esaminare l'elenco di macchine virtuali consigliate e deselezionare quelle a cui non si vuole applicare il controllo delle applicazioni. In **Selezionare i processi per le regole di inserimento nell'elenco elementi consentiti** esaminare l'elenco delle applicazioni consigliate e deselezionare quelle a cui non si vuole applicare il controllo. L'elenco include:

  - **NOME**: percorso completo dell'applicazione.
  - **PROCESSI**: numero di applicazioni in ogni percorso.
  - **COMUNE**: "Sì" indica che i processi sono stati eseguiti nella maggior parte delle macchine virtuali nel gruppo di risorse.
  - **SFRUTTABILE**: un'icona di avviso indica se le applicazioni possono essere usate da un utente malintenzionato per aggirare l'inserimento nell'elenco elementi consentiti. È consigliabile esaminare queste applicazioni prima di approvarle.
  - **UTENTI**: utenti autorizzati all'esecuzione dell'applicazione

4. Dopo avere selezionato le opzioni desiderate, scegliere **Crea**.

Per impostazione predefinita, il Centro sicurezza abilita sempre il controllo delle applicazioni in modalità *Controllo*. Dopo aver verificato che l'elenco elementi consentiti non abbia effetti negativi sul carico di lavoro, è possibile modificare la modalità scegliendo *Applica*.

Il Centro sicurezza si basa su almeno due settimane di dati per creare una baseline e popolare le raccomandazioni univoche per ogni gruppo di VM. In base al comportamento previsto per i nuovi clienti del livello Standard del Centro sicurezza, i gruppi di VM vengono prima visualizzati nella scheda *Nessuna raccomandazione*.

> [!NOTE]
> Come procedura consigliata per la sicurezza, il Centro sicurezza cerca sempre di creare una regola relativa all'editore per le applicazioni che devono essere inserite nell'elenco elementi consentiti e, solo se un'applicazione non ha informazioni relative all'editore (ovvero non è firmata), viene creata una regola di percorso per il percorso completo del file EXE specifico.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Modifica e monitoraggio di un gruppo configurato con il controllo delle applicazioni

1. Per modificare e monitorare un gruppo configurato con il controllo delle applicazioni, tornare alla pagina **Controlli applicazione adattivi** e selezionare **CONFIGURATO** in **Gruppi di risorse**:

  ![Gruppi di risorse](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

  L'elenco include:

  - **NOME**: nome della sottoscrizione e del gruppo di risorse
  - **MACCHINE VIRTUALI**: numero di macchine virtuali nel gruppo di risorse
  - **MODALITÀ**: la modalità di controllo registra i tentativi di eseguire applicazioni non inserite nell'elenco elementi consentiti e il blocco non consente l'esecuzione delle applicazioni non inserite nell'elenco elementi consentiti
  - **PROBLEMI**: qualsiasi violazione corrente

2. Selezionare un gruppo di risorse per apportare le modifiche nella pagina **Edit application control policy** (Modifica criteri di controllo applicazioni).

  ![Protezione](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. In **Modalità di protezione** è possibile selezionare una delle opzioni seguenti:

  - **Controllo**: in questa modalità, la soluzione di controllo delle applicazioni non applica le regole, ma controlla solo l'attività nelle macchine virtuali protette. Si tratta dell'opzione consigliata per scenari in cui si vuole osservare il comportamento generale prima di bloccare l'esecuzione di un'app nella macchina virtuale di destinazione.
  - **Applica**: in questa modalità la soluzione di controllo delle applicazioni applica le regole e garantisce il blocco delle applicazioni la cui esecuzione non è consentita.

  Come indicato in precedenza, per impostazione predefinita i nuovi criteri di controllo delle applicazioni vengono sempre configurati in modalità *Controllo*. In **Policy extension** (Estensione criteri) è possibile aggiungere i percorsi delle applicazioni da inserire nell'elenco elementi consentiti. Dopo l'aggiunta di questi percorsi, il Centro sicurezza crea le regole appropriate per queste applicazioni, oltre a quelle già presenti.

  Nella sezione **Problemi recenti** sono elencate tutte le violazioni correnti.

  ![Problemi](./media/security-center-adaptive-application/security-center-adaptive-application-fig7.png)

  inclusi i seguenti:
  - **PROBLEMI**: le violazioni che sono state registrate e che possono includere quanto segue:

      - **ViolationsBlocked**: quando la soluzione è attivata in modalità Applica e un'applicazione non inserita nell'elenco elementi consentiti prova ad avviare l'esecuzione.
      - **ViolationsAudited**: quando la soluzione è attivata in modalità Controllo e un'applicazione non inserita nell'elenco elementi consentiti viene eseguita.
      - **RulesViolatedManually**: quando un utente ha provato a configurare manualmente le regole nelle macchine virtuali e non con il portale di gestione del Centro sicurezza di Azure.

 - **N. DI VM**: numero di macchine virtuali con questo tipo di problema.

  Se si fa clic su ogni riga, si viene reindirizzati alla pagina [Log attività di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), in cui è possibile visualizzare le informazioni su tutte le macchine virtuali con questo tipo di violazione. Se si fa clic sui tre punti alla fine di ogni riga, è possibile eliminare la voce specifica. La sezione **Configured virtual machines** (Macchine virtuali configurate) elenca le macchine virtuali a cui applicano queste regole.

  ![Macchine virtuali configurate](./media/security-center-adaptive-application/security-center-adaptive-application-fig8.png)

  In **Regole di inserimento delle entità di pubblicazione nell'elenco elementi consentiti** l'elenco contiene gli elementi seguenti:

  - **REGOLA**: applicazioni per cui è stata creata una regola relativa all'editore in base alle informazioni sul certificato trovate per ogni applicazione
  - **UTENTI**: numero di utenti autorizzati a eseguire ogni applicazione

  Per altre informazioni, vedere [Understanding Publisher Rules in Applocker](https://docs.microsoft.com/windows/device-security/applocker/understanding-the-publisher-rule-condition-in-applocker) (Informazioni sulle regole per gli editori in Applocker).

  ![Regole di inserimento nell'elenco elementi consentiti](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

  Se si fa clic sui tre punti alla fine di ogni riga, è possibile eliminare la regola specifica o modificare gli utenti autorizzati.

  La sezione **Regole di inserimento dei percorsi nell'elenco elementi consentiti** elenca il percorso completo (incluso il file eseguibile) delle applicazioni che non sono firmate con un certificato digitale, ma sono comunque presenti nelle regole di inserimento nell'elenco elementi consentiti.

  > [!NOTE]
  > Per impostazione predefinita, come procedura consigliata per la sicurezza, il Centro sicurezza cerca sempre di creare una regola relativa all'editore per i file EXE che devono essere inseriti nell'elenco elementi consentiti e, solo se un file EXE non ha informazioni relative all'editore (ovvero non è firmato), viene creata una regola di percorso per il percorso completo del file EXE specifico.

  ![Regole di inserimento dei percorsi nell'elenco elementi consentiti](./media/security-center-adaptive-application/security-center-adaptive-application-fig10.png)

  L'elenco contiene:
  - **NOME**: percorso completo del file eseguibile.
  - **UTENTI**: numero di utenti autorizzati a eseguire ogni applicazione

  Se si fa clic sui tre punti alla fine di ogni riga, è possibile eliminare la regola specifica o modificare gli utenti autorizzati.

4. Dopo avere apportato le modifiche nella pagina **Controlli applicazione adattivi** fare clic sul pulsante **Salva**. Se si decide di non applicare le modifiche, fare clic su **Rimuovi**.

### <a name="not-recommended-list"></a>Elenco elementi senza raccomandazioni

Il Centro sicurezza propone l'inserimento delle applicazioni nell'elenco elementi consentiti solo per le macchine virtuali che eseguono un set stabile di applicazioni. Non vengono create raccomandazioni se le applicazioni nelle macchine virtuali associate continuano a cambiare.

![Raccomandazione](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

L'elenco contiene:
- **NOME**: nome della sottoscrizione e del gruppo di risorse
- **MACCHINE VIRTUALI**: numero di macchine virtuali nel gruppo di risorse

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come usare il controllo delle applicazioni adattivo nel Centro sicurezza di Azure per inserire nell'elenco elementi consentiti le applicazioni in esecuzione nelle macchine virtuali di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informazioni su come gestire gli avvisi e rispondere agli eventi imprevisti di sicurezza nel Centro sicurezza.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Informazioni sugli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Informazioni sui diversi tipi di avvisi di sicurezza.
* [Guida alla risoluzione dei problemi del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Informazioni su come risolvere i problemi comuni nel Centro sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.
