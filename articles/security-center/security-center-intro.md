<properties
   pageTitle="Introduzione al Centro sicurezza di Azure | Microsoft Azure"
   description="Informazioni sul Centro sicurezza di Azure, le funzionalità principali e il funzionamento."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/21/2016"
   ms.author="terrylan"/>  

# Introduzione al Centro sicurezza di Azure

Informazioni sul Centro sicurezza di Azure, le funzionalità principali e il funzionamento.

> [AZURE.NOTE] Il documento introduce il servizio usando una distribuzione di esempio.

## Informazioni sul Centro sicurezza PC di Azure
 Il Centro sicurezza impedisce, rileva e risponde alle minacce mediante visibilità e controllo avanzati della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni di Azure, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

##	Funzionalità principali
 Il Centro sicurezza fornisce funzionalità efficaci e facili da usare, integrate in Azure, per la prevenzione e il rilevamento delle minacce e la relativa risposta. Funzionalità principali:

| | |
|----- |-----|
| Prevenzione | Monitora lo stato della sicurezza delle risorse di Azure |
| | Definisce i criteri per le sottoscrizioni e i gruppi di risorse di Azure in base ai requisiti di sicurezza dell'azienda, ai tipi di applicazioni in uso o al livello di riservatezza dei dati |
| | Usa i suggerimenti relativi alla sicurezza in base ai criteri per guidare i proprietari del servizio nel processo di implementazione dei controlli necessari |
| | Distribuisce rapidamente i servizi di sicurezza e i dispositivi di Microsoft e dei partner |
| Rilevamento |Raccoglie e analizza automaticamente i dati di sicurezza dalle risorse di Azure, dalla rete e dalle soluzioni dei partner, ad esempio programmi antimalware e firewall |
| | Sfrutta le informazioni sulle minacce globali da prodotti e servizi Microsoft, Microsoft Digital Crimes Unit (DCU) e Microsoft Security Response Center (MSRC), nonché da feed esterni |
| | Applica analisi avanzate, tra cui analisi del comportamento e di Machine Learning |
| Risposta | Fornisce eventi imprevisti o avvisi di sicurezza con priorità |
| | Offre informazioni dettagliate sull'origine dell'attacco e sulle risorse interessate |
| | Suggerisce alcune modalità per arrestare l'attacco attuale e prevenire quelli futuri |

## Procedura dettagliata introduttiva
 È possibile accedere al Centro sicurezza tramite il [portale di Azure](https://azure.microsoft.com/features/azure-portal/). [Accedere al portale](https://portal.azure.com), selezionare **Esplora** e scorrere fino all'opzione **Centro sicurezza** oppure selezionare il riquadro **Centro sicurezza** aggiunto in precedenza al dashboard del portale.

![Riquadro Sicurezza nel portale di Azure][1]

Nel Centro sicurezza è possibile impostare i criteri di sicurezza, monitorare le configurazioni di sicurezza e visualizzare gli avvisi di sicurezza.

### Criteri di sicurezza

È possibile definire i criteri per le sottoscrizioni e i gruppi di risorse di Azure in base ai requisiti di sicurezza della società. È anche possibile personalizzarli in base ai tipi di applicazioni in uso o al livello di riservatezza dei dati in ogni sottoscrizione. Ad esempio, le risorse usate per lo sviluppo o il test possono avere requisiti di sicurezza diversi da quelli delle applicazioni di produzione. Allo stesso modo, le applicazioni con dati regolamentati, come le informazioni personali, possono richiedere un livello di sicurezza più elevato.

> [AZURE.NOTE] Per modificare i criteri di sicurezza a livello di sottoscrizione o di gruppo di risorse, è necessario essere il proprietario della sottoscrizione o un collaboratore.

Nel pannello **Centro sicurezza** selezionare il riquadro **Criteri** per visualizzare un elenco di sottoscrizioni e gruppi di risorse.

![Pannello Centro sicurezza][2]

Nel pannello **Criteri di sicurezza** selezionare una sottoscrizione per visualizzare i dettagli dei criteri.

![Sottoscrizione pannello Criteri di sicurezza][3]

**Raccolta dati** (vedere sopra) abilita la raccolta dei dati per i criteri di sicurezza. L'abilitazione fornisce:

- Analisi giornaliera di tutte le macchine virtuali supportate per il monitoraggio della sicurezza e le raccomandazioni.
- Raccolta di eventi di sicurezza per l'analisi e il rilevamento delle minacce.

**Scegli un account di archiviazione per ogni area** (vedere sopra) consente di scegliere, per ogni area in cui sono in esecuzione delle macchine virtuali, l'account di archiviazione in cui vengono archiviati i dati raccolti da tali macchine virtuali. Se non si sceglie un account di archiviazione per ogni area, verrà creato automaticamente. I dati raccolti vengono isolati logicamente da quelli di altri clienti per motivi di sicurezza.

> [AZURE.NOTE] La raccolta dei dati e la scelta di un account di archiviazione per ogni area è configurata a livello di sottoscrizione.

Selezionare **Criteri di prevenzione** (vedere sopra) per aprire il pannello **Criteri di prevenzione**. **Mostra raccomandazioni per** consente di scegliere i controlli di sicurezza da monitorare e suggerire in base alle esigenze di sicurezza delle risorse all'interno della sottoscrizione.

Successivamente, selezionare un gruppo di risorse per visualizzare i dettagli dei criteri.

![Gruppo di risorse pannello Criteri di sicurezza][4]

**Ereditarietà** (vedere sopra) consente di definire il gruppo di risorse come:

- Ereditato (impostazione predefinita), ossia tutti i criteri di sicurezza per questo gruppo di risorse vengono ereditati dal livello della sottoscrizione.
- Univoco, ossia il gruppo di risorse avrà criteri di sicurezza personalizzati. Può essere necessario apportare modifiche in **Mostra raccomandazioni per**.

> [AZURE.NOTE] In caso di conflitto tra criteri definiti a livello di sottoscrizione e quelli a livello di gruppo di risorse, i criteri a livello di gruppo di risorse avranno la precedenza.

### Suggerimenti per la sicurezza

 Il Centro sicurezza analizza lo stato di sicurezza delle risorse di Azure per identificare le potenziali vulnerabilità di sicurezza. Un elenco di suggerimenti illustra in dettaglio il processo di configurazione dei controlli necessari. Tra gli esempi sono inclusi:

- Provisioning di antimalware per identificare e rimuovere il software dannoso
- Configurazione dei gruppi di sicurezza di rete e delle regole per controllare il traffico verso le macchine virtuali
- Provisioning di un Web application firewall per la difesa da attacchi diretti alle applicazioni Web
- Distribuzione degli aggiornamenti di sistema mancanti
- Risoluzione delle configurazioni del sistema operativo che non corrispondono alle baseline consigliate

Fare clic sul riquadro **Suggerimenti** per visualizzarne un elenco. Fare clic su ogni raccomandazione per visualizzare informazioni aggiuntive o intervenire per risolvere il problema.

![Raccomandazioni di sicurezza nel Centro sicurezza di Azure][5]

### Integrità delle risorse

Il riquadro **Integrità sicurezza delle risorse** visualizza il comportamento di sicurezza complessivo dell'ambiente per tipo di risorsa, ad esempio macchine virtuali, applicazioni Web e altre risorse.

Selezionare un tipo di risorsa nel riquadro **Integrità sicurezza delle risorse** per visualizzare altre informazioni, incluso un elenco delle potenziali vulnerabilità di sicurezza identificate. Nell'esempio seguente è selezionata la risorsa **Macchine virtuali**.

![Riquadro Integrità delle risorse][6]

### Avvisi di sicurezza

 Il Centro sicurezza raccoglie, analizza e integra automaticamente i dati di log dalle risorse di Azure, dalla rete e dalle soluzioni dei partner, ad esempio programmi antimalware e firewall. Quando vengono rilevate minacce, viene creato un avviso di sicurezza. Ad esempio, è compreso il rilevamento di:

- Macchine virtuali compromesse in comunicazione con indirizzi IP dannosi noti
- Malware avanzato rilevato tramite Segnalazione errori Windows
- Attacchi di forza bruta contro le macchine virtuali
- Avvisi di sicurezza da programmi antimalware e firewall integrati

Fare clic sul riquadro **Avvisi di sicurezza** per visualizzare un elenco di avvisi con priorità.

![Avvisi di sicurezza][7]

Se si seleziona un avviso, verranno visualizzate altre informazioni sull'attacco e suggerimenti su come risolverlo.

![Dettagli dell'avviso di sicurezza][8]

### Soluzioni partner

Il riquadro **Soluzioni partner** consente di monitorare a colpo d'occhio lo stato di integrità delle soluzioni dei partner integrate nella sottoscrizione di Azure. Il Centro sicurezza visualizza gli avvisi provenienti dalle soluzioni.

Selezionare il riquadro **Soluzioni partner**. Viene visualizzato un pannello contenente un elenco di tutte le soluzioni dei partner connessi.

![Soluzioni partner][9]

## Introduzione
Per iniziare a usare Centro sicurezza, è necessario avere una sottoscrizione di Microsoft Azure. Il Centro sicurezza viene abilitato con la sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

 È possibile accedere al Centro sicurezza tramite il [portale di Azure](https://azure.microsoft.com/features/azure-portal/). Per altre informazioni, vedere la [documentazione sul portale](https://azure.microsoft.com/documentation/services/azure-portal/).

[Introduzione al Centro sicurezza di Azure](security-center-get-started.md) fornisce una rapida descrizione dei componenti di monitoraggio della sicurezza e gestione dei criteri del Centro sicurezza.

## Vedere anche
Questo documento ha illustrato il Centro sicurezza, le funzionalità principali e come iniziare a usarlo. Per altre informazioni, vedere gli argomenti seguenti:

- [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
- [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md): informazioni sul modo in cui le raccomandazioni facilitano la protezione delle risorse di Azure.
- [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
- [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): informazioni su come monitorare l'integrità delle soluzioni dei partner.
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): informazioni e notizie aggiornate sulla sicurezza di Azure.

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.PNG
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png
[9]: ./media/security-center-intro/partner-solutions.png

<!---HONumber=AcomDC_0810_2016-->
