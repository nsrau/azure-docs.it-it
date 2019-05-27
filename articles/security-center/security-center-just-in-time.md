---
title: Accesso JIT (Just-in-Time) alle macchine virtuali nel Centro sicurezza di Azure | Microsoft Docs
description: In questo documento viene illustrato come usare l'accesso JIT (Just-in-Time) alle macchine virtuali nel Centro sicurezza di Azure per controllare l'accesso alle macchine virtuali di Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/28/2019
ms.author: v-mohabe
ms.openlocfilehash: b07a89491343220c5c3411b5fc525f9b43f54e30
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968406"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Gestire l'accesso alle macchine virtuali con la funzionalità JIT (Just-in-Time)

L'accesso JIT alle macchine virtuali può essere usato per bloccare il traffico in ingresso alle macchine virtuali di Azure, riducendo l'esposizione agli attacchi e al tempo stesso offrendo un facile accesso per connettersi alle macchine virtuali quando necessario.

> [!NOTE]
> La funzionalità JIT è disponibile nel livello Standard del Centro sicurezza.  Per altre informazioni sui piani tariffari di Centro sicurezza, vedere [Prezzi](security-center-pricing.md).
>
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="attack-scenario"></a>Scenario di attacco

Gli attacchi di forza bruta generalmente prendono di mira le porte di gestione per tentare di ottenere l'accesso a una macchina virtuale. Se l'attacco ha esito positivo, un utente malintenzionato può assumere il controllo della macchina virtuale e penetrare nell'ambiente.

Un modo per ridurre l'esposizione agli attacchi di forza bruta consiste nel limitare la quantità di tempo per cui la porta è aperta. Non è necessario lasciare aperte le porte di gestione in qualsiasi momento. Devono essere aperte solo durante la connessione alla macchina virtuale, ad esempio per eseguire attività di gestione o manutenzione. Quando la funzionalità JIT è abilitata, Centro sicurezza usa le regole del [gruppo di sicurezza di rete](../virtual-network/security-overview.md#security-rules) (NSG), che limitano l'accesso alle porte di gestione per impedire che possano essere attaccate da utenti malintenzionati.

![Scenario JIT](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Come funziona l'accesso JIT?

Quando è abilitata la funzionalità JIT, Centro sicurezza protegge il traffico in ingresso alle macchine virtuali di Azure creando una regola NSG. Selezionare le porte nella macchina virtuale per cui proteggere il traffico in ingresso. Queste porte sono controllate dalla soluzione JIT.

Quando un utente richiede l'accesso a una macchina virtuale, Centro sicurezza controlla che l'utente disponga delle autorizzazioni di [controllo degli accessi in base al ruolo](../role-based-access-control/role-assignments-portal.md) che consentono di richiedere l'accesso alla VM. Se la richiesta viene approvata, Centro sicurezza configura automaticamente i gruppi di sicurezza di rete per consentire il traffico in entrata alle porte selezionate e gli indirizzi o gli intervalli IP di origine richiesti, per il periodo di tempo specificato. Al termine di questo periodo, Centro sicurezza ripristina gli stati precedenti dei gruppi di sicurezza di rete. Le connessioni ancora attive non vengono tuttavia interrotte.

> [!NOTE]
> Attualmente, l'accesso JIT alle macchine virtuali in Centro sicurezza supporta solo le macchine virtuali distribuite tramite Azure Resource Manager. Per altre informazioni sui modelli di distribuzione classica e con Azure Resource Manager, vedere [Distribuzione Azure Resource Manager o classica](../azure-resource-manager/resource-manager-deployment-model.md).
>
>

L'accesso JIT è possibile:
- [Usando l'accesso JIT in Centro sicurezza di Azure](#jit-asc)
- [Usando l'accesso JIT in un pannello di macchina virtuale di Azure](#jit-vm)

## Usando l'accesso JIT in Centro sicurezza di Azure <a name="jit-asc"></a>

1. Aprire il dashboard **Centro sicurezza**.

2. Nel riquadro a sinistra selezionare **Accesso Just-In-Time alla VM**.

![Riquadro Accesso Just-In-Time alla VM](./media/security-center-just-in-time/just-in-time.png)

Viene visualizzata la finestra **Accesso Just-In-Time alla VM**.

![Riquadro Accesso Just-In-Time alla VM](./media/security-center-just-in-time/just-in-time-access.png)

**Accesso Just-In-Time alla VM** fornisce informazioni sullo stato delle macchine virtuali:

- **Configurata** - Macchine virtuali che sono state configurate per supportare l'accesso JIT. I dati visualizzati sono relativi all'ultima settimana e includono, per ogni macchina virtuale, il numero di richieste approvate, la data e l'ora dell'ultimo accesso e l'ultimo utente.
- **Consigliata** - Macchine virtuali che possono supportare l'accesso JIT, ma che non sono state configurate a tale scopo. È consigliabile abilitare il controllo dell'accesso JIT per queste macchine virtuali. Vedere [Configurazione dei criteri di accesso Just-In-Time](#jit-config).
- **Nessuna raccomandazione** - I motivi per cui una macchina virtuale può risultare non raccomandata sono:
  - Gruppo di sicurezza di rete mancante - La soluzione JIT richiede la presenza di un gruppo di sicurezza di rete.
  - Macchina virtuale classica - L'accesso JIT alle macchine virtuali in Centro sicurezza attualmente supporta solo le macchine virtuali distribuite tramite Azure Resource Manager. La soluzione JIT non supporta la distribuzione classica.
  - Altro - Una macchina virtuale rientra in questa categoria se la soluzione JIT è disattivata nei criteri di sicurezza della sottoscrizione o del gruppo di risorse oppure se la macchina virtuale non dispone di un indirizzo IP pubblico e di un gruppo di sicurezza di rete.

### Configurazione di un criterio di accesso JIT<a name="jit-config"></a>

Per selezionare le macchine virtuali da abilitare:

1. In **Accesso Just-In-Time alla VM** selezionare la scheda **Consigliata**.

   ![Abilitare l'accesso JIT alla VM](./media/security-center-just-in-time/enable-just-in-time-access.png)

2. In **MACCHINA VIRTUALE** selezionare le VM che si vuole abilitare. Verrà visualizzato un segno di spunta accanto a una macchina virtuale.
3. Selezionare **Abilita JIT in VM**.
   1. Questo pannello visualizza le porte predefinite consigliate dal Centro sicurezza di Azure:
      - 22 - SSH
      - 3389 - RDP
      - 5985 - WinRM 
      - 5986 - WinRM
   2. È anche possibile configurare porte personalizzate. A questo scopo selezionare **Aggiungi**. 
   3. In **Aggiungi la configurazione della porta**, per ogni porta da configurare, predefinita e personalizzata, è possibile personalizzare le impostazioni seguenti:
      - **Tipo di protocollo** - Il protocollo consentito su questa porta quando una richiesta viene approvata.
      - **Allowed source IP addresses** (Indirizzi IP di origine consentiti) - Gli intervalli IP consentiti su questa porta quando una richiesta viene approvata.
      - **Tempo massimo della richiesta** - È l'intervallo di tempo massimo durante il quale una porta specifica può essere aperta.

4. Selezionare **Salva**.


> [!NOTE]
>Quando l'accesso alle VM JIT è abilitato per una macchina virtuale, Centro sicurezza di Azure consente di creare regole "Nega tutto il traffico in ingresso" per le porte selezionate nei gruppi di sicurezza di rete è associati. Se altre regole fosse state create per le porte selezionate, quindi le regole esistenti hanno la priorità sulle nuove regole "Nega tutto il traffico in ingresso". Se le porte selezionate non sono presenti regole esistenti, quindi le nuove regole "Nega tutto il traffico in ingresso" hanno la priorità principali nei gruppi di sicurezza di rete.
>

### <a name="request-jit-access-to-a-vm"></a>Richiedere l'accesso JIT a una macchina virtuale

Per richiedere l'accesso a una macchina virtuale:
1.  In **Accesso Just-In-Time alla VM** selezionare **Configurata**.
2.  In **Macchine virtuali** controllare le macchine virtuali per le quali abilitare l'accesso JIT.
3.  Selezionare **Richiedi accesso**. 
  ![Richiedere l'accesso a una macchina virtuale](./media/security-center-just-in-time/request-access-to-a-vm.png)
4.  In **Richiedi accesso** è possibile configurare, per ogni macchina virtuale, le porte da aprire, gli indirizzi IP di origine per cui la porta viene aperta e l'intervallo di tempo per l'apertura della porta. È possibile richiedere l'accesso solo alle porte configurate nei criteri JIT. Ogni porta ha un tempo massimo consentito, derivato dai criteri JIT.
5.  Selezionare **Open ports** (Apri porte).

> [!NOTE]
> Se un utente che richiede l'accesso si trova dietro un proxy, l'opzione **Indirizzo IP personale** potrebbe non funzionare. Potrebbe essere necessario definire l'intervallo di indirizzi IP completo dell'organizzazione.

### <a name="editing-a-jit-access-policy"></a>Modifica di un criterio di accesso JIT

È possibile modificare i criteri JIT esistenti di una macchina virtuale aggiungendo e configurando una nuova porta da proteggere per la macchina virtuale o modificando qualsiasi altra impostazione correlata a una porta già protetta.

Per modificare un criterio JIT esistente di una macchina virtuale:
1. Nella scheda **Configurata**, in **VM** selezionare una macchina virtuale a cui aggiungere una porta facendo clic sui tre puntini nella riga della macchina virtuale. 
2. Selezionare **Modifica**.
3. In **Configurazione dell'accesso JIT alla VM** è possibile modificare le impostazioni esistenti di una porta già protetta o aggiungere una nuova porta personalizzata. Per altre informazioni, vedere [Configurazione dei criteri di accesso Just-In-Time](#jit-config). 
  ![accesso jit alle vm](./media/security-center-just-in-time/edit-policy.png)

## Utilizzo dell'accesso JIT in un pannello di macchina virtuale di Azure<a name="jit-vm"></a>

Per praticità, è possibile connettersi a una VM tramite JIT direttamente dal pannello della macchina virtuale in Azure.

### <a name="configuring-a-just-in-time-access-policy"></a>Configurazione dei criteri di accesso JIT 
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

### <a name="requesting-jit-access-to-a-vm"></a>Richiesta di accesso JIT a una macchina virtuale

Nel portale di Azure, quando si prova a connettersi a una macchina virtuale, Azure verifica se si dispone di un criterio di accesso JIT configurato su tale macchina virtuale.

- In caso contrario, verrà richiesto di configurarlo.

  ![prompt jit](./media/security-center-just-in-time/jit-prompt.png)

- Se si dispone di un criterio JIT configurato nella macchina virtuale, è possibile fare clic su **Richiedi accesso** per abilitare l'accesso in base ai criteri JIT impostati per la macchina virtuale. È richiesto l'accesso con i parametri predefiniti seguenti:
    - **IP di origine**: 'Any' (*) (non può essere modificato)
    - **intervallo di tempo**: 3 ore (non può essere modificato)
    - **numero di porta** RDP porta 22 per Linux / porta 3389 per Windows (è possibile modificare il numero di porta la **Connetti a macchina virtuale** nella finestra di dialogo.)


  >![richiesta di accesso jit](./media/security-center-just-in-time/jit-request-access.png)

## <a name="auditing-jit-access-activity"></a>Controllo sulle attività di accesso JIT

È possibile ottenere informazioni approfondite sulle attività delle macchine virtuali tramite la funzionalità Ricerca log. Per visualizzare i log:

1. In **Accesso Just-In-Time alla VM** selezionare la scheda **Configurata**.
2. In **Macchine virtuali** selezionare una macchina virtuale per cui visualizzare le informazioni facendo clic sui tre puntini nella riga della macchina virtuale. Verrà aperto un menu.
3. Selezionare **Log attività** nel menu. Si apre **Log attività**.

   ![Selezionare il log attività](./media/security-center-just-in-time/select-activity-log.png)

   **Log attività** fornisce una visualizzazione filtrata delle operazioni precedenti per la macchina virtuale, con data, ora e sottoscrizione.

È possibile scaricare le informazioni del log selezionando **Fare clic qui per scaricare tutti gli elementi come file CSV**.

Modificare i filtri e selezionare **Applica** per creare un log ed eseguire ricerche.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Autorizzazioni necessarie per configurare e usare JIT

Impostare i privilegi necessari per consentire agli utenti di configurare o modificare i criteri JIT di una macchina virtuale.

Assegnare queste *azioni* al ruolo: 
- Nell'ambito di una sottoscrizione o di un gruppo di risorse associato alla macchina virtuale:
  - Microsoft.Security/locations/jitNetworkAccessPolicies/write
- Nell'ambito di una sottoscrizione, di un gruppo di risorse o di una machina virtuale:
  - Microsoft.Compute/virtualMachines/write 

Impostare questi privilegi per consentire agli utenti di richiedere l'accesso JIT per una macchina virtuale: Assegnare queste *azioni* all'utente:
- Nell'ambito di una sottoscrizione o di un gruppo di risorse associato alla macchina virtuale:
  - Microsoft.Security/locations/{posizione_della_VM}/jitNetworkAccessPolicies/ initiate/action
- Nell'ambito di una sottoscrizione, di un gruppo di risorse o di una machina virtuale:
  - Microsoft.Compute/virtualMachines/read



## <a name="use-jit-programmatically"></a>Utilizzo dell'accesso JIT a livello di codice
È possibile configurare e usare l'accesso JIT tramite le API REST e PowerShell.

### <a name="using-just-in-time-vm-access-via-rest-apis"></a>Utilizzo dell'accesso JIT alle macchine virtuali tramite le API REST

È possibile usare la funzionalità di accesso JIT alle macchine virtuali tramite l'API Centro sicurezza di Azure. Tramite questa API è possibile ottenere informazioni sulle macchine virtuali configurate, aggiungerne di nuove, richiedere l'accesso a una macchina virtuale e altro ancora. Per informazioni sull'API REST Just-In-Time, vedere [Jit Network Access Policies](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies) (Criteri di accesso alla rete JIT).

### <a name="using-jit-vm-access-via-powershell"></a>Utilizzo dell'accesso JIT alle macchine virtuali tramite PowerShell 

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

#### <a name="requesting-access-to-a-vm"></a>Richiedere l'accesso a una macchina virtuale

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

