---
title: Informazioni di riferimento sull'integrazione di Azure Active Directory e SAP SuccessFactors
description: Approfondimento tecnico su SAP SuccessFactors-provisioning basato su HR
services: active-directory
author: cmmdesai
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 07/20/2020
ms.author: chmutali
ms.openlocfilehash: ea47f8a6fc29571a27f8976bd0ad9bbd30ed0ad9
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87808457"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-sap-successfactors"></a>Come Azure Active Directory il provisioning si integra con SAP SuccessFactors 

[Azure Active Directory servizio di provisioning utenti](../app-provisioning/user-provisioning.md) si integra con [SAP SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) per gestire il ciclo di vita dell'identità degli utenti. Azure Active Directory offre tre integrazioni predefinite: 

* [SuccessFactors in locale Active Directory provisioning utenti](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors per Azure Active Directory il provisioning utenti](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)
* [Writeback SuccessFactors](../saas-apps/sap-successfactors-writeback-tutorial.md)

Questo articolo illustra come funziona l'integrazione e come è possibile personalizzare il comportamento di provisioning per diversi scenari HR. 

## <a name="establishing-connectivity"></a>Creazione della connettività 
Azure AD servizio di provisioning usa l'autenticazione di base per connettersi agli endpoint dell'API OData centrale dei dipendenti. Quando si configura l'app di provisioning di SuccessFactors, usare il parametro *URL tenant* nella sezione *credenziali amministratore* per configurare l' [URL Data Center dell'API](https://apps.support.sap.com/sap/support/knowledge/en/2215682). 

Per proteggere ulteriormente la connettività tra Azure AD servizio di provisioning e SuccessFactors, è possibile aggiungere gli intervalli IP Azure AD nell'elenco di indirizzi IP consentiti di SuccessFactors usando i passaggi descritti di seguito:

1. Scaricare gli [intervalli IP più recenti](https://www.microsoft.com/download/details.aspx?id=56519) per il cloud pubblico di Azure 
1. Aprire il file e cercare il tag **AzureActiveDirectory** 

   >[!div class="mx-imgBorder"] 
   >![Azure AD intervallo IP](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. Copiare tutti gli intervalli di indirizzi IP elencati nell'elemento *addressPrefixes* e usare l'intervallo per compilare l'elenco di restrizioni degli indirizzi IP.
1. Convertire i valori CIDR in intervalli IP.  
1. Accedere al portale di amministrazione di SuccessFactors per aggiungere gli intervalli IP all'elenco Consenti. Vedere la [Nota di supporto SAP 2253200](https://apps.support.sap.com/sap/support/knowledge/en/2253200). È ora possibile [immettere gli intervalli IP](https://answers.sap.com/questions/12882263/whitelisting-sap-cloud-platform-ip-address-range-i.html) in questo strumento. 

## <a name="supported-entities"></a>Entità supportate
Per ogni utente di SuccessFactors, Azure AD servizio di provisioning recupera le entità seguenti. Ogni entità viene espansa usando l'API OData *$expand* parametro di query. Vedere la colonna della *regola di recupero* riportata di seguito. Alcune entità vengono espanse per impostazione predefinita, mentre alcune entità vengono espanse solo se nel mapping è presente un attributo specifico. 

| \# | Entità SuccessFactors                  | Nodo OData     | Regola di recupero |
|----|----------------------------------------|------------------------------|------------------|
| 1  | PerPerson                              | *nodo radice*                  | Sempre           |
| 2  | PerPersonal                            | personalInfoNav              | Sempre           |
| 3  | PerPhone                               | phoneNav                     | Sempre           |
| 4  | PerEmail                               | emailNav                     | Sempre           |
| 5  | EmpEmployment                          | employmentNav                | Sempre           |
| 6  | Utente                                   | employmentNav/userNav        | Sempre           |
| 7  | EmpJob                                 | employmentNav/jobInfoNav     | Sempre           |
| 8  | EmpEmploymentTermination               | activeEmploymentsCount       | Sempre           |
| 9  | FOCompany                              | employmentNav/jobInfoNav/companyNav | Solo se `company` `companyId` è stato eseguito il mapping dell'attributo o |
| 10 | FODepartment                           | employmentNav/jobInfoNav/departmentNav | Solo se `department` `departmentId` è stato eseguito il mapping dell'attributo o |
| 11 | FOBusinessUnit                         | employmentNav/jobInfoNav/businessUnitNav | Solo se `businessUnit` `businessUnitId` è stato eseguito il mapping dell'attributo o |
| 12 | FOCostCenter                           | employmentNav/jobInfoNav/costCenterNav | Solo se `costCenter` `costCenterId` è stato eseguito il mapping dell'attributo o |
| 13 | FODivision                             | employmentNav/jobInfoNav/divisionNav  | Solo se `division` `divisionId` è stato eseguito il mapping dell'attributo o |
| 14 | FOJobCode                              | employmentNav/jobInfoNav/jobCodeNav  | Solo se `jobCode` `jobCodeId` è stato eseguito il mapping dell'attributo o |
| 15 | FOPayGrade                             | employmentNav/jobInfoNav/payGradeNav  | Solo se `payGrade` è stato eseguito il mapping dell'attributo |
| 16 | FOLocation                             | employmentNav/jobInfoNav/locationNav  | Solo se `location` è stato eseguito il mapping dell'attributo |
| 17 | FOCorporateAddressDEFLT                | employmentNav/jobInfoNav/addressNavDEFLT  | Se il mapping contiene uno degli attributi seguenti:`officeLocationAddress,  officeLocationCity, officeLocationZipCode` |
| 18 | FOEventReason                          | employmentNav/jobInfoNav/eventReasonNav  | Solo se `eventReason` è stato eseguito il mapping dell'attributo |
| 19 | EmpGlobalAssignment                    | employmentNav/empGlobalAssignmentNav | Solo se `assignmentType` è mappato |
| 20 | Elenco a discesa EmploymentType                | employmentNav/jobInfoNav/employmentTypeNav | Solo se `employmentType` è mappato |
| 21 | Elenco a discesa EmployeeClass                 | employmentNav/jobInfoNav/employeeClassNav | Solo se `employeeClass` è mappato |
| 22 | Elenco a discesa EmplStatus                    | employmentNav/jobInfoNav/emplStatusNav | Solo se `emplStatus` è mappato |
| 23 | Elenco a discesa AssignmentType                | employmentNav/empGlobalAssignmentNav/assignmentTypeNav | Solo se `assignmentType` è mappato |

## <a name="how-full-sync-works"></a>Funzionamento della sincronizzazione completa
In base al mapping degli attributi, durante la sincronizzazione completa Azure AD servizio di provisioning Invia la seguente query API OData "GET" per recuperare dati effettivi di tutti gli utenti attivi. 

> [!div class="mx-tdCol2BreakAll"]
>| Parametro | Descrizione |
>| ----------|-------------|
>| Host API OData | Accoda HTTPS all'URL del *tenant*. Esempio: `https://api4.successfactors.com` |
>| Endpoint API OData | `/odata/v2/PerPerson` |
>| Parametro di query OData $format | `json` |
>| Parametro di query OData $filter | `(personEmpTerminationInfoNav/activeEmploymentsCount ge 1) and (lastModifiedDateTime le <CurrentExecutionTime>)` |
>| Parametro di query OData $expand | Il valore di questo parametro dipende dagli attributi di cui è stato eseguito il mapping. Esempio: `employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,phoneNav,emailNav,employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav` |
>| Parametro di query OData customPageSize | `100` |

> [!NOTE]
> Durante la prima sincronizzazione completa iniziale, Azure AD servizio di provisioning non esegue il pull dei dati di lavoro inattivi o terminati.

Per ogni utente SuccessFactors, il servizio di provisioning Cerca un account nella destinazione (Azure AD/locale Active Directory) usando l'attributo corrispondente definito nel mapping. Ad esempio: se *personIdExternal* esegue il mapping a *EmployeeID* ed è impostato come attributo corrispondente, il servizio di provisioning usa il valore *personIdExternal* per cercare l'utente con filtro *EmployeeID* . Se viene individuata una corrispondenza utente, vengono aggiornati gli attributi di destinazione. Se non viene trovata alcuna corrispondenza, viene creata una nuova voce nella destinazione. 

Per convalidare i dati restituiti dall'endpoint dell'API OData per uno specifico `personIdExternal` , aggiornare `SuccessFactorsAPIEndpoint` nella query API riportata di seguito con l'URL del server dell'API Data Center e usare uno strumento come [subpost](https://www.postman.com/downloads/) per richiamare la query. 

```
https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
$filter=(personIdExternal in '[personIdExternalValue]')&
$expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

## <a name="how-incremental-sync-works"></a>Funzionamento della sincronizzazione incrementale

Dopo la sincronizzazione completa, Azure AD servizio di provisioning `LastExecutionTimestamp` lo gestisce e lo usa per creare query Delta per il recupero delle modifiche incrementali. Gli attributi timestamp presenti in ogni entità SuccessFactors, ad esempio `lastModifiedDateTime` , `startDate` , `endDate` e `latestTerminationDate` , vengono valutati per verificare se la modifica rientra tra `LastExecutionTimestamp` e `CurrentExecutionTime` . In caso affermativo, la modifica della voce viene considerata efficace ed elaborata per la sincronizzazione. 

## <a name="reading-attribute-data"></a>Lettura dei dati degli attributi

Quando Azure AD il servizio di provisioning esegue query SuccessFactors, recupera un set di risultati JSON. Il set di risultati JSON include diversi attributi archiviati in Employee Central. Per impostazione predefinita, lo schema di provisioning viene configurato per recuperare solo un subset di tali attributi. 

Per recuperare attributi aggiuntivi, attenersi alla procedura riportata di seguito:
    
1. Passare ad **applicazioni aziendali**  ->  **SuccessFactors app**di  ->  **provisioning**modifica provisioning  ->  **Edit Provisioning**  ->  **attributo-mapping pagina**.
1. Scorrere verso il basso e fare clic su **Mostra opzioni avanzate**.
1. Fare clic su **modifica elenco attributi per SuccessFactors**. 

   > [!NOTE] 
   > Se l'opzione **modifica elenco attributi per SuccessFactors** non viene visualizzata nella portale di Azure, usare l'URL *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* per accedere alla pagina. 

1. Nella colonna **espressione API** in questa vista vengono visualizzate le espressioni JSONPath utilizzate dal connettore.

   >[!div class="mx-imgBorder"] 
   >![Espressione API](media/sap-successfactors-integration-reference/jsonpath-api-expressions.png#lightbox)  

1. È possibile modificare un valore JSONPath esistente o aggiungere un nuovo attributo con un'espressione JSONPath valida allo schema. 

Nella sezione successiva viene fornito un elenco di scenari comuni per la modifica dei valori JSONPath. 

## <a name="handling-different-hr-scenarios"></a>Gestione di scenari HR diversi

JSONPath è un linguaggio di query per JSON simile a XPath for XML. Analogamente a XPath, JSONPath consente l'estrazione e la filtraggio dei dati da un payload JSON.

Con la trasformazione JSONPath è possibile personalizzare il comportamento dell'app di provisioning Azure AD per recuperare gli attributi personalizzati e gestire scenari come il riassunzione, la conversione dei thread di lavoro e l'assegnazione globale. 

Questa sezione illustra come è possibile personalizzare l'app di provisioning per gli scenari HR seguenti: 
* [Recupero di attributi aggiuntivi](#retrieving-additional-attributes)
* [Recupero di attributi personalizzati](#retrieving-custom-attributes)
* [Gestione dello scenario di conversione dei thread di lavoro](#handling-worker-conversion-scenario)
* [Gestione dello scenario di riassunzione](#handling-rehire-scenario)
* [Gestione dello scenario di assegnazione globale](#handling-global-assignment-scenario)
* [Scenario di gestione dei processi simultanei](#handling-concurrent-jobs-scenario)

### <a name="retrieving-additional-attributes"></a>Recupero di attributi aggiuntivi

Il valore predefinito Azure AD lo schema dell'app di provisioning di SuccessFactors viene fornito con più di [90 attributi](sap-successfactors-attribute-reference.md)predefiniti. Per aggiungere altri attributi SuccessFactors allo schema di provisioning, attenersi alla procedura riportata di seguito: 

1. Usare la query OData riportata di seguito per recuperare i dati per un utente test valido da Employee Central. 

   ```
    https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
    $filter=(personIdExternal in '[personIdExternalValue]')&
    $expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
    phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
    employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
    employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
    employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
    employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
    employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
   ```

1. Determinare l'entità centrale Employee associata all'attributo
   * Se l'attributo fa parte dell'entità *EmpEmployment* , cercare l'attributo nel nodo *employmentNav* . 
   * Se l'attributo fa parte dell'entità *utente* , cercare l'attributo nel nodo *employmentNav/userNav* .
   * Se l'attributo fa parte dell'entità *EmpJob* , cercare l'attributo nel nodo *employmentNav/jobInfoNav* . 
1. Costruire il percorso JSON associato all'attributo e aggiungere il nuovo attributo all'elenco di attributi SuccessFactors. 
   * Esempio 1: si vuole aggiungere l'attributo *okToRehire*, che fa parte dell'entità *employmentNav* , quindi usare JSONPath`$.employmentNav.results[0].okToRehire`
   * Esempio 2: si desidera aggiungere l'attributo *TimeZone*, che fa parte dell'entità *userNav* , quindi utilizzare JSONPath`$.employmentNav.results[0].userNav.timeZone`
   * Esempio 3: si vuole aggiungere l'attributo *flsaStatus*, che fa parte dell'entità *jobInfoNav* , quindi usare JSONPath`$.employmentNav.results[0].jobInfoNav.results[0].flsaStatus`
1. Salvare lo schema. 
1. Riavviare il provisioning.

### <a name="retrieving-custom-attributes"></a>Recupero di attributi personalizzati

Per impostazione predefinita, gli attributi personalizzati seguenti sono predefiniti nell'app di provisioning Azure AD SuccessFactors: 
* *custom01-custom15* dall'entità User (userNav)
* *customString1-customString15* dall'entità EmpEmployment (employmentNav) denominata *empNavCustomString1-empNavCustomString15*
* *customString1-customString15* dall'entità EmpJobInfo (jobInfoNav) denominata *empJobNavCustomString1-empNavJobCustomString15*

Supponiamo che nell'istanza centrale del dipendente, l'attributo *customString35* in *EmpJobInfo* archivi la descrizione del percorso. Si vuole eseguire il flusso di questo valore per Active Directory attributo *physicalDeliveryOfficeName* . Per configurare il mapping degli attributi per questo scenario, attenersi alla procedura riportata di seguito: 

1. Modificare l'elenco di attributi SuccessFactors per aggiungere un nuovo attributo denominato *empJobNavCustomString35*.
1. Impostare l'espressione API JSONPath per questo attributo come:`$.employmentNav.results[0].jobInfoNav.results[0].customString35`
1. Salvare e ricaricare la modifica del mapping nell'portale di Azure.  
1. Nel pannello mapping degli attributi eseguire il mapping di *empJobNavCustomString35* a *physicalDeliveryOfficeName*.
1. Salvare il mapping.

Estensione di questo scenario: 
* Se si vuole eseguire il mapping dell'attributo *custom35* dall'entità *User* , usare JSONPath`$.employmentNav.results[0].userNav.custom35`
* Se si vuole eseguire il mapping dell'attributo *customString35* dall'entità *EmpEmployment* , usare JSONPath`$.employmentNav.results[0].customString35`

### <a name="handling-worker-conversion-scenario"></a>Gestione dello scenario di conversione dei thread di lavoro

La conversione del ruolo di lavoro è il processo di conversione di un dipendente a tempo pieno esistente in un appaltatore o in un appaltatore a tempo pieno. In questo scenario Employee Central aggiunge una nuova entità *EmpEmployment* insieme a una nuova entità *User* per la stessa entità *Person* . L'entità *utente* annidata sotto l'entità *EmpEmployment* precedente è impostata su null. Per gestire questo scenario in modo da visualizzare i nuovi dati sull'impiego quando si verifica una conversione, è possibile eseguire l'aggiornamento bulk dello schema dell'app di provisioning usando i passaggi elencati di seguito:  

1. Aprire il pannello mapping attributi dell'app di provisioning di SuccessFactors. 
1. Scorrere verso il basso e fare clic su **Mostra opzioni avanzate**.
1. Fare clic sul collegamento **esaminare lo schema qui** per aprire l'Editor schemi. 

   >![Revisione-schema](media/sap-successfactors-integration-reference/review-schema.png#lightbox)

1. Fare clic sul collegamento **download** per salvare una copia dello schema prima della modifica. 

   >![Scarica-schema](media/sap-successfactors-integration-reference/download-schema.png#lightbox)
1. Nell'Editor schemi premere CTRL + H per aprire il controllo Trova-Sostituisci.
1. Nella casella di testo trova copiare e incollare il valore`$.employmentNav.results[0]`
1. Nella casella di testo Sostituisci copiare e incollare il valore `$.employmentNav.results[?(@.userNav != null)]` . Si noti lo spazio vuoto `!=` che circonda l'operatore, che è importante per una corretta elaborazione dell'espressione JSONPath. 
   >![Find-Replace-Conversion](media/sap-successfactors-integration-reference/find-replace-conversion-scenario.png#lightbox)
1. Fare clic sull'opzione "Sostituisci tutto" per aggiornare lo schema. 
1. Salvare lo schema. 
1. Il processo precedente aggiorna tutte le espressioni JSONPath come segue: 
   * JSONPath precedente:`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Nuovo JSONPath:`$.employmentNav.results[?(@.userNav != null)].jobInfoNav.results[0].departmentNav.name_localized`
1. Riavviare il provisioning. 

### <a name="handling-rehire-scenario"></a>Gestione dello scenario di riassunzione

Sono in genere disponibili due opzioni per elaborare i riassunti: 
* Opzione 1: creare un nuovo profilo persona in Employee Central
* Opzione 2: riutilizzare il profilo Person esistente in Employee Central

Se il processo HR usa l'opzione 1, non sono necessarie modifiche allo schema di provisioning. Se il processo HR usa l'opzione 2, Employee Central aggiunge una nuova entità *EmpEmployment* insieme a una nuova entità *User* per la stessa entità *Person* . A differenza dello scenario di conversione, l'entità *utente* nell'entità *EmpEmployment* precedente non è impostata su null. 

Per gestire questo scenario di riassunto (opzione 2), in modo che i dati di utilizzo più recenti vengano visualizzati per i profili di riassunzione, è possibile eseguire l'aggiornamento bulk dello schema dell'app di provisioning usando i passaggi elencati di seguito:  

1. Aprire il pannello mapping attributi dell'app di provisioning di SuccessFactors. 
1. Scorrere verso il basso e fare clic su **Mostra opzioni avanzate**.
1. Fare clic sul collegamento **esaminare lo schema qui** per aprire l'Editor schemi.   
1. Fare clic sul collegamento **download** per salvare una copia dello schema prima della modifica.   
1. Nell'Editor schemi premere CTRL + H per aprire il controllo Trova-Sostituisci.
1. Nella casella di testo trova copiare e incollare il valore`$.employmentNav.results[0]`
1. Nella casella di testo Sostituisci copiare e incollare il valore `$.employmentNav.results[-1:]` . Questa espressione JSONPath restituisce il record *EmpEmployment* più recente.   
1. Fare clic sull'opzione "Sostituisci tutto" per aggiornare lo schema. 
1. Salvare lo schema. 
1. Il processo precedente aggiorna tutte le espressioni JSONPath come segue: 
   * JSONPath precedente:`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Nuovo JSONPath:`$.employmentNav.results[-1:].jobInfoNav.results[0].departmentNav.name_localized`
1. Riavviare il provisioning. 

Questa modifica dello schema supporta anche lo scenario di conversione del ruolo di lavoro. 

### <a name="handling-global-assignment-scenario"></a>Gestione dello scenario di assegnazione globale

Quando un utente in Employee Central viene elaborato per l'assegnazione globale, SuccessFactors aggiunge una nuova entità *EmpEmployment* e imposta *ASSIGNMENTCLASS* su "GA". Crea anche una nuova entità *utente* . Quindi, l'utente ha ora:
* Un' *EmpEmployment*  +  entità*utente* EmpEmployment che corrisponde all'assegnazione Home con *assignmentClass* impostato su "St" e 
* Un' *EmpEmployment*altra  +  entità*utente* EmpEmployment che corrisponde all'assegnazione globale con *assignmentClass* impostato su "GA"

Per recuperare gli attributi appartenenti al profilo utente assegnazione standard e assegnazione globale, attenersi alla procedura riportata di seguito: 

1. Aprire il pannello mapping attributi dell'app di provisioning di SuccessFactors. 
1. Scorrere verso il basso e fare clic su **Mostra opzioni avanzate**.
1. Fare clic sul collegamento **esaminare lo schema qui** per aprire l'Editor schemi.   
1. Fare clic sul collegamento **download** per salvare una copia dello schema prima della modifica.   
1. Nell'Editor schemi premere CTRL + H per aprire il controllo Trova-Sostituisci.
1. Nella casella di testo trova copiare e incollare il valore`$.employmentNav.results[0]`
1. Nella casella di testo Sostituisci copiare e incollare il valore `$.employmentNav.results[?(@.assignmentClass == 'ST')]` . 
1. Fare clic sull'opzione "Sostituisci tutto" per aggiornare lo schema. 
1. Salvare lo schema. 
1. Il processo precedente aggiorna tutte le espressioni JSONPath come segue: 
   * JSONPath precedente:`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * Nuovo JSONPath:`$.employmentNav.results[?(@.assignmentClass == 'ST')].jobInfoNav.results[0].departmentNav.name_localized`
1. Ricaricare il pannello mapping attributi dell'app. 
1. Scorrere verso il basso e fare clic su **Mostra opzioni avanzate**.
1. Fare clic su **modifica elenco attributi per SuccessFactors**.
1. Aggiungere nuovi attributi per recuperare i dati di assegnazione globali. Ad esempio: se si vuole recuperare il nome del reparto associato a un profilo di assegnazione globale, è possibile aggiungere l'attributo *globalAssignmentDepartment* con l'espressione JSONPath impostata su `$.employmentNav.results[?(@.assignmentClass == 'GA')].jobInfoNav.results[0].departmentNav.name_localized` . 
1. È ora possibile eseguire il flusso di entrambi i valori del reparto per Active Directory attributi o propagarne selettivamente un valore usando il mapping delle espressioni. Esempio: l'espressione seguente imposta il valore dell'attributo del *reparto* ad su *globalAssignmentDepartment* se presente. in caso contrario, imposta il valore su *Department* associato all'assegnazione standard. 
   * `IIF(IsPresent([globalAssignmentDepartment]),[globalAssignmentDepartment],[department])`

1. Salvare il mapping. 
1. Riavviare il provisioning. 

### <a name="handling-concurrent-jobs-scenario"></a>Scenario di gestione dei processi simultanei

Quando un utente in Employee Central dispone di processi simultanei o multipli, sono presenti due entità *EmpEmployment* e *User* con *assignmentClass* impostato su "St". Per recuperare gli attributi appartenenti a entrambi i processi, attenersi alla procedura riportata di seguito: 

1. Aprire il pannello mapping attributi dell'app di provisioning di SuccessFactors. 
1. Scorrere verso il basso e fare clic su **Mostra opzioni avanzate**.
1. Fare clic su **modifica elenco attributi per SuccessFactors**.
1. Supponiamo di voler effettuare il pull del reparto associato al processo 1 e al processo 2. Il *reparto* attributi predefinito recupera già il valore del reparto per il primo processo. È possibile definire un nuovo attributo denominato *secondJobDepartment* e impostare l'espressione JSONPath su`$.employmentNav.results[1].jobInfoNav.results[0].departmentNav.name_localized`
1. È ora possibile eseguire il flusso di entrambi i valori del reparto per Active Directory attributi o propagarne selettivamente un valore usando il mapping delle espressioni. 
1. Salvare il mapping. 
1. Riavviare il provisioning. 

## <a name="writeback-scenarios"></a>Scenari di writeback

In questa sezione vengono illustrati diversi scenari di write-back. Suggerisce gli approcci di configurazione in base alla modalità di configurazione del numero di telefono e della posta elettronica in SuccessFactors.

### <a name="supported-scenarios-for-phone-and-email-write-back"></a>Scenari supportati per il writeback del telefono e della posta elettronica 

| \# | Requisito dello scenario | Posta elettronica primaria <br> valore del flag | Telefono ufficio <br> valore del flag primario | Telefono cellulare <br> valore del flag primario | Telefono ufficio <br> mapping | Telefono cellulare <br> mapping |
|--|--|--|--|--|--|--|
| 1 | * Impostare solo la posta elettronica aziendale come primaria. <br> * Non impostare i numeri di telefono. | true | true | false | \[Non impostato\] | \[Non impostato\] | 
| 2 | * In SuccessFactors, la posta elettronica aziendale e il telefono aziendale sono primari <br> * Passa sempre Azure AD numero di telefono a telefono aziendale e cellulare al telefono cellulare. | true | true | false | telephoneNumber | mobile | 
| 3 | * In SuccessFactors, la posta elettronica aziendale e il telefono cellulare sono primari <br> * Passa sempre Azure AD numero di telefono a telefono aziendale e cellulare al telefono cellulare | true | false | true |  telephoneNumber | mobile | 
| 4 | * In SuccessFactors business email è primario <br> * In Azure AD verificare se il numero di telefono dell'ufficio è presente, se presente, quindi controllare se è presente anche il numero di cellulare, contrassegnare il numero di telefono dell'ufficio come primario solo se il numero di cellulare non è presente. | true | USA mapping espressioni:`IIF(IsPresent([telephoneNumber]), IIF(IsPresent([mobile]),"false", "true"), "false")` | USA mapping espressioni:`IIF(IsPresent([mobile]),"false", "true")` | telephoneNumber | mobile | 
| 5 | * In SuccessFactors business email e Business Phone è primario. <br> * In Azure AD, se è disponibile un dispositivo mobile, impostarlo come telefono aziendale. in caso contrario, usare telephoneNumber. | true | true | false | `IIF(IsPresent([mobile]), [mobile], [telephoneNumber])` | \[Non impostato\] | 

* Se non esiste alcun mapping per il numero di telefono nell'attributo write-back-mapping, solo la posta elettronica viene inclusa nel writeback.
* Durante il caricamento di un nuovo noleggio in Employee Central, il messaggio di posta elettronica aziendale e il numero di telefono potrebbero non essere disponibili. Se l'impostazione della posta elettronica aziendale e del telefono aziendale come primaria è obbligatoria durante l'onboarding, è possibile impostare un valore fittizio per il telefono aziendale e la posta elettronica durante la creazione di una nuova assunzione, che verrà aggiornata dall'app write-back.
 
### <a name="unsupported-scenarios-for-phone-and-email-write-back"></a>Scenari non supportati per il writeback del telefono e della posta elettronica

* In Employee Central, durante l'onboarding personal email e Personal Phone è impostato come primario. L'app write-back non può cambiare questa impostazione e impostare la posta elettronica aziendale e il telefono aziendale come primario.
* In Employee Central il telefono aziendale è impostato come primario. L'app write-back non può modificare questo e impostare il telefono cellulare come primario.
* L'app write-back non è in grado di leggere le impostazioni correnti del flag primario e di usare gli stessi valori per l'operazione di scrittura. Verranno sempre usati i valori dei flag configurati nel mapping degli attributi. 

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come configurare SuccessFactors per il provisioning di Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Informazioni su come configurare il writeback per SuccessFactors](../saas-apps/sap-successfactors-writeback-tutorial.md)
* [Altre informazioni sugli attributi SuccessFactors supportati per il provisioning in ingresso](sap-successfactors-attribute-reference.md)










