---
title: Creare e gestire gruppi di azione nel portale di Azure
description: Informazioni su come creare e gestire gruppi di azione nel portale di Azure.
author: dkamstra
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 17e01844463b6d18bd7d2c3b56ee86d938682b8e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95536320"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Creare e gestire gruppi di azione nel portale di Azure
Un gruppo di azioni è una raccolta delle preferenze di notifica definite dal proprietario di una sottoscrizione di Azure. Gli avvisi di Monitoraggio di Azure e di integrità dei servizi usano gruppi di azioni per notificare agli utenti l'attivazione di un avviso. I vari avvisi possono usare lo stesso gruppo di azioni o gruppi di azioni diversi, a seconda delle esigenze dell'utente. In una sottoscrizione è possibile configurare fino a 2000 gruppi di azioni.

Questo articolo illustra come creare e gestire gruppi di azione nel portale di Azure.

Ogni azione è composta dalle seguenti proprietà:

* **Tipo**: la notifica o l'azione eseguita. Gli esempi includono l'invio di una chiamata vocale, un SMS o un messaggio di posta elettronica oppure l'attivazione di vari tipi di azioni automatizzate. Vedere i tipi più avanti in questo articolo.
* **Name**: un identificatore univoco all'interno del gruppo di azioni.
* **Dettagli**: i dettagli corrispondenti che variano in base al *tipo*.

Per informazioni sull'uso dei modelli di Azure Resource Manager per configurare i gruppi di azione: [Modelli di Resource Manager per il gruppo di azione](./action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Creare un gruppo di azione usando il portale di Azure

1. Nel [portale di Azure](https://portal.azure.com) cercare e selezionare **Monitoraggio**. Il riquadro **Monitoraggio** consolida tutte le impostazioni e i dati di monitoraggio in una vista.

1. Selezionare **avvisi**, quindi fare clic su **Gestisci azioni**.

    ![Pulsante Gestisci azioni](./media/action-groups/manage-action-groups.png)
    
1. Selezionare **Aggiungi gruppo di azione** e compilare i campi pertinenti nell'esperienza della procedura guidata.

    ![Comando "Aggiungi gruppo di azione"](./media/action-groups/add-action-group.PNG)

### <a name="configure-basic-action-group-settings"></a>Configurare le impostazioni del gruppo di azioni di base

In **Dettagli progetto**:

Selezionare la **sottoscrizione** e il **gruppo di risorse** in cui viene salvato il gruppo di azioni.

In **Dettagli istanza**:

1. Immettere un **nome** per il gruppo di azioni.

1. Immettere un **nome visualizzato**. Il nome visualizzato viene usato al posto di un nome completo del gruppo di azioni quando le notifiche vengono inviate usando questo gruppo.

      ![Finestra di dialogo "Aggiungi gruppo di azione"](./media/action-groups/action-group-1-basics.png)


### <a name="configure-notifications"></a>Configurare le notifiche

1. Fare clic sul pulsante **Avanti: notifiche >** per passare alla scheda **notifiche** oppure selezionare la scheda **notifiche** nella parte superiore della schermata.

1. Consente di definire un elenco di notifiche da inviare quando viene attivato un avviso. Fornire quanto segue per ogni notifica:

    a. **Tipo di notifica**: selezionare il tipo di notifica che si vuole inviare. Le opzioni disponibili sono:
      * Invia un messaggio di posta elettronica al ruolo Azure Resource Manager-invia un messaggio di posta elettronica agli utenti assegnati a determinati ruoli ARM di livello sottoscrizione.
      * Posta elettronica/SMS/push/Voice: inviare questi tipi di notifiche a destinatari specifici.
    
    b. **Nome**: immettere un nome univoco per la notifica.

    c. **Dettagli**: in base al tipo di notifica selezionato, immettere un indirizzo di posta elettronica, un numero di telefono e così via.
    
    d. **Schema di avviso comune**: è possibile scegliere di abilitare lo [schema di avviso comune](./alerts-common-schema.md), che fornisce il vantaggio di avere un singolo payload degli avvisi estendibile e unificato per tutti i servizi di avviso in Monitoraggio di Azure.

    ![Scheda notifiche](./media/action-groups/action-group-2-notifications.png)
    
### <a name="configure-actions"></a>Configurare le azioni

1. Fare clic sul pulsante **Avanti: azioni >** per passare alla scheda **azioni** oppure selezionare la scheda **azioni** nella parte superiore della schermata.

1. Definire un elenco di azioni da attivare quando viene attivato un avviso. Per ogni azione, specificare quanto segue:

    a. **Tipo di azione**: selezionare Runbook di automazione, funzione di Azure, ITSM, app per la logica, webhook sicuro, webhook.
    
    b. **Nome**: immettere un nome univoco per l'azione.

    c. **Dettagli**: in base al tipo di azione, immettere un URI del webhook, un'app di Azure, una connessione ITSM o Runbook di automazione. Per l'azione ITSM, specificare anche **Elemento di lavoro** e altri campi richiesti dallo strumento ITSM.
    
    d. **Schema di avviso comune**: è possibile scegliere di abilitare lo [schema di avviso comune](./alerts-common-schema.md), che fornisce il vantaggio di avere un singolo payload degli avvisi estendibile e unificato per tutti i servizi di avviso in Monitoraggio di Azure.
    
    ![Scheda azioni](./media/action-groups/action-group-3-actions.png)

### <a name="create-the-action-group"></a>Creare il gruppo di azione

1. Volendo è possibile esplorare le impostazioni di **Tag**. Ciò consente di associare le coppie chiave/valore al gruppo di azioni per la categorizzazione ed è una funzionalità disponibile per qualsiasi risorsa di Azure.

    ![Scheda Tag](./media/action-groups/action-group-4-tags.png)
    
1. Fare clic su **Rivedi e crea** per rivedere le impostazioni. Verrà eseguita una convalida rapida degli input per assicurarsi che tutti i campi obbligatori siano selezionati. Se sono presenti problemi, verranno segnalati qui. Dopo aver esaminato le impostazioni, fare clic su **Crea** per eseguire il provisioning del gruppo di azioni.
    
    ![Scheda verifica + crea](./media/action-groups/action-group-5-review.png)

> [!NOTE]
> Quando si configura un'azione per inviare una notifica a un utente tramite posta elettronica o SMS, viene ricevuta una conferma che indica che sono stati aggiunti al gruppo di azioni.

## <a name="manage-your-action-groups"></a>Gestire i gruppi di azione

Dopo aver creato un gruppo di azioni, è possibile visualizzare **Gruppi di azioni** selezionando **Gestisci azioni** dalla pagina di destinazione **Avvisi** nel riquadro **Monitoraggio**. Selezionare il gruppo di azione da gestire per:

* Aggiungere, modificare o rimuovere azioni.
* Eliminare il gruppo di azione.

## <a name="action-specific-information"></a>Informazioni specifiche delle azioni

> [!NOTE]
> Vedere [Limiti del servizio di sottoscrizione per il monitoraggio](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-monitor-limits) per i limiti numerici per ognuno degli elementi seguenti.  

### <a name="automation-runbook"></a>Runbook di Automazione
Vedere i [Limiti del servizio per la sottoscrizione di Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) per i limiti sui payload di Runbook.

Un Gruppo di azioni può contenere un numero limitato di azioni di tipo Runbook. 

### <a name="azure-app-push-notifications"></a>Notifiche push dell'app Azure
Un Gruppo di azioni può contenere un numero limitato di azioni dell'app Azure.

### <a name="email"></a>Email
I messaggi di posta elettronica verranno inviati dagli indirizzi di posta elettronica seguenti. Verificare che il filtro della posta elettronica sia configurato correttamente
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Un Gruppo di azioni può contenere un numero limitato di azioni di posta elettronica. Vedere l'articolo relativo alle [informazioni sulla limitazione della frequenza](./alerts-rate-limiting.md).

### <a name="email-azure-resource-manager-role"></a>Invia un messaggio di posta elettronica al ruolo di Azure Resource Manager
Inviare il messaggio di posta elettronica ai membri del ruolo della sottoscrizione. Il messaggio di posta elettronica verrà inviato solo ai membri **utente di Azure AD** del ruolo. Il messaggio di posta elettronica non verrà inviato a gruppi di Azure AD o entità servizio.

Un messaggio di posta elettronica di notifica viene inviato solo all'indirizzo di *posta elettronica primario* .

Un Gruppo di azioni può contenere un numero limitato di azioni di posta elettronica. Vedere l'articolo relativo alle [informazioni sulla limitazione della frequenza](./alerts-rate-limiting.md).

### <a name="function"></a>Funzione
Chiama un endpoint di trigger HTTP esistente in [Funzioni di Azure](../../azure-functions/functions-create-first-azure-function.md#create-a-function-app).

Un Gruppo di azioni può contenere un numero limitato di funzioni.

### <a name="itsm"></a>Gestione dei servizi IT
L'azione ITSM richiede una connessione ITSM. Informazioni su come creare una [connessione ITSM](./itsmc-overview.md).

Un Gruppo di azioni può contenere un numero limitato di azioni di gestione dei servizi IT. 

### <a name="logic-app"></a>App per la logica
Un Gruppo di azioni può contenere un numero limitato di azioni dell'app per la logica.

### <a name="secure-webhook"></a>Webhook protetto

> [!NOTE]
> L'uso dell'azione webhook richiede che l'endpoint del webhook di destinazione non richieda i dettagli dell'avviso per funzionare correttamente o che sia in grado di analizzare le informazioni sul contesto dell'avviso fornite come parte dell'operazione POST. Se l'endpoint del webhook non è in grado di gestire le informazioni sul contesto dell'avviso in modo autonomo, è possibile usare una soluzione come un' [azione dell'app](./action-groups-logic-app.md) per la logica per una manipolazione personalizzata delle informazioni sul contesto degli avvisi in modo che corrispondano al formato dati previsto del webhook.

L'azione Webhook dei gruppi di azione consente di sfruttare Azure Active Directory per proteggere la connessione tra il gruppo di azioni e l'API Web protetta (endpoint webhook). Di seguito è descritto il flusso di lavoro generale per sfruttare questa funzionalità. Per una panoramica delle applicazioni Azure AD e delle entità servizio, vedere [Panoramica di Microsoft Identity Platform (v 2.0)](../../active-directory/develop/v2-overview.md).

1. Creare un'applicazione Azure AD per l'API Web protetta. Vedere [API Web protetta: registrazione dell'app](../../active-directory/develop/scenario-protected-web-api-app-registration.md).
    - Configurare l'API protetta affinché venga [chiamata da un'app daemon](../../active-directory/develop/scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).
    
2. Abilitare i gruppi di azioni per l'uso dell'applicazione Azure AD.

    > [!NOTE]
    > Per eseguire questo script, è necessario essere un membro del [ruolo di amministratore dell'applicazione Azure AD](../../active-directory/roles/permissions-reference.md#available-roles).
    
    - Modificare la chiamata a Connect-AzureAD dello script di PowerShell per usare l'ID tenant di Azure AD.
    - Modificare la variabile dello script di PowerShell $myAzureADApplicationObjectId per usare l'ID oggetto dell'applicazione Azure AD.
    - Eseguire lo script modificato.
    
3. Configurare l'azione del Webhook protetto del Gruppo di azioni.
    - Copiare il valore $myApp.ObjectId dallo script e immetterlo nel campo ID oggetto applicazione nella definizione dell'azione Webhook.
    
    ![Usare l'azione Webhook](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Script di PowerShell per il Webhook protetto

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>sms
Per altre informazioni importanti, vedere le [informazioni sulla limitazione della frequenza](./alerts-rate-limiting.md) e sul [comportamento degli avvisi SMS](./alerts-sms-behavior.md). 

Un Gruppo di azioni può contenere un numero limitato di azioni SMS.

> [!NOTE]
> Se l'interfaccia utente del gruppo di azioni portale di Azure non consente di selezionare il codice paese/regione, il servizio SMS non è supportato per il paese/regione.  Se il codice del paese/regione non è disponibile, è possibile votare se il paese/regione è stato aggiunto alla [voce utente](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice). Nel frattempo, una soluzione ovvia consiste nel fare in modo che il gruppo di azioni chiami un webhook a un provider di SMS di terze parti con supporto per il paese/regione.  

I prezzi per i paesi/regioni supportati sono elencati nella [pagina relativa ai prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).
  

### <a name="voice"></a>Chiamata vocale
Per altre informazioni importanti, vedere le [informazioni sulla limitazione della frequenza](./alerts-rate-limiting.md).

Un Gruppo di azioni può contenere un numero limitato di azioni Voce.

> [!NOTE]
> Se l'interfaccia utente del gruppo di azioni portale di Azure non consente di selezionare il codice paese/regione, le chiamate vocali non sono supportate per il paese/regione. Se il codice del paese/regione non è disponibile, è possibile votare se il paese/regione è stato aggiunto alla [voce utente](https://feedback.azure.com/forums/913690-azure-monitor/suggestions/36663181-add-more-country-codes-for-sms-alerting-and-voice).  Nel frattempo, una soluzione ovvia consiste nel fare in modo che il gruppo di azioni chiami un webhook a un provider di chiamate vocali di terze parti con supporto per il paese/regione.  

I prezzi per i paesi/regioni supportati sono elencati nella [pagina relativa ai prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="webhook"></a>webhook

> [!NOTE]
> L'uso dell'azione webhook richiede che l'endpoint del webhook di destinazione non richieda i dettagli dell'avviso per funzionare correttamente o che sia in grado di analizzare le informazioni sul contesto dell'avviso fornite come parte dell'operazione POST. Se l'endpoint del webhook non è in grado di gestire le informazioni sul contesto dell'avviso in modo autonomo, è possibile usare una soluzione come un' [azione dell'app](./action-groups-logic-app.md) per la logica per una manipolazione personalizzata delle informazioni sul contesto degli avvisi in modo che corrispondano al formato dati previsto del webhook.

I webhook vengono elaborati usando le regole seguenti
- Una chiamata al webhook viene tentata un massimo di 3 volte.
- La chiamata verrà ritentata se non viene ricevuta una risposta entro il periodo di timeout o viene restituito uno dei codici di stato HTTP seguenti: 408, 429, 503 o 504.
- La prima chiamata attenderà 10 secondi per una risposta.
- Il secondo e il terzo tentativo attendono 30 secondi per una risposta.
- Dopo che i 3 tentativi di chiamare il webhook non sono riusciti, nessun gruppo di azione chiamerà l'endpoint per 15 minuti.

Intervalli di indirizzi IP di origine:

 - 13.66.60.119/32
 - 13.66.143.220/30
 - 13.66.202.14/32
 - 13.66.248.225/32
 - 13.66.249.211/32
 - 13.67.10.124/30
 - 13.69.109.132/30
 - 13.71.199.112/30
 - 13.77.53.216/30
 - 13.77.172.102/32
 - 13.77.183.209/32
 - 13.78.109.156/30
 - 13.84.49.247/32
 - 13.84.51.172/32
 - 13.84.52.58/32
 - 13.86.221.220/30
 - 13.106.38.142/32
 - 13.106.38.148/32
 - 13.106.54.3/32
 - 13.106.54.19/32
 - 13.106.57.181/32
 - 13.106.57.196/31
 - 20.38.149.132/30
 - 20.42.64.36/30
 - 20.43.121.124/30
 - 20.44.17.220/30
 - 20.45.123.236/30
 - 20.72.27.152/30
 - 20.150.172.228/30
 - 20.192.238.124/30
 - 20.193.202.4/30
 - 40.68.195.137/32
 - 40.68.201.58/32
 - 40.68.201.65/32
 - 40.68.201.206/32
 - 40.68.201.211/32
 - 40.68.204.18/32
 - 40.115.37.106/32
 - 40.121.219.215/32
 - 40.121.221.62/32
 - 40.121.222.201/32
 - 40.121.223.186/32
 - 51.104.9.100/30
 - 52.183.20.244/32
 - 52.183.31.0/32
 - 52.183.94.59/32
 - 52.184.145.166/32
 - 191.233.50.4/30
 - 191.233.207.64/26
 - 2603:1000:4:402::178/125
 - 2603:1000:104:402::178/125
 - 2603:1010:6:402::178/125
 - 2603:1010:101:402::178/125
 - 2603:1010:304:402::178/125
 - 2603:1010:404:402::178/125
 - 2603:1020:5:402::178/125
 - 2603:1020:206:402::178/125
 - 2603:1020:305:402::178/125
 - 2603:1020:405:402::178/125
 - 2603:1020:605:402::178/125
 - 2603:1020:705:402::178/125
 - 2603:1020:805:402::178/125
 - 2603:1020:905:402::178/125
 - 2603:1020: A04:402:: 178/125
 - 2603:1020: B04:402:: 178/125
 - 2603:1020: C04:402:: 178/125
 - 2603:1020: D04:402:: 178/125
 - 2603:1020: E04:402:: 178/125
 - 2603:1020: F04:402:: 178/125
 - 2603:1020:1004:800:: F8/125
 - 2603:1020:1104:400::178/125
 - 2603:1030: f:400:: 978/125
 - 2603:1030:10:402::178/125
 - 2603:1030:104:402::178/125
 - 2603:1030:107:400:: F0/125
 - 2603:1030:210:402::178/125
 - 2603:1030:40B: 400:: 978/125
 - 2603:1030:40C: 402:: 178/125
 - 2603:1030:504:802:: F8/125
 - 2603:1030:608:402::178/125
 - 2603:1030:807:402::178/125
 - 2603:1030: A07:402:: 8f8/125
 - 2603:1030: B04:402:: 178/125
 - 2603:1030: C06:400:: 978/125
 - 2603:1030: F05:402:: 178/125
 - 2603:1030:1005:402::178/125
 - 2603:1040:5:402::178/125
 - 2603:1040:207:402::178/125
 - 2603:1040:407:402::178/125
 - 2603:1040:606:402::178/125
 - 2603:1040:806:402::178/125
 - 2603:1040:904:402::178/125
 - 2603:1040: A06:402:: 178/125
 - 2603:1040: B04:402:: 178/125
 - 2603:1040: C06:402:: 178/125
 - 2603:1040: D04:800:: F8/125
 - 2603:1040: F05:402:: 178/125
 - 2603:1040:1104:400::178/125
 - 2603:1050:6:402::178/125
 - 2603:1050:403:400:: 1F8/125

Per ricevere aggiornamenti sulle modifiche apportate a questi indirizzi IP, è consigliabile configurare un avviso di Integrità dei servizi che consente di monitorare l'eventuale presenza di notifiche informative sul servizio Gruppi di azioni.

Un Gruppo di azioni può contenere un numero limitato di azioni di tipo Webhook.

Gli aggiornamenti frequenti degli indirizzi IP di origine possono richiedere molto tempo nel webhook. L'uso di **tag di servizio** per *ActionGroup* consente di ridurre la complessità degli aggiornamenti frequenti agli indirizzi IP manualmente. I prefissi di intervallo di indirizzi IP di origine condivisi sopra sono gestiti automaticamente da Microsoft inclusi nel **tag di servizio**.

#### <a name="service-tag"></a>Tag del servizio
Un tag del servizio rappresenta un gruppo di prefissi di indirizzi IP di un determinato servizio di Azure. Microsoft gestisce i prefissi di indirizzo inclusi nel tag del servizio e aggiorna automaticamente il tag di servizio in base alla modifica degli indirizzi, riducendo al minimo la complessità degli aggiornamenti frequenti alle regole di sicurezza di rete per un ActionGroup.

1. In portale di Azure in servizi di Azure cercare il *gruppo di sicurezza di rete*.
2. Fare clic su **Aggiungi** e creare un gruppo di sicurezza di rete.

   1. Aggiungere il nome del gruppo di risorse e quindi immettere i *Dettagli dell'istanza*.
   1. Fare clic su **Verifica + crea** e quindi su *Crea*.
   
   :::image type="content" source="media/action-groups/action-group-create-security-group.png" alt-text="Esempio su come creare un gruppo di sicurezza di rete."border="true":::

3. Passare a gruppo di risorse e quindi fare clic sul *gruppo di sicurezza di rete* creato.

    1. Selezionare *regole di sicurezza in ingresso*.
    1. Fare clic su **Aggiungi**.
    
    :::image type="content" source="media/action-groups/action-group-add-service-tag.png" alt-text="Esempio su come aggiungere un tag di servizio."border="true":::

4. Nel riquadro destro viene aperta una nuova finestra.
    1.  Seleziona origine: **tag servizio**
    1.  Tag del servizio di origine: **ActionGroup**
    1.  Scegliere **Aggiungi**.
    
    :::image type="content" source="media/action-groups/action-group-service-tag.png" alt-text="Esempio su come aggiungere un tag di servizio."border="true":::

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul [Comportamento degli avvisi SMS](./alerts-sms-behavior.md).  
* Leggere le [informazioni sullo schema webhook degli avvisi del log attività](./activity-log-alerts-webhook.md).  
* Altre informazioni sul [connettore ITSM](./itsmc-overview.md).
* Altre informazioni sulla [limitazione della frequenza](./alerts-rate-limiting.md) degli avvisi.
* Leggere una [panoramica degli avvisi del log attività](./alerts-overview.md) e informazioni su come ricevere gli avvisi.  
* Informazioni su come [configurare gli avvisi ogni volta che viene inviata una notifica sull'integrità del servizio](../../service-health/alerts-activity-log-service-notifications-portal.md).
