<properties
   pageTitle="Guida introduttiva per il Centro sicurezza di Azure | Microsoft Azure"
   description="Questo documento consente di iniziare rapidamente a utilizzare il Centro protezione Azure guidando attraverso i componenti di gestione del monitoraggio e dei criteri di sicurezza e fornendo un collegamento ai passaggi successivi."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/26/2016"
   ms.author="terrylan"/>

# Guida introduttiva per il Centro sicurezza di Azure

Questo documento consente di iniziare rapidamente a utilizzare il Centro protezione Azure guidando attraverso i componenti di gestione del monitoraggio e dei criteri di sicurezza e fornendo un collegamento ai passaggi successivi.

> [AZURE.NOTE] Le informazioni contenute in questo documento si applicano alla versione di anteprima del Centro sicurezza di Azure. Il documento introduce il servizio usando una distribuzione di esempio. Questa non è una guida dettagliata.

## Informazioni sul Centro sicurezza PC di Azure
 Il Centro sicurezza impedisce, rileva e risponde alle minacce mediante visibilità e controllo avanzati della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

## Prerequisiti

Per iniziare a usare Centro sicurezza, è necessario avere una sottoscrizione di Microsoft Azure. Il Centro sicurezza viene abilitato con la sottoscrizione. Se non si ha una sottoscrizione, è possibile iscriversi per provare una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

 È possibile accedere al Centro sicurezza tramite il [portale di Azure](https://azure.microsoft.com/features/azure-portal/). Per altre informazioni, vedere la [documentazione sul portale](https://azure.microsoft.com/documentation/services/azure-portal/).


## Accedere al Centro sicurezza

Nel portale seguire questa procedura per accedere al Centro sicurezza:

1. Selezionare **Esplora** e scorrere fino all'opzione **Centro sicurezza**. ![Accedere al Centro protezione di Azure nel portale][1]

2. Selezionare **Centro sicurezza PC**. Verrà visualizzato il pannello **Centro sicurezza PC**.
3. Per semplificare l'accesso al pannello **Centro sicurezza PC** in futuro, selezionare l’opzione **Aggiungi pannello al dashboard** (in alto a destra). ![Opzione Aggiungi pannello al dashboard][2]

## Usare il Centro sicurezza

È possibile configurare criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure. Verranno configurati i **criteri** di sicurezza per la sottoscrizione:

1. Selezionare il riquadro **Criteri** nel pannello **Centro sicurezza**. ![Centro sicurezza][3]

2. Nel pannello **Criteri di sicurezza - Definire i criteri per sottoscrizione o gruppo di risorse** selezionare una sottoscrizione. ![Pannello Criteri di sicurezza nel Centro sicurezza di Azure][4]

3. Nel pannello **Criterio di sicurezza** attivare **Raccolta dati** per raccogliere automaticamente i log. L’attivazione della **Raccolta dati** inoltre effettuerà il provisioning dell'estensione di monitoraggio in tutte le VM correnti e nuove nella sottoscrizione.
4. Selezionare **Scegliere un account di archiviazione per area**. Per ciascuna area in cui si dispone di macchine virtuali in esecuzione, è necessario selezionare l'account di archiviazione in cui vengono archiviati i dati raccolti da tali macchine virtuali. Se non si sceglie un account di archiviazione per ogni area, verrà creato automaticamente. I dati raccolti vengono isolati logicamente da quelli di altri clienti per motivi di sicurezza.

     > [AZURE.NOTE] È consigliabile attivare la raccolta dati e scegliere prima un account di archiviazione a livello di sottoscrizione. I criteri di sicurezza possono essere impostati a livello di sottoscrizione di Azure e a livello di gruppo di risorse, ma la configurazione della raccolta dati e dell'account di archiviazione viene eseguita solo a livello di sottoscrizione.

5. Attivare le **Raccomandazioni** che si desidera visualizzare come parte dei criteri di sicurezza. Esempi:

 - L'attivazione di **Aggiornamenti del sistema** consente di analizzare tutte le macchine virtuali supportate per rilevare gli aggiornamenti del sistema operativo mancanti.
 - L'attivazione di **Regole della baseline** consente di analizzare le macchine virtuali supportate per identificare le configurazioni del sistema operativo che potrebbero rendere la macchina virtuale più vulnerabile agli attacchi.

**Raccomandazioni** indirizzo:

1. Tornare al pannello **Centro sicurezza** e selezionare il riquadro **Raccomandazioni**. Il Centro sicurezza analizza periodicamente lo stato di sicurezza delle risorse di Azure. Quando identifica potenziali vulnerabilità di sicurezza, al suo interno vengono visualizzate raccomandazioni.
2.	Selezionare ogni raccomandazione per visualizzare informazioni aggiuntive e/o intervenire per risolvere il problema. ![Raccomandazioni nel Centro sicurezza di Azure][5]

Visualizzare lo stato di integrità e di sicurezza delle risorse tramite **Integrità sicurezza delle risorse**:

1.	Tornare al pannello **Centro sicurezza PC**.
2.	Il riquadro **Integrità sicurezza delle risorse** contiene indicatori dello stato di sicurezza per **Macchine virtuali**, **Rete**, **SQL** e **Applicazioni**.
3.	Selezionare **Macchine virtuali** per visualizzare ulteriori informazioni.
4.	Il pannello **Macchine virtuali** visualizza un riepilogo dello stato, che mostra lo stato di programmi antimalware, aggiornamenti del sistema, riavvii e regole della baseline delle macchine virtuali.
5.	Selezionare un elemento in **RACCOMANDAZIONI SULLE MACCHINE VIRTUALI** per visualizzare altre informazioni e/o intervenire per configurare i controlli necessari.
6.	Eseguire il drill-down per visualizzare informazioni aggiuntive per specifiche macchine virtuali. ![Riquadro Integrità delle risorse nel Centro sicurezza di Azure][6]

Gestire gli **Avvisi di sicurezza**:

1.	Tornare al pannello **Centro sicurezza** e selezionare il riquadro **Avvisi di sicurezza**. Nel pannello **Avvisi di sicurezza** viene visualizzato un elenco di avvisi. Gli avvisi vengono generati dall'analisi dei log di sicurezza e dell'attività di rete effettuata dal Centro protezione. Sono inclusi anche gli avvisi generati da soluzioni partner integrate. ![Avvisi di sicurezza nel Centro sicurezza di Azure][7]

2.	Selezionare un avviso per visualizzare informazioni aggiuntive. ![Dettagli degli avvisi di sicurezza nel Centro sicurezza di Azure][8]

Visualizzare l'integrità delle **Soluzioni partner**:

1. Tornare al pannello **Centro sicurezza PC**. Il riquadro **Soluzioni partner** consente di monitorare a colpo d'occhio lo stato di integrità delle soluzioni dei partner integrate nella sottoscrizione di Azure.
2. Selezionare il riquadro **Soluzioni partner**. Viene visualizzato un pannello contenente un elenco di soluzioni dei partner connesse al Centro sicurezza. ![Soluzioni partner][9]

3. Selezionare una soluzione dei partner. In questo esempio viene selezionata la soluzione **F5-WAF2**. Viene visualizzato un pannello che mostra che lo stato della soluzione partner e le risorse associate alla soluzione. Selezionare **Console della soluzione** per aprire l'esperienza di gestione dei partner per questa soluzione. ![Dettagli della soluzione di un partner][10]

## Passaggi successivi
In questo documento sono stati presentati i componenti del monitoraggio della sicurezza e della gestione dei criteri nel Centro sicurezza. Per altre informazioni, vedere gli argomenti seguenti:

- [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
- [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md): informazioni sul modo in cui le raccomandazioni contribuiscono alla protezione delle risorse di Azure.
- [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
- [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): informazioni su come gestire gli avvisi di sicurezza e come rispondervi.
- [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): informazioni su come monitorare lo stato di integrità delle soluzioni dei partner.
- [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md): domande frequenti sull'uso del servizio.
- [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/): informazioni e notizie aggiornate sulla sicurezza di Azure.

<!--Image references-->
[1]: ./media/security-center-get-started/security-tile.png
[2]: ./media/security-center-get-started/pin-blade.png
[3]: ./media/security-center-get-started/security-center.png
[4]: ./media/security-center-get-started/security-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/partner-solutions-detail.png

<!---HONumber=AcomDC_0427_2016-->