---
title: "Correggere le vulnerabilità del sistema operativo nel Centro sicurezza di Azure | Microsoft Docs"
description: "Questo documento illustra come implementare la raccomandazione **Remediate OS vulnerabilities (Risolvi vulnerabilità del sistema operativo)** del Centro sicurezza di Azure."
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
ms.date: 09/11/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 39879c22278a55f841e294cda5a89bec2bdf6988
ms.contentlocale: it-it
ms.lasthandoff: 09/13/2017

---
# <a name="remediate-os-vulnerabilities-in-azure-security-center"></a>Risolvere le vulnerabilità del sistema operativo in Centro sicurezza di Azure
Il Centro sicurezza di Azure analizza ogni giorno il sistema operativo delle macchine virtuali e dei computer per trovare eventuali configurazioni che li renderebbero più vulnerabili agli attacchi. Il Centro sicurezza consiglia di risolvere le vulnerabilità quando la configurazione del sistema operativo non corrisponde alle regole di configurazione consigliate e suggerisce di modificare la configurazione per risolvere le vulnerabilità.

> [!NOTE]
> Per altre informazioni sulle configurazioni specifiche monitorate, vedere l'[elenco delle regole di configurazione consigliate](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
>
>

## <a name="implement-the-recommendation"></a>Implementare la raccomandazione
La correzione delle vulnerabilità del sistema operativo viene presentata sotto forma di suggerimento nel Centro sicurezza. Questa indicazione verrà visualizzata in **Raccomandazioni** e in **Calcolo**.

In questo esempio verrà esaminata la raccomandazione **Correggi le vulnerabilità del sistema operativo (di Microsoft)** in **Calcolo**.
1. Selezionare **Calcolo** nel menu principale del Centro sicurezza.

   ![Remediate OS vulnerabilities (Risolvi vulnerabilità del sistema operativo)][1]

2. In **Calcolo** selezionare **Correggi le vulnerabilità del sistema operativo (di Microsoft)**. Si apre il dashboard **Mancata corrispondenza di vulnerabilità del sistema operativo (di Microsoft)**.

   ![Remediate OS vulnerabilities (Risolvi vulnerabilità del sistema operativo)][2]

  Nella parte superiore del dashboard sono presenti:

  - Il numero totale di regole per gravità non riuscite nella configurazione del sistema operativo delle macchine virtuali e del computer.
  - Il numero totale di regole per tipo non riuscite nella configurazione del sistema operativo delle macchine virtuali e del computer.
  - Il numero totale di regole non riuscite nelle configurazioni del sistema operativo Windows e Linux.

  Nella parte inferiore del dashboard vengono elencate tutte le regole non riuscite nelle macchine virtuali e nei computer e la gravità dell'aggiornamento mancante. L'elenco include:

  - **CCIED**: identificatore univoco di CCE per la regola. Il Centro sicurezza usa l'enumerazione di configurazione comune (CCE) per assegnare identificatori univoci per le regole di configurazione.
  - **NOME**: nome della regola non riuscita
  - **TIPO DI REGOLA**: chiave del Registro di sistema, criteri di sicurezza o criteri di controllo
  - **N. DI MACCHINE VIRTUALI E COMPUTER**: numero totale di macchine virtuali e computer a cui si applica la regola non riuscita
  - **GRAVITÀ DELLA REGOLA**: valore della gravità di CCE a livello critico, importante o di avviso
  - **STATO**: stato attuale della raccomandazione:

    - **Aperta**: la raccomandazione non è ancora stata risolta.
    - **In corso**: la raccomandazione viene applicata alle risorse e non è richiesta alcuna azione da parte dell'utente
    - **Risolta**: la raccomandazione è stata già completata. Quando il problema è stato risolto, la voce viene visualizzata in grigio.

3. Selezionare una regola non riuscita nell'elenco per visualizzare i dettagli.

   ![Regole di configurazione non riuscite][3]

  In questo pannello sono disponibili le informazioni seguenti:

  - NOME: nome della regola
  - CCIED: identificatore univoco di CCE per la regola
  - Versione del sistema operativo: versione del sistema operativo del computer o della macchina virtuale
  - GRAVITÀ DELLA REGOLA: valore della gravità di CCE a livello critico, importante o di avviso
  - DESCRIZIONE COMPLETA: descrizione della regola
  - VULNERABILITÀ: spiegazione della vulnerabilità o del rischio in caso di mancata applicazione della regola
  - IMPATTO POTENZIALE: impatto sull'azienda quando viene applicata la regola
  - CONTROMISURA: procedura di correzione
  - VALORE PREVISTO: valore previsto quando il Centro sicurezza analizza la configurazione del sistema operativo della VM rispetto alla regola
  - VALORE EFFETTIVO: valore restituito dopo l'analisi della configurazione del sistema operativo della VM rispetto alla regola
  - OPERAZIONE DI REGOLA: operazione di regola usata dal Centro sicurezza durante l'analisi della configurazione del sistema operativo della VM rispetto alla regola

4. Selezionare l'icona **Cerca** nella barra multifunzione superiore. Si apre la ricerca che elenca le aree di lavoro contenenti le macchine virtuali e i computer con la vulnerabilità del sistema operativo selezionata. Questo pannello di selezione dell'area di lavoro viene visualizzato solo se la regola selezionata si applica a più macchine virtuali connesse a diverse aree di lavoro.

  ![Aree di lavoro elencate][4]

5. Selezionare un'area di lavoro. Consente di aprire una query di ricerca di Log Analytics filtrata nell'area di lavoro con la vulnerabilità del sistema operativo.

  ![Area di lavoro con vulnerabilità del sistema operativo][5]

6. Selezionare un computer dall'elenco per avere maggiori informazioni. Verrà visualizzata un'altra finestra di risultati della ricerca contenente le informazioni filtrate solo per quel computer.

  ![Filtrate per quel computer][6]

## <a name="next-steps"></a>Passaggi successivi
Questo documento illustra come implementare la raccomandazione "Remediate OS vulnerabilities" (Risolvi vulnerabilità del sistema operativo) del Centro sicurezza. È possibile esaminare il set di regole di configurazione [qui](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Il Centro sicurezza usa l'enumerazione di configurazione comune (CCE) per assegnare identificatori univoci per le regole di configurazione. Per altre informazioni, vedere la pagina relativa alla enumerazione [CCE](https://nvd.nist.gov/cce/index.cfm) .

Per altre informazioni sul Centro sicurezza, vedere le risorse seguenti:

* [Supported platforms in Azure Security Center](security-center-os-coverage.md) (Piattaforme supportate nel Centro sicurezza di Azure): contiene un elenco di macchine virtuali Windows e Linux supportate.
* [Impostare i criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare l'integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): post di blog sulla sicurezza e sulla conformità di Azure.

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png

