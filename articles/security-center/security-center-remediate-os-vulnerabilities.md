---
title: Correggere le configurazioni di sicurezza nel Centro sicurezza di Azure | Microsoft Docs
description: In questo documento viene illustrato come implementare la raccomandazione del Centro sicurezza di Azure "Remediate security configurations" (Correggere le configurazioni di sicurezza).
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2018
ms.author: terrylan
ms.openlocfilehash: 477973298d8cc9d99da78e36274933e0bb737c4f
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2018
---
# <a name="remediate-security-configurations-in-azure-security-center"></a>Correggere le configurazioni di sicurezza nel Centro sicurezza di Azure
Il Centro sicurezza di Azure analizza ogni giorno il sistema operativo delle macchine virtuali e dei computer per trovare eventuali configurazioni che li renderebbero più vulnerabili agli attacchi. Il Centro sicurezza consiglia di risolvere le vulnerabilità quando la configurazione del sistema operativo non corrisponde alle regole di configurazione della sicurezza consigliate e suggerisce di modificare la configurazione per risolvere le vulnerabilità.

Per altre informazioni sulle configurazioni specifiche sottoposte a monitoraggio, vedere l'[elenco di regole di configurazione consigliate](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Per informazioni su come personalizzare le valutazioni delle configurazioni di sicurezza, vedere [Personalizzare le configurazioni di sicurezza del sistema operativo nel Centro sicurezza di Azure (anteprima)](security-center-customize-os-security-config.md).

## <a name="implement-the-recommendation"></a>Implementare la raccomandazione
"Remediate security configurations" (Correggere le configurazioni di sicurezza) è una raccomandazione nel Centro sicurezza. La raccomandazione viene visualizzata in **Raccomandazioni** > **Calcolo**.

Questo esempio illustra la raccomandazione "Remediate security configurations" (Correggere le configurazioni di sicurezza) in **Calcolo**.
1. Nel riquadro sinistro del Centro sicurezza selezionare **Calcolo**.  
  Viene aperta la finestra **Calcolo**.

   ![Correggere le configurazioni di sicurezza][1]

2. Selezionare **Remediate security configurations** (Correggere le configurazioni di sicurezza).  
  Viene aperta la finestra **Configurazioni della sicurezza**.

   ![Finestra "Configurazioni della sicurezza"][2]

  La sezione superiore del dashboard include:

  - **Regole non riuscite per gravità**: numero totale di regole non riuscite nella configurazione del sistema operativo delle macchine virtuali e del computer, suddivise per gravità.
  - **Regole non riuscite per tipo**: numero totale di regole non riuscite nella configurazione del sistema operativo delle macchine virtuali e del computer, suddivise per tipo.
  - **Regole di Windows non riuscite**: numero totale di regole non riuscite nelle configurazioni del sistema operativo Windows.
  - **Regole di Linux non riuscite**: numero totale di regole non riuscite nelle configurazioni del sistema operativo Linux.

  Nella sezione inferiore del dashboard vengono elencate tutte le regole non riuscite per le macchine virtuali e i computer e la gravità dell'aggiornamento mancante. L'elenco include gli elementi seguenti:

  - **CCEID**: identificatore univoco di CCE per la regola. Il Centro sicurezza usa l'enumerazione di configurazione comune (CCE) per assegnare identificatori univoci alle regole di configurazione.
  - **Nome**: nome della regola non riuscita.
  - **Tipo di regola**: tipo di regola *Chiave del Registro di sistema*, *Criteri di sicurezza* o *Criterio di controllo*.
  - **N. di VM e computer**: numero totale di macchine virtuali e computer a cui si applica la regola non riuscita.
  - **Gravità della regola**: valore di CCE *Critico*, *Importante* o *Avviso*.
  - **Stato**: stato attuale della raccomandazione:

    - **Aperta**: la raccomandazione non è ancora stata risolta.
    - **In corso**: la raccomandazione è stata applicata alle risorse e non è richiesta alcuna azione da parte dell'utente.
    - **Risolta**: la raccomandazione è stata applicata. Quando il problema è risolto, la voce viene visualizzata in grigio.

3. Per visualizzare i dettagli di una regola non riuscita, selezionarla nell'elenco.

   ![Visualizzazione dettagliata di una regola di configurazione non riuscita][3]

   La visualizzazione include le informazioni seguenti:

   - **Nome**: nome della regola.
   - **CCEID**: identificatore univoco di CCE per la regola.
   - **Versione del sistema operativo**: versione del sistema operativo del computer o della macchina virtuale.
   - **Gravità della regola**: valore di CCE *Critico*, *Importante* o *Avviso*.
   - **Descrizione completa**: descrizione della regola.
   - **Vulnerabilità**: spiegazione della vulnerabilità o del rischio in caso di mancata applicazione della regola.
   - **Impatto potenziale**: impatto sull'azienda quando viene applicata la regola.
   - **Contromisura**: procedura di correzione.
   - **Valore previsto**: valore previsto quando il Centro sicurezza analizza la configurazione del sistema operativo della VM rispetto alla regola.
   - **Valore effettivo**: valore restituito dopo un'analisi della configurazione del sistema operativo della VM rispetto alla regola.
   - **Operazione di regola**: operazione di regola usata dal Centro sicurezza durante l'analisi della configurazione del sistema operativo della VM rispetto alla regola.

4. Nella parte superiore della finestra con la visualizzazione dettagliata selezionare **Cerca**.  
  La ricerca apre un elenco di aree di lavoro con macchine virtuali e computer con la mancata corrispondenza delle configurazioni di sicurezza selezionata. La selezione delle aree di lavoro viene visualizzata solo se la regola selezionata si applica a più macchine virtuali connesse a diverse aree di lavoro.

   ![Aree di lavoro elencate][4]

5. Selezionare un'area di lavoro.  
  Consente di aprire una query di ricerca di Log Analytics filtrata nell'area di lavoro con la mancata corrispondenza delle configurazioni di sicurezza.

   ![Area di lavoro con vulnerabilità del sistema operativo][5]

6. Selezionare un computer nell'elenco.  
  Verrà visualizzata una nuova finestra di risultati della ricerca contenente le informazioni filtrate solo per quel computer.

   ![Informazioni dettagliate relative al computer selezionato][6]

## <a name="next-steps"></a>Passaggi successivi
Questo articolo ha illustrato come implementare la raccomandazione "Remediate security configurations" (Correggere le configurazioni di sicurezza) del Centro sicurezza. Per informazioni su come personalizzare le valutazioni delle configurazioni di sicurezza, vedere [Personalizzare le configurazioni di sicurezza del sistema operativo nel Centro sicurezza di Azure (anteprima)](security-center-customize-os-security-config.md).

Per esaminare le configurazioni specifiche sottoposte a monitoraggio, vedere l'[elenco di regole di configurazione consigliate](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Il Centro sicurezza usa l'enumerazione di configurazione comune (CCE) per assegnare identificatori univoci alle regole di configurazione. Per altre informazioni, andare al sito [CCE](https://nvd.nist.gov/cce/index.cfm).

Per altre informazioni sul Centro sicurezza, vedere le risorse seguenti:

* Per un elenco di macchine virtuali Windows e Linux supportate, vedere [Piattaforme supportate nel Centro sicurezza di Azure](security-center-os-coverage.md). 
* Per informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure, vedere [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md). 
* Per informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure, vedere [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md). 
* Per informazioni su come monitorare l'integrità delle risorse di Azure, vedere [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). 
* Per informazioni su come gestire e rispondere agli avvisi di sicurezza, vedere [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md).
* Per informazioni su come monitorare l'integrità delle soluzioni dei partner, vedere [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md).
* Per le risposte alle domande frequenti sull'uso del servizio, vedere [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md).
* Per i post di blog sulla sicurezza e sulla conformità di Azure, vedere [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/).

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png
