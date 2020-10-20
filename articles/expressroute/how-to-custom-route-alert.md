---
title: 'ExpressRoute: come configurare gli avvisi personalizzati per le route annunciate'
description: Questo articolo illustra come usare automazione di Azure e le app per la logica per monitorare il numero di route annunciate dal gateway ExpressRoute alle reti locali per evitare di raggiungere il limite di 200 Route.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 05/29/2020
ms.author: duau
ms.openlocfilehash: b812a727a555f8c4c95389f2ba0cc5ccea2f608a
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205560"
---
# <a name="configure-custom-alerts-to-monitor-advertised-routes"></a>Configurare avvisi personalizzati per il monitoraggio di route annunciate

Questo articolo consente di usare automazione di Azure e app per la logica per monitorare costantemente il numero di route annunciate dal gateway ExpressRoute alle reti locali. Il monitoraggio consente di evitare di raggiungere il [limite di 200 Route](expressroute-faqs.md#how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering).

**Automazione di Azure** consente di automatizzare l'esecuzione dello script di PowerShell personalizzato archiviato in un *Runbook*. Quando si usa la configurazione in questo articolo, Runbook contiene uno script di PowerShell che esegue una query su uno o più gateway ExpressRoute. Raccoglie un set di dati contenente il gruppo di risorse, il nome del gateway ExpressRoute e il numero di prefissi di rete annunciati in locale.

**App** per la logica di Azure pianifica un flusso di lavoro personalizzato che chiama il Runbook di automazione di Azure. L'esecuzione del Runbook viene eseguita usando un processo. Dopo l'esecuzione della raccolta dati, il flusso di lavoro delle app per la logica di Azure classifica i dati e, in base ai criteri di corrispondenza per il numero di prefissi di rete sopra o sotto una soglia predefine, invia le informazioni a un indirizzo di posta elettronica di destinazione.

### <a name="workflow"></a><a name="workflow"></a>Flusso di lavoro

La configurazione di un avviso personalizzato si basa su tre passaggi principali:

1. Creare un account di automazione con un account e le autorizzazioni "Esegui come".

2. Creare e configurare manuali operativi.

3. Creare un'app per la logica che invierà l'account di automazione e invierà un messaggio di posta elettronica di avviso se il numero è maggiore di una soglia (ad esempio, 160).

## <a name="before-you-begin"></a><a name="before"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare di soddisfare i criteri seguenti:

* Nella distribuzione è presente almeno un gateway ExpressRoute.

* Si ha una conoscenza di base degli [account RunAs](../automation/manage-runas-account.md) in automazione di Azure.

* Si ha familiarità con le app per la [logica di Azure](../logic-apps/logic-apps-overview.md).

* Si ha familiarità con l'uso di Azure PowerShell. Azure PowerShell è necessario per raccogliere i prefissi di rete nel gateway ExpressRoute. Per ulteriori informazioni su Azure PowerShell in generale, vedere la [documentazione di Azure PowerShell](/powershell/azure/?view=azps-4.1.0).

### <a name="notes-and-limitations"></a><a name="limitations"></a>Note e limitazioni

* L'avviso personalizzato illustrato in questo articolo è un componente aggiuntivo per ottimizzare il funzionamento e il controllo. Non è una sostituzione per gli avvisi nativi in ExpressRoute.
* La raccolta dei dati per i gateway ExpressRoute viene eseguita in background. Il runtime può essere più lungo del previsto. Per evitare l'accodamento dei processi, la ricorrenza del flusso di lavoro deve essere configurata correttamente.
* Le distribuzioni tramite script o modelli ARM potrebbero essere più veloci del trigger di allarme personalizzato. Questo potrebbe comportare un aumento del numero di prefissi di rete nel gateway ExpressRoute oltre il limite di 200 Route.

## <a name="create-and-configure-accounts"></a><a name="accounts"></a>Creare e configurare gli account

Quando si crea un account di automazione nella portale di Azure, viene creato automaticamente un account [RunAs](../automation/automation-security-overview.md#run-as-accounts) . Questo account esegue le azioni seguenti:

* Crea un'applicazione di Azure Active Directory (Azure AD) con un certificato autofirmato. L'account RunAs stesso dispone di un certificato che deve essere rinnovato per impostazione predefinita ogni anno.

* Consente di creare un account dell'entità servizio per l'applicazione in Azure AD.

* Assegna il ruolo Collaboratore (RBAC) nella sottoscrizione di Azure in uso. Questo ruolo gestisce le risorse Azure Resource Manager usando manuali operativi.

Per creare un account di automazione, è necessario disporre di privilegi e autorizzazioni. Per informazioni, vedere [autorizzazioni necessarie per creare un account di automazione](../automation/automation-create-standalone-account.md#permissions-required-to-create-an-automation-account).

### <a name="1-create-an-automation-account"></a><a name="about"></a>1. creare un account di automazione

Creare un account di automazione con le autorizzazioni di esecuzione. Per istruzioni, vedere [creare un account di automazione di Azure](../automation/automation-quickstart-create-account.md).

:::image type="content" source="./media/custom-route-alert-portal/create-account.png" alt-text="Aggiungi account di automazione" lightbox="./media/custom-route-alert-portal/create-account-expand.png":::

### <a name="2-assign-the-run-as-account-a-role"></a><a name="about"></a>2. assegnare un ruolo all'account RunAs

Per impostazione predefinita, il ruolo **collaboratore** viene assegnato all'entità servizio usata dall'account **RunAs** . È possibile fare in modo che il ruolo predefinito venga assegnato all'entità servizio oppure è possibile limitare le autorizzazioni assegnando un [ruolo](../role-based-access-control/built-in-roles.md) predefinito, ad esempio Reader, o un [ruolo personalizzato](../active-directory/users-groups-roles/roles-create-custom.md).

 Usare la procedura seguente per determinare il ruolo da assegnare all'entità servizio usata dall'account RunAs:

1. Passare all'account di Automazione. Passare a **Impostazioni account**, quindi selezionare **account RunAs**.

2. Selezionare **Roles (ruoli** ) per visualizzare la definizione di ruolo utilizzata.

   :::image type="content" source="./media/custom-route-alert-portal/run-as-account-permissions.png" alt-text="Aggiungi account di automazione":::

## <a name="create-and-configure-runbooks"></a><a name="runbooks"></a>Creare e configurare manuali operativi

### <a name="1-install-modules"></a><a name="install-modules"></a>1. installare i moduli

Per eseguire i cmdlet di PowerShell in manuali operativi di automazione di Azure, è necessario installare altri Azure PowerShell AZ modules. Per installare i moduli, attenersi alla procedura seguente:

1. Aprire l'account di automazione di Azure e passare a **moduli**.

   :::image type="content" source="./media/custom-route-alert-portal/navigate-modules.png" alt-text="Aggiungi account di automazione":::

2. Eseguire una ricerca nella raccolta e importare i moduli seguenti: **AZ. Accounts**, **AZ. Network**, **AZ. Automation**e **AZ. profile**.

   :::image type="content" source="./media/custom-route-alert-portal/import-modules.png" alt-text="Aggiungi account di automazione" lightbox="./media/custom-route-alert-portal/import-modules-expand.png":::
  
### <a name="2-create-a-runbook"></a><a name="create"></a>2. creare un Runbook

1. Per creare il Runbook di PowerShell, passare all'account di automazione. In **automazione processi**selezionare il riquadro **manuali operativi** , quindi selezionare **Crea un Runbook**.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook.png" alt-text="Aggiungi account di automazione":::

2. Selezionare **Crea** per creare il Runbook.

   :::image type="content" source="./media/custom-route-alert-portal/create-runbook-2.png" alt-text="Aggiungi account di automazione":::

3. Selezionare il Runbook appena creato, quindi fare clic su **modifica**.

   :::image type="content" source="./media/custom-route-alert-portal/edit-runbook.png" alt-text="Aggiungi account di automazione":::

4. In **modifica**incollare lo script di PowerShell. Lo [script di esempio](#script) può essere modificato e usato per monitorare i gateway ExpressRoute in uno o più gruppi di risorse.

   Nello script di esempio, si notino le impostazioni seguenti:

    * La matrice **$rgList** contiene l'elenco dei gruppi di risorse con gateway ExpressRoute. È possibile personalizzare i gateway ExpressRoute basati su elenco.
    * La variabile **$thresholdNumRoutes** definire la soglia per il numero di prefissi di rete annunciati da un gateway ExpressRoute per le reti locali.

#### <a name="example-script"></a><a name="script"></a>Script di esempio

```powershell
  ################# Input parameters #################
# Resource Group Name where the ExR GWs resides in
$rgList= @('ASH-Cust10-02','ASH-Cust30')  
$thresholdNumRoutes = 160
###################################################

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process | Out-Null

Try {

   $conn = Get-AutomationConnection -Name 'AzureRunAsConnection'
   while(!($connectionResult) -And ($logonAttempt -le 5))
   {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =  Connect-AzAccount `
                               -ServicePrincipal `
                               -ApplicationId $conn.ApplicationId `
                               -Tenant $conn.TenantId `
                               -CertificateThumbprint $conn.CertificateThumbprint `
                               -Subscription $conn.SubscriptionId `
                               -Environment AzureCloud 
                               
        Start-Sleep -Seconds 10
    }
} Catch {
    if (!$conn)
    {
        $ErrorMessage = "Service principal not found."
        throw $ErrorMessage
    } 
    else
    {
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Get the name of the Azure subscription
$subscriptionName=(Get-AzSubscription -SubscriptionId $conn.SubscriptionId).Name

#write-Output "<br>$(Get-Date) - selection of the Azure subscription: $subscriptionName" 
Select-AzSubscription -SubscriptionId $conn.SubscriptionId | Out-Null


$GtwList = @()
$results= @()

foreach ($rgName in $rgList)
{
## Collect all the ExpressRoute gateways in a Resource Group
$GtwList=Get-AzVirtualNetworkGateway -ResourceGroupName $rgName 

## For each ExpressRoute gateway, get the IP addresses of the BGP peers and collect the number of routes advertised 
foreach ($gw in $GtwList) {
  
  $peers = Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName


 if ($peers[0].State -eq 'Connected') {
   $routes1=$null
   $routes1 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[0].Neighbor
 }
  if ($peers[1].State -eq 'Connected') {
  
   $routes2=$null
   $routes2 = Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName $gw.Name -ResourceGroupName $rgName -Peer $peers[1].Neighbor
 }
 
  $sampleTime=(Get-Date).ToString("dd-MM-yyyy HH:mm:ss")
  if ($routes1.Count -eq $routes2.Count)
  {
     
     if ($routes1.Count -lt $thresholdNumRoutes){
       $status='OK'
       $alertMsg='number of routes below threshold'
     } 
     else {
       $status='ALERT'
       $alertMsg='number of routes above threshold'
     }
  } 
  else
  {
     $status='WARNING'
     $alertMsg='check ER Gateway'
  }
  
  $obj = [psCustomObject]@{
            resourceGroup =$rgName
            nameGtw  = $gw.Name
            peer1 = $peers[0].Neighbor
            peer2 = $peers[1].Neighbor
            numRoutesPeer1=  $routes1.Count
            numRoutesPeer2=  $routes2.Count
            time=$sampleTime
            status=$status
            alertMessage = $alertMsg
        }
  $results += $obj
} ### end foreach gateways in each resource group
} ### end foreach resource group
$jsonResults= ConvertTo-Json $results -Depth 100
Write-Output  $jsonResults
 
 ```

### <a name="3-save-and-publish-the-runbook"></a><a name="publish"></a>3. salvare e pubblicare il Runbook

1. Selezionare **Save (Salva** ) per salvare una copia bozza del Runbook.
2. Selezionare **pubblica** per pubblicare il Runbook come versione ufficiale di Runbook nell'account di automazione.

   :::image type="content" source="./media/custom-route-alert-portal/save-publish-runbook.png" alt-text="Aggiungi account di automazione":::

Quando si esegue lo script di PowerShell, viene raccolto un elenco di valori:
 
* Gruppo di risorse

* Nome del gateway ExpressRoute

* Indirizzo IP del primo peer BGP (Peer1)

* Indirizzo IP del secondo peer BGP (Peer2)

* Numero di prefissi di rete annunciati dal gateway ExpressRoute al primo peer BGP (Peer1)

* Numero di prefissi di rete annunciati dal gateway ExpressRoute al secondo peer BGP (Peer2)

* Timestamp

* Stato, classificato come:

  * ' OK ' se il numero di route è inferiore a un valore soglia
  * ' ALERT ' se il numero di route supera un valore di soglia
  * ' Avviso ' se il numero di prefissi di rete annunciato per i due peer BGP è diverso

* Messaggio di avviso, per una descrizione dettagliata dello stato (OK, avviso, avviso)

Lo script di PowerShell converte le informazioni raccolte in un output JSON. Runbook usa il cmdlet di PowerShell [Write-Output](/powershell/module/Microsoft.PowerShell.Utility/Write-Output)  come flusso di output per comunicare le informazioni al client.

### <a name="4-validate-the-runbook"></a><a name="validate"></a>4. convalidare il Runbook

Una volta creato, il Runbook deve essere convalidato. Selezionare **Start** e controllare l'output e gli errori per i diversi flussi del processo.

:::image type="content" source="./media/custom-route-alert-portal/validate-runbook.png" alt-text="Aggiungi account di automazione" lightbox="./media/custom-route-alert-portal/validate-runbook-expand.png":::

## <a name="create-and-configure-a-logic-app"></a><a name="logic"></a>Creare e configurare un'app per la logica

App per la logica di Azure è l'agente di orchestrazione di tutti i processi di raccolta e azioni. Nelle sezioni seguenti si compila un flusso di lavoro usando un'app per la logica.

### <a name="workflow"></a>Flusso di lavoro

In questo flusso di lavoro si compila un'app per la logica che monitora regolarmente i gateway ExpressRoute. Se sono presenti nuovi elementi, l'app per la logica invia un messaggio di posta elettronica per ogni elemento. Al termine, a livello generale l'app per la logica dovrebbe avere un flusso di lavoro simile al seguente:

:::image type="content" source="./media/custom-route-alert-portal/logic-apps-workflow.png" alt-text="Aggiungi account di automazione":::

### <a name="1-create-a-logic-app"></a>1. creare un'app per la logica

In **progettazione app**per la logica creare un'app per la logica usando il modello di app per la **logica vuota** . Per i passaggi, vedere [creare app](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app)per la logica.

:::image type="content" source="./media/custom-route-alert-portal/blank-template.png" alt-text="Aggiungi account di automazione":::

### <a name="2-add-a-trigger"></a>2. aggiungere un trigger

Ogni app per la logica viene avviata da un trigger. Un trigger viene attivato quando si verifica un evento specifico o quando viene soddisfatta una determinata condizione. Ogni volta che il trigger viene attivato, il motore di App per la logica di Azure crea un'istanza dell'app per la logica che avvia ed esegue il flusso di lavoro.

Per eseguire regolarmente un'app per la logica basata su una pianificazione temporale predefinita, aggiungere il valore predefinito **ricorrenza: pianificazione** al flusso di lavoro. Nella casella di ricerca digitare **Schedule**. Selezionare **Trigger**. Dall'elenco trigger selezionare **pianificazione ricorrenza**.

:::image type="content" source="./media/custom-route-alert-portal/schedule.png" alt-text="Aggiungi account di automazione":::

Nel trigger di pianificazione della ricorrenza è possibile impostare il fuso orario e una ricorrenza per la ripetizione del flusso di lavoro. La combinazione di intervallo e frequenza consente di definire la pianificazione per il trigger dell'app per la logica. Per stabilire una frequenza di ricorrenza minima ragionevole, considerare i fattori seguenti:

* Il completamento dello script di PowerShell nel runbook di automazione richiede tempo. Il runtime dipende dal numero di gateway ExpressRoute da monitorare. Una frequenza di ricorrenza troppo breve provocherà l'accodamento processi.

* Lo script di PowerShell viene eseguito come processo in background. Non viene avviato immediatamente; viene eseguito dopo un ritardo variabile.

* Una frequenza di ricorrenza troppo breve genererà un carico non necessario nei gateway ExpressRoute di Azure.

Al termine della configurazione del flusso di lavoro, è possibile verificare la coerenza della frequenza di ricorrenza eseguendo il flusso di lavoro alcune volte, quindi verificando il risultato nella **Cronologia esecuzioni**.

:::image type="content" source="./media/custom-route-alert-portal/recurrence.png" alt-text="Aggiungi account di automazione" lightbox="./media/custom-route-alert-portal/recurrence-expand.png":::

### <a name="3-create-a-job"></a><a name="job"></a>3. creare un processo

Un'app per la logica accede ad altre app, servizi e la piattaforma, sebbene i connettori. Il passaggio successivo di questo flusso di lavoro consiste nel selezionare un connettore per accedere all'account di automazione di Azure definito in precedenza.

1. Nella **finestra di progettazione di app**per la logica, sotto **ricorrenza**, selezionare **nuovo passaggio**. In **scegliere un'azione** e la casella di ricerca selezionare **tutti**.
2. Nella casella di ricerca digitare automazione e ricerca di **Azure** . Selezionare **Crea processo**. **Create Job** verrà usato per attivare il Runbook di automazione creato in precedenza.

   :::image type="content" source="./media/custom-route-alert-portal/create-job.png" alt-text="Aggiungi account di automazione":::

3. Accedere con un'entità servizio. È possibile usare un'entità servizio esistente oppure è possibile crearne una nuova. Per creare una nuova entità servizio, vedere [come usare il portale per creare un'entità servizio Azure ad in grado di accedere alle risorse](../active-directory/develop/howto-create-service-principal-portal.md). Selezionare **Connetti con entità servizio**.

   :::image type="content" source="./media/custom-route-alert-portal/sign-in.png" alt-text="Aggiungi account di automazione":::

4. Digitare un **nome di connessione**, aggiungere l'ID **client** (ID applicazione), il **segreto client**e l' **ID tenant**. Scegliere quindi **Create** (Crea).

   :::image type="content" source="./media/custom-route-alert-portal/connect-service-principal.png" alt-text="Aggiungi account di automazione" nel gruppo di **risorse** che ospita l'account di automazione e l'operatore processo di automazione nell' **account di automazione**. Inoltre, verificare di aver aggiunto il **nome Runbook** come nuovo parametro.

   :::image type="content" source="./media/custom-route-alert-portal/roles.png" alt-text="Aggiungi account di automazione" lightbox="./media/custom-route-alert-portal/roles-expand.png":::

### <a name="4-get-the-job-output"></a><a name="output"></a>4. ottenere l'output del processo

1. Selezionare **Nuovo passaggio**. Cercare "automazione di Azure". Nell'elenco **azioni** selezionare **Ottieni output del processo**.

   :::image type="content" source="./media/custom-route-alert-portal/get-output.png" alt-text="Aggiungi account di automazione":::

2. Nella pagina **ottenere l'output del processo** specificare le informazioni necessarie per accedere all'account di automazione. Selezionare la **sottoscrizione, il gruppo di risorse**e l' **account di automazione** che si vuole usare. Fare clic all'interno della casella **ID processo** . Quando viene visualizzato l'elenco di **contenuto dinamico** , selezionare **ID processo**.

   :::image type="content" source="./media/custom-route-alert-portal/job-id.png" alt-text="Aggiungi account di automazione" lightbox="./media/custom-route-alert-portal/job-id-expand.png":::

### <a name="5-parse-the-json"></a><a name="parse"></a>5. analizzare il codice JSON

Le informazioni contenute nell'output dell'azione ' Crea processo di automazione di Azure ' (passaggi precedenti) generano un oggetto JSON. L' **analisi JSON** di app per la logica è un'azione incorporata per creare token semplici dalle proprietà e i relativi valori nel contenuto JSON. È quindi possibile usare tali proprietà nel flusso di lavoro.

1. Aggiungere un'azione. Nell' **azione Ottieni output processo->** selezionare **nuovo passaggio**.
2. Nella casella di ricerca **scegliere un'azione** Digitare "parse JSON" per cercare i connettori che offrono questa azione. Nell'elenco **azioni** selezionare l'azione **analizza JSON** per le operazioni sui dati che si desidera utilizzare.

   :::image type="content" source="./media/custom-route-alert-portal/parse-json.png" alt-text="Aggiungi account di automazione":::

3. Fare clic all'interno della casella **contenuto** . Quando viene visualizzato l'elenco contenuto dinamico, selezionare **contenuto**.

   :::image type="content" source="./media/custom-route-alert-portal/content.png" alt-text="Aggiungi account di automazione" lightbox="./media/custom-route-alert-portal/content-expand.png":::

4. Per l'analisi di un file JSON è necessario uno schema. Lo schema può essere generato usando l'output del Runbook di automazione. Aprire una nuova sessione del Web browser, eseguire il Runbook di automazione e acquisire l'output. Tornare all'azione **analizza operazioni dati JSON in app** per la logica. Nella parte inferiore della pagina selezionare **USA payload di esempio per generare lo schema**.

   :::image type="content" source="./media/custom-route-alert-portal/sample-payload.png" alt-text="Aggiungi account di automazione":::

5. Per **immettere o incollare un payload JSON di esempio**, incollare l'output del Runbook di automazione e selezionare **fine**.

   :::image type="content" source="./media/custom-route-alert-portal/paste-payload.png" alt-text="Aggiungi account di automazione" lightbox="./media/custom-route-alert-portal/paste-payload-expand.png":::

6. Uno schema viene generato automaticamente analizzando il payload di input JSON.

   :::image type="content" source="./media/custom-route-alert-portal/generate-schema.png" alt-text="Aggiungi account di automazione" lightbox="./media/custom-route-alert-portal/generate-schema-expand.png":::

### <a name="6-define-and-initialize-a-variable"></a><a name="define-variable"></a>6. definire e inizializzare una variabile

In questo passaggio del flusso di lavoro viene creata una condizione per inviare un allarme tramite posta elettronica. Per una formattazione flessibile e personalizzata del messaggio del corpo di un messaggio di posta elettronica, nel flusso di lavoro viene introdotta una variabile ausiliaria.

1. Nell' **azione Ottieni output processo**selezionare **nuovo passaggio**. Nella casella di ricerca trovare e selezionare le **variabili**.

   :::image type="content" source="./media/custom-route-alert-portal/variables.png" alt-text="Aggiungi account di automazione":::

2. Nell'elenco **azioni** selezionare l'azione **Inizializza variabile** .

   :::image type="content" source="./media/custom-route-alert-portal/initialize-variables.png" alt-text="Aggiungi account di automazione":::

3. Specificare il nome della variabile. Per **tipo**selezionare **stringa**. Il **valore** della variabile verrà assegnato in un secondo momento nel flusso di lavoro.

   :::image type="content" source="./media/custom-route-alert-portal/string.png" alt-text="Aggiungi account di automazione" lightbox="./media/custom-route-alert-portal/string-expand.png":::

### <a name="7-create-a-for-each-action"></a><a name="cycles-json"></a>7. creare un'azione "for each"

Una volta analizzato il codice JSON, l'azione **analizza operazioni dati JSON** archivia il contenuto nell'output del *corpo* . Per elaborare l'output, è possibile creare un ciclo "for each" ripetendo una o più azioni per ogni elemento nella matrice.

1. In **Inizializza variabile**selezionare **Aggiungi un'azione**. Nella casella di ricerca digitare "for each" come filtro.

   :::image type="content" source="./media/custom-route-alert-portal/control.png" alt-text="Aggiungi account di automazione":::

2. Nell'elenco **azioni** selezionare l'azione **per ogni controllo**.

   :::image type="content" source="./media/custom-route-alert-portal/for-each.png" alt-text="Aggiungi account di automazione":::

3. Fare clic nella casella di testo **selezionare un output dai passaggi precedenti** . Quando viene visualizzato l'elenco di **contenuto dinamico** , selezionare il **corpo**, che è l'output del JSON analizzato.

   :::image type="content" source="./media/custom-route-alert-portal/body.png" alt-text="Aggiungi account di automazione":::

4. Per ogni elemento del corpo JSON, è necessario impostare una condizione. Dal gruppo azione selezionare **controllo**.

   :::image type="content" source="./media/custom-route-alert-portal/condition-control.png" alt-text="Aggiungi account di automazione":::

5. Nell'elenco **azioni** selezionare **Condition-Control**. Il Condition-Control è una struttura di controllo che confronta i dati nel flusso di lavoro con valori o campi specifici. È quindi possibile specificare azioni diverse che vengono eseguite in base al fatto che i dati soddisfino o meno la condizione.

   :::image type="content" source="./media/custom-route-alert-portal/condition.png" alt-text="Aggiungi account di automazione":::

6. Nell'azione della **condizione** radice, modificare l'operazione della logica in **o**.

   :::image type="content" source="./media/custom-route-alert-portal/condition-or.png" alt-text="Aggiungi account di automazione" lightbox="./media/custom-route-alert-portal/condition-or-expand.png":::

7. Controllare il valore per il numero di prefissi di rete che un gateway ExpressRoute annuncia ai due peer BGP. Il numero di route è disponibile in "numRoutePeer1" e "numRoutePeer2" nel **contenuto dinamico**. Nella casella valore digitare il valore per **numRoutePeer1**.

   :::image type="content" source="./media/custom-route-alert-portal/peer-1.png" alt-text="Aggiungi account di automazione":::

8. Per aggiungere un'altra riga alla condizione, scegliere **Aggiungi-> Aggiungi riga**. Nella seconda casella, da **contenuto dinamico**, selezionare **numRoutePeer2**.

   :::image type="content" source="./media/custom-route-alert-portal/peer-2.png" alt-text="Aggiungi account di automazione":::

9. La condizione logica è true quando una delle due variabili dinamiche, numRoute1 o numRoute2, è maggiore della soglia. In questo esempio, la soglia è fissata a 160 (80% del valore massimo di 200 Route). È possibile modificare il valore di soglia in base ai propri requisiti. Per coerenza, il valore deve corrispondere allo stesso valore usato nello script di PowerShell Runbook.

   :::image type="content" source="./media/custom-route-alert-portal/logic-condition.png" alt-text="Aggiungi account di automazione":::

10. In **Se true**, formattare e creare le azioni per inviare l'avviso tramite posta elettronica. In * * scegliere un'azione, cercare e selezionare le **variabili**.

    :::image type="content" source="./media/custom-route-alert-portal/condition-if-true.png" alt-text="Aggiungi account di automazione":::

11. In variabili selezionare **Aggiungi un'azione**. Nell'elenco **azioni** selezionare **Imposta variabile**.

    :::image type="content" source="./media/custom-route-alert-portal/condition-set-variable.png" alt-text="Aggiungi account di automazione":::

12. In **nome**selezionare la variabile denominata **EmailBody** creata in precedenza. Per **valore**, incollare lo script HTML necessario per formattare il messaggio di posta elettronica di avviso. Usare il **contenuto dinamico** per includere i valori del corpo JSON. Dopo aver configurato queste impostazioni, il risultato è che la variabile **emailBody** contiene tutte le informazioni relative all'avviso, in formato HTML.

    :::image type="content" source="./media/custom-route-alert-portal/paste-script.png" alt-text="Aggiungi account di automazione":::

### <a name="8-add-the-email-connector"></a><a name="email"></a>8. aggiungere il connettore di posta elettronica

App per la logica fornisce molti connettori di posta elettronica. In questo esempio viene aggiunto un connettore Outlook per inviare l'avviso tramite posta elettronica. In **Imposta variabile**selezionare **Aggiungi un'azione**. In **scegliere un'azione**Digitare "Invia messaggio di posta elettronica" nella casella di ricerca.

1. Selezionare **Office 365 Outlook**.

   :::image type="content" source="./media/custom-route-alert-portal/email.png" alt-text="Aggiungi account di automazione":::

2. Nell'elenco **azioni** selezionare **Invia un messaggio di posta elettronica (v2)**.

   :::image type="content" source="./media/custom-route-alert-portal/email-v2.png" alt-text="Aggiungi account di automazione":::

3. Eseguire l'accesso per creare una connessione a Office 365 Outlook.

   :::image type="content" source="./media/custom-route-alert-portal/office-365.png" alt-text="Aggiungi account di automazione":::

4. Nel campo **corpo** fare clic su **Aggiungi contenuto dinamico**. Dal pannello del contenuto dinamico aggiungere la variabile **emailBody**. Compilare i campi **oggetto** e **a** .

   :::image type="content" source="./media/custom-route-alert-portal/emailbody.png" alt-text="Aggiungi account di automazione":::

5. L'azione **Invia un messaggio di posta elettronica (v2)** completa l'installazione del flusso di lavoro.

   :::image type="content" source="./media/custom-route-alert-portal/send-email-v2.png" alt-text="Aggiungi account di automazione" lightbox="./media/custom-route-alert-portal/send-email-v2-expand.png":::

### <a name="9-workflow-validation"></a><a name="validation"></a>9. convalida del flusso di lavoro

Il passaggio finale è la convalida del flusso di lavoro. In **Panoramica di app**per la logica selezionare **Esegui trigger**. Selezionare **ricorrenza**. Il flusso di lavoro può essere monitorato e verificato nella **Cronologia esecuzioni**.

:::image type="content" source="./media/custom-route-alert-portal/trigger.png" alt-text="Aggiungi account di automazione":::

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come personalizzare il flusso di lavoro, vedere app per la [logica di Azure](../logic-apps/logic-apps-overview.md).