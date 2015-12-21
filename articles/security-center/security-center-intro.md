<properties
   pageTitle="Introduzione al Centro sicurezza di Azure | Microsoft Azure"
   description="Informazioni sul Centro sicurezza di Azure, le funzionalità principali e il funzionamento."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="terrylan"/>

# Introduzione al Centro sicurezza di Azure

Informazioni sul Centro sicurezza di Azure, le funzionalità principali e il funzionamento.

> [AZURE.NOTE]Le informazioni contenute in questo documento si applicano alla versione di anteprima del Centro sicurezza di Azure. Questa è un'introduzione al servizio con un esempio di distribuzione.

## Informazioni sul Centro sicurezza di Azure
Il Centro sicurezza di Azure impedisce, rileva e risponde alle minacce mediante visibilità e controllo avanzati della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni di Azure, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

##	Funzionalità principali
Il Centro sicurezza di Azure fornisce funzionalità semplici ed efficaci integrate in Azure per la prevenzione e il rilevamento delle minacce e la relativa risposta. Funzionalità principali:

| | |
|----- |-----|
| Prevenzione | Monitora lo stato della sicurezza delle risorse di Azure |
| | Definisce i criteri per le sottoscrizioni di Azure in base ai requisiti di sicurezza dell'azienda e al tipo di applicazione o al livello di riservatezza dei dati |
| | Usa i suggerimenti relativi alla sicurezza in base ai criteri per guidare i proprietari del servizio nel processo di implementazione dei controlli necessari |
| | Distribuisce rapidamente i servizi di sicurezza e i dispositivi di Microsoft e dei partner |
| Rilevamento | Raccoglie e analizza automaticamente i dati di sicurezza dalle risorse di Azure, dalla rete e dalle soluzioni dei partner, ad esempio antimalware e firewall |
| | Sfrutta le informazioni sulle minacce globali da prodotti e servizi Microsoft, centri anti-crimine digitali e di risposta agli eventi imprevisti, nonché da feed esterni |
| | Applica analisi avanzate, tra cui analisi del comportamento e di Machine Learning |
| Risposta | Fornisce eventi imprevisti o avvisi di sicurezza con priorità |
| | Offre informazioni dettagliate sull'origine dell'attacco e sulle risorse interessate |
| | Suggerisce alcune modalità per arrestare l'attacco attuale e prevenire quelli futuri |

## Procedura dettagliata introduttiva
È possibile accedere al Centro sicurezza di Azure tramite il [portale di Azure](http://azure.microsoft.com/features/azure-portal/). A questo scopo, [accedere al portale](https://portal.azure.com), selezionare **Sfoglia** e scorrere fino all'opzione **Centro sicurezza** o selezionare il riquadro **Centro sicurezza** aggiunto in precedenza al dashboard del portale.

![][1]

Dal Centro sicurezza di Azure, è possibile impostare i criteri di sicurezza, monitorare le configurazioni di sicurezza e visualizzare gli avvisi di sicurezza.

### Criteri di sicurezza

È possibile definire i criteri per le sottoscrizioni di Azure in base ai requisiti di sicurezza dell'azienda, personalizzandoli in base al tipo di applicazione o al livello di riservatezza dei dati in ogni sottoscrizione. Ad esempio, le risorse usate per lo sviluppo o il test possono avere requisiti di sicurezza diversi da quelli usati per le applicazioni di produzione. Allo stesso modo, le applicazioni con dati regolamentati, come le informazioni personali, possono richiedere un livello di sicurezza più elevato.

> [AZURE.NOTE]Per modificare i criteri di sicurezza, è necessario essere proprietario o collaboratore della sottoscrizione.

Fare clic sul riquadro **Criteri di sicurezza** per un elenco delle sottoscrizioni e sceglierne una per visualizzare i dettagli dei criteri.

![][2]

**Raccolta dati** (vedere sopra) abilita la raccolta dei dati per i criteri di sicurezza. L'abilitazione consente: - Analisi giornaliera di tutte le macchine virtuali supportate per il monitoraggio della sicurezza e i suggerimenti - Raccolta di eventi di sicurezza per l'analisi e il rilevamento delle minacce

**Mostra suggerimenti per:** (vedere sopra) consente di scegliere i controlli di sicurezza da monitorare e suggerire in base alle esigenze di sicurezza delle risorse all'interno della sottoscrizione.

### Suggerimenti per la sicurezza

Il Centro sicurezza di Azure analizza lo stato di sicurezza delle risorse di Azure per identificare le potenziali vulnerabilità. Un elenco di suggerimenti illustra in dettaglio il processo di configurazione dei controlli necessari. Tra gli esempi sono inclusi:

- Provisioning di antimalware per identificare e rimuovere il software dannoso
- Configurazione dei gruppi di sicurezza di rete e delle regole per controllare il traffico verso le macchine virtuali
- Provisioning di un Web application firewall per la difesa da attacchi diretti alle applicazioni Web
- Distribuzione degli aggiornamenti di sistema mancanti
- Risoluzione delle configurazioni del sistema operativo che non corrispondono alle baseline consigliate

Fare clic sul riquadro **Suggerimenti** per visualizzarne un elenco. Fare clic su ogni suggerimento per visualizzare informazioni aggiuntive o per risolverlo.

![][3]

### Integrità delle risorse

Il riquadro **Integrità delle risorse** visualizza il comportamento di sicurezza complessivo dell'ambiente per tipo di risorsa, ovvero macchine virtuali, applicazioni Web e altre risorse.

Selezionare un tipo di risorsa nel riquadro **Integrità delle risorse** per visualizzare altre informazioni (Macchine virtuali nell'esempio seguente), incluso un elenco delle potenziali vulnerabilità di sicurezza identificate.

![][4]

### Avvisi di sicurezza

Il Centro sicurezza di Azure raccoglie, analizza e integra automaticamente i dati di log dalle risorse di Azure, dalla rete e dalle soluzioni dei partner, ad esempio antimalware e firewall. Quando vengono rilevate minacce, viene creato un avviso di sicurezza. Ad esempio, è incluso il rilevamento di:

- Macchine virtuali compromesse in comunicazione con indirizzi IP dannosi noti
- Malware avanzato rilevato tramite Segnalazione errori Windows
- Attacchi di forza bruta contro le macchine virtuali
- Avvisi di sicurezza da antimalware e firewall integrati

Fare clic sul riquadro **Avvisi di sicurezza** per visualizzare un elenco di avvisi con priorità.

![][5]

Se si seleziona un avviso, verranno visualizzate altre informazioni sull'attacco e suggerimenti su come risolverlo.

![][6]

## Introduzione
Per iniziare a usare Centro sicurezza di Azure, è necessario avere una sottoscrizione di Microsoft Azure. Il Centro sicurezza di Azure viene abilitato con la sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial).

È possibile accedere al Centro sicurezza di Azure tramite il [portale di Azure](http://azure.microsoft.com/features/azure-portal/). Per altre informazioni, vedere la [documentazione del portale](https://azure.microsoft.com/documentation/services/azure-portal/).

[Introduzione al Centro sicurezza di Azure](security-center-get-started.md) fornisce una rapida descrizione dei componenti di monitoraggio della sicurezza e gestione dei criteri del Centro sicurezza di Azure.

## Passaggi successivi
Questo documento ha illustrato il Centro sicurezza di Azure, le funzionalità principali e come iniziare a usarlo. Per altre informazioni, vedere gli argomenti seguenti:

- [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): come configurare i criteri di sicurezza
- [Implementazione dei suggerimenti relativi alla sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md): in che modo i suggerimenti facilitano la protezione delle risorse di Azure
- [Monitoraggio dello stato della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure
- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): come gestire e rispondere agli avvisi di sicurezza
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : ottenere le ultime informazioni e notizie sulla sicurezza di Azure

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.PNG
[2]: ./media/security-center-intro/security-policy.png
[3]: ./media/security-center-intro/recommendations.png
[4]: ./media/security-center-intro/resources-health.png
[5]: ./media/security-center-intro/security-alert.png
[6]: ./media/security-center-intro/security-alert-detail.png

<!---HONumber=AcomDC_1210_2015-->