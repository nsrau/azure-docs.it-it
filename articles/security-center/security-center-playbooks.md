---
title: Playbook di sicurezza nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra come usare i playbook di sicurezza nel Centro sicurezza di Azure per automatizzare la risposta agli eventi imprevisti relativi alla sicurezza.
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: ''
ms.assetid: a8c45ddf-5c4c-4393-b6e9-46ed1f91bf5f
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2018
ms.author: yurid
ms.openlocfilehash: 76f2666df76b802c175eadc38d2ac4100799ceae
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="security-playbook-in-azure-security-center-preview"></a>Playbook di sicurezza nel Centro sicurezza di Azure (anteprima)
Questo documento illustra come usare i playbook di sicurezza nel Centro sicurezza di Azure per rispondere ai problemi correlati alla sicurezza.

## <a name="what-is-security-playbook-in-security-center"></a>Informazioni sul playbook di sicurezza nel Centro sicurezza di Azure
Il playbook di sicurezza è una raccolta di procedure che possono essere eseguite dal Centro sicurezza dopo l'attivazione di un determinato playbook da un avviso selezionato. I playbook di sicurezza possono consentire di automatizzare e orchestrare la risposta a un avviso di sicurezza specifico rilevato dal Centro sicurezza. I playbook di sicurezza nel Centro sicurezza sono basati sulle [App per la logica di Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps), quindi è possibile usare i modelli disponibili nella categoria relativa alla sicurezza nell'elenco di modelli di app per la logica e modificarli in base alle esigenze specifiche oppure creare nuovi playbook usando il [flusso di lavoro di App per la logica di Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app) e usando il Centro sicurezza come trigger. 

> [!NOTE]
> Il playbook sfrutta le funzionalità di App per la logica di Azure, quindi sono previsti addebiti. Per altre informazioni, vedere la pagina dei prezzi per [App per la logica di Azure](https://azure.microsoft.com/pricing/details/logic-apps/). 

## <a name="how-to-create-a-security-playbook-from-security-center"></a>Procedura per la creazione di un playbook di sicurezza dal Centro sicurezza
Per creare un nuovo playbook di sicurezza dal Centro sicurezza, seguire questa procedura:

1.  Aprire il dashboard del **Centro sicurezza**.
2.  Nella sezione **Automazione e orchestrazione** nel riquadro a sinistra fare clic su **Playbook (anteprima)**.

    ![App per la logica](./media/security-center-playbooks/security-center-playbooks-fig17.png)
 
3. Nella pagina **Centro sicurezza - Playbook (anteprima)** fare clic sul pulsante **Aggiungi**.

    ![Creare l'app per la logica](./media/security-center-playbooks/security-center-playbooks-fig2.png)

4. Nella pagina **Crea app per la logica** digitare le informazioni richieste per creare una nuova app per la logica, quindi fare clic sul pulsante **Crea**. Al termine della creazione, il nuovo playbook verrà visualizzato nell'elenco. Se non viene visualizzato, fare clic sul pulsante **Aggiorna**. Quando il playbook è visualizzato, selezionarlo per iniziare a modificarlo.

    ![Creare l'app per la logica](./media/security-center-playbooks/security-center-playbooks-fig3.png)

5. Viene visualizzata la finestra **Progettazione app per la logica**. Fare clic su **App per la logica vuota** per creare un nuovo playbook. È anche possibile selezionare **Sicurezza** nelle categorie e usare uno dei modelli.
    
    ![Progettazione app per la logica](./media/security-center-playbooks/security-center-playbooks-fig4.png)

6. Nel campo **Cerca tutti i connettori e i trigger** digitare *Centro sicurezza di Azure*, quindi selezionare **Quando viene attivata una risposta a un avviso del Centro sicurezza di Azure**.

    ![Trigger](./media/security-center-playbooks/security-center-playbooks-fig12.png)

7. È ora possibile definire cosa accade quando si attiva il playbook. È possibile aggiungere un'azione, una condizione logica, condizioni switch-case o loop.

    ![Progettazione app per la logica](./media/security-center-playbooks/security-center-playbooks-fig5.png)
     
## <a name="how-to-run-a-security-playbook-in-security-center"></a>Procedura per l'esecuzione di un playbook di sicurezza nel Centro sicurezza di Azure

È possibile eseguire un playbook di sicurezza nel Centro sicurezza per orchestrare, ottenere altre informazioni da altri servizi o apportare correzioni. Per accedere ai playbook, seguire questa procedura:

1.  Aprire il dashboard del **Centro sicurezza**.
2.  In **Rilevamento delle minacce** nel riquadro a sinistra fare clic su **Security incidents & alerts** (Eventi imprevisti e avvisi di sicurezza).

    ![Avvisi](./media/security-center-playbooks/security-center-playbooks-fig6.png)

3.  Fare clic sull'avviso da analizzare.
4.  Nella parte superiore della pagina dell'avviso fare clic sul pulsante **Esegui playbook**.

    ![Esegui playbook](./media/security-center-playbooks/security-center-playbooks-fig7.png)

5. Nella pagina Playbook selezionare il playbook da eseguire e quindi fare clic sul pulsante **Esegui**. Per visualizzare il playbook prima dell'attivazione, farvi clic in modo che venga visualizzata la finestra di progettazione.

    ![Playbook](./media/security-center-playbooks/security-center-playbooks-fig13.png)

### <a name="history"></a>Cronologia

Dopo l'esecuzione del playbook, è possibile accedere anche a esecuzioni precedenti e a passaggi che includono altre informazioni sullo stato dei playbook eseguiti in precedenza. La cronologia è contestualizzata in base al singolo avviso, quindi la cronologia dei playbook visualizzata nella pagina è correlata all'avviso che ha attivato il playbook. 

![Cronologia](./media/security-center-playbooks/security-center-playbooks-fig16.png)

Per visualizzare altri dettagli sull'esecuzione di un playbook specifico, fare clic sul playbook. Verrà visualizzata la pagina Esecuzione dell'app per la logica con l'intero flusso di lavoro.

![Dettagli](./media/security-center-playbooks/security-center-playbooks-fig14.png)

In questo flusso di lavoro è possibile visualizzare il tempo necessario per l'esecuzione di ogni attività ed espandere ogni attività per visualizzare il risultato. 

### <a name="changing-an-existing-playbook"></a>Modifica di un playbook esistente

È possibile modificare un playbook esistente nel Centro sicurezza per aggiungere un'azione o alcune condizioni. Per eseguire questa operazione è sufficiente fare clic sul nome del playbook da modificare nella scheda Playbook, in modo da visualizzare Progettazione app per la logica.

> [!NOTE]
> Per altre informazioni su come creare un playbook usando App per la logica di Azure, vedere [Creare il primo flusso di lavoro di app per la logica per automatizzare i processi tra app cloud e servizi cloud](https://docs.microsoft.com/azure/logic-apps/logic-apps-create-a-logic-app#add-an-action-that-responds-to-your-trigger).


## <a name="see-also"></a>Vedere anche 
In questo documento è stato descritto come usare playbook nel Centro sicurezza di Azure. Per ulteriori informazioni sul Centro sicurezza di Azure, vedere gli argomenti seguenti:

* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Informazioni su come gestire gli avvisi e rispondere agli eventi imprevisti di sicurezza nel Centro sicurezza.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md). Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Informazioni sugli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Informazioni sui diversi tipi di avvisi di sicurezza.
* [Guida alla risoluzione dei problemi del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Informazioni su come risolvere i problemi comuni nel Centro sicurezza. 
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](http://blogs.msdn.com/b/azuresecurity/). Post di blog sulla sicurezza e sulla conformità di Azure.

