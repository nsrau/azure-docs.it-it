---
title: Creare ruoli personalizzati di controllo degli accessi in base al ruolo e assegnarli a utenti interni ed esterni in Azure | Microsoft Docs
description: Assegnare ruoli personalizzati di controllo degli accessi in base al ruolo creati con PowerShell e l'interfaccia della riga di comando per utenti interni ed esterni
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: kgremban
ms.assetid: ''
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: d2eb39aa0a3fda7b543b6989cda937559f4d09ea
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="intro-on-role-based-access-control"></a>Introduzione al controllo degli accessi in base al ruolo

La funzionalità di controllo degli accessi in base al ruolo, disponibile solo nel portale di Azure, consente ai proprietari di una sottoscrizione di assegnare ruoli granulari ad altri utenti, i quali possono gestire ambiti di risorse specifici nel proprio ambiente.

Il controllo degli accessi in base al ruolo consente una migliore gestione della sicurezza nelle organizzazioni di grandi dimensioni e nelle piccole e medie imprese che si avvalgono di collaboratori esterni, fornitori o freelance che hanno necessità di accedere a risorse specifiche nell'ambiente, ma non necessariamente all'intera infrastruttura o agli ambiti correlati alla fatturazione. Il controllo degli accessi in base al ruolo consente la flessibilità di possedere una sottoscrizione di Azure gestita dall'account di amministratore (ruolo di amministratore del servizio a livello di sottoscrizione) e di invitare più utenti a usare la stessa sottoscrizione ma senza i diritti amministrativi. Dal punto di vista della gestione e della fatturazione, la funzionalità di controllo degli accessi in base al ruolo è senza dubbio un'opzione molto efficiente in termini di gestione e tempo per l'uso di Azure in vari scenari.

## <a name="prerequisites"></a>prerequisiti
L'uso del controllo degli accessi in base al ruolo nell'ambiente di Azure richiede:

* Una sottoscrizione di Azure autonoma assegnata all'utente come proprietario (ruolo di sottoscrizione)
* Il ruolo di proprietario della sottoscrizione di Azure
* L'accesso al [portale di Azure](https://portal.azure.com)
* Verificare che i provider di risorse seguenti siano registrati per la sottoscrizione dell'utente: **Microsoft.Authorization**. Per altre informazioni su come registrare i provider di risorse, vedere [Provider, aree, versioni API e schemi di Resource Manager](../azure-resource-manager/resource-manager-supported-services.md).

> [!NOTE]
> Gli abbonamenti di Office 365 o le licenze di Azure Active Directory, ad esempio Accesso ad Azure Active Directory, di cui viene effettuato il provisioning dall'interfaccia di amministrazione di Office 365 non danno diritto all'uso del controllo degli accessi in base al ruolo.

## <a name="how-can-rbac-be-used"></a>Modalità d'uso del controllo degli accessi in base al ruolo
Il controllo degli accessi in base al ruolo può essere applicato in tre ambiti diversi in Azure. Dal livello più alto al più basso, gli ambiti sono i seguenti:

* Sottoscrizione (più alto)
* Gruppo di risorse
* Ambito delle risorse (più basso) che fornisce autorizzazioni mirate per un singolo ambito di risorse di Azure

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>Assegnare i ruoli di controllo degli accessi in base al ruolo all'ambito della sottoscrizione
Ecco due esempi comuni di uso del controllo degli accessi in base al ruolo:

* Utenti esterni alle organizzazioni (non inclusi nel tenant di Azure Active Directory dell'utente amministratore) invitati a gestire risorse specifiche o l'intera sottoscrizione
* Collaborazione con utenti all'interno dell'organizzazione (inclusi nel tenant di Azure Active Directory dell'utente), ma appartenenti a team o gruppi diversi che necessitano di un accesso granulare all'intera sottoscrizione o a determinati ambiti o gruppi di risorse nell'ambiente

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Concedere l'accesso a livello di sottoscrizione per un utente all'esterno di Azure Active Directory
I ruoli RBAC possono essere concessi solo dai **proprietari** della sottoscrizione. Pertanto, l'amministratore deve eseguire l'accesso con un account utente a cui è preassegnato questo ruolo o che ha creato la sottoscrizione di Azure.

Dopo avere eseguito l'accesso come amministratore dal portale di Azure, selezionare "Sottoscrizioni" e scegliere quella desiderata.
![Pannello delle sottoscrizioni nel portale di Azure](./media/role-assignments-external-users/0.png) Per impostazione predefinita, se l'utente amministratore ha acquistato la sottoscrizione di Azure, l'utente verrà visualizzato come **amministratore dell'account**, vale a dire con il ruolo di sottoscrizione. Per altre informazioni sui ruoli di sottoscrizione di Azure, vedere [Aggiungere o modificare i ruoli di amministratore di Azure che gestiscono la sottoscrizione o i servizi](../billing/billing-add-change-azure-subscription-administrator.md).

In questo esempio l'utente "alflanigan@outlook.com" è **Proprietario** della sottoscrizione nella "versione di valutazione gratuita" nel tenant AAD "Default tenant Azure". Poiché questo utente ha creato la sottoscrizione di Azure con l'account Microsoft iniziale "Outlook" (account Microsoft = Outlook, Live e così via), il nome di dominio predefinito per tutti gli altri utenti aggiunti in questo tenant sarà **"@alflaniganuoutlook.onmicrosoft.com"**. Per impostazione predefinita, la sintassi del nuovo dominio è costituita dalla combinazione di nome utente e nome dominio dell'utente che ha creato il tenant con l'aggiunta dell'estensione **".onmicrosoft.com"**.
Gli utenti possono anche accedere con un nome di dominio personalizzato nel tenant dopo averlo aggiunto e verificato per il nuovo tenant. Per altre informazioni su come verificare un nome di dominio personalizzato in un tenant di Azure Active Directory, vedere [Aggiungere un nome di dominio personalizzato alla directory](/active-directory/active-directory-add-domain).

In questo esempio la directory "Default tenant Azure" contiene solo gli utenti con il nome di dominio "@alflanigan.onmicrosoft.com".

Dopo avere selezionato la sottoscrizione, l'utente amministratore deve fare clic su **Controllo di accesso (IAM)** e quindi su **Aggiungi nuovo ruolo**.





![funzione IAM di controllo di accesso nel portale di Azure](./media/role-assignments-external-users/1.png)





![aggiungere un nuovo utente nella funzione IAM di controllo di accesso nel portale di Azure](./media/role-assignments-external-users/2.png)

Il passaggio successivo consiste nel selezionare il ruolo da assegnare e l'utente a cui verrà assegnato il ruolo di controllo degli accessi in base al ruolo. Nel menu a discesa **Ruolo** l'utente amministratore vede solo i ruoli predefiniti di controllo degli accessi in base al ruolo disponibili in Azure. Per altre descrizioni di ogni ruolo e dei relativi ambiti assegnabili, vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](built-in-roles.md).

L'utente amministratore deve quindi aggiungere l'indirizzo e-mail dell'utente esterno. Per l'utente esterno il comportamento previsto è quello di non essere visibile nel tenant esistente. Dopo che è stato invitato, l'utente esterno sarà visibile in **Sottoscrizioni > Controllo di accesso (IAM)** con tutti gli utenti correnti assegnati attualmente a un ruolo di controllo degli accessi in base al ruolo nell'ambito della sottoscrizione.





![aggiungere autorizzazioni al nuovo ruolo di controllo degli accessi in base al ruolo](./media/role-assignments-external-users/3.png)





![elenco di ruoli di controllo degli accessi in base al ruolo a livello di sottoscrizione](./media/role-assignments-external-users/4.png)

L'utente "chessercarlton@gmail.com" è stato inviato ad essere un **proprietario** per la sottoscrizione nella "versione di valutazione gratuita". Dopo avere inviato l'invito, l'utente esterno riceverà una conferma tramite posta elettronica con un collegamento di attivazione.
![Invito tramite posta elettronica per il ruolo di controllo degli accessi in base al ruolo](./media/role-assignments-external-users/5.png)

Essendo esterno all'organizzazione, il nuovo utente non dispone degli attributi esistenti nella directory "Default tenant Azure". Questi verranno creati previo consenso dell'utente esterno alla registrazione nella directory associata alla sottoscrizione per cui gli è stato assegnato un ruolo.





![messaggio di invito tramite posta elettronica per il ruolo di controllo degli accessi in base al ruolo](./media/role-assignments-external-users/6.png)

L'utente esterno diventa visibile nel tenant di Azure Active Directory da questo momento in poi come utente esterno e può essere visualizzato nel portale di Azure.





![pannello utenti azure active directory portale di Azure](./media/role-assignments-external-users/7.png)



Nella visualizzazione **Utenti**, gli utenti esterni sono riconoscibili dal tipo di icona diverso nel portale di Azure.

Tuttavia, la concessione dell'accesso come **Proprietario** o **Collaboratore** a un utente esterno nell'ambito della **sottoscrizione**, non consente l'accesso alla directory dell'utente amministratore, a meno che ciò non sia consentito dall'opzione di **amministrazione globale**. Nelle proprietà dell'utente è possibile identificare il **tipo di utente**, che dispone di due parametri comuni, **Membro** e **Guest**. Un membro è un utente registrato nella directory, mentre un utente guest è un utente invitato nella directory da un'origine esterna. Per altre informazioni, vedere [Procedura per aggiungere utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](../active-directory/active-directory-b2b-admin-add-users.md).

> [!NOTE]
> Assicurarsi che, dopo avere immesso le credenziali nel portale, l'utente esterno selezioni la directory corretta a cui accedere. Lo stesso utente può avere accesso a più directory e selezionarne una facendo clic sul nome utente nella parte superiore destra nel portale di Azure e quindi scegliere la directory appropriata nell'elenco a discesa.

Pur essendo un utente guest nella directory, l'utente esterno può gestire tutte le risorse per la sottoscrizione di Azure, ma non è in grado di accedere alla directory.





![accesso limitato ad Azure Active Directory nel portale di Azure](./media/role-assignments-external-users/9.png)

Azure Active Directory e una sottoscrizione di Azure non hanno una relazione padre-figlio come quella che hanno altre risorse di Azure, ad esempio le macchine virtuali, le reti virtuali, le app Web, le risorse di archiviazione e così via, con una sottoscrizione di Azure. Tutte queste ultime vengono create, gestite e fatturate in una sottoscrizione di Azure, mentre una sottoscrizione di Azure viene usata per gestire l'accesso a una directory di Azure. Per altre informazioni, vedere [Come associare una sottoscrizione di Azure ad Azure AD](/active-directory/active-directory-how-subscriptions-associated-directory).

Da tutti i ruoli predefiniti di controllo degli accessi in base al ruolo, **Proprietario** e **Collaboratore** offrono l'accesso completo a livello di gestione a tutte le risorse nell'ambiente, ma il ruolo Collaboratore non può creare ed eliminare nuovi ruoli di controllo degli accessi in base al ruolo. Altri ruoli predefiniti, come **Collaboratore Macchina Virtuale**, offrono l'accesso completo a livello di gestione solo alle risorse indicate dal nome, indipendentemente dal **gruppo di risorse** in cui sono state create.

L'assegnazione del ruolo predefinito di controllo degli accessi in base al ruolo **Collaboratore Macchina Virtuale** a livello di sottoscrizione implica che all'utente a cui è stato assegnato il ruolo seguente:

* Può visualizzare tutte le macchine virtuali indipendentemente dalla data di distribuzione e dai gruppi di risorse di appartenenza
* Ha accesso completo a livello di gestione alle macchine virtuali nella sottoscrizione
* Non può visualizzare gli altri tipi di risorse nella sottoscrizione
* Non può applicare modifiche dalla prospettiva della fatturazione

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Assegnare un ruolo predefinito di controllo degli accessi in base al ruolo a un utente esterno
Per uno scenario diverso in questo test, l'utente esterno "alflanigan@gmail.com" viene aggiunto come **Collaboratore Macchina virtuale**.




![ruolo predefinito collaboratore macchina virtuale](./media/role-assignments-external-users/11.png)

Il comportamento normale per questo utente esterno con questo ruolo predefinito è visualizzare e gestire solo le macchine virtuali e solo le risorse di Resource Manager adiacenti necessarie durante la distribuzione. Per impostazione predefinita, questi ruoli limitati consentono l'accesso solo alle risorse corrispondenti create nel portale di Azure.



![Panoramica del ruolo Collaboratore Macchina virtuale nel portale di Azure](./media/role-assignments-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Concedere l'accesso a livello di sottoscrizione per un utente nella stessa directory
Il flusso del processo è identico all'aggiunta di un utente esterno sia dal punto di vista dell'amministratore che concede il ruolo di controllo degli accessi in base al ruolo sia dal punto di vista dell'utente a cui viene concesso l'accesso al ruolo. La differenza è che l'utente invitato non riceverà gli inviti tramite posta elettronica e tutti gli ambiti di risorse nella sottoscrizione saranno disponibili nel dashboard dopo l'accesso.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Assegnare ruoli di controllo degli accessi in base al ruolo nell'ambito del gruppo di risorse
L'assegnazione di un ruolo di controllo degli accessi in base al ruolo nell'ambito del **gruppo di risorse** prevede un processo identico per l'assegnazione del ruolo a livello di sottoscrizione per entrambi i tipi di utenti: esterni o interni (appartenenti alla stessa directory). Gli utenti a cui viene assegnato il ruolo Controllo degli accessi in base al ruolo vedono nel proprio ambiente solo il gruppo di risorse per cui è stato loro assegnato l'accesso dall'icona **Gruppi di risorse** nel portale di Azure.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Assegnare ruoli di controllo degli accessi in base al ruolo nell'ambito delle risorse
Il processo di assegnazione di un ruolo di controllo degli accessi in base al ruolo nell'ambito delle risorse in Azure è identico per l'assegnazione del ruolo a livello di sottoscrizione o di gruppo di risorse, seguendo lo stesso flusso di lavoro per entrambi gli scenari. Anche in questo caso, gli utenti assegnati al ruolo Controllo degli accessi in base al ruolo possono vedere solo gli elementi per cui è stato loro assegnato l'accesso nella scheda **Tutte le risorse** o direttamente nel dashboard.

Un aspetto importante per il controllo degli accessi in base al ruolo nell'ambito del gruppo di risorse o delle risorse è che gli utenti devono eseguire l'accesso alla directory corretta.





![accesso alla directory nel portale di Azure](./media/role-assignments-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Assegnare ruoli di controllo degli accessi in base al ruolo per un gruppo di Azure Active Directory
Tutti gli scenari che usano il controllo degli accessi in base al ruolo nei tre ambiti diversi in Azure offrono il privilegio di gestione, distribuzione e amministrazione di varie risorse come utente assegnato, senza la necessità di gestire una sottoscrizione personale. Indipendentemente dal fatto che venga assegnato il ruolo di controllo degli accessi in base al ruolo nell'ambito di una sottoscrizione, di un gruppo di risorse o delle risorse, tutte le risorse create successivamente dagli utenti assegnati vengono fatturate per la sottoscrizione di Azure a cui gli utenti hanno accesso. In questo modo gli utenti con autorizzazioni di amministratore per la fatturazione per l'intera sottoscrizione di Azure hanno una panoramica completa sul consumo, indipendentemente da chi gestisce le risorse.

Per le organizzazioni di dimensioni maggiori, i ruoli di controllo degli accessi in base al ruolo possono essere applicati allo stesso modo per i gruppi di Azure Active Directory considerando la prospettiva che l'utente amministratore intenda concedere l'accesso granulare a team o a interi reparti, non singolarmente per ogni utente; si tratta quindi di un'opzione molto efficiente in termini di gestione e tempo. Per illustrare questo esempio, il ruolo **Collaboratore** è stato aggiunto a uno dei gruppi nel tenant a livello di sottoscrizione.





![aggiungere il ruolo di controllo degli accessi in base al ruolo per i gruppi AAD](./media/role-assignments-external-users/14.png)

Questi gruppi sono gruppi di sicurezza di cui viene eseguito il provisioning e la gestione solo all'interno di Azure Active Directory.

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-powershell"></a>Creare un ruolo personalizzato di controllo degli accessi in base al ruolo per aprire richieste di supporto usando PowerShell
I ruoli predefiniti disponibili in Azure assicurano determinati livelli di autorizzazione in base alle risorse disponibili nell'ambiente. Tuttavia, se i ruoli predefiniti non soddisfano le specifiche esigenze, è possibile creare ruoli personalizzati.

Per creare un ruolo personalizzato, è possibile iniziare con un ruolo predefinito, modificarlo e quindi creare un nuovo ruolo. Per questo esempio, il ruolo predefinito **Lettore** è stato personalizzato per consentire all'utente la possibilità di aprire richieste di supporto.

In PowerShell usare il comando [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) per esportare il ruolo **Lettore** in formato JSON.

```powershell
Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json
```

Di seguito è riportato l'output JSON per il ruolo Lettore.

```json
{
    "Name":  "Reader",
    "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "IsCustom":  false,
    "Description":  "Lets you view everything, but not make any changes.",
    "Actions":  [
                    "*/read"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Successivamente, si modifica l'output JSON per creare il ruolo personalizzato.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/subscriptions/11111111-1111-1111-1111-111111111111"
                         ]
}
```

Un ruolo tipico è composto da tre sezioni principali, **Actions**, **NotActions** e **AssignableScopes**.

Nella sezione **Actions** vengono elencate tutte le operazioni consentite per questo ruolo. In questo caso, per creare ticket di supporto, è necessario aggiungere l'operazione **Microsoft.Support/&ast;**. È importante tenere presente che ogni operazione viene resa disponibile da un provider di risorse. Per ottenere un elenco delle operazioni per un provider di risorse, è possibile usare il comando [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) oppure vedere [Azure Resource Manager Resource Provider operations](resource-provider-operations.md) (Operazioni dei provider di risorse di Azure Resource Manager).

Per limitare tutte le azioni per un ruolo specifico, i provider di risorse sono elencati nella sezione **NotActions**.
È obbligatorio che il ruolo contenga gli ID di sottoscrizione espliciti in cui viene usato. Gli ID di sottoscrizione sono elencati in **AssignableScopes**. In caso contrario non sarà possibile importare il ruolo nella sottoscrizione.

Per creare il ruolo personalizzato, usare il comando [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) e specificare il file di definizione del ruolo JSON aggiornato.

```powershell
New-AzureRmRoleDefinition -InputFile "C:\rbacrole2.json"
```

In questo esempio, il nome per questo ruolo personalizzato è "Reader support tickets access level" (Livello di accesso Lettore ticket supporto). Consente all'utente di visualizzare tutti gli elementi nella sottoscrizione e anche di aprire richieste di supporto.

> [!NOTE]
> Gli unici due ruoli predefiniti che consentono a un utente di aprire richieste di supporto sono **Proprietario** e **Collaboratore**. Per consentire a un utente di aprire richieste di supporto, è necessario che gli sia stato assegnato un ruolo nell'ambito della sottoscrizione, perché tutte le richieste di supporto vengono create in base a una sottoscrizione di Azure.

Il nuovo ruolo personalizzato è ora disponibile nel portale di Azure e può essere assegnato agli utenti.

![Screenshot del ruolo personalizzato importato nel portale di Azure](./media/role-assignments-external-users/18.png)

![Screenshot dell'assegnazione del ruolo importato personalizzato all'utente nella stessa directory](./media/role-assignments-external-users/19.png)

![Screenshot delle autorizzazioni per il ruolo importato personalizzato](./media/role-assignments-external-users/20.png)

Gli utenti con questo ruolo personalizzato possono ora creare nuove richieste di supporto.

![Screenshot del ruolo personalizzato che crea richieste di supporto](./media/role-assignments-external-users/21.png)

Gli utenti con questo ruolo personalizzato non possono eseguire altre azioni, ad esempio creare macchine virtuali o creare gruppi di risorse.

![Screenshot del ruolo personalizzato che non può creare macchine virtuali](./media/role-assignments-external-users/22.png)

![Screenshot del ruolo personalizzato che non può creare nuovi gruppi di risorse](./media/role-assignments-external-users/23.png)

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-azure-cli"></a>Creare un ruolo personalizzato di controllo degli accessi in base al ruolo per aprire richieste di supporto usando l'interfaccia della riga di comando di Azure

I passaggi per creare un ruolo personalizzato usando l'interfaccia della riga di comando di Azure sono simili alla procedura con PowerShell, ad eccezione del fatto che l'output JSON è diverso.

Per questo esempio, è possibile iniziare con il ruolo predefinito **Lettore**. Per elencare le azioni del ruolo Lettore, usare il comando [az role definition list](/cli/azure/role/definition#az_role_definition_list).

```azurecli
az role definition list --name "Reader" --output json
```

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "notActions": []
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Creare un file JSON con il formato seguente. L'operazione **Microsoft.Support/&ast;** è stata aggiunta nella sezione **Actions** in modo che questo utente possa aprire richieste di supporto pur continuando a essere un lettore. È necessario aggiungere l'ID sottoscrizione in cui verrà usato questo ruolo nella sezione **AssignableScopes**.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes": [
                            "/subscriptions/11111111-1111-1111-1111-111111111111"
                        ]
}
```

Per creare il ruolo personalizzato, usare il comando [az role definition create](/cli/azure/role/definition#az_role_definition_create).

```azurecli
az role definition create --role-definition ~/roles/rbacrole1.json
```

Il nuovo ruolo personalizzato è ora disponibile nel portale di Azure e il processo per usare questo ruolo è uguale a quello descritto nella sezione precedente per PowerShell.

![Screenshot del portale di Azure del ruolo personalizzato creato usando l'interfaccia della riga di comando 1.0](./media/role-assignments-external-users/26.png)
