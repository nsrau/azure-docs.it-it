---
title: Controlli delle applicazioni adattivi nel Centro sicurezza di Azure
description: Questo documento consente di usare il controllo delle applicazioni adattivi nel centro sicurezza di Azure per consentire l'esecuzione di applicazioni di elenco in macchine di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2020
ms.author: memildin
ms.openlocfilehash: 95c3ad6f689893195e0e5c17c59ab31143cf238d
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93346520"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>Usare i controlli delle applicazioni adattivi per ridurre la superficie di attacco dei computer

Informazioni sui vantaggi dei controlli delle applicazioni adattivi del Centro sicurezza di Azure e su come migliorare la sicurezza grazie a questa funzionalità intelligente basata sui dati.


## <a name="what-are-security-centers-adaptive-application-controls"></a>Quali sono i controlli delle applicazioni adattivi del Centro sicurezza?

I controlli delle applicazioni adattivi sono una soluzione intelligente e automatizzata per la definizione di Consenti elenchi di applicazioni sicure per i computer. 

Spesso le organizzazioni hanno raccolte di computer che eseguono regolarmente gli stessi processi. Il Centro sicurezza USA Machine Learning per analizzare le applicazioni in esecuzione nei computer e creare un elenco del software sicuro noto. Gli elenchi consentiti sono basati sui carichi di lavoro specifici di Azure ed è possibile personalizzare ulteriormente i consigli seguendo le istruzioni riportate di seguito.

Dopo l'abilitazione e la configurazione dei controlli applicazioni adattivi, si riceveranno avvisi di sicurezza se viene eseguita un'applicazione non inclusa nell'elenco di applicazioni definite come sicure.


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>Quali sono i vantaggi dei controlli applicazione adattivi?

Definendo elenchi di applicazioni sicure e generando avvisi quando viene eseguita qualsiasi altra operazione, è possibile raggiungere più obiettivi di protezione avanzata:

- Identificare i potenziali malware, anche quelli che potrebbero non essere presenti nelle soluzioni antimalware
- Migliorare la conformità ai criteri di sicurezza locali che impongono l'uso solo del software concesso in licenza
- Evitare l'esecuzione di applicazioni obsolete o non supportate
- Impedisci un software specifico vietato dall'organizzazione
- Aumentare la supervisione delle app che accedono ai dati sensibili



## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Disponibile a livello generale|
|Prezzi:|Richiede [Azure Defender per server](defender-for-servers-introduction.md)|
|Computer supportati:|![Sì ](./media/icons/yes-icon.png) macchine virtuali di Azure e non Azure che eseguono Windows e Linux<br>![Sì ](./media/icons/yes-icon.png) computer [Azure Arc](../azure-arc/index.yml)|
|Autorizzazioni e ruoli obbligatori:|I ruoli Reader **e Reader di** **sicurezza** possono visualizzare i gruppi e gli elenchi di applicazioni sicure.<br>I ruoli di amministratore di **collaboratore** e **sicurezza** possono sia modificare i gruppi che gli elenchi di applicazioni sicure.|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)|
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>Abilitare i controlli dell'applicazione in un gruppo di computer

Se il Centro sicurezza ha identificato gruppi di computer nelle sottoscrizioni che eseguono in modo coerente un set di applicazioni simile, verrà visualizzata la raccomandazione seguente: i controlli delle applicazioni **adattivi per la definizione di applicazioni sicure devono essere abilitati nei computer**.

Selezionare la raccomandazione oppure aprire la pagina controlli applicazione adattivi per visualizzare l'elenco delle applicazioni e dei gruppi di computer consigliati e sicuri.

1. Aprire il dashboard di Azure Defender e dall'area protezione avanzata selezionare **controlli applicazione adattivi**.

    :::image type="content" source="./media/security-center-adaptive-application/opening-adaptive-application-control.png" alt-text="Apertura di controlli applicazione adattivi dal dashboard di Azure" lightbox="./media/security-center-adaptive-application/opening-adaptive-application-control.png":::

    Viene visualizzata la pagina **controlli applicazione adattivi** con le macchine virtuali raggruppate nelle schede seguenti:

    - **Configurato** : gruppi di computer in cui è già definito un elenco di applicazioni consentite. Per ogni gruppo, la scheda configurata Mostra:
        - numero di computer nel gruppo
        - avvisi recenti

    - **Consigliato** : gruppi di computer che eseguono in modo coerente le stesse applicazioni e non hanno un elenco di consentiti configurati. Si consiglia di abilitare i controlli delle applicazioni adattivi per questi gruppi.
    
      > [!TIP]
      > Se viene visualizzato il prefisso "REVIEWGROUP", il nome del gruppo contiene i computer con un elenco parzialmente coerente di applicazioni. Il Centro sicurezza non può visualizzare un modello, ma consiglia di rivedere questo gruppo per verificare se _è_ possibile definire manualmente alcune regole di controllo delle applicazioni adattive come descritto in [modifica della regola di controllo delle applicazioni adattive di un gruppo](#edit-a-groups-adaptive-application-controls-rule).
      >
      > È anche possibile spostare i computer da questo gruppo in altri gruppi, come descritto in [spostare un computer da un gruppo a un altro](#move-a-machine-from-one-group-to-another).

    - **Nessuna raccomandazione** -computer senza un elenco di applicazioni consentite definito e che non supportano la funzionalità. Il computer potrebbe trovarsi in questa scheda per i motivi seguenti:
      - Manca un agente Log Analytics
      - L'agente di Log Analytics non invia eventi
      - Si tratta di un computer Windows con criteri di [AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview) preesistenti abilitati da un oggetto Criteri di gruppo o criteri di sicurezza locali

      > [!TIP]
      > Il Centro sicurezza necessita di almeno due settimane di dati per definire le raccomandazioni univoche per ogni gruppo di computer. I computer che sono stati creati di recente o che appartengono alle sottoscrizioni abilitate solo di recente con Azure Defender verranno visualizzati nella scheda **Nessuna raccomandazione** .


1. Aprire la scheda **consigliata** . Vengono visualizzati i gruppi di computer con gli elenchi consentiti consigliati.

   ![Scheda consigliata](./media/security-center-adaptive-application/adaptive-application-recommended-tab.png)

1. Selezionare un gruppo. 

1. Per configurare la nuova regola, esaminare le varie sezioni della pagina **configurare le regole di controllo delle applicazioni** e il contenuto, che sarà univoco per questo gruppo di computer specifico:

   ![Configurare una nuova regola](./media/security-center-adaptive-application/adaptive-application-create-rule.png)

   1. **Seleziona computer** : per impostazione predefinita, vengono selezionate tutte le macchine virtuali del gruppo identificato. Deselezionare any per rimuoverli da questa regola.
   
   1. **Applicazioni consigliate** : esaminare questo elenco di applicazioni comuni ai computer all'interno di questo gruppo ed è consigliabile consentirne l'esecuzione.
   
   1. **Altre applicazioni** : esaminare questo elenco di applicazioni che vengono visualizzate con minore frequenza nei computer all'interno di questo gruppo o che possono essere sfruttabili. Un'icona di avviso indica che un'applicazione specifica può essere usata da un utente malintenzionato per ignorare un elenco di applicazioni consentite. Si consiglia di esaminare attentamente queste applicazioni.

      > [!TIP]
      > Entrambi gli elenchi di applicazioni includono l'opzione per limitare un'applicazione specifica a determinati utenti. Adottare il principio dei privilegi minimi laddove possibile.
      > 
      > Le applicazioni sono definite dai rispettivi server di pubblicazione, se un'applicazione non dispone di informazioni sul server di pubblicazione (senza firma), viene creata una regola di percorso per il percorso completo dell'applicazione specifica.

   1. Per applicare la regola, selezionare **controllo**. 




## <a name="edit-a-groups-adaptive-application-controls-rule"></a>Modificare la regola di controllo delle applicazioni adattive di un gruppo

È possibile decidere di modificare l'elenco Consenti per un gruppo di computer a causa di modifiche note nell'organizzazione. 

Per modificare le regole per un gruppo di computer:

1. Aprire il dashboard di Azure Defender e dall'area protezione avanzata selezionare **controlli applicazione adattivi**.

1. Dalla scheda **configurata** selezionare il gruppo con la regola che si desidera modificare.

1. Esaminare le varie sezioni della pagina **configurare le regole di controllo delle applicazioni** come descritto in [abilitare i controlli delle applicazioni adattivi in un gruppo di computer](#enable-application-controls-on-a-group-of-machines).

1. Facoltativamente, aggiungere una o più regole personalizzate:

   1. Selezionare **Aggiungi regola**.

      ![Aggiungere una regola personalizzata](./media/security-center-adaptive-application/adaptive-application-add-custom-rule.png)

   1. Se si sta definendo un percorso sicuro noto, modificare il **tipo di regola** in "Path" e immettere un singolo percorso. È possibile includere caratteri jolly nel percorso.
   
      > [!TIP]
      > Alcuni scenari per i quali i caratteri jolly in un percorso possono essere utili:
      > 
      > * Utilizzando un carattere jolly alla fine di un percorso per consentire tutti i file eseguibili all'interno di questa cartella e sottocartelle.
      > * Utilizzando un carattere jolly all'interno di un percorso per abilitare un nome di eseguibile noto con un nome di cartella modificabile (ad esempio, le cartelle utente personali contenenti un eseguibile noto, i nomi di cartella generati automaticamente e così via).
  
   1. Definire gli utenti consentiti e i tipi di file protetti.

   1. Al termine della definizione della regola, selezionare **Aggiungi**.

1. Per applicare le modifiche, selezionare **Salva**.


## <a name="review-and-edit-a-groups-settings"></a>Esaminare e modificare le impostazioni di un gruppo

1. Per visualizzare i dettagli e le impostazioni del gruppo, selezionare **Impostazioni gruppo**

    Questo riquadro Mostra il nome del gruppo (che può essere modificato), il tipo di sistema operativo, la posizione e altri dettagli pertinenti.

    :::image type="content" source="./media/security-center-adaptive-application/adaptive-application-group-settings.png" alt-text="Pagina Impostazioni gruppo per i controlli applicazione adattivi" lightbox="./media/security-center-adaptive-application/adaptive-application-group-settings.png":::

    > [!IMPORTANT]
    > L'opzione **applica** , nelle impostazioni della modalità di protezione dei tipi di file, è disattivata in **tutti gli** scenari. Al momento non sono disponibili opzioni di imposizione. 
    >
    > :::image type="content" source="./media/security-center-adaptive-application/adaptive-application-modes.png" alt-text="La modalità di imposizione per la protezione dei file è disabilitata in modo permanente. Non sono disponibili opzioni di imposizione.":::

1. Facoltativamente, modificare il nome del gruppo o le modalità di protezione dei tipi di file.

1. Selezionare **applica** e **Salva**.



## <a name="respond-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>Rispondere alle raccomandazioni "Consenti regole consentite nel criterio di controllo delle applicazioni adattivo"

Questa raccomandazione viene visualizzata quando Machine Learning del Centro sicurezza identifica un comportamento potenzialmente legittimo che non è stato precedentemente consentito. La raccomandazione suggerisce nuove regole per le definizioni esistenti per ridurre il numero di avvisi falsi positivi.

Per correggere i problemi:

1. Dalla pagina raccomandazioni selezionare le regole di **consenso nei criteri di controllo delle applicazioni adattivi da aggiornare** consigliate per visualizzare i gruppi con un comportamento potenzialmente legittimo identificato.

1. Selezionare il gruppo con la regola che si desidera modificare.

1. Esaminare le varie sezioni della pagina **configurare le regole di controllo delle applicazioni** come descritto in [abilitare i controlli delle applicazioni adattivi in un gruppo di computer](#enable-application-controls-on-a-group-of-machines).

1. Per applicare le modifiche, selezionare **controllo**.




## <a name="audit-alerts-and-violations"></a>Controlla avvisi e violazioni

1. Aprire il dashboard di Azure Defender e dall'area protezione avanzata selezionare **controlli applicazione adattivi**.

1. Per visualizzare i gruppi con computer con avvisi recenti, esaminare i gruppi elencati nella scheda **configurata** .

1. Per esaminare ulteriormente, selezionare un gruppo.

   ![Avvisi recenti](./media/security-center-adaptive-application/recent-alerts.png)

1. Per ulteriori informazioni e per l'elenco dei computer interessati, selezionare un avviso.



## <a name="move-a-machine-from-one-group-to-another"></a>Spostare un computer da un gruppo a un altro

Quando si sposta un computer da un gruppo a un altro, il criterio di controllo delle applicazioni applicato viene modificato in base alle impostazioni del gruppo in cui è stato spostato. È anche possibile spostare un computer da un gruppo configurato a un gruppo non configurato. in questo modo vengono rimosse tutte le regole di controllo delle applicazioni applicate al computer.

1. Aprire il dashboard di Azure Defender e dall'area protezione avanzata selezionare **controlli applicazione adattivi**.

1. Dalla scheda **configurato** della pagina **controlli applicazione adattivi** selezionare il gruppo che contiene il computer da spostare.

1. Aprire l'elenco dei  **computer configurati**.

1. Aprire il menu del computer da tre punti alla fine della riga e selezionare **Sposta**. Viene visualizzato il riquadro **Sposta computer in un gruppo diverso** .

1. Selezionare il gruppo di destinazione e selezionare **Sposta computer**.

1. Per salvare le modifiche, selezionare **Salva**.





## <a name="manage-application-controls-via-the-rest-api"></a>Gestire i controlli delle applicazioni tramite l'API REST 

Per gestire i controlli applicazione adattivi a livello, usare l'API REST. 

La documentazione completa dell'API è disponibile [qui](/rest/api/securitycenter/adaptiveapplicationcontrols).

Di seguito sono riportate alcune delle funzioni disponibili nell'API REST:

* **Elenco** recupera tutte le raccomandazioni del gruppo e fornisce un JSON con un oggetto per ogni gruppo.

* **Get** Recupera il JSON con i dati di raccomandazione completi, ovvero un elenco di computer, regole di pubblicazione/percorso e così via.

* **Put** configura la regola (usare il codice JSON recuperato con **Get** come corpo della richiesta).
 
   > [!IMPORTANT]
   > La funzione **put** prevede un minor numero di parametri rispetto al codice JSON restituito dal comando Get.
   >
   > Rimuovere le proprietà seguenti prima di usare JSON nella richiesta PUT: recommendationStatus, configurationStatus, issues, location e sourceSystem.




## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come usare il controllo delle applicazioni adattivi nel centro sicurezza di Azure per definire gli elenchi di applicazioni in esecuzione nei computer Azure e non Azure. Per ulteriori informazioni sulle altre funzionalità di protezione del carico di lavoro cloud del Centro sicurezza, vedere:

* [Informazioni sull'accesso JIT alle macchine virtuali](just-in-time-explained.md)
* [Protezione dei cluster Kubernetes di Azure](defender-for-kubernetes-introduction.md)