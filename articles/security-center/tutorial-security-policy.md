---
title: Esercitazione per il Centro sicurezza di Azure - Definire e valutare i criteri di sicurezza | Microsoft Docs
description: Esercitazione per il Centro sicurezza di Azure - Definire e valutare i criteri di sicurezza
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: terrylan
ms.openlocfilehash: 15c69bce87ede96eb3a7bc0bada4e4f6a6669abb
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358609"
---
# <a name="tutorial-define-and-assess-security-policies"></a>Esercitazione: Definire e valutare i criteri di sicurezza
Il Centro sicurezza consente di assicurare la conformità ai requisiti di sicurezza aziendali o normativi, usando criteri di sicurezza per definire la configurazione desiderata per i carichi di lavoro. Dopo aver definito i criteri per le sottoscrizioni di Azure e averli adattati al tipo di carico di lavoro o di sensibilità dei dati, il Centro sicurezza può fornire consigli sulla sicurezza per le risorse di calcolo, dell'applicazione, di rete, di dati e archiviazione e di identità e accesso. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare i criteri di sicurezza
> * Valutare la sicurezza delle risorse

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
Per esaminare le funzionalità descritte in questa esercitazione è necessario il piano tariffario Standard del Centro sicurezza. È possibile provare gratuitamente il livello Standard del Centro sicurezza per i primi 60 giorni. La [Guida introduttiva per il Centro sicurezza di Azure ](security-center-get-started.md) illustra come eseguire l'aggiornamento al livello Standard.

## <a name="configure-security-policy"></a>Configurare i criteri di sicurezza
Il Centro sicurezza crea automaticamente un criterio di sicurezza predefinito per ogni sottoscrizione di Azure. I criteri di sicurezza sono costituiti da raccomandazioni che è possibile attivare o disattivare secondo i requisiti di sicurezza della sottoscrizione. Per apportare modifiche ai criteri di sicurezza predefiniti è necessario essere proprietario, collaboratore o amministratore della sicurezza della sottoscrizione.

1. Scegliere **Criteri di sicurezza** nel menu principale del Centro sicurezza.
2. Selezionare la sottoscrizione da usare.

  ![Criteri di sicurezza](./media/tutorial-security-policy/tutorial-security-policy-fig1.png)  

3. In **COMPONENTI DEI CRITERI** selezionare **Criteri di sicurezza**.
4. Per ogni configurazione di sicurezza che si vuole monitorare, selezionare **Sì**. Il Centro sicurezza valuterà continuamente la configurazione dell'ambiente e genererà una raccomandazione di sicurezza se esiste una vulnerabilità. Selezionare **No** se la configurazione di sicurezza non è consigliata o pertinente. Ad esempio, in un ambiente di sviluppo o test potrebbe non essere necessario lo stesso livello di sicurezza di un ambiente di produzione. Dopo aver selezionato i criteri applicabili all'ambiente, fare clic su **Salva**.

  ![Configurazione di sicurezza](./media/tutorial-security-policy/tutorial-security-policy-fig6.png)  

Attendere il Centro sicurezza elabori questi criteri e generi le raccomandazioni. Alcune configurazioni, ad esempio gli aggiornamenti del sistema e le configurazioni del sistema operativo, possono richiedere fino a 12 ore, mentre i gruppi di sicurezza di rete e le configurazioni di crittografia possono essere valutati quasi istantaneamente. Quando vengono visualizzate le raccomandazioni nel dashboard del Centro sicurezza è possibile procedere al passaggio successivo.

## <a name="assess-security-of-resources"></a>Valutare la sicurezza delle risorse
1. A seconda dei criteri di sicurezza abilitati, il Centro sicurezza fornirà una serie di raccomandazioni sulla sicurezza. È consigliabile iniziare esaminando le raccomandazioni relative a macchine virtuali e computer. Nel dashboard del Centro sicurezza selezionare **Panoramica** e quindi **Compute & apps** (Calcolo e app).

  ![Calcolo](./media/tutorial-security-policy/tutorial-security-policy-fig2.png)

  Esaminare ogni raccomandazione dando la precedenza a quelle indicate in rosso (priorità alta). Alcune di queste raccomandazioni offrono rimedi che possono essere implementati direttamente dal Centro sicurezza, ad esempio i [problemi di protezione endpoint](https://docs.microsoft.com/azure/security-center/security-center-install-endpoint-protection). Altre raccomandazioni hanno solo linee guida per l'applicazione del rimedio, ad esempio la raccomandazione relativa alla crittografia del disco mancante.

2. Dopo la verifica delle raccomandazioni relative al calcolo, è consigliabile passare al carico di lavoro successivo, ovvero Rete. Nel dashboard del Centro sicurezza fare clic su **Panoramica** e quindi su **Rete**.

  ![Rete](./media/tutorial-security-policy/tutorial-security-policy-fig3.png)

  La pagina delle raccomandazioni sulla rete visualizza un elenco di problemi di sicurezza per la configurazione della rete, gli endpoint con connessione Internet e la topologia di rete. Analogamente a **Compute & apps** (Calcolo e app), alcuni consigli sulla rete forniranno correzioni integrate mentre altri no.

3. Dopo la verifica delle raccomandazioni relative alla rete, è consigliabile passare al carico di lavoro successivo, ovvero Archiviazione e dati. Nel dashboard del Centro sicurezza fare clic su **Panoramica** e quindi su **Data & storage** (Dati e archiviazione).

  ![Risorse dati](./media/tutorial-security-policy/tutorial-security-policy-fig4.png)

  La pagina **Risorse dati** contiene consigli relativi all'abilitazione del controllo per database e server SQL di Azure e della crittografia per i database SQL e dell'account di archiviazione di Azure. Se non si hanno questi carichi di lavoro, non verranno visualizzate raccomandazioni. Analogamente a **Compute & apps** (Calcolo e app), alcuni consigli relativi a dati e archiviazione forniranno correzioni integrate, mentre altri no.

4. Dopo la verifica di tutti i consigli rilevanti per dati e archiviazione, è consigliabile passare al carico di lavoro successivo, relativo a identità e accesso. Nel dashboard del Centro sicurezza fare clic su **Panoramica** e quindi su **Identity & access** (Identità e accesso).

  ![Identity & access (Identità e accesso)](./media/tutorial-security-policy/tutorial-security-policy-fig5.png)

  La pagina **Identity & Access** (Identità e accesso) contiene consigli quali:

   - Abilitare MFA per gli account con privilegi nella sottoscrizione
   - Rimuovere gli account esterni con autorizzazioni di scrittura dalla sottoscrizione
   - Rimuovere account esterni con privilegi dalla sottoscrizione

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
In questa esercitazione sono stati descritti concetti base della definizione dei criteri e della valutazione della sicurezza del carico di lavoro con il Centro sicurezza, ad esempio:

> [!div class="checklist"]
> * Configurazione dei criteri di sicurezza per assicurare la conformità ai requisiti di sicurezza aziendali o normativi
> * Valutazione della sicurezza per le risorse di calcolo, rete, SQL e archiviazione e applicazioni

Passare all'esercitazione successiva per informazioni sull'uso del Centro sicurezza per la protezione delle risorse.

> [!div class="nextstepaction"]
> [Proteggere le risorse](tutorial-protect-resources.md)
