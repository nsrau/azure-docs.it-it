---
title: Monitoraggio dell'integrità dei file nel Centro sicurezza di Azure | Microsoft Docs
description: " Informazioni su come abilitare il monitoraggio dell'integrità dei file nel Centro sicurezza di Azure. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: monhaber
ms.openlocfilehash: f8bc10edcdc31dd2ae3995dcb8321a5523e1e51c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901585"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Monitoraggio dell'integrità dei file nel Centro sicurezza di Azure
Questa procedura dettagliata fornisce informazioni su come configurare il monitoraggio dell'integrità dei file nel Centro sicurezza di Azure.

## <a name="what-is-fim-in-security-center"></a>Cos'è il monitoraggio dell'integrità dei file nel Centro sicurezza?
Il monitoraggio dell'integrità dei file, detto anche monitoraggio delle modifiche, esamina i file e i registri del sistema operativo, il software delle applicazioni e altri elementi alla ricerca di modifiche che potrebbero indicare un attacco. Viene usato un metodo di confronto per determinare se lo stato corrente del file è diverso rispetto all'ultima analisi. È possibile sfruttare questo confronto per determinare se sono state apportate modifiche sospette o valide ai file.

Il monitoraggio dell'integrità dei file del Centro sicurezza convalida l'integrità dei file Windows, del registro di sistema Windows e dei file Linux. Selezionare i file che si desidera monitorare, abilitando il monitoraggio dell'integrità dei file. Il Centro sicurezza monitora i file nei quali è abilitato il monitoraggio alla ricerca di attività come:

- Rimozione e creazione di file e registri di sistema
- Modifiche nei file (modifiche nelle dimensioni dei file, elenchi di controllo degli accessi e hash del contenuto)
- Modifiche nei registri di sistema (modifiche nelle dimensioni, elenchi di controllo degli accessi, tipi e contenuti)

Il Centro sicurezza consiglia le entità da monitorare, in cui è possibile abilitare facilmente il monitoraggio dell'integrità dei file. È inoltre possibile definire i criteri di monitoraggio dell'integrità dei file o le entità da monitorare. Questa procedura dettagliata illustra come fare.

> [!NOTE]
> La funzionalità di monitoraggio dell'integrità dei file viene eseguita nelle macchine virtuali e nei computer Windows e Linux ed è disponibile nel livello Standard del Centro sicurezza. Per altre informazioni sui piani tariffari di Centro sicurezza, vedere [Prezzi](security-center-pricing.md). Il monitoraggio dell'integrità dei file carica i dati nell'area di lavoro Log Analytics. Si applicano costi in base alla quantità di dati caricati. Per altre informazioni, vedere [Prezzi di Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

Il monitoraggio dell'integrità dei file usa la soluzione Rilevamento modifiche di Azure per tenere traccia delle modifiche nell'ambiente e identificarle. Quando è abilitato il monitoraggio dell'integrità dei File, è necessario un **Change Tracking** risorsa di tipo **soluzione**. Per dettagli sulla frequenza di raccolta dati, vedere [Change Tracking informazioni dettagliate sulla raccolta dati](https://docs.microsoft.com/azure/automation/automation-change-tracking#change-tracking-data-collection-details) per rilevamento modifiche di Azure.

> [!NOTE]
> Se si rimuove il **Change Tracking** risorsa, è anche disabiliterà l'integrità del File di funzionalità nel Centro sicurezza di monitoraggio.

## <a name="which-files-should-i-monitor"></a>Quali file è necessario monitorare?
Quando si scelgono i file da monitorare, è necessario considerare quali sono critici per il sistema e le applicazioni. Prendere in considerazione di scegliere i file che non si prevede di modificare senza pianificazione. Scegliendo i file che vengono modificati di frequente dalle applicazioni o dal sistema operativo (ad esempio file di log e file di testo) si crea molto disturbo, che rende difficile identificare un attacco.

Il Centro sicurezza consiglia i file da monitorare per impostazione predefinita in base ai modelli di attacchi noti, che includono modifiche a file e registri di sistema.

## <a name="using-file-integrity-monitoring"></a>Uso del monitoraggio dell'integrità dei file
1. Aprire il dashboard **Centro sicurezza**.
2. Nel riquadro sinistro sotto **Difesa cloud avanzata** selezionare **Monitoraggio dell'integrità dei file**.
![Dashboard Centro sicurezza][1]

Si apre **Monitoraggio dell'integrità dei file**.
  ![Dashboard Centro sicurezza][2]

Per ogni area di lavoro sono disponibili le informazioni seguenti:

- Numero totale di modifiche che sono state effettuate nell'ultima settimana (potrebbe essere visualizzato un trattino "-" se il monitoraggio dell'integrità dei file non è abilitato nell'area di lavoro)
- Numero totale di computer e macchine virtuali che creano report nell'area di lavoro
- Posizione geografica dell'area di lavoro
- Sottoscrizione di Azure in cui si trova l'area di lavoro

Per un'area di lavoro potrebbero essere visualizzati anche i pulsanti seguenti:

- ![Icona Abilita][3] Indica che il monitoraggio dell'integrità dei file non è abilitato per l'area di lavoro. Selezionando l'area di lavoro è possibile abilitare il monitoraggio dell'integrità dei file in tutti i computer dell'area di lavoro.
- ![Icona Piano di aggiornamento][4] Indica che l'area di lavoro o la sottoscrizione non viene eseguita nel livello Standard del Centro sicurezza. Per usare il monitoraggio dell'integrità dei file, la sottoscrizione deve essere eseguita nel livello Standard.  Per eseguire l'aggiornamento al livello Standard, è necessario selezionare l'area di lavoro. Per altre informazioni sul livello Standard e su come eseguire l'aggiornamento, vedere [Eseguire l'aggiornamento al livello Standard del Centro sicurezza per la sicurezza avanzata](security-center-pricing.md).
- L'assenza di pulsanti indica che il monitoraggio dell'integrità dei file è già abilitato nell'area di lavoro.

In **Monitoraggio dell'integrità dei file** è possibile selezionare un'area di lavoro per abilitare la funzionalità, visualizzare il dashboard Monitoraggio dell'integrità dei file per l'area di lavoro o [eseguire l'aggiornamento](security-center-pricing.md) dell'area di lavoro al livello Standard.

## <a name="enable-fim"></a>Abilitare il monitoraggio dell'integrità dei file
Per abilitare il monitoraggio dell'integrità dei file in un'area di lavoro:

1. In **Monitoraggio dell'integrità dei file** selezionare un'area di lavoro con il pulsante **Abilita**.
2. Viene aperto **Abilita il monitoraggio dell'integrità dei file**, che visualizza il numero di computer Windows e Linux nell'area di lavoro.

   ![Abilita il monitoraggio dell'integrità dei file][5]

   Sono elencate anche le impostazioni consigliate per Windows e Linux.  Espandere **File Windows**, **Registro di sistema** e **File Linux** per visualizzare l'elenco completo di elementi consigliati.

3. Deselezionare eventuali entità consigliate a cui non si desidera applicare il monitoraggio dell'integrità dei file.
4. Selezionare **Applica il monitoraggio dell'integrità dei file** per abilitare il monitoraggio dell'integrità dei file.

> [!NOTE]
> È possibile modificare le impostazioni in qualsiasi momento. Per altre informazioni, vedere Modificare le entità monitorate di seguito.
>
>

## <a name="view-the-fim-dashboard"></a>Visualizzare il dashboard Monitoraggio dell'integrità dei file
Viene visualizzato il dashboard **Monitoraggio dell'integrità dei file** per le aree di lavoro in cui è abilitata la funzionalità. Il dashboard viene aperto dopo che il monitoraggio dell'integrità dei file è stato abilitato in un'area di lavoro o quando nella finestra **Monitoraggio dell'integrità dei file** viene selezionata un'area di lavoro in cui è già abilitata la funzionalità.

![Dashboard Monitoraggio dell'integrità dei file][6]

Il dashboard Monitoraggio dell'integrità dei file per un'area di lavoro visualizza gli elementi seguenti:

- Numero totale di macchine virtuali connesse all'area di lavoro
- Numero totale di modifiche che sono state effettuate durante il periodo di tempo selezionato
- Suddivisione per tipo di modifica (file, registro di sistema)
- Suddivisione per categoria di modifica (modifica, aggiunta, rimozione)

Selezionando Filtro nella parte superiore del dashboard è possibile applicare il periodo di tempo per cui si desidera visualizzare le modifiche.

![Filtro del periodo di tempo][7]

La scheda **Computer** (mostrata sopra) elenca tutti i computer che creano report per questa area di lavoro. Per ogni computer, il dashboard elenca:

- Il numero totale di modifiche che sono state effettuate durante il periodo di tempo selezionato
- Una suddivisione delle modifiche totali come modifiche al file o al registro di sistema

**Ricerca log** si apre quando si immette il nome di un computer nel campo di ricerca o si seleziona un computer elencato nella scheda Computer. Ricerca log visualizza tutte le modifiche apportate durante il periodo di tempo selezionato per il computer. Per altre informazioni, è possibile espandere una modifica.

![Ricerca log][8]

La scheda **Modifiche** (mostrata sotto) elenca tutte le modifiche per l'area di lavoro durante il periodo di tempo selezionato. Per ogni entità modificata, il dashboard elenca:

- Il computer in cui si è verificata la modifica
- Il tipo di modifica (al registro di sistema o al file)
- La categoria di modifica (modifica, aggiunta o rimozione)
- Data e ora della modifica

![Le modifiche per l'area di lavoro][9]

**Dettagli modifiche** si apre quando si immette una modifica nel campo di ricerca oppure si seleziona un'entità elencata nella scheda **Modifiche**.

![Dettagli modifiche][10]

## <a name="edit-monitored-entities"></a>Modificare le entità monitorate

1. Tornare al **dashboard Monitoraggio dell'integrità dei file** e selezionare **Impostazioni**.

   ![Impostazioni][11]

   **Configurazione dell'area di lavoro** si apre visualizzando tre schede: **Registro di sistema di Windows**, **File Windows** e **File Linux**. Ogni scheda elenca le entità che è possibile modificare nella categoria. Per ogni entità elencata, il Centro sicurezza identifica se il monitoraggio dell'integrità dei file è abilitato (true) o non abilitato (false).  La modifica dell'entità consente di abilitare o disabilitare il monitoraggio dell'integrità dei file.

   ![Configurazione dell'area di lavoro][12]

2. Selezionare la protezione di un'identità. In questo esempio è stato selezionato un elemento in Registro di sistema di Windows. Si apre **Modifica Registro di sistema di Windows per Rilevamento modifiche**.

   ![Rilevamento delle modifiche][13]

In **Modifica Registro di sistema di Windows per Rilevamento modifiche** è possibile:

- Abilitare (True) o disabilitare (False) il monitoraggio dell'integrità dei file
- Specificare o modificare il nome dell'entità
- Specificare o modificare il valore o il percorso
- Eliminare l'entità, rimuovere o salvare la modifica

## <a name="add-a-new-entity-to-monitor"></a>Aggiungere una nuova entità da monitorare
1. Tornare al **dashboard Monitoraggio dell'integrità dei file** e selezionare **Impostazioni** nella parte superiore. Si apre **Configurazione dell'area di lavoro**.
2. In **Configurazione dell'area di lavoro** selezionare la scheda per il tipo di entità che si desidera aggiungere: Registro di sistema di Windows, File Windows o File Linux. In questo esempio è stato selezionato **File Linux**.

   ![Aggiungere una nuova entità da monitorare][14]

3. Selezionare **Aggiungi**. Si apre **Aggiungi file Linux per Rilevamento modifiche**.

   ![Immettere le informazioni richieste][15]

4. Nella pagina **Aggiungi** digitare le informazioni richieste e selezionare **Salva**.

## <a name="disable-monitored-entities"></a>Disabilitare le entità monitorate
1. Tornare al dashboard **Monitoraggio dell'integrità dei file**.
2. Selezionare un'area di lavoro in cui la funzionalità è attualmente abilitata. Un'area di lavoro è abilitata per il monitoraggio dell'integrità dei file se manca il pulsante Abilita o Piano di aggiornamento.

   ![Selezionare un'area di lavoro in cui la funzionalità è abilitata][16]

3. In Monitoraggio dell'integrità dei file selezionare **Impostazioni**.

   ![Selezionare Impostazioni][17]

4. In **Configurazione dell'area di lavoro** selezionare un gruppo in cui **Abilitato** sia impostato su true.

   ![Configurazione dell'area di lavoro][18]

5. Nella finestra **Modifica Registro di sistema di Windows per Rilevamento modifiche** impostare **Abilitato** su False.

   ![Impostare Abilitato su False][19]

6. Selezionare **Salva**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Monitoraggio di cartelle e percorsi con l'uso di caratteri jolly

Usare i caratteri jolly per semplificare le attività di rilevamento nelle directory. Quando viene configurato il monitoraggio delle cartelle con l'uso di caratteri jolly, si applicano le regole seguenti:
-   I caratteri jolly sono necessari per il rilevamento di più file.
-   I caratteri jolly possono essere usati solo nell'ultimo segmento di un percorso, as esempio C:\cartella\file o /etc/*.conf
-   Se una variabile di ambiente include un percorso non valido, la convalida avrà esito positivo ma il percorso avrà esito negativo quando si esegue l'inventario.
-   Quando si imposta il percorso, evitare di specificare percorsi generici, ad esempio c:\*.*, poiché sarà individuato un numero eccessivo di cartelle.

## <a name="disable-fim"></a>Disabilitare il monitoraggio dell'integrità dei file
È possibile disabilitare il monitoraggio dell'integrità dei file. Il monitoraggio dell'integrità dei file usa la soluzione Rilevamento modifiche di Azure per tenere traccia delle modifiche nell'ambiente e identificarle. La disabilitazione rimuove la soluzione Rilevamento modifiche dall'area di lavoro selezionata.

1. Per disabilitare il monitoraggio dell'integrità dei file, tornare al dashboard **Monitoraggio dell'integrità dei file**.
2. Selezionare un'area di lavoro.
3. In **Monitoraggio dell'integrità dei file** selezionare **Disabilita**.

   ![Disabilitare il monitoraggio dell'integrità dei file][20]

4. Selezionare **Rimuovi** per disabilitare.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato descritto come usare il monitoraggio dell'integrità dei file nel Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza](tutorial-security-policy.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione dei consigli di sicurezza](security-center-recommendations.md): informazioni su come i consigli semplificano la protezione delle risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza](security-center-managing-and-responding-alerts.md): informazioni su come gestire gli avvisi di sicurezza e rispondervi.
* [Monitoraggio delle soluzioni dei partner](security-center-partner-solutions.md): informazioni su come monitorare lo stato di integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza](security-center-faq.md): leggere le domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/): informazioni e notizie aggiornate sulla sicurezza di Azure.

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[2]: ./media/security-center-file-integrity-monitoring/file-integrity-monitoring.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[6]: ./media/security-center-file-integrity-monitoring/fim-dashboard.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png
