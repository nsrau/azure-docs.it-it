---
title: Accesso JIT (Just-in-Time) alle macchine virtuali nel Centro sicurezza di Azure | Microsoft Docs
description: In questo documento viene illustrato come usare l'accesso JIT (Just-in-Time) alle macchine virtuali nel Centro sicurezza di Azure per controllare l'accesso alle macchine virtuali di Azure.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/26/2018
ms.author: terrylan
ms.openlocfilehash: a15857f0df5c967031aed00d89e71b3199eed0c4
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="manage-virtual-machine-access-using-just-in-time-preview"></a>Gestire l'accesso alle macchine virtuali con la funzionalità JIT (Just-in-Time) (anteprima)

L'accesso Just-in-Time alle macchine virtuali può essere usato per bloccare il traffico in ingresso alle macchine virtuali di Azure, riducendo l'esposizione agli attacchi e al tempo stesso offrendo un facile accesso per connettersi alle macchine virtuali quando necessario.

> [!NOTE]
> La funzionalità Just-In-Time è in anteprima e disponibile nel livello Standard di Centro sicurezza.  Per altre informazioni sui piani tariffari di Centro sicurezza, vedere [Prezzi](security-center-pricing.md).
>
>

## <a name="attack-scenario"></a>Scenario di attacco

Gli attacchi di forza bruta generalmente prendono di mira le porte di gestione per tentare di ottenere l'accesso a una macchina virtuale. Se l'attacco ha esito positivo, un utente malintenzionato può assumere il controllo della macchina virtuale e penetrare nell'ambiente.

Un modo per ridurre l'esposizione agli attacchi di forza bruta consiste nel limitare la quantità di tempo per cui la porta è aperta. Non è necessario lasciare aperte le porte di gestione in qualsiasi momento. Devono essere aperte solo durante la connessione alla macchina virtuale, ad esempio per eseguire attività di gestione o manutenzione. Quando la funzionalità Just-In-Time è abilitata, Centro sicurezza usa le regole del [gruppo di sicurezza di rete ](../virtual-network/virtual-networks-nsg.md) (NSG), che limitano l'accesso alle porte di gestione per impedire che possano essere attaccate da utenti malintenzionati.

![Scenario Just-in-Time][1]

## <a name="how-does-just-in-time-access-work"></a>Come funziona l'accesso Just-in-Time?

Quando è abilitata la funzionalità Just-in-Time, Centro sicurezza protegge il traffico in ingresso alle macchine virtuali di Azure creando una regola NSG. Selezionare le porte nella macchina virtuale per cui proteggere il traffico in ingresso. Queste porte sono controllate dalla soluzione Just-in-Time.

Quando un utente richiede l'accesso a una macchina virtuale, il Centro sicurezza controlla che abbuia le autorizzazioni di [controllo degli accessi in base al ruolo](../active-directory/role-based-access-control-configure.md) che forniscono l'accesso in scrittura alla VM. Se l'utente dispone delle autorizzazioni di scrittura, la richiesta viene approvata e Centro sicurezza configura automaticamente i gruppi di sicurezza di rete per consentire il traffico in entrata alle porte di gestione per il periodo di tempo specificato. Al termine di questo periodo, Centro sicurezza ripristina gli stati precedenti dei gruppi di sicurezza di rete.

> [!NOTE]
> L'accesso Just-in-Time alle macchine virtuali in Centro sicurezza attualmente supporta solo le macchine virtuali distribuite tramite Azure Resource Manager. Per altre informazioni sui modelli di distribuzione classica e con Azure Resource Manager, vedere [Distribuzione Azure Resource Manager o classica](../azure-resource-manager/resource-manager-deployment-model.md).
>
>

## <a name="using-just-in-time-access"></a>Uso dell'accesso Just-in-Time

1. Aprire il dashboard **Centro sicurezza**.

2. Nel riquadro a sinistra selezionare **Accesso Just-In-Time alla VM**.

![Riquadro Accesso Just-In-Time alla VM][2]

Viene visualizzata la finestra **Accesso Just-In-Time alla VM**.

![Riquadro Accesso Just-In-Time alla VM][10]

**Accesso Just-In-Time alla VM** fornisce informazioni sullo stato delle macchine virtuali:

- **Configurata** - Macchine virtuali che sono state configurate per supportare l'accesso Just-In-Time. I dati visualizzati sono relativi all'ultima settimana e includono, per ogni macchina virtuale, il numero di richieste approvate, la data e l'ora dell'ultimo accesso e l'ultimo utente.
- **Consigliata** - Macchine virtuali che possono supportare l'accesso Just-In-Time, ma che non sono state configurate a tale scopo. È consigliabile abilitare il controllo dell'accesso Just-In-Time per queste macchine virtuali. Vedere [Configurazione dei criteri di accesso Just-In-Time](#configuring-a-just-in-time-access-policy).
- **Nessuna raccomandazione** - I motivi per cui una macchina virtuale può risultare non raccomandata sono:
  - Gruppo di sicurezza di rete mancante - La soluzione Just-In-Time richiede la presenza di un gruppo di sicurezza di rete.
  - Macchina virtuale classica - L'accesso Just-in-Time alle macchine virtuali in Centro sicurezza attualmente supporta solo le macchine virtuali distribuite tramite Azure Resource Manager. Una distribuzione classica non è supportata dalla soluzione Just-In-Time.
  - Altro - Una macchina virtuale rientra in questa categoria se la soluzione Just-In-Time è disattivata nei criteri di sicurezza della sottoscrizione o del gruppo di risorse oppure se la macchina virtuale non dispone di un indirizzo IP pubblico e di un gruppo di sicurezza di rete.

## <a name="configuring-a-just-in-time-access-policy"></a>Configurazione dei criteri di accesso Just-In-Time

Per selezionare le macchine virtuali da abilitare:

1. In **Accesso Just-In-Time alla VM** selezionare la scheda **Consigliata**.

  ![Abilitare l'accesso Just-in-Time][3]

2. In **MACCHINA VIRTUALE** selezionare le VM che si vuole abilitare. Verrà visualizzato un segno di spunta accanto a una macchina virtuale.
3. Selezionare **Abilita JIT in VM**.
4. Selezionare **Salva**.

### <a name="default-ports"></a>Porte predefinite

È possibile visualizzare le porte predefinite per cui Centro sicurezza consiglia l'abilitazione della funzionalità Just-In-Time.

1. In **Accesso Just-In-Time alla VM** selezionare la scheda **Consigliata**.

  ![Visualizzare le porte predefinite][6]

2. In **Macchine virtuali** selezionare una macchina virtuale. Verrà visualizzato un segno di spunta accanto alla macchina virtuale e verrà aperto **Configurazione dell'accesso JIT alla VM**. Questo pannello consente di visualizzare le porte predefinite.

### <a name="add-ports"></a>Aggiungere porte

In **Configurazione dell'accesso JIT alla VM** è anche possibile aggiungere e configurare una nuova porta per cui abilitare la soluzione Just-In-Time.

1. In **Configurazione dell'accesso JIT alla VM** selezionare **Aggiungi**. Verrà aperto **Add port configuration** (Aggiungi configurazione porta).

  ![Configurazione delle porte][7]

2. In **Add port configuration** (Aggiungi configurazione porta) identificare la porta, il tipo di protocollo, gli indirizzi IP di origine consentiti e il tempo massimo per la richiesta.

  Gli indirizzi IP di origine consentiti sono gli intervalli IP a cui è consentito di ottenere l'accesso in seguito a una richiesta approvata.

  Il tempo massimo per la richiesta è l'intervallo di tempo massimo che può essere aperto da una porta specifica.

3. Selezionare **OK**.

## <a name="requesting-access-to-a-vm"></a>Richiedere l'accesso a una macchina virtuale

Per richiedere l'accesso a una macchina virtuale:

1. In **Accesso Just-In-Time alla VM** selezionare la scheda **Configurata**.
2. In **Macchine virtuali** selezionare le macchine virtuali per cui abilitare l'accesso. Verrà visualizzato un segno di spunta accanto a una macchina virtuale.
3. Selezionare **Richiedi accesso**. Si apre **Richiedi accesso**.

  ![Richiedere l'accesso a una macchina virtuale][4]

4. In **Richiedi accesso** è possibile configurare, per ogni macchina virtuale, le porte da aprire, l'indirizzo IP di origine per cui la porta viene aperta e l'intervallo di tempo per l'apertura della porta. È possibile richiedere l'accesso solo alle porte configurate nei criteri Just-In-Time. Ogni porta ha un tempo massimo consentito, derivato dai criteri Just-In-Time.
5. Selezionare **Open ports** (Apri porte).

## <a name="editing-a-just-in-time-access-policy"></a>Modifica dei criteri di accesso Just-In-Time

È possibile modificare i criteri Just-In-Time esistenti di una macchina virtuale aggiungendo e configurando una nuova porta da aprire per la macchina virtuale o modificando qualsiasi altro parametro correlato a una porta già protetta.

Per modificare i criteri Just-In-Time esistenti di una macchina virtuale, viene usata la scheda **Configurata**:

1. In **Macchine virtuali** selezionare una macchina virtuale a cui aggiungere una porta facendo clic sui tre puntini nella riga della macchina virtuale. Verrà aperto un menu.
2. Selezionare **Modifica** nel menu. Verrà visualizzato **Configurazione dell'accesso JIT alla VM**.

  ![Modificare i criteri][8]

3. In **Configurazione dell'accesso JIT alla VM** è possibile modificare le impostazioni esistenti di una porta già protetta facendo clic sulla porta desiderata oppure è possibile selezionare **Aggiungi**. Verrà aperto **Add port configuration** (Aggiungi configurazione porta).

  ![Aggiungere una porta][7]

4. In **Add port configuration** (Aggiungi configurazione porta) identificare la porta, il tipo di protocollo, gli indirizzi IP di origine consentiti e il tempo massimo per la richiesta.
5. Selezionare **OK**.
6. Selezionare **Salva**.

## <a name="auditing-just-in-time-access-activity"></a>Controllo delle attività di accesso Just-in-Time

È possibile ottenere informazioni approfondite sulle attività delle macchine virtuali tramite la funzionalità Ricerca log. Per visualizzare i log:

1. In **Accesso Just-In-Time alla VM** selezionare la scheda **Configurata**.
2. In **Macchine virtuali** selezionare una macchina virtuale per cui visualizzare le informazioni facendo clic sui tre puntini nella riga della macchina virtuale. Verrà aperto un menu.
3. Selezionare **Log attività** nel menu. Si apre **Log attività**.

  ![Selezionare il log attività][9]

  **Log attività** fornisce una visualizzazione filtrata delle operazioni precedenti per la macchina virtuale, con data, ora e sottoscrizione.

  ![Visualizzare il log attività][5]

È possibile scaricare le informazioni del log selezionando **Fare clic qui per scaricare tutti gli elementi come file CSV**.

Modificare i filtri e selezionare **Applica** per creare un log ed eseguire ricerche.

## <a name="using-just-in-time-vm-access-via-powershell"></a>Uso dell'accesso Just-In-Time alle macchine virtuali tramite PowerShell

Per usare la soluzione Just-In-Time tramite PowerShell, verificare di disporre della [versione più recente](/powershell/azure/install-azurerm-ps) di Azure PowerShell.
È quindi necessario installare la [versione più recente](https://aka.ms/asc-psgallery) del Centro sicurezza di Azure da PowerShell Gallery.

### <a name="configuring-a-just-in-time-policy-for-a-vm"></a>Configurazione dei criteri Just-In-Time per una macchina virtuale

Per configurare i criteri Just-In-Time in una macchina virtuale specifica, è necessario eseguire il comando seguente nella sessione di PowerShell: Set-ASCJITAccessPolicy.
Per altre informazioni, vedere la documentazione del cmdlet.

### <a name="requesting-access-to-a-vm"></a>Richiedere l'accesso a una macchina virtuale

Per accedere a una macchina virtuale specifica che è protetta con la soluzione Just-In-Time, è necessario eseguire il comando seguente nella sessione di PowerShell: Invoke-ASCJITAccess.
Per altre informazioni, vedere la documentazione del cmdlet.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come usare l'accesso JIT (Just-in-Time) alle macchine virtuali in Centro sicurezza per controllare l'accesso alle macchine virtuali di Azure.

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

- [Impostazione dei criteri di sicurezza](security-center-policies.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
- [Gestione delle raccomandazioni di sicurezza](security-center-recommendations.md): informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
- [Monitoraggio dell'integrità della sicurezza](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
- [Gestione e risposta agli avvisi di sicurezza](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
- [Monitoraggio delle soluzioni dei partner](security-center-partner-solutions.md): informazioni su come monitorare l'integrità delle soluzioni dei partner.
- [Domande frequenti sul Centro sicurezza](security-center-faq.md): leggere le domande frequenti sull'uso del servizio.
- [Blog sulla sicurezza di Azure](https://blogs.msdn.microsoft.com/azuresecurity/) : post di blog sulla sicurezza e sulla conformità di Azure.


<!--Image references-->
[1]: ./media/security-center-just-in-time/just-in-time-scenario.png
[2]: ./media/security-center-just-in-time/just-in-time.png
[10]: ./media/security-center-just-in-time/just-in-time-access.png
[3]: ./media/security-center-just-in-time/enable-just-in-time-access.png
[4]: ./media/security-center-just-in-time/request-access-to-a-vm.png
[5]: ./media/security-center-just-in-time/activity-log.png
[6]: ./media/security-center-just-in-time/default-ports.png
[7]: ./media/security-center-just-in-time/add-a-port.png
[8]: ./media/security-center-just-in-time/edit-policy.png
[9]: ./media/security-center-just-in-time/select-activity-log.png
