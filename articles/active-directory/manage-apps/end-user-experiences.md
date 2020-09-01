---
title: Esperienza dell'utente finale per le applicazioni-Azure Active Directory
description: Azure Active Directory (Azure AD) fornisce diverse soluzioni personalizzabili per distribuire le applicazioni agli utenti finali all'interno dell'organizzazione.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: kenwith
ms.reviewer: arvindh
ms.openlocfilehash: d3b7dbbb9aab29e083795025ad5bf7381a4fa0ee
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89231215"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Esperienze per gli utenti finali per le applicazioni in Azure Active Directory

Azure Active Directory (Azure AD) fornisce diverse soluzioni personalizzabili per distribuire le applicazioni agli utenti finali all'interno dell'organizzazione:

* Azure AD app personali
* Applicazione di avvio di Office 365
* Accesso diretto alle applicazioni federate
* Collegamenti diretti per applicazioni federate, basate su password o esistenti

I metodi che è possibile scegliere per la distribuzione nell'organizzazione sono a discrezione dell'utente.

## <a name="azure-ad-my-apps"></a>Azure AD app personali

My Apps at https://myapps.microsoft.com è un portale basato sul Web che consente a un utente finale con un account aziendale in Azure Active Directory di visualizzare e avviare applicazioni a cui è stato concesso l'accesso dall'amministratore Azure ad. Se si è un utente finale con [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), è anche possibile usare le funzionalità di gestione dei gruppi self-service tramite le app personali.

Per impostazione predefinita, tutte le applicazioni sono elencate insieme in un'unica pagina. È tuttavia possibile usare le raccolte per raggruppare le applicazioni correlate e presentarle in una scheda separata, rendendole più facili da trovare. È ad esempio possibile utilizzare le raccolte per creare raggruppamenti logici di applicazioni per ruoli di processo, attività, progetti e così via specifici. Per informazioni, vedere [creare raccolte nel portale app personali](access-panel-collections.md). 

App personali è separata dalla portale di Azure e non richiede agli utenti di avere una sottoscrizione di Azure o una sottoscrizione di Office 365.

Per altre informazioni su Azure AD app personali, vedere [Introduzione alle app personali](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="office-365-application-launcher"></a>Applicazione di avvio di Office 365

Per le organizzazioni che hanno distribuito Office 365, le applicazioni assegnate agli utenti tramite Azure AD verranno visualizzate anche nel portale di Office 365 all'indirizzo [https://portal.office.com/myapps](https://portal.office.com/myapps) . Questo rende facile e comodo per gli utenti all’interno di un'organizzazione avviare le proprie applicazioni senza dover utilizzare un secondo portale ed è la soluzione di avvio delle applicazioni consigliata per le organizzazioni che utilizzano Office 365.

Per ulteriori informazioni sull'avvio di applicazioni di Office 365, vedere [Visualizzazione dell'applicazione nell’applicazione di avvio di Office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

## <a name="direct-sign-on-to-federated-apps"></a>Accesso diretto alle applicazioni federate

La maggior parte delle applicazioni federate che supportano OpenID Connect, WS-Federation o SAML 2.0 supportano anche la possibilità per gli utenti di avviare l'applicazione e quindi ottenere l’accesso tramite Azure AD con il reindirizzamento automatico oppure facendo clic su un collegamento per accedere. Questa operazione è nota come accesso avviato dal provider di servizi e la maggior parte delle applicazioni federate nella raccolta di applicazioni Azure AD supporta questa operazione. per informazioni dettagliate, vedere la documentazione collegata dalla configurazione portale di Azure guidata di Single Sign-On dell'app.

## <a name="direct-sign-on-links"></a>Collegamenti diretti Single Sign-On

Azure AD supporta anche collegamenti diretti Single Sign-On alle singole applicazioni che supportano Single Sign-On basato su password, Single Sign-On collegato e qualunque forma di Single Sign-On federato.

Questi collegamenti sono URL creati in modo specifico che inviano un utente attraverso il processo di accesso Azure AD per un'applicazione specifica senza richiedere all'utente di avviarli da Azure AD app personali o Office 365. Questi **URL di accesso utente** si trovano nelle proprietà delle applicazioni aziendali disponibili. Nel portale di Azure selezionare **Azure Active Directory** > **Applicazioni aziendali**. Selezionare l'applicazione, quindi selezionare **Proprietà**.

![Esempio di URL di accesso utente nelle proprietà di Twitter](media/end-user-experiences/direct-sign-on-link.png)

Questi collegamenti possono essere copiati e incollati ovunque per fornire un collegamento di accesso all'applicazione selezionata. Questo potrebbe essere all’interno di un messaggio di posta elettronica o in qualsiasi portale personalizzato basato sul Web configurato per l'accesso alle applicazioni dell’utente. Di seguito è riportato un esempio di URL Single Sign-On diretto di Azure AD per Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Analogamente agli URL specifici dell'organizzazione per le mie app, è possibile personalizzare ulteriormente questo URL aggiungendo uno dei domini attivi o verificati per la directory dopo il dominio *MyApps.Microsoft.com* . Questo assicura che eventuali personalizzazioni dell’organizzazione vengano caricate immediatamente nella pagina di accesso senza che l'utente debba immettere prima l'ID utente:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Quando un utente autorizzato fa clic su uno di questi collegamenti specifici dell'applicazione, Visualizza per prima cosa la pagina di accesso dell'organizzazione (presupponendo che non sia già stato eseguito l'accesso) e dopo che l'accesso è stato reindirizzato alla propria app senza fermarsi prima alle app personali. Se l'utente non dispone dei prerequisiti per l'accesso all'applicazione, ad esempio l'estensione browser di accesso singolo basato su password, il collegamento richiederà all'utente di installare l'estensione mancante. L'URL del collegamento rimane costante se la configurazione Single Sign-On per l'applicazione viene modificata.

Questi collegamenti usano gli stessi meccanismi di controllo di accesso delle app personali e di Office 365 e solo gli utenti o i gruppi che sono stati assegnati all'applicazione nella portale di Azure potranno eseguire correttamente l'autenticazione. Tuttavia, qualsiasi utente non autorizzato visualizzerà un messaggio che informa che non è stato concesso l'accesso e viene fornito un collegamento per caricare le app per visualizzare le applicazioni disponibili per le quali hanno accesso.

## <a name="next-steps"></a>Passaggi successivi

* [Serie di guide di avvio rapido sulla gestione delle applicazioni](view-applications-portal.md)
* [Che cos'è Single Sign-On?](what-is-single-sign-on.md)
* [Guida introduttiva all'integrazione di Azure Active Directory con le applicazioni](plan-an-application-integration.md)
