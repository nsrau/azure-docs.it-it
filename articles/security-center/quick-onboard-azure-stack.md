---
title: Avvio rapido di Centro sicurezza di Azure - Onboarding delle macchine virtuali di Azure Stack nel Centro sicurezza | Microsoft Docs
description: Questa guida di avvio rapido mostra come effettuare il provisioning dell'estensione macchina virtuale Monitoraggio di Azure, Gestione aggiornamenti e configurazione nelle macchine virtuali di Azure Stack.
services: security-center
documentationcenter: na
author: pipposera
manager: dsavage
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: fiseraci
ms.openlocfilehash: 1772fd34a2d79b725b2b5ccaa66adb0b251b7e1d
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202857"
---
# <a name="quickstart--onboard-your-azure-stack-virtual-machines-to-security-center"></a>Guida introduttiva:  Onboarding delle macchine virtuali di Azure Stack nel Centro sicurezza
Dopo l'onboarding della sottoscrizione di Azure, è possibile abilitare il Centro sicurezza per proteggere le macchine virtuali in esecuzione in Azure Stack aggiungendo l'estensione macchina virtuale **Monitoraggio di Azure, Gestione aggiornamenti e configurazione** dal Marketplace di Azure Stack.

Questa guida di avvio rapido mostra come aggiungere l'estensione macchina virtuale **Monitoraggio di Azure, Gestione aggiornamenti e configurazione** in una macchina virtuale (sono supportati sia Linux che Windows) in esecuzione su Azure Stack.

## <a name="prerequisites"></a>Prerequisiti
Per iniziare a usare Centro sicurezza, è necessario avere una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).

Prima di iniziare questo argomento di avvio rapido, è necessario avere una sottoscrizione al piano Standard del Centro sicurezza. Vedere [Guida introduttiva per il Centro sicurezza di Azure](security-center-get-started.md) per istruzioni sull'aggiornamento. È possibile provare gratuitamente il livello Standard del Centro sicurezza per 30 giorni. Per altre informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="select-your-workspace-in-azure-security-center"></a>Selezionare l'area di lavoro nel Centro sicurezza di Azure

1. Accedere al [portale di Azure](https://azure.microsoft.com/features/azure-portal/).
2. Scegliere **Centro sicurezza** dal menu **Microsoft Azure**. Viene visualizzato **Centro sicurezza - Panoramica**. 

   ![Panoramica del Centro sicurezza di Azure][2]

3. Scegliere **Introduzione** dal menu principale del Centro sicurezza.
4. Selezionare la scheda **Introduzione**.

   ![Attività iniziali][3]

5. Fare clic su **Configura** in **Aggiungi nuovi computer non Azure**. Viene visualizzato un elenco delle aree di lavoro di Log Analytics. L'elenco include, se applicabile, l'area di lavoro predefinita creata dal Centro sicurezza quando è stato abilitato il provisioning automatico. Selezionare questa o un'altra area di lavoro in cui inviare i dati di sicurezza della VM di Azure Stack.

    ![Aggiungere computer non Azure](./media/quick-onboard-windows-computer/non-azure.png)

   Il pannello **Agente diretto** si apre con un collegamento per scaricare l'agente e le chiavi per l'ID dell'area di lavoro da usare nella configurazione dell'agente.

   >[!NOTE]
   > NON è necessario scaricare l'agente manualmente. L'agente verrà installato come un'estensione VM nei passaggi seguenti.

6. A destra di **ID area di lavoro** selezionare l'icona di copia e incollare l'ID nel Blocco note.

7. A destra di **Chiave primaria** selezionare l'icona di copia e incollare la chiave nel Blocco note.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>Aggiungere l'estensione macchina virtuale nelle macchine virtuali di Azure Stack esistenti
È ora necessario aggiungere l'estensione macchina virtuale **Monitoraggio di Azure, Gestione aggiornamenti e configurazione** nelle macchine virtuali in esecuzione su Azure Stack.

1. In una nuova scheda del browser accedere al portale di **Azure Stack**.
2. Passare alla pagina **Macchine virtuali** e selezionare la macchina virtuale da proteggere con il Centro sicurezza. Per informazioni su come creare una macchina virtuale in Azure Stack, vedere [questo argomento di avvio rapido per le macchine virtuali Windows](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) oppure [per le macchine virtuali Linux](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Selezionare **Estensioni**. Viene visualizzato l'elenco delle estensioni macchina virtuale installate in questa macchina virtuale.
4. Fare clic sulla scheda **Add** (Aggiungi). Viene visualizzato il pannello del menu **Nuova risorsa** che contiene un elenco delle estensioni macchina virtuale disponibili. 
5. Selezionare l'estensione **Monitoraggio di Azure, Gestione aggiornamenti e configurazione** e fare clic su **Crea**. Verrà visualizzato il pannello di configurazione **Installa estensione**.

>[!NOTE]
> Se l'estensione **Monitoraggio di Azure, Gestione aggiornamenti e configurazione** non è elencata nel marketplace, contattare un operatore di Azure Stack per ottenerla.

6. Nel pannello di configurazione **Installa estensione** incollare l'**ID area di lavoro** e la **chiave dell'area di lavoro (chiave primaria)** copiati nel Blocco note nella procedura precedente.
7.  Dopo aver specificato le impostazioni di configurazione necessarie, fare clic su **OK**.
8. Una volta completata l'installazione dell'estensione, il relativo stato risulterà **Provisioning completato**. La visualizzazione della macchina virtuale nel portale del Centro sicurezza potrebbe richiedere fino a un'ora.

Per altre informazioni sull'installazione e configurazione dell'agente per Windows, vedere [Connettere computer Windows](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Per risolvere i problemi relativi agli agenti Linux, vedere [Risoluzione dei problemi relativi all'agente Linux di Azure Log Analytics](../azure-monitor/platform/agent-linux-troubleshoot.md).

Ora è possibile monitorare le macchine virtuali di Azure e i computer non di Azure in un'unica posizione. In **Calcolo** nel portale Centro sicurezza di Azure si avrà una panoramica di tutte le VM e di tutti i computer insieme alle raccomandazioni. Il Centro sicurezza segnala anche qualsiasi rilevamento per questi computer negli avvisi di sicurezza.

  ![Pannello Calcolo][6]

Esistono due tipi di icone rappresentate sul pannello **Calcolo**:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Computer non Azure 

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Macchina virtuale di Azure (le macchine virtuali di Azure Stack saranno visibili in questo gruppo)

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non serve più, è possibile rimuovere l'estensione dalla macchina virtuale tramite il portale di Azure Stack.

Per rimuovere l'estensione:

1. Aprire il **portale di Azure Stack**.
2. Passare alla pagina **Macchine virtuali** e selezionare la macchina virtuale da cui si vuole rimuovere l'estensione.
3. Selezionare **Estensioni** e quindi l'estensione **Microsoft.EnterpriseCloud.Monitoring**.
4. Fare clic su **Disinstalla** e confermare la selezione facendo clic su **Sì**.

## <a name="next-steps"></a>Passaggi successivi
In questa guida di avvio rapido è stato effettuato il provisioning dell'estensione Monitoraggio di Azure, Gestione aggiornamenti e configurazione in una macchina virtuale in esecuzione su Azure Stack. Per altre informazioni su come usare il Centro sicurezza, continuare l'esercitazione per configurare i criteri di sicurezza e valutare la sicurezza delle risorse.

> [!div class="nextstepaction"]
> [Esercitazione: Definire e valutare i criteri di sicurezza](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
