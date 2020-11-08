---
title: Personalizzazione dei mapping degli attributi di Azure AD | Microsoft Docs
description: Informazioni sui mapping degli attributi per app SaaS in Azure Active Directory e su come è possibile modificarli in base alle esigenze aziendali.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: kenwith
ms.openlocfilehash: 2f21e4f41814b47d8e630df72c255886ac2af53b
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94364293"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personalizzazione dei mapping degli attributi del provisioning utenti per le applicazioni SaaS in Azure Active Directory

Microsoft Azure AD offre il supporto per il provisioning degli utenti in applicazioni SaaS di terze parti, ad esempio Salesforce, G Suite e altre. Se si abilita il provisioning degli utenti per un'applicazione SaaS di terze parti, il portale di Azure ne controlla i valori degli attributi usando il mapping degli attributi.

Esiste un set preconfigurato di attributi e mapping degli attributi tra gli oggetti utente di Azure AD e gli oggetti utente di ogni app SaaS. Alcune app gestiscono altri tipi di oggetti insieme a utenti e gruppi.

È possibile personalizzare i mapping degli attributi predefiniti in base alle esigenze aziendali. Quindi è possibile modificare o eliminare i mapping degli attributi esistenti oppure crearne di nuovi.

## <a name="editing-user-attribute-mappings"></a>Modifica dei mapping degli attributi utente

Attenersi alla procedura seguente per accedere alla funzionalità **Mapping** del provisioning utenti:

1. Accedere al [portale di Azure Active Directory](https://aad.portal.azure.com).
1. Selezionare **Applicazioni aziendali** nel riquadro sinistro. Viene visualizzato un elenco di tutte le app configurate, incluse le app aggiunte dalla raccolta.
1. Selezionare un'app per caricare il relativo riquadro di gestione, in cui è possibile visualizzare i report e gestire le impostazioni dell'app.
1. Selezionare **Provisioning** per gestire le impostazioni di provisioning degli account utente per l'app selezionata.
1. Espandere **Mapping** per visualizzare e modificare gli attributi utente che scorrono tra Azure AD e l'applicazione di destinazione. Se l'applicazione di destinazione supporta tale possibilità, questa sezione consente anche di configurare il provisioning dei gruppi e degli account utente.

   ![Usare i mapping per visualizzare e modificare gli attributi utente](./media/customize-application-attributes/21.png)

1. Selezionare una configurazione **Mapping** per aprire la schermata **Mapping attributi** corrispondente. Alcuni mapping di attributi sono obbligatori per il corretto funzionamento di un'applicazione SaaS. Per gli attributi obbligatori la funzionalità **Elimina** non è disponibile.

   ![Usare Mapping attributi per configurare i mapping degli attributi per le app](./media/customize-application-attributes/22.png)

   In questo screenshot si può notare che l'attributo **Username** di un oggetto gestito in Salesforce è popolato con il valore **userPrincipalName** dell'oggetto di Azure Active Directory collegato.

1. Selezionare un **mapping degli attributi** esistente per aprire la schermata **Modifica attributo** , in cui è possibile modificare gli attributi utente che scorrono tra Azure AD e l'applicazione di destinazione.

   ![Usare Modifica attributo per modificare gli attributi utente](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Informazioni sui tipi di mapping degli attributi

I mapping degli attributi permettono di controllare il modo in cui gli attribuiti vengono popolati in un'applicazione SaaS di terze parti.
Sono supportati quattro diversi tipi di mapping:

- **Diretto:** l'attributo di destinazione viene popolato con il valore di un attributo dell'oggetto collegato in Azure AD.
- **Costante:** l'attributo di destinazione viene popolato con una stringa specificata.
- **Espressione:** l'attributo di destinazione viene popolato in base al risultato di un'espressione simile a uno script.
  Per altre informazioni, vedere [Scrittura di espressioni per il mapping degli attributi in Azure Active Directory](../app-provisioning/functions-for-customizing-application-data.md).
- **Nessuno:** l'attributo di destinazione viene lasciato invariato. Se l'attributo di destinazione viene lasciato vuoto, viene popolato con il valore predefinito specificato.

Oltre a questi quattro tipi base, i mapping degli attributi personalizzati supportano il concetto di assegnazione di un valore **predefinito** facoltativo. L'assegnazione di un valore predefinito garantisce che un attributo di destinazione venga popolato con un valore nel caso in cui non sia presente un valore in Azure AD o nell'oggetto di destinazione. Nella maggior parte delle configurazioni questo campo viene lasciato vuoto.

### <a name="understanding-attribute-mapping-properties"></a>Informazioni sulle proprietà di mapping degli attributi

Nella sezione precedente è già stata presentata la proprietà del tipo di mapping degli attributi.
Oltre a questa proprietà i mapping degli attributi supportano i seguenti attributi:

- **Attributo di origine** : attributo utente nel sistema di origine, ad esempio Azure Active Directory.
- **Attributo di destinazione** : attributo utente nel sistema di destinazione, ad esempio ServiceNow.
- **Valore predefinito se Null (facoltativo)** : valore che verrà passato al sistema di destinazione se l'attributo di origine è Null. Il provisioning di questo valore verrà eseguito solo quando viene creato un utente. Il provisioning del "valore predefinito se Null" non verrà eseguito quando si aggiorna un utente esistente. Se, ad esempio, si vuole eseguire il provisioning di tutti gli utenti esistenti nel sistema di destinazione con una posizione (jobTitle) specifica (quando è Null nel sistema di origine), è possibile usare l'[espressione](../app-provisioning/functions-for-customizing-application-data.md) seguente: Switch(IsPresent([jobTitle]), "DefaultValue", "True", [jobTitle]). Assicurarsi di sostituire il "valore predefinito" con quello di cui si vuole eseguire il provisioning quando è Null nel sistema di origine. 
- **Abbina gli oggetti in base a questo attributo** : specifica se questo mapping va usato per l'identificazione univoca degli utenti tra il sistema di origine e il sistema di destinazione. Questa opzione è in genere impostata sull'attributo userPrincipalName o mail in Azure AD, che a sua volta è in genere mappato a un campo username in un'applicazione di destinazione.
- **Precedenza abbinamento** : è possibile impostare più attributi corrispondenti. Se sono presenti più attributi, vengono valutati nell'ordine definito da questo campo. Quando viene rilevata una corrispondenza la valutazione degli attributi corrispondenti termina. Sebbene sia possibile impostare qualsiasi numero di attributi corrispondenti, valutare se gli attributi che si stanno usando come attributi corrispondenti sono realmente univoci e devono essere attributi corrispondenti. In genere i clienti hanno 1 o 2 attributi corrispondenti nella propria configurazione. 
- **Applica questo mapping**
  - **Sempre** : applica il mapping sia all'azione di creazione che all'azione di aggiornamento dell'utente.
  - **Solo durante la creazione** : applica il mapping solo alle azioni di creazione dell'utente.

## <a name="matching-users-in-the-source-and-target--systems"></a>Corrispondenza degli utenti nei sistemi di origine e di destinazione
Il servizio di provisioning di Azure AD può essere distribuito sia in scenari "Greenfield" (dove gli utenti non esistono nel sistema di destinazione), sia in scenari "Brownfield" (dove gli utenti esistono già nel sistema di destinazione). Per supportare entrambi gli scenari, il servizio di provisioning usa il concetto di attributi corrispondenti. Gli attributi corrispondenti consentono di determinare come identificare in modo univoco un utente nell'origine e associare l'utente nella destinazione. Nell'ambito della pianificazione della distribuzione, identificare l'attributo che può essere usato per identificare in modo univoco un utente nei sistemi di origine e di destinazione. Aspetti da considerare:

- **Gli attributi corrispondenti devono essere univoci:** I clienti spesso usano attributi come userPrincipalName, mail o ID oggetto come attributo corrispondente.
- **È possibile usare più attributi come attributi corrispondenti:** È possibile definire più attributi da valutare quando si abbinano gli utenti e l'ordine in cui vengono valutati (definito come precedenza abbinamento nell'interfaccia utente). Se, ad esempio, si definiscono tre attributi come attributi corrispondenti e un utente viene abbinato in modo univoco dopo aver valutato i primi due attributi, il servizio non valuterà il terzo attributo. Il servizio valuterà gli attributi corrispondenti nell'ordine specificato e arresterà la valutazione quando viene trovata una corrispondenza.  
- **Non è necessario che il valore nell'origine e nella destinazione corrispondano esattamente:** Il valore nella destinazione può essere una funzione semplice del valore nell'origine. Quindi, si può avere un attributo emailAddress nell'origine e userPrincipalName nella destinazione e una corrispondenza in base a una funzione dell'attributo emailAddress che sostituisce alcuni caratteri con un valore costante.  
- **La corrispondenza basata su una combinazione di attributi non è supportata:** La maggior parte delle applicazioni non supporta l'esecuzione di query in base a due proprietà. Di conseguenza, non è possibile trovare una corrispondenza in base a una combinazione di attributi. È possibile valutare le singole proprietà una dopo l'altra.
- **Tutti gli utenti devono avere un valore per almeno un attributo corrispondente:** Se si definisce un attributo corrispondente, tutti gli utenti devono avere un valore per tale attributo nel sistema di origine. Se, ad esempio, si definisce userPrincipalName come attributo corrispondente, tutti gli utenti devono avere un userPrincipalName. Se si definiscono più attributi corrispondenti, ad esempio extensionAttribute1 e mail, non tutti gli utenti devono avere lo stesso attributo corrispondente. Un utente può avere extensionAttribute1 ma non mail e un altro utente può avere mail ma non extensionAttribute1. 
- **L'applicazione di destinazione deve supportare i filtri per l'attributo corrispondente:** Gli sviluppatori di applicazioni consentono il filtraggio per un subset di attributi nell'API utente o gruppo. Per le applicazioni nella raccolta, si garantisce che il mapping predefinito degli attributi sia per un attributo di cui l'API dell'applicazione di destinazione supporta il filtraggio. Quando si modifica l'attributo corrispondente predefinito per l'applicazione di destinazione, consultare la documentazione dell'API di terze parti per verificare che all'attributo si possa applicare un filtro.  

## <a name="editing-group-attribute-mappings"></a>Modifica dei mapping degli attributi gruppo

Un numero selezionato di applicazioni, tra cui ServiceNow, Box e G Suite, supporta la possibilità di effettuare il provisioning di oggetti gruppo e oggetti utente. Oltre ai membri del gruppo, gli oggetti gruppo possono contenere proprietà di gruppo come i nomi visualizzati e gli alias di posta elettronica.

![L'esempio illustra ServiceNow con oggetti gruppo e utente di cui è stato effettuato il provisioning](./media/customize-application-attributes/24.png)

Il provisioning dei gruppi può essere facoltativamente abilitato o disabilitato selezionando il mapping dei gruppi in **Mapping** e impostando **Abilitato** su un'opzione nella schermata **Mapping attributi**.

Gli attributi di cui viene effettuato il provisioning come parte di oggetti gruppo possono essere personalizzati allo stesso modo degli oggetti utente, descritto in precedenza. 

> [!TIP]
> Il provisioning di oggetti gruppo (proprietà e membri) è un concetto distinto dall'[assegnazione di gruppi](../manage-apps/assign-user-or-group-access-portal.md) a un'applicazione. Benché sia possibile assegnare un gruppo a un'applicazione, è possibile effettuare il provisioning solo degli oggetti utente contenuti nel gruppo. Il provisioning di oggetti gruppo completi non è necessario per usare i gruppi nelle assegnazioni.

## <a name="editing-the-list-of-supported-attributes"></a>Modifica dell'elenco degli attributi supportati

Gli attributi utente supportati per un'applicazione specifica sono preconfigurati. La maggior parte delle API di gestione utente dell'applicazione non supporta l'individuazione dello schema. Quindi, il servizio di provisioning di Azure AD non è in grado di generare dinamicamente l'elenco degli attributi supportati effettuando chiamate all'applicazione.

Tuttavia, alcune applicazioni supportano gli attributi personalizzati e il servizio di provisioning di Azure AD può leggere e scrivere negli attributi personalizzati. Per immettere le relative definizioni nel portale di Azure, selezionare la casella di controllo **Mostra opzioni avanzate** nella parte inferiore della schermata **Mapping attributi** e quindi selezionare **Modifica elenco attributi** per l'app.

Alcuni sistemi e applicazioni che supportano la personalizzazione dell'elenco di attributi:

- Salesforce
- ServiceNow
- Giorni lavorativi Active Directory/giorni lavorativi per Azure Active Directory
- Da SuccessFactors a Active Directory/SuccessFactors Azure Active Directory
- Azure Active Directory (sono supportati gli [attributi predefiniti dell'API Graph di Azure AD](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#user-entity) e le estensione della directory personalizzate)
- App che supportano [SCIM 2,0](https://tools.ietf.org/html/rfc7643)
- Per Azure Active Directory writeback per la giornata lavorativa o SuccessFactors, è supportato l'aggiornamento dei metadati rilevanti per gli attributi supportati (XPATH e JSONPath), ma non è supportata l'aggiunta di nuovi attributi di giorno lavorativo o SuccessFactors oltre a quelli inclusi nello schema predefinito


> [!NOTE]
> La modifica dell'elenco degli attributi supportati è consigliata solo agli amministratori che hanno personalizzato lo schema dei propri sistemi e applicazioni e che hanno una conoscenza diretta del modo in cui sono stati definiti gli attributi personalizzati. Questa operazione richiede a volte familiarità con le API e gli strumenti per sviluppatori forniti da un'applicazione o un sistema.

Quando si modifica l'elenco degli attributi supportati, vengono fornite le proprietà seguenti:

- **Nome** : nome di sistema dell'attributo, definito nello schema dell'oggetto di destinazione.
- **Tipo** : tipo di dati archiviati dall'attributo, definito nello schema dell'oggetto di destinazione, che può essere uno dei seguenti:
  - *Binario* : l'attributo contiene dati binari.
  - *Booleano* : l'attributo contiene un valore True o False.
  - *DateTime* : l'attributo contiene una stringa di data.
  - *Integer* : l'attributo contiene un numero intero.
  - *Riferimento* : l'attributo contiene un ID che fa riferimento a un valore archiviato in un'altra tabella nell'applicazione di destinazione.
  - *String* : l'attributo contiene una stringa di testo.
- **Chiave primaria?** : specifica se l'attributo è definito come campo di chiave primaria nello schema dell'oggetto di destinazione.
- **Obbligatorio?** specifica se l'attributo deve essere immesso nell'applicazione o nel sistema di destinazione.
- **Multivalore?** : specifica se l'attributo supporta più valori.
- **Maiuscole/minuscole esatte?** : specifica se i valori degli attributi vengono valutati facendo distinzione tra maiuscole e minuscole.
- **Espressione API** : non usare questa opzione a meno che non sia specificato nella documentazione per un determinato connettore di provisioning, ad esempio Workday.
- **Attributo oggetto di riferimento** : se l'attributo è di tipo Riferimento, in questo menu è possibile selezionare la tabella e l'attributo nell'applicazione di destinazione che contiene il valore associato all'attributo. Ad esempio, in presenza di un attributo denominato "Reparto" il cui valore archiviato fa riferimento a un oggetto in una tabella "Reparti" separata, sarà necessario selezionare "Reparti.Nome". Le tabelle di riferimento e i campi degli ID primari supportati per un'applicazione specifica sono preconfigurati e attualmente non possono essere modificati tramite il portale di Azure, ma possono essere modificati usando l'[API Microsoft Graph](/graph/api/resources/synchronization-configure-with-custom-target-attributes).

#### <a name="provisioning-a-custom-extension-attribute-to-a-scim-compliant-application"></a>Provisioning di un attributo di estensione personalizzato in un'applicazione conforme a SCIM
SCIM RFC definisce uno schema di utenti e gruppi centrale, consentendo anche estensioni dello schema per soddisfare le esigenze dell'applicazione. Per aggiungere un attributo personalizzato a un'applicazione SCIM:
   1. Accedere al [portale di Azure Active Directory](https://aad.portal.azure.com), selezionare **Applicazioni aziendali** , selezionare l'applicazione e quindi selezionare **Provisioning**.
   2. In **Mapping** selezionare l'oggetto (utente o gruppo) per il quale si vuole aggiungere un attributo personalizzato.
   3. Nella parte inferiore della pagina selezionare **Mostra opzioni avanzate**.
   4. Selezionare **Modifica elenco attributi per AppName**.
   5. Nella parte inferiore dell'elenco di attributi immettere le informazioni sull'attributo personalizzato nei campi disponibili. Quindi selezionare **Aggiungi attributo**.

Per le applicazioni SCIM, il nome dell'attributo deve seguire il modello illustrato nell'esempio riportato di seguito. "CustomExtensionName" e "CustomAttribute" possono essere personalizzati in base ai requisiti dell'applicazione, ad esempio: urn: IETF: params: SCIM: schemas: Extension: CustomExtensionName: 2.0: User: CustomAttribute 

Queste istruzioni sono valide solo per le applicazioni abilitate per SCIM. Applicazioni come ServiceNow e Salesforce non vengono integrate con Azure AD usando SCIM e quindi non richiedono questo spazio dei nomi specifico quando si aggiunge un attributo personalizzato.

Gli attributi personalizzati non possono essere attributi referenziali, attributi multivalore o tipi complessi. Gli attributi personalizzati di estensione multivalore e con tipi complessi sono attualmente supportati solo per le applicazioni nella raccolta.  
 
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
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User": {
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

- Per eseguire il mapping di un appRoleAssignment in Azure AD a un ruolo nell'applicazione, è necessario trasformare l'attributo usando un'[espressione](../app-provisioning/functions-for-customizing-application-data.md). L'attributo appRoleAssignment **non deve essere mappato direttamente** a un attributo Role senza usare un'espressione per analizzare i dettagli del ruolo. 

- **SingleAppRoleAssignment** 
  - **Uso consigliato:** Usare l'espressione SingleAppRoleAssignment per eseguire il provisioning di un singolo ruolo per un utente e per specificare il ruolo primario. 
  - **Modalità di configurazione:** Usare la procedura descritta in precedenza per passare alla pagina dei mapping degli attributi e usare l'espressione SingleAppRoleAssignment per eseguire il mapping all'attributo Role. Si può scegliere tra tre attributi Role: (roles[primary eq "True"].display, roles[primary eq "True].type e roles[primary eq "True"].value). È possibile scegliere di includere uno o tutti gli attributi Role nei mapping. Se si vuole includere più di un attributo, è sufficiente aggiungere un nuovo mapping e includerlo come attributo di destinazione.  
  
  ![Aggiungere SingleAppRoleAssignment](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
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
  - **Uso consigliato:** Usare l'espressione AppRoleAssignmentsComplex per eseguire il provisioning di più ruoli per un utente. 
  - **Modalità di configurazione:** Modificare l'elenco degli attributi supportati come descritto sopra per includere un nuovo attributo per i ruoli: 
  
    ![Aggiungere ruoli](./media/customize-application-attributes/add-roles.png)<br>

    Usare quindi l'espressione AppRoleAssignmentsComplex per eseguire il mapping all'attributo Role personalizzato, come illustrato nell'immagine seguente:

    ![Aggiungere AppRoleAssignmentsComplex](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Aspetti da considerare**
    - Tutti i ruoli verranno sottoposti a provisioning come primary = false.
    - La richiesta POST contiene il tipo di ruolo. La richiesta PATCH non contiene il tipo. Microsoft sta lavorando all'invio del tipo in entrambe le richieste POST e PATCH.
    
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
Alcuni attributi, ad esempio phoneNumbers ed emails, sono attributi multivalore in cui può essere necessario specificare tipi diversi di numeri di telefono o indirizzi di posta elettronica. Per gli attributi multivalore, usare l'espressione seguente, che consente di specificare il tipo di attributo e di eseguirne il mapping all'attributo utente di Azure AD corrispondente per il valore. 

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

Se è necessario ricominciare da capo e reimpostare i mapping esistenti sul rispettivo stato predefinito, è possibile selezionare la casella di controllo **Ripristina mapping predefiniti** e salvare la configurazione. Con questa operazione, tutti i mapping e i filtri per la definizione dell'ambito vengono impostati come se l'applicazione fosse stata appena aggiunta al tenant di Azure AD dalla raccolta di applicazioni.

La selezione di questa opzione forza una nuova sincronizzazione di tutti gli utenti durante l'esecuzione del servizio di provisioning.

> [!IMPORTANT]
> È assolutamente consigliabile impostare **Stato provisioning** su **No** prima di richiamare questa opzione.

## <a name="what-you-should-know"></a>Informazioni utili

- Microsoft Azure AD offre un'implementazione efficiente di un processo di sincronizzazione. In un ambiente inizializzato, durante un ciclo di sincronizzazione vengono elaborati solo gli oggetti che richiedono aggiornamenti.
- L'aggiornamento dei mapping degli attributi influisce negativamente sulle prestazioni di un ciclo di sincronizzazione. Un aggiornamento della configurazione del mapping degli attributi richiede la rivalutazione di tutti gli oggetti gestiti.
- È consigliabile ridurre al minimo il numero di modifiche consecutive ai mapping degli attributi.
- L'aggiunta di un attributo Photo per eseguirne il provisioning in un'app non è attualmente supportata perché non è possibile specificare il formato per sincronizzare la foto. La funzionalità può essere richiesta su [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory).
- L'attributo IsSoftDeleted fa spesso parte dei mapping predefiniti per un'applicazione. IsSoftdeleted può essere True in uno di quattro scenari: l'utente è fuori ambito perché non è assegnato all'applicazione, l'utente è fuori ambito perché non è conforme a un filtro per la definizione dell'ambito, l'utente è stato eliminato temporaneamente in Azure AD o la proprietà AccountEnabled è impostata su False per l'utente. Non è consigliabile rimuovere l'attributo IsSoftDeleted dai mapping degli attributi.
- Il servizio di provisioning di Azure AD non supporta il provisioning di valori Null.
- La chiave primaria, in genere "ID", non deve essere inclusa come attributo di destinazione nei mapping degli attributi. 
- In genere l'attributo Role deve essere mappato usando un'espressione anziché un mapping diretto. Per altre informazioni sul mapping dei ruoli, vedere la sezione precedente. 
- Sebbene sia possibile disabilitare i gruppi dai mapping, la disabilitazione degli utenti non è supportata. 

## <a name="next-steps"></a>Passaggi successivi

- [Automatizzare il provisioning e il deprovisioning utenti in app SaaS](user-provisioning.md)
- [Scrittura di espressioni per i mapping degli attributi](functions-for-customizing-application-data.md)
- [Ambito dei filtri per il Provisioning utente](define-conditional-rules-for-provisioning-user-accounts.md)
- [Uso di SCIM per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory alle applicazioni](use-scim-to-provision-users-and-groups.md)
- [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](../saas-apps/tutorial-list.md)
