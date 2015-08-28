<properties
	pageTitle="Modello app 2.0 | Microsoft Azure"
	description="Introduzione alla compilazione di app con l'accesso sia per account Microsoft che per Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# Anteprima di Modello app 2.0: Accesso per account Microsoft e utenti di Azure AD nella stessa app

> [AZURE.NOTE]Queste informazioni fanno riferimento all'anteprima pubblica di Modello app 2.0. Per istruzioni su come eseguire l'integrazione con il servizio Azure AD disponibile a livello generale, consultare la [guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md).

In passato, gli sviluppatori di app che intendevano supportare sia gli account Microsoft che Azure Active Directory dovevano eseguire l'integrazione con due sistemi separati. Modello app 2.0 permette concedere l'accesso agli utenti con entrambi i tipi di account. Grazie a una semplice integrazione è possibile raggiungere un pubblico costituito da milioni di utenti, sia con account personali che con account aziendali o dell'istituto di istruzione.

L'app può anche utilizzare un [set di API REST per Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) con entrambi i tipi di account. Al momento, queste API includono le API per la posta, i contatti e i calendari di Outlook. Nel prossimo futuro verranno aggiunti altri servizi.
<!-- TODO: customer reference article -->
<!-- Several apps have already begun to bridge the gap between consumer and enterprise accounts, including: [Boomerang](), [TripIt](), & [Uber](). -->

Modello app 2.0 è disponibile in anteprima. Durante il periodo di anteprima, ci auguriamo di ricevere i commenti e i suggerimenti degli utenti sull'esperienza di utilizzo del nuovo modello di app. In base ai commenti e suggerimenti ricevuti, apporteremo tutte le modifiche necessarie per migliorare il servizio offerto. Non rilasciare un'app per l'ambiente di produzione usando Modello app 2.0 durante questo periodo.
<!-- TODO: Get approval on how it looks  -->

## Introduzione
Esistono due modi per creare un'app con Modello app 2.0. È possibile scegliere di inviare direttamente messaggi del protocollo tramite [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) o [Open ID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). In alternativa, è possibile usare le librerie per creare automaticamente l'app. Per iniziare, è sufficiente scegliere la piattaforma preferita.
<!-- TODO: Finalize this table  -->

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## Novità
Visitare spesso questa pagina per rimanere aggiornati sulle modifiche futura all'anteprima pubblica di Modello app 2.0. Gli aggiornamenti saranno anche resi noti su Twitter all'indirizzo @AzureAD.

- Informazioni sui [tipi di app che si possono creare con Modello app 2.0](active-directory-v2-flows.md).
- Gli sviluppatori che hanno familiarità con Azure Active Directory possono vedere l'articolo che illustra gli [aggiornamenti ai protocolli e le differenze in Modello app 2.0](active-directory-v2-compare.md).
- [Limitazioni, restrizioni e vincoli della versione di anteprima](active-directory-v2-limitations.md) corrente

## Riferimento
I collegamenti seguenti sono utili per un'esplorazione più approfondita della piattaforma:

- Per ottenere supporto su Stack Overflow, usare i tag [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) o [adal](http://stackoverflow.com/questions/tagged/adal).
- Per inviare i commenti e i suggerimenti sulla versione di anteprima, è possibile usare [UserVoice](http://feedback.azure.com/forums/169401-azure-active-directory). Specificare "AppModelv2:" nel titolo del post per permetterci di individuarlo.
- [Riferimento al protocollo di Modello app 2.0](active-directory-v2-protocols.md)
- [Riferimento al token di Modello app 2.0](active-directory-v2-tokens.md)
- [Riferimento all'API REST di Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Ambiti e consenso nell'endpoint versione 2](active-directory-v2-scopes.md)

<!-- TODO: These articles
- [ADAL Library Reference]()
- [v2 Endpoint FAQs](active-directory-v2-faq.md)
-->

<!------HONumber=August15_HO7-->