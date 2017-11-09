---
title: Monitoraggio delle risorse con la soluzione Security and Audit di Operations Management Suite | Documentazione Microsoft
description: "Questo documento fornisce informazioni su come utilizzare le funzionalità della soluzione Security and Audit di OMS per monitorare le risorse e identificare i problemi di sicurezza."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: d6752120-821f-4aa7-a049-25bf5a653b95
ms.service: operations-management-suite
ms.custom: oms-security
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
ms.openlocfilehash: 2f73266b65a4eda6c8751a2d56bc3f11bf4e6a57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-resources-in-operations-management-suite-security-and-audit-solution"></a>Monitoraggio delle risorse con la soluzione Security and Audit di Operations Management Suite
Questo documento fornisce informazioni su come utilizzare le funzionalità della soluzione Security and Audit di OMS per monitorare le risorse e identificare i problemi di sicurezza.

## <a name="what-is-oms"></a>Cos'è OMS?
Microsoft Operations Management Suite (OMS) è la soluzione Microsoft per la gestione IT basata sul cloud che consente di gestire e proteggere l'infrastruttura locale e cloud. Per altre informazioni su OMS, leggere l'articolo [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="monitoring-resources"></a>Monitoraggio delle risorse
Quando possibile, si desidera prevenire problemi di sicurezza fin dall’inizio. Tuttavia, è impossibile impedire tutti i problemi di sicurezza. Quando si verificano problemi relativi alla sicurezza, è necessario assicurarsi che l'impatto sia ridotto ai minimi termini.  Tre indicazioni importanti possono essere messe in atto per ridurre al minimo il numero e l'impatto dei problemi di sicurezza:

* Valutare periodicamente le vulnerabilità nel proprio ambiente.
* Controllare periodicamente tutti i computer e i dispositivi di rete per assicurarsi che abbiano tutte le patch più recenti installate.
* Controllare periodicamente tutti i log e i meccanismi di registrazione, inclusi i log eventi del sistema operativo, i log specifici dell'applicazione e i log di sistema di rilevamento delle intrusioni.

La soluzione Security and Audit di OMS consente all’IT di monitorare attivamente tutte le risorse, per ridurre l’impatto dei problemi di sicurezza. Security and Audit di OMS dispone dei domini di sicurezza che possono essere utilizzati per il monitoraggio delle risorse. I domini di sicurezza consentono di accedere rapidamente alle opzioni per il monitoraggio della sicurezza. I seguenti domini verranno analizzati più da vicino:

* Valutazione di software dannoso
* Valutazione degli aggiornamenti
* Identità e accesso

> [!NOTE]
> Per una panoramica di tutte queste opzioni, leggere [Introduzione alla soluzione Security and Audit di Operations Management Suite](oms-security-getting-started.md)
> 
> 

### <a name="monitoring-system-protection"></a>Protezione del sistema di monitoraggio
Con un approccio di difesa avanzato, tutti i livelli di protezione sono importanti per lo stato di sicurezza generale dell'asset. Computer con minacce rilevate e computer con una protezione insufficiente vengono visualizzati nel riquadro Malware Assessment in Security Domains. Usando le informazioni in Malware Assessment, è possibile sviluppare un piano per applicare la protezione ai server per cui è necessaria. Per accedere a questa opzione, attenersi alla procedura seguente:

1. Nel dashboard principale **Microsoft Operations Management Suite** fare clic sul riquadro **Security and Audit** (Sicurezza e controllo).
   
    ![Security and Audit](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)
2. Nel dashboard **Security and Audit** fare clic su **Antimalware Assessment** in **Security Domains**. Viene visualizzato il dashboard **Antimalware Assessment** come illustrato di seguito:

![Valutazione di software dannoso](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig2-ga.png)

È possibile utilizzare il dashboard **Malware Assessment** per identificare i problemi di sicurezza seguenti:

* **Minacce attive**: computer compromessi che presentano minacce attive nel sistema.
* **Minacce risolte**: computer compromessi con minacce risolte.
* **Firma scaduta**: computer in cui è abilitata la protezione da malware, ma la firma è scaduta.
* **Nessuna protezione in tempo reale**: computer che non dispongono di un antimalware installato.

### <a name="monitoring-updates"></a>Monitoraggio degli aggiornamenti
L'applicazione degli aggiornamenti di sicurezza più recenti è una procedura consigliata relativa alla sicurezza da incorporare nella strategia di gestione degli aggiornamenti. Il servizio Microsoft Monitoring Agent (HealthService.exe) legge le informazioni di aggiornamento dai computer monitorati e invia quindi le informazioni aggiornate al servizio OMS nel cloud per l'elaborazione. Il servizio Microsoft Monitoring Agent è configurato come un servizio automatico e dovrebbe essere sempre in esecuzione nel computer di destinazione.

![Monitoraggio degli aggiornamenti](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig3.png)

Viene applicata la logica ai dati di aggiornamento, che vengono quindi registrati dal servizio cloud. Gli eventuali aggiornamenti mancanti rilevati vengono visualizzati nel dashboard **Updates** . È possibile usare il dashboard **Updates** per elaborare gli aggiornamenti mancanti e sviluppare un piano per applicarli ai server su cui sono necessari. Attenersi alla procedura seguente per accedere al dashboard **Updates** :

1. Nel dashboard principale **Microsoft Operations Management Suite** fare clic sul riquadro **Security and Audit** (Sicurezza e controllo).
2. Nel dashboard **Security and Audit** fare clic su **Update Assessment** in **Security Domains**. Il dashboard Update viene visualizzato come illustrato di seguito:

![Valutazione degli aggiornamenti](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig4.png)

In questo dashboard è possibile eseguire una valutazione dell'aggiornamento per comprendere lo stato corrente dei computer e risolvere le minacce più critiche. Usando il riquadro **Critical or Security Updates** , gli amministratori IT saranno in grado di accedere a informazioni dettagliate sugli aggiornamenti mancanti come illustrato di seguito:

![Risultato della ricerca](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig5.png)

Questo report include informazioni critiche che possono essere utilizzate per identificare il tipo di minaccia a cui è vulnerabile questo sistema, inclusi gli articoli della Microsoft KB associati all'aggiornamento della sicurezza e il bollettino Microsoft che fornisce ulteriori dettagli sulla vulnerabilità.

### <a name="monitoring-identity-and-access"></a>Monitoraggio di identità e accesso
Con gli utenti che lavorano ovunque, utilizzano diversi dispositivi e l'accedono a una grande quantità di app sul cloud e in locale, è fondamentale che le credenziali siano protette. Gli attacchi per il furto di credenziali sono quelli in cui un utente malintenzionato ottiene inizialmente l'accesso alle credenziali di un altro utente normale per accedere a un sistema all'interno della rete. In molti casi, questo tipo di attacco iniziale è solo un modo per ottenere l'accesso alla rete, l'obiettivo finale consiste nell'individuare gli account con privilegi. 

Gli utenti malintenzionati rimarranno nella rete, utilizzando liberamente gli strumenti disponibili per estrarre le credenziali dalle sessioni di altri account connessi. A seconda della configurazione di sistema, queste credenziali possono essere estratte sotto forma di hash, ticket o password non crittografate.  

> [!NOTE]
> I computer esposti direttamente a Internet visualizzeranno molti tentativi di accesso falliti utilizzando tutti i tipi di nome utente noti (ad esempio, amministratore). Nella maggior parte dei casi, va bene se non vengono utilizzati i nomi utente noti e la password è sufficientemente complessa.
> 
> 

Questi intrusi possono essere identificati prima che compromettano un account con privilegi. È possibile usufruire della **soluzione Security and Audit di OMS** per monitorare identità e accesso. Attenersi alla procedura seguente per accedere al dashboard **Identity and Access** :

1. Nel dashboard principale di **Microsoft Operations Management Suite** fare clic sul riquadro Security and Audit.
2. Nel dashboard **Security and Audit** fare clic su **Identity and Access** in **Security Domains**. Viene visualizzato il dashboard **Identity and Access** come illustrato di seguito:

![Identità e accesso](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig6-ga.png)

Come parte della strategia di monitoraggio regolare, è necessario includere il monitoraggio dell’identità. L’amministratore IT dovrebbe verificare se è presente un nome utente valido specifico con un elevato numero di tentativi di accesso. Ciò potrebbe indicare l'autore dell'attacco che ha acquisito il nome utente reale e ha tentato un attacco di forza bruta o uno strumento automatico che utilizza una password hardcoded scaduta.

Questo dashboard consente all’IT di identificare rapidamente potenziali minacce correlate all'identità e all’accesso alle risorse della società. Inoltre, è particolare importante identificare tendenze potenziali. Nel riquadro Logons Over Time, ad esempio, è possibile verificare quante volte è stato effettuato un tentativo di accesso non riuscito in un determinato intervallo di tempo. In questo caso sul computer **FileServer** sono stati effettuati 35 tentativi di accesso. È possibile scoprire ulteriori dettagli su questo computer facendo clic su di esso. 

![Altre informazioni](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig7-new.png)

Il report generato per questo computer fornisce importanti informazioni su questo modello. Nella colonna **ACCOUNT** viene indicato l'account utente utilizzato per tentare di accedere al sistema, nella colonna **TIMEGENERATED** viene indicato l'intervallo di tempo in cui è stato effettuato il tentativo e nella colonna **LOGONTYPENAME** viene indicata la posizione in cui è stato effettuato il tentativo. Se questi tentativi sono stati eseguiti localmente nel sistema da un programma, nella colonna **PROCESS** viene indicato il nome del processo. In scenari in cui il tentativo di accesso proviene da un programma, si dispone già del nome del processo ed è dunque possibile effettuare ulteriori indagini nel sistema di destinazione.

## <a name="see-also"></a>Vedere anche
In questo documento è stato illustrato come utilizzare la soluzione Security and Audit di OMS per monitorare le risorse. Per altre informazioni sulle funzionalità di OMS per la sicurezza, vedere gli articoli seguenti:

* [Panoramica di Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Introduzione alla soluzione Security and Audit di Operations Management Suite](oms-security-getting-started.md)
* [Monitoraggio e gestione degli avvisi di sicurezza con la soluzione Security and Audit di Operations Management Suite](oms-security-responding-alerts.md)

