---
title: Aggiornamento di Azure MFA | Microsoft Docs
description: "Passaggi e indicazioni per aggiornare il server Azure Multi-Factor Authentication a una versione più recente."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 50bb8ac3-5559-4d8b-a96a-799a74978b14
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.openlocfilehash: 6e4e09f8539aad56f92ad9137f4a6b9eb0d82370
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Aggiornare il server Azure Multi-Factor Authentication alla versione più recente

In questo articolo viene illustrato il processo di aggiornamento del Server Azure multi-Factor Authentication (MFA) alla versione 6.0 o successiva. Se si desidera aggiornare una versione precedente dell'agente PhoneFactor, fare riferimento all'articolo [Aggiornare l'agente PhoneFactor al server Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server-upgrade.md).

Se si esegue l'aggiornamento dalla versione 6.x o precedente alla versione 7.x o successiva, tutti i componenti da .NET 2.0 a .NET 4.5 cambiano. Inoltre per tutti i componenti è necessario l'aggiornamento di Microsoft Visual C++ 2015 1 o versione successiva. Il programma di installazione del server MFA installa entrambe le versioni, x86 e x64, di questi componenti se non sono già installati. Se il Portale utenti e il servizio Web app per dispositivi mobili vengono eseguiti su server diversi, è necessario installare tali pacchetti prima di aggiornare i componenti. È possibile cercare l'aggiornamento più recente di Microsoft Visual C++ 2015 Redistributable nell'[Area download Microsoft](https://www.microsoft.com/en-us/download/). 

## <a name="install-the-latest-version-of-azure-mfa-server"></a>Installare la versione più recente del server Azure MFA

1. Usare le istruzioni in [Scaricare il server Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server.md#download-the-azure-multi-factor-authentication-server) per ottenere la versione più recente del server Azure MFA.
2. Eseguire il backup del file di dati del Server MFA che si trova in C:\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata (prevedendo il percorso di installazione predefinito) sul server principale di MFA.
3. Se si eseguono più server per avere una disponibilità elevata, modificare i sistemi client che eseguono l'autenticazione nel server MFA in modo da arrestare l'invio del traffico ai server che si intende aggiornare. Se si usa un bilanciamento del carico, rimuovere un server MFA dal bilanciamento del carico, eseguire l'aggiornamento e quindi aggiungere nuovamente il server alla farm.
4. Eseguire il nuovo programma di installazione in ogni server MFA. Aggiornare prima i server subordinati perché leggono il vecchio file di dati che viene replicato dal server principale. 

  Non è necessario disinstallare il server MFA corrente prima di eseguire il programma di installazione. Il programma di installazione esegue un aggiornamento sul posto. Il percorso di installazione viene rilevato dal registro dall'installazione precedente in modo da eseguire l'installazione nello stesso percorso, ad esempio C:\Program Files\Multi-Factor Authentication Server. 
  
5. Se viene chiesto di installare un pacchetto di aggiornamento di Microsoft Visual C++ 2015 Redistributable, accettare la richiesta. Entrambe le versioni, x86 e x64, del pacchetto vengono installate.
5. Se si usa l'SDK servizio Web, viene chiesto di installare il nuovo SDK servizio Web. Quando si installa il nuovo SDK servizio Web, assicurarsi che il nome della directory virtuale corrisponda alla directory virtuale installata in precedenza (ad esempio MultiFactorAuthWebServiceSdk).
6. Ripetere i passaggi in tutti i server subordinati. Alzare il livello di uno dei server subordinati a server principale, quindi aggiornare il vecchio server principale. 

## <a name="upgrade-the-user-portal"></a>Aggiornare il Portale utenti

1. Eseguire il backup del file web.config che si trova nella directory virtuale del percorso di installazione del Portale utenti (ad esempio C:\inetpub\wwwroot\MultiFactorAuth). Se sono state apportate delle modifiche al tema predefinito, eseguire il backup anche per la cartella App_Themes\Predefinito. È preferibile creare una copia della cartella Predefinito e creare un nuovo tema per modificare il tema predefinito.
2. Se il Portale utenti è in esecuzione nello stesso server come gli altri componenti del server MFA, l'installazione del server MFA richiede di aggiornare il Portale utenti. Accettare la richiesta e installare l'aggiornamento del Portale utenti. Assicurarsi che il nome della directory virtuale corrisponda alla directory virtuale installata in precedenza, ad esempio MultiFactorAuth.
3. Se il Portale utenti è installato sul proprio server, copiare il file MultiFactorAuthenticationUserPortalSetup64.msi dal percorso di installazione di uno dei server MFA e inserirlo nel server Web del Portale utenti. Eseguire il programma di installazione. 

  Se si verifica un errore che informa che è necessario eseguire l'aggiornamento di Microsoft Visual C++ 2015 Redistributable 1 o di una versione successiva, scaricare e installare il pacchetto di aggiornamento più recente dall'[Area download Microsoft](https://www.microsoft.com/download/). Installare le versioni x86 e x64.

4. Dopo aver installato il software del Portale utenti aggiornato, confrontare il backup del file web.config fatto nel passaggio 1 con il nuovo file web.config. Se non sono presenti nuovi attributi nel nuovo file web.config, copiare il file di backup web.config nella directory virtuale per sovrascrivere quella nuova. È inoltre possibile copiare e incollare i valori appSettings e l'URL di SDK servizio Web dal file di backup in un nuovo file web.config.

Se il Portale utenti è su più server, ripetere l'installazione su tutti i server. 


## <a name="upgrade-the-mobile-app-web-service"></a>Aggiornare il servizio Web app per dispositivi mobili

1. Eseguire il backup del file web.config che si trova nella directory virtuale del percorso di installazione del servizio Web app per dispositivi mobili (ad esempio C:\inetpub\wwwroot\app or C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService).
2. Copiare il file MultiFactorAuthenticationMobileAppWebServiceSetup64.msi dal percorso di installazione dei server MFA e inserirlo nel server Web di registrazione dell'app per dispositivi mobili.
3. Eseguire il programma di installazione. 

  Se si verifica un errore che informa che è necessario eseguire l'aggiornamento di Microsoft Visual C++ 2015 Redistributable 1 o di una versione successiva, scaricare e installare il pacchetto di aggiornamento più recente dall'[Area download Microsoft](https://www.microsoft.com/download/). Installare le versioni x86 e x64.

4. Dopo avere installato l'aggiornamento del software per il servizio Web app per dispositivi mobili, confrontare il file web.config di cui è stato eseguito il backup nel passaggio 1 con il nuovo file web.config. Se non sono presenti nuovi attributi nel nuovo file web.config, è possibile ricopiare il file web.config salvato nella directory virtuale e sovrascrivere quello nuovo. È inoltre possibile copiare e incollare i valori appSettings e l'URL di SDK servizio Web dal file di backup in un nuovo file web.config.

Se il servizio Web app per dispositivi mobili è su più server, ripetere l'installazione su tutti i server. 

## <a name="upgrade-the-ad-fs-adapters"></a>Aggiornare le schede AD FS


### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Se MFA viene eseguito su server diversi da AD FS

Queste istruzioni sono valide solo se si esegue il server Multi-Factor Authentication indipendentemente dai server AD FS. Se entrambi i servizi vengono eseguiti sugli stessi server, saltare questa sezione e passare alla fase di installazione. 

1. Salvare una copia del file MultiFactorAuthenticationAdfsAdapter.config registrato in AD FS o esportare la configurazione esistente in un file usando il comando PowerShell seguente: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`. Il nome della scheda è "WindowsAzureMultiFactorAuthentication" o "AzureMfaServerAuthentication" a seconda della versione precedentemente installata.
2. Copiare i file seguenti dal percorso di installazione del server MFA nei server AD FS:

  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config

3. Modificare lo script Register-MultiFactorAuthenticationAdfsAdapter.ps1 aggiungendo `-ConfigurationFilePath [path]` alla fine del comando `Register-AdfsAuthenticationProvider`. Sostituire *[percorso]* con il percorso completo del file MultiFactorAuthenticationAdfsAdapter.config o del file di configurazione esportato nel passaggio precedente. 

  Controllare gli attributi nel nuovo file MultiFactorAuthenticationAdfsAdapter.config per verificare che corrispondano al vecchio file di configurazione. Se sono stati aggiunti o rimossi attributi nella nuova versione, copiare i valori degli attributi dal file di configurazione precedente in quello nuovo o modificare il file di configurazione precedente per farlo corrispondere a quello nuovo.

### <a name="install-new-ad-fs-adapters"></a>Installare le nuove schede AD FS

> [!IMPORTANT] 
> Gli utenti non dovranno eseguire la verifica in due passaggi durante i passaggi 3-8 di questa sezione. Se ADFS è configurato in più cluster, è possibile rimuovere, aggiornare e ripristinare ogni cluster nella farm indipendentemente da altri cluster in modo da evitare tempi di inattività.

1. Rimuovere alcuni server AD FS dalla farm. Aggiornare questi server, mentre gli altri sono ancora in esecuzione.
2. Installare la nuova scheda AD FS su ogni server rimosso dalla farm AD FS. Se il server MFA è installato su ogni server ADFS, è possibile aggiornare tramite l'esperienza utente dell'amministratore del server MFA. Altrimenti fare l'aggiornamento eseguendo MultiFactorAuthenticationAdfsAdapterSetup64.msi. 

  Se si verifica un errore che informa che è necessario eseguire l'aggiornamento di Microsoft Visual C++ 2015 Redistributable 1 o di una versione successiva, scaricare e installare il pacchetto di aggiornamento più recente dall'[Area download Microsoft](https://www.microsoft.com/download/). Installare le versioni x86 e x64.

3. Passare ad **AD FS** > **Criteri di autenticazione** > **Edit Global MultiFactor Authentication Policy** (Modifica i criteri di autenticazione a più fattori globali). Deselezionare **WindowsAzureMultiFactorAuthentication** o **AzureMfaServerAuthentication** a seconda della versione precedentemente installata. 

  Dopo avere completato questo passaggio, la verifica in due passaggi tramite il server MFA non sarà disponibile in questo cluster AD FS fino al completamento del passaggio 8.

4. Annullare la registrazione della versione precedente della scheda AD FS eseguendo lo script di PowerShell Unregister-MultiFactorAuthenticationAdfsAdapter.ps1. Assicurarsi che il parametro *-Name* ("WindowsAzureMultiFactorAuthentication" o "AzureMFAServerAuthentication") corrisponda al nome visualizzato nel passaggio 3. Questo si applica a tutti i server nello stesso cluster AD FS poiché è presente una configurazione centrale.
5. Registrare la nuova scheda AD FS eseguendo lo script di PowerShell Register-MultiFactorAuthenticationAdfsAdapter.ps1. Questo si applica a tutti i server nello stesso cluster AD FS poiché è presente una configurazione centrale.
6. Riavviare il nuovo servizio AD FS su ogni server rimosso dalla farm AD FS.
7. Aggiungere nuovamente i server aggiornati alla farm AD FS e rimuovere gli altri server della farm.
8. Passare ad **AD FS** > **Criteri di autenticazione** > **Edit Global MultiFactor Authentication Policy** (Modifica i criteri di autenticazione a più fattori globali). Controllare **AzureMfaServerAuthentication**.
9. Ripetere il passaggio 2 per aggiornare i server rimossi dalla farm AD FS e riavviare il servizio AD FS in tali server.
10. Aggiungere di nuovo i server alla farm AD FS.

## <a name="next-steps"></a>Passaggi successivi

- Consultare gli esempi di [Scenari avanzati con Azure Multi-Factor Authentication e VPN di terze parti](multi-factor-authentication-advanced-vpn-configurations.md)

- [Sincronizzazione del server MFA con Windows Server Active Directory](multi-factor-authentication-get-started-server-dirint.md)

- [Configurare l'autenticazione di Windows](multi-factor-authentication-get-started-server-windows.md) per le applicazioni
