---
title: Installare l'agente di provisioning cloud di Azure AD Connect
description: Questo articolo descrive come installare l'agente di provisioning di Azure AD Connect Cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4581ab89f74425682f569425f62714ead2c27c5
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620944"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Installare l'agente di provisioning cloud di Azure AD Connect
Questo documento illustra il processo di installazione per l'agente di provisioning di Azure Active Directory (Azure AD) e come configurarlo inizialmente nella portale di Azure.

>[!IMPORTANT]
>Nelle istruzioni di installazione seguenti si presuppone che siano stati soddisfatti tutti i [prerequisiti](how-to-prerequisites.md) .

L'installazione e la configurazione di Azure AD Connect provisioning vengono eseguite nei passaggi seguenti:
    
- [Installare l'agente](#install-the-agent)
- [Verificare l'installazione dell'agente](#verify-agent-installation)


## <a name="install-the-agent"></a>Installare l'agente
Per installare l'agente, attenersi alla seguente procedura.

1. Accedere al server che verrà usato con le autorizzazioni di amministratore dell'organizzazione.
1. Accedere al portale di Azure. A sinistra selezionare **Azure Active Directory**.
1. Selezionare **Gestisci provisioning (anteprima)**  > **esaminare tutti gli agenti**.
1. Scaricare l'agente di provisioning Azure AD Connect dalla portale di Azure.

   ![Scarica agente locale](media/how-to-install/install9.png)</br>
1. Eseguire il programma di installazione del provisioning Azure AD Connect (AADConnectProvisioningAgent. Installer).
1. Nella schermata **Microsoft Azure ad connettere il pacchetto agente di provisioning** , accettare le condizioni di licenza e selezionare **Installa**.

   ![Schermata del pacchetto dell'agente di provisioning di Microsoft Azure AD Connect](media/how-to-install/install1.png)</br>

1. Al termine di questa operazione, viene avviata la configurazione guidata. Accedere con l'account amministratore globale di Azure AD.
1. Nella schermata **connetti Active Directory** selezionare **Aggiungi directory**. Accedere quindi con l'account amministratore Active Directory. Questa operazione aggiunge la directory locale. Fare clic su **Avanti**.

   ![Schermata Connetti Active Directory](media/how-to-install/install3.png)</br>

1. Nella schermata **Configurazione completata** selezionare **conferma**. Questa operazione registra e riavvia l'agente.

   ![Schermata Configurazione completata](media/how-to-install/install4.png)</br>

1. Al termine di questa operazione, verrà visualizzato il avviso che la **configurazione dell'agente è stata verificata correttamente.** Selezionare **Esci**.

   ![Pulsante Esci](media/how-to-install/install5.png)</br>
1. Se viene ancora visualizzata la schermata iniziale **Microsoft Azure ad Connetti il pacchetto dell'agente di provisioning** , selezionare **Chiudi**.

## <a name="verify-agent-installation"></a>Verificare l'installazione dell'agente
La verifica dell'agente viene eseguita nel portale di Azure e nel server locale in cui è in esecuzione l'agente.

### <a name="azure-portal-agent-verification"></a>Verifica dell'agente nel portale di Azure
Per verificare che l'agente sia visualizzato da Azure, seguire questa procedura.

1. Accedere al portale di Azure.
1. A sinistra selezionare **Azure Active Directory** > **Azure ad Connect**. In centro selezionare **Manage provisioning (anteprima)** .

   ![Portale di Azure](media/how-to-install/install6.png)</br>

1.  Nella schermata **Azure ad provisioning (anteprima)** selezionare **verifica tutti gli agenti**.

    ![Opzione controlla tutti gli agenti](media/how-to-install/install7.png)</br>
 
1. Nella schermata **agenti di provisioning locali** vengono visualizzati gli agenti installati. Verificare che l'agente in questione sia presente e contrassegnato come *attivo*.

   ![Schermata agenti di provisioning locale](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>Verificare la porta
Per verificare che Azure sia in ascolto sulla porta 443 e che l'agente possa comunicare con esso, seguire questa procedura.

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Questo test verifica che gli agenti possano comunicare con Azure tramite la porta 443. Aprire un browser e passare all'URL precedente dal server in cui è installato l'agente.

![Verifica della raggiungibilità della porta](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>Nel server locale
Per verificare che l'agente sia in esecuzione, attenersi alla seguente procedura.

1.  Accedere al server con un account di amministratore.
1.  Per aprire i **Servizi** , passare a esso o **avviare** > **eseguire** > **Services. msc**.
1.  In **Servizi**verificare che **Microsoft Azure ad Connect agent Updater** e **Microsoft Azure ad Connect provisioning Agent** siano presenti e che il relativo stato sia *in esecuzione*.

    ![Schermata dei servizi](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>L'agente è stato installato, ma è necessario configurarlo e abilitarlo prima di avviare la sincronizzazione degli utenti. Per configurare un nuovo agente, vedere [creare una nuova configurazione per il provisioning basato su cloud Azure ad Connect](how-to-configure.md).



## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)
 
