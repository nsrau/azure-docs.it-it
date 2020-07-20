---
title: Abilitare Azure Active Directory Domain Services usando un modello | Microsoft Docs
description: Informazioni su come configurare e abilitare Azure Active Directory Domain Services usando un modello di Azure Resource Manager.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: 9a9518eb4c8635275b9cbf0467f3091eca10f647
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223007"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>Creare un dominio gestito di Azure Active Directory Domain Services usando un modello di Azure Resource Manager

Azure Active Directory Domain Services (Azure AD DS) offre servizi di dominio gestiti, come l'aggiunta a un dominio, Criteri di gruppo, LDAP e l'autenticazione Kerberos/NTLM, completamente compatibili con Windows Server Active Directory. È possibile utilizzare questi servizi di dominio senza distribuire, gestire e applicare patch manualmente ai controller di dominio. Azure AD DS si integra con il tenant di Azure AD esistente. Questa integrazione consente agli utenti di accedere con le proprie credenziali aziendali ed è possibile usare i gruppi e gli account utente esistenti per proteggere l'accesso alle risorse.

Questo articolo descrive come creare un dominio gestito con un modello di Azure Resource Manager. Le risorse di supporto vengono create con Azure PowerShell.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessarie le risorse seguenti:

* Installare e configurare Azure PowerShell.
    * Se necessario, seguire le istruzioni nell'articolo per [installare il modulo Azure PowerShell e connettersi alla sottoscrizione di Azure](/powershell/azure/install-az-ps).
    * Assicurarsi di accedere alla sottoscrizione di Azure usando il cmdlet [Connect-AzAccount][Connect-AzAccount].
* Installare e configurare Azure AD PowerShell.
    * Se necessario, seguire le istruzioni nell'articolo per [installare il modulo Azure AD PowerShell e connettersi ad Azure AD](/powershell/azure/active-directory/install-adv2).
    * Assicurarsi di accedere al tenant di Azure usando il cmdlet [Connect-AzureAD][Connect-AzureAD].
* Per abilitare Azure AD DS, sono necessari privilegi di *amministratore globale* nel tenant di Azure AD.
* Per creare le risorse di Azure AD DS richieste, sono necessari privilegi di *collaboratore* nella sottoscrizione di Azure.

## <a name="dns-naming-requirements"></a>Requisiti per la denominazione DNS

Quando si crea un dominio gestito di Azure AD DS, si specifica un nome DNS. Di seguito sono riportate alcune considerazioni per la scelta di questo nome DNS:

* **Nome di dominio predefinito:** per impostazione predefinita, viene usato il nome di dominio predefinito della directory, con il suffisso *.onmicrosoft.com*. Se si vuole abilitare l'accesso LDAP sicuro al dominio gestito tramite Internet, non è possibile creare un certificato digitale per proteggere la connessione con il dominio predefinito. Microsoft è proprietaria del dominio *.onmicrosoft.com*, quindi un'autorità di certificazione (CA) pubblica non emetterà un certificato.
* **Nomi di dominio personalizzati:** l'approccio più comune è quello di specificare un nome di dominio personalizzato, in genere uno di cui si è già proprietari ed è instradabile. Se si usa un dominio personalizzato instradabile, il traffico può fluire correttamente in base alle esigenze per supportare le applicazioni.
* **Suffissi di dominio non instradabili:** è in genere consigliabile evitare un suffisso del nome di dominio non instradabile, ad esempio *contoso.local*. Il suffisso *.local* non è instradabile e può causare problemi con la risoluzione DNS.

> [!TIP]
> Se si crea un nome di dominio personalizzato, prestare attenzione agli spazi dei nomi DNS esistenti. È consigliabile usare un nome di dominio separato da uno spazio dei nomi DNS locale o di Azure esistente.
>
> Se ad esempio lo spazio dei nomi DNS esistente è *contoso.com*, creare un dominio gestito con il nome di dominio personalizzato *aaddscontoso.com*. Se occorre usare il protocollo LDAP sicuro, è necessario eseguire la registrazione ed essere il proprietario del nome di dominio personalizzato per generare i certificati necessari.
>
> Potrebbe essere necessario creare alcuni record DNS aggiuntivi per altri servizi nell'ambiente in uso o server di inoltro DNS condizionali tra gli spazi dei nomi DNS esistenti nell'ambiente corrente. Ad esempio, se si esegue un server Web che ospita un sito con il nome DNS radice, possono essere presenti conflitti di denominazione che richiedono voci DNS aggiuntive.
>
> In questo esempio e in queste guide pratiche a titolo di esempio si usa il dominio personalizzato *aadds.contoso.com*. In tutti i comandi specificare il proprio nome di dominio.

Si applicano anche le seguenti restrizioni relative ai nomi DNS:

* **Limitazioni dei prefissi di dominio:** non è possibile creare un dominio gestito con un prefisso più lungo di 15 caratteri. Il prefisso del nome di dominio specificato (ad esempio, *aaddscontoso* nel nome di dominio *aaddscontoso.com*) deve contenere un massimo di 15 caratteri.
* **Conflitti nei nomi di rete:** il nome di dominio DNS per il dominio gestito non deve essere già presente nella rete virtuale. In particolare, verificare i seguenti scenari che potrebbero causare un conflitto di nomi:
    * È già presente un dominio di Active Directory con lo stesso nome di dominio DNS nella rete virtuale.
    * La rete virtuale in cui si intende abilitare il dominio gestito ha una connessione VPN alla rete locale. In questo caso, verificare che non sia presente un dominio con lo stesso nome di dominio DNS nella rete locale.
    * Esiste un servizio cloud di Azure con lo stesso nome della rete virtuale di Azure.

## <a name="create-required-azure-ad-resources"></a>Creare le risorse di Azure AD necessarie

Azure AD Domain Services richiede un'entità servizio e un gruppo di Azure AD. Queste risorse consentono al dominio gestito di sincronizzare i dati e di definire quali utenti hanno autorizzazioni amministrative nel dominio gestito.

Per iniziare, registrare il provider di risorse Azure Active Directory Domain Services usando il cmdlet [Register-AzResourceProvider][Register-AzResourceProvider]:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Creare un'entità servizio di Azure AD per Azure AD Domain Services per comunicare ed eseguire l'autenticazione usando il cmdlet [New-AzureADServicePrincipal][New-AzureADServicePrincipal]. Viene usato un ID applicazione specifico denominato *Domain Controller Services* con ID *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Non modificare questo ID applicazione.

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Ora creare un gruppo di Azure AD denominato *AAD DC Administrators* usando il cmdlet [New-AzureADGroup][New-AzureADGroup]. Agli utenti aggiunti a questo gruppo vengono concesse le autorizzazioni per eseguire attività di amministrazione nel dominio gestito.

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Dopo aver creato il gruppo *AAD DC Administrators*, aggiungere un utente al gruppo usando il cmdlet [Add-AzureADGroupMember][Add-AzureADGroupMember]. È prima necessario ottenere l'ID oggetto del gruppo *AAD DC Administrators* usando il cmdlet [Get-AzureADGroup][Get-AzureADGroup], quindi l'ID oggetto dell'utente desiderato usando il cmdlet [Get-AzureADUser][Get-AzureADUser].

Nell'esempio seguente, l'ID oggetto utente per l'account con nome dell'entità utente `admin@contoso.onmicrosoft.com`. Sostituire questo account utente con il nome dell'entità utente dell'utente che si vuole aggiungere al gruppo *AAD DC Administrators*:

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

Creare infine un gruppo di risorse con il cmdlet [New-AzResourceGroup][New-AzResourceGroup]. Nell'esempio seguente viene creato un gruppo di risorse denominato *myResourceGroup* nell'area *westus*. Usare il proprio nome e l'area desiderata:

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

Se si sceglie un'area che supporta le zone di disponibilità, le risorse di Azure AD DS vengono distribuite in più zone per garantire maggiore ridondanza. Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Per garantire la resilienza sono presenti almeno tre zone separate in tutte le aree abilitate.

Non è necessario eseguire alcuna operazione di configurazione per distribuire Azure AD DS in più zone. La piattaforma Azure gestisce automaticamente la distribuzione delle risorse nelle zone. Per altre informazioni e per consultare la disponibilità delle aree, vedere [Informazioni sulle zone di disponibilità di Azure][availability-zones].

## <a name="resource-definition-for-azure-ad-ds"></a>Definizione della risorsa per Azure AD Domain Services

Come parte della definizione della risorsa Resource Manager, sono necessari i parametri di configurazione seguenti:

| Parametro               | valore |
|-------------------------|---------|
| domainName              | Immettere un nome di dominio DNS per il dominio gestito, prendendo in considerazione i punti precedenti sui prefissi e sui conflitti di denominazione. |
| filteredSync            | Azure AD DS consente di sincronizzare *tutti* gli utenti e i gruppi disponibili in Azure AD oppure di eseguire una sincronizzazione *con ambito* solo di gruppi specifici.<br /><br /> Per altre informazioni sulla sincronizzazione con ambito, vedere [Sincronizzazione con ambito in Azure AD Domain Services][scoped-sync].|
| notificationSettings    | Se sono stati generati avvisi nel dominio gestito, è possibile inviare notifiche tramite posta elettronica. <br /><br />Gli *amministratori globali* del tenant di Azure e i membri del gruppo *AAD DC Administrators* possono essere *Abilitati* per queste notifiche.<br /><br /> Se si desidera, è possibile aggiungere altri destinatari per le notifiche quando sono presenti avvisi che richiedono attenzione.|
| domainConfigurationType | Per impostazione predefinita, un dominio gestito viene creato come foresta *Utente*. Questo tipo di foresta sincronizza tutti gli oggetti di Azure AD, inclusi tutti gli account utente creati in un ambiente AD DS locale. Non è necessario specificare un valore per *domainConfiguration* per creare una foresta di utenti.<br /><br /> Una foresta *Risorsa* sincronizza solo gli utenti e i gruppi creati direttamente in Azure AD. Le foreste Risorsa sono attualmente disponibili in anteprima. Impostare il valore su *ResourceTrusting* per creare una foresta di risorse.<br /><br />Per altre informazioni sulle foreste *Risorsa*, inclusi i motivi per cui usarle e come creare trust tra foreste con domini di AD DS locali, vedere [Panoramica delle foreste di risorse di Azure AD DS][resource-forests].|

La definizione dei parametri condensati seguente mostra come vengono dichiarati questi valori. Viene creata una foresta utente denominata *aaddscontoso.com* con tutti gli utenti di Azure AD sincronizzati con il dominio gestito:

```json
"parameters": {
    "domainName": {
        "value": "aaddscontoso.com"
    },
    "filteredSync": {
        "value": "Disabled"
    },
    "notificationSettings": {
        "value": {
            "notifyGlobalAdmins": "Enabled",
            "notifyDcAdmins": "Enabled",
            "additionalRecipients": []
        }
    },
    [...]
}
```

Il tipo di risorsa del modello di Resource Manager ridotto viene quindi usato per definire e creare il dominio gestito. Devono essere presenti un rete virtuale di Azure e una subnet di Azure oppure è necessario crearle come parte del modello di Resource Manager. Il dominio gestito è connesso a questa subnet.

```json
"resources": [
    {
        "apiVersion": "2017-06-01",
        "type": "Microsoft.AAD/DomainServices",
        "name": "[parameters('domainName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
            "domainName": "[parameters('domainName')]",
            "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
            "filteredSync": "[parameters('filteredSync')]",
            "notificationSettings": "[parameters('notificationSettings')]"
        }
    },
    [...]
]
```

Questi parametri e tipo di risorsa possono essere usati come parte di un modello di Resource Manager più ampio per distribuire un dominio gestito, come illustrato nella sezione seguente.

## <a name="create-a-managed-domain-using-sample-template"></a>Creare un dominio gestito con un modello di esempio

Il seguente esempio completo di modello di Resource Manager crea un dominio gestito insieme alle regole del gruppo di sicurezza di rete, alla subnet e alla rete virtuale di supporto. Le regole del gruppo di sicurezza di rete sono necessarie per proteggere il dominio gestito e verificare che il flusso del traffico funzioni correttamente. Viene creata una foresta di utenti con il nome DNS *aaddscontoso.com* con tutti gli utenti sincronizzati da Azure AD:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "apiVersion": {
            "value": "2017-06-01"
        },
        "domainConfigurationType": {
            "value": "FullySynced"
        },
        "domainName": {
            "value": "aaddscontoso.com"
        },
        "filteredSync": {
            "value": "Disabled"
        },
        "location": {
            "value": "westus"
        },
        "notificationSettings": {
            "value": {
                "notifyGlobalAdmins": "Enabled",
                "notifyDcAdmins": "Enabled",
                "additionalRecipients": []
            }
        },
        "subnetName": {
            "value": "aadds-subnet"
        },
        "vnetName": {
            "value": "aadds-vnet"
        },
        "vnetAddressPrefixes": {
            "value": [
                "10.1.0.0/24"
            ]
        },
        "subnetAddressPrefix": {
            "value": "10.1.0.0/24"
        },
        "nsgName": {
            "value": "aadds-nsg"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-06-01",
            "type": "Microsoft.AAD/DomainServices",
            "name": "[parameters('domainName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "domainName": "[parameters('domainName')]",
                "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
                "filteredSync": "[parameters('filteredSync')]",
                "domainConfigurationType": "[parameters('domainConfigurationType')]",
                "notificationSettings": "[parameters('notificationSettings')]"
            }
        },
        {
            "type": "Microsoft.Network/NetworkSecurityGroups",
            "name": "[parameters('nsgName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "AllowSyncWithAzureAD",
                        "properties": {
                            "access": "Allow",
                            "priority": 101,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "443"
                        }
                    },
                    {
                        "name": "AllowPSRemoting",
                        "properties": {
                            "access": "Allow",
                            "priority": 301,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "5986"
                        }
                    },
                    {
                        "name": "AllowRD",
                        "properties": {
                            "access": "Allow",
                            "priority": 201,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "CorpNetSaw",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "3389"
                        }
                    }
                ]
            },
            "apiVersion": "2018-04-01"
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('location')]",
            "apiVersion": "2018-04-01",
            "dependsOn": [
                "[concat('Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": "[parameters('vnetAddressPrefixes')]"
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetAddressPrefix')]",
                            "networkSecurityGroup": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
                            }
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {}
}
```

Questo modello può essere distribuito usando il metodo di distribuzione preferito, ad esempio il [portale di Azure][portal-deploy], [Azure PowerShell][powershell-deploy] o una pipeline di integrazione continua e distribuzione continua (CI/CD). Nell'esempio seguente viene usato il cmdlet [New-AzResourceGroupDeployment][New-AzResourceGroupDeployment]. Specificare il nome del gruppo di risorse e il nome file del modello:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

Sono necessari alcuni minuti per creare la risorsa e restituire il controllo al prompt di PowerShell. Il provisioning del dominio gestito continua a essere eseguito in background e il completamento della distribuzione può richiedere fino a un'ora. Nel portale di Azure la pagina **Panoramica** per il dominio gestito mostra lo stato corrente di questa fase di distribuzione.

Quando il portale di Azure indica che il provisioning del dominio gestito è stato completato, è necessario eseguire le attività seguenti:

* Aggiornare le impostazioni DNS per la rete virtuale, in modo che le macchine virtuali possano trovare il dominio gestito per l'autenticazione o l'aggiunta al dominio.
    * Per configurare il DNS, selezionare il dominio gestito nel portale. Nella finestra **Panoramica** viene richiesto di configurare automaticamente queste impostazioni DNS.
* [Abilitare la sincronizzazione password in Azure Active Directory Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) in modo che gli utenti finali possano accedere al dominio gestito con le credenziali aziendali.

## <a name="next-steps"></a>Passaggi successivi

Per vedere il dominio gestito in azione, è possibile [aggiungere a un dominio una macchina virtuale Windows][windows-join], [configurare LDAP sicuro][tutorial-ldaps] e [configurare la sincronizzazione degli hash delle password][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[availability-zones]: ../availability-zones/az-overview.md
[portal-deploy]: ../azure-resource-manager/templates/deploy-portal.md
[powershell-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[scoped-sync]: scoped-synchronization.md
[resource-forests]: concepts-resource-forest.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain
[New-AzResourceGroupDeployment]: /powershell/module/Az.Resources/New-AzResourceGroupDeployment
