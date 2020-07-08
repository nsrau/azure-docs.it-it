---
title: Installare l'agente di provisioning cloud di Azure AD Connect
description: Questo articolo descrive come installare l'agente di provisioning cloud di Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5b59942731c8ca7b29de30e160d8370c9cf76ef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85807639"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Installare l'agente di provisioning cloud di Azure AD Connect
Questo documento illustra il processo di installazione per l'Agente provisioning di Microsoft Azure Active Directory Connect (Azure AD) e come configurarlo inizialmente nel portale di Azure.

>[!IMPORTANT]
>Nelle istruzioni di installazione seguenti si presuppone che siano stati soddisfatti tutti i [Prerequisiti](how-to-prerequisites.md).

Per installare e configurare il provisioning di Azure AD Connect sono necessari i passaggi seguenti:
    
- [Installare l'agente](#install-the-agent)
- [Verificare l'installazione dell'agente](#verify-agent-installation)


## <a name="install-the-agent"></a>Installare l'agente
Per installare l'agente, seguire questa procedura.

1. Accedere al server che si userà con le autorizzazioni di amministratore dell'organizzazione.
1. Accedere al portale di Azure, quindi passare a **Azure Active Directory**.
1. Nel menu a sinistra selezionare **Azure ad Connect**.
1. Selezionare **Gestisci il provisioning (anteprima)**  > **Verifica tutti gli agenti**.
1. Scaricare l'agente di provisioning di Azure AD Connect dal portale di Azure.

   ![Scarica agente locale](media/how-to-install/install9.png)</br>
1. Eseguire il programma di installazione del provisioning di Azure AD Connect (AADConnectProvisioningAgent.Installer).
1. Nella schermata **Microsoft Azure AD Connect Provisioning Agent Package** accettare le condizioni di licenza e selezionare **Installa**.

   ![Schermata Microsoft Azure AD Connect Provisioning Agent Package](media/how-to-install/install1.png)</br>

1. Al termine dell'operazione, viene avviata la configurazione guidata. Accedere con l'account amministratore globale di Azure AD.
1. Nella schermata **Connetti Active Directory** selezionare **Aggiungi directory**. Accedere con l'account amministratore di Active Directory. Questa operazione aggiungerà la directory locale. Selezionare **Avanti**.

   ![Schermata Connetti Active Directory](media/how-to-install/install3.png)</br>

1. Nella schermata **Configurazione completata** selezionare **Conferma**. Questa operazione registra e riavvia l'agente.

   ![Schermata Configurazione completata](media/how-to-install/install4.png)</br>

1. Al termine di questa operazione, verrà visualizzato il avviso **La configurazione dell'agente è stata verificata.** Selezionare **Esci**.

   ![Pulsante Esci](media/how-to-install/install5.png)</br>
1. Se viene ancora visualizzata la schermata iniziale **Microsoft Azure AD Connect Provisioning Agent Package**, selezionare **Chiudi**.

## <a name="verify-agent-installation"></a>Verificare l'installazione dell'agente
La verifica dell'agente si esegue nel portale di Azure e nel server locale che esegue l'agente.

### <a name="azure-portal-agent-verification"></a>Verifica dell'agente nel portale di Azure
Per verificare se l'agente viene visto da Azure, seguire questa procedura.

1. Accedere al portale di Azure.
1. Sulla sinistra, selezionare **Azure Active Directory** > **Azure AD Connect**. Al centro, selezionare **Gestione del provisioning (anteprima)** .

   ![Portale di Azure](media/how-to-install/install6.png)</br>

1.  Nella schermata **Provisioning di Azure AD (anteprima)** selezionare **Verifica tutti gli agenti**.

    ![Opzione Verifica tutti gli agenti](media/how-to-install/install7.png)</br>
 
1. Nella schermata **Agenti di provisioning locali** verranno visualizzati gli agenti installati. Verificare che l'agente in questione sia presente e contrassegnato come *attivo*.

   ![Schermata Agenti di provisioning locali](media/how-to-install/verify1.png)</br>



### <a name="on-the-local-server"></a>Nel server locale
Per verificare se l'agente è in esecuzione, seguire questa procedura.

1.  Accedere al server con un account amministratore.
1.  Aprire **Servizi** passando all'opzione relativa oppure selezionando **Start** > **Esegui** > **Services.msc**.
1.  In **Servizi** assicurarsi che siano presenti i servizi **Microsoft Azure AD Connect Agent Updater** e **Microsoft Azure AD Connect Provisioning Agent** e che il relativo stato sia *In esecuzione*.

    ![Schermata Servizi](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>L'agente è stato installato, ma è necessario configurarlo e abilitarlo prima di avviare la sincronizzazione degli utenti. Per configurare un nuovo agente, vedere [Creare una nuova configurazione per il provisioning basato sul cloud di Azure AD Connect](how-to-configure.md).



## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)
 
