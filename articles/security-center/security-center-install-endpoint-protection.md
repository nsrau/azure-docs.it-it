---
title: Gestire i problemi di protezione degli endpoint con il Centro sicurezza di Azure | Microsoft Docs
description: Informazioni su come gestire i problemi di protezione endpoint nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/23/2017
ms.author: terrylan
ms.openlocfilehash: abbcb0a8e0206d78ca94520dfa81ab92506c47af
ms.sourcegitcommit: 4d90200f49cc60d63015bada2f3fc4445b34d4cb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2017
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Gestire i problemi di protezione degli endpoint con il Centro sicurezza di Azure
Il Centro sicurezza di Azure monitora lo stato della protezione antimalware e lo segnala nel pannello dei problemi di protezione endpoint. Il Centro sicurezza evidenzia i problemi, quali minacce rilevate e protezione insufficiente, che può rendere i computer e le macchine virtuali vulnerabili ai rischi antimalware. Usando le informazioni in **Problemi di protezione degli endpoint**, è possibile sviluppare un piano per risolvere eventuali problemi identificati.

Il Centro sicurezza segnala i problemi di protezione endpoint seguenti:

- Protezione endpoint non installata nelle macchine virtuali di Azure: non è stata installata una soluzione antimalware supportata sulle macchine virtuali di Azure.
- Protezione endpoint non installata sui computer che non hanno Azure: una soluzione antimalware supportata non è stata installata sui computer diverso da Azure.
- Integrità della protezione endpoint:

   - Firma non aggiornata: sulle macchine virtuali e sui computer è installata una soluzione antimalware che non dispone delle firme antimalware più recenti.
   - Protezione non in tempo reale: sulle macchine virtuali e sui computer è installata una soluzione antimalware che non è configurata per la protezione in tempo reale.   È possibile che il servizio sia disattivato o che il Centro sicurezza non riesca a ottenere lo stato perché la soluzione non è supportata. Vedere l'[integrazione dei partner](security-center-partner-integration.md) per un elenco delle soluzioni supportate.
   - Segnalazioni non inviate: è installata una soluzione antimalware che non invia le segnalazioni dei dati.
   - Sconosciuto: è installata una soluzione antimalware il cui stato è stato è sconosciuto o segnala un errore sconosciuto.

   > [!NOTE]
   > Vedere [Integrare soluzioni di sicurezza](security-center-partner-integration.md#integrated-azure-security-solutions) per un elenco delle soluzioni di sicurezza di Endpoint Protection integrate nel Centro sicurezza.
   >
   >

## <a name="implement-the-recommendation"></a>Implementare la raccomandazione
I problemi di protezione dell'endpoint vengono presentati sotto forma di suggerimento nel Centro sicurezza.  Se l'ambiente è esposto a minacce antimalware, la raccomandazione verrà visualizzata in **Raccomandazioni** e **Calcolo**. Per vedere il **dashboard dei problemi di protezione endpoint**, è necessario seguire il flusso di lavoro di Calcolo.

In questo esempio si userà **Calcolo**.  Verrà illustrato come installare l'antimalware sulle macchine virtuali di Azure e sui computer che non hanno Azure.

## <a name="install-antimalware-on-azure-vms"></a>Installare l'antimalware sulle macchine virtuali di Azure

1. Selezionare **Calcolo** nel menu principale del Centro sicurezza oppure **Panoramica**.

   ![Selezionare Calcolo][1]

2. In **Calcolo** selezionare **Endpoint protection issues** (Problemi di protezione endpoint). Si apre il dashboard **Endpoint protection issues** (Problemi di protezione endpoint).

   ![Selezionare Endpoint protection issues (Problemi di protezione endpoint)][2]

   Nella parte superiore del dashboard sono presenti:

   - Installed endpoint protection providers (Provider di protezione endpoint installata): elenca i diversi provider identificati dal Centro sicurezza.
   - Installed endpoint protection health state (Stato di integrità della protezione endpoint installata), mostra lo stato di integrità delle macchine virtuali e dei computer su cui è installata una soluzione di protezione endpoint. Il grafico mostra il numero di macchine virtuali e computer integri e quelli con protezione insufficiente.
   - Malware rilevato: mostra il numero di macchine virtuali e computer in cui il Centro sicurezza segnala il malware rilevato.
   - Attacked computers (Computer attaccati): mostra il numero di macchine virtuali e computer in cui il Centro sicurezza segnala gli attacchi dei malware.

   Nella parte inferiore del dashboard è presente un elenco di problemi di protezione endpoint che include le informazioni seguenti:  

   - **TOTALE**: il numero di macchine virtuali e computer interessati dal problema.
   - Una barra che raccoglie il numero di macchine virtuali e computer interessati dal problema. I colori nella barra ne identificano la priorità:

      - Rosso: priorità elevata e da risolvere immediatamente
      - Arancione: priorità media e da risolvere appena possibile

3. Selezionare **Endpoint protection not installed on Azure VMs** (Protezione endpoint non installata sulle macchine virtuali di Azure).

   ![Selezionare Endpoint protection not installed on Azure VMs (Protezione endpoint non installata sulle macchine virtuali di Azure)][3]

4. **Endpoint protection not installed on Azure VMs** (Protezione endpoint non installata sulle macchine virtuali di Azure) contiene un elenco di macchine virtuali di Azure su cui non è installato un antimalware.  È possibile scegliere di installare l'antimalware su tutte le macchine virtuali dell'elenco oppure selezionare singole macchine virtuali su cui installare l'antimalware facendo clic sulla macchina virtuale specifica.
5. In **Seleziona Endpoint Protection** selezionare la soluzione di Endpoint Protection da usare. In questo esempio selezionare **Microsoft Antimalware**.
6. Vengono visualizzate altre informazioni sulla soluzione di Endpoint Protection selezionata. Selezionare **Crea**.

## <a name="install-antimalware-on-non-azure-computers"></a>Installare l'antimalware sui computer che non hanno Azure

1. Tornare a **Endpoint protection issues** (Problemi di protezione endpoint) e selezionare **Endpoint protection not installed on non-Azure computers** (Protezione endpoint non installata sui computer che non hanno Azure).

   ![Selezionare Endpoint protection not installed on non-Azure computers (Protezione endpoint non installata sui computer che non hanno Azure)][4]

2. In **Endpoint protection not installed on non-Azure computers** (Protezione endpoint non installata sui computer che non hanno Azure) selezionare un'area di lavoro. Si apre una query di ricerca di Log Analytics filtrata in base all'area di lavoro che elenca i computer privi di antimalware. Selezionare un computer dall'elenco per avere maggiori informazioni.

   ![Ricerca di Log Analytics][5]

Verrà visualizzata un'altra finestra di risultati della ricerca contenente le informazioni filtrate solo per quel computer.

  ![Ricerca di Log Analytics][6]

> [!NOTE]
> Si consiglia si eseguire il provisioning di protezione endpoint per tutte le macchine virtuali e i computer, per identificare e rimuovere virus, spyware e altro software dannoso.
>
>

## <a name="next-steps"></a>Passaggi successivi
Questo documento illustra come implementare la raccomandazione "Installa Endpoint Protection" del Centro sicurezza. Per altre informazioni sull'abilitazione di un programma antimalware di Microsoft in Azure, vedere i documenti seguenti:

* [Microsoft Antimalware per Servizi cloud e Macchine virtuali di Azure](../security/azure-security-antimalware.md): informazioni su come distribuire la protezione antimalware Microsoft.

Per altre informazioni sul Centro sicurezza, vedere i documenti seguenti:

* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : post di blog sulla sicurezza e sulla conformità di Azure.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
