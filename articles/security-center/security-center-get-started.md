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
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: c28f92af96f31d1c386cf072f83fc142b9a7f588
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-quick-start-guide"></a>Guida introduttiva per il Centro sicurezza di Azure
Questo documento consente di iniziare a usare rapidamente il Centro sicurezza di Azure illustrando i componenti di monitoraggio della sicurezza e gestione dei criteri del Centro sicurezza.

## <a name="prerequisites"></a>Prerequisiti
Per iniziare a usare Centro sicurezza, è necessario avere una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).

Il livello gratuito del Centro sicurezza viene abilitato automaticamente in tutte le sottoscrizioni di Azure e offre criteri di sicurezza, valutazione continua della sicurezza e raccomandazioni sulla sicurezza utili per proteggere le risorse di Azure.

È possibile accedere al Centro sicurezza tramite il [portale di Azure](https://azure.microsoft.com/features/azure-portal/). Per altre informazioni sul portale di Azure, vedere la [documentazione sul portale](https://azure.microsoft.com/documentation/services/azure-portal/).

## <a name="permissions"></a>Autorizzazioni
In Centro sicurezza gli utenti possono visualizzare solo informazioni relative a una risorsa quando dispongono del ruolo di proprietario, collaboratore o lettore per la sottoscrizione o il gruppo di risorse cui tali risorse appartengono. Per altre informazioni sui ruoli e sulle azioni consentite in Centro sicurezza, vedere [Permissions in Azure Security Center](security-center-permissions.md) (Autorizzazioni in Centro sicurezza di Azure).

## <a name="data-collection"></a>Raccolta dei dati
Il Centro sicurezza raccoglie i dati delle macchine virtuali di Azure e dei computer che non hanno Azure per monitorare le minacce e le vulnerabilità della sicurezza. I dati vengono raccolti tramite Microsoft Monitoring Agent, che legge diverse configurazioni correlate alla sicurezza oltre ai registri eventi del computer e copia i dati nell'area di lavoro per eseguire l'analisi. Il Centro sicurezza effettua il provisioning di Microsoft Monitoring Agent in tutte le macchine virtuali di Azure supportate esistenti e in quelle nuove che vengono create. Vedere [Abilitare la raccolta dati](security-center-enable-data-collection.md) per altre informazioni sul funzionamento della raccolta dati.

Il provisioning automatico è fortemente consigliato ed è obbligatorio per le sottoscrizioni del livello Standard del Centro sicurezza. La disabilitazione automatica del provisioning limita il monitoraggio delle risorse.

Per altre informazioni sui piani tariffari gratuito e standard vedere [Prezzi del Centro sicurezza](security-center-pricing.md).

I passaggi seguenti descrivono come accedere al Centro sicurezza e usarne i componenti.

> [!NOTE]
> Questo articolo introduce il servizio usando una distribuzione di esempio. Non si tratta di una guida dettagliata.
>
>

## <a name="access-security-center"></a>Accedere al Centro sicurezza
Nel portale seguire questa procedura per accedere al Centro sicurezza:

1. Scegliere **Centro sicurezza** dal menu **Microsoft Azure**.

   ![Menu di Azure][1]
2. Se si accede al Centro sicurezza per la prima volta, verrà visualizzato il pannello **iniziale**. Selezionare **Avvia il Centro sicurezza** per aprire il **Centro sicurezza**.
   ![Schermata iniziale][10]
3. Dopo aver avviato il Centro sicurezza dal pannello iniziale o aver selezionato Centro sicurezza dal menu Microsoft Azure, si aprirà il **Centro sicurezza**. Per semplificare l'accesso al pannello **Centro sicurezza** in futuro, selezionare l'opzione **Aggiungi pannello al dashboard** (in alto a destra).
   ![Opzione Aggiungi pannello al dashboard][2]

## <a name="use-security-center"></a>Usare il Centro sicurezza
È possibile configurare criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure. Configurare i criteri di sicurezza per la sottoscrizione:

1. Scegliere **Criteri di sicurezza** dal menu principale del Centro sicurezza.
2. In **Centro sicurezza - Criteri di sicurezza** selezionare una sottoscrizione.
3. In **Criteri di sicurezza - Raccolta dati** viene attivato **Provisioning automatico**. Il Centro sicurezza effettua il provisioning di Microsoft Monitoring Agent in tutte le macchine virtuali di Azure supportate esistenti e in quelle nuove che vengono create.

    ![Criteri di sicurezza][12]

4. Nel pannello **Criteri di sicurezza** selezionare il componente dei criteri **Criteri di sicurezza**.

     ![Criteri di sicurezza][11]

5. In **Mostra raccomandazioni per** attivare le raccomandazioni da visualizzare nell'ambito dei criteri di sicurezza. Esempi:

   * Impostando **Aggiornamenti del sistema** su **On** (Attivo), tutte le macchine virtuali supportate verranno analizzate per rilevare gli aggiornamenti del sistema operativo mancanti.
   * Impostando **Vulnerabilità del sistema operativo** su **On** (Attivo), tutte le macchine virtuali supportate verranno analizzate per identificare le configurazioni del sistema operativo che possono esporre la macchina virtuale ad attacchi.

6. Selezionare **Salva**.

### <a name="view-recommendations"></a>Visualizzare raccomandazioni
1. Tornare al pannello **Centro sicurezza** e selezionare il riquadro **Raccomandazioni**. Il Centro sicurezza analizza periodicamente lo stato di sicurezza delle risorse di Azure. Quando identifica potenziali vulnerabilità della sicurezza, il Centro sicurezza indica raccomandazioni nel pannello **Raccomandazioni**.
   ![Raccomandazioni nel Centro sicurezza di Azure][5]
2. Selezionare una raccomandazione nel pannello **Raccomandazioni** per visualizzare altre informazioni e/o intervenire per risolvere il problema.

### <a name="view-the-security-state-of-your-resources"></a>Visualizzare lo stato di sicurezza delle risorse
1. Tornare al pannello **Centro sicurezza PC** . La sezione **Prevenzione** del dashboard contiene indicatori dello stato di sicurezza per macchine virtuali, rete, dati e applicazioni.
2. Selezionare **Calcolo** per visualizzare altre informazioni. Viene visualizzato il pannello **Calcolo** con tre schede:

  - **Panoramica**: contiene consigli sul monitoraggio e sulle macchine virtuali.
  - **VM e computer**: elenca lo stato di sicurezza corrente di tutte le VM e di tutti i computer.
  - **Servizi cloud**: elenca di tutti i ruoli Web e di lavoro monitorati dal Centro sicurezza.

    ![Integrità della sicurezza delle risorse di calcolo][6]

3. Nella scheda **Panoramica** selezionare una raccomandazione per visualizzare altre informazioni e/o intervenire per configurare i controlli necessari.
4. Nella scheda **VM e computer** selezionare una risorsa per visualizzare altri dettagli.

### <a name="view-security-alerts"></a>Visualizzare avvisi di sicurezza
1. Tornare al pannello **Centro sicurezza** e selezionare il riquadro **Avvisi di sicurezza**. Nel pannello **Avvisi di sicurezza** verrà visualizzato un elenco di avvisi. Gli avvisi vengono generati dall'analisi dei log di sicurezza e dell'attività di rete effettuata dal Centro sicurezza. Sono inclusi gli avvisi generati da soluzioni partner integrate.
   ![Avvisi di sicurezza nel Centro sicurezza di Azure][7]

2. Selezionare un avviso per visualizzare informazioni aggiuntive. In questo esempio verrà selezionato **Modified system binary discovered in dump filter** (Individuato file binario di sistema modificato nel filtro di dump). Si apriranno pannelli con altri dettagli dell'avviso.
   ![Dettagli degli avvisi di sicurezza nel Centro sicurezza di Azure][8]

### <a name="view-the-health-of-your-partner-solutions"></a>Visualizzare l'integrità delle soluzioni partner
1. Tornare al pannello **Centro sicurezza PC** . Il riquadro **Soluzioni di sicurezza** consente di monitorare a colpo d'occhio lo stato di integrità delle soluzioni dei partner integrate nella sottoscrizione di Azure.
2. Selezionare il riquadro **Soluzioni di sicurezza**. Verrà visualizzato un pannello con un elenco di soluzioni dei partner connesse al Centro sicurezza.
   ![soluzioni partner][9]
3. Selezionare una soluzione dei partner. Verrà visualizzato un pannello con lo stato della soluzione partner e le risorse associate alla soluzione. Selezionare **Console della soluzione** per aprire l'esperienza di gestione dei partner per questa soluzione.

   ![Soluzioni partner][13]

## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono stati presentati i componenti di monitoraggio della sicurezza e di gestione dei criteri del Centro sicurezza. Dopo aver acquisito familiarità con il Centro sicurezza, provare a eseguire questi passaggi:

* Per configurare un criterio di sicurezza per la sottoscrizione di Azure, vedere [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md).
* Per usare le raccomandazioni nel Centro sicurezza per proteggere le risorse di Azure, vedere [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).
* Per riesaminare e gestire gli avvisi di sicurezza correnti, vedere [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md).
- Per altre informazioni sull'integrazione con i partner per migliorare la sicurezza complessiva, vedere [Integrazione di partner e soluzioni](security-center-partner-integration.md).
- Per informazioni sulla gestione e la protezione dei dati nel Centro sicurezza, vedere [Sicurezza dei dati nel Centro sicurezza di Azure](security-center-data-security.md).
* Altre informazioni sulle [funzionalità avanzate di rilevamento delle minacce](security-center-detection-capabilities.md) incluse con il [livello Standard](security-center-pricing.md) del Centro sicurezza. Il livello Standard è gratuito per i primi 60 giorni.
* In caso di domande sull'uso del Centro sicurezza, vedere [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md).

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png
[11]: ./media/security-center-get-started/show-recommendations-for.png
[12]: ./media/security-center-get-started/automatic-provisioning.png
[13]: ./media/security-center-get-started/partner-solutions-detail.png
