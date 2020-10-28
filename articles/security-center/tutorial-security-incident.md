---
title: Esercitazione sulla risposta agli avvisi - Centro sicurezza di Azure
description: Questa esercitazione illustra come valutare gli avvisi di sicurezza e come determinare la causa radice e l'ambito di un avviso.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2020
ms.author: memildin
ms.openlocfilehash: 02b0ee4d572290436cc45bab73921ae1298bc72f
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92359000"
---
# <a name="tutorial-triage-investigate-and-respond-to-security-alerts"></a>Esercitazione: Valutare, analizzare e rispondere agli avvisi di sicurezza
Il Centro sicurezza analizza continuamente i carichi di lavoro del cloud ibrido usando funzionalità avanzate di analisi e intelligence sulle minacce per segnalare attività potenzialmente dannose nelle risorse cloud. Nel Centro sicurezza è anche possibile integrare gli avvisi di altri prodotti e servizi di sicurezza. Dopo che è stato generato un avviso, è necessaria un'azione rapida per l'analisi e la correzione del potenziale problema di sicurezza. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Valutare gli avvisi di sicurezza
> * Analizzare un avviso di sicurezza per determinare la causa radice
> * Rispondere a un avviso di sicurezza e mitigare la causa radice

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
Per esaminare le funzionalità descritte in questa esercitazione, Azure Defender deve essere abilitato. È possibile provare Azure Defender gratuitamente. Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/). La guida di avvio rapido [Introduzione a Centro sicurezza](security-center-get-started.md) illustra nel dettaglio come eseguire l'aggiornamento.


## <a name="triage-security-alerts"></a>Valutare gli avvisi di sicurezza
Il Centro sicurezza offre una vista centralizzata di tutti gli avvisi di sicurezza. Gli avvisi di sicurezza sono classificati in base alla gravità dell'attività individuata. 

Valutare gli avvisi nella pagina **Avvisi di sicurezza** :

:::image type="content" source="./media/tutorial-security-incident/alerts-list.png" alt-text="Pagina Avvisi di sicurezza" lightbox="./media/tutorial-security-incident/alerts-list.png":::

Usare questa pagina per esaminare gli avvisi di sicurezza attivi nell'ambiente e decidere quale analizzare per primo.

Quando si valutano gli avvisi di sicurezza, assegnare priorità in base alla gravità e gestire prima quelli con la gravità più alta. Per altre informazioni sulla gravità, vedere [come vengono classificati gli avvisi](security-center-alerts-overview.md#how-are-alerts-classified).

> [!TIP]
> È possibile connettere il Centro sicurezza di Azure alle soluzioni SIEM più diffuse, tra cui Azure Sentinel, e gestire gli avvisi con uno strumento a scelta. Per altre informazioni, vedere [Esportazione degli avvisi in un sistema SIEM](continuous-export.md).


## <a name="investigate-a-security-alert"></a>Analizzare un avviso di sicurezza

Dopo aver stabilito quale avviso analizzare per primo:

1. Selezionare l'avviso in questione.
1. Nella pagina di panoramica degli avvisi selezionare la risorsa da analizzare per prima.
1. Avviare l'analisi nel riquadro sinistro, che mostra informazioni generali sull'avviso di sicurezza.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-left-pane.png" alt-text="Pagina Avvisi di sicurezza":::

    Questo riquadro mostra:
    - Gravità, stato e tempo di attività dell'avviso
    - Descrizione dell'attività esatta rilevata
    - Risorse interessate
    - Finalità dell'attività nel modello kill chain della matrice MITRE ATT&CK

1. Per informazioni più dettagliate che possono risultare utili per analizzare l'attività sospetta, esaminare la scheda **Dettagli avviso** .

1. Una volta esaminate le informazioni in questa pagina, si avranno dati sufficienti per procedere con una risposta. Se invece sono necessari ulteriori dettagli:

    - Contattare il proprietario della risorsa per verificare se l'attività rilevata è un falso positivo.
    - Esaminare i log non elaborati generati dalla risorsa interessata

## <a name="respond-to-a-security-alert"></a>Rispondere a un avviso di sicurezza
Dopo aver analizzato un avviso e averne compreso l'ambito, è possibile rispondere nel Centro sicurezza di Azure:

1.  Aprire la scheda **Intervieni** per visualizzare le risposte raccomandate.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-take-action.png" alt-text="Pagina Avvisi di sicurezza" lightbox="./media/tutorial-security-incident/alert-details-take-action.png":::

1.  Esaminare la sezione **Mitiga la minaccia** per la procedura di analisi manuale da eseguire per mitigare il problema.
1.  Per rafforzare le risorse ed evitare attacchi futuri di questo tipo, seguire le raccomandazioni per la sicurezza riportate nella sezione **Evita attacchi futuri** .
1.  Per attivare un'app per la logica con passaggi di risposta automatizzati, usare la sezione **Attiva la risposta automatica** .
1.  Se l'attività rilevata *non è* dannosa, è possibile eliminare gli avvisi futuri di questo tipo usando la sezione **Elimina avvisi simili** .

1.  Dopo aver completato l'analisi dell'avviso e aver risposto nel modo appropriato, cambiare lo stato in **Ignorato** .

    :::image type="content" source="./media/tutorial-security-incident/set-status-dismissed.png" alt-text="Pagina Avvisi di sicurezza":::

    L'avviso verrà rimosso dall'elenco principale. È possibile usare il filtro nella pagina dell'elenco degli avvisi per visualizzare tutti gli avvisi con lo stato **Ignorato** .

1.  Microsoft incoraggia l'invio di feedback sull'avviso
    1. contrassegnandolo come **utile** o **non utile** .
    1. Selezionare un motivo e aggiungere un commento.

        :::image type="content" source="./media/tutorial-security-incident/alert-feedback.png" alt-text="Pagina Avvisi di sicurezza":::

    > [!TIP]
    > Microsoft esamina il feedback per migliorare gli algoritmi e fornire avvisi di sicurezza più efficaci.

## <a name="end-the-tutorial"></a>Terminare l'esercitazione

Altre guide introduttive ed esercitazioni della raccolta si basano su questa. Se si prevede di continuare con le guide di avvio rapido e le esercitazioni successive, tenere abilitati il provisioning automatico e Azure Defender. 

Se non si prevede di continuare o si vogliono disabilitare queste funzionalità:

1. Tornare al menu principale del Centro sicurezza e selezionare **Prezzi e impostazioni** .
1. Selezionare la sottoscrizione pertinente.
1. Per effettuare il downgrade, selezionare **Azure Defender - Off** .
1. Per disabilitare il provisioning automatico, aprire la pagina **Raccolta dati** e disattivare l'opzione **Provisioning automatico** .
4. Selezionare **Salva** .

>[!NOTE]
> La disabilitazione del provisioning automatico non implica la rimozione dell'agente di Log Analytics dalle macchine virtuali di Azure che contengono già l'agente. La disabilitazione automatica del provisioning limita il monitoraggio delle risorse.
>

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono state descritte le funzionalità del Centro sicurezza da usare per rispondere a un avviso di sicurezza. Per informazioni correlate, vedere:

- [Rispondere agli avvisi di Azure Defender per Key Vault](defender-for-key-vault-usage.md)
- [Guida di riferimento per gli avvisi di sicurezza](alerts-reference.md)
- [Introduzione ad Azure Defender](azure-defender.md)
