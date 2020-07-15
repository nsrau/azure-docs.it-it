---
title: Cos'è l'agente di amministrazione di Azure AD Connect - Azure AD Connect | Microsoft Docs
description: Vengono descritti gli strumenti usati per sincronizzare e monitorare l'ambiente locale con Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/04/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43b69deb242fd1ad80d9cdc4667070d2dbfefcd5
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86255609"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Cos'è l'agente di amministrazione di Azure AD Connect? 
L'agente di amministrazione di Azure AD Connect è un nuovo componente di Azure Active Directory Connect che può essere installato in un server Azure Active Directory Connect. Viene usato per raccogliere dati specifici dall'ambiente di Active Directory che consentono a un tecnico del supporto Microsoft di risolvere i problemi quando si apre un caso di supporto. 

>[!NOTE]
>L'agente di amministrazione non viene installato e abilitato per impostazione predefinita.  È necessario installarlo per raccogliere i dati e agevolare i casi di supporto.

Quando viene installato, l'agente di amministrazione di Azure AD Connect attende richieste specifiche di dati da Azure Active Directory, ottiene i dati richiesti dall'ambiente di sincronizzazione e li invia ad Azure Active Directory, dove vengono presentati al tecnico di supporto Microsoft. 

Le informazioni recuperate dall'agente di amministrazione Azure AD Connect dall'ambiente non vengono archiviate in alcun modo. Vengono mostrate solo al tecnico del supporto Microsoft per assisterlo nell'identificazione e nella risoluzione del caso di supporto aperto correlato ad Azure Active Directory Connect. L'agente di amministrazione di Azure AD Connect non viene installato nel server Azure AD Connect per impostazione predefinita. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Installare l'agente di amministrazione di Azure AD Connect nel server Azure AD Connect 

Prerequisiti:
1.    Azure AD Connect è installato nel server
2.    Azure AD Connect Health è installato nel server

![agente di amministrazione](media/whatis-aadc-admin-agent/adminagent0.png)

I file binari dell'agente di amministrazione di Azure AD Connect sono inseriti nel server AAD Connect. Per installare l'agente, procedere come segue:

1.    Aprire PowerShell in modalità di amministrazione
2.    Passare alla directory in cui si trova l'applicazione, "C:\Programmi\Microsoft Azure Active Directory Connect\Tools"
3.    Eseguire ConfigureAdminAgent.ps1

Quando richiesto, immettere le credenziali di amministratore globale di Azure AD. Dovranno essere le stesse credenziali immesse durante l'installazione di Azure AD Connect.

Dopo l'installazione dell'agente, nell'elenco "Installazione applicazioni" del Pannello di controllo del server verranno visualizzati i due nuovi programmi seguenti: 

![agente di amministrazione](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Quali dati del servizio di sincronizzazione vengono mostrati al tecnico del supporto Microsoft? 
Quando si apre un caso di supporto, il tecnico del supporto Microsoft può vedere, per un determinato utente, i dati pertinenti in Active Directory, lo spazio connettore Active Directory nel server Azure Active Directory Connect, lo spazio connettore Azure Active Directory nel server Azure Active Directory Connect e il metaverse nel server Azure Active Directory Connect. 

Il tecnico del supporto Microsoft non può cambiare i dati nel sistema e non può visualizzare le password. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Cosa succede se non si vuole che il tecnico del supporto Microsoft acceda ai dati? 
Una volta installato l'agente, se non si vuole che il tecnico del supporto Microsoft acceda ai dati per una chiamata di supporto, è possibile disabilitare la funzionalità modificando il file di configurazione del servizio come descritto di seguito: 

1. Aprire **C:\Programmi\Microsoft Azure AD Connect Administration Agent\AzureADConnectAdministrationAgentService.exe.config** nel Blocco note.
2. Disabilitare l'impostazione **UserDataEnabled** come indicato di seguito. Se l'impostazione **UserDataEnabled** esiste ed è impostata su True, impostarla su False. Se l'impostazione non esiste, aggiungerla come indicato di seguito.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3. Salvare il file di configurazione.
4. Riavviare il servizio agente di amministrazione di Azure AD Connect come illustrato di seguito

![agente di amministrazione](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
