---
title: 'Azure AD Connect: writeback del gruppo'
description: Questo articolo descrive il writeback del gruppo in Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 06/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2e24246c749978cd2bbb5b3d0821eea6d7dfb4b
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89660878"
---
# <a name="azure-ad-connect-group-writeback"></a>Writeback del gruppo di Azure AD Connect

Il writeback dei gruppi consente ai clienti di sfruttare i gruppi di cloud per le proprie esigenze ibride. Se si usa la funzionalità gruppi di Microsoft 365, è possibile che questi gruppi siano rappresentati nell'Active Directory locale. Questa opzione è disponibile **solo** se si dispone di Exchange presente nell'Active Directory locale.

## <a name="pre-requisites"></a>Prerequisiti
Per abilitare il writeback del gruppo, è necessario soddisfare i prerequisiti seguenti.
- Azure Active Directory Premium licenze per il tenant.
- Una distribuzione ibrida configurata tra l'organizzazione di Exchange locale e Microsoft 365 e verificato che funzioni correttamente.
- Installata una versione supportata di Exchange locale
- Configurata Single Sign-On tramite Azure Active Directory Connect 

## <a name="enable-group-writeback"></a>Abilitare il writeback dei gruppi
Per abilitare il writeback del gruppo, attenersi alla procedura seguente:

1. Aprire la procedura guidata Azure AD Connect, selezionare **Configura** e quindi fare clic su **Avanti**.
2. Selezionare **Personalizza opzioni di sincronizzazione** e quindi fare clic su **Avanti**.
3. Nella pagina **Connetti a Azure ad** immettere le credenziali. Fare clic su **Avanti**.
4. Nella pagina **funzionalità facoltative** verificare che le opzioni configurate in precedenza siano ancora selezionate.
5. Selezionare **writeback del gruppo** e quindi fare clic su **Avanti**.
6. Nella **pagina writeback**selezionare un'unità organizzativa Active Directory per archiviare gli oggetti sincronizzati da Microsoft 365 all'organizzazione locale, quindi fare clic su **Avanti**.
7. Nella pagina **pronto** per la configurazione fare clic su **Configura**.
8. Al termine della procedura guidata, fare clic su **Esci** nella pagina Configurazione completata.
9. Aprire Windows PowerShell come amministratore nel server Azure Active Directory Connect ed eseguire i comandi seguenti.

```Powershell
$AzureADConnectSWritebackAccountDN =  <MSOL_ account DN>
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN
```

Per altre informazioni sulla configurazione dei gruppi di Microsoft 365 vedere [configurare gruppi di Microsoft 365 con Exchange ibrido locale](/exchange/hybrid-deployment/set-up-microsoft-365-groups#enable-group-writeback-in-azure-ad-connect).

## <a name="disabling-group-writeback"></a>Disabilitazione del writeback del gruppo
Per disabilitare il writeback del gruppo, attenersi alla procedura seguente: 


1. Avviare la procedura guidata Azure Active Directory Connect e passare alla pagina attività aggiuntive. Selezionare l'attività **Personalizza opzioni di sincronizzazione** e fare clic su **Avanti**.
2. Nella pagina **funzionalità facoltative** deselezionare il writeback del gruppo.  Verrà visualizzato un avviso che informa che i gruppi verranno eliminati.  Fare clic su **Sì**.
   >[!IMPORTANT]
   > La disabilitazione del writeback del gruppo provocherà l'eliminazione da parte dei gruppi creati in precedenza da questa funzionalità dal Active Directory locale al successivo ciclo di sincronizzazione. 

   ![Deseleziona casella](media/how-to-connect-group-writeback/group2.png)
  
3. Fare clic su **Avanti**.
4. Fare clic su **Configura**.

 >[!NOTE]
 > La disabilitazione del writeback del gruppo imposterà i flag completi per l'importazione e la sincronizzazione completa su' true ' sul connettore Azure Active Directory, causando la propagazione della regola durante il ciclo di sincronizzazione successivo, eliminando i gruppi precedentemente riscritti nel Active Directory.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).