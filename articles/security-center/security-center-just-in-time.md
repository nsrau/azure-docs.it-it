---
title: Accesso JIT (Just-in-Time) alle macchine virtuali nel Centro sicurezza di Azure | Microsoft Docs
description: In questo documento viene illustrato come usare l'accesso JIT (Just-in-Time) alle macchine virtuali nel Centro sicurezza di Azure per controllare l'accesso alle macchine virtuali di Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 9948f4d9e6287530004b073adf10bb723899e96d
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910613"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Gestire l'accesso alle macchine virtuali con la funzionalità JIT (Just-in-Time)

L'accesso alle macchine virtuali JIT (just-in-Time) può essere usato per bloccare il traffico in ingresso verso le macchine virtuali di Azure, riducendo l'esposizione agli attacchi offrendo un facile accesso per connettersi alle macchine virtuali quando necessario.

> [!NOTE]
> La funzionalità JIT è disponibile nel livello Standard del Centro sicurezza. Per altre informazioni sui piani tariffari di Centro sicurezza, vedere [Prezzi](security-center-pricing.md).


> [!NOTE]
> Attualmente, l'accesso JIT alle macchine virtuali in Centro sicurezza supporta solo le macchine virtuali distribuite tramite Azure Resource Manager. Per altre informazioni sui modelli di distribuzione classica e con Azure Resource Manager, vedere [Distribuzione Azure Resource Manager o classica](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="attack-scenario"></a>Scenario di attacco

Gli attacchi di forza bruta generalmente prendono di mira le porte di gestione per tentare di ottenere l'accesso a una macchina virtuale. Se l'attacco ha esito positivo, un utente malintenzionato può assumere il controllo della macchina virtuale e penetrare nell'ambiente.

Un modo per ridurre l'esposizione agli attacchi di forza bruta consiste nel limitare la quantità di tempo per cui la porta è aperta. Non è necessario aprire le porte di gestione in qualsiasi momento. Devono essere aperti solo quando si è connessi alla macchina virtuale, ad esempio per eseguire attività di gestione o manutenzione. Quando è abilitata l'opzione JIT, il Centro sicurezza usa il [gruppo di sicurezza di rete](../virtual-network/security-overview.md#security-rules) (NSG) e le regole del firewall di Azure, che limitano l'accesso alle porte di gestione in modo che non possano essere usate dagli utenti malintenzionati.

![Scenario JIT](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Come funziona l'accesso JIT?

Quando è abilitata la funzionalità JIT, Centro sicurezza protegge il traffico in ingresso alle macchine virtuali di Azure creando una regola NSG. Selezionare le porte nella macchina virtuale per cui proteggere il traffico in ingresso. Queste porte sono controllate dalla soluzione JIT.

Quando un utente richiede l'accesso a una macchina virtuale, Centro sicurezza controlla che l'utente disponga delle autorizzazioni di [controllo degli accessi in base al ruolo](../role-based-access-control/role-assignments-portal.md) che consentono di richiedere l'accesso alla VM. Se la richiesta viene approvata, il Centro sicurezza configura automaticamente i gruppi di sicurezza di rete (gruppi) e il firewall di Azure per consentire il traffico in ingresso alle porte selezionate e gli intervalli o gli indirizzi IP di origine richiesti per il periodo di tempo specificato. Al termine di questo periodo, Centro sicurezza ripristina gli stati precedenti dei gruppi di sicurezza di rete. Le connessioni ancora attive non vengono tuttavia interrotte.

 > [!NOTE]
 > Se una richiesta di accesso JIT viene approvata per una macchina virtuale dietro un firewall di Azure, il Centro sicurezza modifica automaticamente le regole dei criteri NSG e firewall. Per la quantità di tempo specificata, le regole consentono il traffico in ingresso verso le porte selezionate e gli indirizzi IP o gli intervalli di origine richiesti. Al termine del periodo di tempo, il Centro sicurezza Ripristina le regole firewall e NSG agli stati precedenti.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Autorizzazioni necessarie per configurare e usare JIT

| Per consentire a un utente di: | Autorizzazioni da impostare|
| --- | --- |
| Configurare o modificare un criterio JIT per una macchina virtuale | *Assegnare queste azioni al ruolo:*  <ul><li>Nell'ambito di una sottoscrizione o di un gruppo di risorse associato alla macchina virtuale:<br/> ```Microsoft.Security/locations/jitNetworkAccessPolicies/write``` </li><li> Nell'ambito di una sottoscrizione, di un gruppo di risorse o di una machina virtuale: <br/>```Microsoft.Compute/virtualMachines/write```</li></ul> | 
| ||
|Richiedere l'accesso JIT a una macchina virtuale | *Assegnare le azioni seguenti all'utente:*  <ul><li>Nell'ambito di una sottoscrizione o di un gruppo di risorse associato alla macchina virtuale:<br/>  ```Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action``` </li><li>  Nell'ambito di una sottoscrizione, di un gruppo di risorse o di una machina virtuale:<br/> ```Microsoft.Compute/virtualMachines/read``` </li></ul>|


## <a name="configure-jit-on-a-vm"></a>Configurare JIT in una macchina virtuale

Esistono tre modi per configurare un criterio JIT in una macchina virtuale:

- [Configurare l'accesso JIT nel centro sicurezza di Azure](#jit-asc)
- [Configurare l'accesso JIT in un pannello della macchina virtuale di Azure](#jit-vm)
- [Configurare un criterio JIT in una macchina virtuale a livello di codice](#jit-program)

## <a name="configure-jit-in-asc"></a>Configurare JIT in ASC

Da ASC è possibile configurare un criterio JIT e richiedere l'accesso a una macchina virtuale usando un criterio JIT


### Configurare l'accesso JIT in una macchina virtuale in ASC<a name="jit-asc"></a>

1. Aprire il dashboard **Centro sicurezza**.

2. Nel riquadro a sinistra selezionare **Accesso Just-In-Time alla VM**.

    ![Riquadro Accesso Just-In-Time alla VM](./media/security-center-just-in-time/just-in-time.png)

    Viene visualizzata la finestra **Accesso Just-In-Time alla VM**.

      ![Abilitare l'accesso JIT alla VM](./media/security-center-just-in-time/enable-just-in-time.png)

    **Accesso Just-In-Time alla VM** fornisce informazioni sullo stato delle macchine virtuali:

    - **Configurata** - Macchine virtuali che sono state configurate per supportare l'accesso JIT. I dati visualizzati sono relativi all'ultima settimana e includono, per ogni macchina virtuale, il numero di richieste approvate, la data e l'ora dell'ultimo accesso e l'ultimo utente.
    - Macchine virtuali **consigliate** che possono supportare l'accesso just-in-time alle macchine virtuali ma non sono state configurate in. È consigliabile abilitare il controllo dell'accesso JIT per queste macchine virtuali.
    - **Nessuna raccomandazione** - I motivi per cui una macchina virtuale può risultare non raccomandata sono:
      - Gruppo di sicurezza di rete mancante - La soluzione JIT richiede la presenza di un gruppo di sicurezza di rete.
      - Macchina virtuale classica - L'accesso JIT alle macchine virtuali in Centro sicurezza attualmente supporta solo le macchine virtuali distribuite tramite Azure Resource Manager. La soluzione JIT non supporta la distribuzione classica. 
      - Altro: una macchina virtuale si trova in questa categoria se la soluzione JIT è disattivata nei criteri di sicurezza della sottoscrizione o del gruppo di risorse o se la macchina virtuale non dispone di un indirizzo IP pubblico e non ha un NSG di sicurezza di sistema.

3. Selezionare la scheda **Consigliati**.

4. In **macchina virtuale**fare clic sulle VM che si vuole abilitare. Verrà visualizzato un segno di spunta accanto a una macchina virtuale.

5. Fare clic **su Enable JIT in VM**.
   -. Questo pannello visualizza le porte predefinite consigliate dal Centro sicurezza di Azure:
      - 22 - SSH
      - 3389 - RDP
      - 5985 - WinRM 
      - 5986 - WinRM
6. È anche possibile configurare porte personalizzate:

      1. Fare clic su **Aggiungi**. Verrà visualizzata la finestra **Aggiungi configurazione porta** .
      2. Per ogni porta che si sceglie di configurare, sia predefinita che personalizzata, è possibile personalizzare le impostazioni seguenti:

    - **Tipo di protocollo** - Il protocollo consentito su questa porta quando una richiesta viene approvata.
    - **Allowed source IP addresses** (Indirizzi IP di origine consentiti) - Gli intervalli IP consentiti su questa porta quando una richiesta viene approvata.
    - **Tempo massimo della richiesta** - È l'intervallo di tempo massimo durante il quale una porta specifica può essere aperta.

     3. Fare clic su **OK**.

1. Fare clic su **Save**.

> [!NOTE]
>Quando è abilitata l'accesso JIT alla macchina virtuale per una macchina virtuale, il Centro sicurezza di Azure crea le regole di "nega tutto il traffico in ingresso" per le porte selezionate nei gruppi di sicurezza di rete associati e nel firewall di Azure. Se sono state create altre regole per le porte selezionate, le regole esistenti hanno la priorità sulla nuova regola "nega tutto il traffico in ingresso". Se non sono presenti regole esistenti sulle porte selezionate, la nuova regola "nega tutto il traffico in ingresso" ha la priorità nei gruppi di sicurezza di rete e nel firewall di Azure.


## <a name="request-jit-access-via-asc"></a>Richiedere l'accesso JIT tramite ASC

Per richiedere l'accesso a una macchina virtuale tramite ASC:

1. In **Accesso Just-In-Time alla VM** selezionare la scheda **Configurata**.

2. In **macchina virtuale**fare clic sulle macchine virtuali per cui si desidera richiedere l'accesso. Viene inserito un segno di spunta accanto alla macchina virtuale.


    - L'icona nella colonna **Dettagli connessione** indica se JIT è abilitato in NSG o FW. Se è abilitata su entrambi, viene visualizzata solo l'icona del firewall.

    - La colonna **Dettagli connessione** fornisce le informazioni necessarie per connettere la macchina virtuale e le relative porte aperte.

      ![Richiedere l'accesso just-in-time](./media/security-center-just-in-time/request-just-in-time-access.png)

3. Fare clic su **Richiedi accesso**. Verrà visualizzata la finestra **Richiedi accesso** .

      ![Dettagli JIT](./media/security-center-just-in-time/just-in-time-details.png)

4. In **Richiedi accesso** è possibile configurare, per ogni macchina virtuale, le porte da aprire, gli indirizzi IP di origine per cui la porta viene aperta e l'intervallo di tempo per l'apertura della porta. Sarà possibile richiedere l'accesso solo alle porte configurate nei criteri JIT (just-in-Time). Ogni porta ha un tempo massimo consentito, derivato dai criteri JIT.

5. Fare clic su **Apri porte**.

> [!NOTE]
> Se un utente che richiede l'accesso si trova dietro un proxy, l'opzione **Indirizzo IP personale** potrebbe non funzionare. Potrebbe essere necessario definire l'intervallo di indirizzi IP completo dell'organizzazione.

## <a name="edit-a-jit-access-policy-via-asc"></a>Modificare i criteri di accesso JIT tramite ASC

È possibile modificare i criteri JIT esistenti di una macchina virtuale aggiungendo e configurando una nuova porta da proteggere per la macchina virtuale o modificando qualsiasi altra impostazione correlata a una porta già protetta.

Per modificare un criterio JIT esistente di una macchina virtuale:
1. Nella scheda **Configurata**, in **VM** selezionare una macchina virtuale a cui aggiungere una porta facendo clic sui tre puntini nella riga della macchina virtuale. 

1. Selezionare **Modifica**.
1. In **Configurazione dell'accesso JIT alla VM** è possibile modificare le impostazioni esistenti di una porta già protetta o aggiungere una nuova porta personalizzata. 
  ![accesso jit alle vm](./media/security-center-just-in-time/edit-policy.png)

## <a name="audit-jit-access-activity-in-asc"></a>Controllare l'attività di accesso JIT in ASC

È possibile ottenere informazioni approfondite sulle attività delle macchine virtuali tramite la funzionalità Ricerca log. Per visualizzare i log:

1. In **Accesso Just-In-Time alla VM** selezionare la scheda **Configurata**.
2. In **macchine virtuali**selezionare una macchina virtuale per la quale visualizzare le informazioni facendo clic sui tre puntini di sospensione nella riga della macchina virtuale e selezionare **log attività** nel menu. Viene aperto il **log attività** .

   ![Selezionare il log attività](./media/security-center-just-in-time/select-activity-log.png)

   **Log attività** fornisce una visualizzazione filtrata delle operazioni precedenti per la macchina virtuale, con data, ora e sottoscrizione.

È possibile scaricare le informazioni del log selezionando **Fare clic qui per scaricare tutti gli elementi come file CSV**.

Modificare i filtri e fare clic su **applica** per creare una ricerca e un log.



## Configurare l'accesso JIT in un pannello della macchina virtuale di Azure<a name="jit-vm"></a>

Per praticità, è possibile connettersi a una VM tramite JIT direttamente dal pannello della macchina virtuale in Azure.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-blade"></a>Configurare l'accesso JIT in una macchina virtuale tramite il pannello della macchina virtuale di Azure

Per semplificare l'implementazione dell'accesso JIT nelle macchine virtuali, è possibile impostare una macchina virtuale per consentire solo l'accesso JIT direttamente dall'interno della macchina virtuale.

1. Nel portale di Azure selezionare **Macchine virtuali**.
2. Fare clic sulla macchina virtuale a cui si vuole limitare l'accesso solamente all'accesso JIT.
3. Nel menu fare clic su **Configuration** (Configurazione).
4. In **Just-in-time-access** fare clic su **Enable just-in-time policy** (Abilita criterio JIT). 

In questo modo si abilita l'accesso JIT per la macchina virtuale con le impostazioni seguenti:

- Server Windows:
    - Porta RDP 3389
    - Tre ore di accesso massimo consentito
    - Gli indirizzi IP di origine consentiti sono impostati su Any
- Server Linux:
    - Porta SSH 22
    - Tre ore di accesso massimo consentito
    - Gli indirizzi IP di origine consentiti sono impostati su Any
     
Se in una macchina virtuale è già abilitato JIT, quando si passa alla pagina di configurazione, sarà possibile vedere che JIT è abilitato e si potrà usare il collegamento per aprire il criterio in Centro sicurezza di Azure per visualizzare e modificare le impostazioni.

![configurazione JIT nella macchina virtuale](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-the-azure-vm-blade"></a>Richiedere l'accesso JIT a una macchina virtuale tramite il pannello della macchina virtuale di Azure

Nel portale di Azure, quando si prova a connettersi a una macchina virtuale, Azure verifica se si dispone di un criterio di accesso JIT configurato su tale macchina virtuale.

- Se si dispone di un criterio JIT configurato nella macchina virtuale, è possibile fare clic su **Richiedi accesso** per abilitare l'accesso in base ai criteri JIT impostati per la macchina virtuale. 

  >![richiesta JIT](./media/security-center-just-in-time/jit-request.png)

  L'accesso viene richiesto con i parametri predefiniti seguenti:

  - **IP di origine**: ' Any ' (*) (non può essere modificato)
  - **intervallo di tempo**: Tre ore (non modificabile) <!--Isn't this set in the policy-->
  - **numero di porta** Porta RDP 3389 per Windows/porta 22 per Linux (è possibile modificare)

    > [!NOTE]
    > Dopo che una richiesta è stata approvata per una macchina virtuale protetta da firewall di Azure, il Centro sicurezza fornisce all'utente i dettagli di connessione appropriati (il mapping della porta dalla tabella DNAT) da usare per la connessione alla macchina virtuale.

- In caso contrario, verrà richiesto di configurarlo.

  ![prompt jit](./media/security-center-just-in-time/jit-prompt.png)

## Configurare un criterio JIT in una macchina virtuale a livello di codice<a name="jit-program"></a>

È possibile configurare e usare l'accesso JIT tramite le API REST e PowerShell.

## <a name="jit-vm-access-via-rest-apis"></a>Accesso JIT alle VM tramite le API REST

È possibile usare la funzionalità di accesso JIT alle macchine virtuali tramite l'API Centro sicurezza di Azure. Tramite questa API è possibile ottenere informazioni sulle macchine virtuali configurate, aggiungerne di nuove, richiedere l'accesso a una macchina virtuale e altro ancora. Per informazioni sull'API REST Just-In-Time, vedere [Jit Network Access Policies](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies) (Criteri di accesso alla rete JIT).

## <a name="jit-vm-access-via-powershell"></a>Accesso JIT alle VM tramite PowerShell

Per usare la soluzione di accesso JIT alle macchine virtuali tramite PowerShell, usare i cmdlet ufficiali di PowerShell di Centro sicurezza di Azure e in particolare `Set-AzJitNetworkAccessPolicy`.

L'esempio seguente imposta criteri di accesso JIT alla macchina virtuale per una macchina virtuale specifica e imposta quanto segue:

1.  Chiudere le porte 22 e 3389.

2.  Impostare un intervallo di tempo massimo di 3 ore per ognuna in modo che possano essere aperte in seguito all'approvazione di una richiesta.
3.  Consente all'utente che richiede l'accesso di controllare gli indirizzi IP di origine e consente all'utente di aprire una sessione riuscita in seguito all'approvazione di una richiesta di accesso JIT.

Eseguire i comandi seguenti in PowerShell per svolgere queste operazioni:

1.  Assegnare una variabile che contiene solo i criteri di accesso JIT alla macchina virtuale per una macchina virtuale:

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

2.  Inserire i criteri di accesso JIT alla macchina virtuale in una matrice:
    
        $JitPolicyArr=@($JitPolicy)

3.  Configurare i criteri di accesso JIT alla macchina virtuale nella macchina virtuale selezionata:
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

#### <a name="request-access-to-a-vm-via-powershell"></a>Richiedere l'accesso a una macchina virtuale tramite PowerShell

Nell'esempio seguente, è possibile visualizzare una richiesta di accesso JIT alla macchina virtuale per una macchina virtuale specifica in cui si richiede l'apertura della porta 22 per un indirizzo IP specifico e per un determinato periodo di tempo:

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

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Per altre informazioni, vedere la documentazione sui cmdlet di PowerShell.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come usare l'accesso JIT (Just-in-Time) alle macchine virtuali in Centro sicurezza per controllare l'accesso alle macchine virtuali di Azure.

Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

- [Impostazione dei criteri di sicurezza](tutorial-security-policy.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
- [Gestione delle raccomandazioni di sicurezza](security-center-recommendations.md): informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.
- [Monitoraggio dell'integrità della sicurezza](security-center-monitoring.md): informazioni su come monitorare l'integrità delle risorse di Azure.
- [Gestione e risposta agli avvisi di sicurezza](security-center-managing-and-responding-alerts.md): informazioni su come gestire e rispondere agli avvisi di sicurezza.
- [Monitoraggio delle soluzioni dei partner](security-center-partner-solutions.md): informazioni su come monitorare l'integrità delle soluzioni dei partner.
- [Domande frequenti sul Centro sicurezza](security-center-faq.md): leggere le domande frequenti sull'uso del servizio.
- [Blog sulla sicurezza di Azure](https://blogs.msdn.microsoft.com/azuresecurity/) : post di blog sulla sicurezza e sulla conformità di Azure.

