---
title: Introduzione al Centro sicurezza di Azure | Documentazione Microsoft
description: "Informazioni sul Centro sicurezza di Azure, le funzionalità principali e il funzionamento."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: 4c2be7c35f678430d0ad83a3374ef25f68fd2509
ms.openlocfilehash: 8951167213da6ab5341c1ca420353ec625ef5424
ms.contentlocale: it-it
ms.lasthandoff: 09/20/2017

---
# <a name="introduction-to-azure-security-center"></a>Introduzione al Centro sicurezza di Azure
Informazioni sul Centro sicurezza di Azure, le funzionalità principali e il funzionamento.

> [!NOTE]
> A partire dall'inizio di giugno 2017, il Centro sicurezza usa Microsoft Monitoring Agent per raccogliere e archiviare i dati. Per altre informazioni, vedere [Migrazione della piattaforma del Centro sicurezza di Azure](security-center-platform-migration.md). Le informazioni contenute in questo articolo si riferiscono alle funzionalità del Centro sicurezza dopo la transizione a Microsoft Monitoring Agent.
>
>

## <a name="what-is-azure-security-center"></a>Informazioni sul Centro sicurezza PC di Azure
 Il Centro sicurezza impedisce, rileva e risponde alle minacce mediante visibilità e controllo avanzati della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni di Azure, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

## <a name="key-capabilities"></a>Funzionalità principali
 Il Centro sicurezza fornisce funzionalità efficaci e facili da usare, integrate in Azure, per la prevenzione e il rilevamento delle minacce e la relativa risposta. Funzionalità principali:

| Fase | Funzionalità |
| --- | --- |
| Prevenzione |Monitora lo stato della sicurezza delle risorse di Azure |
| Prevenzione | Definisce i criteri per le sottoscrizioni di Azure in base ai requisiti di sicurezza dell'azienda, ai tipi di applicazioni in uso o al livello di riservatezza dei dati |
| Prevenzione | Usa i suggerimenti relativi alla sicurezza in base ai criteri per guidare i proprietari del servizio nel processo di implementazione dei controlli necessari |
| Prevenzione | Distribuisce rapidamente i servizi di sicurezza e i dispositivi di Microsoft e dei partner |
| Rilevamento |Raccoglie e analizza automaticamente i dati di sicurezza dalle risorse di Azure, dalla rete e dalle soluzioni dei partner, ad esempio programmi antimalware e firewall |
| Rilevamento | Usa le informazioni sulle minacce globali di prodotti e servizi Microsoft, Microsoft Digital Crimes Unit (DCU) e Microsoft Security Response Center (MSRC), nonché da feed esterni |
| Rilevamento | Applica analisi avanzate, tra cui analisi del comportamento e di Machine Learning |
| Risposta |Fornisce eventi imprevisti o avvisi di sicurezza con priorità |
| Risposta | Offre informazioni dettagliate sull'origine dell'attacco e sulle risorse interessate |
| Risposta | Suggerisce alcune modalità per arrestare l'attacco attuale e prevenire quelli futuri |

## <a name="introductory-walkthrough"></a>Procedura dettagliata introduttiva

> [!NOTE]
> Il documento introduce il servizio usando una distribuzione di esempio. Questo argomento non costituisce una guida dettagliata.
>
>

 È possibile accedere al Centro sicurezza tramite il [portale di Azure](https://azure.microsoft.com/features/azure-portal/). [Accedere al portale](https://portal.azure.com). Nel menu principale del portale scorrere fino all'opzione **Centro sicurezza** oppure selezionare il riquadro **Centro sicurezza** aggiunto in precedenza al dashboard del portale.

![Riquadro Sicurezza nel portale di Azure][1]

Nel Centro sicurezza è possibile impostare i criteri di sicurezza, monitorare le configurazioni di sicurezza e visualizzare gli avvisi di sicurezza.

### <a name="security-policies"></a>Criteri di sicurezza
È possibile definire i criteri per le sottoscrizioni di Azure in base ai requisiti di sicurezza della società. È anche possibile personalizzarli in base ai tipi di applicazioni in uso o al livello di riservatezza dei dati in ogni sottoscrizione. Ad esempio, le risorse usate per lo sviluppo o il test possono avere requisiti di sicurezza diversi da quelli delle applicazioni di produzione. Allo stesso modo, le applicazioni con dati regolamentati, come le informazioni personali, possono richiedere un livello di sicurezza più elevato.

> [!NOTE]
> Per modificare i criteri di sicurezza, è necessario essere Amministratore della protezione, proprietario o collaboratore della sottoscrizione. Per altre informazioni sui ruoli e sulle azioni consentite nel Centro sicurezza, vedere [Autorizzazioni nel Centro sicurezza di Azure](security-center-permissions.md).
>
>

Nel pannello **Centro sicurezza** selezionare il riquadro **Criteri** per visualizzare un elenco di sottoscrizioni e gruppi di risorse.   

![Pannello Centro sicurezza][2]

Nel pannello **Criteri di sicurezza** selezionare una sottoscrizione per visualizzare i dettagli dei criteri.

**Raccolta dati** abilita la raccolta dei dati per i criteri di sicurezza. L'abilitazione fornisce:

* Analisi giornaliera di tutte le macchine virtuali (VM) supportate per il monitoraggio della sicurezza e le raccomandazioni.
* Raccolta di eventi di sicurezza per l'analisi e il rilevamento delle minacce.

> [!NOTE]
> La raccolta dei dati è configurata a livello di sottoscrizione.
>
>

Selezionare **Criteri di prevenzione** per aprire il pannello **Criteri di prevenzione**. **Mostra raccomandazioni per** consente di scegliere i controlli di sicurezza da monitorare e le raccomandazioni da visualizzare in base alle esigenze di sicurezza delle risorse della sottoscrizione.

### <a name="security-recommendations"></a>Suggerimenti per la sicurezza
 Il Centro sicurezza analizza lo stato di sicurezza delle risorse di Azure per identificare le potenziali vulnerabilità di sicurezza. Un elenco di suggerimenti illustra in dettaglio il processo di configurazione dei controlli necessari. Tra gli esempi sono inclusi:

* Provisioning di antimalware per identificare e rimuovere il software dannoso
* Configurazione dei gruppi di sicurezza di rete e delle regole per controllare il traffico verso le VM
* Provisioning di un Web application firewall per la difesa da attacchi diretti alle applicazioni Web
* Distribuzione degli aggiornamenti di sistema mancanti
* Risoluzione delle configurazioni del sistema operativo che non corrispondono alle baseline consigliate

Fare clic sul riquadro **Suggerimenti** per visualizzarne un elenco. Fare clic su ogni raccomandazione per visualizzare informazioni aggiuntive o intervenire per risolvere il problema.

![Raccomandazioni di sicurezza nel Centro sicurezza di Azure][5]

### <a name="security-state-of-azure-resources"></a>Stato di sicurezza delle risorse di Azure
La sezione **Prevenzione** del dashboard mostra il comportamento di sicurezza complessivo dell'ambiente per ogni tipo di risorsa, tra cui VM, applicazioni Web e altre risorse.   

Selezionare un tipo di risorsa in **Prevenzione** per visualizzare altre informazioni, incluso un elenco delle potenziali vulnerabilità della sicurezza identificate. Nell'esempio seguente è selezionata la risorsa**Calcolo**.

![Riquadro Integrità delle risorse][6]

### <a name="security-alerts"></a>Avvisi di sicurezza
 Il Centro sicurezza raccoglie, analizza e integra automaticamente i dati di log dalle risorse di Azure, dalla rete e dalle soluzioni dei partner, ad esempio programmi antimalware e firewall. Quando vengono rilevate minacce, viene creato un avviso di sicurezza. Ad esempio, è compreso il rilevamento di:

* VM compromesse in comunicazione con indirizzi IP dannosi noti
* Malware avanzato rilevato tramite Segnalazione errori Windows
* Attacchi di forza bruta alle VM
* Avvisi di sicurezza da programmi antimalware e firewall integrati

Fare clic sul riquadro **Avvisi di sicurezza** per visualizzare un elenco di avvisi con priorità.

![Avvisi di sicurezza][7]

Se si seleziona un avviso, verranno visualizzate altre informazioni sull'attacco e suggerimenti su come risolverlo.

![Dettagli dell'avviso di sicurezza][8]

### <a name="partner-solutions"></a>Soluzioni partner
Il riquadro **Soluzioni partner** consente di monitorare a colpo d'occhio lo stato di sicurezza delle soluzioni partner integrate nella sottoscrizione di Azure. Il Centro sicurezza visualizza gli avvisi provenienti dalle soluzioni.

Selezionare il riquadro **Soluzioni partner** . Viene visualizzato un pannello contenente un elenco di tutte le soluzioni dei partner connessi.

![Soluzioni partner][9]

## <a name="get-started"></a>Introduzione
Per iniziare a usare Centro sicurezza, è necessario avere una sottoscrizione di Microsoft Azure. Il Centro sicurezza viene abilitato con la sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

 È possibile accedere al Centro sicurezza tramite il [portale di Azure](https://azure.microsoft.com/features/azure-portal/). Per altre informazioni, vedere la [documentazione sul portale](https://azure.microsoft.com/documentation/services/azure-portal/) .

[Introduzione al Centro sicurezza di Azure](security-center-get-started.md) fornisce una rapida descrizione dei componenti di monitoraggio della sicurezza e gestione dei criteri del Centro sicurezza.

## <a name="next-steps"></a>Passaggi successivi
Questo documento ha illustrato il Centro sicurezza, le funzionalità principali e come iniziare a usarlo. Per altre informazioni, vedere le risorse seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni facilitano la protezione delle risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare l'integrità delle soluzioni dei partner.
- [Sicurezza dei dati nel Centro sicurezza di Azure](security-center-data-security.md): informazioni sulla gestione e la protezione dei dati nel Centro sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : informazioni e notizie aggiornate sulla sicurezza di Azure.

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.png
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png
[9]: ./media/security-center-intro/partner-solutions.png

