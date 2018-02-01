---
title: 'Servizio di sincronizzazione Azure AD Connect: estensioni della directory | Documentazione Microsoft'
description: "Questo argomento illustra la funzionalità relativa alle estensioni della directory in Azure AD Connect."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 9abd035b13a0d51c534eb8cac50c045012399a69
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Servizio di sincronizzazione Azure AD Connect: estensioni della directory
Le estensioni della directory consentono di estendere lo schema in Azure AD con attributi personalizzati da Active Directory in locale. Questa funzionalità consente di compilare app line-of-business che utilizzano attributi che continuano a essere gestiti in locale. Questi attributi possono essere utilizzati tramite le [estensioni della directory Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) o [Microsoft Graph](https://graph.microsoft.io/). È possibile visualizzare gli attributi disponibili usando rispettivamente lo [strumento di esplorazione di Azure AD Graph](https://graphexplorer.azurewebsites.net/) e lo [strumento di esplorazione di Microsoft Graph](https://developer.microsoft.com/en-us/graph/graph-explorer).

Attualmente nessun carico di lavoro di Office 365 utilizza questi attributi.

È possibile configurare gli attributi aggiuntivi da sincronizzare nel percorso delle impostazioni personalizzate nell'installazione guidata.
![Procedura guidata per l'estensione dello schema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png)  
L'installazione mostra gli attributi seguenti, che sono candidati validi:

* Tipi di oggetto utente e gruppo
* Attributi a valore singolo: String, Boolean, Integer, Binary
* Attributi multivalore: String, Binary


>[!NOTE]
> Anche se Azure AD Connect supporta la sincronizzazione di attributi AD multivalore con Azure AD come estensioni di directory multivalore, Azure AD non offre attualmente funzionalità che supportino l'uso delle estensioni di directory multivalore.

L'elenco di attributi viene letto dalla cache dello schema creata durante l'installazione di Azure AD Connect. Se è stato esteso lo schema di Active Directory con altri attributi, prima che questi nuovi attributi siano visibili è quindi necessario [aggiornare lo schema della directory](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) .

Un oggetto in Azure AD può avere al massimo 100 attributi delle estensioni della directory. La lunghezza massima consentita è di 250 caratteri. Se il valore di un attributo è più lungo, viene quindi troncato dal motore di sincronizzazione.

Durante l'installazione di Azure AD Connect viene registrata un'applicazione in cui sono disponibili questi attributi. È possibile visualizzare questa applicazione nel portale di Azure.  
![App estensione dello schema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3new.png)

Gli attributi sono preceduti da extension\_{AppClientId}\_. AppClientId ha lo stesso valore per tutti gli attributi nel tenant di Azure AD.

Questi attributi ora sono disponibili tramite **Azure AD Graph**:

È possibile eseguire una query in Azure AD Graph tramite Azure AD Graph Explorer: [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/)

![Grafico](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Oppure tramite l'**API Microsoft Graph**:

È possibile eseguire una query nell'API Microsoft Graph tramite Microsoft Graph Explorer: [https://developer.microsoft.com/en-us/graph/graph-explorer#](https://developer.microsoft.com/en-us/graph/graph-explorer#)

>[!NOTE]
> È necessario richiedere esplicitamente l'attributo da restituire. Per eseguire questa operazione, è possibile selezionare attributi come questo: https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division. Per altre informazioni, vedere [Microsoft Graph: Use query parameters](https://developer.microsoft.com/en-us/graph/docs/concepts/query_parameters#select-parameter) (Microsoft Graph: Usare parametri di query).

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).
