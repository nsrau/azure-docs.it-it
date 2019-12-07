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
ms.openlocfilehash: 138ca9bf3352c46b8ac495b58a2fd6d7bafeb658
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74889833"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Servizio di sincronizzazione Azure AD Connect: estensioni della directory
È possibile usare le estensioni della directory per estendere lo schema in Azure Active Directory (Azure AD) con attributi personalizzati dall'istanza di Active Directory locale. Questa funzionalità consente di compilare app line-of-business che utilizzano attributi che continuano a essere gestiti in locale. Questi attributi possono essere utilizzati tramite le [estensioni della directory API Graph di Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) o [Microsoft Graph](https://developer.microsoft.com/graph/). È possibile visualizzare gli attributi disponibili usando rispettivamente lo strumento [Graph Explorer di Azure AD](https://graphexplorer.azurewebsites.net/) e lo strumento [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). È anche possibile usare questa funzionalità per creare gruppi dinamici in Azure AD.

Attualmente nessun carico di lavoro di Office 365 utilizza questi attributi.

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Personalizzare gli attributi da sincronizzare con Azure AD

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

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>Modifiche di configurazione in Azure AD eseguite dalla procedura guidata

Durante l'installazione di Azure AD Connect viene registrata un'applicazione in cui sono disponibili questi attributi. È possibile visualizzare questa applicazione nel portale di Azure. Il nome è sempre **app per estensione dello schema tenant**.

![App estensione dello schema](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Assicurarsi di selezionare **tutte le applicazioni** per visualizzare questa app.

Gli attributi sono preceduti dall' **estensione \_{ApplicationId}\_** . ApplicationId ha lo stesso valore per tutti gli attributi nel tenant del Azure AD. Questo valore sarà necessario per tutti gli altri scenari in questo argomento.

## <a name="viewing-attributes-using-graph"></a>Visualizzazione degli attributi tramite Graph

Questi attributi ora sono disponibili tramite l'API Graph di Azure AD. È possibile eseguire query su questi attributi usando lo strumento [Graph Explorer di Azure AD](https://graphexplorer.azurewebsites.net/).

![Graph Explorer di Azure AD](./media/how-to-connect-sync-feature-directory-extensions/extension4.png)

In alternativa è possibile eseguire una query sugli attributi tramite l'API Microsoft Graph, usando [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> In Microsoft Graph è necessario richiedere gli attributi da restituire. Selezionare in modo esplicito gli attributi seguenti: https\://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com? $select = extension_9d98ed114c4840d298fad781915f27e4_employeeID extension_9d98ed114c4840d298fad781915f27e4_division.
>
> Per altre informazioni, vedere [Microsoft Graph: Usare parametri di query](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter).

## <a name="use-the-attributes-in-dynamic-groups"></a>Usare gli attributi nei gruppi dinamici

Uno degli scenari più utili consiste nell'usare questi attributi nei gruppi di sicurezza dinamica o di Office 365.

1. Creare un nuovo gruppo in Azure AD. Assegnare un nome valido e verificare che il **tipo di appartenenza** sia **Dynamic User**.

   ![Screenshot con un nuovo gruppo](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. Selezionare questa per **aggiungere una query dinamica**. Se si esaminano le proprietà, questi attributi estesi non vengono visualizzati. È necessario aggiungerli per primi. Fare clic su **Ottieni proprietà estensioni personalizzate**, immettere l'ID applicazione e fare clic su **Aggiorna proprietà**.

   ![Schermata in cui sono state aggiunte le estensioni di directory](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. Aprire l'elenco a discesa delle proprietà e notare che gli attributi aggiunti sono ora visibili.

   ![Screenshot con nuovi attributi visualizzati nell'interfaccia utente](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   Completare l'espressione in base ai propri requisiti. In questo esempio la regola è impostata su **(User. extension_9d98ed114c4840d298fad781915f27e4_division-EQ "Sales and marketing")** .

4. Dopo aver creato il gruppo, concedere Azure AD tempo per popolare i membri e quindi esaminare i membri.

   ![Screenshot con membri nel gruppo dinamico](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](how-to-connect-sync-whatis.md).

Ulteriori informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
