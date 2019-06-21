---
title: Accesso JIT (Just-in-Time) alle macchine virtuali nel Centro sicurezza di Azure | Microsoft Docs
description: In questo documento viene illustrato come usare l'accesso JIT (Just-in-Time) alle macchine virtuali nel Centro sicurezza di Azure per controllare l'accesso alle macchine virtuali di Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 671930b1-fc84-4ae2-bf7c-d34ea37ec5c7
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/17/2019
ms.author: v-mohabe
ms.openlocfilehash: eb9366acf82c94bdf99c4d4f0c7c6bdf4f51e06d
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67294964"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Gestire l'accesso alle macchine virtuali con la funzionalità JIT (Just-in-Time)

L'accesso JIT alle macchine virtuali può essere usato per bloccare il traffico in ingresso alle macchine virtuali di Azure, riducendo l'esposizione agli attacchi e al tempo stesso offrendo un facile accesso per connettersi alle macchine virtuali quando necessario.

> [!NOTE]
> La funzionalità JIT è disponibile nel livello Standard del Centro sicurezza.  Per altre informazioni sui piani tariffari di Centro sicurezza, vedere [Prezzi](security-center-pricing.md).


> [!NOTE]
> Attualmente, l'accesso JIT alle macchine virtuali in Centro sicurezza supporta solo le macchine virtuali distribuite tramite Azure Resource Manager. Per altre informazioni sui modelli di distribuzione classica e con Azure Resource Manager, vedere [Distribuzione Azure Resource Manager o classica](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="attack-scenario"></a>Scenario di attacco

Gli attacchi di forza bruta generalmente prendono di mira le porte di gestione per tentare di ottenere l'accesso a una macchina virtuale. Se l'attacco ha esito positivo, un utente malintenzionato può assumere il controllo della macchina virtuale e penetrare nell'ambiente.

Un modo per ridurre l'esposizione agli attacchi di forza bruta consiste nel limitare la quantità di tempo per cui la porta è aperta. Non è necessario lasciare aperte le porte di gestione in qualsiasi momento. Devono essere aperte solo durante la connessione alla macchina virtuale, ad esempio per eseguire attività di gestione o manutenzione. Quando just-in-time è abilitata, Centro sicurezza Usa [gruppo di sicurezza di rete](../virtual-network/security-overview.md#security-rules) (NSG) e le regole del Firewall di Azure, che limitano l'accesso alle porte di gestione in modo che possano essere attaccate da utenti malintenzionati.

![Scenario JIT](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Come funziona l'accesso JIT?

Quando è abilitata la funzionalità JIT, Centro sicurezza protegge il traffico in ingresso alle macchine virtuali di Azure creando una regola NSG. Selezionare le porte nella macchina virtuale per cui proteggere il traffico in ingresso. Queste porte sono controllate dalla soluzione JIT.

Quando un utente richiede l'accesso a una macchina virtuale, Centro sicurezza controlla che l'utente disponga delle autorizzazioni di [controllo degli accessi in base al ruolo](../role-based-access-control/role-assignments-portal.md) che consentono di richiedere l'accesso alla VM. Se la richiesta viene approvata, il Centro sicurezza configura automaticamente i gruppi di sicurezza di rete (Nsg) e il Firewall di Azure per consentire il traffico in ingresso verso le porte selezionate e indirizzi IP di origine richiesto o gli intervalli, la quantità di tempo specificato. Al termine di questo periodo, Centro sicurezza ripristina gli stati precedenti dei gruppi di sicurezza di rete. Le connessioni ancora attive non vengono tuttavia interrotte.

 > [!NOTE]
 > Quindi se approvato una richiesta di accesso JIT per una macchina virtuale protetto da un Firewall di Azure, Centro sicurezza di modifica automaticamente le regole dei criteri di sicurezza di rete sia il firewall. La quantità di tempo specificato, le regole consentono il traffico in ingresso verso le porte selezionate e indirizzi IP di origine richiesto o gli intervalli. Dopo il tempo di failover, il Centro sicurezza consente di ripristinare il firewall e regole NSG per ripristinarne lo stato precedente.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Autorizzazioni necessarie per configurare e usare JIT

| Per consentire agli utenti di: | Autorizzazioni per impostare|
| --- | --- |
| Configurare o modificare i criteri JIT per una macchina virtuale | *Assegnare queste azioni al ruolo:*  Nell'ambito di una sottoscrizione o di un gruppo di risorse associato alla macchina virtuale: ```Microsoft.Security/locations/jitNetworkAccessPolicies/write``` Nell'ambito di una sottoscrizione o gruppo di risorse o della macchina virtuale: ```Microsoft.Compute/virtualMachines/write``` | 
| ||
|Richiedere l'accesso JIT a una macchina virtuale | *Assegnare all'utente queste azioni:*  Nell'ambito di una sottoscrizione o di un gruppo di risorse associato alla macchina virtuale:  ```Microsoft.Security/locations/{the_location_of_the_VM}/jitNetworkAccessPolicies/initiate/action``` Nell'ambito di una sottoscrizione o gruppo di risorse o della macchina virtuale: ```Microsoft.Compute/virtualMachines/read``` |


## <a name="configure-jit-on-a-vm"></a>Configurare JIT in una macchina virtuale

Esistono 3 modi per configurare i criteri JIT in una macchina virtuale:

- [Configurare l'accesso JIT in Centro sicurezza di Azure](#jit-asc)
- [Configurare l'accesso JIT in un pannello della VM di Azure](#jit-vm)
- [Configurare un criterio di JIT in una macchina virtuale a livello di codice](#jit-program)

## <a name="configure-jit-in-asc"></a>Configurare JIT in Centro sicurezza di AZURE

Da Centro sicurezza di AZURE, è possibile configurare un JIT criteri e richiedere l'accesso a una macchina virtuale usando i criteri JIT


### Configurare l'accesso JIT in una macchina virtuale nel Centro sicurezza di AZURE <a name="jit-asc"></a>

1. Aprire il dashboard **Centro sicurezza**.

2. Nel riquadro sinistro, selezionare **Just-in-time alle macchine Virtuali**.

    ![Riquadro Accesso Just-In-Time alla VM](./media/security-center-just-in-time/just-in-time.png)

    Viene visualizzata la finestra **Accesso Just-In-Time alla VM**.

      ![Abilitare l'accesso JIT alla VM](./media/security-center-just-in-time/enable-just-in-time.png)

    **Accesso Just-In-Time alla VM** fornisce informazioni sullo stato delle macchine virtuali:

    - **Configurata** - Macchine virtuali che sono state configurate per supportare l'accesso JIT. I dati visualizzati sono relativi all'ultima settimana e includono, per ogni macchina virtuale, il numero di richieste approvate, la data e l'ora dell'ultimo accesso e l'ultimo utente.
    - **Consigliata** - Macchine virtuali che possono supportare l'accesso JIT, ma che non sono state configurate a tale scopo. È consigliabile abilitare il controllo dell'accesso JIT per queste macchine virtuali. 
    - **Nessuna raccomandazione** - I motivi per cui una macchina virtuale può risultare non raccomandata sono:
      - Gruppo di sicurezza di rete mancante - La soluzione JIT richiede la presenza di un gruppo di sicurezza di rete.
      - Macchina virtuale classica - L'accesso JIT alle macchine virtuali in Centro sicurezza attualmente supporta solo le macchine virtuali distribuite tramite Azure Resource Manager. La soluzione JIT non supporta la distribuzione classica. 
      - Altro - una macchina virtuale si trova in questa categoria se la soluzione just-in-time è disattivata nei criteri di sicurezza della sottoscrizione o il gruppo di risorse o se la macchina virtuale non è presente un indirizzo IP pubblico e non dispone di un gruppo di sicurezza in posizione.

3. Selezionare la scheda **Consigliati**.

4. Sotto **macchina virtuale**, fare clic su macchine virtuali che si desidera abilitare. Verrà visualizzato un segno di spunta accanto a una macchina virtuale.

5. Fare clic su **Abilita JIT in VM**.
   -. Questo pannello visualizza le porte predefinite consigliate dal Centro sicurezza di Azure:
      - 22 - SSH
      - 3389 - RDP
      - 5985 - WinRM 
      - 5986 - WinRM
6. È possibile configurare anche porte personalizzate:

      1. Fare clic su **Aggiungi**. Il **Aggiungi configurazione della porta** verrà visualizzata la finestra.
      2. Per ogni porta si sceglie di configurare, entrambi predefiniti e personalizzati, è possibile personalizzare le impostazioni seguenti:

    - **Tipo di protocollo** - Il protocollo consentito su questa porta quando una richiesta viene approvata.
    - **Allowed source IP addresses** (Indirizzi IP di origine consentiti) - Gli intervalli IP consentiti su questa porta quando una richiesta viene approvata.
    - **Tempo massimo della richiesta** - È l'intervallo di tempo massimo durante il quale una porta specifica può essere aperta.

     3. Fare clic su **OK**.

1. Fare clic su **Save**.

> [!NOTE]
>Quando l'accesso alle VM JIT è abilitato per una macchina virtuale, Centro sicurezza di Azure crea regole "Nega tutto il traffico in ingresso" per le porte selezionate nel Firewall di Azure e gruppi di sicurezza di rete associati con esso. Se altre regole fosse state create per le porte selezionate, quindi le regole esistenti hanno la priorità sulle nuove regole "Nega tutto il traffico in ingresso". Se le porte selezionate non sono presenti regole esistenti, le nuove regole "Nega tutto il traffico in ingresso" richiedere ad alta priorità nei gruppi di sicurezza di rete e Firewall di Azure.


## <a name="request-jit-access-via-asc"></a>Richiedere l'accesso JIT tramite Centro sicurezza di AZURE

Per richiedere l'accesso a una macchina virtuale tramite Centro sicurezza di AZURE:

1. In **Accesso Just-In-Time alla VM** selezionare la scheda **Configurata**.

2. Sotto **macchina virtuale**, fare clic su macchine virtuali che si vuole richiedere l'accesso. In questo modo un segno di spunta accanto alla macchina virtuale.


    - L'icona nel **dettagli della connessione** colonna indica se JIT è abilitato nella sicurezza di rete o firewall. Se è attivata su entrambi, viene visualizzato solo l'icona di Firewall.

    - Il **dettagli della connessione** colonna fornisce le informazioni corrette necessarie per connettere la macchina virtuale, nonché indica le porte aperte.

      ![Richiedere l'accesso just-in-time](./media/security-center-just-in-time/request-just-in-time-access.png)

3. Fare clic su **richiedere l'accesso**. Il **richiedere l'accesso** verrà visualizzata la finestra.

      ![Dettagli JIT](./media/security-center-just-in-time/just-in-time-details.png)

4. In **Richiedi accesso** è possibile configurare, per ogni macchina virtuale, le porte da aprire, gli indirizzi IP di origine per cui la porta viene aperta e l'intervallo di tempo per l'apertura della porta. È possibile richiedere l'accesso solo alle porte configurate nei criteri JIT. Ogni porta ha un tempo massimo consentito, derivato dai criteri JIT.

5. Fare clic su **aprire le porte**.

> [!NOTE]
> Se un utente che richiede l'accesso si trova dietro un proxy, l'opzione **Indirizzo IP personale** potrebbe non funzionare. Potrebbe essere necessario definire l'intervallo di indirizzi IP completo dell'organizzazione.

## <a name="edit-a-jit-access-policy-via-asc"></a>Modificare un criterio di accesso JIT tramite Centro sicurezza di AZURE

È possibile modificare i criteri JIT esistenti di una macchina virtuale aggiungendo e configurando una nuova porta da proteggere per la macchina virtuale o modificando qualsiasi altra impostazione correlata a una porta già protetta.

Per modificare un criterio JIT esistente di una macchina virtuale:
1. Nella scheda **Configurata**, in **VM** selezionare una macchina virtuale a cui aggiungere una porta facendo clic sui tre puntini nella riga della macchina virtuale. 

1. Selezionare **Modifica**.
1. In **Configurazione dell'accesso JIT alla VM** è possibile modificare le impostazioni esistenti di una porta già protetta o aggiungere una nuova porta personalizzata. 
  ![accesso jit alle vm](./media/security-center-just-in-time/edit-policy.png)

## <a name="audit-jit-access-activity-in-asc"></a>Controllare l'attività di accesso JIT in Centro sicurezza di AZURE

È possibile ottenere informazioni approfondite sulle attività delle macchine virtuali tramite la funzionalità Ricerca log. Per visualizzare i log:

1. In **Accesso Just-In-Time alla VM** selezionare la scheda **Configurata**.
2. Sotto **VMs**, selezionare una macchina virtuale per visualizzare informazioni sul facendo clic sui tre puntini nella riga per la macchina virtuale e selezionare **Log attività** nel menu di scelta. Il **log attività** apre.

   ![Selezionare il log attività](./media/security-center-just-in-time/select-activity-log.png)

   **Log attività** fornisce una visualizzazione filtrata delle operazioni precedenti per la macchina virtuale, con data, ora e sottoscrizione.

È possibile scaricare le informazioni del log selezionando **Fare clic qui per scaricare tutti gli elementi come file CSV**.

Modificare i filtri e fare clic su **applica** per creare un log ed eseguire ricerche.



## Configurare l'accesso JIT in un pannello della VM di Azure <a name="jit-vm"></a>

Per praticità, è possibile connettersi a una VM tramite JIT direttamente dal pannello della macchina virtuale in Azure.

### <a name="configure-jit-access-on-a-vm-via-the-azure-vm-blade"></a>Configurare l'accesso JIT in una macchina virtuale usando il pannello macchine Virtuali di Azure

Per semplificare l'implementazione dell'accesso JIT nelle macchine virtuali, è possibile impostare una macchina virtuale per consentire solo l'accesso JIT direttamente dall'interno della macchina virtuale.

1. Nel portale di Azure selezionare **Macchine virtuali**.
2. Fare clic sulla macchina virtuale a cui si vuole limitare l'accesso solamente all'accesso JIT.
3. Nel menu fare clic su **Configuration** (Configurazione).
4. In **Just-in-time-access** fare clic su **Enable just-in-time policy** (Abilita criterio JIT). 

In questo modo si abilita l'accesso JIT per la macchina virtuale con le impostazioni seguenti:

- Server Windows:
    - Porta RDP 3389
    - 3 ore di accesso massimo consentito
    - Gli indirizzi IP di origine consentiti è impostata su Any
- Server Linux:
    - Porta SSH 22
    - 3 ore di accesso massimo consentito
    - Gli indirizzi IP di origine consentiti è impostata su Any
     
Se in una macchina virtuale è già abilitato JIT, quando si passa alla pagina di configurazione, sarà possibile vedere che JIT è abilitato e si potrà usare il collegamento per aprire il criterio in Centro sicurezza di Azure per visualizzare e modificare le impostazioni.

![configurazione JIT nella macchina virtuale](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="request-jit-access-to-a-vm-via-the-azure-vm-blade"></a>Richiedere l'accesso JIT per una macchina virtuale usando il pannello macchine Virtuali di Azure

Nel portale di Azure, quando si prova a connettersi a una macchina virtuale, Azure verifica se si dispone di un criterio di accesso JIT configurato su tale macchina virtuale.

- Se si dispone di un criterio JIT configurato nella macchina virtuale, è possibile fare clic su **Richiedi accesso** per abilitare l'accesso in base ai criteri JIT impostati per la macchina virtuale. 

  >![richiesta JIT](./media/security-center-just-in-time/jit-request.png)

  È richiesto l'accesso con i parametri predefiniti seguenti:

  - **IP di origine**: 'Any' (*) (non può essere modificato)
  - **intervallo di tempo**: 3 ore (non può essere modificato)  <!--Isn't this set in the policy-->
  - **numero di porta** RDP porta 22 per Linux (può essere modificata) / porta 3389 per Windows

    > [!NOTE]
    > Dopo aver approvato una richiesta per una macchina virtuale protetta da Firewall di Azure, Centro sicurezza fornisce all'utente con i dettagli di connessione appropriato (il mapping della porta dalla tabella DNAT) per la connessione alla macchina virtuale.

- In caso contrario, verrà richiesto di configurarlo.

  ![prompt jit](./media/security-center-just-in-time/jit-prompt.png)

## Configurare un criterio di JIT in una macchina virtuale a livello di codice  <a name="jit-program"></a>

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

