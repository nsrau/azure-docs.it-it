---
title: Controlli delle applicazioni adattivi nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento aiuta a usare il controllo delle applicazioni adattivo nel Centro sicurezza di Azure per inserire nell'elenco elementi consentiti le applicazioni in esecuzione nelle macchine virtuali di Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2019
ms.author: monhaber
ms.openlocfilehash: d92d9c444985989cdec3e94840ff21f4a79fe3ad
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56104925"
---
# <a name="adaptive-application-controls-in-azure-security-center"></a>Controlli delle applicazioni adattivi nel Centro sicurezza di Azure
Questa procedura dettagliata fornisce informazioni su come configurare il controllo delle applicazioni nel Centro sicurezza di Azure.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Cosa sono i controlli delle applicazioni adattivi nel Centro sicurezza?
Il controllo delle applicazioni adattivo è una soluzione per l'inserimento delle applicazioni nell'elenco elementi consentiti end-to-end, intelligente e automatizzata del Centro sicurezza di Azure. Consente di controllare quali applicazioni possono essere eseguite nelle macchine virtuali in Azure e, tra gli altri vantaggi, garantisce la protezione avanzata delle macchine virtuali contro i malware. Il Centro sicurezza usa la funzione Machine Learning per analizzare le applicazioni in esecuzione nelle macchine virtuali e, grazie a questa funzionalità intelligente, consente di applicare specifiche regole di inserimento nell'elenco elementi consentiti. Questa funzionalità semplifica notevolmente il processo di configurazione e gestione dei criteri di inserimento delle applicazioni negli elenchi elementi consentiti, permettendo di:

- Bloccare i tentativi di esecuzione di applicazioni dannose, inclusi quelli che potrebbero altrimenti non venire rilevati dalle soluzioni antimalware oppure inviare un avviso per tali tentativi.
- Rispettare i criteri di sicurezza dell'organizzazione che impongono l'uso solo di software concesso in licenza.
- Evitare l'uso di software indesiderato nel proprio ambiente.
- Evitare l'esecuzione di app obsolete e non supportate.
- Impedire l'uso di strumenti software specifici non consentiti nell'organizzazione.
- Consentire al personale IT di controllare l'accesso ai dati sensibili tramite l'utilizzo delle app.

## <a name="how-to-enable-adaptive-application-controls"></a>Come si abilitano i controlli delle applicazioni adattivi?
I controlli delle applicazioni adattivi consentono di definire un set di applicazioni che è possibile eseguire in gruppi configurati di VM. Questa funzionalità è disponibile solo per computer Windows (tutte le versioni, versione classica o Azure Resource Manager). La procedura seguente può essere usata per configurare l'inserimento delle applicazioni nell'elenco elementi consentiti nel Centro sicurezza:

1. Aprire il dashboard **Centro sicurezza**.
2. Nel riquadro a sinistra selezionare **Controlli applicazione adattivi** in **Difesa cloud avanzata**.

    ![Difesa](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)

Verrà visualizzata la pagina **Controlli applicazione adattivi**.

![controls](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

La sezione **Groups of VMs** (Gruppi di macchine virtuali) contiene tre schede:

* **Configurato**: elenco dei gruppi contenenti le macchine virtuali configurate con il controllo delle applicazioni.
* **Consigliato**: elenco dei gruppi per cui è consigliato il controllo delle applicazioni. Il Centro sicurezza usa Machine Learning per identificare le macchine virtuali che sono buone candidate per il controllo delle applicazioni, in base al fatto che le macchine virtuali eseguano in modo coerente le stesse applicazioni.
* **Nessuna raccomandazione**: elenco dei gruppi contenenti le macchine virtuali senza alcuna raccomandazione per il controllo delle applicazioni. Ad esempio, le macchine virtuali in cui le applicazioni cambiano sempre e che non hanno raggiunto uno stato stabile.

> [!NOTE]
> Centro sicurezza usa un algoritmo di clustering proprietario per creare gruppi di VM, assicurando che le macchine virtuali simili ottengano criteri di controllo delle applicazioni consigliati ottimali.
>
>

### <a name="configure-a-new-application-control-policy"></a>Configurare nuovi criteri di controllo delle applicazioni
1. Fare clic sulla scheda **Consigliato** per un elenco dei gruppi con raccomandazioni per il controllo delle applicazioni:

  ![Consigliato](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

  L'elenco include:

  - **NOME**: nome della sottoscrizione e del gruppo
  - **MACCHINE VIRTUALI**: numero di macchine virtuali nel gruppo
  - **STATO**: stato attuale delle raccomandazioni
  - **GRAVITÀ**: livello di gravità delle raccomandazioni

2. Fare clic su un gruppo per accedere all'opzione **Crea le regole di controllo delle applicazioni**.

  ![Regole di controllo applicazioni](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

3. In **Seleziona macchine virtuali** esaminare l'elenco di macchine virtuali consigliate e deselezionare quelle a cui non si vogliono applicare i criteri di inserimento nell'elenco elementi consentiti. Verranno visualizzati due elenchi:

  - **Applicazioni consigliate**: elenco di applicazioni frequenti nelle macchine virtuali all'interno del gruppo e la cui esecuzione è consigliata.
  - **Altre applicazioni**: elenco di applicazioni meno frequenti nelle macchine virtuali all'interno del gruppo oppure note come sfruttabili (vedere più avanti) e per le quali è consigliato l'esame.

4. Esaminare le applicazioni in ognuno degli elenchi e deselezionare quelle a cui non si vuole applicare il controllo delle applicazioni. Ogni elenco include:

  - **NOME**: informazioni sul certificato o il percorso completo di un'applicazione
  - **TIPI DI FILE**: tipo di file dell'applicazione. Può trattarsi di file EXE, Script, file MSI o qualsiasi permutazione di questi tipi.
  - **SFRUTTABILE**: un'icona di avviso indica se un'applicazione specifica può essere usata da un utente malintenzionato per aggirare una soluzione di inserimento delle applicazioni nell'elenco elementi consentiti. È consigliabile esaminare queste applicazioni prima di approvarle.
  - **UTENTI**: utenti ai quali è consigliabile consentire l'esecuzione di un'applicazione

5. Dopo avere selezionato le opzioni desiderate, scegliere **Crea**. <br>
Dopo aver selezionato Crea, il Centro sicurezza di Azure crea automaticamente le regole appropriate sopra la soluzione predefinita di inserimento delle applicazioni nell'elenco elementi consentiti disponibile nei server Windows (AppLocker).

> [!NOTE]
> - Il Centro sicurezza si basa su almeno due settimane di dati per creare una baseline e popolare le raccomandazioni univoche per ogni gruppo di VM. In base al comportamento previsto per i nuovi clienti del livello Standard del Centro sicurezza, i gruppi di VM vengono prima visualizzati nella scheda *Nessuna raccomandazione*.
> - Controlli delle applicazioni adattivi di Centro sicurezza non supporta le macchine virtuali per cui i criteri di AppLocker sono già abilitati da un oggetto Criteri di gruppo o un criterio di sicurezza locale.
> -  Come procedura consigliata per la sicurezza, il Centro sicurezza cerca sempre di creare una regola relativa all'editore per le applicazioni indicate come consentite e, solo se un'applicazione non ha informazioni relative all'editore, ovvero non è firmata, viene creata una regola di percorso per il percorso completo dell'applicazione specifica.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Modifica e monitoraggio di un gruppo configurato con il controllo delle applicazioni

1. Per modificare e monitorare un gruppo configurato con i criteri di inserimento delle applicazioni nell'elenco elementi consentiti, tornare alla pagina **Controlli applicazione adattivi** e selezionare **CONFIGURATO** in **Gruppi di macchine virtuali**:

  ![Gruppi](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

  L'elenco include:

  - **Nome**: nome della sottoscrizione e del gruppo
  - **MACCHINE VIRTUALI**: numero di macchine virtuali nel gruppo
  - **Modalità**: la modalità di controllo registra i tentativi di eseguire applicazioni non inserite nell'elenco elementi consentiti, mentre la modalità Applica impedisce l'esecuzione delle applicazioni non inserite nell'elenco elementi consentiti
  - **Avvisi**: qualsiasi violazione corrente

2. Fare clic su un gruppo per apportare le modifiche nella pagina **Modifica il criterio del controllo applicazione**.

  ![Protezione](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. In **Modalità di protezione** è possibile selezionare una delle opzioni seguenti:

  - **Controllo**: in questa modalità, la soluzione di controllo delle applicazioni non applica le regole, ma controlla solo l'attività nelle macchine virtuali protette. Si tratta dell'opzione consigliata per scenari in cui si vuole osservare il comportamento generale prima di bloccare l'esecuzione di un'app nella macchina virtuale di destinazione.
  - **Applica**: in questa modalità la soluzione di controllo delle applicazioni applica le regole e garantisce il blocco delle applicazioni la cui esecuzione non è consentita.

   > [!NOTE]
   > -  La modalità di protezione**Applica** è disabilitata fino a nuovo avviso.
   > - Come indicato in precedenza, per impostazione predefinita i nuovi criteri di controllo delle applicazioni vengono sempre configurati in modalità *Controllo*. 
   >

4. In **Estensione dei criteri** aggiungere il percorso di qualsiasi applicazione da consentire. Dopo aver aggiunto tali percorsi, il Centro sicurezza aggiorna i criteri di inserimento nell'elenco elementi consentiti nelle macchine virtuali all'interno del gruppo selezionato e crea le regole appropriate per queste applicazioni, oltre a quelle già presenti.

5. Esaminare le violazioni correnti elencate nella sezione **Avvisi recenti**. Fare clic su ogni riga da reindirizzare alla pagina **Avvisi** all'interno di Centro sicurezza di Azure e visualizzare tutti gli avvisi che sono stati rilevati dal Centro sicurezza di Azure nelle macchine virtuali associate.
  - **Avvisi**: eventuali violazioni registrate.
  - **N. di VM**: numero di macchine virtuali con questo tipo di avviso.

6. In **Regole di inserimento delle entità di pubblicazione nell'elenco elementi consentiti** e **Regole di inserimento dei percorsi nell'elenco elementi consentiti** e **Regole di inserimento degli hash nell'elenco elementi consentiti** è possibile visualizzare quali regole di inserimento delle applicazioni nell'elenco elementi consentiti sono attualmente configurate nelle macchine virtuali all'interno di un gruppo, in base al tipo di raccolta di regole. Per ogni regola è possibile visualizzare:

  - **Regola**: i parametri specifici in base ai quali un'applicazione viene esaminata da AppLocker per determinare se può essere eseguita.
  - **Tipo di file**: i tipi di file coperti da una specifica regola. Può trattarsi di uno dei seguenti: EXE, Script, file MSI o qualsiasi permutazione di questi tipi di file.
  - **Utenti**: nome o numero di utenti autorizzati a eseguire un'applicazione che è coperta da una regola di inserimento delle applicazioni nell'elenco elementi consentiti.

   ![Regole di inserimento nell'elenco elementi consentiti](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Fare clic sui tre punti alla fine di ogni riga per eliminare la regola specifica o modificare gli utenti autorizzati.

8. Dopo aver modificato un criterio **Controlli applicazione adattivi**, fare clic su **Salva**.

### <a name="not-recommended-list"></a>Elenco elementi senza raccomandazioni

Il Centro sicurezza propone i criteri di inserimento delle applicazioni nell'elenco elementi consentiti solo per le macchine virtuali che eseguono un set stabile di applicazioni. Non vengono create raccomandazioni se le applicazioni nelle macchine virtuali associate continuano a cambiare.

![Recommendation](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

L'elenco contiene:
- **NOME**: nome della sottoscrizione e del gruppo
- **MACCHINE VIRTUALI**: numero di macchine virtuali nel gruppo

Centro sicurezza di Azure consente di definire criteri di inserimento delle applicazioni nell'elenco elementi consentiti anche per gruppi non consigliati di macchine virtuali. Per configurare i criteri di inserimento delle applicazioni nell'elenco elementi consentiti anche per tali gruppi, seguire gli stessi principi delineati in precedenza.


## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come usare il controllo delle applicazioni adattivo nel Centro sicurezza di Azure per inserire nell'elenco elementi consentiti le applicazioni in esecuzione nelle macchine virtuali di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informazioni su come gestire gli avvisi e rispondere agli eventi imprevisti di sicurezza nel Centro sicurezza.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Informazioni sugli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Informazioni sui diversi tipi di avvisi di sicurezza.
* [Guida alla risoluzione dei problemi del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Informazioni su come risolvere i problemi comuni nel Centro sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.
