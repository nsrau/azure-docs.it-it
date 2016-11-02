<properties
   pageTitle="Monitoraggio e gestione degli avvisi di sicurezza nella soluzione Sicurezza e controllo di Operations Management Suite | Microsoft Azure"
   description="Questo documento illustra come usare la funzionalità di intelligence per le minacce disponibile nel dashboard Security and Audit (Sicurezza e controllo) di OMS per monitorare e gestire gli avvisi di sicurezza."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/02/2016"
   ms.author="yurid"/>


# <a name="monitoring-and-responding-to-security-alerts-in-operations-management-suite-security-and-audit-solution"></a>Monitoraggio e gestione degli avvisi di sicurezza nella soluzione Sicurezza e controllo di Operations Management Suite

Questo documento illustra come usare la funzionalità di intelligence per le minacce disponibile nel dashboard Security and Audit (Sicurezza e controllo) di OMS per monitorare e gestire gli avvisi di sicurezza.

## <a name="what-is-oms?"></a>Cos'è OMS?

Microsoft Operations Management Suite (OMS) è la soluzione Microsoft per la gestione IT basata sul cloud che consente di gestire e proteggere l'infrastruttura locale e cloud. Per altre informazioni su OMS, leggere l'articolo [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="threat-intelligence"></a>Intelligence per le minacce

In un ambiente aziendale in cui gli utenti hanno ampio accesso alla rete e usano una vasta gamma di dispositivi per connettersi ai dati aziendali, è fondamentale avere la possibilità di monitorare attivamente le risorse e rispondere in modo rapido agli eventi di sicurezza imprevisti. Ciò è particolarmente importante dal punto di vista della sicurezza perché le minacce alla sicurezza informatica non sempre generano avvisi o segnalazioni di attività sospette identificabili dai tradizionali controlli tecnici della sicurezza. 

Tramite la funzionalità **Threat Intelligence** (Intelligence per le minacce) disponibile nel dashboard Security and Audit (Sicurezza e controllo) di OMS, gli amministratori IT possono identificare le minacce alla sicurezza dell'ambiente, ad esempio determinare se un computer fa parte di una [botnet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection). I computer possono diventare nodi di una botnet quando utenti malintenzionati installano illecitamente malware che connette il computer al comando e al controllo all'insaputa dell'utente. Questa funzionalità è anche in grado di identificare potenziali minacce provenienti da canali di comunicazione sotterranei, ad esempio una [darknet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection_honeypots_darkents). 

Per creare questa intelligence per le minacce, Security and Audit (Sicurezza e controllo) di OMS si basa su dati provenienti da più origini nell'ambito di Microsoft e sfrutta i dati rilevati per identificare le potenziali minacce all'ambiente.

Il riquadro Threat Intelligence (Intelligence per le minacce) è composto da tre aree principali:
- Server con traffico dannoso in uscita
- Tipi di minacce rilevate
- Mappa di intelligence per le minacce

> [AZURE.NOTE] Per una panoramica di tutte queste opzioni, leggere [Introduzione alla soluzione Sicurezza e controllo di Operations Management Suite](oms-security-getting-started.md).

### <a name="responding-to-security-alerts"></a>Rispondere agli avvisi di sicurezza

Uno dei passaggi della procedura di [risposta agli eventi di sicurezza imprevisti](https://technet.microsoft.com/library/cc512623.aspx) consiste nell'identificare la gravità della compromissione dei sistemi. In questa fase è necessario svolgere le attività seguenti:

- Determinare la natura dell'attacco.
- Determinare il punto di origine dell'attacco.
- Determinare l'intento dell'attacco. Si tratta di un attacco casuale o di un attacco appositamente mirato ad acquisire informazioni specifiche dell'organizzazione?
- Identificare i sistemi che sono stati compromessi.
- Identificare i file a cui è stato eseguito l'accesso e determinarne il grado di riservatezza.

Per svolgere più facilmente queste attività è possibile sfruttare le informazioni di **Threat Intelligence** (Intelligence per le minacce) nella soluzione OMS per la sicurezza e il controllo. Per accedere alle opzioni di **Threat Intelligence** (Intelligence per le minacce) seguire questa procedura:

1. Nel dashboard principale **Microsoft Operations Management Suite** fare clic sul riquadro **Security and Audit** (Sicurezza e controllo).

    ![Security and Audit](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)

2. Nel dashboard **Security and Audit** (Sicurezza e controllo) verranno visualizzate le opzioni di **Threat Intelligence** (Intelligence per le minacce), come illustrato di seguito:

    ![Intelligence per le minacce](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig2-ga.png)

Questi tre riquadri presentano una panoramica delle minacce correnti. In **Server with outbound malicious traffic** (Server con traffico dannoso in uscita) è possibile identificare se un computer sottoposto a monitoraggio (all'interno o all'esterno della rete) sta inviando traffico dannoso alla rete Internet. 

Il riquadro **Detected threat types** (Tipi di minacce rilevate) mostra un riepilogo delle minacce attualmente "in circolazione". Se si fa clic su questo riquadro, vengono visualizzati altri dettagli sulle minacce, come mostrato di seguito:

![Tipi di minacce rilevate](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig3.png)

È possibile ottenere altre informazioni facendo clic su ogni minaccia. L'esempio seguente mostra altri dettagli su Botnet:

![altri dettagli su una minaccia](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig4.png)

Come descritto all'inizio della sezione, queste informazioni possono essere molto utili per rispondere a un evento imprevisto. Possono essere importanti anche nell'ambito di un'indagine giudiziaria, in cui è necessario individuare l'origine dell'attacco, i sistemi compromessi e la sequenza temporale. In questo report è possibile identificare con facilità alcuni dettagli fondamentali sull'attacco, ad esempio l'origine, l'indirizzo IP locale compromesso e lo stato della sessione corrente della connessione. 

La **mappa di intelligence per le minacce** consente di individuare facilmente le località del mondo in cui è presente traffico dannoso. Le frecce di colore arancione (in entrata) e quelle di colore rosso (in uscita) sulla mappa identificano la direzione del traffico. Se si fa clic su una delle frecce, vengono visualizzati il tipo di minaccia e la direzione del traffico, come illustrato di seguito:

![mappa di intelligence per le minacce](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig5.png)

## <a name="see-also"></a>Vedere anche

In questo documento è stato descritto come rispondere agli avvisi di sicurezza mediante la funzionalità **Threat Intelligence** (Intelligence per le minacce) disponibile nella soluzione OMS per la sicurezza e il controllo. Per altre informazioni sulle funzionalità di OMS per la sicurezza, vedere gli articoli seguenti:

- [Panoramica di Operations Management Suite (OMS)](operations-management-suite-overview.md)
- [Introduzione alla soluzione Sicurezza e controllo di Operations Management Suite](oms-security-getting-started.md)
- [Monitoraggio delle risorse nella soluzione Sicurezza e controllo di Operations Management Suite](oms-security-monitoring-resources.md)



<!--HONumber=Oct16_HO2-->


