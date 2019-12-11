---
title: Personalizzazione dei mapping degli attributi Azure AD | Microsoft Docs
description: Informazioni sui mapping degli attributi per app SaaS in Azure Active Directory e su come è possibile modificarli in base alle esigenze aziendali.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 804eb63406b33b94e70ef56e0066fa213be04708
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997055"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personalizzazione dei mapping degli attributi per il provisioning degli utenti per le applicazioni SaaS in Azure Active Directory

Microsoft Azure AD fornisce il supporto per il provisioning degli utenti in applicazioni SaaS di terze parti, ad esempio Salesforce, G Suite e altri. Se si Abilita il provisioning utenti per un'applicazione SaaS di terze parti, il portale di Azure controlla i valori degli attributi tramite mapping degli attributi.

Esiste un set preconfigurato di attributi e mapping di attributi tra Azure AD oggetti utente e gli oggetti utente di ogni app SaaS. Alcune app gestiscono altri tipi di oggetti insieme agli utenti, ad esempio i gruppi.

È possibile personalizzare i mapping degli attributi predefiniti in base alle esigenze aziendali. È quindi possibile modificare o eliminare i mapping degli attributi esistenti o creare nuovi mapping degli attributi.

## <a name="editing-user-attribute-mappings"></a>Modifica dei mapping degli attributi utente

Per accedere alla funzionalità **mapping** del provisioning utenti, attenersi alla procedura seguente:

1. Accedere al portale di [Azure Active Directory](https://aad.portal.azure.com).
1. Selezionare **applicazioni aziendali** dal riquadro sinistro. Viene visualizzato un elenco di tutte le app configurate, incluse le app aggiunte dalla raccolta.
1. Selezionare un'app per caricare il riquadro di gestione delle app, in cui è possibile visualizzare i report e gestire le impostazioni dell'app.
1. Selezionare il **provisioning** per gestire le impostazioni di provisioning degli account utente per l'app selezionata.
1. Espandere **mapping** per visualizzare e modificare gli attributi utente che scorrono tra Azure ad e l'applicazione di destinazione. Se l'applicazione di destinazione la supporta, questa sezione consente di configurare facoltativamente il provisioning di gruppi e account utente.

   ![Usare i mapping per visualizzare e modificare gli attributi utente](./media/customize-application-attributes/21.png)

1. Selezionare una configurazione di **mapping** per aprire la schermata **mapping attributi** correlati. Alcuni mapping di attributi sono necessari per il corretto funzionamento di un'applicazione SaaS. Per gli attributi obbligatori la funzionalità **Elimina** non è disponibile.

   ![Usare il mapping degli attributi per configurare i mapping degli attributi per le app](./media/customize-application-attributes/22.png)

   In questa schermata è possibile vedere che l'attributo **username** di un oggetto gestito in Salesforce è popolato con il valore **userPrincipalName** dell'oggetto Azure Active Directory collegato.

1. Selezionare un **mapping di attributi** esistente per aprire la schermata **modifica attributo** . Qui è possibile modificare gli attributi utente che scorrono tra Azure AD e l'applicazione di destinazione.

   ![Usare modifica attributo per modificare gli attributi utente](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Informazioni sui tipi di mapping degli attributi

I mapping degli attributi permettono di controllare il modo in cui gli attribuiti vengono popolati in un'applicazione SaaS di terze parti.
Sono supportati quattro diversi tipi di mapping:

- **Diretto:** l'attributo di destinazione viene popolato con il valore di un attributo dell'oggetto collegato in Azure AD.
- **Costante** : l'attributo di destinazione viene popolato con una stringa specifica specificata.
- **Espressione:** l'attributo di destinazione viene popolato in base al risultato di un'espressione simile a uno script.
  Per altre informazioni, vedere [Scrittura di espressioni per il mapping degli attributi in Azure Active Directory](functions-for-customizing-application-data.md).
- **Nessuno:** l'attributo di destinazione viene lasciato invariato. Tuttavia, se l'attributo di destinazione è sempre vuoto, viene popolato con il valore predefinito specificato.

Insieme a questi quattro tipi di base, i mapping di attributi personalizzati supportano il concetto di assegnazione di un valore **predefinito** facoltativo. L'assegnazione di valori predefiniti garantisce che un attributo di destinazione venga popolato con un valore se non è presente un valore in Azure AD o nell'oggetto di destinazione. Nella maggior parte delle configurazioni questo campo viene lasciato vuoto.

### <a name="understanding-attribute-mapping-properties"></a>Informazioni sulle proprietà di mapping degli attributi

Nella sezione precedente è già stata introdotta la proprietà tipo mapping attributi.
Insieme a questa proprietà, i mapping degli attributi supportano anche gli attributi seguenti:

- **Attributo di origine**: attributo utente nel sistema di origine, ad esempio Azure Active Directory.
- **Attributo di destinazione**: attributo utente nel sistema di destinazione, ad esempio ServiceNow.
- Trova la **corrispondenza con gli oggetti che usano questo attributo** : indica se questo mapping deve essere usato per identificare in modo univoco gli utenti tra i sistemi di origine e di destinazione. Viene in genere impostato sull'attributo userPrincipalName o mail in Azure AD, che in genere viene mappato a un campo username in un'applicazione di destinazione.
- **Precedenza abbinamento**: è possibile impostare più attributi corrispondenti. Quando sono presenti più, vengono valutati nell'ordine definito da questo campo. Quando viene rilevata una corrispondenza la valutazione degli attributi corrispondenti termina.
- **Applica questo mapping**
  - **Sempre** : applica il mapping sia alle azioni di creazione che di aggiornamento dell'utente.
  - **Solo durante la creazione** : applicare questo mapping solo alle azioni di creazione dell'utente.

## <a name="matching-users-in-the-source-and-target--systems"></a>Corrispondenza degli utenti nei sistemi di origine e di destinazione
Il servizio Azure AD provisioning può essere distribuito in scenari "Greenfield" (dove gli utenti non vengono chiusi nel sistema di destinazione) e in scenari "Brownfield" (dove gli utenti esistono già nel sistema di destinazione). Per supportare entrambi gli scenari, il servizio di provisioning usa il concetto di attributi corrispondenti. Gli attributi corrispondenti consentono di determinare come identificare in modo univoco un utente nell'origine e associare l'utente nella destinazione. Nell'ambito della pianificazione della distribuzione, identificare l'attributo che può essere usato per identificare in modo univoco un utente nei sistemi di origine e di destinazione. Aspetti da considerare:

- **Gli attributi corrispondenti devono essere univoci:** I clienti spesso utilizzano attributi come userPrincipalName, mail o ID oggetto come attributo corrispondente.
- **È possibile utilizzare più attributi come attributi corrispondenti:** È possibile definire più attributi da valutare quando si abbinano gli utenti e l'ordine in cui vengono valutati (definito come precedenza corrispondente nell'interfaccia utente). Se, ad esempio, si definiscono tre attributi come attributi corrispondenti e un utente viene associato in modo univoco dopo aver valutato i primi due attributi, il servizio non valuterà il terzo attributo. Il servizio valuterà gli attributi corrispondenti nell'ordine specificato e arresterà la valutazione quando viene trovata una corrispondenza.  
- **Non è necessario che il valore nell'origine e nella destinazione corrisponda esattamente a** quanto segue: Il valore nella destinazione può essere una funzione semplice del valore nell'origine. Quindi, è possibile che sia presente un attributo emailAddress nell'origine e userPrincipalName nella destinazione e corrisponda a una funzione dell'attributo emailAddress che sostituisce alcuni caratteri con un valore costante.  
- **La corrispondenza basata su una combinazione di attributi non è supportata:** La maggior parte delle applicazioni non supporta l'esecuzione di query in base a due proprietà. Pertanto, non è possibile trovare una corrispondenza in base a una combinazione di attributi. È possibile valutare le singole proprietà dopo l'altra.
- **Tutti gli utenti devono avere un valore per almeno un attributo corrispondente:** Se si definisce un attributo corrispondente, tutti gli utenti devono disporre di un valore per tale attributo nel sistema di origine. Se, ad esempio, si definisce userPrincipalName come attributo corrispondente, tutti gli utenti devono disporre di un userPrincipalName. Se si definisce più attributi corrispondenti (ad esempio extensionAttribute1 e mail), non tutti gli utenti devono avere lo stesso attributo corrispondente. Un utente può avere un extensionAttribute1 ma non un messaggio di posta elettronica, mentre un altro utente può avere la posta elettronica ma non extensionAttribute1. 
- **L'applicazione di destinazione deve supportare l'applicazione di filtri all'attributo corrispondente:** Gli sviluppatori di applicazioni consentono di filtrare un subset di attributi nell'API utente o gruppo. Per le applicazioni nella raccolta, si garantisce che il mapping predefinito degli attributi sia per un attributo di cui l'API dell'applicazione di destinazione supporta il filtraggio. Quando si modifica l'attributo corrispondente predefinito per l'applicazione di destinazione, controllare la documentazione dell'API di terze parti per assicurarsi che l'attributo possa essere filtrato in base a.  

## <a name="editing-group-attribute-mappings"></a>Modifica dei mapping degli attributi gruppo

Un numero selezionato di applicazioni, ad esempio ServiceNow, box e G Suite, supporta la possibilità di effettuare il provisioning di oggetti gruppo e oggetti utente. Gli oggetti Group possono contenere proprietà del gruppo, ad esempio i nomi visualizzati e gli alias di posta elettronica, insieme ai membri del gruppo.

![Esempio Mostra ServiceNow con gli oggetti gruppo e utente di cui è stato effettuato il provisioning](./media/customize-application-attributes/24.png)

Facoltativamente, è possibile abilitare o disabilitare il provisioning dei gruppi selezionando il mapping del gruppo in **mapping**e impostando **abilitato** sull'opzione desiderata nella schermata **mapping attributi** .

Gli attributi di cui viene effettuato il provisioning come parte di oggetti gruppo possono essere personalizzati allo stesso modo degli oggetti utente, descritto in precedenza. 

> [!TIP]
> Il provisioning di oggetti gruppo (proprietà e membri) è un concetto distinto dall'[assegnazione di gruppi](assign-user-or-group-access-portal.md) a un'applicazione. Benché sia possibile assegnare un gruppo a un'applicazione, è possibile effettuare il provisioning solo degli oggetti utente contenuti nel gruppo. Il provisioning di oggetti gruppo completi non è necessario per usare i gruppi nelle assegnazioni.

## <a name="editing-the-list-of-supported-attributes"></a>Modifica dell'elenco degli attributi supportati

Gli attributi utente supportati per un'applicazione specifica sono preconfigurati. La maggior parte delle API di gestione utente dell'applicazione non supporta l'individuazione dello schema. Il servizio di provisioning Azure AD non è quindi in grado di generare dinamicamente l'elenco degli attributi supportati effettuando chiamate all'applicazione.

Tuttavia, alcune applicazioni supportano attributi personalizzati e il servizio di provisioning Azure AD può leggere e scrivere in attributi personalizzati. Per immettere le definizioni nella portale di Azure, selezionare la casella di controllo **Mostra opzioni avanzate** nella parte inferiore della schermata **mapping attributi** , quindi selezionare **modifica elenco attributi per** l'app.

Alcuni sistemi e applicazioni che supportano la personalizzazione dell'elenco di attributi:

- Salesforce
- ServiceNow
- Workday
- Azure Active Directory (sono supportati gli [attributi predefiniti dell'API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) e le estensione della directory personalizzate)
- App che supportano [SCIM 2.0](https://tools.ietf.org/html/rfc7643), in cui devono essere aggiunti gli attributi definiti nello [schema di base](https://tools.ietf.org/html/rfc7643)

> [!NOTE]
> La modifica dell'elenco degli attributi supportati è consigliata solo agli amministratori che hanno personalizzato lo schema dei propri sistemi e applicazioni e che hanno una conoscenza diretta del modo in cui sono stati definiti gli attributi personalizzati. Questa operazione richiede a volte familiarità con le API e gli strumenti per sviluppatori forniti da un'applicazione o un sistema.

Quando si modifica l'elenco degli attributi supportati, vengono fornite le proprietà seguenti:

- **Nome**: nome di sistema dell'attributo, definito nello schema dell'oggetto di destinazione.
- **Tipo** : tipo di dati archiviato dall'attributo, come definito nello schema dell'oggetto di destinazione, che può essere uno dei tipi seguenti:
  - *Binario*: l'attributo contiene dati binari.
  - *Booleano*: l'attributo contiene un valore True o False.
  - *DateTime*: l'attributo contiene una stringa di data.
  - *Integer*: l'attributo contiene un numero intero.
  - *Riferimento*: l'attributo contiene un ID che fa riferimento a un valore archiviato in un'altra tabella nell'applicazione di destinazione.
  - *String*: l'attributo contiene una stringa di testo.
- **Chiave primaria?** : -Indica se l'attributo è definito come campo di chiave primaria nello schema dell'oggetto di destinazione.
- **Obbligatorio?** -Indica se è necessario che l'attributo venga popolato nell'applicazione o nel sistema di destinazione.
- **Multivalore?** : Indica se l'attributo supporta più valori.
- **Maiuscole/minuscole esatte?** : Indica se i valori degli attributi vengono valutati con distinzione tra maiuscole e minuscole.
- **Espressione API** : non usare, a meno che non sia stato richiesto dalla documentazione per un connettore di provisioning specifico, ad esempio la giornata lavorativa.
- **Attributo oggetto a cui si fa riferimento** : se è un attributo di tipo riferimento, questo menu consente di selezionare la tabella e l'attributo nell'applicazione di destinazione che contiene il valore associato all'attributo. Ad esempio, in presenza di un attributo denominato "Reparto" il cui valore archiviato fa riferimento a un oggetto in una tabella "Reparti" separata, sarà necessario selezionare "Reparti.Nome". Le tabelle di riferimento e i campi ID primari supportati per un'applicazione specifica sono preconfigurati e attualmente non possono essere modificati usando il portale di Azure, ma possono essere modificati usando il [API Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>Provisioning di un attributo di estensione personalizzato in un'applicazione conforme a SCIM
SCIM RFC definisce uno schema di utenti e gruppi di base, consentendo allo schema anche le estensioni per soddisfare le esigenze dell'applicazione. Per aggiungere un attributo personalizzato a un'applicazione SCIM:
   1. Accedere al portale di [Azure Active Directory](https://aad.portal.azure.com), selezionare **applicazioni aziendali**, selezionare l'applicazione e quindi selezionare **provisioning**.
   2. In **mapping**selezionare l'oggetto (utente o gruppo) per il quale si vuole aggiungere un attributo personalizzato.
   3. Nella parte inferiore della pagina selezionare **Mostra opzioni avanzate**.
   4. Selezionare **modifica elenco attributi per AppName**.
   5. Nella parte inferiore dell'elenco di attributi immettere le informazioni sull'attributo personalizzato nei campi specificati. Quindi selezionare **Aggiungi attributo**.

Per le applicazioni SCIM, il nome dell'attributo deve seguire il modello illustrato nell'esempio riportato di seguito. "CustomExtensionName" e "CustomAttribute" possono essere personalizzati in base ai requisiti dell'applicazione, ad esempio: urn: IETF: params: SCIM: schemas: Extension: 2.0: CustomExtensionName: CustomAttribute

Queste istruzioni sono valide solo per le applicazioni abilitate per SCIM. Le applicazioni, ad esempio ServiceNow e Salesforce, non sono integrate con Azure AD usando SCIM e pertanto non richiedono questo spazio dei nomi specifico quando si aggiunge un attributo personalizzato.

Gli attributi personalizzati non possono essere attributi referenziali o attributi multivalore. Gli attributi di estensione multivalore personalizzati sono attualmente supportati solo per le applicazioni nella raccolta.  
 
**Rappresentazione di esempio di un utente con un attributo di estensione:**

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
Attenersi alla procedura seguente per eseguire il provisioning dei ruoli per un utente nell'applicazione. Si noti che la descrizione seguente è specifica per le applicazioni SCIM personalizzate. Per le applicazioni della raccolta, ad esempio Salesforce e ServiceNow, usare i mapping dei ruoli predefiniti. I punti elenco seguenti descrivono come trasformare l'attributo AppRoleAssignments nel formato previsto dall'applicazione.

- Per eseguire il mapping di un appRoleAssignment in Azure AD a un ruolo nell'applicazione, è necessario trasformare l'attributo usando un' [espressione](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data). L'attributo appRoleAssignment **non deve essere mappato direttamente** a un attributo Role senza utilizzare un'espressione per analizzare i dettagli del ruolo. 

- **SingleAppRoleAssignment** 
  - **Quando usare:** Usare l'espressione SingleAppRoleAssignment per eseguire il provisioning di un singolo ruolo per un utente e per specificare il ruolo primario. 
  - **Come configurare:** Utilizzare la procedura descritta in precedenza per passare alla pagina mapping attributi e utilizzare l'espressione SingleAppRoleAssignment per eseguire il mapping all'attributo roles. È possibile scegliere tra tre attributi di ruolo: (Roles [Primary EQ "true"]. display, Roles [Primary EQ "true]. Type e Roles [Primary EQ" true "]. Value). È possibile scegliere di includere uno o tutti gli attributi del ruolo nei mapping. Se si desidera includere più di uno, è sufficiente aggiungere un nuovo mapping e includerlo come attributo di destinazione.  
  
  ![Aggiungi SingleAppRoleAssignment](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **Aspetti da considerare**
    - Assicurarsi che a un utente non siano assegnati più ruoli. Non è possibile garantire quale ruolo verrà sottoposto a provisioning.
    
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
  - **Quando usare:** Usare l'espressione AppRoleAssignmentsComplex per eseguire il provisioning di più ruoli per un utente. 
  - **Come configurare:** Modificare l'elenco degli attributi supportati come descritto in precedenza per includere un nuovo attributo per i ruoli: 
  
    ![Aggiungere ruoli](./media/customize-application-attributes/add-roles.png)<br>

    Usare quindi l'espressione AppRoleAssignmentsComplex per eseguire il mapping all'attributo del ruolo personalizzato, come illustrato nell'immagine seguente:

    ![Aggiungi AppRoleAssignmentsComplex](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Aspetti da considerare**
    - Verrà eseguito il provisioning di tutti i ruoli come Primary = false.
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
Alcuni attributi, ad esempio phoneNumbers e messaggi di posta elettronica, sono attributi multivalore, in cui potrebbe essere necessario specificare tipi diversi di numeri di telefono o messaggi di posta elettronica. Utilizzare l'espressione seguente per gli attributi multivalore. Consente di specificare il tipo di attributo ed eseguirne il mapping all'attributo utente Azure AD corrispondente per il valore. 

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

Se è necessario ricominciare e reimpostare i mapping esistenti sullo stato predefinito, è possibile selezionare la casella di controllo **Ripristina mapping predefiniti** e salvare la configurazione. In questo modo, tutti i mapping vengono impostati come se l'applicazione venisse appena aggiunta al tenant di Azure AD dalla raccolta di applicazioni.

Se si seleziona questa opzione, verrà forzata una risincronizzazione di tutti gli utenti durante l'esecuzione del servizio di provisioning.

> [!IMPORTANT]
> È consigliabile impostare **lo stato di provisioning** su **disattivato** prima di richiamare questa opzione.

## <a name="what-you-should-know"></a>Informazioni utili

- Microsoft Azure AD offre un'implementazione efficiente di un processo di sincronizzazione. In un ambiente inizializzato, durante un ciclo di sincronizzazione vengono elaborati solo gli oggetti che richiedono aggiornamenti.
- L'aggiornamento dei mapping degli attributi influisce negativamente sulle prestazioni di un ciclo di sincronizzazione. Un aggiornamento della configurazione del mapping degli attributi richiede la rivalutazione di tutti gli oggetti gestiti.
- Una procedura consigliata consiste nel limitare al minimo il numero di modifiche consecutive ai mapping degli attributi.
- L'aggiunta di un attributo Photo per eseguire il provisioning in un'app non è attualmente supportata perché non è possibile specificare il formato per sincronizzare la foto. È possibile richiedere la funzionalità nella [voce utente](https://feedback.azure.com/forums/169401-azure-active-directory)
- L'attributo IsSoftDeleted fa spesso parte dei mapping predefiniti per un'applicazione. IsSoftdeleted può essere true in uno dei quattro scenari (l'utente è esterno all'ambito perché non è stato assegnato dall'applicazione, l'utente è esterno all'ambito a causa del mancato rispetto di un filtro di ambito, l'utente è stato eliminato temporaneamente in Azure AD o la proprietà AccountEnabled è impostata su false  utente). 
- Il servizio di provisioning Azure AD non supporta il provisioning di valori null

## <a name="next-steps"></a>Passaggi successivi

- [Automatizzare il provisioning e il deprovisioning utenti in app SaaS](user-provisioning.md)
- [Scrittura di espressioni per i mapping degli attributi](functions-for-customizing-application-data.md)
- [Ambito dei filtri per il Provisioning utente](define-conditional-rules-for-provisioning-user-accounts.md)
- [Uso di SCIM per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory alle applicazioni](use-scim-to-provision-users-and-groups.md)
- [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](../saas-apps/tutorial-list.md)
