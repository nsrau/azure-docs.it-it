---
title: Installare l'agente di provisioning cloud di Azure AD Connect
description: Questo articolo descrive come installare l'agente di provisioning cloud di Azure AD Connect.This article describes how to install the Azure AD Connect cloud provisioning agent.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263348"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>Installare l'agente di provisioning cloud di Azure AD Connect
Questo documento illustra il processo di installazione dell'agente di provisioning di Azure Active Directory (Azure AD) Connect e come configurarlo inizialmente nel portale di Azure.This document walks you through the installation process for the Azure Active Directory (Azure AD) Connect provisioning agent and how to initially configure it in the Azure portal.

>[!IMPORTANT]
>Le istruzioni di installazione seguenti presuppongono che tutti i [prerequisiti](how-to-prerequisites.md) siano stati soddisfatti.

L'installazione e la configurazione del provisioning di Azure AD Connect vengono eseguite nei passaggi seguenti:Installing and configuring Azure AD Connect provisioning is accomplished in the following steps:
    
- [Installare l'agente](#install-the-agent)
- [Verificare l'installazione dell'agente](#verify-agent-installation)


## <a name="install-the-agent"></a>Installare l'agente
Per installare l'agente, attenersi alla seguente procedura.

1. Accedere al server che verrà usato con le autorizzazioni di amministratore aziendale.
1. Accedere al portale di Azure. A sinistra selezionare **Azure Active Directory**.
1. Selezionare **Gestisci provisioning (anteprima)** > **Esaminare tutti gli agenti**.
1. Scaricare l'agente di provisioning di Azure AD Connect dal portale di Azure.Download the Azure AD Connect provisioning agent from the Azure portal.

   ![Scarica l'agente locale](media/how-to-install/install9.png)</br>
1. Eseguire il programma di installazione del provisioning di Azure AD Connect (AADConnectProvisioningAgent.Installer).
1. Nella schermata **Pacchetto agente di provisioning di Microsoft Azure AD Connect** accettare le condizioni di licenza e selezionare **Installa**.

   ![Schermata del pacchetto dell'agente di provisioning di Microsoft Azure AD Connect](media/how-to-install/install1.png)</br>

1. Al termine dell'operazione, viene avviata la configurazione guidata. Accedere con l'account amministratore globale di Azure AD.
1. Nella schermata **Connetti Active Directory** selezionare Aggiungi **directory**. Accedere quindi con l'account amministratore di Active Directory. Questa operazione aggiunge la directory locale. Fare clic su **Avanti**.

   ![Schermata Connetti Active Directory](media/how-to-install/install3.png)</br>

1. Nella schermata **Configurazione completata** selezionare **Conferma**. Questa operazione registra e riavvia l'agente.

   ![Schermata Configurazione completata](media/how-to-install/install4.png)</br>

1. Al termine dell'operazione, verrà visualizzata l'avviso **La configurazione dell'agente è stata verificata correttamente.** Selezionare **Esci**.

   ![Pulsante Esci](media/how-to-install/install5.png)</br>
1. Se viene ancora visualizzata la schermata iniziale del **pacchetto dell'agente** di provisioning di Microsoft Azure AD Connect, selezionare **Chiudi**.

## <a name="verify-agent-installation"></a>Verificare l'installazione dell'agente
La verifica dell'agente viene eseguita nel portale di Azure e nel server locale che esegue l'agente.

### <a name="azure-portal-agent-verification"></a>Verifica dell'agente nel portale di Azure
Per verificare che l'agente venga visualizzato da Azure, attenersi alla seguente procedura.

1. Accedere al portale di Azure.
1. A sinistra selezionare **Azure Active Directory** > **Azure AD Connect**. Al centro, selezionare **Gestisci provisioning (anteprima)**.

   ![Portale di Azure](media/how-to-install/install6.png)</br>

1.  Nella schermata **Provisioning (anteprima)** di Azure AD selezionare **Controlla tutti gli agenti.**

    ![Opzione Controlla tutti gli agenti](media/how-to-install/install7.png)</br>
 
1. Nella schermata Agenti di provisioning locali vengono **visualizzati** gli agenti installati. Verificare che l'agente in questione sia presente e contrassegnato come *attivo*.

   ![Schermata Degli agenti di provisioning locali](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>Verificare la porta
Per verificare che Azure sia in ascolto sulla porta 443 e che l'agente possa comunicare con esso, eseguire la procedura seguente.

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Questo test verifica che gli agenti possano comunicare con Azure tramite la porta 443.This test verifies that your agents can communicate with Azure over port 443. Aprire un browser e passare all'URL precedente dal server in cui è installato l'agente.

![Verifica della raggiungibilità del porto](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>Nel server locale
Per verificare che l'agente sia in esecuzione, attenersi alla seguente procedura.

1.  Accedere al server con un account amministratore.
1.  Aprire **Servizi** accedendo ad esso o accedendo a **Avvia** > **eseguire** > **Services.msc**.
1.  In **Servizi**verificare che **Microsoft Azure AD Connect Agent Updater** e **Microsoft Azure AD Connect Provisioning Agent** siano presenti e che il relativo stato sia *In esecuzione.*

    ![Schermata Servizi](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>L'agente è stato installato, ma deve essere configurato e abilitato prima di avviare la sincronizzazione degli utenti. Per configurare un nuovo agente, vedere Creare una nuova configurazione per il [provisioning basato su cloud](how-to-configure.md)di Azure AD Connect.



## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)
 
