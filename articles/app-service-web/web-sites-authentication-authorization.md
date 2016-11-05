---
title: Eseguire l'autenticazione con l'istanza locale di Active Directory nell'app Azure | Microsoft Docs
description: Informazioni sulle diverse opzioni per le app line-of-business nel servizio app di Azure per l'autenticazione con l'istanza di Active Directory locale
services: app-service
documentationcenter: ''
author: cephalin
manager: wpickett
editor: jimbe

ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 08/31/2016
ms.author: cephalin

---
# Eseguire l'autenticazione con l'istanza locale di Active Directory nell'app Azure
Questo articolo illustra come eseguire l'autenticazione con l'istanza locale di Active Directory (AD) nel [servizio app di Azure](../app-service/app-service-value-prop-what-is.md). Un'app Azure è ospitata nel cloud, ma esistono modi per eseguire l'autenticazione degli utenti di Active Directory locale in modo sicuro.

## Eseguire l'autenticazione tramite Azure Active Directory
Un tenant di Azure Active Directory può essere sincronizzato a livello di directory con un'istanza di Active Directory locale. Questo approccio consente agli utenti di Active Directory di accedere all'app da internet e autenticarsi con le credenziali locali. Inoltre, il servizio app di Azure offre una [soluzione chiavi in mano per questo metodo](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). Con pochi clic su un pulsante è possibile abilitare l'autenticazione con un tenant sincronizzato a livello di directory per l'app Azure. Questo approccio offre i vantaggi seguenti:

* Non richiede alcun codice di autenticazione nell'app. Consente al servizio app di eseguire automaticamente l'autenticazione, permettendo all'utente di dedicare tempo alle funzionalità da aggiungere all'app.
* L'[API Graph di Azure AD](http://msdn.microsoft.com/library/azure/hh974476.aspx) abilita l'accesso ai dati della directory dall'app Azure.
* Fornisce l'accesso Single Sign-On a [tutte le applicazioni supportate da Azure Active Directory](/marketplace/active-directory/), tra cui Office 365, Dynamics CRM Online, Microsoft Intune e migliaia di applicazioni cloud non Microsoft.
* Azure Active Directory supporta il controllo degli accessi in base al ruolo. È possibile usare il modello [Authorize(Roles="X")] con modifiche minime al codice.

Per informazioni su come scrivere un'app line-of-business in Azure che esegue l'autenticazione con Azure Active Directory, vedere [Creare un'app line-of-business in Azure con l'autenticazione di Azure Active Directory](web-sites-dotnet-lob-application-azure-ad.md).

## Eseguire l'autenticazione tramite un servizio token di sicurezza locale
Se è disponibile un servizio token di sicurezza locale, quale Active Directory Federation Services (AD FS), è possibile usarlo per la federazione di autenticazione per l'app Azure. Questo approccio è migliore quando i criteri aziendali impediscono che i dati di Active Directory vengano archiviati in Azure. Tenere tuttavia presente quanto segue:

* La topologia del servizio token di sicurezza deve essere distribuita in locale, con sovraccarico in termini di costi e gestione.
* Solo gli amministratori di AD FS possono configurare i [trust della relying party e le regole attestazioni ](http://technet.microsoft.com/library/dd807108.aspx). D'altra parte, è possibile gestire e personalizzare le [attestazioni](http://technet.microsoft.com/library/ee913571.aspx) per ogni applicazione.
* L'accesso ai dati dell'istanza locale di Active Directory tramite il firewall aziendale richiede una soluzione separata.

Per informazioni su come scrivere un'app line-of-business in Azure che esegue l'autenticazione con il servizio token di sicurezza, vedere [Creare un'app line-of-business in Azure con l'autenticazione di AD FS](web-sites-dotnet-lob-application-adfs.md).

<!---HONumber=AcomDC_0831_2016-->