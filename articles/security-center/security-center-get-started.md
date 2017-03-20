---
title: Guida introduttiva per il Centro sicurezza di Azure | Documentazione Microsoft
description: Questo documento consente di iniziare a usare rapidamente il Centro sicurezza di Azure illustrando i componenti di monitoraggio della sicurezza e gestione dei criteri e fornendo un collegamento ai passaggi successivi.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 40636aa9d847b97ea872bf4689a1da6e3eb23eb4
ms.lasthandoff: 03/07/2017


---
# <a name="azure-security-center-quick-start-guide"></a>Guida introduttiva per il Centro sicurezza di Azure
Questo documento consente di iniziare a usare rapidamente il Centro sicurezza di Azure illustrando i componenti di monitoraggio della sicurezza e gestione dei criteri del Centro sicurezza.

> [!NOTE]
> Questo articolo introduce il servizio usando una distribuzione di esempio. Non si tratta di una guida dettagliata.
>
>

## <a name="prerequisites"></a>Prerequisiti
Per iniziare a usare Centro sicurezza, è necessario avere una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).

Il livello gratuito del Centro sicurezza viene abilitato automaticamente con la sottoscrizione e consente di esaminare lo stato di sicurezza delle risorse di Azure. Offre la gestione di criteri di sicurezza di base, raccomandazioni sulla sicurezza e l'integrazione con prodotti e servizi dei partner di Azure.

È possibile accedere al Centro sicurezza tramite il [portale di Azure](https://azure.microsoft.com/features/azure-portal/). Per altre informazioni sul portale di Azure, vedere la [documentazione sul portale](https://azure.microsoft.com/documentation/services/azure-portal/).

## <a name="permissions"></a>autorizzazioni
In Centro sicurezza gli utenti visualizzeranno solo informazioni relative alle risorse di Azure quando sono assegnati al ruolo di proprietario, collaboratore o lettore per la sottoscrizione o il gruppo di risorse a cui tali risorse appartengono. Per altre informazioni sui ruoli e sulle azioni consentite in Centro sicurezza, vedere [Permissions in Azure Security Center](security-center-permissions.md) (Autorizzazioni in Centro sicurezza di Azure).

## <a name="data-collection"></a>Raccolta dei dati
Il Centro sicurezza raccoglie i dati dalle macchine virtuali per valutarne lo stato della sicurezza, indicare raccomandazioni sulla sicurezza e segnalare le minacce. La prima volta che si accede al Centro sicurezza, la raccolta dati viene abilitata in tutte le macchine virtuali della sottoscrizione. La raccolta dati è consigliata, ma è possibile rifiutare esplicitamente disattivandola nei criteri del Centro sicurezza.

I passaggi seguenti descrivono come accedere al Centro sicurezza e usarne i componenti. Questi passaggi illustrano come disattivare la raccolta dei dati se si sceglie di rifiutare esplicitamente questa opzione.

## <a name="access-security-center"></a>Accedere al Centro sicurezza
Nel portale seguire questa procedura per accedere al Centro sicurezza:

1. Scegliere **Centro sicurezza** dal menu **Microsoft Azure**.

   ![Menu di Azure][1]
2. Se si accede al Centro sicurezza per la prima volta, verrà visualizzato il pannello **iniziale**. Selezionare **Sì! Voglio avviare il Centro sicurezza di Azure** per aprire il pannello **Centro sicurezza** e abilitare la raccolta dati.
   ![Schermata iniziale][10]
3. Dopo aver avviato il Centro sicurezza dal pannello iniziale o aver selezionato Centro sicurezza dal menu Microsoft Azure, si aprirà il pannello **Centro sicurezza**. Per semplificare l'accesso al pannello **Centro sicurezza** in futuro, selezionare l'opzione **Aggiungi pannello al dashboard** (in alto a destra).
   ![Opzione Aggiungi pannello al dashboard][2]

## <a name="use-security-center"></a>Usare il Centro sicurezza
È possibile configurare criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure. Configurare i criteri di sicurezza per la sottoscrizione:

1. Selezionare il riquadro **Criteri** nel pannello **Centro sicurezza**.
   ![Criteri di sicurezza][3]
2. Selezionare una sottoscrizione nel pannello **Criteri di sicurezza - Definire il criterio per ogni sottoscrizione o gruppo di risorse**.
3. Nel pannello **Criteri di sicurezza** **Raccolta dati** è abilitato per raccogliere automaticamente i log. Il provisioning dell'estensione di monitoraggio viene effettuato in tutte le VM correnti e nuove nella sottoscrizione. È possibile rifiutare esplicitamente la raccolta dati impostando **Raccolta dati** su **No**, ma in questo modo il Centro sicurezza non potrà fornire avvisi e raccomandazioni sulla sicurezza.
4. Nel pannello **Criteri di sicurezza** selezionare **Scegliere un account di archiviazione per area**. Per ciascuna area in cui sono in esecuzione VM, è necessario selezionare l'account di archiviazione in cui vengono archiviati i dati raccolti dalle VM. Se non si sceglie un account di archiviazione per ogni area, viene creato un account di archiviazione per il gruppo di risorse securitydata e inserito in esso. I dati raccolti vengono isolati in modo logico da quelli di altri clienti per motivi di sicurezza.

   > [!NOTE]
   > È consigliabile abilitare la raccolta dati e scegliere prima un account di archiviazione a livello di sottoscrizione. I criteri di sicurezza possono essere impostati a livello di sottoscrizione di Azure e a livello di gruppo di risorse, ma la configurazione della raccolta dati e dell'account di archiviazione viene eseguita solo a livello di sottoscrizione.
   >
   >
5. Selezionare **Criteri di prevenzione** nel pannello **Criteri di sicurezza**. Verrà visualizzato il pannello **Criteri di prevenzione**.
   ![Criteri di prevenzione][4]
6. Nel pannello **Criteri di prevenzione** attivare le raccomandazioni da visualizzare nell'ambito dei criteri di sicurezza. Esempi:

   * Impostando **Aggiornamenti del sistema** su **Sì**, tutte le macchine virtuali supportate verranno analizzate per rilevare gli aggiornamenti del sistema operativo mancanti.
   * Impostando **Vulnerabilità del sistema operativo** su **Sì**, tutte le macchine virtuali supportate verranno analizzate per identificare le configurazioni del sistema operativo che possono esporre la macchina virtuale ad attacchi.

### <a name="view-recommendations"></a>Visualizzare raccomandazioni
1. Tornare al pannello **Centro sicurezza** e selezionare il riquadro **Raccomandazioni**. Il Centro sicurezza analizza periodicamente lo stato di sicurezza delle risorse di Azure. Quando identifica potenziali vulnerabilità della sicurezza, il Centro sicurezza indica raccomandazioni nel pannello **Raccomandazioni**.
   ![Raccomandazioni nel Centro sicurezza di Azure][5]
2. Selezionare una raccomandazione nel pannello **Raccomandazioni** per visualizzare altre informazioni e/o intervenire per risolvere il problema.

### <a name="view-the-health-and-security-state-of-your-resources"></a>Visualizzare lo stato di integrità e di sicurezza delle risorse
1. Tornare al pannello **Centro sicurezza PC** . Il riquadro **Integrità sicurezza delle risorse** contiene indicatori dello stato di sicurezza per macchine virtuali, rete, dati e applicazioni.
2. Selezionare **Macchine virtuali** per visualizzare ulteriori informazioni. Il pannello **Macchine virtuali** visualizzerà un riepilogo dello stato di programmi antimalware, aggiornamenti del sistema, riavvii e vulnerabilità del sistema operativo delle macchine virtuali.
   ![Riquadro Integrità sicurezza delle risorse nel Centro sicurezza di Azure][6]
3. Selezionare una raccomandazione in **RACCOMANDAZIONI SULLE MACCHINE VIRTUALI** per visualizzare altre informazioni e/o intervenire per configurare i controlli necessari.
4. Selezionare una VM in **Macchine virtuali** per visualizzare altri dettagli.

### <a name="view-security-alerts"></a>Visualizzare avvisi di sicurezza
1. Tornare al pannello **Centro sicurezza** e selezionare il riquadro **Avvisi di sicurezza**. Nel pannello **Avvisi di sicurezza** verrà visualizzato un elenco di avvisi. Gli avvisi vengono generati dall'analisi dei log di sicurezza e dell'attività di rete effettuata dal Centro sicurezza. Sono inclusi gli avvisi generati da soluzioni partner integrate.
   ![Avvisi di sicurezza nel Centro sicurezza di Azure][7]

   > [!NOTE]
   > Gli avvisi di sicurezza sono disponibili solo se è abilitato il livello Standard del Centro sicurezza. Per il livello Standard è disponibile una versione di valutazione gratuita di 60 giorni. Vedere [Passaggi successivi](#next-steps) per informazioni su come ottenere il livello Standard.
   >
   >
2. Selezionare un avviso per visualizzare informazioni aggiuntive. In questo esempio verrà selezionato **Modified system binary discovered** (Individuato file binario di sistema modificato). Si apriranno pannelli con altri dettagli dell'avviso.
   ![Dettagli degli avvisi di sicurezza nel Centro sicurezza di Azure][8]

### <a name="view-the-health-of-your-partner-solutions"></a>Visualizzare l'integrità delle soluzioni partner
1. Tornare al pannello **Centro sicurezza PC** . Il riquadro **Soluzioni partner** consente di monitorare a colpo d'occhio lo stato di integrità delle soluzioni dei partner integrate nella sottoscrizione di Azure.
2. Selezionare il riquadro **Soluzioni partner** . Verrà visualizzato un pannello con un elenco di soluzioni dei partner connesse al Centro sicurezza.
   ![soluzioni partner][9]
3. Selezionare una soluzione dei partner. In questo esempio verrà selezionata la soluzione **F5-WAF**.  Verrà visualizzato un pannello con lo stato della soluzione partner e le risorse associate alla soluzione. Selezionare **Console della soluzione** per aprire l'esperienza di gestione dei partner per questa soluzione.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono stati presentati i componenti di monitoraggio della sicurezza e di gestione dei criteri del Centro sicurezza. Dopo aver acquisito familiarità con il Centro sicurezza, provare a eseguire questi passaggi:

* Configurare i criteri di sicurezza per la sottoscrizione di Azure. Per altre informazioni, vedere [Impostare i criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md).
* Usare le raccomandazioni presenti nel Centro sicurezza per la protezione delle risorse di Azure. Per altre informazioni, vedere [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).
* Esaminare e gestire gli avvisi di sicurezza correnti. Per altre informazioni, vedere [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md).
* Altre informazioni sulle [funzionalità avanzate di rilevamento delle minacce](security-center-detection-capabilities.md) incluse con il [livello Standard](security-center-pricing.md) del Centro sicurezza. Il livello Standard è gratuito per i primi 60 giorni.
* In caso di domande sull'uso del Centro sicurezza, vedere [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md).

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/prevention-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png

