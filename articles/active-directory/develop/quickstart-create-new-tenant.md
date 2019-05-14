---
title: Creare un tenant di Azure Active Directory | Microsoft Docs
description: Informazioni su come creare un tenant di Azure AD per la registrazione e la creazione di applicazioni.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 02/05/2019
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f569b497afe01db0fc4c3aa29589e6f2c538faf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58108662"
---
# <a name="quickstart-set-up-a-dev-environment"></a>Avvio rapido: Configurare un ambiente di sviluppo

Microsoft Identity Platform consente agli sviluppatori di creare app che si rivolgono a un'ampia gamma di identità e ambienti Microsoft 365 personalizzati. Per iniziare a usare Microsoft Identity Platform occorre accedere a un ambiente, detto anche tenant di Azure AD, in grado di registrare e gestire le app, accedere ai dati di Microsoft 365 e distribuire le restrizioni di accesso condizionale e tenant personalizzate.

Un tenant è una rappresentazione di un'organizzazione. È un'istanza dedicata di Azure AD che un'organizzazione o uno sviluppatore di app riceve quando crea una relazione con Microsoft, come l'iscrizione ad Azure, Microsoft Intune o Microsoft 365.

Ogni tenant di Azure AD è distinto e separato dagli altri tenant di Azure AD e possiede una propria rappresentazione di identità aziendali e dell'istituto di istruzione, identità di consumer (se si tratta di un tenant di Azure AD B2C) e registrazioni di app. La registrazione di un'app all'interno del tenant può consentire le autenticazioni dagli account solo all'interno del tenant o in tutti i tenant.

## <a name="determining-environment-type"></a>Scelta del tipo di ambiente

È possibile creare due tipi di ambienti. La decisione del tipo più adatto si basa unicamente sui tipi di utenti che eseguiranno l'autenticazione nell'app:

* account aziendali, e dell'istituto di istruzione (account di Azure AD) o account Microsoft (ad esempio outlook.com e live.com).
* Account di social e locali (Azure AD B2C)

La guida introduttiva è suddivisa in due scenari a seconda del tipo di app che si vuole creare. Per altro supporto su come definire come target un tipo di identità, vedere [Microsoft Identity Platform](about-microsoft-identity-platform.md).

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>Account aziendali e dell'istituto di istruzione o account Microsoft personali

### <a name="use-an-existing-tenant"></a>Usare un tenant esistente

Molti sviluppatori dispongono già di tenant attraverso sottoscrizioni o servizi associati ai tenant di Azure AD, ad esempio gli abbonamenti di Microsoft 365 o le sottoscrizioni di Azure.

1. Per controllare il tenant, accedere al [portale di Azure](https://portal.azure.com) con l'account che si vuole usare per gestire l'applicazione.
1. Controllare nell'angolo in alto a destra. Se si dispone già di un tenant, verrà effettuata automaticamente la connessione e si vedrà il nome del tenant direttamente sotto il nome dell'account.
   * Se si passa il puntatore sul nome dell'account nella parte superiore destra del portale di Azure, verranno visualizzati il nome utente, l'indirizzo di posta elettronica, un ID di directory/tenant (GUID) e il dominio.
   * Se l'account è associato a più tenant, è possibile selezionare il nome dell'account per aprire un menu che consente di spostarsi tra i tenant. Ogni tenant ha un proprio ID.

> [!TIP]
> Se è necessario individuare l'ID tenant, è possibile:
> * Passare il mouse sul nome dell'account per ottenere l'ID directory/tenant oppure
> * Selezionare **Azure Active Directory > Proprietà > ID directory** nel portale di Azure.

Se all'account non è associato un tenant, verrà visualizzato un GUID sotto il nome dell'account e non sarà possibile eseguire operazioni come la registrazione di app finché non si applica la procedura descritta nella sezione seguente.

### <a name="create-a-new-azure-ad-tenant"></a>Creare un nuovo tenant Azure AD

Se non si dispone ancora di un tenant di Azure AD o se ne vuole creare uno nuovo per lo sviluppo, seguire l'[esperienza di creazione della directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory). Per creare il nuovo tenant, sarà necessario specificare le informazioni seguenti:

- **Nome organizzazione**
- **Dominio iniziale**: farà parte di *.onmicrosoft.com. È possibile personalizzare il dominio in un secondo momento.
- **Paese o area geografica**

> [!NOTE]
> Quando si assegna un nome al tenant, usare caratteri alfanumerici. I caratteri speciali non sono consentiti. Il nome non deve superare 256 caratteri.

## <a name="social-and-local-accounts"></a>Account di social e locali

Per iniziare a creare app che consentono l'accesso agli account di social e locali, è necessario creare un tenant di Azure AD B2C. Per iniziare, seguire la procedura descritta in [Creazione di un tenant di Azure AD B2C](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="next-steps"></a>Passaggi successivi

* Provare un'esercitazione introduttiva di scrittura del codice e iniziare ad autenticare gli utenti. 
* Per altri esempi di codice approfonditi, consultare la sezione **Esercitazioni** della documentazione.
* Si intende distribuire l'app nel cloud? Verificare [la distribuzione di contenitori in Azure](https://docs.microsoft.com/azure/index#pivot=products&panel=containers). 
