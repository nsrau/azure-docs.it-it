---
title: Installare l'agente di provisioning cloud di Azure AD Connect
description: Questo articolo descrive come installare l'agente di provisioning cloud di Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3960b8afeb9d7ecc80aa49fc13eee4977fa5494
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173971"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Installare l'agente di provisioning cloud di Azure AD Connect
Questo documento illustra il processo di installazione per l'Agente provisioning di Microsoft Azure Active Directory Connect (Azure AD) e come configurarlo inizialmente nel portale di Azure.

>[!IMPORTANT]
>Nelle istruzioni di installazione seguenti si presuppone che siano stati soddisfatti tutti i [Prerequisiti](how-to-prerequisites.md).

Per installare e configurare il provisioning di Azure AD Connect sono necessari i passaggi seguenti:

- [Account del servizio gestito di gruppo](#group-managed-service-accounts) 
- [Installare l'agente](#install-the-agent)
- [Verificare l'installazione dell'agente](#verify-agent-installation)


## <a name="group-managed-service-accounts"></a>Group Managed Service Accounts
Un account del servizio gestito del gruppo è un account di dominio gestito che offre la gestione automatica delle password, la gestione semplificata del nome dell'entità servizio (SPN), la possibilità di delegare la gestione ad altri amministratori e anche di estendere questa funzionalità su più server.  Azure AD Connect Cloud Sync supporta e consiglia l'uso di un account del servizio gestito del gruppo per l'esecuzione dell'agente.  Per altre informazioni su un gMSA, vedere [account del servizio gestito del gruppo](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) 


### <a name="upgrading-an-existing-agent-to-use-the-gmsa-account"></a>Aggiornamento di un agente esistente per l'utilizzo dell'account gMSA
Per aggiornare un agente esistente per l'utilizzo dell'account gMSA creato durante l'installazione, è sufficiente aggiornare il servizio Agent alla versione più recente eseguendo il AADConnectProvisioningAgent.msi.  Il servizio verrà aggiornato alla versione più recente.  A questo punto, eseguire nuovamente l'installazione guidata e fornire le credenziali per creare l'account quando richiesto.



## <a name="install-the-agent"></a>Installare l'agente
Per installare l'agente, seguire questa procedura.

 1. Accedere al server che si userà con le autorizzazioni di amministratore dell'organizzazione.
 2. Accedere al portale di Azure, quindi passare a **Azure Active Directory**.
 3. Nel menu a sinistra selezionare **Azure ad Connect**.
 4. Selezionare **Gestisci il provisioning (anteprima)**  > **Verifica tutti gli agenti**.
 5. Scaricare l'agente di provisioning di Azure AD Connect dal portale di Azure.
   ![Scarica agente locale](media/how-to-install/install-9.png)</br>
 6. Eseguire il programma di installazione del provisioning Azure AD Connect AADConnectProvisioningAgent.msi.
 7. Nella schermata **Microsoft Azure AD Connect Provisioning Agent Package** accettare le condizioni di licenza e selezionare **Installa**.
   ![Schermata Microsoft Azure AD Connect Provisioning Agent Package](media/how-to-install/install-1.png)</br>
 8. Al termine dell'operazione, viene avviata la configurazione guidata. Accedere con l'account amministratore globale di Azure AD.
 9. Nella **schermata Configura account di servizio** selezionare **Crea gMSA** o **USA gMSA personalizzato**.  Se si consente all'agente di creare l'account, questo verrà denominato provAgentgMSA $. Se si specifica **Use Custom gMSA** , verrà richiesto di fornire l'account.
 10. Immettere le credenziali di amministratore di dominio per creare l'account del servizio gestito del gruppo che verrà utilizzato per eseguire il servizio Agent. Fare clic su **Avanti**.  
   ![Crea gMSA](media/how-to-install/install-12.png)</br>
 11. Nella schermata **Connetti Active Directory** selezionare **Aggiungi directory**. Accedere con l'account amministratore di Active Directory. Questa operazione aggiungerà la directory locale. 
 12. Facoltativamente, è possibile gestire le preferenze dei controller di dominio che l'agente userà selezionando **Seleziona priorità controller di dominio** e ordinando l'elenco dei controller di dominio.   Fare clic su **OK**.
  ![Controlllers dominio ordini](media/how-to-install/install-2a.png)</br>
 13. Selezionare **Avanti**.
  ![Schermata Connetti Active Directory](media/how-to-install/install-3a.png)</br>
 14.  Nella schermata **installazione agente** confermare le impostazioni e l'account che verrà creato e fare clic su **conferma**.
  ![Conferma settngs](media/how-to-install/install-11.png)</br>
 15. Al termine di questa operazione, si noterà che **l'installazione dell'agente è stata completata.** Selezionare **Esci**.
  ![Schermata Configurazione completata](media/how-to-install/install-4a.png)</br>
1. Se viene ancora visualizzata la schermata iniziale **Microsoft Azure AD Connect Provisioning Agent Package**, selezionare **Chiudi**.

## <a name="verify-agent-installation"></a>Verificare l'installazione dell'agente
La verifica dell'agente si esegue nel portale di Azure e nel server locale che esegue l'agente.

### <a name="azure-portal-agent-verification"></a>Verifica dell'agente nel portale di Azure
Per verificare se l'agente viene visto da Azure, seguire questa procedura.

1. Accedere al portale di Azure.
1. Sulla sinistra, selezionare **Azure Active Directory** > **Azure AD Connect**. Al centro, selezionare **Gestione del provisioning (anteprima)** .

   ![Portale di Azure](media/how-to-install/install-6.png)</br>

1.  Nella schermata **Provisioning di Azure AD (anteprima)** selezionare **Verifica tutti gli agenti**.

    ![Opzione Verifica tutti gli agenti](media/how-to-install/install-7.png)</br>
 
1. Nella schermata **Agenti di provisioning locali** verranno visualizzati gli agenti installati. Verificare che l'agente in questione sia presente e contrassegnato come *attivo*.

   ![Schermata Agenti di provisioning locali](media/how-to-install/verify-1.png)</br>



### <a name="on-the-local-server"></a>Nel server locale
Per verificare se l'agente è in esecuzione, seguire questa procedura.

1.  Accedere al server con un account amministratore.
1.  Aprire **Servizi** passando all'opzione relativa oppure selezionando **Start** > **Esegui** > **Services.msc**.
1.  In **Servizi** assicurarsi che siano presenti i servizi **Microsoft Azure AD Connect Agent Updater** e **Microsoft Azure AD Connect Provisioning Agent** e che il relativo stato sia *In esecuzione*.

    ![Schermata Servizi](media/how-to-install/troubleshoot-1.png)

>[!IMPORTANT]
>L'agente è stato installato, ma è necessario configurarlo e abilitarlo prima di avviare la sincronizzazione degli utenti. Per configurare un nuovo agente, vedere [Creare una nuova configurazione per il provisioning basato sul cloud di Azure AD Connect](how-to-configure.md).




## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)
