---
title: Regole di avviso personalizzate nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento consente di creare regole di avviso personalizzate nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: f335d8c4-0234-4304-b386-6f1ecda07833
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: yurid
ms.openlocfilehash: 10c5c3e6f714aac4c08cb810f54eb09c3d2bbe70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="custom-alert-rules-in-azure-security-center-preview"></a>Regole di avviso personalizzate nel Centro sicurezza di Azure (anteprima)
Questo documento consente di creare regole di avviso personalizzate nel Centro sicurezza di Azure.

## <a name="what-are-custom-alert-rules-in-security-center"></a>Informazioni sulle regole di avviso personalizzate nel Centro sicurezza

Il Centro sicurezza include un set di [avvisi di sicurezza](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) predefiniti, che vengono attivati in caso di minaccia o di attività sospetta. In alcuni scenari è consigliabile creare un avviso personalizzato per rispondere a esigenze specifiche dell'ambiente. 

Le regole di avviso personalizzate nel Centro sicurezza consentono di definire nuovi avvisi di sicurezza in base ai dati già raccolti dall'ambiente. È possibile creare query e usare i risultati di tali query come criteri per la regola personalizzata, che viene eseguita in caso di corrispondenza con i criteri. Per creare le query personalizzate, è possibile usare gli eventi di sicurezza dei computer, i log delle soluzioni di sicurezza dei partner o i dati inseriti tramite API. 

## <a name="how-to-create-a-custom-alert-rule-in-security-center"></a>Procedura per la creazione di una regola di avviso nel Centro sicurezza

Aprire il dashboard **Centro sicurezza** e seguire questa procedura per creare una regola di avviso personalizzata:

1.  In **Rilevamento** nel riquadro di sinistra fare clic su **Regole di avviso personalizzate (anteprima)**.
2.  Nella pagina **Centro sicurezza – Regole di avviso personalizzate (anteprima)** fare clic su **Nuova regola di avviso personalizzata**.

    ![Regola personalizzata](./media/security-center-custom-alert/security-center-custom-alert-fig1.png)
    
3.  Viene visualizzata la pagina Crea una regola di avviso personalizzata con le regole seguenti:
    
    ![Create](./media/security-center-custom-alert/security-center-custom-alert-fig2.png)

4.  Digitare il nome per la regola personalizzata nel campo **Nome**.
5.  Immettere una breve descrizione che rispecchia la finalità della regola nel campo **Descrizione**.
6.  Selezionare il livello di gravità (Alta, Media, Bassa) in base alle esigenze specifiche nel campo **Gravità**.
7.  Selezionare la sottoscrizione in cui è applicabile questa regola nel campo **Sottoscrizione**.
8.  Selezionare l'area di lavoro da monitorare con questa regola nel campo **Area di lavoro** e quindi nel campo **Query di ricerca** selezionare la query da usare per ottenere i risultati. Il risultato della query attiva l'avviso. Si noti che quando si immette una query valida viene visualizzato un segno di spunta verde nell'angolo destro del campo:

    ![Query](./media/security-center-custom-alert/security-center-custom-alert-fig3.png)

10. Nel campo **Periodo** selezionare l'intervallo di tempo durante il quale verrà eseguita la query precedente. Si noti che il risultato della ricerca nella parte inferiore del campo dipenderà dall'intervallo di tempo selezionato.

    ![Periodo](./media/security-center-custom-alert/security-center-custom-alert-fig4.png)

11. Nel campo **Valutazione** selezionare la frequenza per la valutazione e l'esecuzione di questa regola.
12. Nel campo **Numero di risultati** selezionare l'operatore (maggiore di o minore di).
13. Nel campo **Soglia** digitare un numero che verrà usato come riferimento per l'operatore selezionato in precedenza.
14. Abilitare l'opzione **Elimina avvisi** se si vuole configurare un tempo di attesa prima dell'invio di un altro avviso per questa regola da parte del Centro sicurezza.
15. Fare clic su **OK** per terminare.

Dopo la creazione, la nuova regola di avviso verrà visualizzata nell'elenco di regole di avviso personalizzate. Quando vengono soddisfatte le condizioni della regola, verrà attivato un nuovo avviso, che verrà visualizzato nel dashboard **Avvisi di sicurezza**.

![Avviso](./media/security-center-custom-alert/security-center-custom-alert-fig5.png)

Si noti che i parametri (query di ricerca, soglia e così via) stabiliti durante la creazione della regola sono disponibili nell'avviso per questa regola personalizzata.

## <a name="see-also"></a>Vedere anche
In questo documento è stato descritto come creare una regola di avviso personalizzata nel Centro sicurezza di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informazioni su come gestire gli avvisi e rispondere agli eventi imprevisti di sicurezza nel Centro sicurezza.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Informazioni sugli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Informazioni sui diversi tipi di avvisi di sicurezza.
* [Guida alla risoluzione dei problemi del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Informazioni su come risolvere i problemi comuni nel Centro sicurezza. 
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.

