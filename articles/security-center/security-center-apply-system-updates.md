---
title: Applicare gli aggiornamenti del sistema nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra come implementare le raccomandazioni **Applica gli aggiornamenti del sistema** e **Riavvia dopo gli aggiornamenti del sistema** del Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 9f7924f3f0975dc32fdf5b8e1b89a1fb8e9b7d57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="apply-system-updates-in-azure-security-center"></a>Applicare gli aggiornamenti del sistema nel Centro sicurezza di Azure
Il Centro sicurezza di Azure monitora ogni giorno le macchine virtuali e i computer Windows e Linux alla ricerca di eventuali aggiornamenti mancanti del sistema operativo. Il Centro sicurezza recupera un elenco di aggiornamenti di sicurezza e critici disponibili da Windows Update o Windows Server Update Services (WSUS), in base al servizio configurato nel computer Windows. Il Centro sicurezza cerca anche gli aggiornamenti più recenti nei sistemi Linux. Se nella macchina virtuale o nel computer non è stato eseguito un aggiornamento del sistema, il Centro sicurezza ne consiglierà l'applicazione.

## <a name="implement-the-recommendation"></a>Implementare la raccomandazione
L'applicazione degli aggiornamenti del sistema viene presentata sotto forma di suggerimento nel Centro sicurezza. Se nella macchina virtuale o nel computer non è stato eseguito l'aggiornamento del sistema, questa raccomandazione verrà visualizzata in **Raccomandazioni** e in **Calcolo**.  La selezione della raccomandazione consente di aprire il dashboard **Applica gli aggiornamenti del sistema**.

In questo esempio si userà **Calcolo**.

1. Selezionare **Calcolo** nel menu principale del Centro sicurezza.

   ![Selezionare Calcolo][1]

2. In **Calcolo** selezionare **Aggiornamenti di sistema mancanti**. Si apre il dashboard **Applica gli aggiornamenti del sistema**.

   ![Dashboard Applica gli aggiornamenti del sistema][2]

   Nella parte superiore del dashboard sono presenti:

    - Il numero totale di macchine virtuali e computer Windows e Linux a cui mancano gli aggiornamenti di sistema.
    - Il numero totale di aggiornamenti critici mancanti nelle macchine virtuali e nei computer.
    - Il numero totale di aggiornamenti di sicurezza mancanti nelle macchine virtuali e nei computer.

  Nella parte inferiore del dashboard vengono elencati tutti gli aggiornamenti mancanti nelle macchine virtuali e nei computer e la gravità dell'aggiornamento mancante.  L'elenco include:

    - NOME: il nome dell'aggiornamento mancante.
    - N. DI MACCHINE VIRTUALI E COMPUTER: il numero totale di macchine virtuali e computer a cui manca l'aggiornamento.
    - STATO: stato attuale della raccomandazione:

      - Aperta: la raccomandazione non è ancora stata risolta.
      - In corso: la raccomandazione è stata applicata alle risorse e non è richiesta alcuna azione da parte dell'utente.
      - Risolta: la raccomandazione è stata già completata. Quando il problema è stato risolto, la voce viene visualizzata in grigio.

    - GRAVITÀ: descrive il livello di gravità della raccomandazione specifica:

      - Elevata: esiste una vulnerabilità associata a una risorsa significativa, ad esempio applicazione, macchina virtuale, gruppo di sicurezza di rete, che richiede attenzione.
      - Media: sono necessari passaggi aggiuntivi o non critici per completare un processo o eliminare una vulnerabilità.
      - Bassa: vulnerabilità che è opportuno risolvere, ma non richiede attenzione immediata. Per impostazione predefinita, le raccomandazioni con gravità bassa non appaiono, ma è possibile visualizzarle applicando il filtro corrispondente.

3. Selezionare un aggiornamento mancante nell'elenco per visualizzare i dettagli.

   ![Aggiornamento della sicurezza mancante][3]

4. Selezionare l'icona **Cerca** nella barra multifunzione superiore.  Si apre una query di ricerca di Log Analytics che filtra i computer in cui manca l'aggiornamento.

   ![Ricerca di Log Analytics][4]

5. Selezionare un computer dall'elenco per avere maggiori informazioni. Verrà visualizzata un'altra finestra di risultati della ricerca contenente le informazioni filtrate solo per quel computer.

    ![Ricerca di Log Analytics][5]

## <a name="reboot-after-system-updates"></a>Riavviare dopo gli aggiornamenti del sistema
1. Tornare al pannello **Raccomandazioni** . Dopo l'applicazione degli aggiornamenti del sistema è stata generata una nuova voce, denominata **Riavvia dopo gli aggiornamenti del sistema**. Questa voce indica che è necessario avviare la VM per completare il processo di applicazione degli aggiornamenti del sistema.

   ![Riavviare dopo gli aggiornamenti del sistema][6]
2. Selezionare **Riavvia dopo gli aggiornamenti del sistema**. Verrà visualizzato il pannello **In attesa di un riavvio per completare gli aggiornamenti del sistema** che mostra un elenco di macchine virtuali che devono essere riavviate per completare il processo di aggiornamento del sistema.

   ![Riavvio in sospeso][7]

Riavviare la VM da Azure per completare il processo.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : post di blog sulla sicurezza e sulla conformità di Azure.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
[6]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[7]: ./media/security-center-apply-system-updates/restart-pending.png
