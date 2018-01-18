---
title: Esercitazione per Centro sicurezza di Azure - Protezione delle risorse con Centro sicurezza di Azure | Microsoft Docs
description: Questa esercitazione illustra come configurare i criteri di accesso JIT alle macchine virtuali e i criteri di controllo delle applicazioni.
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
ms.date: 01/08/2018
ms.author: terrylan
ms.openlocfilehash: f0a32f90e68101f805a52427fab2d5bb29b94939
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2018
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Esercitazione: Protezione delle risorse con Centro sicurezza di Azure
Centro sicurezza di Azure limita l'esposizione a minacce usando i controlli di accesso e delle applicazioni per bloccare le attività dannose. L'accesso JIT alle macchine virtuali (VM) riduce l'esposizione agli attacchi grazie alla possibilità di negare l'accesso permanente alle macchine virtuali. In alternativa, è possibile fornire l'accesso controllato alle macchine virtuali solo quando è necessario. I controlli delle applicazioni adattivi consentono di applicare la protezione avanzata delle macchine virtuali contro il malware controllando quali applicazioni possono essere eseguite nelle macchine virtuali. Il Centro sicurezza usa Machine Learning per analizzare i processi in esecuzione nella macchina virtuale e, grazie a questa funzionalità intelligente, aiuta ad applicare regole di inserimento nell'elenco elementi consentiti.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurazione dei criteri di accesso JIT alle macchine virtuali
> * Configurazione dei criteri di controllo delle applicazioni

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
Per esaminare le funzionalità descritte in questa esercitazione è necessario il piano tariffario Standard del Centro sicurezza. È possibile provare gratuitamente il livello Standard del Centro sicurezza per i primi 60 giorni. La [Guida introduttiva per il Centro sicurezza di Azure ](security-center-get-started.md) illustra come eseguire l'aggiornamento al livello Standard.

## <a name="manage-vm-access"></a>Gestire l'accesso alle macchine virtuali
L'accesso JIT alle macchine virtuali può essere usato per bloccare il traffico in ingresso alle macchine virtuali di Azure, riducendo l'esposizione agli attacchi e al tempo stesso offrendo un facile accesso per connettersi alle macchine virtuali quando necessario.

L'accesso JIT alle macchine virtuali è in anteprima.

Non è necessario lasciare aperte le porte di gestione in qualsiasi momento. Devono essere aperte solo durante la connessione alla macchina virtuale, ad esempio per eseguire attività di gestione o manutenzione. Quando la funzionalità JIT è abilitata, Centro sicurezza usa le regole del gruppo di sicurezza di rete (NSG), che limitano l'accesso alle porte di gestione per impedire che possano essere attaccate da utenti malintenzionati.

1. Dal menu principale del Centro sicurezza di Azure selezionare **Accesso Just-In-Time alla VM** in **DIFESA CLOUD AVANZATA**.

  ![Accesso Just-In-Time alla VM][1]

  **Accesso Just-In-Time alla VM** fornisce informazioni sullo stato delle macchine virtuali:

  - **Configurata** - Macchine virtuali che sono state configurate per supportare l'accesso Just-In-Time.
  - **Consigliata** - Macchine virtuali che possono supportare l'accesso Just-In-Time, ma che non sono state configurate a tale scopo.
  - **Nessuna raccomandazione** - I motivi per cui una macchina virtuale può risultare non raccomandata sono:

    - Gruppo di sicurezza di rete mancante - La soluzione Just-In-Time richiede la presenza di un gruppo di sicurezza di rete.
    - Macchina virtuale classica - L'accesso Just-in-Time alle macchine virtuali in Centro sicurezza attualmente supporta solo le macchine virtuali distribuite tramite Azure Resource Manager.
    - Altro - Una macchina virtuale rientra in questa categoria se la soluzione Just-In-Time è disattivata nei criteri di sicurezza della sottoscrizione o del gruppo di risorse oppure se la macchina virtuale non dispone di un indirizzo IP pubblico e di un gruppo di sicurezza di rete.

2. Selezionare una macchina virtuale consigliata e fare clic su **Enable JIT on 1 VM** (Abilita JIT in una VM) per configurare i criteri JIT per quella macchina virtuale:

  È possibile salvare le porte predefinite consigliate da Centro sicurezza di Azure o è possibile aggiungere e configurare una nuova porta su cui si intende abilitare la soluzione JIT. In questa esercitazione si aggiunge una porta selezionando **Aggiungi**.

  ![Aggiungere la configurazione della porta][2]

3. In **Aggiungi configurazione della porta**, si identifica:

  - La porta
  - Il tipo di protocollo
  - Gli indirizzi IP di origine consentiti: gli intervalli IP a cui è consentito di ottenere l'accesso in seguito a una richiesta approvata
  - Il tempo massimo per la richiesta: l'intervallo di tempo in cui può essere aperta una porta specifica

4. Selezionare **OK** per salvare.

## <a name="harden-vms-against-malware"></a>Implementare la protezione avanzata contro il malware delle macchine virtuali
I controlli delle applicazioni adattivi aiutano a definire un set di applicazioni che è possibile eseguire in gruppi di risorse configurati. Questo, oltre ad altri vantaggi, consente di implementare la protezione avanzata contro il malware delle macchine virtuali. Il Centro sicurezza usa Machine Learning per analizzare i processi in esecuzione nella macchina virtuale e, grazie a questa funzionalità intelligente, aiuta ad applicare regole di inserimento nell'elenco elementi consentiti.

Controlli applicazione adattivi è in anteprima. Questa funzionalità è disponibile solo per i computer Windows.

1. Tornare al menu principale del Centro sicurezza di Azure. In **DIFESA CLOUD AVANZATA**selezionare **Controlli delle applicazioni adattivi**.

   ![Controlli delle applicazioni adattivi][3]

  La sezione **Gruppi di risorse** contiene tre schede:

  - **Configurato**: elenco dei gruppi di risorse contenenti le macchine virtuali configurate con il controllo delle applicazioni.
  - **Consigliato**: elenco dei gruppi di risorse per cui è consigliato il controllo delle applicazioni.
  - **Nessuna raccomandazione**: elenco dei gruppi di risorse contenenti le macchine virtuali senza alcuna raccomandazione per il controllo delle applicazioni. Ad esempio, le macchine virtuali in cui le applicazioni cambiano sempre e che non hanno raggiunto uno stato stabile.

2. Selezionare la scheda **Consigliato** per un elenco dei gruppi di risorse con raccomandazioni per il controllo delle applicazioni.

  ![Raccomandazioni per il controllo delle applicazioni][4]

3. Selezionare un gruppo di risorse per aprire l'opzione **Create application control rules** (Crea regole di controllo applicazioni). In **Seleziona macchine virtuali** esaminare l'elenco di macchine virtuali consigliate e deselezionare quelle a cui non si vuole applicare il controllo delle applicazioni. In **Selezionare i processi per le regole di inserimento nell'elenco elementi consentiti** esaminare l'elenco delle applicazioni consigliate e deselezionare quelle a cui non si vuole applicare il controllo. L'elenco include:

  - **NOME**: il percorso completo dell'applicazione
  - **PROCESSI**: il numero di applicazioni in ogni percorso
  - **COMUNE**: "Sì" indica che i processi sono stati eseguiti nella maggior parte delle macchine virtuali nel gruppo di risorse
  - **SFRUTTABILE**: un'icona di avviso indica se le applicazioni possono essere usate da un utente malintenzionato per aggirare l'inserimento nell'elenco elementi consentiti. È consigliabile esaminare queste applicazioni prima di approvarle.

4. Dopo avere selezionato le opzioni desiderate, scegliere **Crea**.

## <a name="clean-up-resources"></a>Pulire le risorse
Altre guide introduttive ed esercitazioni della raccolta si basano su questa. Se si prevede di usare le guide introduttive e le esercitazioni successive, continuare a eseguire il livello Standard e tenere abilitato il provisioning automatico. Se non si intende proseguire oppure si vuole tornare al livello gratuito:

1. Tornare al menu principale del Centro sicurezza e selezionare **Criteri di sicurezza**.
2. Selezionare la sottoscrizione o i criteri per i quali si vuole tornare al livello gratuito. Si aprirà **Criteri di sicurezza**.
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
In questa esercitazione è stato descritto come limitare l'esposizione a minacce mediante le operazioni seguenti:

> [!div class="checklist"]
> * Configurazione di criteri di accesso JIT alle macchine virtuali per fornire accesso alle macchine virtuali solo quando necessario
> * Configurazione di criteri di controllo delle applicazioni adattivo per controllare quali applicazioni possono essere eseguite nelle macchine virtuali

Passare all'esercitazione successiva per informazioni su come rispondere agli eventi imprevisti relativi alla sicurezza.

> [!div class="nextstepaction"]
> [Tutorial: Respond to security incidents](tutorial-security-incident.md) (Esercitazione: Rispondere agli eventi imprevisti della sicurezza)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
