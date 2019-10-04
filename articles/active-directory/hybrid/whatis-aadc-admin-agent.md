---
title: Informazioni sull'agente di amministrazione Azure AD Connect-Azure AD Connect | Microsoft Docs
description: Vengono descritti gli strumenti usati per sincronizzare e monitorare l'ambiente locale con Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/04/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 222dab87ee71870e564e426d7466555893cc565b
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305209"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Cos'è l'agente di amministrazione di Azure AD Connect? 
L'agente di amministrazione di Azure AD Connect è un nuovo componente di Azure Active Directory Connect che può essere installato in un server Azure Active Directory Connect. Viene usato per raccogliere dati specifici dall'ambiente Active Directory che consentono a un tecnico del supporto Microsoft di risolvere i problemi quando si apre un caso di supporto. 

>[!NOTE]
>L'agente di amministrazione non è installato e abilitato per impostazione predefinita.  È necessario installare l'agente per raccogliere i dati per agevolare i casi di supporto.

Quando viene installato, l'agente di amministrazione di Azure AD Connect attende richieste specifiche di dati da Azure Active Directory, ottiene i dati richiesti dall'ambiente di sincronizzazione e li invia a Azure Active Directory, dove viene presentata al supporto tecnico Microsoft. Ingegnere. 

Le informazioni riportate dall'agente di amministrazione Azure AD Connect dall'ambiente non vengono archiviate in alcun modo. viene visualizzato solo al tecnico del supporto Microsoft per assisterle nell'analisi e nella risoluzione dei problemi relativi al Azure Active Directory Connect Il caso di supporto correlato aperto dall'agente di amministrazione di Azure AD Connect non è installato nel server Azure AD Connect per impostazione predefinita. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Installare l'agente di amministrazione di Azure AD Connect nel server di Azure AD Connect 

Prerequisiti:
1.  Azure AD Connect è installato nel server
2.  Azure AD Connect Health è installato nel server

![agente di amministrazione](media/whatis-aadc-admin-agent/adminagent0.png)

I file binari dell'agente di amministrazione Azure AD Connect vengono inseriti nel server AAD Connect. Per installare l'agente, eseguire le operazioni seguenti:

1.  Aprire PowerShell in modalità di amministrazione
2.  Passare alla directory in cui si trova l'applicazione CD "C:\Programmi\Microsoft Azure Active Directory Connect\Tools"
3.  Eseguire ConfigureAdminAgent. ps1

Quando richiesto, immettere le credenziali di amministratore globale Azure AD. Deve corrispondere alle stesse credenziali immesse durante Azure AD Connect installazione.

Dopo l'installazione dell'agente, verranno visualizzati i due nuovi programmi seguenti nell'elenco "Installazione applicazioni" nel pannello di controllo del server: 

![agente di amministrazione](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Quali dati del servizio di sincronizzazione vengono visualizzati presso il tecnico dei servizi Microsoft? 
Quando si apre un caso di supporto, il tecnico supporto tecnico Microsoft può vedere, per un determinato utente, i dati rilevanti in Active Directory, lo spazio connettore Active Directory nel server di Azure Active Directory Connect, lo spazio connettore Azure Active Directory in Azure Active Directory connessione del server e del metaverse nel server di Azure Active Directory Connect. 

Il tecnico supporto tecnico Microsoft non può modificare i dati nel sistema e non può visualizzare alcuna password. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Cosa accade se non si vuole che il tecnico del supporto Microsoft acceda ai dati? 
Una volta installato l'agente, se non si desidera che il tecnico del servizio Microsoft acceda ai dati per una chiamata di supporto, è possibile disabilitare la funzionalità modificando il file di configurazione del servizio come descritto di seguito: 

1.  Aprire **c:\programmi\microsoft Azure ad Connect Administration Agent\AzureADConnectAdministrationAgentService.exe.config** nel blocco note.
2.  Disabilitare l'impostazione **UserDataEnabled** , come illustrato di seguito. Se l'impostazione **UserDataEnabled** esiste ed è impostata su true, impostarla su false. Se l'impostazione non esiste, aggiungere l'impostazione come illustrato di seguito.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.  Salvare il file di configurazione.
4.  Riavviare il servizio agente di amministrazione di Azure AD Connect come illustrato di seguito

![agente di amministrazione](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
