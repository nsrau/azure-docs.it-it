<properties
   pageTitle="Servizio di sincronizzazione Azure AD Connect: estensioni della directory | Microsoft Azure"
   description="Questo argomento illustra la funzionalità relativa alle estensioni della directory in Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/19/2016"
   ms.author="andkjell"/>

# Servizio di sincronizzazione Azure AD Connect: estensioni della directory
Le estensioni della directory consentono di estendere lo schema in Azure AD con attributi personalizzati da Active Directory in locale. Questa funzionalità consente di compilare app line-of-business che utilizzano attributi che continuano a essere gestiti in locale. Questi attributi possono essere utilizzati tramite le [estensioni della directory Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) o [Microsoft Graph](https://graph.microsoft.io/). È possibile visualizzare gli attributi disponibili usando rispettivamente lo [strumento di esplorazione di Azure AD Graph](https://graphexplorer.cloudapp.net) e lo [strumento di esplorazione di Microsoft Graph](https://graphexplorer2.azurewebsites.net/).

Attualmente nessun carico di lavoro di Office 365 utilizza questi attributi.

È possibile configurare gli attributi aggiuntivi da sincronizzare nel percorso delle impostazioni personalizzate nell'installazione guidata. ![Procedura guidata per l'estensione dello schema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png) L'installazione mostra gli attributi seguenti, che sono candidati validi:

- Tipi di oggetto utente e gruppo
- Attributi a valore singolo: String, Boolean, Integer, Binary
- Attributi multivalore: String, Binary

L'elenco di attributi viene letto dalla cache creata durante l'installazione di Azure AD Connect. Se è stato esteso lo schema di Active Directory con altri attributi, prima che questi nuovi attributi siano visibili è necessario [aggiornare lo schema della directory](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema).

Un oggetto può avere al massimo 100 attributi delle estensioni della directory. La lunghezza massima consentita è di 250 caratteri. Se il valore di un attributo è più lungo, viene troncato dal motore di sincronizzazione.

Durante l'installazione di Azure AD Connect viene registrata un'applicazione in cui sono disponibili questi attributi. È possibile visualizzare questa applicazione nel portale di Azure. ![App estensione dello schema](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3.png)

Questi attributi ora sono disponibili tramite Graph: ![Grafico](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

Gli attributi sono preceduti da extension\_{AppClientId}\_. AppClientId ha lo stesso valore per tutti gli attributi nella directory di Azure AD.

## Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0824_2016-->