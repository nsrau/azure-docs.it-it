---
title: Accesso JIT (Just-in-Time) alle macchine virtuali nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra in che modo JIT (just-in-Time VM Access) nel centro sicurezza di Azure consente di controllare l'accesso alle macchine virtuali di Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: a077e1dfd166051ad1cf16e42d11e8eeb61d2c91
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87419853"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Proteggere le porte di gestione con accesso just-in-Time

Bloccare il traffico in ingresso alle macchine virtuali di Azure con la funzionalità di accesso JIT (just-in-Time) del Centro sicurezza di Azure (VM). In questo modo si riduce l'esposizione agli attacchi offrendo un facile accesso quando è necessario connettersi a una macchina virtuale.

Per una spiegazione completa del funzionamento di JIT e della logica sottostante, vedere la pagina relativa [alla spiegazione JIT](just-in-time-explained.md).

Questa pagina illustra come includere JIT nel programma di sicurezza. Si apprenderà come: 

- **Abilitare JIT nelle VM** : è possibile abilitare JIT con le proprie opzioni personalizzate per una o più macchine virtuali con il Centro sicurezza, PowerShell o l'API REST. In alternativa, è possibile abilitare JIT con parametri hardcoded predefiniti da macchine virtuali di Azure. Se abilitata, JIT blocca il traffico in ingresso nelle macchine virtuali di Azure creando una regola nel gruppo di sicurezza di rete.
- **Richiedere l'accesso a una macchina virtuale in cui è abilitato JIT** : l'obiettivo di JIT è garantire che anche se il traffico in ingresso è bloccato, il Centro sicurezza offre ancora un facile accesso per connettersi alle macchine virtuali quando necessario. È possibile richiedere l'accesso a una macchina virtuale abilitata per JIT dal centro sicurezza, dalle macchine virtuali di Azure, da PowerShell o dall'API REST.
- **Controllare l'attività** : per assicurarsi che le macchine virtuali siano protette in modo appropriato, esaminare gli accessi alle macchine virtuali abilitate per JIT nell'ambito dei normali controlli di sicurezza.   



## <a name="availability"></a>Disponibilità

- Stato versione: **disponibilità generale**
- Prezzi: **livello standard**. [Altre informazioni sui prezzi](/azure/security-center/security-center-pricing).
- Ruoli e autorizzazioni necessari:
    - I ruoli **Reader** e **SecurityReader** possono visualizzare lo stato e i parametri JIT.
    - Per creare ruoli personalizzati che possono funzionare con JIT, vedere [quali sono le autorizzazioni necessarie per configurare e usare JIT?](just-in-time-explained.md#what-permissions-are-needed-to-configure-and-use-jit)
    - Per creare un ruolo con privilegi minimi per gli utenti che devono richiedere l'accesso JIT a una macchina virtuale e non eseguire altre operazioni JIT, usare lo [script set-JitLeastPrivilegedRole](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) delle pagine della community di GitHub del Centro sicurezza.
- Macchine virtuali supportate: 
    - ✔ Macchine virtuali distribuite tramite Azure Resource Manager.
    - Macchine virtuali ✘ distribuite con i modelli di distribuzione classica. [Altre informazioni su questi modelli di distribuzione](../azure-resource-manager/management/deployment-models.md).
    - Macchine virtuali ✘ protette dai firewall di Azure controllati da [gestione firewall di Azure](https://docs.microsoft.com/azure/firewall-manager/overview).
- Cloud: 
    - ✔ Cloud commerciali
    - ✔ Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)




## <a name="enable-jit-vm-access"></a>Abilitare l'accesso JIT alla VM<a name="jit-configure"></a>

È possibile abilitare l'accesso JIT alle VM con le opzioni personalizzate per una o più macchine virtuali con il Centro sicurezza o a livello di codice. 

In alternativa, è possibile abilitare JIT con parametri hardcoded predefiniti da macchine virtuali di Azure.

Ognuna di queste opzioni è illustrata in una scheda separata riportata di seguito.

### <a name="azure-security-center"></a>[**Centro sicurezza di Azure**](#tab/jit-config-asc)

### <a name="enable-jit-on-your-vms-from-azure-security-center"></a>Abilitare JIT nelle VM dal centro sicurezza di Azure<a name="jit-asc"></a>

![Configurazione dell'accesso JIT alle macchine virtuali nel centro sicurezza di Azure](./media/security-center-just-in-time/jit-config-security-center.gif)

Dal centro sicurezza è possibile abilitare e configurare l'accesso JIT alla macchina virtuale.

1. Dal menu del Centro sicurezza selezionare **accesso just-in-time alle macchine virtuali**.

    Viene visualizzata la pagina di **accesso just-in-time alle VM** con le macchine virtuali raggruppate nelle schede seguenti:

    - **Configurate** : macchine virtuali che sono già state configurate per supportare l'accesso just-in-time alle macchine virtuali. Per ogni macchina virtuale, la scheda configurata Mostra:
        - numero di richieste JIT approvate negli ultimi sette giorni
        - Data e ora dell'ultimo accesso
        - dettagli della connessione configurati
        - Ultimo utente
    - **Non configurato** : le macchine virtuali senza JIT sono abilitate, ma in grado di supportare JIT. Si consiglia di abilitare JIT per queste macchine virtuali.
    - Non **supportate** : le macchine virtuali senza JIT sono abilitate e non supportano la funzionalità. La macchina virtuale potrebbe trovarsi in questa scheda per i motivi seguenti:
      - Gruppo di sicurezza di rete mancante (NSG): JIT richiede la configurazione di un NSG
      - VM classica: JIT supporta le macchine virtuali distribuite tramite Azure Resource Manager, non "distribuzione classica". [Altre informazioni sui modelli di distribuzione classica di Visual studio Azure Resource Manager](../azure-resource-manager/management/deployment-models.md).
      - Altro: la macchina virtuale potrebbe trovarsi in questa scheda se la soluzione JIT è disabilitata nei criteri di sicurezza della sottoscrizione o del gruppo di risorse.

1. Dalla scheda **non configurata** , contrassegnare le VM da proteggere con JIT e selezionare **Abilita JIT sulle VM**. 

    Viene visualizzata la pagina accesso JIT alla macchina virtuale che elenca le porte che il Centro sicurezza consiglia di proteggere:
    - 22 - SSH
    - 3389 - RDP
    - 5985 - WinRM 
    - 5986 - WinRM

    Per accettare le impostazioni predefinite, selezionare **Salva**.

1. Per personalizzare le opzioni JIT:

    - Aggiungere porte personalizzate con il pulsante **Aggiungi** . 
    - Modificare una delle porte predefinite selezionandola nell'elenco.

    Per ogni porta (personalizzata e predefinita) il riquadro **configurazione aggiunta porta** offre le opzioni seguenti:

    - **Protocollo**: protocollo consentito su questa porta quando viene approvata una richiesta
    - **Indirizzi IP di origine consentiti**: intervalli IP consentiti su questa porta quando viene approvata una richiesta
    - **Tempo di richiesta massimo**: intervallo di tempo massimo durante il quale è possibile aprire una porta specifica

     1. Impostare la sicurezza delle porte in base alle esigenze.

     1. Selezionare **OK**.

1. Selezionare **Salva**.



### <a name="edit-the-jit-configuration-on-a-jit-enabled-vm-using-security-center"></a>Modificare la configurazione JIT in una VM abilitata per JIT usando il Centro sicurezza<a name="jit-modify"></a>

È possibile modificare la configurazione JIT di una macchina virtuale aggiungendo e configurando una nuova porta da proteggere per tale macchina virtuale o modificando qualsiasi altra impostazione correlata a una porta già protetta.

Per modificare le regole JIT esistenti per una macchina virtuale:

1. Dal menu del Centro sicurezza selezionare **accesso just-in-time alle macchine virtuali**.

1. Dalla scheda **configurato** , fare clic con il pulsante destro del mouse sulla macchina virtuale a cui si desidera aggiungere una porta e scegliere modifica. 

    ![Modifica di una configurazione di accesso JIT alla VM nel centro sicurezza di Azure](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

1. In **Configurazione dell'accesso JIT alla VM** è possibile modificare le impostazioni esistenti di una porta già protetta o aggiungere una nuova porta personalizzata.

1. Al termine della modifica delle porte, selezionare **Salva**.
 


### <a name="azure-virtual-machines"></a>[**Macchine virtuali di Azure**](#tab/jit-config-avm)

### <a name="enable-jit-on-your-vms-from-azure-virtual-machines"></a>Abilitare JIT nelle VM da macchine virtuali di Azure

È possibile abilitare JIT in una macchina virtuale dalle pagine di macchine virtuali di Azure della portale di Azure.

![Configurazione dell'accesso JIT alle VM in macchine virtuali di Azure](./media/security-center-just-in-time/jit-config-virtual-machines.gif)

> [!TIP]
> Se una macchina virtuale ha già abilitato JIT, quando si passa alla relativa pagina di configurazione si noterà che JIT è abilitato ed è possibile usare il collegamento per aprire la pagina di accesso just-in-time alle macchine virtuali nel centro sicurezza e visualizzare e modificare le impostazioni.

1. Dalla [portale di Azure](https://ms.portal.azure.com)cercare e selezionare **macchine virtuali**. 

1. Selezionare la macchina virtuale che si vuole proteggere con JIT.

1. Nel menu selezionare **configurazione**.

1. In **accesso JIT**selezionare **Abilita Just-in-Time**. 

    Questo consente l'accesso JIT per la macchina virtuale usando le impostazioni predefinite seguenti:

    - Computer Windows:
        - Porta RDP 3389
        - Tre ore di accesso massimo consentito
        - Gli indirizzi IP di origine consentiti sono impostati su Any
    - Computer Linux:
        - Porta SSH 22
        - Tre ore di accesso massimo consentito
        - Gli indirizzi IP di origine consentiti sono impostati su Any

1. Per modificare uno di questi valori o aggiungere altre porte alla configurazione JIT, usare la pagina just-in-Time del Centro sicurezza di Azure:

    1. Dal menu del Centro sicurezza selezionare **accesso just-in-time alle macchine virtuali**.

    1. Dalla scheda **configurato** , fare clic con il pulsante destro del mouse sulla macchina virtuale a cui si desidera aggiungere una porta e scegliere modifica. 

        ![Modifica di una configurazione di accesso JIT alla VM nel centro sicurezza di Azure](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

    1. In **Configurazione dell'accesso JIT alla VM** è possibile modificare le impostazioni esistenti di una porta già protetta o aggiungere una nuova porta personalizzata.

    1. Al termine della modifica delle porte, selezionare **Salva**.


### <a name="powershell"></a>[**PowerShell**](#tab/jit-config-powershell)

### <a name="enable-jit-on-your-vms-using-powershell"></a>Abilitare JIT nelle VM tramite PowerShell

Per abilitare l'accesso just-in-time alle macchine virtuali da PowerShell, usare il cmdlet di PowerShell ufficiale del Centro sicurezza di Azure `Set-AzJitNetworkAccessPolicy` .

**Esempio** : abilitare l'accesso just-in-time alle macchine virtuali in una macchina virtuale specifica con le regole seguenti:

* Chiudere le porte 22 e 3389
* Impostare un intervallo di tempo massimo di 3 ore per ciascuno, in modo che sia possibile aprirlo per ogni richiesta approvata
* Consente all'utente che richiede l'accesso di controllare gli indirizzi IP di origine
* Consenti all'utente che richiede l'accesso di stabilire una sessione riuscita su una richiesta di accesso just-in-Time approvata

I comandi di PowerShell seguenti creano questa configurazione JIT:

1. Assegnare una variabile che contenga le regole di accesso just-in-time per una macchina virtuale:

    ```azurepowershell
    $JitPolicy = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
             number=22;
             protocol="\*";
             allowedSourceAddressPrefix=@("\*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="\*";
             allowedSourceAddressPrefix=@("\*");
             maxRequestAccessDuration="PT3H"})})
    ```

1. Inserire le regole di accesso just-in-time della macchina virtuale in una matrice:
    
    ```azurepowershell
    $JitPolicyArr=@($JitPolicy)
    ```

1. Configurare le regole di accesso just-in-time alle macchine virtuali nella macchina virtuale selezionata:
    
    ```azurepowershell
    Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr
    ```

    Usare il parametro-Name per specificare una macchina virtuale. Ad esempio, per stabilire la configurazione JIT per due diverse macchine virtuali, VM1 e VM2, usare: ```Set-AzJitNetworkAccessPolicy -Name VM1``` e ```Set-AzJitNetworkAccessPolicy -Name VM2``` .


### <a name="rest-api"></a>[**API REST**](#tab/jit-config-api)

### <a name="enable-jit-on-your-vms-using-the-rest-api"></a>Abilitare JIT nelle VM usando l'API REST

È possibile usare la funzionalità di accesso JIT alle macchine virtuali tramite l'API Centro sicurezza di Azure. Usare questa API per ottenere informazioni sulle macchine virtuali configurate, aggiungerne di nuove, richiedere l'accesso a una macchina virtuale e altro ancora. 

Per altre informazioni, vedere [criteri di accesso alla rete JIT](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies).


--- 










## <a name="request-access-to-a-jit-enabled-vm"></a>Richiedere l'accesso a una macchina virtuale abilitata per JIT

È possibile richiedere l'accesso a una VM abilitata per JIT dal portale di Azure (nel centro sicurezza o macchine virtuali di Azure) o a livello di codice.

Ognuna di queste opzioni è illustrata in una scheda separata riportata di seguito.

### <a name="azure-security-center"></a>[**Centro sicurezza di Azure**](#tab/jit-request-asc)

### <a name="request-access-to-a-jit-enabled-vm-from-azure-security-center"></a>Richiedere l'accesso a una macchina virtuale abilitata per JIT dal centro sicurezza di Azure 

Quando per una macchina virtuale è abilitata la modalità JIT, è necessario richiedere l'accesso per la connessione. È possibile richiedere l'accesso in qualsiasi modo supportato, indipendentemente dal modo in cui è stato abilitato JIT.

![Richiesta dell'accesso JIT dal centro sicurezza](./media/security-center-just-in-time/jit-request-security-center.gif)

1. Dalla pagina **accesso just-in-time alle VM** selezionare la scheda **configurata** .

1. Contrassegnare le macchine virtuali a cui si vuole accedere.

    - L'icona nella colonna **Dettagli connessione** indica se JIT è abilitato nel gruppo di sicurezza di rete o nel firewall. Se è abilitata su entrambi, viene visualizzata solo l'icona del firewall.

    - La colonna **Dettagli connessione** fornisce le informazioni necessarie per connettere la macchina virtuale e le relative porte aperte.

1. Selezionare **Richiedi accesso**. Verrà visualizzata la finestra **Richiedi accesso** .

1. In **Richiedi accesso** è possibile configurare, per ogni macchina virtuale, le porte da aprire, gli indirizzi IP di origine per cui la porta viene aperta e l'intervallo di tempo per l'apertura della porta. Sarà possibile richiedere l'accesso alle porte configurate. Ogni porta ha un tempo massimo consentito derivato dalla configurazione JIT creata.

1. Selezionare **Open ports** (Apri porte).

> [!NOTE]
> Se un utente che richiede l'accesso si trova dietro un proxy, l'opzione **Indirizzo IP personale** potrebbe non funzionare. Potrebbe essere necessario definire l'intervallo di indirizzi IP completo dell'organizzazione.



### <a name="azure-virtual-machines"></a>[**Macchine virtuali di Azure**](#tab/jit-request-avm)

### <a name="request-access-to-a-jit-enabled-vm-from-the-azure-virtual-machines-connect-page"></a>Richiedere l'accesso a una VM abilitata per JIT dalla pagina Connect della macchina virtuale di Azure

Quando per una macchina virtuale è abilitata la modalità JIT, è necessario richiedere l'accesso per la connessione. È possibile richiedere l'accesso in qualsiasi modo supportato, indipendentemente dal modo in cui è stato abilitato JIT.

  >![richiesta JIT JIT](./media/security-center-just-in-time/jit-request-vm.png)


Per richiedere l'accesso da macchine virtuali di Azure:

1. Nella portale di Azure aprire le pagine macchine virtuali.

1. Selezionare la macchina virtuale a cui si desidera connettersi e aprire la pagina **Connetti** .

    Azure verifica se JIT è abilitato nella macchina virtuale.

    - Se JIT non è abilitato per la macchina virtuale, verrà richiesto di abilitarlo.

    - Se JIT è abilitato, selezionare **Richiedi accesso** per passare una richiesta di accesso con l'IP richiedente, l'intervallo di tempo e le porte configurate per la macchina virtuale.

> [!NOTE]
> Dopo che una richiesta è stata approvata per una macchina virtuale protetta da firewall di Azure, il Centro sicurezza fornisce all'utente i dettagli di connessione appropriati (il mapping della porta dalla tabella DNAT) da usare per la connessione alla macchina virtuale.



### <a name="powershell"></a>[**PowerShell**](#tab/jit-request-powershell)

### <a name="request-access-to-a-jit-enabled-vm-using-powershell"></a>Richiedere l'accesso a una macchina virtuale abilitata per JIT usando PowerShell

Nell'esempio seguente, è possibile visualizzare una richiesta di accesso JIT alla macchina virtuale per una macchina virtuale specifica in cui si richiede l'apertura della porta 22 per un indirizzo IP specifico e per un determinato periodo di tempo:

Eseguire questo comando in PowerShell:

1. Configurare le proprietà di accesso alla richiesta della macchina virtuale:

    ```azurepowershell
    $JitPolicyVm1 = (@{
        id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
           number=22;
           endTimeUtc="2020-07-15T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
    ```

1. Inserire i parametri della richiesta di accesso alla macchina virtuale in una matrice:

    ```azurepowershell
    $JitPolicyArr=@($JitPolicyVm1)
    ```
        
1. Inviare l'accesso alla richiesta (usare l'ID risorsa del passaggio 1)

    ```azurepowershell
    Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr
    ```

Per altre informazioni, vedere la [documentazione del cmdlet di PowerShell](https://docs.microsoft.com/powershell/scripting/developer/cmdlet/cmdlet-overview).



### <a name="rest-api"></a>[**API REST**](#tab/jit-request-api)

### <a name="request-access-to-a-jit-enabled-vms-using-the-rest-api"></a>Richiedere l'accesso a una VM abilitata per JIT usando l'API REST

È possibile usare la funzionalità di accesso JIT alle macchine virtuali tramite l'API Centro sicurezza di Azure. Usare questa API per ottenere informazioni sulle macchine virtuali configurate, aggiungerne di nuove, richiedere l'accesso a una macchina virtuale e altro ancora. 

Per altre informazioni, vedere [criteri di accesso alla rete JIT](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies).

---








## <a name="audit-jit-access-activity-in-security-center"></a>Controllare l'attività di accesso JIT nel centro sicurezza

È possibile ottenere informazioni approfondite sulle attività delle macchine virtuali tramite la funzionalità Ricerca log. Per visualizzare i log:

1. Dall' **accesso just-in-time alle macchine virtuali**, selezionare la scheda **configurata** .

1. Per la macchina virtuale che si desidera controllare, aprire il menu con i puntini di sospensione alla fine della riga.
 
1. Selezionare **log attività** dal menu.

   ![Selezionare il log attività JIT just-in-Time](./media/security-center-just-in-time/jit-select-activity-log.png)

   Il log attività fornisce una visualizzazione filtrata delle operazioni precedenti per tale macchina virtuale insieme a data, ora e sottoscrizione.

1. Per scaricare le informazioni del log, selezionare **Scarica come CSV**.








## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come configurare e usare l'accesso just-in-time alle macchine virtuali. Per informazioni sul motivo per cui è necessario usare JIT, leggere l'articolo del concetto che descrive le minacce da difendere:

> [!div class="nextstepaction"]
> [Spiegazione di JIT](just-in-time-explained.md)