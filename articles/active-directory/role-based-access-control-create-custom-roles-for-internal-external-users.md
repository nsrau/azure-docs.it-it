---
title: Creare ruoli personalizzati di controllo degli accessi in base al ruolo e assegnarli a utenti interni ed esterni in Azure | Microsoft Docs
description: Assegnare ruoli personalizzati di controllo degli accessi in base al ruolo creati con PowerShell e l'interfaccia della riga di comando per utenti interni ed esterni
services: active-directory
documentationcenter: 
author: andreicradu
manager: catadinu
editor: kgremban
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/10/2017
ms.author: a-crradu
ms.openlocfilehash: cccd0af0c991efe330567c2459717798d116e68f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
## <a name="intro-on-role-based-access-control"></a>Introduzione al controllo degli accessi in base al ruolo

La funzionalità di controllo degli accessi in base al ruolo, disponibile solo nel portale di Azure, consente ai proprietari di una sottoscrizione di assegnare ruoli granulari ad altri utenti, i quali possono gestire ambiti di risorse specifici nel proprio ambiente.

Il controllo degli accessi in base al ruolo consente una migliore gestione della sicurezza nelle organizzazioni di grandi dimensioni e nelle piccole e medie imprese che si avvalgono di collaboratori esterni, fornitori o freelance che hanno necessità di accedere a risorse specifiche nell'ambiente, ma non necessariamente all'intera infrastruttura o agli ambiti correlati alla fatturazione. Il controllo degli accessi in base al ruolo consente la flessibilità di possedere una sottoscrizione di Azure gestita dall'account di amministratore (ruolo di amministratore del servizio a livello di sottoscrizione) e di invitare più utenti a usare la stessa sottoscrizione ma senza i diritti amministrativi. Dal punto di vista della gestione e della fatturazione, la funzionalità di controllo degli accessi in base al ruolo è senza dubbio un'opzione molto efficiente in termini di gestione e tempo per l'uso di Azure in vari scenari.

## <a name="prerequisites"></a>Prerequisiti
L'uso del controllo degli accessi in base al ruolo nell'ambiente di Azure richiede:

* Una sottoscrizione di Azure autonoma assegnata all'utente come proprietario (ruolo di sottoscrizione)
* Il ruolo di proprietario della sottoscrizione di Azure
* L'accesso al [portale di Azure](https://portal.azure.com)
* Verificare che i provider di risorse seguenti siano registrati per la sottoscrizione dell'utente: **Microsoft.Authorization**. Per altre informazioni su come registrare i provider di risorse, vedere [Provider, aree, versioni API e schemi di Resource Manager](/azure-resource-manager/resource-manager-supported-services.md).
<!---Loc Comment: Link [Resource Manager providers, regions, API versions and schemas] is broken with an error message "404 - Content Not Found--->

> [!NOTE]
> Le sottoscrizioni di Office 365 o le licenze di Azure Active Directory, ad esempio l'accesso ad Azure Active Directory, fornite dal portale di Office 365 non danno diritto all'uso del controllo degli accessi in base al ruolo.

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
I ruoli di controllo degli accessi in base al ruolo possono essere concessi solo dai **proprietari** della sottoscrizione; l'utente amministratore deve essere registrato con un nome utente che ha preassegnato questo ruolo o che ha creato la sottoscrizione di Azure.

Dopo avere eseguito l'accesso come amministratore dal portale di Azure selezionare "Sottoscrizioni" e scegliere quella desiderata.
![Pannello delle sottoscrizioni nel portale di Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/0.png) Per impostazione predefinita, se l'utente amministratore ha acquistato la sottoscrizione di Azure, l'utente verrà visualizzato come **amministratore dell'account**, vale a dire con il ruolo di sottoscrizione. Per altri dettagli sui ruoli di sottoscrizione di Azure, vedere [Aggiungere o modificare i ruoli di amministratore di Azure che gestiscono la sottoscrizione o i servizi](/billing/billing-add-change-azure-subscription-administrator.md).

In questo esempio l'utente "alflanigan@outlook.com" è **Proprietario** della sottoscrizione nella "versione di valutazione gratuita" nel tenant AAD "Default tenant Azure". Poiché questo utente ha creato la sottoscrizione di Azure con l'account Microsoft iniziale "Outlook" (account Microsoft = Outlook, Live e così via), il nome di dominio predefinito per tutti gli altri utenti aggiunti in questo tenant sarà **"@alflaniganuoutlook.onmicrosoft.com"**. Per impostazione predefinita, la sintassi del nuovo dominio è costituita dalla combinazione di nome utente e nome dominio dell'utente che ha creato il tenant con l'aggiunta dell'estensione **".onmicrosoft.com"**.
Inoltre gli utenti possono accedere con un nome di dominio personalizzato nel tenant dopo averlo aggiunto e verificato per il nuovo tenant. Per altre informazioni su come verificare un nome di dominio personalizzato in un tenant di Azure Active Directory, vedere [Aggiungere un nome di dominio personalizzato alla directory](/active-directory/active-directory-add-domain).

In questo esempio la directory "Default tenant Azure" contiene solo gli utenti con il nome di dominio "@alflanigan.onmicrosoft.com".

Dopo avere selezionato la sottoscrizione, l'utente amministratore deve fare clic su **Controllo di accesso (IAM)** e quindi su **Aggiungi nuovo ruolo**.





![funzione IAM di controllo di accesso nel portale di Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/1.png)





![aggiungere un nuovo utente nella funzione IAM di controllo di accesso nel portale di Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/2.png)

Il passaggio successivo consiste nel selezionare il ruolo da assegnare e l'utente a cui verrà assegnato il ruolo di controllo degli accessi in base al ruolo. Nel menu a discesa **Ruolo** l'utente amministratore vede solo i ruoli predefiniti di controllo degli accessi in base al ruolo disponibili in Azure. Per altre descrizioni di ogni ruolo e dei relativi ambiti assegnabili, vedere [Ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](/active-directory/role-based-access-built-in-roles.md).
<!---Loc Comment: Link [Built-in roles for Azure Role-Based Access Control] is broken with an error message "404 - Content Not Found--->

L'utente amministratore deve quindi aggiungere l'indirizzo e-mail dell'utente esterno. Per l'utente esterno il comportamento previsto è quello di non essere visibile nel tenant esistente. Dopo che è stato invitato, l'utente esterno sarà visibile in **Sottoscrizioni > Controllo di accesso (IAM)** con tutti gli utenti correnti assegnati attualmente a un ruolo di controllo degli accessi in base al ruolo nell'ambito della sottoscrizione.





![aggiungere autorizzazioni al nuovo ruolo di controllo degli accessi in base al ruolo](./media/role-based-access-control-create-custom-roles-for-internal-external-users/3.png)





![elenco di ruoli di controllo degli accessi in base al ruolo a livello di sottoscrizione](./media/role-based-access-control-create-custom-roles-for-internal-external-users/4.png)

L'utente "chessercarlton@gmail.com" è stato inviato ad essere un **proprietario** per la sottoscrizione nella "versione di valutazione gratuita". Dopo avere inviato l'invito, l'utente esterno riceverà una conferma tramite posta elettronica con un collegamento di attivazione.
![Invito tramite posta elettronica per il ruolo di controllo degli accessi in base al ruolo](./media/role-based-access-control-create-custom-roles-for-internal-external-users/5.png)

Essendo esterno all'organizzazione, il nuovo utente non dispone degli attributi esistenti nella directory "Default tenant Azure". Questi verranno creati previo consenso dell'utente esterno alla registrazione nella directory associata alla sottoscrizione per cui gli è stato assegnato un ruolo.





![messaggio di invito tramite posta elettronica per il ruolo di controllo degli accessi in base al ruolo](./media/role-based-access-control-create-custom-roles-for-internal-external-users/6.png)

L'utente esterno diventa visibile nel tenant di Azure Active Directory da questo momento in poi come utente esterno e può essere visualizzato sia nel portale di Azure sia nel portale classico.





![pannello utenti azure active directory portale di Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/7.png)





![pannello utenti azure active directory portale di Azure classico](./media/role-based-access-control-create-custom-roles-for-internal-external-users/8.png)

Nella visualizzazione **Utenti** in entrambi i portali gli utenti esterni possono essere riconosciuti da:

* Il tipo di icona diverso nel portale di Azure
* Il punto di origine diverso nel portale classico

Tuttavia, la concessione dell'accesso come **Proprietario** o **Collaboratore** a un utente esterno nell'ambito della **sottoscrizione**, non consente l'accesso alla directory dell'utente amministratore, a meno che ciò non sia consentito dall'opzione di **amministrazione globale**. Nelle proprietà dell'utente è possibile identificare il **tipo di utente** che dispone di due parametri comuni, **Membro** e **Guest**. Un membro è un utente registrato nella directory, mentre un utente guest è un utente invitato nella directory da un'origine esterna. Per altre informazioni, vedere [Procedura per aggiungere utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](/active-directory/active-directory-b2b-admin-add-users).
<!---Loc Comment: Link [How do Azure Active Directory admins add B2B collaboration users] is broken with an error message "404 - Content Not Found--->

> [!NOTE]
> Assicurarsi che dopo avere immesso le credenziali nel portale, l'utente esterno selezioni la directory corretta a cui accedere. Lo stesso utente può avere accesso a più directory e selezionarne una facendo clic sul nome utente nella parte superiore destra nel portale di Azure e quindi scegliere la directory appropriata nell'elenco a discesa.

Pur essendo un utente guest nella directory, l'utente esterno può gestire tutte le risorse per la sottoscrizione di Azure, ma non è in grado di accedere alla directory.





![accesso limitato ad Azure Active Directory nel portale di Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/9.png)

Azure Active Directory e una sottoscrizione di Azure non hanno una relazione padre-figlio come quella che hanno altre risorse di Azure, ad esempio le macchine virtuali, le reti virtuali, le app Web, le risorse di archiviazione e così via, con una sottoscrizione di Azure. Tutte queste ultime vengono create, gestite e fatturate in una sottoscrizione di Azure, mentre una sottoscrizione di Azure viene usata per gestire l'accesso a una directory di Azure. Per altre informazioni, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](/active-directory/active-directory-how-subscriptions-associated-directory).

Da tutti i ruoli predefiniti di controllo degli accessi in base al ruolo, **Proprietario** e **Collaboratore** offrono l'accesso completo a livello di gestione a tutte le risorse nell'ambiente, ma il ruolo Collaboratore non può creare ed eliminare nuovi ruoli di controllo degli accessi in base al ruolo. Altri ruoli predefiniti, come **Collaboratore Macchina Virtuale**, offrono l'accesso completo a livello di gestione solo alle risorse indicate dal nome, indipendentemente dal **gruppo di risorse** in cui sono state create.

L'assegnazione del ruolo predefinito di controllo degli accessi in base al ruolo **Collaboratore Macchina Virtuale** a livello di sottoscrizione implica che all'utente a cui è stato assegnato il ruolo seguente:

* Può visualizzare tutte le macchine virtuali indipendentemente dalla data di distribuzione e dai gruppi di risorse di appartenenza
* Ha accesso completo a livello di gestione alle macchine virtuali nella sottoscrizione
* Non può visualizzare gli altri tipi di risorse nella sottoscrizione
* Non può applicare modifiche dalla prospettiva della fatturazione

> [!NOTE]
> Il controllo degli accessi in base al ruolo non concede l'accesso al portale classico poiché è una funzionalità disponibile solo nel portale di Azure.

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Assegnare un ruolo predefinito di controllo degli accessi in base al ruolo a un utente esterno
Per uno scenario diverso in questo test, l'utente esterno "alflanigan@gmail.com" viene aggiunto come **Collaboratore Macchina virtuale**.




![ruolo predefinito collaboratore macchina virtuale](./media/role-based-access-control-create-custom-roles-for-internal-external-users/11.png)

Il comportamento normale per questo utente esterno con questo ruolo predefinito è visualizzare e gestire solo le macchine virtuali e solo le risorse di Resource Manager adiacenti necessarie durante la distribuzione. Per impostazione predefinita, questi ruoli limitati consentono l'accesso solo alle risorse corrispondenti create nel portale di Azure, indipendentemente dal fatto che alcune possano essere distribuite anche nel portale classico, ad esempio le macchine virtuali.





![Panoramica del ruolo Collaboratore macchina virtuale nel portale di Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Concedere l'accesso a livello di sottoscrizione per un utente nella stessa directory
Il flusso del processo è identico all'aggiunta di un utente esterno sia dal punto di vista dell'amministratore che concede il ruolo di controllo degli accessi in base al ruolo sia dal punto di vista dell'utente a cui viene concesso l'accesso al ruolo. La differenza è che l'utente invitato non riceverà gli inviti tramite posta elettronica e tutti gli ambiti di risorse nella sottoscrizione saranno disponibili nel dashboard dopo l'accesso.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Assegnare ruoli di controllo degli accessi in base al ruolo nell'ambito del gruppo di risorse
L'assegnazione di un ruolo di controllo degli accessi in base al ruolo nell'ambito del **gruppo di risorse** prevede un processo identico per l'assegnazione del ruolo a livello di sottoscrizione per entrambi i tipi di utenti: esterni o interni (appartenenti alla stessa directory). Gli utenti a cui viene assegnato il ruolo di controllo degli accessi in base al ruolo vedono nel proprio ambiente solo il gruppo di risorse per cui gli è stato assegnato l'accesso dall'icona **Gruppi di risorse** nel portale di Azure.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Assegnare ruoli di controllo degli accessi in base al ruolo nell'ambito delle risorse
Il processo di assegnazione di un ruolo di controllo degli accessi in base al ruolo nell'ambito delle risorse in Azure è identico per l'assegnazione del ruolo a livello di sottoscrizione o di gruppo di risorse, seguendo lo stesso flusso di lavoro per entrambi gli scenari. Anche in questo caso, gli utenti assegnati al ruolo di controllo degli accessi in base al ruolo possono vedere solo gli elementi per cui gli è stato assegnato l'accesso nella scheda **Tutte le risorse** o direttamente nel dashboard.

Un aspetto importante per il controllo degli accessi in base al ruolo nell'ambito del gruppo di risorse o delle risorse è che gli utenti devono eseguire l'accesso alla directory corretta.





![accesso alla directory nel portale di Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Assegnare ruoli di controllo degli accessi in base al ruolo per un gruppo di Azure Active Directory
Tutti gli scenari che usano il controllo degli accessi in base al ruolo nei tre ambiti diversi in Azure offrono il privilegio di gestione, distribuzione e amministrazione di varie risorse come utente assegnato, senza la necessità di gestire una sottoscrizione personale. Indipendentemente dal fatto che venga assegnato il ruolo di controllo degli accessi in base al ruolo nell'ambito di una sottoscrizione, di un gruppo di risorse o delle risorse, tutte le risorse create successivamente dagli utenti assegnati vengono fatturate per la sottoscrizione di Azure a cui gli utenti hanno accesso. In questo modo gli utenti con autorizzazioni di amministratore per la fatturazione per l'intera sottoscrizione di Azure hanno una panoramica completa sul consumo, indipendentemente da chi gestisce le risorse.

Per le organizzazioni di dimensioni maggiori, i ruoli di controllo degli accessi in base al ruolo possono essere applicati allo stesso modo per i gruppi di Azure Active Directory considerando la prospettiva che l'utente amministratore intenda concedere l'accesso granulare a team o a interi reparti, non singolarmente per ogni utente; si tratta quindi di un'opzione molto efficiente in termini di gestione e tempo. Per illustrare questo esempio, il ruolo **Collaboratore** è stato aggiunto a uno dei gruppi nel tenant a livello di sottoscrizione.





![aggiungere il ruolo di controllo degli accessi in base al ruolo per i gruppi AAD](./media/role-based-access-control-create-custom-roles-for-internal-external-users/14.png)

Questi gruppi sono gruppi di sicurezza di cui viene eseguito il provisioning e la gestione solo all'interno di Azure Active Directory.

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-powershell"></a>Creare un ruolo personalizzato di controllo degli accessi in base al ruolo per aprire richieste di supporto usando PowerShell
I ruoli predefiniti di controllo degli accessi in base al ruolo disponibili in Azure assicurano determinati livelli di autorizzazione in base alle risorse disponibili nell'ambiente. Se tuttavia nessuno di questi ruoli è adatto alle esigenze dell'utente amministratore, è possibile limitare ulteriormente l'accesso creando ruoli personalizzati di controllo degli accessi in base al ruolo.

Per la creazione di ruoli personalizzati è necessario usare un ruolo predefinito, modificarlo e importarlo di nuovo nell'ambiente. Il download e l'upload del ruolo vengono gestiti usando PowerShell o l'interfaccia della riga di comando.

È importante comprendere i prerequisiti per la creazione di un ruolo personalizzato che può concedere l'accesso granulare a livello di sottoscrizione e consentire all'utente invitato la flessibilità di aprire richieste di supporto.

In questo esempio è stato personalizzato il ruolo predefinito **Lettore**, che consente agli utenti di accedere di visualizzare tutti gli ambiti di risorse ma non di modificarli o di crearne uno nuovo, per consentire all'utente di aprire richieste di supporto.

La prima azione di esportazione del ruolo **Lettore** deve essere completata in PowerShell con autorizzazioni elevate come amministratore.

```
Login-AzureRMAccount

Get-AzureRMRoleDefinition -Name "Reader"

Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json

```





![Screenshot di PowerShell per il ruolo Lettore di controllo degli accessi in base al ruolo](./media/role-based-access-control-create-custom-roles-for-internal-external-users/15.png)

È quindi necessario estrarre il modello JSON del ruolo.





![Modello JSON per il ruolo personalizzato Lettore di controllo degli accessi in base al ruolo](./media/role-based-access-control-create-custom-roles-for-internal-external-users/16.png)

Un tipico ruolo di controllo degli accessi in base al ruolo è composto da tre sezioni principali, **Actions**, **NotActions** e **AssignableScopes**.

Nella sezione **Azione** vengono elencate tutte le operazioni consentite per questo ruolo. È importante tenere presente che ogni azione viene assegnata da un provider di risorse. In questo caso per la creazione di ticket di supporto è necessario che sia elencato il provider di risorse **Microsoft.Support**.

Per poter visualizzare tutti i provider di risorse disponibili e registrati nella sottoscrizione, è possibile usare PowerShell.
```
Get-AzureRMResourceProvider

```
Inoltre è possibile cercare di tutti i cmdlet di PowerShell disponibili per gestire i provider di risorse.
    ![Screenshot di PowerShell per la gestione dei provider di risorse](./media/role-based-access-control-create-custom-roles-for-internal-external-users/17.png)

Per limitare tutte le azioni di uno specifico ruolo di controllo degli accessi in base al ruolo, i provider di risorse sono elencati nella sezione **NotActions**.
Infine è obbligatorio che il ruolo di controllo degli accessi in base al ruolo contenga gli ID di sottoscrizione espliciti in cui viene usato. Gli ID di sottoscrizione sono elencati in **AssignableScopes**; in caso contrario non sarà possibile importare il ruolo nella sottoscrizione.

Dopo la creazione e la personalizzazione del ruolo di controllo degli accessi in base al ruolo, è necessario reimportarlo nell'ambiente.

```
New-AzureRMRoleDefinition -InputFile "C:\rbacrole2.json"

```

In questo esempio il nome personalizzato per questo ruolo di controllo degli accessi in base al ruolo è "Reader support tickets access level" che consente all'utente di visualizzare tutti gli elementi nella sottoscrizione e di aprire le richieste di supporto.

> [!NOTE]
> Esistono solo due ruoli predefiniti di controllo degli accessi in base al ruolo che consentono di aprire richieste di supporto: **Proprietario** e **Collaboratore**. Per consentire a un utente di aprire richieste di supporto, è necessario che gli sia stato assegnato un ruolo di controllo degli accessi in base al ruolo solo nell'ambito della sottoscrizione perché tutte le richieste di supporto vengono create in base a una sottoscrizione di Azure.

Questo nuovo ruolo personalizzato è stato assegnato a un utente della stessa directory.





![screenshot del ruolo di controllo degli accessi in base al ruolo importati nel portale di Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/18.png)





![screenshot dell'assegnazione dei ruoli di controllo degli accessi in base al ruolo importati personalizzati all'utente della stessa directory](./media/role-based-access-control-create-custom-roles-for-internal-external-users/19.png)





![screenshot delle autorizzazioni per il ruolo personalizzato importato di controllo degli accessi in base al ruolo](./media/role-based-access-control-create-custom-roles-for-internal-external-users/20.png)

Altri dettagli nell'esempio evidenziano i limiti di questo ruolo personalizzato, come indicato di seguito:
* Può creare nuove richieste di supporto
* Non può creare nuovi ambiti di risorse, ad esempio la macchina virtuale
* Non può creare nuovi gruppi di risorse





![screenshot del ruolo personalizzato di controllo degli accessi in base al ruolo che crea richieste di supporto](./media/role-based-access-control-create-custom-roles-for-internal-external-users/21.png)





![screenshot del ruolo personalizzato di controllo degli accessi in base al ruolo che non può creare VM](./media/role-based-access-control-create-custom-roles-for-internal-external-users/22.png)





![screenshot del ruolo personalizzato di controllo degli accessi in base al ruolo che non può creare nuovi gruppi di risorse](./media/role-based-access-control-create-custom-roles-for-internal-external-users/23.png)

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-azure-cli"></a>Creare un ruolo personalizzato di controllo degli accessi in base al ruolo per aprire richieste di supporto usando l'interfaccia della riga di comando di Azure
Per l'esecuzione su un Mac e senza dover accedere a PowerShell, l'interfaccia della riga di comando di Azure è la soluzione adatta.

I passaggi per creare un ruolo personalizzato sono gli stessi, con l'unica eccezione che quando si usa l'interfaccia della riga di comando non è possibile scaricare il ruolo in un modello JSON, ma è possibile visualizzarlo nell'interfaccia della riga di comando.

In questo esempio è stato scelto il ruolo predefinito **Lettore di backup**.

```

azure role show "backup reader" --json

```





![Screenshot dell'interfaccia della riga di comando del ruolo di lettore backup](./media/role-based-access-control-create-custom-roles-for-internal-external-users/24.png)

Modificando il ruolo in Visual Studio dopo la copia delle proprietà in un modello JSON, il provider di risorse **Microsoft.Support** è stato aggiunto nelle sezioni **Azioni** in modo che questo utente possa aprire le richieste di supporto pur continuando a essere un lettore per gli insiemi di credenziali di backup. Anche in questo caso è necessario aggiungere l'ID sottoscrizione in cui verrà usato questo ruolo nella sezione **AssignableScopes**.

```

azure role create --inputfile <path>

```





![Screenshot dell'interfaccia della riga di comando di importazione del ruolo personalizzato di controllo degli accessi in base al ruolo](./media/role-based-access-control-create-custom-roles-for-internal-external-users/25.png)

Il nuovo ruolo è ora disponibile nel portale di Azure e il processo di assegnazione è lo stesso descritto negli esempi precedenti.





![Screenshot del portale di Azure del ruolo personalizzato di controllo degli accessi in base al ruolo creato usando l'interfaccia della riga di comando 1.0](./media/role-based-access-control-create-custom-roles-for-internal-external-users/26.png)

Al momento della build 2017 aggiornata, Azure Cloud Shell è disponibile a livello generale. Azure Cloud Shell è complementare all'ambiente IDE e al portale di Azure. Con questo servizio si ottiene una shell basata su browser autenticata e ospitata in Azure che è possibile usare al posto dell'interfaccia della riga di comando installata nel computer.





![Azure Cloud Shell](./media/role-based-access-control-create-custom-roles-for-internal-external-users/27.png)
