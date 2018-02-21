---
title: Guida introduttiva del Centro sicurezza di Azure - Caricare i computer Linux nel Centro sicurezza | Microsoft Docs
description: Questa guida introduttiva illustra come caricare i computer Linux nel Centro sicurezza.
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
ms.openlocfilehash: 365afd2199b1b8b2e70d882f6a729384edbdffbc
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2018
---
# <a name="quickstart-onboard-linux-computers-to-azure-security-center"></a>Guida introduttiva: Caricare i computer Linux nel Centro sicurezza di Azure
Dopo aver caricato le sottoscrizioni di Azure è possibile abilitare il Centro sicurezza per le risorse di Linux in esecuzione all'esterno di Azure, per esempio in locale o in altri cloud, effettuando il provisioning dell'agente Linux.

Questa guida introduttiva mostra come installare l'agente Linux in un computer Linux.

## <a name="prerequisites"></a>prerequisiti
Per iniziare a usare Centro sicurezza, è necessario avere una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).

Prima di iniziare questa guida introduttiva, è necessario essere nel piano tariffario Standard del Centro sicurezza. Vedere [Guida introduttiva per il Centro sicurezza di Azure](security-center-get-started.md) per istruzioni sull'aggiornamento. È possibile provare gratuitamente il livello Standard del Centro sicurezza per i primi 60 giorni.

## <a name="add-new-linux-computer"></a>Aggiungere un nuovo computer Linux

1. Accedere al [portale di Azure](https://azure.microsoft.com/features/azure-portal/).
2. Scegliere **Centro sicurezza** dal menu **Microsoft Azure**. Viene visualizzato **Centro sicurezza - Panoramica**.

 ![Panoramica del Centro sicurezza di Azure][2]

3. Selezionare **Onboarding nella sicurezza avanzata** nel menu principale del Centro sicurezza.
4. Selezionare **Aggiungere computer non Azure?**.
   ![Passare alla sicurezza avanzata][3]

5. In **Aggiungi nuovi computer non Azure** viene visualizzato un elenco delle aree di lavoro di Log Analytics. L'elenco include, se applicabile, l'area di lavoro predefinita creata dal Centro sicurezza quando è stato abilitato il provisioning automatico. Selezionare questa area di lavoro o un'altra area di lavoro che si desidera usare.

    ![Aggiungere computer non Azure][4]

6.  Nella pagina **Direct Agent** (Agente diretto) sotto **DOWNLOAD AND ONBOARD AGENT FOR LINUX** (SCARICARE E CARICARE L'AGENTE PER LINUX) selezionare il pulsante di **copia** per copiare il comando *wget*.

7.  Aprire Blocco note e incollare il comando. Salvare questo file in un percorso accessibile dal computer Linux.

## <a name="install-the-agent"></a>Installare l'agente

1.  Nel computer Linux aprire il file salvato in precedenza. Selezionare tutto il contenuto, quindi copiare, aprire una console del terminale e incollare il comando.
2.  Al termine dell'installazione è possibile convalidare che *omsagent* sia installato eseguendo il comando *pgrep*. Il comando restituirà il PID (ID processo) *omsagent* come riportato di seguito:

  ![Installare l'agente][5]

I log per l'agente del Centro sicurezza per Linux sono reperibili in: */var/opt/microsoft/omsagent/<workspace id>/log/*

  ![Log per agente][6]

Dopo un po' di tempo, fino a circa 30 minuti, il nuovo computer Linux verrà visualizzato nel Centro sicurezza.

Ora è possibile monitorare le macchine virtuali di Azure e i computer non di Azure in un'unica posizione. In **Calcolo** si dispone di una panoramica di tutte le VM e di tutti i computer insieme alle raccomandazioni. Ogni colonna rappresenta un set di raccomandazioni. Il colore rappresenta lo stato corrente della sicurezza della macchina virtuale o del computer per tale raccomandazione. Il Centro sicurezza segnala anche qualsiasi rilevamento per questi computer negli avvisi di sicurezza.

  ![Pannello Calcolo][7] Esistono due tipi di icone rappresentate sul pannello **Calcolo**:

  ![icon1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Computer non Azure

  ![icon2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Macchina virtuale di Azure

## <a name="clean-up-resources"></a>Pulire le risorse
Se non è più necessario è possibile rimuovere l'agente dal computer Linux.

Per rimuovere l'agente:

1. Scaricare lo [script universale](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) dell'agente Linux nel computer.
2. Sul computer eseguire il file .sh del bundle con l'argomento *--purge*, che rimuove completamente l'agente e la relativa configurazione.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

## <a name="next-steps"></a>Passaggi successivi
In questa Guida introduttiva è stato eseguito il provisioning dell'agente in un computer Linux. Per altre informazioni su come usare il Centro sicurezza, continuare l'esercitazione per configurare i criteri di sicurezza e valutare la sicurezza delle risorse.

> [!div class="nextstepaction"]
> [Esercitazione: Definire e valutare i criteri di sicurezza](tutorial-security-policy.md)

<!--Image references-->
[1]: ./media/quick-onboard-linux-computer/portal.png
[2]: ./media/quick-onboard-linux-computer/overview.png
[3]: ./media/quick-onboard-linux-computer/onboard-windows-computer.png
[4]: ./media/quick-onboard-linux-computer/add-computer.png
[5]: ./media/quick-onboard-linux-computer/pgrep-command.png
[6]: ./media/quick-onboard-linux-computer/logs-for-agent.png
[7]: ./media/quick-onboard-linux-computer/compute.png
