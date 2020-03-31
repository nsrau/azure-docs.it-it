---
title: 'Servizio di sincronizzazione Azure AD Connect: estensioni della directory | Documentazione Microsoft'
description: Questo argomento illustra la funzionalità relativa alle estensioni della directory in Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80438319a6337dd6f28f9bdca8a428829b6cb0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77917914"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Servizio di sincronizzazione Azure AD Connect: estensioni della directory
È possibile usare le estensioni della directory per estendere lo schema in Azure Active Directory (Azure AD) con attributi personalizzati dall'istanza di Active Directory locale. Questa funzionalità consente di compilare app line-of-business che utilizzano attributi che continuano a essere gestiti in locale. Questi attributi possono essere utilizzati tramite [le estensioni](https://docs.microsoft.com/graph/extensibility-overview
). È possibile visualizzare gli attributi disponibili utilizzando Esplora risorse di [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). È anche possibile usare questa funzionalità per creare gruppi dinamici in Azure AD.

Attualmente nessun carico di lavoro di Office 365 utilizza questi attributi.

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Personalizzare gli attributi da sincronizzare con Azure ADCustomize which attributes to synchronize with Azure AD

È possibile configurare gli attributi aggiuntivi da sincronizzare nel percorso delle impostazioni personalizzate nell'installazione guidata.

>[!NOTE]
>La casella Attributi disponibili fa distinzione tra maiuscole e minuscole.

![Procedura guidata per l'estensione dello schema](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

 L'installazione mostra gli attributi seguenti, che sono candidati validi:

* Tipi di oggetto utente e gruppo
* Attributi a valore singolo: String, Boolean, Integer, Binary
* Attributi multivalore: String, Binary


>[!NOTE]
> Anche se Azure AD Connect supporta la sincronizzazione di attributi Active Directory multivalore con Azure AD come estensioni di directory multivalore, attualmente non è possibile recuperare/usare i dati caricati negli attributi di estensioni di directory multivalore.

L'elenco di attributi viene letto dalla cache dello schema creata durante l'installazione di Azure AD Connect. Se è stato esteso lo schema di Active Directory con altri attributi, prima che questi nuovi attributi siano visibili è necessario [aggiornare lo schema](how-to-connect-installation-wizard.md#refresh-directory-schema).

Un oggetto in Azure AD può avere al massimo 100 attributi per le estensioni della directory. La lunghezza massima consentita è di 250 caratteri. Se il valore di un attributo è più lungo, viene troncato dal motore di sincronizzazione.

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>Modifiche alla configurazione in Azure AD apportate dalla procedura guidataConfiguration changes in Azure AD made by the wizard

Durante l'installazione di Azure AD Connect viene registrata un'applicazione in cui sono disponibili questi attributi. È possibile visualizzare questa applicazione nel portale di Azure. Il nome è sempre **Tenant Schema Extension App**.

![App estensione dello schema](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Assicurati di selezionare **Tutte le applicazioni** per visualizzare questa app.

Gli attributi sono preceduti **dall'estensione \_"ApplicationId"\_**. ApplicationId ha lo stesso valore per tutti gli attributi nel tenant di Azure AD. Questo valore è necessario per tutti gli altri scenari in questo argomento.

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>Visualizzazione degli attributi tramite l'API Microsoft Graph

Questi attributi sono ora disponibili tramite l'API Microsoft Graph, utilizzando [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> Nell'API di Microsoft Graph, è necessario richiedere la restituzione degli attributi. Selezionare in modo `https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division`esplicito gli attributi come questo: .
>
> Per altre informazioni, vedere [Microsoft Graph: Usare parametri di query](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter).

## <a name="use-the-attributes-in-dynamic-groups"></a>Utilizzare gli attributi nei gruppi dinamici

Uno degli scenari più utili consiste nell'utilizzare questi attributi in gruppi di sicurezza dinamica o di Office 365.One of the more useful scenarios is to use these attributes in dynamic security or Office 365 groups.

1. Creare un nuovo gruppo in Azure AD. Assegnare un nome valido e assicurarsi che il **tipo di appartenenza** sia **Utente dinamico**.

   ![Screenshot con un nuovo gruppo](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. Selezionare **Aggiungi query dinamica**. Se si esaminano le proprietà, non verranno visualizzati questi attributi estesi. È necessario aggiungerli prima. Fare clic su **Ottieni proprietà estensione personalizzate**, immettere l'ID applicazione e fare clic su Aggiorna **proprietà**.

   ![Screenshot in cui sono state aggiunte le estensioni di directory](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. Aprire l'elenco a discesa delle proprietà e notare che gli attributi aggiunti sono ora visibili.

   ![Screenshot con i nuovi attributi visualizzati nell'interfaccia utente](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   Completare l'espressione in base alle proprie esigenze. In questo esempio, la regola è impostata su **(user.extension_9d98ed114c4840d298fad781915f27e4_division -eq "Sales and marketing")**.

4. Dopo aver creato il gruppo, concedere ad Azure AD del tempo per popolare i membri e quindi esaminare i membri.

   ![Screenshot con i membri del gruppo dinamico](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](how-to-connect-sync-whatis.md).

Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
