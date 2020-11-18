---
title: Single Sign-On basata su intestazione per le app locali con App Azure AD proxy
description: Informazioni su come fornire Single Sign-On per applicazioni locali protette con l'autenticazione basata su intestazione.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 61bff59114d25b2f3167a34100ec2f742036bb90
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663707"
---
# <a name="header-based-single-sign-on-for-on-premises-apps-with-azure-ad-app-proxy-preview"></a>Single Sign-On basata su intestazione per le app locali con App Azure AD proxy (anteprima)

Il proxy dell'applicazione Azure Active Directory (Azure AD) supporta in modo nativo Single Sign-On l'accesso alle applicazioni che usano intestazioni per l'autenticazione. È possibile configurare i valori di intestazione richiesti dall'applicazione in Azure AD. I valori dell'intestazione verranno inviati all'applicazione tramite il proxy di applicazione. Alcuni vantaggi dell'utilizzo del supporto nativo per l'autenticazione basata su intestazione con il proxy di applicazione includono:  

* **Semplifica la fornitura dell'accesso remoto alle app locali** : il proxy app consente di semplificare l'architettura di accesso remoto esistente. È possibile sostituire l'accesso VPN a queste app. È anche possibile rimuovere le dipendenze dalle soluzioni di identità locali per l'autenticazione. Gli utenti non noteranno nulla di diverso quando eseguono l'accesso per usare le applicazioni aziendali. Possono comunque lavorare da qualsiasi luogo e dispositivo.  

* **Nessun software aggiuntivo o modifiche alle app** : è possibile usare i connettori del proxy di applicazione esistenti e non richiede l'installazione di software aggiuntivo.  

* **Elenco esteso di attributi e trasformazioni disponibili** : tutti i valori di intestazione disponibili sono basati su attestazioni standard emesse da Azure ad. Tutti gli attributi e le trasformazioni disponibili per la [configurazione delle attestazioni per le applicazioni SAML o OIDC](../develop/active-directory-saml-claims-customization.md#attributes) sono disponibili anche per l'uso come valori di intestazione. 

## <a name="pre-requisites"></a>Prerequisiti
Prima di iniziare a usare Single Sign-On per le app di autenticazione basate su intestazione, assicurarsi che l'ambiente sia pronto con le impostazioni e le configurazioni seguenti:
- È necessario abilitare il proxy di applicazione e installare un connettore con una riga di sito per le applicazioni. Vedere l'esercitazione [aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) per informazioni su come preparare l'ambiente locale, installare e registrare un connettore e testare il connettore. 

## <a name="supported-capabilities"></a>Funzionalità supportate

La tabella seguente elenca le funzionalità comuni necessarie per le applicazioni di autenticazione basate su intestazione supportate con il proxy di applicazione. 

|Requisito   |Descrizione|
|----------|-----------|
|SSO federato |In modalità pre-autenticazione, tutte le applicazioni sono protette con l'autenticazione Azure AD e consentono agli utenti di avere Single Sign-On. |
|Accesso remoto |Il proxy di applicazione consente l'accesso remoto all'app. Gli utenti possono accedere all'applicazione da Internet in qualsiasi browser usando l'URL esterno. Il proxy di applicazione non è destinato all'uso dell'accesso aziendale. |
|Integrazione basata su intestazione |Il proxy di applicazione esegue l'integrazione SSO con Azure AD e quindi passa l'identità o altri dati dell'applicazione come intestazioni HTTP all'applicazione. |
|Autorizzazione applicazione |I criteri comuni possono essere specificati in base all'applicazione a cui si accede, all'appartenenza a gruppi dell'utente e ad altri criteri. In Azure AD i criteri vengono implementati usando [l'accesso condizionale](../conditional-access/overview.md). I criteri di autorizzazione delle applicazioni si applicano solo alla richiesta di autenticazione iniziale. |
|Autenticazione per istruzione |È possibile definire criteri per forzare l'autenticazione aggiuntiva, ad esempio, per ottenere l'accesso alle risorse sensibili. |
|Autorizzazione con granularità fine |Fornisce il controllo di accesso a livello di URL. I criteri aggiunti possono essere applicati in base all'URL a cui si accede. L'URL interno configurato per l'app definisce l'ambito dell'app a cui sono applicati i criteri. Viene applicato il criterio configurato per il percorso più granulare.  |

> [!NOTE] 
> Questo articolo descrive come connettere le applicazioni di autenticazione basate su intestazioni a Azure AD usando il proxy di applicazione ed è il modello consigliato. In alternativa, è disponibile anche un modello di integrazione che usa PingAccess con Azure AD per abilitare l'autenticazione basata su intestazione. Per informazioni dettagliate, vedere [autenticazione basata su intestazione per Single Sign-on con il proxy di applicazione e PingAccess](application-proxy-ping-access-publishing-guide.md).

## <a name="how-it-works"></a>Funzionamento

:::image type="content" source="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png" alt-text="Funzionamento del Single Sign-On basato su intestazione con il proxy di applicazione." lightbox="./media/application-proxy-configure-single-sign-on-with-headers/how-it-works.png":::

1. L'amministratore Personalizza i mapping degli attributi richiesti dall'applicazione nel portale di Azure AD. 
2. Quando un utente accede all'app, il proxy di applicazione garantisce che l'utente venga autenticato da Azure AD 
3. Il servizio cloud del proxy di applicazione è in grado di riconoscere gli attributi necessari. Quindi, il servizio recupera le attestazioni corrispondenti dal token ID ricevuto durante l'autenticazione. Il servizio converte quindi i valori nelle intestazioni HTTP richieste come parte della richiesta al connettore. 
4. La richiesta viene quindi passata al connettore, che viene quindi passata all'applicazione back-end. 
5. L'applicazione riceve le intestazioni e può usare queste intestazioni in base alle esigenze. 

## <a name="publish-the-application-with-application-proxy"></a>Pubblicare l'applicazione con il proxy di applicazione

1. Pubblicare l'applicazione seguendo le istruzioni descritte in [pubblicare applicazioni con il proxy di applicazione](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).  
    - Il valore URL interno determina l'ambito dell'applicazione. Se si configura il valore URL interno al percorso radice dell'applicazione, tutti i sottopercorsi sotto la radice riceveranno la stessa configurazione dell'intestazione e la stessa configurazione dell'applicazione. 
    - Creare una nuova applicazione per impostare una configurazione di intestazione o un'assegnazione utente diversa per un percorso più granulare rispetto all'applicazione configurata. Nella nuova applicazione configurare l'URL interno con il percorso specifico richiesto e quindi configurare le intestazioni specifiche necessarie per questo URL. Il proxy di applicazione corrisponderà sempre alle impostazioni di configurazione al percorso più granulare impostato per un'applicazione. 

2. Selezionare **Azure Active Directory**   come **metodo di pre-autenticazione**. 
3. Assegnare un utente di test passando a **utenti e gruppi** e assegnando gli utenti e i gruppi appropriati. 
4. Aprire un browser e passare all' **URL esterno**   dalle impostazioni del proxy di applicazione. 
5. Verificare che sia possibile connettersi all'applicazione. Anche se è possibile connettersi, non è ancora possibile accedere all'app perché le intestazioni non sono configurate. 

## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On 
Prima di iniziare a usare Single Sign-On per le applicazioni basate su intestazione, è necessario avere già installato un connettore del proxy di applicazione e il connettore può accedere alle applicazioni di destinazione. In caso contrario, seguire la procedura descritta in [esercitazione: Azure ad proxy di applicazione](application-proxy-add-on-premises-application.md)   e tornare qui. 

1. Quando l'applicazione viene visualizzata nell'elenco di applicazioni aziendali, selezionarla e selezionare **Single Sign-on**. 
2. Impostare la modalità di Single Sign-On su **basata su intestazione**. 
3. In configurazione di base **Azure Active Directory** verrà selezionato come valore predefinito. 
4. Selezionare la matita Edit (modifica) in intestazioni per configurare le intestazioni da inviare all'applicazione. 
5. Selezionare **Aggiungi nuova intestazione**. Specificare un **nome** per l'intestazione e selezionare l' **attributo** o la **trasformazione** e selezionare dall'elenco a discesa le intestazioni necessarie per l'applicazione.  
    - Per ulteriori informazioni sull'elenco di attributi disponibili, vedere [personalizzazioni delle attestazioni-attributi](../develop/active-directory-saml-claims-customization.md#attributes). 
    - Per ulteriori informazioni sull'elenco di trasformazione disponibile, vedere [personalizzazioni delle attestazioni-trasformazioni](../develop/active-directory-saml-claims-customization.md#claim-transformations)delle attestazioni. 
    - È anche possibile aggiungere un' **intestazione di gruppo**, per inviare tutti i gruppi di cui un utente fa parte o i gruppi assegnati all'applicazione come intestazione. Per ulteriori informazioni sulla configurazione dei gruppi come valore, vedere: [Configure Group claims for Applications](../hybrid/how-to-connect-fed-group-claims.md#add-group-claims-to-tokens-for-saml-applications-using-sso-configuration). 
6. Selezionare Salva. 

## <a name="test-your-app"></a>Test dell'app 

Dopo aver completato tutti questi passaggi, l'app deve essere in esecuzione e disponibile. Per testare l'app: 
1. Aprire un browser e passare all' **URL esterno**   dalle impostazioni del proxy di applicazione. 
2. Accedere con l'account di test assegnato all'app. Se è possibile caricare ed eseguire l'accesso all'applicazione tramite SSO, si è pronti. 


## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è l'accesso Single Sign-On?](what-is-single-sign-on.md)
- [Che cos'è il proxy di applicazione?](what-is-application-proxy.md)
- [Serie di guide di avvio rapido sulla gestione delle applicazioni](view-applications-portal.md)
