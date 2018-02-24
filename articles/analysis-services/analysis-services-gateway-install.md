---
title: Installare il gateway dati locale | Microsoft Docs
description: Informazioni su come installare e configurare un gateway dati locale.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: c2cbe1c60f67c689a38d1585245610a6fa73bff4
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Installare e configurare un gateway dati locale
Quando uno o più server Azure Analysis Services nella stessa area si connettono a origini dati locali, è necessario un gateway dati locale. Per altre informazioni sul gateway, vedere [Gateway dati locale](analysis-services-gateway.md).

## <a name="prerequisites"></a>prerequisiti
**Requisiti minimi:**

* .NET Framework 4.5
* Versione a 64 bit di Windows 7 o Windows Server 2008 R2 (o versione successiva)

**Consigliato:**

* 8 CPU core
* 8 GB di memoria
* Windows 2012 R2 versione a 64 bit (o versione successiva)

**Considerazioni importanti:**

* Durante l'installazione, quando si registra il gateway con Azure, viene selezionata l'area predefinita per la sottoscrizione. È possibile scegliere un'area diversa. Se si dispone di server in più aree, è necessario installare un gateway per ogni area. 
* Il gateway non può essere installato in un controller di dominio.
* In un singolo computer può essere installato un solo gateway.
* Installare il gateway in un computer che rimane attivo e non passa alla modalità di sospensione.
* Non installare il gateway in un computer connesso alla rete in modalità wireless. È infatti possibile che si verifichi un calo delle prestazioni.
* Accedere ad Azure con un account di Azure AD per lo stesso [tenant](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) della sottoscrizione in cui si sta registrando il gateway. Gli account Azure B2B (guest) non sono supportati per l'installazione e registrazione di un gateway.


## <a name="download"></a>Scaricare
 [Scaricare il gateway](https://aka.ms/azureasgateway)

## <a name="install"></a>Installare

1. Eseguire l'installazione.

2. Selezionare un percorso, accettare le condizioni e quindi fare clic su **Installa**.

   ![Percorso di installazione e condizioni di licenza](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Accedere ad Azure. L'account deve essere nell'istanza di Microsoft Azure Active Directory del tenant. Questo account viene usato per l'amministratore del gateway. Gli account Azure B2B (guest) non sono supportati per l'installazione e registrazione del gateway.

   ![Accedere ad Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Se si accede con un account di dominio, questo viene mappato all'account aziendale in Azure AD. L'account aziendale viene usato come amministratore del gateway.

## <a name="register"></a>Registrare
Per creare una risorsa per il gateway in Azure, è necessario registrare l'istanza locale installata con il servizio cloud gateway. 

1.  Selezionare l'opzione che **consente di registrare un nuovo gateway in questo computer**.

    ![Register ](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Digitare un nome e la chiave di ripristino per il gateway. Per impostazione predefinita, il gateway usa l'area predefinita della sottoscrizione. Se è necessario selezionare un'area diversa, selezionare **Cambia area**.

    > [!IMPORTANT]
    > Salvare la chiave di ripristino in un luogo sicuro. La chiave di ripristino è necessaria per poter acquisire la proprietà, eseguire la migrazione o ripristinare un gateway. 

   ![Register ](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-resource"></a>Creare una risorsa per il gateway di Azure
Dopo aver installato e registrato il gateway, è necessario creare una risorsa per il gateway nella sottoscrizione di Azure. Accedere ad Azure con lo stesso account usato durante la registrazione del gateway.

1. Nel portale di Azure fare clic su **Crea un nuovo servizio** > **Integrazione aziendale**  > **Gateway dati locale** > **Crea**.

   ![Creazione di una risorsa per il gateway](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. In **Crea gateway di connessione**, immettere queste impostazioni:

    * **Nome**: inserire un nome per la risorsa del gateway. 

    * **Sottoscrizione**: selezionare la sottoscrizione di Azure da associare alla risorsa del gateway. 
   
      La sottoscrizione predefinita si basa sull'account di Azure usato per accedere.

    * **Gruppo di risorse**: creare un gruppo di risorse o selezionarne uno esistente.

    * **Percorso**: selezionare l'area in cui è stato registrato il gateway.

    * **Nome installazione**: se l'installazione del gateway non è già selezionata, selezionare il gateway registrato. 

    Al termine dell'operazione, scegliere **Crea**.

## <a name="connect-servers"></a>Connettere i server alla risorsa per il gateway

1. Nella panoramica del server Azure Analysis Services fare clic su **Gateway dati locale**.

   ![Connessione del server al gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. In **Selezionare un gateway dati locale da connettere** selezionare la risorsa per il gateway e quindi fare clic su **Connetti gateway selezionato**.

   ![Connessione del server alla risorsa per il gateway](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Se il gateway non viene visualizzato nell'elenco, probabilmente il server non si trova nella stessa area specificata durante la registrazione del gateway. 

È tutto. Se è necessario aprire le porte o risolvere eventuali problemi, vedere l'articolo su [Gateway dati locale](analysis-services-gateway.md).

## <a name="next-steps"></a>Passaggi successivi
* [Gestire Analysis Services](analysis-services-manage.md)   
* [Ottenere dati da Azure Analysis Services](analysis-services-connect.md)
