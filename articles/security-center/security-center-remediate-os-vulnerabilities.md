---
title: "Correggere le vulnerabilità del sistema operativo nel Centro sicurezza di Azure | Microsoft Docs"
description: "In questo documento viene illustrato come implementare l&quot;indicazione del Centro sicurezza di Azure **Risolvere le vulnerabilità del sistema operativo**."
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
ms.date: 02/03/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 522496ddcd3cc21501f2390e8cdc158798b6289d
ms.openlocfilehash: 35d33625badeb0b32079f0487b8bf2a3dd97e141
ms.lasthandoff: 02/04/2017


---
# <a name="remediate-os-vulnerabilities-in-azure-security-center"></a>Risolvere le vulnerabilità del sistema operativo in Centro sicurezza di Azure
Il Centro sicurezza di Azure analizza ogni giorno le configurazioni del sistema operativo delle macchine virtuali (VM) che potrebbero rendere la VM più vulnerabile agli attacchi e suggerisce le modifiche di configurazione per risolvere tali problemi. Per altre informazioni sulle configurazioni specifiche monitorate, vedere l'[elenco delle regole di configurazione consigliate](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Il Centro sicurezza consiglia di risolvere le vulnerabilità quando la configurazione del sistema operativo della VM non corrisponde alle regole di configurazione consigliate.

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio.  Questa non è una guida dettagliata.
>
>

## <a name="implement-the-recommendation"></a>Implementare la raccomandazione
1. Nel pannello **Indicazioni** selezionare **Correggi le vulnerabilità del sistema operativo**.
   ![Remediate OS vulnerabilities (Risolvi vulnerabilità del sistema operativo)][1]

    Viene aperto il pannello **Correggi le vulnerabilità del sistema operativo**, in cui sono elencate le VM con configurazioni del sistema operativo che non corrispondono alle regole di configurazione consigliate.  Per ogni VM, il pannello identifica:

   * **REGOLE NON RIUSCITE** : il numero di regole non riuscite nella configurazione del sistema operativo della VM.
   * **ORA DELL'ULTIMA ANALISI** : data e ora dell'ultima volta in cui il Centro sicurezza ha verificato la configurazione del sistema operativo della VM.
   * **STATO** : stato corrente della vulnerabilità:

     * Aperta: la vulnerabilità non è ancora stata applicata
     * In corso: l'applicazione della vulnerabilità in corso e non è richiesta alcuna azione da parte dell'utente
     * Risolta: la vulnerabilità è già stata risolta. Dopo che il problema è stato risolto, la voce viene visualizzata in grigio
   * **GRAVITÀ** : tutte le vulnerabilità sono impostate su un livello di gravità bassa, vale a dire che è necessario gestire una vulnerabilità ma non è necessaria un'attenzione immediata.

2. Selezionare una macchina virtuale. Si apre un pannello per la VM, in cui sono visualizzate le regole non riuscite.
   ![Regole di configurazione non riuscite][2]

3. Selezionare una regola. In questo esempio selezioniamo **Le password devono essere conformi ai requisiti di complessità**. Verrà visualizzato un pannello che descrive la regola non riuscita e l'impatto. Esaminare i dettagli e valutare come verranno applicate le configurazioni del sistema operativo.
  ![Descrizione della regola non riuscita][3]

  Il Centro sicurezza usa l'enumerazione di configurazione comune (CCE) per assegnare identificatori univoci per le regole di configurazione. In questo pannello sono disponibili le informazioni seguenti:

  - NOME: nome della regola
  - GRAVITÀ: valore della gravità di CCE a livello critico, importante o di avviso
  - CCIED: identificatore univoco di CCE per la regola
  - DESCRIZIONE: descrizione della regola
  - VULNERABILITÀ: spiegazione della vulnerabilità o del rischio in caso di mancata applicazione della regola
  - IMPATTO: impatto sull'azienda quando viene applicata la regola
  - VALORE PREVISTO: valore previsto quando il Centro sicurezza analizza la configurazione del sistema operativo della VM rispetto alla regola
  - OPERAZIONE DI REGOLA: operazione di regola usata dal Centro sicurezza durante l'analisi della configurazione del sistema operativo della VM rispetto alla regola
  - VALORE EFFETTIVO: valore restituito dopo l'analisi della configurazione del sistema operativo della VM rispetto alla regola
  - RISULTATO DELLA VALUTAZIONE: risultati dell'analisi: riuscita, non riuscita

## <a name="see-also"></a>Vedere anche
Questo documento illustra come implementare la raccomandazione "Remediate OS vulnerabilities" (Risolvi vulnerabilità del sistema operativo) del Centro sicurezza. È possibile esaminare il set di regole di configurazione [qui](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Il Centro sicurezza usa l'enumerazione di configurazione comune (CCE) per assegnare identificatori univoci per le regole di configurazione. Per altre informazioni, vedere la pagina relativa alla enumerazione [CCE](http://cce.mitre.org) .

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : post di blog sulla sicurezza e sulla conformità di Azure.

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/recommendation.png
[2]:./media/security-center-remediate-os-vulnerabilities/vm-remediate-os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png

