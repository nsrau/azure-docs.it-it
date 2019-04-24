---
title: Che cos'è l'agente Azure AD Connect amministratore - Azure AD Connect | Microsoft Docs
description: Vengono descritti gli strumenti usati per sincronizzare e monitorare l'ambiente locale con Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/03/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e33143626e136523b4af086e841b92e9ad30fa86
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60295009"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Cos'è l'agente di amministrazione di Azure AD Connect? 
L'agente Azure AD Connect amministrazione è un nuovo componente di Azure Active Directory Connect installato in un server di Azure Active Directory Connect. Si è usato per raccogliere dati specifici dall'ambiente Active Directory che consente a un Microsoft di supporto tecnico per risolvere i problemi quando si apre una richiesta di assistenza.

Durante l'installazione, l'agente di Azure AD Connect amministrazione attende richieste specifiche per i dati da Azure Active Directory, ottiene i dati richiesti dall'ambiente di sincronizzazione e li invia ad Azure Active Directory, in cui viene presentato a Microsoft supporto Engineer.

Le informazioni che l'agente Azure AD Connect amministrazione recupera dall'ambiente in uso non vengono archiviate in qualsiasi modo, viene visualizzata solo per il supporto tecnico Microsoft per assisterli nelle analisi e risoluzione dei problemi di Azure Active Directory Connect caso di supporto correlati che è stato aperto

## <a name="how-is-the-azure-ad-connect-admin-agent-installed-on-the-azure-ad-connect-server"></a>Modo in cui è installato l'agente Azure AD Connect amministratore nel server di Azure AD Connect? 
Dopo aver installato l'agente di amministratore, verrà visualizzato i seguenti due nuovi programmi nell'elenco "Aggiungi/Rimuovi programmi" nel Pannello di controllo del server: 

![Amministrazione agente](media/whatis-aadc-admin-agent/adminagent1.png)

Non rimuovere o deprovisioning installare i programmi, così come sono una parte essenziale dell'installazione di Azure AD Connect.

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Per il tecnico del servizio Microsoft vengono mostrati i dati nella mio servizio di sincronizzazione?
Quando si apre un caso di supporto, il supporto tecnico Microsoft può vedere, per un determinato utente, i dati rilevanti in Active Directory, lo spazio connettore di Active Directory nel server di Azure Active Directory Connect, lo spazio connettore di Azure Active Directory nel portale di Azure Server Active Directory Connect e il Metaverse nel server di Azure Active Directory Connect.

Il tecnico del supporto Microsoft non è possibile modificare qualsiasi dato nel sistema e non può visualizzare tutte le password.

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Cosa accade se si desidera che il tecnico del supporto Microsoft per accedere ai dati personali? 
 
Se non si desidera il tecnico del servizio Microsoft per accedere ai dati per una chiamata al supporto tecnico è possibile disabilitare questa, modificando il file di configurazione del servizio, come descritto di seguito: 

1.  Aprire **c:\Programmi\Microsoft Azure AD Connect amministrazione Agent\AzureADConnectAdministrationAgentService.exe.config** nel blocco note.
2.  Disabilitare **UserDataEnabled** impostazione come illustrato di seguito. Se **UserDataEnabled** impostazione esiste e viene impostato su true, quindi impostarlo su false. Se l'impostazione non esiste, quindi aggiungere l'impostazione come illustrato di seguito.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.  Salvare il file di configurazione.
4.  Riavviare il servizio agente di Azure AD Connect amministrazione come illustrato di seguito

![Amministrazione agente](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).