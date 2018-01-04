---
title: Personalizzazione dei mapping degli attributi di Azure AD | Documentazione Microsoft
description: "Informazioni sui mapping degli attributi per app SaaS in Azure Active Directory e su come è possibile modificarli in base alle esigenze aziendali."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 549e0b8c-87ce-4c9b-b487-b7bf0155dc77
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d4618f7cd702a1cfefafe80eff35a0753e04a50c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personalizzazione dei mapping degli attributi del provisioning degli utenti per le applicazioni SaaS in Azure Active Directory
Microsoft Azure AD fornisce supporto per il provisioning degli utenti in applicazioni SaaS di terze parti, ad esempio Salesforce, Google Apps e altre. Se è abilitato il provisioning degli utenti per un'applicazione SaaS di terze parti, il portale di gestione di Azure controlla i relativi valori di attributo tramite una configurazione denominata "mapping degli attributi".

Esiste un set preconfigurato di mapping degli attributi tra gli oggetti utente di Azure AD e gli oggetti utente di ogni app SaaS. Alcune app gestiscono altri tipi di oggetti, quali Gruppi o Contatti. <br> 
 È possibile personalizzare i mapping degli attributi predefiniti in base alle esigenze aziendali. È infatti possibile modificare o eliminare i mapping degli attributi esistenti oppure crearne di nuovi.

Nel portale di Azure AD è possibile accedere a questa funzionalità facendo clic su una configurazione **Mapping** in **Provisioning** nella sezione **Gestione** di un'**applicazione aziendale**.


![Salesforce][5] 

Se si fa clic su una configurazione **Mapping** viene aperto il pannello **Mapping attributi** corrispondente.  
Alcuni mapping di attributi sono obbligatori per il corretto funzionamento di un'applicazione SaaS. Per gli attributi obbligatori la funzionalità **Elimina** non è disponibile.


![Salesforce][6]  

Nell'esempio riportato sopra si può notare che l'attributo **Username** di un oggetto gestito in Salesforce è popolato con il valore **userPrincipalName** dell'oggetto di Azure Active Directory collegato.

È possibile personalizzare i **Mapping degli attributi** esistenti facendo clic su un mapping. Viene visualizzato il pannello **Modifica attributo**.

![Salesforce][7]  


  

## <a name="understanding-attribute-mapping-types"></a>Informazioni sui tipi di mapping degli attributi
I mapping degli attributi permettono di controllare il modo in cui gli attribuiti vengono popolati in un'applicazione SaaS di terze parti. Sono supportati quattro diversi tipi di mapping:

* **Diretto:** l'attributo di destinazione viene popolato con il valore di un attributo dell'oggetto collegato in Azure AD.
* **Costante:** l'attributo di destinazione viene popolato con una stringa specificata.
* **Espressione:** l'attributo di destinazione viene popolato in base al risultato di un'espressione simile a uno script. 
  Per altre informazioni, vedere [Scrittura di espressioni per il mapping degli attributi in Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md).
* **Nessuno:** l'attributo di destinazione viene lasciato invariato. Se l'attributo di destinazione viene lasciato vuoto, viene popolato con il valore predefinito specificato.

Oltre a questi quattro tipi base di mapping, i mapping degli attributi personalizzati supportano il concetto di assegnazione di un valore **predefinito** facoltativo. L'assegnazione di un valore predefinito garantisce che un attributo di destinazione venga popolato con un valore nel caso in cui non sia presente un valore né in Azure AD, né nell'oggetto di destinazione. Nella maggior parte delle configurazioni questo campo viene lasciato vuoto.


## <a name="understanding-attribute-mapping-properties"></a>Informazioni sulle proprietà di mapping degli attributi

Nella sezione precedente è già stata presentata la proprietà del tipo di mapping degli attributi.
Oltre a questa proprietà i mapping degli attributi supportano i seguenti attributi:

- **Attributo di origine**: attributo utente del sistema di origine (ad esempio Azure Active Directory).
- **Attributo di destinazione**: attributo utente nel sistema di destinazione (ad esempio ServiceNow).
- **Abbina gli oggetti in base a questo attributo**: specifica se questo mapping va usato per l'identificazione univoca degli utenti tra il sistema di origine e il sistema di destinazione. Questa opzione è in genere impostata sull'attributo userPrincipalName o mail in Azure AD, che a sua volta è in genere mappato su un campo username in un'applicazione di destinazione.
- **Precedenza abbinamento**: è possibile impostare più attributi corrispondenti. Se sono presenti più attributi, vengono valutati nell'ordine definito da questo campo. Quando viene rilevata una corrispondenza la valutazione degli attributi corrispondenti termina.
- **Applica questo mapping**
    - **Sempre**: applica il mapping sia all'azione di creazione che all'azione di aggiornamento dell'utente
    - **Only during creation** (Solo durante la creazione): applica il mapping solo alle azioni di creazione dell'utente


## <a name="what-you-should-know"></a>Informazioni utili

Microsoft Azure AD offre un'implementazione efficiente di un processo di sincronizzazione. In un ambiente inizializzato, durante un ciclo di sincronizzazione vengono elaborati solo gli oggetti che richiedono aggiornamenti. L'aggiornamento dei mapping degli attributi influisce negativamente sulle prestazioni di un ciclo di sincronizzazione. Un aggiornamento della configurazione del mapping degli attributi richiede la rivalutazione di tutti gli oggetti gestiti. È consigliabile ridurre al minimo il numero di modifiche consecutive ai mapping degli attributi.

## <a name="next-steps"></a>Passaggi successivi

* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
* [Automatizzare il provisioning e il deprovisioning utenti in app SaaS](active-directory-saas-app-provisioning.md)
* [Scrittura di espressioni per i mapping degli attributi](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Ambito dei filtri per il Provisioning utente](active-directory-saas-scoping-filters.md)
* [Uso di SCIM per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory alle applicazioni](active-directory-scim-provisioning.md)
* [Notifiche relative al provisioning dell'account](active-directory-saas-account-provisioning-notifications.md)
* [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png
[5]: ./media/active-directory-saas-customizing-attribute-mappings/21.png
[6]: ./media/active-directory-saas-customizing-attribute-mappings/22.png
[7]: ./media/active-directory-saas-customizing-attribute-mappings/23.png

