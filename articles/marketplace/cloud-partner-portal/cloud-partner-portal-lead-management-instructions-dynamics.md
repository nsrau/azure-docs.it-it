---
title: Istruzioni relative alla gestione dei lead per Dynamics CRM Online in Azure Marketplace | Microsoft Docs
description: Questo articolo illustra le istruzioni dettagliate per configurare la gestione dei lead con Dynamics CRM Online.
services: cloud-partner-portal
documentationcenter: 
author: Bigbrd
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: brdi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 05b076bf173d0fbff4baaf44c57ce215dbf23979
ms.lasthandoff: 04/18/2017


---

# <a name="lead-management-instructions-for-dynamics-crm-online"></a>Istruzioni relative alla gestione dei lead per Dynamics CRM Online

Questo documento illustra come configurare il sistema Dynamics CRM Online in modo che Microsoft possa fornire lead di vendita.

> [!NOTE]
> **Autorizzazioni utente necessarie per eseguire la procedura seguente:** per installare una soluzione è necessario essere un amministratore nell'istanza di Dynamics CRM Online; per creare un nuovo account del servizio lead è necessario essere un amministratore tenant.

**Installazione della soluzione:** <br/>
1. Scaricare la [soluzione Microsoft Marketplace Lead Writer](https://testdriveaccount.blob.core.windows.net/testdrivecon/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) e salvarla in locale.

2. Passare alle impostazioni di Dynamics CRM Online.  <br/>
Se non vengono visualizzate le opzioni come nell'immagine seguente, significa che non si dispone delle autorizzazioni necessarie. <br/>
![Immagine di configurazione di Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.    Fare clic su Importa e selezionare la soluzione scaricata nel passaggio 1.  <br/>
![Immagine di importazione di Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.    Completare l'installazione della soluzione.

**Autorizzazioni utente:** <br/>
È necessario condividere le informazioni sull'account del servizio in modo che Microsoft possa autorizzare la scrittura dei lead nell'istanza di Dynamics CRM. La procedura seguente illustra come creare l'account del servizio e le autorizzazioni da assegnare. <br/>
1.    Passare al [portale di amministrazione di Microsoft Office 365](https://go.microsoft.com/fwlink/?LinkId=225975).

2.    Fare clic sul riquadro "Amministratore": <br/>
![Immagine di configurazione amministratore di Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3.    Fare clic su "Aggiungi utente".<br/>
![Immagine di aggiunta utente di Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4.    Creare un nuovo utente per il servizio Lead Writer. Verificare quanto segue 
*  Fornire una password e deselezionare l'opzione "Make this user change their password when they first sign in" (Richiedere all'utente di modificare la password al primo accesso)
*     Selezionare il ruolo "User (no administrator access)" (Utente - nessun accesso amministratore)
*     Selezionare la licenza del prodotto come illustrato di seguito. Verrà addebitato l'importo per la licenza selezionata. La soluzione funzionerà anche con la licenza di base di Dynamics CRM Online. <br/>
![Immagine di aggiunta utente di Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

5.  Passare a Dynamics CRM Online e scegliere Impostazioni->Sicurezza.<br/>
![Immagine delle impostazioni di sicurezza di Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

6.  Selezionare l'utente creato nel passaggio 4, fare clic su Gestisci ruoli nella barra multifunzione in alto e assegnare il ruolo Microsoft Marketplace Lead Writer come illustrato di seguito: <br/>
![Immagine di gestione ruoli di Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)<br/>

Questo ruolo viene creato dalla soluzione importata e dispone solo di autorizzazioni per la scrittura dei lead e l'individuazione della versione della soluzione per garantire la compatibilità.

7.  Per Dynamics 365 8.2, sono necessarie ulteriori autorizzazioni per il ruolo Microsoft Marketplace Lead Writer creato dalla soluzione importata. In Sicurezza selezionare Ruoli di sicurezza e individuare il ruolo per Microsoft Marketplace Lead Writer. Selezionare tale ruolo e modificare l'impostazione per la creazione/lettura/scrittura in Impostazioni interfaccia utente entità utente nella scheda Record Principali illustrata di seguito: <br/>
![Immagine dei ruoli di sicurezza di Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)<br/>
![Immagine della gestione dei ruoli di sicurezza di Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)<br/>


Infine, inserire le informazioni di accesso nei campi **Url**, **Nome utente** e **Password** nel portale per Cloud Partner.
