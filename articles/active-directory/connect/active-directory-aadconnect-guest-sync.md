---
title: Sincronizzare gli account utente guest che usano la posta elettronica per l'accesso ad Azure | Microsoft Docs
description: Questo articolo illustra come sincronizzare gli account utente guest che usano un ID alternativo per accedere alle applicazioni.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/19/2018
ms.author: billmath
ms.openlocfilehash: 4e6cf3bb4a691380a5fe22f5afdf749b40f15ef3
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2018
---
# <a name="synchronizing-guest-user-accounts-that-use-email-for-sign-in-preview"></a>Sincronizzare gli account utente guest che usano la posta elettronica per l'accesso (anteprima)

>[!NOTE]
> Questa funzionalità è attualmente in anteprima pubblica.

Lo scenario seguente si riferisce a una situazione in cui nell'ambiente AD locale sono presenti utenti esterni, ad esempio partner, che usano un metodo di accesso alternativo.

Nell'esempio precedente Nina Morin lavora per Fabrikam e ha l'indirizzo di posta elettronica seguente: nmorin@fabrikam.com. Nina è partner di Contoso e deve accedere a determinate applicazioni di Contoso. Contoso ha creato un account per Nina e ha chiesto a Nina di usare il suo indirizzo di posta elettronica per accedere alle applicazioni.

Per le applicazioni locali, questo scenario ha funzionato perfettamente. Ora, tuttavia, Contoso sta spostando le applicazioni nel cloud e vuole offrire ai suoi partner la stessa esperienza.

Questo scenario si riferisce a una situazione di questo tipo.


## <a name="pre-requisites-and-assumptions"></a>Prerequisiti e presupposti
Questa sezione contiene un elenco di prerequisiti e presupposti da conoscere prima di cercare di configurare lo scenario.

### <a name="pre-requisites"></a>Prerequisiti
- Credenziali di amministratore globale per configurare Azure AD Connect, verificare i domini e configurare le impostazioni di federazione dei domini
- Azure AD Connect versione 1.1.524.0 o successiva
- Dominio verificato per impostare l'UPN cloud degli utenti esterni (esempio: bmcontoso.com).
- Servizio federativo per autenticare gli utenti esterni. Se si usa AD FS, usare la versione 2012 R2 o successiva
- MSOL PowerShell v1.1 installato in un computer per verificare le impostazioni di federazione. Per altre informazioni, vedere [Azure ActiveDirectory (MSOnline)](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0).


### <a name="assumptions"></a>Presupposti 
- Azure AD Connect è già stato configurato e installato correttamente. Per informazioni su come installare Azure AD Connect, vedere [Azure AD Connect e federazione](active-directory-aadconnectfed-whatis.md).
Questo documento presuppone quanto segue:
- Che sia stato configurato un servizio federativo che convalida correttamente gli utenti.
- Che gli utenti esterni possano eseguire l'autenticazione usando il proprio indirizzo di posta elettronica esterno.
- - È stato impostato e configurato l'uso di un ID alternativo per l'accesso. Gli utenti possono eseguire l'autenticazione usando il proprio ID alternativo. Per altre informazioni sull'impostazione di un ID alternativo con AD FS, vedere [Configurazione di un ID di accesso alternativo](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).

## <a name="task-1--prepare-the-environment"></a>Attività 1: Preparare l'ambiente
L'attività seguente ha principalmente scopo informativo, in modo da essere pronti a iniziare la sincronizzazione degli account esterni affinché possano accedere usando un ID alternativo, come l'attributo mail.

Definire gli elementi nella tabella riportata di seguito prima di passare alla seconda attività.

![Architecture](media/active-directory-aadconnect-guest-sync/guest2.png)

|Aspetto ambientale|Scopo|Implementazione nell'ambiente|
|-----|-----|-----|
|Attributo UPN cloud|Attributo che popola l'UPN degli oggetti utente esterni nel cloud. Il suffisso UPN per gli account esterni deve essere quello definito nei prerequisiti. Si tratta del dominio verificato.|* Esempio: UserPrincipalName (nmorin@bmcontoso.com)|
|Indirizzo di accesso|Attributo che gli utenti esterni devono digitare durante l'accesso. Questo attributo deve avere il formato di un indirizzo di posta elettronica e nella maggior parte dei casi coincide con l'indirizzo di posta elettronica effettivo dell'utente esterno.|* Esempio: mail (nmorin@fabrikam.com)|
|Filtro di ambito di Azure AD Connect|Filtro che consente di specificare le identità esterne per definire l'ambito delle regole di sincronizzazione definite più avanti in questa guida. I modi tipici per definire un ambito includono: un'unità organizzativa predefinita nell'organizzazione, una determinata convenzione di denominazione, un dominio specifico e così via.|* Esempio: OU contains Externals|
|Tenant di Azure AD|Nome del tenant di Azure AD visualizzato in Azure AD Connect.|Esempio:  contoso.onmicrosoft.com|

Nello screenshot seguente sono evidenziati tre elementi nelle caselle.
- L'unità organizzativa **Externals** usata nel filtro di ambito di Azure AD Connect e che rappresenta la posizione degli utenti esterni.
- L'attributo **mail**, usato dagli utenti esterni per accedere.
- L'attributo **userPrincipalName**, ovvero il dominio verificato con cui l'ambiente locale è federato.

![Utente](media/active-directory-aadconnect-guest-sync/guest1.png)

## <a name="task-2--configure-azure-ad-connect"></a>Attività 2: Configurare Azure AD Connect
Dopo aver definito le informazioni precedenti, è possibile procedere con l'impostazione delle regole di sincronizzazione di Azure AD Connect. Per impostare le regole, usare l'editor delle regole di sincronizzazione di Azure AD Connect. Per altre informazioni sull'editor, vedere [Provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

### <a name="how-to-configure-the-synchronization-rule"></a>Come configurare la regola di sincronizzazione
Usare la procedura seguente per configurare Azure AD Connect.

1. Aprire l'editor delle regole di sincronizzazione di Azure AD Connect passando a **Start - Azure AD Connect - Synchronization rules editor** (Editor regole di sincronizzazione).
2. Nella schermata **Synchronization Rules Editor** (Editor regole di sincronizzazione) verificare che la direzione sia **inbound** (in ingresso) e a destra fare clic su **Add new rule** (Aggiungi nuova regola).
3. Nella pagina **Description** (Descrizione) configurare quanto segue e fare clic su **Next** (Avanti).
    - **Name** (Nome): immettere un nome per la regola 
    - **Connected System** (Sistema connesso): ambiente AD locale
    - **Connected System Object Type** (Tipo di oggetto sistema connesso): user (utente)
    - **Metaverse Object Type** (Tipo di oggetto metaverse): person (persona)
    - **Link Type** (Tipo di collegamento): Join
    - **Precedence** (Precedenza): 90
    - 
![](media/active-directory-aadconnect-guest-sync/guest3.png)

4. Nella schermata **Scoping Filter** (Filtro di ambito) fare clic su **Add group** (Aggiungi gruppo).
5. Usare gli elenchi a discesa per configurare il filtro. Immettere quanto segue e fare clic su **Next** (Avanti). Questa azione crea un filtro che si applica solo agli oggetti che si trovano nell'unità organizzativa esterna.
    - **Attribute** (Attributo): dn
    - **Operator** (Operatore): CONTAINS
    - **Value** (Valore): Externals
 
 ![Filtro](media/active-directory-aadconnect-guest-sync/guest4.png)

6. Nella schermata **Join Rules** (Regole di unione) fare clic su **Next** (Avanti).
7. Nella schermata **Transformations** (Trasformazioni) fare clic su **Add Transformation** (Aggiungi trasformazione). Immettere le seguenti informazioni:
    - **FlowType** (Tipo di flusso): Constant (Costante)
    - **Target Attribute** (Attributo di destinazione): userType
    - **Source** (Origine): Guest
8. Nella schermata **Transformations** (Trasformazioni) fare clic su **Add Transformation** (Aggiungi trasformazione). Immettere le seguenti informazioni:
    - **FlowType** (Tipo di flusso): Direct (Diretto)
    - **Target Attribute** (Attributo di destinazione): onPremisesUserPrincipalName
    - **Source** (Origine): mail
9. Nella schermata **Transformations** (Trasformazioni) fare clic su **Add Transformation** (Aggiungi trasformazione). Immettere le seguenti informazioni:
    - **FlowType** (Tipo di flusso): Direct (Diretto)
    - **Target Attribute** (Attributo di destinazione): userPrincipalName
    - **Source** (Origine): userPrincipalName ![Trasformazione](media/active-directory-aadconnect-guest-sync/guest5.png)
10. Fare clic su **Aggiungi**. 
11. Nella schermata **Synchronization Rules Editor** (Editor regole di sincronizzazione) verificare che la direzione sia **outbound** (in uscita) e a destra fare clic su **Add new rule** (Aggiungi nuova regola).
12. Nella pagina **Description** (Descrizione) configurare quanto segue e fare clic su **Next** (Avanti).
    - **Name** (Nome): immettere un nome per la regola 
    - **Connected System** (Sistema connesso): tenant di Azure AD
    - **Connected System Object Type** (Tipo di oggetto sistema connesso): user (utente)
    - **Metaverse Object Type** (Tipo di oggetto metaverse): person (persona)
    - **Link Type** (Tipo di collegamento): Join
    - **Precedence** (Precedenza): 90
    - 
![Regola](media/active-directory-aadconnect-guest-sync/guest6.png)

13. Nella schermata **Scoping Filter** (Filtro di ambito) fare clic su **Next** (Avanti).
14. Nella schermata **Join Rules** (Regole di unione) fare clic su **Next** (Avanti).
15. Nella schermata **Transformations** (Trasformazioni) fare clic su **Add Transformation** (Aggiungi trasformazione).  Immettere le seguenti informazioni:
    - **FlowType** (Tipo di flusso): Direct (Diretto)
    - **Target Attribute** (Attributo di destinazione): userType
    - **Source** (Origine): userType
9. Nella schermata **Transformations** (Trasformazioni) fare clic su **Add Transformation** (Aggiungi trasformazione). Immettere le seguenti informazioni:
    - **FlowType** (Tipo di flusso): Direct (Diretto)
    - **Target Attribute** (Attributo di destinazione): onPremisesUserPrincipalName
    - **Source** (Origine): onPremisesUserPrincipalName ![Trasformazione](media/active-directory-aadconnect-guest-sync/guest7.png)
10. Fare clic su **Aggiungi**. 
11. Una volta configurate queste regole, è necessario eseguire una sincronizzazione completa. Usare PowerShell per avviare una sincronizzazione completa. Una volta completata la sincronizzazione, è possibile procedere al passaggio successivo.

``` powershell
    Start-ADSyncSyncCycle -PolicyType Initial
```

## <a name="task-3--federation"></a>Attività 3: Federazione
L'attività seguente è un'attività informativa relativa ad alcuni aspetti necessari per il funzionamento dello scenario.

È possibile verificare le impostazioni di federazione con Azure usando Azure AD PowerShell. Questo documento usa MSOL PowerShell v1.1. È possibile installare questa versione [qui](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0).

### <a name="verify-the-federation-settings"></a>Verificare le impostazioni di federazione
Usare la procedura seguente per verificare le impostazioni di federazione.
1. Aprire Windows PowerShell e connettersi ad Azure AD usando il comando seguente:
``` powershell
      Connect-MSOLservice
```
2.  Immettere le credenziali di amministratore globale
3.  Immettere ora il comando seguente:
  ``` powershell
      Get-MSOLDomainFederationSettings
  ```
4.   Osservare le informazioni di federazione che vengono restituite. **ActiveLogonUri** è l'URL del server federativo.

  ![Federazione](media/active-directory-aadconnect-guest-sync/guest8.png)

### <a name="verify-alternate-login-id"></a>Verificare l'ID di accesso alternativo
Questo documento usa AD FS come provider di identità (IdP). Se si usa un IdP diverso, i passaggi potrebbero essere diversi.

1. Aprire Windows PowerShell e immettere il comando seguente:
   ```powershell
    Get-ADFSClaimsProviderTrust
   ```
2. Dovrebbero venire visualizzate le informazioni di AD FS.  Osservare **AlternateLoginID** e **LookupForests**.

![AD FS](media/active-directory-aadconnect-guest-sync/guest9.png)

## <a name="task-4--testing"></a>Attività 4: Eseguire i test
Per verificare il corretto funzionamento, è necessario effettuare l'accesso a un endpoint che è stato configurato per eseguire l'autenticazione con l'IdP. Per eseguire questo test, è stato distribuito un sito Web in Azure e viene usato l'URL seguente: contososampapp.azurewebsites.net

### <a name="verify-that-you-can-sign-in-with-the-alternate-id"></a>Verificare che sia possibile accedere con l'ID alternativo
1. Accedere all'endpoint.</br>
![Endpoint](media/active-directory-aadconnect-guest-sync/guest10.png)
1. Immettere il nome utente. Si verrà reindirizzati alla pagina di accesso della federazione.
![Federazione](media/active-directory-aadconnect-guest-sync/guest11.png)
1. Immettere le credenziali.
2. Verrà così eseguito l'accesso.
![Success](media/active-directory-aadconnect-guest-sync/guest12.png)

## <a name="next-steps"></a>Passaggi successivi
- [Proprietà di un utente di Collaborazione B2B di Azure Active Directory](../../active-directory/b2b/user-properties.md#key-properties-of-the-azure-ad-b2b-collaboration-user)
- [Configurazione di un ID di accesso alternativo](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)
- [Azure AD Connect: Cronologia delle versioni](active-directory-aadconnect-version-history.md)
