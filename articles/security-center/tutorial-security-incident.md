---
title: Esercitazione per il Centro sicurezza di Azure - Rispondere agli eventi imprevisti della sicurezza | Microsoft Docs
description: Esercitazione per il Centro sicurezza di Azure - Rispondere agli eventi imprevisti della sicurezza
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: rkarlin
ms.openlocfilehash: 12ea21a3f61404c2e031909adda28f8e1e768cd0
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992075"
---
# <a name="tutorial-respond-to-security-incidents"></a>Esercitazione: Rispondere agli eventi imprevisti per la sicurezza
Il Centro sicurezza analizza continuamente i carichi di lavoro del cloud ibrido usando funzionalità avanzate di analisi e intelligence delle minacce per segnalare attività dannose. È anche possibile integrare nel Centro sicurezza gli avvisi generati da altri servizi e prodotti per la sicurezza e creare avvisi personalizzati in base a indicatori o origini di intelligence dell'utente. Dopo che è stato generato un avviso, è necessaria un'azione rapida per l'analisi e la correzione. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Valutare gli avvisi di sicurezza
> * Eseguire altri verifiche per determinare la causa radice e l'ambito di un evento imprevisto della sicurezza
> * Eseguire ricerche nei dati relativi alla sicurezza per agevolare l'indagine

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
Per esaminare le funzionalità descritte in questa esercitazione è necessario il piano tariffario Standard del Centro sicurezza. È possibile provare il livello Standard del Centro sicurezza gratuitamente. Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/). La [Guida introduttiva per il Centro sicurezza di Azure ](security-center-get-started.md) illustra come eseguire l'aggiornamento al livello Standard.

## <a name="scenario"></a>Scenario
Contoso ha recentemente eseguito la migrazione di alcune delle risorse locali in Azure, inclusi alcuni carichi di lavoro e database SQL di linea di business basati sulle macchine virtuali. Il team di risposta agli eventi imprevisti della sicurezza di Contoso ha attualmente difficoltà nell'analisi dei problemi di sicurezza, data la mancanza di una intelligence di sicurezza integrata con gli attuali strumenti di risposta agli eventi imprevisti. Questa mancata integrazione costituisce un problema durante la fase di rilevamento (troppi falsi positivi) e durante le fasi di valutazione e diagnosi. Nell'ambito di questa migrazione, il team ha deciso di ricorrere al Centro sicurezza per risolvere questo problema.

La prima fase della migrazione è stata completata dopo l'onboarding di tutte le risorse e l'adozione di tutte le raccomandazioni del Centro sicurezza. Il team di risposta agli eventi imprevisti della sicurezza di Contoso è il punto focale per la gestione degli eventi imprevisti della sicurezza relativi ai computer. Il team è costituito da un gruppo di persone responsabili della gestione di eventuali eventi imprevisti della sicurezza. I membri del team hanno mansioni chiaramente definite per garantire che vengano coperte tutte le aree della risposta.

Ai fini di questo scenario verranno presi in esame i ruoli degli utenti seguenti che appartengono al team di risposta agli eventi imprevisti della sicurezza di Contoso:

![Ciclo di vita della risposta agli eventi imprevisti](./media/tutorial-security-incident/security-center-incident-response.png)

Alice si occupa delle attività di sicurezza. Le sue responsabilità includono:

* Monitoraggio e risposta alle minacce per la sicurezza 24 ore su 24.
* Escalation al proprietario dei carichi di lavoro nel cloud o all'analista della sicurezza in base alle esigenze.

Guido è un analista della sicurezza e le sue responsabilità includono:

* Analisi degli attacchi.
* Risoluzione degli avvisi.
* Collaborazione con i proprietari dei carichi di lavoro per determinare e applicare soluzioni di mitigazione.

Come si può notare, Alice e Guido hanno responsabilità diverse e devono interagire tra loro per condividere le informazioni del Centro sicurezza.

## <a name="triage-security-alerts"></a>Valutare gli avvisi di sicurezza
Il Centro sicurezza offre una vista centralizzata di tutti gli avvisi di sicurezza. Gli avvisi di sicurezza vengono classificati in base alla gravità e gli avvisi correlati vengono riuniti in un unico evento imprevisto, quando possibile. Nella valutazione di avvisi ed eventi imprevisti è necessario:

- Ignorare gli avvisi per i quali non sono necessarie altre azioni, ad esempio in caso di falsi positivi
- Eseguire operazioni per correggere gli attacchi noti, ad esempio bloccando il traffico di rete di un indirizzo IP dannoso
- Determinare gli avvisi che richiedono altre indagini


1. Nel menu principale del Centro sicurezza selezionare **Avvisi di sicurezza** in **RILEVAMENTO**:

   ![Avvisi di sicurezza](./media/tutorial-security-incident/tutorial-security-incident-fig1.png)  

2. Nell'elenco degli avvisi fare clic su un evento imprevisto della sicurezza, ovvero una raccolta di avvisi, per ottenere altre informazioni sull'evento imprevisto. Si aprirà **Security incident detected** (Rilevato evento imprevisto sicurezza).

   ![Evento imprevisto della sicurezza](./media/tutorial-security-incident/tutorial-security-incident-fig2.png)

3. Questa schermata indica la descrizione dell'evento imprevisto della sicurezza in alto e l'elenco degli avvisi che fanno parte di questo evento imprevisto. Fare clic sull'avviso da analizzare per ottenere altre informazioni.

   ![Evento imprevisto della sicurezza](./media/tutorial-security-incident/tutorial-security-incident-fig3.png)

   Il tipo di avviso può variare. Vedere [Informazioni sugli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) per altre informazioni sul tipo di avviso e le possibili procedure correttive. Per gli avvisi che possono essere ignorati, è possibile fare clic con il pulsante destro del mouse sull'avviso e scegliere **Ignora**:

   ![Avviso](./media/tutorial-security-incident/tutorial-security-incident-fig4.png)

4. Se la causa radice e l'ambito dell'attività dannosa sono sconosciuti, procedere al passaggio successivo per approfondire la verifica.

## <a name="investigate-an-alert-or-incident"></a>Analizzare un avviso o un evento imprevisto
1. Nella pagina **Avviso di sicurezza** fare clic sul pulsante **Avvia indagine**. Se l'indagine è già stata avviata, il pulsante visualizzerà **Continua l'indagine**.

   ![Analisi](./media/tutorial-security-incident/tutorial-security-incident-fig5.png)

   La mappa dell'indagine è una rappresentazione grafica delle entità collegate a questo evento imprevisto o avviso di sicurezza. Facendo clic su un'entità nella mappa, le informazioni sull'entità visualizzeranno nuove entità, espandendo la mappa. Le proprietà dell'entità selezionata sono evidenziate nel riquadro sul lato destro della pagina. Le informazioni disponibili in ogni scheda variano a seconda dell'entità selezionata. Durante il processo di indagine, esaminare tutte le informazioni pertinenti per comprendere meglio l'attività dell'utente malintenzionato.

2. Procedere al passaggio successivo se sono necessarie altre prove oppure se si devono analizzare in modo più approfondito le entità trovate durante l'indagine.

## <a name="search-data-for-investigation"></a>Eseguire ricerche nei dati per l'indagine

È possibile usare le funzionalità di ricerca del Centro sicurezza per trovare altre prove di sistemi compromessi e altre informazioni sulle entità che fanno parte dell'indagine.

Per eseguire una ricerca, aprire il dashboard del **Centro sicurezza**, fare clic su **Cerca** nel riquadro di spostamento a sinistra, selezionare l'area di lavoro che contiene le entità per le quali si vuole eseguire la ricerca, digitare la query e fare clic sul pulsante Cerca.

## <a name="clean-up-resources"></a>Pulire le risorse
Altre guide introduttive ed esercitazioni della raccolta si basano su questa. Se si prevede di usare le guide introduttive e le esercitazioni successive, continuare a eseguire il livello Standard e tenere abilitato il provisioning automatico. Se non si intende proseguire oppure si vuole tornare al livello gratuito:

1. Tornare al menu principale del Centro sicurezza e selezionare **Criteri di sicurezza**.
2. Selezionare la sottoscrizione o i criteri per i quali si vuole tornare al livello gratuito. Viene visualizzata la finestra **Criteri di sicurezza**.
3. In **COMPONENTI DEI CRITERI** selezionare **Piano tariffario**.
4. Selezionare **Gratuito** per modificare il livello della sottoscrizione da Standard a Gratuito.
5. Selezionare **Salva**.

Se si vuole disabilitare il provisioning automatico:

1. Tornare al menu principale del Centro sicurezza e selezionare **Criteri di sicurezza**.
2. Selezionare la sottoscrizione per cui si desidera disabilitare il provisioning automatico.
3. In **Criteri di sicurezza - Raccolta dati** selezionare **No** in **Onboarding** per disabilitare il provisioning automatico.
4. Selezionare **Salva**.

>[!NOTE]
> La disabilitazione del provisioning automatico non rimuove Microsoft Monitoring Agent dalle macchine virtuali di Azure in cui è stato eseguito il provisioning dell'agente. La disabilitazione automatica del provisioning limita il monitoraggio delle risorse.
>

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono state descritte le funzionalità del Centro sicurezza da usare per rispondere a un evento imprevisto della sicurezza, ad esempio:

> [!div class="checklist"]
> * Evento imprevisto della sicurezza, ovvero un'aggregazione degli avvisi relativi a una risorsa
> * Mappa dell'indagine, ovvero una rappresentazione grafica delle entità collegate a un evento imprevisto o avviso di sicurezza
> * Funzionalità di ricerca per trovare altre prove di sistemi compromessi

Per altre informazioni sulla funzionalità di indagine del Centro sicurezza, vedere:

> [!div class="nextstepaction"]
> [Analizzare gli eventi imprevisti e gli avvisi](security-center-investigation.md)
