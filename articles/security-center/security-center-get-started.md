<properties
   pageTitle="Introduzione al Centro sicurezza di Azure | Microsoft Azure"
   description="Questo documento consente di iniziare rapidamente a utilizzare il Centro protezione Azure guidando attraverso i componenti di gestione del monitoraggio e dei criteri di sicurezza e fornendo un collegamento ai passaggi successivi."
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
   ms.date="12/10/2015"
   ms.author="terrylan"/>

# Introduzione al Centro sicurezza di Azure

Questo documento consente di iniziare rapidamente a utilizzare il Centro protezione Azure guidando attraverso i componenti di gestione del monitoraggio e dei criteri di sicurezza e fornendo un collegamento ai passaggi successivi.

> [AZURE.NOTE] Le informazioni contenute in questo documento si applicano alla versione di anteprima del Centro sicurezza di Azure. Questa è un'introduzione al servizio con un esempio di distribuzione, Questa non è una guida dettagliata.

## Informazioni sul Centro sicurezza PC di Azure
Il Centro sicurezza PC di Azure impedisce, rileva e risponde alle minacce mediante visibilità e controllo avanzati della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

## Prerequisiti

Per iniziare a usare Centro sicurezza PC di Azure, è necessario avere una sottoscrizione di Microsoft Azure. Il Centro sicurezza PC di Azure viene abilitato con la sottoscrizione di Azure. Se non si dispone di una sottoscrizione, è possibile iscriversi per provare la [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

È possibile accedere al Centro sicurezza di Azure tramite il [portale di Azure](https://azure.microsoft.com/features/azure-portal/). Per altre informazioni, vedere la [documentazione del portale](https://azure.microsoft.com/documentation/services/azure-portal/).


## Accesso al Centro sicurezza PC di Azure

Nel portale attenersi alla procedura seguente per accedere al Centro sicurezza di Azure:

1. Selezionare **Sfoglia** e scorrere le opzioni del **Centro sicurezza PC**. ![][1]

2. Selezionare **Centro sicurezza PC**. Verrà visualizzato il pannello **Centro sicurezza PC**.
3. Per semplificare l'accesso al pannello **Centro sicurezza PC** in futuro, selezionare l’opzione **Aggiungi pannello al dashboard** (in alto a destra). ![][2]

## Utilizzo del Centro sicurezza PC di Azure

Configurare un **criterio** di sicurezza per le sottoscrizioni:

4. Fare clic sul riquadro **Criterio di sicurezza** nel pannello **Centro sicurezza PC**.
5. Nel pannello **Criterio di sicurezza: definizione dei criteri per sottoscrizione**, selezionare una sottoscrizione.
6. Nel pannello **Criterio di sicurezza** attivare **Raccolta dati** per raccogliere automaticamente i log. L’attivazione della **Raccolta dati** inoltre effettuerà il provisioning dell'estensione di monitoraggio in tutte le VM correnti e nuove nella sottoscrizione.
7. Fare clic su **Scegliere gli account di archiviazione**. Per ciascuna area in cui si dispone di macchine virtuali in esecuzione, è necessario selezionare l'account di archiviazione in cui vengono archiviati i dati raccolti da tali macchine virtuali. Se non si sceglie un account di archiviazione per ogni area, ne verrà creato uno automaticamente. I dati raccolti vengono isolati logicamente da quelli di altri clienti per motivi di sicurezza.
8. Attivare le **Raccomandazioni** che si desidera visualizzare come parte dei criteri di sicurezza. Esempi:

  - L’attivazione degli **Aggiornamenti del sistema** consente di analizzare tutte le macchine virtuali supportate per rilevare gli aggiornamenti del sistema operativo mancanti.
  - L’attivazione delle **Regole di base** consente di analizzare le macchine virtuali supportate per identificare le configurazioni del sistema operativo che potrebbero rendere la macchina virtuale più vulnerabile agli attacchi.

![][3]

**Raccomandazioni** indirizzo:

9. Tornare al pannello **Centro sicurezza PC** e fare clic sul riquadro **Raccomandazioni**.
10.	Il Centro sicurezza di Azure analizza periodicamente lo stato di sicurezza delle risorse di Azure. Quando identifica potenziali vulnerabilità di sicurezza, al suo interno vengono visualizzate raccomandazioni.
11.	Fare clic su ogni raccomandazione per visualizzare ulteriori informazioni e/o intervenire per risolvere il problema.

![][4]

Visualizzare lo stato di integrità e di sicurezza delle risorse tramite **Integrità risorse**:

12.	Tornare al pannello **Centro sicurezza PC**.
13.	Il riquadro **Integrità risorse** contiene indicatori dello stato della protezione per **Macchine virtuali**, **Rete**, **SQL** e **Applicazioni**.
14.	Selezionare **Macchine virtuali** per visualizzare ulteriori informazioni.
15.	Il pannello **Macchine virtuali** visualizza un riepilogo dello stato, inclusi antimalware, aggiornamenti del sistema, riavvii e regole di base delle macchine virtuali.
16.	Selezionare un elemento in **PASSAGGI DI PREVENZIONE** per visualizzare ulteriori informazioni e/o intraprendere le azioni per configurare i controlli necessari.
17.	Eseguire il drill down per visualizzare informazioni aggiuntive per specifiche macchine virtuali.

![][5]

**Avvisi di sicurezza** indirizzo:

19.	Tornare al pannello **Centro sicurezza PC** e fare clic sul riquadro **Avvisi di sicurezza**.
20.	Nel pannello **Avvisi di sicurezza** viene visualizzato un elenco di avvisi. Gli avvisi sono stati rilevati dall'analisi del Centro protezione Azure dei registri di sicurezza e delle attività di rete. Sono inclusi anche gli avvisi generati da soluzioni partner integrate. ![][6]

21.	Fare clic su un avviso per visualizzare informazioni aggiuntive.

  ![][7]

## Passaggi successivi
In questo documento sono stati presentati i componenti del monitoraggio della sicurezza e della gestione dei criteri nel Centro protezione Azure. Per altre informazioni, vedere gli argomenti seguenti:

- [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): come configurare i criteri di sicurezza
- [Gestione dei suggerimenti relativi alla sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md): in che modo i suggerimenti facilitano la protezione delle risorse di Azure
- [Monitoraggio dello stato della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure
- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : ottenere le ultime informazioni e notizie sulla sicurezza di Azure

<!--Image references-->
[1]: ./media/security-center-get-started/security-tile.png
[2]: ./media/security-center-get-started/pin-blade.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/recommendations.png
[5]: ./media/security-center-get-started/resources-health.png
[6]: ./media/security-center-get-started/security-alert.png
[7]: ./media/security-center-get-started/security-alert-detail.png

<!---HONumber=AcomDC_0128_2016-->