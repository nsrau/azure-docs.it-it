---
title: Guida introduttiva per il Centro sicurezza di Azure - Onboarding di computer Windows su Centro sicurezza | Microsoft Docs
description: Questa Guida introduttiva illustra come eseguire il provisioning di Microsoft Monitoring Agent in un computer Windows.
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
ms.date: 01/07/2018
ms.author: terrylan
ms.openlocfilehash: 50cbbca9181d67bc41632a4650c76b9636a72356
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2018
---
# <a name="quickstart-onboard-windows-computers-to-azure-security-center"></a>Guida introduttiva: Onboarding di computer Windows su Centro sicurezza
Dopo l'onboarding delle sottoscrizioni di Azure, è possibile abilitare il Centro sicurezza per le risorse in esecuzione all'esterno di Azure, ad esempio in locale o in altri cloud, effettuando il provisioning di Microsoft Monitoring Agent.

Questa Guida introduttiva illustra come installare Microsoft Monitoring Agent in un computer Windows.

## <a name="prerequisites"></a>prerequisiti
Per iniziare a usare Centro sicurezza, è necessario avere una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).

Prima di iniziare questa guida introduttiva, è necessario essere nel piano tariffario Standard del Centro sicurezza. Vedere [Guida introduttiva per il Centro sicurezza di Azure](security-center-get-started.md) per istruzioni sull'aggiornamento. È possibile provare gratuitamente il livello Standard del Centro sicurezza per i primi 60 giorni.

## <a name="add-new-windows-computer"></a>Aggiunta di un nuovo computer Windows

1. Accedere al [portale di Azure](https://azure.microsoft.com/features/azure-portal/).
2. Scegliere **Centro sicurezza** dal menu **Microsoft Azure**. Viene visualizzato **Centro sicurezza - Panoramica**.

 ![Panoramica del Centro sicurezza di Azure][2]

3. Selezionare **Onboarding nella sicurezza avanzata** nel menu principale del Centro sicurezza.
4. Selezionare **Aggiungere computer non Azure?**.

   ![Passare alla sicurezza avanzata][3]

5. In **Aggiungi nuovi computer non Azure** viene visualizzato un elenco delle aree di lavoro di Log Analytics. L'elenco include, se applicabile, l'area di lavoro predefinita creata dal Centro sicurezza quando è stato abilitato il provisioning automatico. Selezionare questa area di lavoro o un'altra area di lavoro che si desidera usare.

    ![Aggiungere computer non Azure][4]

  Il pannello **Agente diretto** si apre con un collegamento per scaricare un agente Windows e le chiavi per l'ID dell'area di lavoro da usare nella configurazione dell'agente.

6.  Selezionare il collegamento **Scarica agente Windows** applicabile al tipo di processore del computer per scaricare il file di installazione.

7.  A destra di **ID area di lavoro** selezionare l'icona di copia e incollare l'ID nel Blocco note.

8.  A destra di **Chiave primaria** selezionare l'icona di copia e incollare la chiave nel Blocco note.

## <a name="install-the-agent"></a>Installare l'agente
Ora è necessario installare il file scaricato nel computer di destinazione.

1. Copiare il file nel computer di destinazione ed eseguire l'installazione.
2. Nella pagina di **benvenuto** selezionare **Avanti**.
3. Nella pagina **Condizioni di licenza** leggere la licenza e quindi fare clic su **Accetto**.
4. Nella pagina **Cartella di destinazione** modificare o mantenere la cartella di installazione predefinita e quindi scegliere **Avanti**.
5. Nella pagina **Opzioni di installazione dell'agente** scegliere di connettere l'agente ad Azure Log Analytics (OMS) e quindi scegliere **Avanti**.
6. Nella pagina **Azure Log Analytics** incollare l'**ID area di lavoro** e la **chiave dell'area di lavoro (chiave primaria)** copiati nel Blocco note nella procedura precedente.
7. Se il computer deve fare riferimento a un'area di lavoro di Log Analytics nel cloud di Azure per enti pubblici, selezionare **Azure per enti pubblici statunitensi** nell'elenco a discesa **Cloud di Azure**.  Se il computer deve comunicare tramite un server proxy con il servizio Log Analytics, scegliere **Avanzate** e specificare l'URL e il numero di porta del server proxy.
8. Scegliere **Avanti** dopo aver specificato le impostazioni di configurazione necessarie.

  ![Installare l'agente][5]

9. Nella pagina **Pronto per l'installazione** rivedere le scelte effettuate e quindi scegliere **Installa**.
10. Nella pagina **Configurazione completata** scegliere **Fine**

Al termine, verrà visualizzato **Microsoft Monitoring Agent** nel **Pannello di controllo**. È possibile rivedere la configurazione e verificare che l'agente sia connesso.

Per altre informazioni sull'installazione e configurazione dell'agente, vedere [Connettere computer Windows al servizio Log Analytics in Azure](../log-analytics/log-analytics-agent-windows.md#install-the-agent-using-setup).

Ora è possibile monitorare le macchine virtuali di Azure e i computer non di Azure in un'unica posizione. In **Calcolo** si dispone di una panoramica di tutte le VM e di tutti i computer insieme alle raccomandazioni. Ogni colonna rappresenta un set di raccomandazioni. Il colore rappresenta lo stato corrente della sicurezza della macchina virtuale o del computer per tale raccomandazione. Il Centro sicurezza segnala anche qualsiasi rilevamento per questi computer negli avvisi di sicurezza.

  ![Pannello Calcolo][6]

Esistono due tipi di icone rappresentate sul pannello **Calcolo**:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Computer non Azure

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Macchina virtuale di Azure

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessario, è possibile rimuovere l'agente dal computer Windows.

Per rimuovere l'agente:

1. Aprire il **Pannello di controllo**.
2. Aprire **Programmi e funzionalità**.
3. In **Programmi e funzionalità** selezionare **Microsoft Monitoring Agent** e fare clic su **Disinstalla**.

## <a name="next-steps"></a>Passaggi successivi
In questa Guida introduttiva è stato eseguito il provisioning di Microsoft Monitoring Agent in un computer Windows. Per altre informazioni su come usare il Centro sicurezza, continuare l'esercitazione per configurare i criteri di sicurezza e valutare la sicurezza delle risorse.

> [!div class="nextstepaction"]
> [Esercitazione: Definire e valutare i criteri di sicurezza](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/onboard-windows-computer.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
