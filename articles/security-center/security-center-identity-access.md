---
title: Monitorare identità e accesso nel Centro sicurezza di Azure | Microsoft Docs
description: Informazioni su come usare le funzionalità per identità e accesso nel Centro sicurezza di Azure per monitorare l'attività di accesso degli utenti e i problemi correlati all'identità.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 6b262baddd10c9d0dff4b196b733972b97d99872
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552985"
---
# <a name="monitor-identity-and-access-preview"></a>Monitorare identità e accesso (anteprima)
Quando il Centro sicurezza identifica potenziali vulnerabilità della sicurezza, crea raccomandazioni utili per definire il processo di configurazione dei controlli necessari per rafforzare e tutelare le risorse.

Questo articolo illustra la pagina di **identità e accesso** della sezione relativa alla sicurezza delle risorse del Centro sicurezza di Azure.

Per un elenco completo delle raccomandazioni che è possibile visualizzare in questa pagina, vedere le indicazioni relative a [identità e accesso](recommendations-reference.md#recs-identity).

> [!NOTE]
> La funzionalità di monitoraggio dell'identità e dell'accesso è disponibile in anteprima e solo per il livello Standard del Centro sicurezza. Per altre informazioni sui piani tariffari di Centro sicurezza, vedere [Prezzi](security-center-pricing.md).
>

L'identità deve essere il piano di controllo per l'organizzazione e la protezione delle identità deve essere la priorità più alta. Il perimetro di sicurezza si è evoluto da perimetro di rete a perimetro di identità. La sicurezza consiste sempre meno nel difendere la rete e sempre più nel difendere i dati e nel gestire la sicurezza di app e utenti. Attualmente, con il trasferimento nel cloud di una maggiore quantità di dati e app, l'identità diventa il nuovo perimetro.

Il monitoraggio delle attività associate alle identità consente di intraprendere azioni proattive prima che si verifichi un evento imprevisto o azioni reattive per bloccare un tentativo di attacco. Il dashboard Identità e accesso visualizza consigli, ad esempio:

- Abilitare MFA per gli account con privilegi nella sottoscrizione
- Rimuovere gli account esterni con autorizzazioni di scrittura dalla sottoscrizione
- Rimuovere account esterni con privilegi dalla sottoscrizione

> [!NOTE]
> Se la sottoscrizione include più di 600 account, il Centro sicurezza non è in grado di applicare i consigli relativi all'identità per la sottoscrizione. Le raccomandazioni che non vengono eseguite sono elencate di seguito in "valutazioni non disponibili".
Il Centro sicurezza non è in grado di applicare i consigli relativi all'identità per gli agenti di amministrazione di un partner Cloud Solution Provider (CSP).
>

## <a name="monitor-identity-and-access"></a>Monitorare identità e accesso

Aprire l'elenco dei problemi di accesso e identità identificati selezionando **identity & Access** dalla barra laterale del Centro sicurezza (in **risorse**) o dalla pagina panoramica. 

In **Identità e accesso** sono presenti due schede:

- **Panoramica**: consigli identificati dal Centro sicurezza.
- **Sottoscrizioni**: elenco delle sottoscrizioni e stato di sicurezza corrente di ognuna.

[Accesso ![identità &](./media/security-center-identity-access/identity-dashboard.png)](./media/security-center-identity-access/identity-dashboard.png#lightbox)

### <a name="overview-section"></a>Sezione Panoramica
In **Panoramica** è presente un elenco di consigli. La prima colonna elenca la raccomandazione. La seconda colonna visualizza il numero totale di sottoscrizioni interessate dal consiglio. La terza colonna indica la gravità del problema.

1. Selezionare un consiglio. Verrà visualizzata la finestra raccomandazioni con i seguenti elementi:

   - Descrizione del consiglio
   - Elenco delle sottoscrizioni non integre e integre
   - Elenco di risorse non analizzate a causa di una valutazione non riuscita o del fatto che la risorsa è all'interno di una sottoscrizione in esecuzione a livello gratuito e non viene quindi valutata

    [finestra consigli ![](./media/security-center-identity-access/select-subscription.png)](./media/security-center-identity-access/select-subscription.png#lightbox)

1. Per altri dettagli, selezionare una sottoscrizione nell'elenco.

### <a name="subscriptions-section"></a>Sezione Sottoscrizioni
In **Sottoscrizioni** è presente un elenco di sottoscrizioni. La prima colonna elenca le sottoscrizioni. La seconda colonna visualizza il numero totale di consigli per ogni sottoscrizione. La terza colonna indica il livello di gravità dei problemi.

[scheda Sottoscrizioni ![](./media/security-center-identity-access/subscriptions.png)](./media/security-center-identity-access/subscriptions.png#lightbox)

1. Seleziona una sottoscrizione. Viene aperta una visualizzazione di riepilogo con tre schede:

   - **Consigli**: in base alle valutazioni non riuscite eseguite dal Centro sicurezza.
   - **Valutazioni superate**: elenco di valutazioni superate eseguite dal Centro sicurezza.
   - **Valutazioni non disponibili**: elenco di valutazioni la cui esecuzione non è riuscita a causa di un errore o perché la sottoscrizione ha più di 600 account.

   In **Consigli** è presente un elenco di consigli per la sottoscrizione selezionata, con l'indicazione del livello di gravità per ogni consiglio.

   [![consigli per la sottoscrizione Select](./media/security-center-identity-access/recommendations.png)](./media/security-center-identity-access/recommendations.png#lightbox)

1. Selezionare un consiglio per una descrizione di questo, l'elenco delle sottoscrizioni non integre e integre e l'elenco delle risorse non analizzate.

   [Descrizione ![della raccomandazione](./media/security-center-identity-access/designate.png)](./media/security-center-identity-access/designate.png#lightbox)

   In **Valutazioni superate** è presente un elenco di valutazioni superate.  Il livello di gravità di tali valutazioni è sempre di colore verde.

   [valutazioni ![superate](./media/security-center-identity-access/passed-assessments.png)](./media/security-center-identity-access/passed-assessments.png#lightbox)

1. Selezionare una valutazione superata dall'elenco per visualizzare la descrizione di questa e un elenco di sottoscrizioni integre. Per le sottoscrizioni non integre è presente una scheda che elenca tutte le sottoscrizioni non riuscite.

   [valutazioni ![superate](./media/security-center-identity-access/remove.png)](./media/security-center-identity-access/remove.png#lightbox)

> [!NOTE]
> Se sono stati creati criteri di accesso condizionale che richiedono l'autenticazione a più fattori ma sono state impostate esclusioni, la valutazione dell'autenticazione a più fattori del Centro sicurezza considera i criteri non conformi, perché consente ad alcuni utenti di accedere ad Azure senza autenticazione a più fattori.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sui suggerimenti applicabili ad altri tipi di risorse di Azure, vedere gli articoli seguenti:

- [Protezione delle macchine virtuali nel Centro sicurezza di Azure](security-center-virtual-machine-protection.md)
- [Protezione della rete nel Centro sicurezza di Azure](security-center-network-recommendations.md)
- [Protezione del servizio SQL di Azure e dei dati nel Centro sicurezza di Azure](security-center-sql-service-recommendations.md)