---
title: Connessione di Microsoft Advanced Threat Analytics al Centro sicurezza di Azure| Microsoft Docs
description: Informazioni sull'integrazione del Centro sicurezza di Azure con Microsoft Advanced Threat Analytics.
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: ''
ms.assetid: 5d80bf91-16c3-40b3-82fc-e0805e6708db
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: yurid
ms.openlocfilehash: 02e14315018f6586644eb560894785e6b6eb022e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="connecting-microsoft-advanced-threat-analytics-to-azure-security-center"></a>Connessione di Microsoft Advanced Threat Analytics al Centro sicurezza di Azure
Questo documento consente di configurare l'integrazione tra Microsoft Advanced Threat Analytics e il Centro sicurezza di Azure.

## <a name="why-add-advanced-threat-analytics-data"></a>Perché aggiungere i dati di Advanced Threat Analytics?
[Advanced Threat Analytics (ATA)](https://docs.microsoft.com/advanced-threat-analytics/what-is-ata) è una piattaforma locale che aiuta a rilevare i comportamenti sospetti degli utenti. Una volta stabilita la connessione, è possibile visualizzare le azioni sospette rilevate da ATA nel Centro sicurezza. Questa integrazione consente di visualizzare, correlare e analizzare tutti gli avvisi di sicurezza relativi ai carichi di lavoro cloud ibridi nel Centro sicurezza. 

## <a name="how-do-i-configure-this-integration"></a>Come si configura l'integrazione?
Presupponendo che ATA sia già installato e che funzioni correttamente in locale, seguire questa procedura per configurare l'integrazione:

1. Accedere al Centro ATA e al portale di ATA.
2. Fare clic su **Server SysLog** nel riquadro a sinistra.

    ![Server Syslog](./media/security-center-ata-integration/security-center-ata-integration-fig1.png)

3. Nel campo **Syslog server endpoint** (Endpoint server Syslog) digitare 127.0.0.7 (deve essere esattamente questo indirizzo) e 5114 per la porta (scelta consigliata). Il numero di porta è una scelta consigliata, ma è possibile specificare qualsiasi numero di porta univoco. Lasciare invariate tutte le altre opzioni e fare clic su **Salva**.
4. Fare clic su **Notifiche** nel riquadro sinistro e abilitare tutte le notifiche Syslog (scelta consigliata), come illustrato nella figura seguente:

    ![Notifiche](./media/security-center-ata-integration/security-center-ata-integration-fig2.png)

5. Fare clic su **Save**.
6. Aprire il dashboard del **Centro sicurezza**.
7. Nel riquadro sinistro fare clic su **Soluzioni di sicurezza**.
8. In **Advanced Threat Analytics** fare clic su **AGGIUNGI**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig3.png)
    
9. Andare all'ultimo passaggio e fare clic su **Scarica l'agente**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig4.png)

10. Nella pagina **Aggiungi nuovi computer non Azure** selezionare l'area di lavoro.

    ![Non Azure](./media/security-center-ata-integration/security-center-ata-integration-fig5.png)

11. Nella pagina **Agente diretto** scaricare l'agente di Windows appropriato e annotare i valori di **ID area di lavoro** e **Chiave primaria**.

    ![Agente diretto](./media/security-center-ata-integration/security-center-ata-integration-fig6.png)

12. Installare l'agente nel Centro ATA. Durante l'installazione, assicurarsi di selezionare l'opzione **Connect the agent to Azure Log Analytics (OMS)** (Connetti l'agente ad Azure Log Analytics - OMS) e, quando richiesto, fornire *ID area di lavoro* e *chiave primaria*.


Una volta terminata l'installazione, l'integrazione è completata e sarà possibile visualizzare i nuovi avvisi inviati da ATA al Centro sicurezza nei risultati della **ricerca**. La soluzione è visualizzata nella pagina **Soluzioni di sicurezza** in **Soluzioni connesse**. 

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Microsoft ATA al Centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

* [Connessione di Azure Active Directory Identity Protection al Centro sicurezza di Azure](security-center-aadip-integration.md)
* [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
* [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md) : informazioni sul modo in cui le raccomandazioni facilitano la protezione delle risorse di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare l'integrità delle soluzioni dei partner.
- [Sicurezza dei dati nel Centro sicurezza di Azure](security-center-data-security.md): informazioni sulla gestione e la protezione dei dati nel Centro sicurezza.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md) : domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/) : informazioni e notizie aggiornate sulla sicurezza di Azure.


