---
title: Informazioni di riferimento sugli attributi di SAP SuccessFactors | Microsoft Docs
description: Informazioni sugli attributi di SuccessFactors supportati dal provisioning basato su SuccessFactors-HR
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 6f7497e62be0036c13d5c33fa82301469df16f26
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74971112"
---
# <a name="sap-successfactors-attribute-reference"></a>Informazioni di riferimento sugli attributi di SAP SuccessFactors

## <a name="supported-successfactors-entities-and-attributes"></a>Entità e attributi SuccessFactors supportati

La tabella seguente acquisisce l'elenco degli attributi SuccessFactors supportati dalle due app di provisioning seguenti: 
* [SuccessFactors per Active Directory il provisioning utenti](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors per Azure AD il provisioning utenti](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | Entità SuccessFactors                  | Attributo SuccessFactors     | Tipo di operazione |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Lettura           |
| 2  | PerPerson                              | personId                     | Lettura           |
| 3  | PerPerson                              | perPersonUuid                | Lettura           |
| 4  | PerPersonal                            | displayName                  | Lettura           |
| 5  | PerPersonal                            | firstName                    | Lettura           |
| 6  | PerPersonal                            | gender                       | Lettura           |
| 7  | PerPersonal                            | lastName                     | Lettura           |
| 8  | PerPersonal                            | middleName                   | Lettura           |
| 9  | PerPersonal                            | preferenzaname                | Lettura           |
| 10 | Utente                                   | addressLine1                 | Lettura           |
| 11 | Utente                                   | addressLine2                 | Lettura           |
| 12 | Utente                                   | addressLIne3                 | Lettura           |
| 13 | Utente                                   | businessPhone                | Lettura           |
| 14 | Utente                                   | cellPhone                    | Lettura           |
| 15 | Utente                                   | city                         | Lettura           |
| 16 | Utente                                   | country                      | Lettura           |
| 17 | Utente                                   | custom01                     | Lettura           |
| 18 | Utente                                   | custom02                     | Lettura           |
| 19 | Utente                                   | custom03                     | Lettura           |
| 20 | Utente                                   | custom04                     | Lettura           |
| 21 | Utente                                   | custom05                     | Lettura           |
| 22 | Utente                                   | custom06                     | Lettura           |
| 23 | Utente                                   | custom07                     | Lettura           |
| 24 | Utente                                   | custom08                     | Lettura           |
| 25 | Utente                                   | custom09                     | Lettura           |
| 26 | Utente                                   | personalizzata10                     | Lettura           |
| 27 | Utente                                   | custom11                     | Lettura           |
| 28 | Utente                                   | custom12                     | Lettura           |
| 29 | Utente                                   | custom13                     | Lettura           |
| 30 | Utente                                   | custom14                     | Lettura           |
| 31 | Utente                                   | empId                        | Lettura           |
| 32 | Utente                                   | homePhone                    | Lettura           |
| 33 | Utente                                   | jobFamily                    | Lettura           |
| 34 | Utente                                   | nickname                     | Lettura           |
| 35 | Utente                                   | state                        | Lettura           |
| 36 | Utente                                   | timeZone                     | Lettura           |
| 37 | Utente                                   | Nome utente                     | Lettura           |
| 38 | Utente                                   | zipCode                      | Lettura           |
| 39 | PerPhone                               | areaCode                     | Lettura           |
| 40 | PerPhone                               | countryCode                  | Lettura           |
| 41 | PerPhone                               | estensione                    | Lettura           |
| 42 | PerPhone                               | phoneNumber                  | Lettura           |
| 43 | PerPhone                               | phoneType                    | Lettura           |
| 44 | PerEmail                               | emailAddress                 | Lettura, Scrittura    |
| 45 | PerEmail                               | emailType                    | Lettura           |
| 46 | EmpEmployment                          | firstDateWorked              | Lettura           |
| 47 | EmpEmployment                          | lastDateWorked               | Lettura           |
| 48 | EmpEmployment                          | userId                       | Lettura           |
| 49 | EmpEmployment                          | isContingentWorker           | Lettura           |
| 50 | EmpJob                                 | countryOfCompany             | Lettura           |
| 51 | EmpJob                                 | emplStatus                   | Lettura           |
| 52 | EmpJob                                 | endDate                      | Lettura           |
| 53 | EmpJob                                 | startDate                    | Lettura           |
| 54 | EmpJob                                 | jobTitle                     | Lettura           |
| 55 | EmpJob                                 | località                     | Lettura           |
| 65 | EmpJob                                 | customString13               | Lettura           |
| 56 | EmpJob                                 | managerId                    | Lettura           |
| 57 | EmpJob\.BusinessUnit                   | businessUnit                 | Lettura           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | Lettura           |
| 59 | EmpJob\.Company                        | company                      | Lettura           |
| 60 | EmpJob\.Company                        | companyId                    | Lettura           |
| 61 | EmpJob\.Company\.CountryOfRegistration | twoCharCountryCode           | Lettura           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | Lettura           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | Lettura           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | Lettura           |
| 65 | EmpJob\.Department                     | department                   | Lettura           |
| 66 | EmpJob\.Department                     | departmentId                 | Lettura           |
| 67 | Divisione\.EmpJob                       | divisione                     | Lettura           |
| 68 | Divisione\.EmpJob                       | divisionId                   | Lettura           |
| 69 | EmpJob\.JobCode                        | jobCode                      | Lettura           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | Lettura           |
| 71 | Percorso\.EmpJob                       | LocationName                 | Lettura           |
| 72 | Percorso\.EmpJob                       | officeLocationAddress        | Lettura           |
| 73 | Percorso\.EmpJob                       | officeLocationCity           | Lettura           |
| 74 | Percorso\.EmpJob                       | officeLocationCustomString4  | Lettura           |
| 75 | Percorso\.EmpJob                       | officeLocationZipCode        | Lettura           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | Lettura           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Lettura           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Lettura           |


## <a name="default-attribute-mapping"></a>Mapping predefinito degli attributi

La tabella seguente fornisce il mapping predefinito degli attributi tra gli attributi SuccessFactors elencati sopra e gli attributi AD/Azure AD. Nel pannello "mapping" dell'app di provisioning di Azure AD è possibile modificare questo mapping predefinito per includere gli attributi dall'elenco precedente. 

| \# | Entità SuccessFactors                  | Attributo SuccessFactors | Mapping di attributi AD/Azure AD predefinito   | Elaborazione del contrassegno                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | employeeId                              | Utilizzato come attributo corrispondente                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[non è stato eseguito il mapping \- usato come ancoraggio di origine\] | Durante la sincronizzazione iniziale, il servizio di provisioning collega personUuid a objectGuid\. esistenti  |
| 3  | PerPersonal                            | displayName              | displayName                             | ND                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | ND                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | ND                                                                                           |
| 6  | Utente                                   | addressLine1             | streetAddress                           | ND                                                                                           |
| 7  | Utente                                   | city                     | l                                       | ND                                                                                           |
| 8  | Utente                                   | country                  | co                                      | ND                                                                                           |
| 9  | Utente                                   | state                    | st                                      | ND                                                                                           |
| 10 | Utente                                   | Nome utente                 | samAccountName                          | ND                                                                                           |
| 11 | Utente                                   | zipCode                  | postalCode                              | ND                                                                                           |
| 12 | PerEmail                               | emailAddress             | mail                                    | ND                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | title                                   | ND                                                                                           |
| 14 | EmpJob                                 | managerId                | manager                                 | ND                                                                                           |
| 15 | EmpJob\.Company\.CountryOfRegistration | twoCharCountryCode       | c                                       | ND                                                                                           |
| 16 | EmpJob\.Department                     | department               | department                              | ND                                                                                           |
| 17 | Divisione\.EmpJob                       | divisione                 | company                                 | ND                                                                                           |
| 18 | Percorso\.EmpJob                       | officeLocationAddress    | streetAddress                           | ND                                                                                           |
| 19 | Percorso\.EmpJob                       | officeLocationZipCode    | postalCode                              | ND                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | Se activeEmploymentsCount = 0, disabilitare il account\.                                           |

