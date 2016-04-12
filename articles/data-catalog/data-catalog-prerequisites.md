<properties
   pageTitle="Prerequisiti del Catalogo dati di Azure"
   description="Prerequisiti per iniziare a usare il Catalogo dati di Azure"
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="03/30/2016"
   ms.author="maroche"/>

# Prerequisiti per iniziare a usare il Catalogo dati di Azure

## Prerequisiti del Catalogo dati di Azure

Vi sono alcune cose di cui è necessario occuparsi prima di poter impostare il **Catalogo dati di Azure**. Nessuna c’è da preoccuparsi: occorre poco tempo.

## Azure Active Directory

Azure Active Directory (Azure AD) è un servizio che offre semplici e pratiche funzionalità di gestione delle identità e degli accessi, sia nel cloud sia in locale. Gli utenti possono utilizzare un singolo account aziendale o dell'istituto di istruzione per eseguire il Single Sign-On a qualsiasi applicazione Web locale o nel cloud. Il Catalogo dati di Azure utilizza Azure AD per autenticare il Single Sign-On. Per ulteriori informazioni, vedere [Introduzione all'uso di Azure AD](active-directory-get-started.md).

## Configurazione dei criteri di Azure Active Directory

In alcuni casi, è possibile che si verifichi una situazione in cui gli utenti possono accedere al portale del Catalogo dati di Azure, ma, quando tentano di accedere allo strumento di registrazione dell'origine dati, viene restituito un messaggio di errore che impedisce l'accesso. Questo errore si verifica solo quando l'utente è nella rete aziendale o si connette dall'esterno della rete aziendale.

Lo strumento di registrazione dell'origine dati usa l'autenticazione basata su form per convalidare gli accessi degli utenti in Active Directory. Per accedere, l'autenticazione basata su form deve essere abilitata nei criteri di autenticazione globali da un amministratore di Active Directory.

I criteri di autenticazione globali consentono di abilitare i metodi di autenticazione separatamente per le connessioni Extranet e Intranet, come illustrato di seguito. Se l'autenticazione basata su form non è abilitata per la rete da cui l'utente si connette, è possibile che si verifichino errori di accesso.

 ![Criteri di autenticazione globali di Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

Per altre informazioni, vedere [Configurare i criteri di autenticazione](https://technet.microsoft.com/library/dn486781.aspx).


## Sottoscrizione di Azure
Le sottoscrizioni di Azure consentono di organizzare l'accesso alle risorse del servizio cloud, come Catalogo dati di Azure. Consentono inoltre di controllare come l'utilizzo delle risorse viene segnalato, fatturato e pagato. Ogni sottoscrizione può disporre di un’impostazione di fatturazione e pagamento diversa, in modo da poter avere sottoscrizioni e piani diversi per reparto, progetto, ufficio regionale e così via. Ogni servizio cloud appartiene a una sottoscrizione ed è necessario che la sottoscrizione sia disponibile prima di impostare il Catalogo dati di Azure. Per ulteriori informazioni, vedere [Gestire account, sottoscrizioni e ruoli amministrativi](https://msdn.microsoft.com/library/azure/hh531793.aspx).

<!---HONumber=AcomDC_0330_2016-->