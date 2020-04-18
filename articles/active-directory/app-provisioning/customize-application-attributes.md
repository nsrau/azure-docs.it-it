---
title: Personalizzazione dei mapping degli attributi di Azure AD Documenti Microsoft
description: Informazioni sui mapping degli attributi per app SaaS in Azure Active Directory e su come è possibile modificarli in base alle esigenze aziendali.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfee19e9cfd1def71ebad82c2210ffc10146c896
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639755"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personalizzazione del provisioning degli utenti dei mapping degli attributi per le applicazioni SaaS in Azure Active DirectoryCustomizing user provisioning attribute-mappings for SaaS applications in Azure Active Directory

Microsoft Azure AD fornisce il supporto per il provisioning degli utenti ad applicazioni SaaS di terze parti, ad esempio Salesforce, G Suite e altri. Se si abilita il provisioning degli utenti per un'applicazione SaaS di terze parti, il portale di Azure controlla i valori degli attributi tramite i mapping degli attributi.

Esiste un set preconfigurato di attributi e mapping di attributi tra gli oggetti utente di Azure AD e gli oggetti utente di ogni app SaaS. Alcune app gestiscono altri tipi di oggetti insieme agli utenti, ad esempio Gruppi.

È possibile personalizzare i mapping degli attributi predefiniti in base alle esigenze aziendali. In questo modo, è possibile modificare o eliminare i mapping di attributi esistenti o creare nuovi mapping di attributi.

## <a name="editing-user-attribute-mappings"></a>Modifica dei mapping degli attributi utente

Per accedere alla funzionalità Mapping del provisioning degli utenti, **attenersi** alla seguente procedura:

1. Accedere al [portale](https://aad.portal.azure.com)di Azure Active Directory .
1. Selezionare **Applicazioni aziendali** nel riquadro sinistro. Viene visualizzato un elenco di tutte le app configurate, incluse le app aggiunte dalla raccolta.
1. Seleziona qualsiasi app per caricare il relativo riquadro di gestione delle app, in cui puoi visualizzare i report e gestire le impostazioni dell'app.
1. Selezionare **Provisioning** per gestire le impostazioni di provisioning degli account utente per l'app selezionata.
1. Espandere **Mapping** per visualizzare e modificare gli attributi utente che passano tra Azure AD e l'applicazione di destinazione. Se l'applicazione di destinazione lo supporta, questa sezione consente di configurare facoltativamente il provisioning di gruppi e account utente.

   ![Utilizzare i mapping per visualizzare e modificare gli attributi utente](./media/customize-application-attributes/21.png)

1. Selezionare una configurazione **Mapping** per aprire la relativa schermata **Mappatura attributi.** Alcuni mapping di attributi sono necessari da un'applicazione SaaS per funzionare correttamente. Per gli attributi obbligatori la funzionalità **Elimina** non è disponibile.

   ![Usare il mapping degli attributi per configurare i mapping degli attributi per le appUse Attribute Mapping to configure attribute mappings for apps](./media/customize-application-attributes/22.png)

   In questa schermata è possibile vedere che l'attributo **Username** di un oggetto gestito in Salesforce viene popolato con il valore **userPrincipalName** dell'oggetto Azure Active Directory collegato.

1. Selezionare un **mapping di attributi** esistente per aprire la schermata Modifica **attributo.** Qui è possibile modificare gli attributi utente che scorrono tra Azure AD e l'applicazione di destinazione.

   ![Utilizzare Modifica attributo per modificare gli attributi utente](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Informazioni sui tipi di mapping degli attributi

I mapping degli attributi permettono di controllare il modo in cui gli attribuiti vengono popolati in un'applicazione SaaS di terze parti.
Sono supportati quattro diversi tipi di mapping:

- **Diretto:** l'attributo di destinazione viene popolato con il valore di un attributo dell'oggetto collegato in Azure AD.
- **Costante:** l'attributo di destinazione viene popolato con una stringa specifica specificata.
- **Espressione:** l'attributo di destinazione viene popolato sulla base del risultato di una espressione simile a uno script.
  Per altre informazioni, vedere [Scrittura di espressioni per il mapping degli attributi in Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md).
- **Nessuno:** l'attributo di destinazione viene lasciato invariato. Tuttavia, se l'attributo di destinazione è sempre vuoto, viene popolato con il valore predefinito specificato.

Insieme a questi quattro tipi di base, i mapping di attributi personalizzati supportano il concetto di un'assegnazione di valore **predefinito** facoltativa. L'assegnazione di valore predefinito assicura che un attributo di destinazione viene popolato con un valore se non è presente un valore in Azure AD o nell'oggetto di destinazione. Nella maggior parte delle configurazioni questo campo viene lasciato vuoto.

### <a name="understanding-attribute-mapping-properties"></a>Informazioni sulle proprietà di mapping degli attributi

Nella sezione precedente è già stata introdotta la proprietà del tipo di mapping degli attributi.
Insieme a questa proprietà, i mapping degli attributi supportano anche gli attributi seguenti:

- **Attributo di origine**: attributo utente nel sistema di origine, ad esempio Azure Active Directory.
- **Attributo di destinazione**: attributo utente nel sistema di destinazione, ad esempio ServiceNow.
- **Valore predefinito se null (facoltativo):** il valore che verrà passato al sistema di destinazione se l'attributo di origine è null. Questo valore verrà eseguito solo quando viene creato un utente. Il "valore predefinito quando null" non verrà eseguito il provisioning durante l'aggiornamento di un utente esistente. Se, ad esempio, si desidera eseguire il provisioning di tutti gli utenti esistenti nel sistema di destinazione con un particolare titolo professionale (quando è null nel sistema di origine), è possibile utilizzare [l'espressione](../app-provisioning/functions-for-customizing-application-data.md)seguente: Switch(IsPresent([jobTitle]), "DefaultValue", "True", [jobTitle]). Assicurarsi di sostituire il "Valore predefinito" con ciò che si desidera eseguire il provisioning quando null nel sistema di origine. 
- **Corrispondenza oggetti utilizzando questo attributo:** indica se questo mapping deve essere utilizzato per identificare in modo univoco gli utenti tra i sistemi di origine e di destinazione. In genere viene impostato sull'attributo userPrincipalName o mail in Azure AD, che in genere viene mappato a un campo username in un'applicazione di destinazione.
- **Precedenza abbinamento**: è possibile impostare più attributi corrispondenti. Quando sono presenti più, vengono valutati nell'ordine definito da questo campo. Quando viene rilevata una corrispondenza la valutazione degli attributi corrispondenti termina.
- **Applica questo mapping**
  - **Sempre:** applicare questo mapping sia alle azioni di creazione che di aggiornamento dell'utente.
  - **Solo durante la creazione:** applicare questo mapping solo alle azioni di creazione dell'utente.

## <a name="matching-users-in-the-source-and-target--systems"></a>Corrispondenza degli utenti nei sistemi di origine e di destinazione
Il servizio di provisioning di Azure AD può essere distribuito in scenari "greenfield" (in cui gli utenti non si escono nel sistema di destinazione) e in scenari "brownfield" (in cui gli utenti esistono già nel sistema di destinazione). Per supportare entrambi gli scenari, il servizio di provisioning utilizza il concetto di attributi corrispondenti. Gli attributi corrispondenti consentono di determinare come identificare in modo univoco un utente nell'origine e trovare una corrispondenza con l'utente nella destinazione. Durante la pianificazione della distribuzione, identificare l'attributo che può essere utilizzato per identificare in modo univoco un utente nei sistemi di origine e di destinazione. Cose da notare:

- **Gli attributi corrispondenti devono essere univoci:** I clienti utilizzano spesso attributi quali userPrincipalName, mail o ID oggetto come attributo corrispondente.
- **È possibile utilizzare più attributi come attributi corrispondenti:** È possibile definire più attributi da valutare quando corrispondono agli utenti e l'ordine in cui vengono valutati (definito come precedenza corrispondente nell'interfaccia utente). Se, ad esempio, si definiscono tre attributi come attributi corrispondenti e un utente viene abbinato in modo univoco dopo la valutazione dei primi due attributi, il servizio non valuterà il terzo attributo. Il servizio valuterà gli attributi corrispondenti nell'ordine specificato e interromperà la valutazione quando viene trovata una corrispondenza.  
- **Il valore nell'origine e nella destinazione non deve corrispondere esattamente a:** Il valore nella destinazione può essere una semplice funzione del valore nell'origine. Pertanto, si potrebbe avere un attributo emailAddress nell'origine e userPrincipalName nella destinazione e la corrispondenza da una funzione dell'attributo emailAddress che sostituisce alcuni caratteri con un valore costante.  
- **La corrispondenza basata su una combinazione di attributi non è supportata:** La maggior parte delle applicazioni non supporta l'esecuzione di query in base a due proprietà. Pertanto, non è possibile trovare una corrispondenza in base a una combinazione di attributi. È possibile valutare singole proprietà dopo un'altra.
- **Tutti gli utenti devono avere un valore per almeno un attributo corrispondente:All users must** have a value for at least one matching attribute: Se si definisce un attributo corrispondente, tutti gli utenti devono avere un valore per tale attributo nel sistema di origine. Se, ad esempio, si definisce userPrincipalName come attributo corrispondente, tutti gli utenti devono disporre di un userPrincipalName. Se si definiscono più attributi corrispondenti (ad esempio extensionAttribute1 e mail), non tutti gli utenti devono avere lo stesso attributo corrispondente. Un utente potrebbe avere un extensionAttribute1 ma non posta mentre un altro utente potrebbe avere posta ma nessun extensionAttribute1. 
- **L'applicazione di destinazione deve supportare il filtro sull'attributo corrispondente:The target application must support filtering on the matching attribute:** Gli sviluppatori di applicazioni consentono di filtrare per un sottoinsieme di attributi nell'API dell'utente o del gruppo. Per le applicazioni nella raccolta, ci assicuriamo che il mapping degli attributi predefinito sia per un attributo su cui l'API dell'applicazione di destinazione supporta il filtro. Quando si modifica l'attributo corrispondente predefinito per l'applicazione di destinazione, controllare la documentazione dell'API di terze parti per assicurarsi che l'attributo possa essere filtrato.  

## <a name="editing-group-attribute-mappings"></a>Modifica dei mapping degli attributi gruppo

Un numero selezionato di applicazioni, ad esempio ServiceNow, Box e G Suite, supporta la possibilità di eseguire il provisioning di oggetti Gruppo e Oggetti utente. Gli oggetti gruppo possono contenere proprietà del gruppo, ad esempio nomi visualizzati e alias di posta elettronica, insieme ai membri del gruppo.

![L'esempio mostra ServiceNow con gli oggetti Group e User di cui è stato eseguito il provisioning](./media/customize-application-attributes/24.png)

Il provisioning dei gruppi può essere abilitato o disabilitato facoltativamente selezionando il mapping dei gruppi in **Mapping**e impostando **Abilitato sull'opzione** desiderata nella schermata **Mapping attributi.**

Gli attributi di cui viene effettuato il provisioning come parte di oggetti gruppo possono essere personalizzati allo stesso modo degli oggetti utente, descritto in precedenza. 

> [!TIP]
> Il provisioning di oggetti gruppo (proprietà e membri) è un concetto distinto dall'[assegnazione di gruppi](../manage-apps/assign-user-or-group-access-portal.md) a un'applicazione. Benché sia possibile assegnare un gruppo a un'applicazione, è possibile effettuare il provisioning solo degli oggetti utente contenuti nel gruppo. Il provisioning di oggetti gruppo completi non è necessario per usare i gruppi nelle assegnazioni.

## <a name="editing-the-list-of-supported-attributes"></a>Modifica dell'elenco degli attributi supportati

Gli attributi utente supportati per un'applicazione specifica sono preconfigurati. La maggior parte delle API di gestione degli utenti dell'applicazione non supporta l'individuazione dello schema. Pertanto, il servizio di provisioning di Azure AD non è in grado di generare dinamicamente l'elenco di attributi supportati effettuando chiamate all'applicazione.

Tuttavia, alcune applicazioni supportano attributi personalizzati e il servizio di provisioning di Azure AD può leggere e scrivere negli attributi personalizzati. Per immettere le relative definizioni nel portale di Azure, selezionare la casella di controllo **Mostra opzioni avanzate** nella parte inferiore della schermata Mapping **attributi** e quindi selezionare Modifica elenco **attributi per** l'app.

Alcuni sistemi e applicazioni che supportano la personalizzazione dell'elenco di attributi:

- Salesforce
- ServiceNow
- Workday
- Azure Active Directory (sono supportati i[riferimenti all'API REST di Microsoft Graph v1.0](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0) e le estensioni di directory personalizzate)Azure Active Directory ( Microsoft Graph REST API v1.0 reference and custom directory extensions are supported)
- App che supportano [SCIM 2.0](https://tools.ietf.org/html/rfc7643), in cui devono essere aggiunti gli attributi definiti nello [schema di base](https://tools.ietf.org/html/rfc7643)

> [!NOTE]
> La modifica dell'elenco degli attributi supportati è consigliata solo agli amministratori che hanno personalizzato lo schema dei propri sistemi e applicazioni e che hanno una conoscenza diretta del modo in cui sono stati definiti gli attributi personalizzati. Questa operazione richiede a volte familiarità con le API e gli strumenti per sviluppatori forniti da un'applicazione o un sistema.

Quando si modifica l'elenco degli attributi supportati, vengono fornite le proprietà seguenti:

- **Nome**: nome di sistema dell'attributo, definito nello schema dell'oggetto di destinazione.
- **Tipo:** il tipo di dati archiviati dall'attributo, come definito nello schema dell'oggetto di destinazione, che può essere uno dei tipi seguenti:
  - *Binario*: l'attributo contiene dati binari.
  - *Booleano*: l'attributo contiene un valore True o False.
  - *DateTime*: l'attributo contiene una stringa di data.
  - *Integer*: l'attributo contiene un numero intero.
  - *Riferimento*: l'attributo contiene un ID che fa riferimento a un valore archiviato in un'altra tabella nell'applicazione di destinazione.
  - *String*: l'attributo contiene una stringa di testo.
- **Chiave primaria?** - Se l'attributo è definito come campo chiave primaria nello schema dell'oggetto di destinazione.
- **Obbligatorio?** - Se l'attributo deve essere popolato nell'applicazione o nel sistema di destinazione.
- **Multivalore?** - Indica se l'attributo supporta più valori.- whether the attribute supports multiple values.
- **Maiuscole/minuscole esatte?**: - Indica se i valori degli attributi vengono valutati in modo che esedano distinzioni tra maiuscole e minuscole.
- **Espressione API:** non usare, a meno che non venga richiesto dalla documentazione di un connettore di provisioning specifico (ad esempio Workday).
- **Attributo oggetto** di riferimento: se si tratta di un attributo di tipo Riferimento, questo menu consente di selezionare la tabella e l'attributo nell'applicazione di destinazione che contiene il valore associato all'attributo. Ad esempio, in presenza di un attributo denominato "Reparto" il cui valore archiviato fa riferimento a un oggetto in una tabella "Reparti" separata, sarà necessario selezionare "Reparti.Nome". Le tabelle di riferimento e i campi dell'ID primario supportati per una determinata applicazione sono preconfigurati e attualmente non possono essere modificati tramite il portale di Azure, ma possono essere modificati tramite [l'API Microsoft Graph.](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes)

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>Provisioning di un attributo di estensione personalizzato in un'applicazione conforme a SCIM
La RFC SCIM definisce uno schema di utenti e gruppi di base, consentendo al tempo stesso le estensioni allo schema in base alle esigenze dell'applicazione. Per aggiungere un attributo personalizzato a un'applicazione SCIM:
   1. Accedere al [portale](https://aad.portal.azure.com)di Azure Active Directory , selezionare **Applicazioni aziendali**, selezionare l'applicazione e quindi selezionare **Provisioning**.
   2. In **Mapping**selezionare l'oggetto (utente o gruppo) per il quale si desidera aggiungere un attributo personalizzato.
   3. Nella parte inferiore della pagina selezionare **Mostra opzioni avanzate**.
   4. Selezionare **Modifica elenco attributi per AppName**.
   5. Nella parte inferiore dell'elenco di attributi, immettere le informazioni sull'attributo personalizzato nei campi forniti. Selezionare **quindi Aggiungi attributo**.

Per le applicazioni SCIM, il nome dell'attributo deve seguire il modello illustrato nell'esempio seguente. "CustomExtensionName" e "CustomAttribute" possono essere personalizzati in base ai requisiti dell'applicazione, ad esempio:  
 * urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User:CustomAttribute 
 * urn:ietf:params:scim:schemas:extension:2.0:CustomExtensionName:CustomAttribute  
 * urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User.CustomAttributeName:value

Queste istruzioni sono applicabili solo alle applicazioni abilitate per SCIM. Applicazioni come ServiceNow e Salesforce non sono integrate con Azure AD tramite SCIM e pertanto non richiedono questo spazio dei nomi specifico quando si aggiunge un attributo personalizzato.

Gli attributi personalizzati non possono essere attributi referenziali o attributi multivalore. Gli attributi di estensione multivalore personalizzati sono attualmente supportati solo per le applicazioni nella raccolta.  
 
**Rappresentazione di esempio di un utente con un attributo di estensione:Example representation of a user with an extension attribute:**

```json
   {
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "formatted":"Ms. Barbara J Jensen III",
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "employeeNumber": "701984",
     "costCenter": "4130",
     "organization": "Universal Studios",
     "division": "Theme Park",
     "department": "Tour Operations",
     "manager": {
       "value": "26118915-6090-4610-87e4-49d8ca9f808d",
       "$ref": "../Users/26118915-6090-4610-87e4-49d8ca9f808d",
       "displayName": "John Smith"
     }
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "CustomAttribute": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 }
```


## <a name="provisioning-a-role-to-a-scim-app"></a>Provisioning di un ruolo in un'app SCIM
Usare la procedura seguente per eseguire il provisioning dei ruoli per un utente nell'applicazione. Si noti che la descrizione riportata di seguito è specifica per le applicazioni SCIM personalizzate. Per le applicazioni della raccolta, ad esempio Salesforce e ServiceNow, utilizzare i mapping dei ruoli predefiniti. I punti elenco seguenti descrivono come trasformare l'attributo AppRoleAssignments nel formato previsto dall'applicazione.

- Il mapping di un'appRoleAssignment in Azure AD a un ruolo nell'applicazione richiede la trasformazione dell'attributo usando [un'espressione](../app-provisioning/functions-for-customizing-application-data.md). L'attributo appRoleAssignment **non deve essere mappato direttamente** a un attributo role senza usare un'espressione per analizzare i dettagli del ruolo. 

- **SingleAppRoleAssignment** 
  - **Quando utilizzare:** Usare l'espressione SingleAppRoleAssignment per eseguire il provisioning di un singolo ruolo per un utente e specificare il ruolo primario. 
  - **Come configurare:** Utilizzare la procedura descritta in precedenza per passare alla pagina dei mapping degli attributi e usare l'espressione SingleAppRoleAssignment per eseguire il mapping all'attributo roles. Ci sono tre attributi di ruolo tra cui scegliere: (ruoli[primario eq "True"].display, roles[primary eq "True].type, and roles[primary eq "True"].value). È possibile scegliere di includere uno o tutti gli attributi di ruolo nei mapping. Se si desidera includerne più di uno, è sufficiente aggiungere un nuovo mapping e includerlo come attributo di destinazione.  
  
  ![Aggiungi SingleAppRoleAssignment](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **Aspetti da considerare**
    - Assicurarsi che più ruoli non siano assegnati a un utente. Non possiamo garantire quale ruolo verrà eseguito il provisioning.
    
  - **Output di esempio** 

   ```json
    {
      "schemas": [
          "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": true,
               "type": "WindowsAzureActiveDirectoryRole",
               "value": "Admin"
         }
      ]
   }
   ```
  
- **AppRoleAssignmentsComplex** 
  - **Quando utilizzare:** Usare l'espressione AppRoleAssignmentsComplex per eseguire il provisioning di più ruoli per un utente. 
  - **Come configurare:** Modificare l'elenco degli attributi supportati come descritto in precedenza per includere un nuovo attributo per i ruoli: 
  
    ![Aggiungere ruoli](./media/customize-application-attributes/add-roles.png)<br>

    Usare quindi l'espressione AppRoleAssignmentsComplex per eseguire il mapping all'attributo del ruolo personalizzato, come illustrato nell'immagine seguente:Then use the AppRoleAssignmentsComplex expression to map to the custom role attribute as shown in the image below:

    ![Aggiungere AppRoleAssignmentsComplexAdd AppRoleAssignmentsComplex](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Aspetti da considerare**
    - Il provisioning di tutti i ruoli verrà eseguito come primario , false.
    - Il POST contiene il tipo di ruolo. La richiesta PATCH non contiene il tipo. Stiamo lavorando per inviare il tipo nelle richieste POST e PATCH.
    
  - **Output di esempio** 
  
   ```json
   {
       "schemas": [
           "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "Admin",
               "value": "Admin"
         },
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "User",
             "value": "User"
         }
      ]
   }
   ```

  


## <a name="provisioning-a-multi-value-attribute"></a>Provisioning di un attributo multivalore
Alcuni attributi come phoneNumbers ed e-mail sono attributi multivalore in cui potrebbe essere necessario specificare diversi tipi di numeri di telefono o e-mail. Utilizzare l'espressione seguente per gli attributi multivalore. Consente di specificare il tipo di attributo ed eseguire il mapping all'attributo utente di Azure AD corrispondente per il valore. 

* phoneNumbers[type eq "work"].value
* phoneNumbers[type eq "mobile"].value
* phoneNumbers[type eq "fax"].value

   ```json
   "phoneNumbers": [
       {
         "value": "555-555-5555",
         "type": "work"
      },
      {
         "value": "555-555-5555",
         "type": "mobile"
      },
      {
         "value": "555-555-5555",
         "type": "fax"
      }
   ]
   ```

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Ripristino degli attributi e dei mapping degli attributi predefiniti

Se è necessario ricominciare e ripristinare lo stato predefinito dei mapping esistenti, è possibile selezionare la casella di controllo **Ripristina mapping predefiniti** e salvare la configurazione. In questo modo vengono impostati tutti i mapping e i filtri di ambito come se l'applicazione fosse stata appena aggiunta al tenant di Azure AD dalla raccolta di applicazioni.

La selezione di questa opzione forza in modo efficace una risincronizzazione di tutti gli utenti mentre il servizio di provisioning è in esecuzione.

> [!IMPORTANT]
> È consigliabile che **lo stato del provisioning** sia impostato su **Disattivato** prima di richiamare questa opzione.

## <a name="what-you-should-know"></a>Informazioni utili

- Microsoft Azure AD offre un'implementazione efficiente di un processo di sincronizzazione. In un ambiente inizializzato, durante un ciclo di sincronizzazione vengono elaborati solo gli oggetti che richiedono aggiornamenti.
- L'aggiornamento dei mapping degli attributi influisce negativamente sulle prestazioni di un ciclo di sincronizzazione. Un aggiornamento della configurazione del mapping degli attributi richiede la rivalutazione di tutti gli oggetti gestiti.
- Una procedura consigliata consiste nel ridurre al minimo il numero di modifiche consecutive ai mapping degli attributi.
- L'aggiunta di un attributo di foto di cui eseguire il provisioning a un'app non è supportata oggi, poiché non è possibile specificare il formato per la sincronizzazione della foto. È possibile richiedere la funzione su [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory)
- L'attributo IsSoftDeleted fa spesso parte dei mapping predefiniti per un'applicazione. IsSoftdeleted può essere true in uno dei quattro scenari (l'utente non rientra nell'ambito perché non è assegnato dall'applicazione, l'utente non è nell'ambito perché non soddisfa un filtro di ambito, l'utente è stato eliminato temporaneamente in Azure AD o la proprietà AccountEnabled è impostata su false per l'utente). 
- Il servizio di provisioning di Azure AD non supporta il provisioning di valori NullThe Azure AD provisioning service does not support provisioning null values

## <a name="next-steps"></a>Passaggi successivi

- [Automatizzare il provisioning e il deprovisioning utenti in app SaaS](user-provisioning.md)
- [Scrittura di espressioni per i mapping degli attributi](../app-provisioning/functions-for-customizing-application-data.md)
- [Ambito dei filtri per il Provisioning utente](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [Uso di SCIM per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory alle applicazioni](use-scim-to-provision-users-and-groups.md)
- [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](../saas-apps/tutorial-list.md)
