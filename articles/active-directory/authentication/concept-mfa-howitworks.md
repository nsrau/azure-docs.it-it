---
title: Azure Multi-Factor Authentication - Come funziona
description: Azure multi-Factor Authentication contribuisce a salvaguardare l'accesso a dati e applicazioni rispondendo alla richiesta degli utenti di poter usare un processo di accesso semplice.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 46c99011a22f855f6faf53e03169b2d1e4c6ce85
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669008"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Funzionamento di Azure Multi-Factor Authentication

La sicurezza della verifica in due passaggi sta nel suo approccio a livelli. La manomissione di più fattori rappresenta una sfida significativa per gli autori di attacchi. Tuttavia, anche se un autore di un attacco riesce a ottenere la password dell'utente, questa risulta inutile se non è in possesso del metodo di autenticazione aggiuntivo. In genere richiede due o più dei metodi di autenticazione seguenti:

* Un'informazione nota (in genere una password)
* Un oggetto che si possiede (un dispositivo attendibile non facile da duplicare, ad esempio un telefono)
* Una caratteristica fisica dell'utente (biometrica)

<center>![Immagine concettuale relativa ai metodi di autenticazione](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication consente di proteggere l'accesso ai dati e alle applicazioni, garantendo al tempo stesso un utilizzo semplice agli utenti. Offre ulteriore sicurezza richiedendo una seconda forma di autenticazione nonché un'autenticazione avanzata tramite una gamma di [metodi di autenticazione semplici](concept-authentication-methods.md).

## <a name="how-to-get-multi-factor-authentication"></a>Come ottenere Multi-Factor Authentication

Multi-Factor Authentication è inclusa in quanto parte delle offerte seguenti:

* **Licenze di Azure Active Directory Premium**: utilizzo del servizio Microsoft Azure Multi-Factor Authentication (Cloud) o server Microsoft Azure Multi-Factor Authentication (in locale) con funzionalità complete.
   * **Servizio Azure MFA (Cloud)** - **Questa opzione è il percorso consigliato per le nuove distribuzioni**. Azure MFA nel cloud non richiede alcuna infrastruttura locale e può essere usato con gli utenti federati o solo cloud.
   * **Server Azure MFA**: può essere un'opzione se l'organizzazione desidera gestire gli elementi di infrastruttura associati ed è stata distribuita AD FS nell'ambiente locale.
* **Multi-Factor Authentication per Office 365**: nella sottoscrizione è incluso un subset di funzionalità di autenticazione a più fattori di Azure. Per altre informazioni sull'autenticazione a più fattori per Office 365, vedere l'articolo [Pianificare l'autenticazione a più fattori per le distribuzioni di Office 365](https://support.office.com/article/plan-for-multi-factor-authentication-for-office-365-deployments-043807b2-21db-4d5c-b430-c8a6dee0e6ba).
* **Amministratori globali di Azure Active Directory**: per proteggere gli account di amministratore globale è disponibile un subset di funzionalità di Azure multi-Factor Authentication.

> [!NOTE]
> I nuovi clienti non possono più acquistare Azure Multi-Factor Authentication come offerta autonoma valida dal 1 ° settembre 2018. L'autenticazione a più fattori continuerà a essere una funzionalità disponibile nelle licenze di Azure AD Premium.

### <a name="auth-provider-or-mfa-license"></a>Licenza di provider di autenticazione o autenticazione a più fattori

Se si dispone di Azure AD Premium o di un altro pacchetto di licenza che include Azure AD Premium, Azure MFA è già incluso. L'organizzazione non ha l'esigenza di avere elementi aggiuntivi per estendere la funzionalità di verifica in due passaggi a tutti gli utenti. È sufficiente assegnare una licenza a un utente e quindi è possibile attivare l'autenticazione MFA.

Se non si dispone di licenze che includono Azure MFA o se non dispone di sufficienti licenze per coprire tutti gli utenti, è possibile creare un [Provider di autenticazione MFA](concept-mfa-authprovider.md) per estendere le funzionalità complete di autenticazione a più fattori per gli utenti che ne hanno bisogno.

> [!IMPORTANT]
> Se non si dispone di licenze sufficienti per tutti gli utenti, è possibile creare un provider di Multi-Factor Authentication in base al numero di utenti per coprire il resto dell'organizzazione. Non creare un provider di Multi-Factor Authentication in base al numero di autenticazioni. Questo perché si rischia di pagare per richieste di verifica provenienti da utenti che dispongono già di licenze.

## <a name="supportability"></a>Supporto

Poiché la maggior parte degli utenti è abituata a usare le password solo per l'autenticazione, è importante che l'organizzazione si metta in comunicazione con tutti gli utenti per informarli in merito al processo. La consapevolezza può ridurre la probabilità che gli utenti contattino l'help desk per problemi di lieve entità riguardanti l'autenticazione a più fattori. Esistono tuttavia alcuni scenari in cui è necessario disabilitare temporaneamente MFA. Per sapere come gestire questi scenari, usare le linee guida seguenti:

* Istruire il personale di supporto per le situazioni in cui l'utente non riesce ad accedere perché non ha accesso ai suoi metodi di autenticazione oppure perchè questi ultimi non funzionano correttamente.
   * Usando i criteri di accesso condizionale per il servizio Azure MFA, il personale di supporto può aggiungere un utente a un gruppo che è escluso da un criterio che richiede un'autenticazione a più fattori.
   * Il personale di supporto è possibile può abilitare un bypass monouso temporaneo per gli utenti di Azure MFA Server per consentire loro di autenticarsi senza la verifica in due passaggi. Il bypass è temporaneo e scade dopo un numero di secondi specificato.
   * Usando i criteri di accesso condizionale per il servizio Azure MFA, il personale di supporto può aggiungere un utente a un gruppo che è escluso da un criterio che richiede un'autenticazione a più fattori.
* È consigliabile usare indirizzi IP attendibili o posizioni specifiche come metodo per ridurre al minimo le richieste di verifica in due passaggi. Questa funzionalità consente agli amministratori di un tenant gestito o federato di ignorare la verifica in due passaggi per gli utenti che accedono da un percorso di rete affidabile, ad esempio la rete Intranet dell'organizzazione.
* Distribuire [Azure AD Identity Protection](../active-directory-identityprotection.md) e attivare la verifica in due passaggi basata su eventi di rischio.

## <a name="next-steps"></a>Passaggi successivi

- Ottenere un [piano di distribuzione](https://aka.ms/MFADeploymentPlan) guidato per l'autenticazione a più fattori

- Trovare i dettagli sulle [licenze per gli utenti](concept-mfa-licensing.md)

- Ottenere dettagli sulla [versione da distribuire](concept-mfa-whichversion.md)

- Trovare risposte in [Domande frequenti](multi-factor-authentication-faq.md)
