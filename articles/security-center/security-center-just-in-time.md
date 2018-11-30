---
title: Accesso JIT (Just-in-Time) alle macchine virtuali nel Centro sicurezza di Azure | Microsoft Docs
description: In questo documento viene illustrato come usare l'accesso JIT (Just-in-Time) alle macchine virtuali nel Centro sicurezza di Azure per controllare l'accesso alle macchine virtuali di Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: rkarlin
ms.openlocfilehash: 72acf0f06bbed0129ff322b10a7faf16fd94f712
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2018
ms.locfileid: "52314746"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Gestire l'accesso alle macchine virtuali con la funzionalità JIT (Just-in-Time)

L'accesso Just-in-Time alle macchine virtuali può essere usato per bloccare il traffico in ingresso alle macchine virtuali di Azure, riducendo l'esposizione agli attacchi e al tempo stesso offrendo un facile accesso per connettersi alle macchine virtuali quando necessario.

> [!NOTE]
> La funzionalità Just-In-Time è disponibile nel livello Standard del Centro sicurezza.  Per altre informazioni sui piani tariffari di Centro sicurezza, vedere [Prezzi](security-center-pricing.md).
>
>

## <a name="attack-scenario"></a>Scenario di attacco

Gli attacchi di forza bruta generalmente prendono di mira le porte di gestione per tentare di ottenere l'accesso a una macchina virtuale. Se l'attacco ha esito positivo, un utente malintenzionato può assumere il controllo della macchina virtuale e penetrare nell'ambiente.

Un modo per ridurre l'esposizione agli attacchi di forza bruta consiste nel limitare la quantità di tempo per cui la porta è aperta. Non è necessario lasciare aperte le porte di gestione in qualsiasi momento. Devono essere aperte solo durante la connessione alla macchina virtuale, ad esempio per eseguire attività di gestione o manutenzione. Quando la funzionalità JIT è abilitata, Centro sicurezza usa le regole del [gruppo di sicurezza di rete](../virtual-network/security-overview.md#security-rules) (NSG), che limitano l'accesso alle porte di gestione per impedire che possano essere attaccate da utenti malintenzionati.

![Scenario Just-in-Time][1]

## <a name="how-does-just-in-time-access-work"></a>Come funziona l'accesso Just-in-Time?

Quando è abilitata la funzionalità Just-in-Time, Centro sicurezza protegge il traffico in ingresso alle macchine virtuali di Azure creando una regola NSG. Selezionare le porte nella macchina virtuale per cui proteggere il traffico in ingresso. Queste porte sono controllate dalla soluzione Just-in-Time.

Quando un utente richiede l'accesso a una macchina virtuale, il Centro sicurezza controlla che abbuia le autorizzazioni di [controllo degli accessi in base al ruolo](../role-based-access-control/role-assignments-portal.md) che forniscono l'accesso in scrittura alla VM. Se l'utente dispone delle autorizzazioni di scrittura, la richiesta viene approvata e Centro sicurezza configura automaticamente i gruppi di sicurezza di rete per consentire il traffico in entrata alle porte selezionate per il periodo di tempo specificato. Al termine di questo periodo, Centro sicurezza ripristina gli stati precedenti dei gruppi di sicurezza di rete. Le connessioni ancora attive non vengono tuttavia interrotte.

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

> [!NOTE]
>Quando l'accesso JIT alla VM è abilitato per una macchina virtuale, il Centro sicurezza di Azure crea regole di rifiuto di tutto il traffico in ingresso per le porte selezionate nei gruppi di sicurezza di rete associati a essa. Le regole avranno la priorità maggiore per i gruppi di sicurezza di rete o una priorità inferiore rispetto alle regole esistenti già presenti. Ciò dipende dall'analisi eseguita dal Centro sicurezza di Azure che determina se una regola è sicura o meno.
>


## <a name="set-just-in-time-within-a-vm"></a>Impostare JIT in una macchina virtuale

Per semplificare l'implementazione dell'accesso JIT nelle macchine virtuali, è possibile impostare una macchina virtuale per consentire solo l'accesso JIT direttamente dall'interno della macchina virtuale.

1. Nel portale di Azure selezionare **Macchine virtuali**.
2. Fare clic sulla macchina virtuale a cui si vuole limitare l'accesso solamente all'accesso JIT.
3. Nel menu fare clic su **Configuration** (Configurazione).
4. In **Just-in-time-access** fare clic su **Enable just-in-time policy** (Abilita criterio JIT). 

In questo modo si abilita l'accesso JIT per la macchina virtuale con le impostazioni seguenti:

- Server Windows:
    - Porta RDP 3389
    - 3 ore di accesso
    - L'opzione Indirizzi IP di origine consentiti è impostata su Per richiesta
- Server Linux:
    - Porta SSH 22
    - 3 ore di accesso
    - L'opzione Indirizzi IP di origine consentiti è impostata su Per richiesta
     
Se in una macchina virtuale è già abilitato JIT, quando si passa alla pagina di configurazione, sarà possibile vedere che JIT è abilitato e si potrà usare il collegamento per aprire il criterio in Centro sicurezza di Azure per visualizzare e modificare le impostazioni.

![configurazione JIT nella macchina virtuale](./media/security-center-just-in-time/jit-vm-config.png)


## <a name="requesting-access-to-a-vm"></a>Richiedere l'accesso a una macchina virtuale

Per richiedere l'accesso a una macchina virtuale:

1. In **Accesso Just-In-Time alla VM** selezionare la scheda **Configurata**.
2. In **Macchine virtuali** selezionare le macchine virtuali per cui abilitare l'accesso. Verrà visualizzato un segno di spunta accanto a una macchina virtuale.
3. Selezionare **Richiedi accesso**. Si apre **Richiedi accesso**.

  ![Richiedere l'accesso a una macchina virtuale][4]

4. In **Richiedi accesso** è possibile configurare, per ogni macchina virtuale, le porte da aprire, l'indirizzo IP di origine per cui la porta viene aperta e l'intervallo di tempo per l'apertura della porta. È possibile richiedere l'accesso solo alle porte configurate nei criteri Just-In-Time. Ogni porta ha un tempo massimo consentito, derivato dai criteri Just-In-Time.
5. Selezionare **Open ports** (Apri porte).

> [!NOTE]
> Quando un utente richiede l'accesso a una macchina virtuale, il Centro sicurezza controlla che abbuia le autorizzazioni di [controllo degli accessi in base al ruolo](../role-based-access-control/role-assignments-portal.md) che forniscono l'accesso in scrittura alla VM. Se dispone delle autorizzazioni di scrittura, la richiesta viene approvata.azioni di scrittura, la richiesta viene approvata.
>
>

> [!NOTE]
> Se un utente che richiede l'accesso si trova dietro un proxy, l'opzione "Indirizzo IP personale" potrebbe non funzionare. Potrebbe essere necessario definire l'intervallo completo dell'organizzazione.
>
>

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

È possibile scaricare le informazioni del log selezionando **Fare clic qui per scaricare tutti gli elementi come file CSV**.

Modificare i filtri e selezionare **Applica** per creare un log ed eseguire ricerche.

## <a name="using-just-in-time-vm-access-via-rest-apis"></a>Uso dell'accesso Just-In-Time alle macchine virtuali tramite le API REST

È possibile usare la funzionalità di accesso Just-In-Time alle macchine virtuali tramite l'API Centro sicurezza di Azure. Tramite questa API è possibile ottenere informazioni sulle macchine virtuali configurate, aggiungerne di nuove, richiedere l'accesso a una macchina virtuale e altro ancora. Per informazioni sull'API REST Just-In-Time, vedere [Jit Network Access Policies](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies) (Criteri di accesso alla rete JIT).

## <a name="using-just-in-time-vm-access-via-powershell"></a>Uso dell'accesso Just-In-Time alle macchine virtuali tramite PowerShell 

Per usare la soluzione di accesso Just-In-Time alla VM tramite PowerShell, usare i cmdlet ufficiali di PowerShell di Centro sicurezza di Azure e in particolare `Set-AzureRmJitNetworkAccessPolicy`.

L'esempio seguente imposta criteri di accesso Just-In-Time alla macchina virtuale per una VM specifica e imposta quanto segue:
1.  Chiudere le porte 22 e 3389.
2.  Impostare un intervallo di tempo massimo di 3 ore per ognuna in modo che possano essere aperte in seguito all'approvazione di una richiesta.
3.  Consente all'utente che richiede l'accesso di controllare gli indirizzi IP di origine e consente all'utente di aprire una sessione riuscita in seguito all'approvazione di una richiesta di accesso JIT.

Eseguire i comandi seguenti in PowerShell per svolgere queste operazioni:

1.  Assegnare una variabile che contiene solo i criteri di accesso Just-In-Time alla macchina virtuale per una VM:

        $JitPolicy = (@{
         id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})

2.  Inserire i criteri di accesso Just-In-Time alla macchina virtuale per una VM in una matrice:
    
        $JitPolicyArr=@($JitPolicy)

3.  Configurare i criteri di accesso Just-In-Time alla macchina virtuale nella VM selezionata:
    
        Set-AzureRmJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

### <a name="requesting-access-to-a-vm"></a>Richiedere l'accesso a una macchina virtuale

Nell'esempio seguente, è possibile visualizzare una richiesta di accesso Just-In-Time alla macchina virtuale per una VM specifica in cui si richiede l'apertura della porta 22 per un indirizzo IP specifico e per un determinato periodo di tempo:

Eseguire questo comando in PowerShell:
1.  Configurare le proprietà della richiesta di accesso alla macchina virtuale

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  Inserire i parametri della richiesta di accesso alla macchina virtuale in una matrice:

        $JitPolicyArr=@($JitPolicyVm1)
3.  Inviare la richiesta di accesso usando l'ID risorsa ottenuto al passaggio 1

        Start-AzureRmJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Per altre informazioni, vedere la documentazione sui cmdlet di PowerShell.


## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come usare l'accesso JIT (Just-in-Time) alle macchine virtuali in Centro sicurezza per controllare l'accesso alle macchine virtuali di Azure.

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

- [Impostazione dei criteri di sicurezza](security-center-azure-policy.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
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
